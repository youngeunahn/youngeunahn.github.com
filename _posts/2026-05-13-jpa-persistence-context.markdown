---
layout: docs
title: "JPA 내부 동작 이해하기: 트랜잭션, 영속성 컨텍스트, 지연 로딩"
date: 2026-05-13 12:47:00 +0900
categories: Spring_JPA
badges:
- type: primary
  tag: JPA
---

JPA를 처음 사용하면 `save()`를 호출했는데 바로 `insert` 쿼리가 보이지 않거나, 객체의 값을 바꿨을 뿐인데 `update` 쿼리가 실행되는 상황을 만나게 된다.
또 어떤 경우에는 연관 객체를 조회하는 순간 추가 쿼리가 나가고, 트랜잭션 밖에서는 `LazyInitializationException`이 발생하기도 한다.

이런 동작은 JPA가 단순히 SQL을 대신 만들어 주는 도구가 아니라, **트랜잭션 안에서 엔티티를 영속성 컨텍스트로 관리하고 필요한 시점에 SQL을 실행하는 기술**이기 때문에 발생한다.

이번 글에서는 JPA 내부 동작을 이해하기 위해 꼭 알아야 하는 트랜잭션, 영속성 컨텍스트, flush, 변경 감지, 지연 로딩을 한 흐름으로 정리한다.

---

## 1. JPA 요청은 어떤 흐름으로 동작할까?

Spring Data JPA를 사용하면 보통 `Repository`만 호출하기 때문에 내부 동작이 잘 보이지 않는다.
하지만 실제로는 대략 다음 흐름으로 동작한다.

1. 서비스 메서드에 `@Transactional`이 적용된다.
2. 트랜잭션이 시작되고 `EntityManager`가 준비된다.
3. `EntityManager`는 영속성 컨텍스트를 통해 엔티티를 관리한다.
4. 조회, 저장, 수정 작업이 영속성 컨텍스트 안에서 처리된다.
5. 트랜잭션이 끝나기 직전 `flush`가 발생한다.
6. 변경 내용이 SQL로 데이터베이스에 전달된다.
7. 트랜잭션이 `commit`되거나 문제가 있으면 `rollback`된다.

즉 JPA 코드는 겉으로 보면 객체를 다루는 코드처럼 보이지만, 내부에서는 트랜잭션과 영속성 컨텍스트가 함께 동작한다.

```java
@Transactional
public void changeName(Long memberId, String name) {
  Member member = memberRepository.findById(memberId)
      .orElseThrow();

  member.changeName(name);
}
```

위 코드에는 `update` 쿼리를 직접 실행하는 부분이 없다.
하지만 트랜잭션 안에서 조회된 `member`는 영속성 컨텍스트가 관리하는 엔티티가 되고, 값이 바뀌면 트랜잭션 종료 시점에 JPA가 변경 내용을 감지해서 SQL을 만든다.

---

## 2. 트랜잭션과 EntityManager

JPA에서 트랜잭션은 단순히 commit, rollback만 담당하지 않는다.
영속성 컨텍스트가 의미 있게 동작하는 범위를 만들어 준다.

스프링에서는 보통 서비스 계층에 `@Transactional`을 붙인다.

```java
@Service
public class MemberService {

  @Transactional
  public void updateMember(Long memberId, String name) {
    Member member = memberRepository.findById(memberId)
        .orElseThrow();

    member.changeName(name);
  }
}
```

트랜잭션이 시작되면 스프링은 현재 요청 흐름에서 사용할 `EntityManager`를 연결한다.
그리고 그 `EntityManager`가 영속성 컨텍스트를 사용한다.

중요한 점은 **같은 트랜잭션 안에서는 같은 영속성 컨텍스트가 유지된다**는 것이다.
그래서 같은 엔티티를 다시 조회했을 때 데이터베이스를 다시 조회하지 않고, 이미 관리 중인 객체를 반환할 수 있다.

---

## 3. 영속성 컨텍스트란?

영속성 컨텍스트(Persistence Context)는 엔티티를 저장하고 관리하는 논리적인 공간이다.
개발자가 `EntityManager`를 통해 엔티티를 저장하거나 조회하면, JPA는 해당 엔티티를 바로 데이터베이스와만 주고받는 것이 아니라 영속성 컨텍스트에 올려서 관리한다.

엔티티는 크게 네 가지 상태를 가진다.

| 상태 | 설명 |
| --- | --- |
| 비영속(new/transient) | 객체만 생성했고 JPA가 관리하지 않는 상태 |
| 영속(managed) | 영속성 컨텍스트가 관리하는 상태 |
| 준영속(detached) | 한 번 관리되었지만 현재는 분리된 상태 |
| 삭제(removed) | 삭제 대상으로 등록된 상태 |

예를 들어 아래 코드는 객체만 생성한 상태다.

```java
Member member = new Member("kim");
```

아직 JPA가 관리하지 않으므로 비영속 상태다.
이 객체를 저장하면 영속 상태가 된다.

```java
memberRepository.save(member);
```

영속 상태가 되었다고 해서 즉시 데이터베이스에 `insert` 쿼리가 실행된다는 뜻은 아니다.
JPA는 영속성 컨텍스트 안에 변경 사항을 모아 두고, flush 시점에 SQL로 반영한다.

---

## 4. 1차 캐시와 동일성 보장

영속성 컨텍스트는 내부에 1차 캐시를 가지고 있다.
엔티티를 한 번 조회하면 같은 트랜잭션 안에서는 다시 데이터베이스를 조회하지 않고 영속성 컨텍스트에 있는 엔티티를 반환할 수 있다.

```java
@Transactional
public void findTwice(Long memberId) {
  Member member1 = memberRepository.findById(memberId).orElseThrow();
  Member member2 = memberRepository.findById(memberId).orElseThrow();

  System.out.println(member1 == member2); // true
}
```

첫 번째 조회에서는 SQL이 실행된다.
하지만 같은 트랜잭션 안에서 같은 식별자로 다시 조회하면 JPA는 1차 캐시에 있는 엔티티를 반환한다.
그래서 `member1`과 `member2`는 같은 객체 참조가 된다.

이것을 동일성 보장이라고 한다.
데이터베이스의 같은 row를 같은 트랜잭션 안에서 하나의 객체처럼 다룰 수 있게 해 주는 기능이다.

---

## 5. 변경 감지와 쓰기 지연

JPA는 영속 상태의 엔티티가 변경되었는지 추적한다.
이를 변경 감지(Dirty Checking)라고 한다.

```java
@Transactional
public void changeName(Long memberId) {
  Member member = memberRepository.findById(memberId)
      .orElseThrow();

  member.changeName("lee");
}
```

위 코드에는 `memberRepository.save(member)`가 없다.
그래도 트랜잭션이 끝날 때 `update` 쿼리가 실행될 수 있다.

JPA는 엔티티가 영속 상태가 되는 순간의 값을 스냅샷으로 저장해 둔다.
flush 시점에 현재 엔티티 값과 스냅샷을 비교하고, 달라진 값이 있으면 `update` SQL을 만든다.

또 JPA는 `insert`, `update`, `delete` SQL을 바로 실행하지 않고 쓰기 지연 SQL 저장소에 모아 둘 수 있다.
그리고 flush 시점에 모아 둔 SQL을 데이터베이스에 전달한다.

이 구조 덕분에 개발자는 객체 상태를 변경하는 방식으로 코드를 작성하고, JPA는 트랜잭션 종료 시점에 필요한 SQL을 정리해서 실행할 수 있다.

---

## 6. flush와 commit은 다르다

flush는 영속성 컨텍스트의 변경 내용을 데이터베이스에 반영하는 동작이다.
여기서 주의할 점은 flush가 commit과 같지 않다는 것이다.

flush가 실행되면 SQL은 데이터베이스로 전달된다.
하지만 트랜잭션이 아직 commit되지 않았다면 다른 트랜잭션에서 그 변경 내용을 볼 수 없고, 이후 rollback도 가능하다.

flush가 발생하는 대표적인 시점은 다음과 같다.

- 트랜잭션 commit 직전
- JPQL 실행 직전
- `entityManager.flush()`를 직접 호출했을 때

예를 들어 JPQL은 데이터베이스를 직접 조회한다.
그래서 JPA는 JPQL 실행 전에 영속성 컨텍스트의 변경 사항을 먼저 flush해서 데이터 정합성을 맞춘다.

```java
@Transactional
public void flushBeforeQuery() {
  memberRepository.save(new Member("kim"));

  List<Member> members = entityManager
      .createQuery("select m from Member m", Member.class)
      .getResultList();
}
```

위 코드에서 `select` 쿼리를 실행하기 전에 `insert` 쿼리가 먼저 나갈 수 있다.
JPQL 조회 결과에 방금 저장한 엔티티가 포함되어야 하기 때문이다.

정리하면 흐름은 다음과 같다.

```text
엔티티 변경
  -> 영속성 컨텍스트에 변경 내용 누적
  -> flush 시 SQL 실행
  -> commit 시 트랜잭션 확정
```

---

## 7. 지연 로딩이란?

지연 로딩(Lazy Loading)은 연관된 엔티티를 실제로 사용할 때 조회하는 방식이다.

예를 들어 `Member`가 `Team`을 참조한다고 해 보자.

```java
@Entity
public class Member {

  @ManyToOne(fetch = FetchType.LAZY)
  private Team team;
}
```

`Member`를 조회할 때 `Team`까지 항상 필요한 것은 아니다.
그래서 `FetchType.LAZY`를 사용하면 JPA는 `Member`만 먼저 조회하고, `team`에는 실제 `Team` 객체 대신 프록시 객체를 넣어 둔다.

```java
@Transactional
public void findMember(Long memberId) {
  Member member = memberRepository.findById(memberId)
      .orElseThrow();

  Team team = member.getTeam(); // 아직 Team 조회 쿼리가 나가지 않을 수 있다
  String teamName = team.getName(); // 이 시점에 Team 조회 쿼리 실행
}
```

지연 로딩의 핵심은 **연관 객체가 필요한 순간까지 조회를 미루는 것**이다.
불필요한 조인을 줄일 수 있지만, 언제 쿼리가 실행되는지 예측하지 못하면 성능 문제나 예외를 만들 수 있다.

---

## 8. 프록시와 LazyInitializationException

지연 로딩은 프록시 객체를 통해 동작한다.
프록시는 실제 엔티티처럼 보이지만, 아직 데이터베이스에서 값을 가져오지 않은 대리 객체다.
프록시의 값을 처음 사용하는 순간 JPA는 영속성 컨텍스트를 통해 실제 데이터를 조회한다.

문제는 영속성 컨텍스트가 이미 닫힌 뒤에 프록시를 초기화하려고 할 때 발생한다.

```java
public MemberResponse getMember(Long memberId) {
  Member member = memberRepository.findById(memberId)
      .orElseThrow();

  return new MemberResponse(
      member.getId(),
      member.getTeam().getName()
  );
}
```

위 코드에서 트랜잭션이 없거나, 트랜잭션 밖에서 `member.getTeam().getName()`이 실행되면 `LazyInitializationException`이 발생할 수 있다.
프록시를 초기화하려면 영속성 컨텍스트가 필요한데, 이미 엔티티가 준영속 상태가 되었기 때문이다.

해결 방향은 상황에 따라 다르다.

- 서비스 로직 안에서 필요한 데이터를 트랜잭션 범위 안에 조회한다.
- 조회 전용 화면에서는 DTO 조회나 fetch join을 사용한다.
- 엔티티를 컨트롤러나 뷰까지 그대로 노출하지 않는다.

단순히 `FetchType.EAGER`로 바꾸는 것은 좋은 해결책이 아니다.
즉시 로딩은 예상하지 못한 조인과 추가 쿼리를 만들 수 있고, N+1 문제를 더 찾기 어렵게 만들 수 있다.

---

## 9. N+1 문제와 fetch join

지연 로딩을 사용하면 연관 객체를 실제로 사용할 때 쿼리가 나간다.
이 동작은 편리하지만 목록 조회에서는 N+1 문제로 이어질 수 있다.

```java
@Transactional(readOnly = true)
public void findMembers() {
  List<Member> members = memberRepository.findAll(); // Member 목록 조회 1번

  for (Member member : members) {
    System.out.println(member.getTeam().getName()); // Member 수만큼 Team 조회
  }
}
```

회원이 10명이라면 회원 목록 조회 1번, 각 회원의 팀 조회 10번이 실행될 수 있다.
그래서 총 11번의 쿼리가 실행된다.
이런 패턴을 N+1 문제라고 부른다.

이럴 때는 fetch join으로 필요한 연관 데이터를 한 번에 가져올 수 있다.

```java
@Query("select m from Member m join fetch m.team")
List<Member> findAllWithTeam();
```

fetch join을 사용하면 `Member`와 `Team`을 함께 조회해서 반복문 안에서 추가 쿼리가 발생하지 않도록 만들 수 있다.

다만 fetch join도 무조건 많이 쓰면 안 된다.
연관 관계가 많거나 컬렉션을 함께 조회하면 데이터가 중복되거나 페이징이 어려워질 수 있다.
목록 화면에서 필요한 데이터가 명확하다면 DTO 조회를 사용하는 것도 좋은 선택이다.

---

## 10. save를 남발하지 않아도 되는 이유

Spring Data JPA를 사용하면 수정 로직 끝에 습관적으로 `save()`를 다시 호출하는 경우가 있다.

```java
@Transactional
public void updateMember(Long memberId, String name) {
  Member member = memberRepository.findById(memberId)
      .orElseThrow();

  member.changeName(name);
  memberRepository.save(member); // 보통은 필요하지 않다
}
```

이미 조회된 `member`는 영속 상태다.
따라서 값을 변경하면 트랜잭션 commit 직전에 변경 감지가 동작한다.
이 경우 `save()`를 다시 호출하지 않아도 된다.

다만 모든 상황에서 `save()`가 불필요한 것은 아니다.
새 엔티티를 저장할 때는 `save()`가 필요하고, 준영속 상태의 엔티티를 병합해야 하는 특수한 상황에서는 `merge` 계열 동작이 필요할 수 있다.
하지만 일반적인 수정 로직에서는 **조회한 엔티티를 변경하고 트랜잭션을 종료한다**는 흐름이 JPA에 더 자연스럽다.

---

## 11. 실무에서 기억할 기준

JPA를 사용할 때는 SQL이 언제 보이는지만 보는 것보다, 엔티티가 지금 어떤 상태인지 먼저 생각해야 한다.

정리하면 다음과 같다.

- 트랜잭션은 영속성 컨텍스트가 동작하는 중요한 범위다.
- 영속성 컨텍스트는 1차 캐시, 동일성 보장, 변경 감지를 제공한다.
- 영속 상태 엔티티의 변경은 flush 시점에 SQL로 변환된다.
- flush는 SQL을 데이터베이스에 전달하지만 commit은 아니다.
- 지연 로딩은 프록시를 사용하고, 영속성 컨텍스트가 닫힌 뒤에는 초기화할 수 없다.
- 목록 조회에서 지연 로딩을 잘못 사용하면 N+1 문제가 생길 수 있다.
- 즉시 로딩으로 문제를 덮기보다 fetch join, DTO 조회, 트랜잭션 범위 설계를 먼저 검토하는 것이 좋다.

JPA의 동작이 예측되지 않을 때는 `save()`를 더 호출하거나 `FetchType.EAGER`로 바꾸기 전에, 현재 엔티티가 영속 상태인지, 트랜잭션 범위 안에 있는지, flush와 지연 로딩이 언제 발생하는지를 먼저 확인해 보는 것이 좋다.

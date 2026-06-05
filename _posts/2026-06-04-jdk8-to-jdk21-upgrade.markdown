---
layout: docs
title: "JDK 8에서 JDK 21로 업그레이드하기: 점검 포인트와 마이그레이션 순서"
date: 2026-06-04 11:53:28 +0900
categories: Java
badges:
- type: primary
  tag: Java
- type: secondary
  tag: JDK 21
---

<style>
.td-content .jdk-upgrade-post {
  color: #26323f;
}

.td-content .jdk-upgrade-post p,
.td-content .jdk-upgrade-post li {
  line-height: 1.82;
}

.td-content .jdk-upgrade-post h2 {
  margin-top: 3rem;
  padding-bottom: 0.45rem;
  border-bottom: 1px solid #d8dee4;
  color: #17212b;
  font-weight: 700;
}

.td-content .jdk-upgrade-post .subsection-details {
  margin: 1.35rem 0;
  border: 1px solid #cfd8e3;
  border-radius: 6px;
  background: #fff;
  box-shadow: 0 1px 2px rgba(16, 24, 40, 0.04);
  overflow: hidden;
}

.td-content .jdk-upgrade-post .subsection-details[open] {
  padding-bottom: 0.9rem;
}

.td-content .jdk-upgrade-post .subsection-details summary {
  padding: 0.9rem 1rem;
  background: #f3f7fa;
  color: #17212b;
  font-weight: 700;
  cursor: pointer;
  list-style-position: inside;
}

.td-content .jdk-upgrade-post .subsection-details summary:hover {
  background: #edf3f8;
}

.td-content .jdk-upgrade-post .subsection-details > *:not(summary) {
  margin-left: 1rem;
  margin-right: 1rem;
}

.td-content .jdk-upgrade-post .topic-details {
  margin: 1rem;
  border: 1px solid #d8dee4;
  border-radius: 5px;
  background: #fff;
  overflow: hidden;
}

.td-content .jdk-upgrade-post .topic-details[open] {
  padding-bottom: 0.8rem;
}

.td-content .jdk-upgrade-post .topic-details summary {
  padding: 0.75rem 0.9rem;
  background: #fbfcfd;
  color: #243447;
  font-weight: 700;
  cursor: pointer;
  list-style-position: inside;
}

.td-content .jdk-upgrade-post .topic-details summary:hover {
  background: #f3f7fa;
}

.td-content .jdk-upgrade-post .topic-details > *:not(summary) {
  margin-left: 0.9rem;
  margin-right: 0.9rem;
}

.td-content .jdk-upgrade-post h3 {
  margin-top: 2.2rem;
  color: #243447;
  font-weight: 700;
}

.td-content .jdk-upgrade-post h4 {
  margin-top: 1.6rem;
  color: #35475a;
  font-weight: 700;
}

.td-content .jdk-upgrade-post hr {
  margin: 2.4rem 0;
  border-top: 1px solid #e6ebf0;
}

.td-content .jdk-upgrade-post a {
  color: #2563a8;
}

.td-content .jdk-upgrade-post a:hover {
  color: #173f70;
  text-decoration: underline;
}

.td-content .jdk-upgrade-post table {
  min-width: 760px;
  margin: 1.1rem 0 1.6rem;
  border: 1px solid #cfd8e3;
  border-collapse: collapse;
  font-size: 0.92rem;
  line-height: 1.55;
  background: #fff;
  box-shadow: 0 1px 2px rgba(16, 24, 40, 0.04);
}

.td-content .jdk-upgrade-post table:first-of-type {
  min-width: 960px;
}

.td-content .jdk-upgrade-post table th {
  background: #edf3f8;
  color: #17212b;
  font-weight: 700;
  white-space: nowrap;
}

.td-content .jdk-upgrade-post table th,
.td-content .jdk-upgrade-post table td {
  padding: 0.65rem 0.8rem;
  border: 1px solid #cfd8e3 !important;
  vertical-align: top;
}

.td-content .jdk-upgrade-post table tbody tr:nth-child(even) {
  background: #f8fafc;
}

.td-content .jdk-upgrade-post table tbody tr:hover {
  background: #eef6ff;
}

.td-content .jdk-upgrade-post pre,
.td-content .jdk-upgrade-post .highlight pre,
.td-content .jdk-upgrade-post .highlight code,
.td-content .jdk-upgrade-post pre code {
  font-size: 0.82rem;
  line-height: 1.55;
}

.td-content .jdk-upgrade-post pre,
.td-content .jdk-upgrade-post .highlight {
  border: 1px solid #d8dee4;
  border-radius: 6px;
  box-shadow: 0 1px 2px rgba(16, 24, 40, 0.05);
}

.td-content .jdk-upgrade-post p code,
.td-content .jdk-upgrade-post li > code,
.td-content .jdk-upgrade-post table code {
  padding: 0.12rem 0.3rem;
  border: 1px solid #e2e8f0;
  border-radius: 4px;
  background: #f6f8fa;
  color: #9f2f24;
}

.td-content .jdk-upgrade-post .post-toc {
  margin: 0 0 2.2rem;
  padding: 1.05rem 1.2rem;
  border: 1px solid #cfd8e3;
  border-left: 4px solid #30638e;
  background: #f8fbfd;
  box-shadow: 0 1px 2px rgba(16, 24, 40, 0.04);
}

.td-content .jdk-upgrade-post .post-toc strong {
  display: block;
  margin-bottom: 0.5rem;
  color: #17212b;
  font-size: 0.98rem;
}

.td-content .jdk-upgrade-post .post-toc ul {
  margin: 0;
  padding-left: 1.2rem;
}

.td-content .jdk-upgrade-post .post-toc li {
  margin: 0.28rem 0;
  line-height: 1.6;
}

.td-content .jdk-upgrade-post .post-toc a {
  color: #244a70;
  font-weight: 600;
}
</style>

<div class="jdk-upgrade-post" markdown="1">

<nav class="post-toc" aria-label="목차">
  <strong>목차</strong>
  <ul>
    <li><a href="#impact-scope">1. 먼저 JDK 변경의 영향 범위를 파악한다</a></li>
    <li><a href="#jdk21-changes">2. JDK 21 주요 변경점</a></li>
    <li><a href="#jdk-version-notes">3. JDK 버전별 주요 변경점 및 고려사항</a></li>
    <li><a href="#spring-boot-notes">4. Spring Boot 버전별 주요 변경점 및 고려사항</a></li>
    <li><a href="#migration-record">5. 실제 마이그레이션 진행 기록</a></li>
    <li><a href="#conclusion">6. 마치며</a></li>
  </ul>
</nav>

<script>
document.addEventListener('DOMContentLoaded', function () {
  document.querySelectorAll('.jdk-upgrade-post .post-toc a[href^="#"]').forEach(function (link) {
    link.addEventListener('click', function (event) {
      var target = document.querySelector(link.getAttribute('href'));

      if (!target) {
        return;
      }

      event.preventDefault();

      var targetTop = target.getBoundingClientRect().top + window.pageYOffset;
      var scrollTop = Math.max(targetTop - window.innerHeight * 0.3, 0);

      window.scrollTo({
        top: scrollTop,
        behavior: 'smooth'
      });

      history.pushState(null, '', link.getAttribute('href'));
    });
  });
});
</script>

JDK8은 오랫동안 현업 표준처럼 사용된 버전이라, 지금도 레거시 프로젝트의 기준선으로 남아 있는 경우가 많다.  
이럴 경우 레거시 프로젝트는 무조건 업그레이드 대상이며, 배척해야 할 것이라고 생각하는 사람들이 있다.  

그러나 JDK 8을 무작정 업그레이드하게 되면 Java 문법, 프레임워크, 라이브러리, 운영 환경 등 서비스 운영 자체에 위험 요소가 되기도 한다.  
그렇기 때문에 더더욱 JDK 업그레이드는 고려되어야 한다.  

더욱이 JDK 8의 일부 배포 업체는 무료 지원 기간을 늘리기도 했다.
다음 일정을 참고하여 JDK 버전 업그레이드 작업 일정을 산정해도 좋겠다.
(여기서 말하는 "무료 지원"은 배포판을 비용 없이 사용할 수 있고 보안 업데이트가 제공되는지를 의미)

이 명령어를 통해 현재 사용하는 JDK 배포판을 알 수 있다.
````bash
java -XshowSettings:properties -version
java.vendor 부분을 확인하면 된다.
````

| 배포판 | JDK 8 무료 업데이트 기준                    | JDK 8 지원 종료 시점           | 비고                                                |
| --- |-------------------------------------|--------------------------|---------------------------------------------------|
| [Amazon Corretto 8](https://aws.amazon.com/corretto/faqs/) | 비용 없이 사용 가능하며 LTS 보안 업데이트 제공        | 2030년 12월                | JavaFX 지원은 2026년 3월 31일 종료                        |
| [Eclipse Temurin 8](https://adoptium.net/support/) | Adoptium 커뮤니티가 제공하는 무료 OpenJDK 배포판  | 최소 2030년 12월             | 별도 SLA가 필요하면 상용 지원 업체 검토 필요                       |
| [Red Hat OpenJDK](https://access.redhat.com/articles/1299013) | RHEL 및 Red Hat 제품 구독 범위에서 제공        | 2026년 11월                | RHEL 버전과 구독 상태에 따라 실제 업데이트 제공 범위가 달라질 수 있음        |
| [Oracle JDK 8](https://www.oracle.com/java/technologies/java-se-support-roadmap.html) | 개인, 개발, 일부 허용된 용도는 무료               | 개인/개발 용도 공개 업데이트는 종료일 미정 | 상용 운영 업데이트는 2019년 4월 이후 Oracle 지원 계약 필요           |
| [Microsoft Build of OpenJDK](https://learn.microsoft.com/en-us/java/openjdk/support) | Microsoft 자체 JDK 8 LTS 배포판은 제공하지 않음 | Temurin 정책을 따름           | Azure의 일부 Java 8 런타임은 Eclipse Temurin 8에 의존       |
| [Ubuntu OpenJDK](https://ubuntu.com/security/esm) | Ubuntu 패키지 저장소에서 제공하는 OpenJDK       | Ubuntu 릴리스 지원 기간에 따름     | JDK 자체 지원 기간이 아니라 Ubuntu 버전과 보안 업데이트 제공 기간에 따라 달라짐 |

상용 서비스라면 바로 버전을 많이 올리는 것보다 LTS 버전 기준으로 한 단계씩 올라가는 것이 적당할 듯 하다.  
나의 경우, 개인 프로젝트이기 때문에 JDK8 -> JDK21로 업그레이드 하는 기록을 올릴 것이나,  
JDK11, JDK17 업그레이드 검토사항도 함께 참고하였다.  

---

<a id="impact-scope"></a>
## 1. 먼저 JDK 변경의 영향 범위를 파악한다

JDK를 바꾸려고 마음 먹었다면 가장 먼저 해야 할 일은 현재 JDK 변경에 영향을 받는 부분이 무엇인지 파악하는 것이다.

Java문법은 당연히 고친다고 생각하고, 그 외의 JDK버전에 영향을 미치는 프레임워크, 라이브러리 버전을 확인한다.
이걸 확인해야 영향 범위를 파악할 수 있다.

나의 경우 소규모 개인프로젝트 였기 때문에 이정도만 확인하면 되었다. 
spring boot, mybatis, ibm-cos-sdk

JDK21 버전 호환성에 맞춘 라이브러리 버전 확인이 필요하다.

| 구분                          | JDK 8 | JDK 11 | JDK 17 | JDK 21 |
|-----------------------------| --- | --- | --- | --- |
| Spring Boot                 | 2.7.x 이하 | 2.7.x 이하 | 3.0.x 이상 | 2.7.18 또는 3.0.x 이상 |
| MyBatis Spring Boot Starter | 2.3.x | 2.3.x | 3.0.x | 3.0.x |
| MyBatis-Spring              | 2.1.x | 2.1.x | 3.0.x | 3.0.x |
| Lombok                      | 1.18.x | 1.18.x | 1.18.22 이상 | 1.18.30 이상 |
| `ibm-cos-sdk-java` 계열       | 2.x | 2.x | 2.x | 2.x |

여기서 약간의 문제가 생기게 되는 것이다.  
나는 레거시 프로젝트로 Spring Boot버전이 2.6.11버전, MyBatis Spring Boot Starter2.1.3버전 이다.

JDK21 업그레이드, Spring Boot/MyBatis 업그레이드 2분류의 업그레이드가 필요하다.  
한꺼번에 업그레이드 하는것은 위험부담이 있다.  
만약에 오류가 날 경우 어떤부분에서 어떻게 문제가 되는지 혼동될 수 있기 때문이다.  

그래서 나의 경우 순서를 정했다.  
1. Spring Boot 2.7.18버전/MyBatis 2.3.2버전 으로 업그레이드하면서 Spring Security 설정 전환  
2. JDK 21 + Spring Boot 3.5.14버전/MyBatis 3.0.3버전 기반으로 업그레이드  

우선 JDK 21 업그레이드 변경점을 살펴본 후 Spring Boot 변경점을 살펴보기로 했다.  
MyBatis는 확인해보니 큰 변경이 없어 기존에 사용하던 쿼리가 잘 실행되는지만 확인하면 됐다.

---

<a id="jdk21-changes"></a>
## 2. JDK 21 주요 변경점

<details class="subsection-details" markdown="1">
<summary>2-1) 가상 스레드 (Virtual Threads)의 등장</summary>

가장 큰 업데이트 기능은 가상 스레드(Project Loom)이다.
이 기능 하나만으로도 JDK21로 넘어갈 이유가 충분하다고 보는 이가 있을 정도이다.

#### 2-1-1) 가상 스레드 설명

기존 자바의 스레드(Platform Thread)는 운영체제(OS)의 스레드와 1:1로 매핑되는 구조이다.
플랫폼 스레드는 생성 비용이 비싸고(메모리 사용, 컨텍스트 스위칭 비용) OS스펙에 따라 개수 제한을 두어야만 한다.
동시 처리를 하려면 비동기 프로그래밍을 사용해야만 했다.

그러나 JDK21의 가상 스레드는 JVM 내부에서 관리되는 경량 스레드이다.
OS스레드 하나 위에서 다수의 가상 스레드가 번갈아 가며 실행될 수 있다.

덕분에 기존 동기 방식의 코드를 사용하면서도, 비동기 프로그래밍 수준의 처리량을 낼 수 있게 된다.
플랫폼 스레드와 가상 스레드를 다같이 관리해야 해서 관리포인트가 늘어난다는 단점은 있다.

#### 2-1-2) 가상스레드 사용 방법  

```java
import java.time.Duration;
import java.util.concurrent.Executors;
import java.util.stream.IntStream;

public class VirtualThreadExample {
    public static void main(String[] args) {

        // JDK 21에서 도입된 가상 스레드 실행자 사용
        // try-with-resources 구문 종료 시 executor.close()가 호출되어 
        // 제출된 10,000개의 가상 스레드 작업이 모두 완료될 때까지 메인 스레드가 대기
        try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
            
            // 10,000개의 작업을 가상 스레드로 동시에 실행
            IntStream.range(0, 10_000).forEach(i -> {
                executor.submit(() -> {
                    try {
                        // 스레드를 1초간 잠재움 (Blocking I/O 시뮬레이션)
                        Thread.sleep(Duration.ofSeconds(1)); 
                        return i;
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                });
            });
        } // executor close 시점에 제출된 작업이 완료될 때까지 기다린다.
    }
}
```

</details>

<details class="subsection-details" markdown="1">
<summary>2-2) 시퀀스 컬렉션 (Sequenced Collections)</summary>

#### 2-2-1) 시퀀스 컬렉션 설명
자바의 컬렉션은 강력하지만 '순서가 있는 데이터'를 다룰 때 일관성이 부족했다.
예를 들어, List는 get(0)으로 첫 요소를 가져오지만 Deque는 getFirst()
, SortedSet은 first()를 사용하는 등 함수가 파편화 되어있었다.

그러나 JDK21에서 추가된 시퀀스 컬렉션을 사용하면 통일된 접근방식을 사용할 수 있다.
SequencedCollection, SequencedSet, SequencedMap이라는 새로운 인터페이스를 도입하여 계층 구조를 재정비 했다.
첫 번째 요소와 마지막 요소를 조회하거나 추가/삭제하는 방법이 통일되었다.
또한, reversed() 메소드를 통해 원본 데이터를 건드리지 않고도 역순 뷰(View)를 손쉽게 얻을 수 있게 되었다.

#### 2-2-2) 시퀀스 컬렉션 사용 방법

```java
import java.util.ArrayList;
import java.util.LinkedHashMap;
import java.util.List;
import java.util.SequencedCollection;
import java.util.SequencedMap;

public class SequencedCollectionExample {
    public static void main(String[] args) {
        // 1. List (SequencedCollection 구현체)
        SequencedCollection<String> list = new ArrayList<>();
        list.add("Java");
        list.add("Kotlin");
        list.add("Python");

        // JDK 21의 새로운 메서드들
        list.addFirst("C++"); // 맨 앞에 추가
        list.addLast("Go");   // 맨 뒤에 추가

        System.out.println("전체 리스트: " + list);
        System.out.println("첫 번째 요소: " + list.getFirst());
        System.out.println("마지막 요소: " + list.getLast());
        
        // 2. Map (SequencedMap 구현체) - LinkedHashMap 등 순서가 있는 맵
        SequencedMap<String, Integer> map = new LinkedHashMap<>();
        map.put("Apple", 1000);
        map.put("Banana", 2000);
        
        map.putFirst("Orange", 1500); // 맨 앞에 엔트리 추가
        map.putLast("Grape", 3000);   // 맨 뒤에 엔트리 추가

        System.out.println("전체 맵: " + map);
        System.out.println("첫 번째 키: " + map.firstEntry().getKey());
        System.out.println("역순 맵 뷰: " + map.reversed());
    }
}

/*
[코드 실행 결과값]
전체 리스트: [C++, Java, Kotlin, Python, Go]
첫 번째 요소: C++
마지막 요소: Go
전체 맵: {Orange=1500, Apple=1000, Banana=2000, Grape=3000}
첫 번째 키: Orange
역순 맵 뷰: {Grape=3000, Banana=2000, Apple=1000, Orange=1500}
*/
```

</details>

<details class="subsection-details" markdown="1">
<summary>2-3) 레코드 패턴 (Record Patterns)</summary>

record는 JDK 14에서 도입되었다. 불변 데이터 객체를 만드는 데 유용하다.
JDK 21에서는 이 record를 강력하게 사용할 수 있도록 레코드 패턴이 정식 기능으로 추가됐다.

#### 2-3-1) 레코드 패턴 설명

기존에는 객체의 타입 확인(instanceof) 후, 내부에 있는 데이터를 꺼내 쓰려면 명시적인 형 변환(Casting)과 접근자 메서드 (Getter)호출이 필요했다. 
그러나 레코드 패턴을 사용하면 이를 한번에 할 수 있다.
타입 확인과 동시에 내부 필드 값을 바로 변수로 추출할 수 있기 때문에 코드를 간결하고 직관적으로 작성할 수 있다.

#### 2-3-2) 레코드 패턴 사용 방법

```java
public class RecordPatternExample {
    // Record 정의
    record Point(int x, int y) {}
    record ColoredPoint(Point p, String color) {}

    public static void printPoint(Object obj) {
        // JDK 17 스타일 (여전히 조금 번거로움)
        if (obj instanceof Point p) {
            System.out.println("JDK 17: x=" + p.x() + ", y=" + p.y());
        }

        // JDK 21 스타일: 레코드 패턴 매칭
        // Point 내부의 x, y를 바로 변수로 추출
        if (obj instanceof Point(int x, int y)) {
            System.out.println("JDK 21: x=" + x + ", y=" + y);
        }
    }
    
    // 중첩된 레코드 패턴 (Nested Record Patterns)
    public static void printColoredPoint(Object obj) {
        // ColoredPoint 안에 있는 Point의 x, y까지 한 번에 추출!
        if (obj instanceof ColoredPoint(Point(int x, int y), String color)) {
             System.out.println("Color: " + color + ", Position: " + x + "," + y);
        }
    }

    public static void main(String[] args) {
        Point point = new Point(10, 20);
        ColoredPoint coloredPoint = new ColoredPoint(point, "Red");

        printPoint(point);
        printColoredPoint(coloredPoint);
    }
}

/*
[코드 실행 결과값]
JDK 17: x=10, y=20
JDK 21: x=10, y=20
Color: Red, Position: 10,20
*/
```

</details>

<details class="subsection-details" markdown="1">
<summary>2-4) Switch 패턴 매칭 (Pattern Matching for Switch)</summary>

#### 2-4-1) Switch 패턴 설명

switch의 case문에 타입을 지정할 수 있게 되었다. 
when 키워드를 사용해 추가적인 조건(Guarded Pattern)을 붙일 수 있다.
이를 통해 if-else if 체인을 깔끔하고 가독성 높은 switch표현식으로 대체할 수 있다.

#### 2-4-2) Switch 패턴 사용 방법

```java
public class SwitchPatternExample {
    public static String formatterPatternSwitch(Object obj) {
        return switch (obj) {
            case Integer i -> String.format("정수: %d", i);
            case Long l    -> String.format("Long 타입: %d", l);
            case Double d  -> String.format("실수: %f", d);
            
            // when 절을 사용한 추가 조건 (Guarded Pattern)
            case String s when s.length() > 5 -> "긴 문자열: " + s;
            case String s                     -> "짧은 문자열: " + s;
            
            // null 처리 가능
            case null      -> "널 값입니다";
            
            default        -> obj.toString();
        };
    }

    public static void main(String[] args) {
        System.out.println(formatterPatternSwitch(10));
        System.out.println(formatterPatternSwitch("Hello World"));
        System.out.println(formatterPatternSwitch("Hi"));
        System.out.println(formatterPatternSwitch(null));
    }
}

/*
[코드 실행 결과값]
정수: 10
긴 문자열: Hello World
짧은 문자열: Hi
널 값입니다
*/
```

</details>

<details class="subsection-details" markdown="1">
<summary>2-5) 세대별 ZGC (Generational ZGC, JEP 439)</summary>

#### 2-5-1) ZGC 설명

ZGC는 대용량 메모리에서도 짧은 지연 시간(Low Latency)을 보장하는 GC이다.
JDK 21에서는 ZGC에 '세대별(Generational)' 개념이 도입되어 금방 사라지는 객체(Young부분 DTO, List, String등)와
오래 남는 객체(Old부분, CacheManager, DataSource, Spring Bean등)을 구분해서 관리함으로써
CPU사용량은 줄이고 처리량을 높이며 초저지연을 구현 했다.

#### 2-5-2) ZGC 사용 방법

VM 옵션으로 아래 값을 추가하여 활성화할 수 있음.

```text
-XX:+UseZGC -XX:+ZGenerational
```

</details>

---

<a id="jdk-version-notes"></a>
## 3. JDK 버전별 주요 변경점 및 고려사항

- 모듈 시스템 (Java 9): 내부 API 접근이 제한될 수 있다.(Java 8까지는 JDK 내부 구현에 대한 접근이 비교적 자유로웠음)  
리플렉션을 사용하는 경우 점검해보아야 하며, jdeps --jdk-internals 명령으로 의존성 확인 필요
- UTF-8 기본화 (JEP 400): JDK 18부터 기본 인코딩이 UTF-8로 고정되었다.   
한글 윈도우(MS949) 환경에서 파일 입출력을 하던 레거시 코드가 있다면 인코딩 설정(-Dfile.encoding=COMPAT)을 확인해야 한다.
- 32비트 지원 중단: JDK 21부터는 윈도우 32비트를 지원하지 않는다.
- 제거된 API: Thread.stop(), suspend(), resume() 등 오랫동안 Deprecated였던 메소드 사용 불가
- 가상 스레드 피닝(Pinning): `synchronized` 자체가 항상 문제인 것은 아니지만,
`synchronized` 영역 안에서 Blocking I/O나 오래 걸리는 작업이 실행되면 carrier thread가 붙잡혀
가상 스레드의 이점이 줄어들 수 있다. 이런 구간은 `ReentrantLock`으로 대체하거나 임계 영역을 줄일 수 있는지 검토가 필요하다.
- Spring Boot 3.2+: 3.0이나 3.1을 사용하고 있다면 3.2버전 이상으로 올려야 가상스레드 설정이 편리하다.

---

<a id="spring-boot-notes"></a>
## 4. Spring Boot 버전별 주요 변경점 및 고려사항

### 4-1) Spring Boot 3.0

<details class="topic-details" markdown="1">
<summary>4-1-1) Spring MVC의 trailing slash 매칭 변경</summary>

Spring Boot 3.0부터는 `/users`와 `/users/`를 같은 경로로 자동 매칭하지 않는 방향으로 바뀌었다.  
기존 API 호출이나 프론트엔드 코드에서 마지막 `/`가 섞여 들어오는 경우가 있다면 404가 발생할 수 있으므로, 주요 컨트롤러 URL을 실제 호출 기준으로 확인해야 한다.

레거시 호환성을 위해 예전처럼 trailing slash를 허용하고 싶다면 아래 설정을 임시로 추가할 수 있다.

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        configurer.setUseTrailingSlashMatch(true);
    }
}
```

다만 Spring Boot 3.0부터는 `setUseTrailingSlashMatch(true)`는 deprecated된 임시 호환 방식이다.  
장기적으로는 `/users`, `/users/` 호출을 명시적으로 정리하는 편이 좋다.

</details>

<details class="topic-details" markdown="1">
<summary>4-1-2) 설정 프로퍼티 변경</summary>

Spring Boot 3.0부터는 2.x에서 deprecated 되었던 프로퍼티가 제거되거나 이름이 바뀐 경우가 있다.  
`application.yml`을 그대로 두고 올렸을 때 property binding 실패가 나면, 임시로 `spring-boot-properties-migrator`를 추가해 어떤 설정을 바꿔야 하는지 확인할 수 있다.

Gradle을 사용한다면 아래 의존성을 임시로 추가한다.

```gradle
runtimeOnly("org.springframework.boot:spring-boot-properties-migrator")
```

Maven을 사용한다면 아래처럼 추가한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-properties-migrator</artifactId>
    <scope>runtime</scope>
</dependency>
```

의존성을 추가한 뒤 애플리케이션을 실행하면, 변경이 필요한 설정 프로퍼티가 로그에 출력된다.

```text
The use of configuration keys that are no longer supported was found in the environment:

Property source 'Config resource ... application.yml':
    Key: spring.mvc.pathmatch.matching-strategy
        Reason: ...
```

이 로그를 기준으로 `application.yml`이나 `application.properties`의 설정명을 수정하면 된다.  
단, `spring-boot-properties-migrator`는 운영에 계속 넣어두는 의존성이 아니라 마이그레이션 확인용이다.  
프로퍼티를 수정하고 애플리케이션이 정상 기동되는 것을 확인했다면 반드시 제거해야 한다.

</details>

<details class="topic-details" markdown="1">
<summary>4-1-3) `RestTemplate`에서 Apache HttpClient를 사용하는 경우</summary>

Spring Boot 3.0부터는 `RestTemplate`에서 Apache HttpClient 4 기반 설정을 그대로 사용하기 어렵고 HttpClient 5 계열을 사용해야 한다.  
`HttpComponentsClientHttpRequestFactory`나 커스텀 `RestTemplateBuilder` 설정을 사용하고 있다면 관련 의존성과 생성 코드를 확인해야 한다.

`RestTemplate` 자체가 없어졌다는 뜻은 아니다. `RestTemplate`에 연결해서 쓰던 Apache HttpClient 버전이 문제이다.  
Spring Boot 2.x, Spring Framework 5.x에서는 Apache HttpClient 4 기반 설정을 많이 사용했다.

```java
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.springframework.http.client.HttpComponentsClientHttpRequestFactory;
import org.springframework.web.client.RestTemplate;

CloseableHttpClient httpClient = HttpClients.custom()
    .setMaxConnTotal(100)
    .setMaxConnPerRoute(20)
    .build();

HttpComponentsClientHttpRequestFactory factory =
    new HttpComponentsClientHttpRequestFactory(httpClient);

RestTemplate restTemplate = new RestTemplate(factory);
```

그러나 Spring Boot 3.x, Spring Framework 6.x에서는 `HttpComponentsClientHttpRequestFactory`가 Apache HttpClient 5 기준으로 동작한다.  
그래서 기존처럼 `org.apache.http.*` 패키지의 HttpClient 4 객체를 넘기면 타입이 맞지 않아 컴파일 오류가 날 수 있다.

```java
// HttpClient 4 계열
org.apache.http.impl.client.CloseableHttpClient

// HttpClient 5 계열
org.apache.hc.client5.http.impl.classic.CloseableHttpClient
```

Boot 3 기준으로는 아래처럼 HttpClient 5 패키지를 사용해야 한다.

```java
import org.apache.hc.client5.http.impl.classic.CloseableHttpClient;
import org.apache.hc.client5.http.impl.classic.HttpClients;
import org.springframework.boot.web.client.RestTemplateBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.http.client.HttpComponentsClientHttpRequestFactory;
import org.springframework.web.client.RestTemplate;

@Bean
RestTemplate restTemplate(RestTemplateBuilder builder) {
    CloseableHttpClient httpClient = HttpClients.custom()
        .build();

    HttpComponentsClientHttpRequestFactory factory =
        new HttpComponentsClientHttpRequestFactory(httpClient);

    return builder
        .requestFactory(() -> factory)
        .build();
}
```

따라서 업그레이드할 때는 `pom.xml`이나 `build.gradle`에 `org.apache.httpcomponents:httpclient` 같은 HttpClient 4 의존성이 남아 있는지 확인해야 한다.  
그리고 코드에서 `org.apache.http.*` import를 사용하는 `RestTemplate` 설정이 있다면 `org.apache.hc.client5.*` 기반 코드로 바꿔야 한다.

</details>

<details class="topic-details" markdown="1">
<summary>4-1-4) Actuator endpoint 변경</summary>

Spring Boot 3.0부터 Actuator의 `httptrace` endpoint는 제거되고 `httpexchanges` endpoint로 대체되었다.  
또한 `/env`, `/configprops` endpoint의 값 마스킹 정책도 달라졌다.  

`httptrace`는 애플리케이션으로 들어온 HTTP 요청과 응답 정보를 Actuator로 확인할 때 사용하던 endpoint이다.  
예를 들어 운영에서 최근 요청 URI, 응답 상태 코드, 처리 시간, 헤더 일부를 확인하는 용도로 사용했을 수 있다.  
Spring Boot 3.0부터는 이 이름이 `httpexchanges`로 바뀌었으므로, 기존에 `/actuator/httptrace`를 호출하던 모니터링 도구나 스크립트가 있다면 `/actuator/httpexchanges` 기준으로 수정해야 한다.

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,httpexchanges
```

그리고 `httpexchanges` endpoint를 노출한다고 해서 항상 요청 기록이 쌓이는 것은 아니다.  
요청/응답 교환 정보를 저장할 `HttpExchangeRepository` Bean이 필요하므로, 실제로 endpoint 응답이 비어 있거나 Bean 관련 오류가 난다면 아래처럼 저장소를 등록해서 확인할 수 있다.

```java
import org.springframework.boot.actuate.web.exchanges.HttpExchangeRepository;
import org.springframework.boot.actuate.web.exchanges.InMemoryHttpExchangeRepository;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ActuatorConfig {

    @Bean
    HttpExchangeRepository httpExchangeRepository() {
        return new InMemoryHttpExchangeRepository();
    }
}
```

단, `InMemoryHttpExchangeRepository`는 이름 그대로 메모리에 최근 요청 정보만 보관하는 방식이다.  
운영에서 장기 보관이나 상세 분석이 필요하다면 로그 수집 시스템이나 APM으로 보는 편이 더 적절하다.

`/env`, `/configprops`의 마스킹 정책 변경도 같이 봐야 한다.  
기존에는 `password`, `secret`, `key`, `token` 같은 이름 패턴을 기준으로 민감값을 가리는 방식에 익숙했을 수 있다.  
Spring Boot 3.0부터는 값 노출 정책이 더 보수적으로 바뀌었기 때문에, 운영에서 `/actuator/env`나 `/actuator/configprops`를 보고 설정값을 직접 확인하던 방식이 달라질 수 있다.

```yaml
management:
  endpoint:
    env:
      show-values: never
    configprops:
      show-values: never
```

값을 꼭 확인해야 하는 내부 환경이라면 `show-values` 설정을 조정할 수 있지만, 운영에서는 민감 정보 노출 위험이 있으므로 기본적으로는 열어두지 않는 것이 좋다.  
결국 이 변경의 핵심은 Actuator endpoint URL만 바뀐 것이 아니라, endpoint를 노출하는 설정, 데이터를 쌓는 방식, 민감값을 보여주는 정책까지 함께 확인해야 한다는 점이다.

</details>

### 4-2) Spring Boot 2.7

<details class="topic-details" markdown="1">
<summary>4-2-1) Spring Security 설정 변경</summary>

Spring Boot 2.7부터는 `WebSecurityConfigurerAdapter` 기반 설정을 그대로 가져가기 어렵다.  
`SecurityFilterChain` Bean을 등록하는 방식으로 바꾸고, `antMatchers`, `mvcMatchers` 대신 `requestMatchers` 기준으로 권한 설정을 정리해야 한다.
또한 Spring Boot 2.7부터는 dispatcher type별 보안 필터 적용 방식도 달라질 수 있으므로, forward/error dispatch를 쓰는 예외 페이지나 내부 포워딩이 있다면 함께 확인해야 한다.

[AS-IS]
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            .authorizeRequests()
                .antMatchers("/", "/login", "/assets/**", "/error").permitAll()
                .antMatchers("/admin/**").hasRole("ADMIN")
                .mvcMatchers("/api/**").authenticated()
                .anyRequest().permitAll()
            .and()
            .formLogin()
                .loginPage("/login")
                .permitAll()
            .and()
            .logout()
                .logoutUrl("/logout");
    }
}
```

[TO-BE]
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
                .csrf(csrf -> csrf.disable())
                .authorizeHttpRequests(auth -> auth
                        // error page dispatch는 인증 실패로 다시 막히지 않도록 허용
                        .dispatcherTypeMatchers(DispatcherType.ERROR).permitAll()

                        // 정적 리소스와 공개 페이지
                        .requestMatchers("/", "/login", "/assets/**", "/error").permitAll()

                        // 권한이 필요한 URL
                        .requestMatchers("/admin/**").hasRole("ADMIN")
                        .requestMatchers("/api/**").authenticated()

                        // 나머지는 인증 필요
                        .anyRequest().authenticated()
                )
                .formLogin(form -> form
                        .loginPage("/login")
                        .permitAll()
                )
                .logout(logout -> logout
                        .logoutUrl("/logout")
                );

        return http.build();
    }
}
```

여기서 주의할 점은 `requestMatchers`가 단순히 `antMatchers` 이름만 바뀐 것이 아니라는 점이다.  
Spring MVC 기반 매칭이 필요한 경우에는 `HandlerMappingIntrospector`를 주입받아 `MvcRequestMatcher`를 명시적으로 사용하는 편이 안전하다.

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.servlet.util.matcher.MvcRequestMatcher;
import org.springframework.web.servlet.handler.HandlerMappingIntrospector;

@Configuration
public class SecurityConfig {

    @Bean
    SecurityFilterChain securityFilterChain(
            HttpSecurity http,
            HandlerMappingIntrospector introspector
    ) throws Exception {
        MvcRequestMatcher.Builder mvc = new MvcRequestMatcher.Builder(introspector);

        http
                .authorizeHttpRequests(auth -> auth
                        .requestMatchers(mvc.pattern("/admin/**")).hasRole("ADMIN")
                        .requestMatchers(mvc.pattern("/api/**")).authenticated()
                        .anyRequest().authenticated()
                );

        return http.build();
    }
}
```

예외 페이지를 `/error`로 forward하거나, 컨트롤러에서 내부 forward를 쓰고 있다면 실제 요청 흐름도 같이 확인해야 한다.

```java
@Controller
public class PageController {

    @GetMapping("/old-page")
    public String oldPage() {
        // FORWARD dispatcher type으로 다시 들어오는 요청
        return "forward:/new-page";
    }

    @GetMapping("/new-page")
    public String newPage() {
        return "new-page";
    }
}
```

이런 경우 예외 페이지 처리를 위한 `ERROR` dispatcher type은 막히지 않게 하고, `FORWARD` dispatcher type은 내부 포워딩 대상에 민감한 경로가 있는지 확인한 뒤 허용 범위를 정해야 한다.  
`FORWARD`를 전체 허용하면 내부 포워딩으로 접근되는 경로가 보안 규칙을 우회하는 것처럼 보일 수 있으므로, 필요한 경우에만 별도로 허용하는 편이 안전하다.  
예외 페이지가 인증 실패로 다시 막히면 사용자는 원래 오류 대신 403이나 리다이렉트 루프를 볼 수 있으므로, 업그레이드 후에는 일부러 404, 500 상황을 만들어 `/error` 처리까지 확인하는 것이 좋다.

</details>

<details class="topic-details" markdown="1">
<summary>4-2-2) 직접 만든 starter나 자동 설정 등록 방식</summary>

Spring Boot 2.7부터 자동 설정 등록 방식으로 `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` 파일이 도입되었다.  
Spring Boot 3.0부터는 `spring.factories`의 `EnableAutoConfiguration` 키만으로 자동 설정을 등록하는 방식이 제거되었으므로, 직접 만든 starter가 있다면 등록 파일을 확인해야 한다.

이 내용은 직접 만든 공통 starter나 사내 라이브러리가 있을 때 특히 중요하다.  
예를 들어 `my-common-starter` 같은 모듈 안에 자동 설정 클래스를 넣어두고, 애플리케이션에서는 의존성만 추가해서 Bean이 자동 등록되도록 만든 경우가 있다.

Spring Boot 2.6까지는 보통 아래처럼 `spring.factories`에 자동 설정 클래스를 등록했다.

```properties
# src/main/resources/META-INF/spring.factories
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.example.common.autoconfig.MyCommonAutoConfiguration
```

그리고 자동 설정 클래스는 대략 이런 형태이다.

```java
package com.example.common.autoconfig;

import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MyCommonAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean
    MyCommonClient myCommonClient() {
        return new MyCommonClient();
    }
}
```

Spring Boot 2.7부터는 새 등록 파일인 `AutoConfiguration.imports`를 같이 사용할 수 있다.

```text
# src/main/resources/META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
com.example.common.autoconfig.MyCommonAutoConfiguration
```

Spring Boot 3.0 이상만 대상으로 한다면 `spring.factories`의 `EnableAutoConfiguration` 등록에 의존하지 말고, 위 `AutoConfiguration.imports` 파일을 반드시 추가해야 한다.  
이 파일이 없으면 starter 의존성은 정상적으로 들어와도 자동 설정 클래스가 로딩되지 않아, 기대했던 Bean이 등록되지 않을 수 있다.

Spring Boot 3.x 기준으로는 자동 설정 클래스에 `@Configuration` 대신 `@AutoConfiguration`을 사용하는 방식도 자연스럽다.

```java
package com.example.common.autoconfig;

import org.springframework.boot.autoconfigure.AutoConfiguration;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.context.annotation.Bean;

@AutoConfiguration
public class MyCommonAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean
    MyCommonClient myCommonClient() {
        return new MyCommonClient();
    }
}
```

정리하면, Spring Boot 2.7을 중간 단계로 거쳐 간다면 `spring.factories`와 `AutoConfiguration.imports`를 같이 둬서 호환성을 확보할 수 있다.  
최종적으로 Spring Boot 3.x로 올린 뒤에는 `AutoConfiguration.imports` 기준으로 자동 설정이 등록되는지 확인해야 한다.

</details>

---
<a id="migration-record"></a>
## 5. 실제 마이그레이션 진행 순서

앞에서는 JDK 8에서 JDK 21로 올릴 때 확인해야 할 변경점을 정리했다.  
이번에는 개인 프로젝트에 실제로 적용하면서 어떤 순서로 진행했는지 기록했다.
모든 단계 작업은 마지막에 테스트/빌드 과정을 필수로 거치도록 했다.

<details class="topic-details" markdown="1">
<summary>5-1) 1단계: Spring Boot 2.7.18/MyBatis 2.3.2 업그레이드</summary>

첫 단계에서는 JDK는 8로 유지하고 Spring Boot와 MyBatis 버전만 먼저 올렸다.  
처음부터 JDK 21과 Spring Boot 3.x를 한 번에 적용하면 오류가 발생했을 때  
JDK 변경 때문인지, Spring Boot 변경 때문인지, 라이브러리 호환성 문제인지 구분하기 어렵기 때문이다.

그래서 우선 Spring Boot 2.x의 마지막 안정 버전인 2.7.18로 올리고, MyBatis Spring Boot Starter도 2.3.2로 올렸다.  
이 단계의 목적은 JDK 변경 없이 프레임워크와 라이브러리 의존성 변경만 먼저 검증하는 것이었다.

이 단계에서는 다음 항목을 확인했다.

- 애플리케이션이 정상적으로 기동되는지
- 기존 Controller URL이 그대로 호출되는지
- MyBatis mapper 쿼리가 정상적으로 실행되는지
- 로그인과 권한 처리가 기존과 동일하게 동작하는지
- `application.properties` 설정 바인딩 오류가 발생하지 않는지
- deprecated 설정이나 의존성 경고가 있는지

이렇게 먼저 Spring Boot 2.7.x 단계까지 올려두면, 이후 JDK 21로 변경했을 때 새로 발생하는 문제를 JDK 변경 영향으로 좁혀서 볼 수 있다.  
즉, 이 단계는 최종 업그레이드라기보다 문제 원인을 분리하기 위한 중간 기준점을 만드는 작업이었다.  
Spring Security 설정 전환도 이 단계에서 함께 진행했지만, 변경 내용이 작지 않아 아래에서 따로 정리했다.

</details>

<details class="topic-details" markdown="1">
<summary>5-2) 1단계에서 함께 진행한 Spring Security 설정 전환</summary>

Spring Boot 2.7.18로 올리는 과정에서 Spring Security 설정도 함께 전환했다.  
기존에는 `WebSecurityConfigurerAdapter`를 상속하고 `configure(HttpSecurity http)` 메서드 안에서 권한 규칙을 정의하는 방식이었지만,  
이후 Spring Security 6.x와 Spring Boot 3.x에서는 이 방식이 제거된다.

그래서 JDK를 바꾸기 전에 Security 설정을 다음 방향으로 먼저 정리했다.

- `WebSecurityConfigurerAdapter` 상속 제거
- `SecurityFilterChain` Bean 등록 방식으로 변경
- 기존 `antMatchers` 기반 URL 권한 규칙은 유지
- 인증 설정은 `AuthenticationManagerBuilder` 오버라이드 대신 Bean 등록 방식으로 정리
- 로그인, 로그아웃, 정적 리소스, 관리자 URL 권한 규칙을 기존 동작과 동일하게 재확인
- CSRF, CORS, 세션 정책처럼 애플리케이션 동작에 영향을 줄 수 있는 설정을 함께 점검

실제 프로젝트에서는 Security 설정을 완전히 새로 짜기보다는, 기존 권한 규칙은 최대한 그대로 두고 설정 방식만 먼저 바꿨다.  
한 번에 권한 규칙까지 같이 손대면 로그인 문제인지, URL 매칭 문제인지, 버전 업그레이드 문제인지 구분하기 어려울 것 같았기 때문이다.

그래서 이 단계에서는 `WebSecurityConfigurerAdapter` 상속을 제거하고, 기존 설정을 `SecurityFilterChain` Bean 안으로 옮기는 데 집중했다.

[AS-IS]
```java
@EnableWebSecurity
@Configuration
@RequiredArgsConstructor
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    private final UserService userService;
    private final LoginSuccessHandler loginSuccessHandler;
    private final LoginFailureHandler loginFailureHandler;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .cors().and()
            .csrf().disable()
            .authorizeRequests()
                .antMatchers("/login", "/api/user/**", "/admin/signUp*", "/admin/signUp/checkId", "/css/**", "/js/**").permitAll()
                .antMatchers("/*").hasRole("ADMIN")
                .anyRequest().authenticated()
            .and()
            .formLogin()
                .loginPage("/login")
                .loginProcessingUrl("/login")
                .successHandler(loginSuccessHandler)
                .failureHandler(loginFailureHandler)
                .permitAll()
            .and()
            .logout()
                .logoutUrl("/logout")
                .invalidateHttpSession(true)
                .clearAuthentication(true)
                .deleteCookies("JSESSIONID")
                .logoutSuccessHandler((request, response, authentication) -> {
                    String accept = request.getHeader("Accept");
                    if (accept != null && accept.contains("text/html")) {
                        response.sendRedirect("/login?logout");
                        return;
                    }
                    response.setStatus(HttpServletResponse.SC_NO_CONTENT);
                })
                .permitAll();
    }

    @Override
    public void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userService).passwordEncoder(new BCryptPasswordEncoder());
    }
}
```

[TO-BE]
```java
@EnableWebSecurity
@Configuration
@RequiredArgsConstructor
public class SecurityConfig {

    private final UserService userService;
    private final LoginSuccessHandler loginSuccessHandler;
    private final LoginFailureHandler loginFailureHandler;

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .cors().and()
            .csrf().disable()
            .authorizeRequests()
                .antMatchers("/login", "/api/user/**", "/admin/signUp*", "/admin/signUp/checkId", "/css/**", "/js/**").permitAll()
                .antMatchers("/*").hasRole("ADMIN")
                .anyRequest().authenticated()
            .and()
            .formLogin()
                .loginPage("/login")
                .loginProcessingUrl("/login")
                .successHandler(loginSuccessHandler)
                .failureHandler(loginFailureHandler)
                .permitAll()
            .and()
            .logout()
                .logoutUrl("/logout")
                .invalidateHttpSession(true)
                .clearAuthentication(true)
                .deleteCookies("JSESSIONID")
                .logoutSuccessHandler((request, response, authentication) -> {
                    String accept = request.getHeader("Accept");
                    if (accept != null && accept.contains("text/html")) {
                        response.sendRedirect("/login?logout");
                        return;
                    }
                    response.setStatus(HttpServletResponse.SC_NO_CONTENT);
                })
                .permitAll();

        http.userDetailsService(userService);

        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration authenticationConfiguration) throws Exception {
        return authenticationConfiguration.getAuthenticationManager();
    }
}
```

위 코드는 실제 변경 내용을 보여주기 위해 일부 import만 제외하고 가져온 예시다.  
URL 권한 규칙 자체는 크게 바꾸지 않고, 설정 구조를 먼저 옮겼다고 보면 된다.  
변경작업 소스 :: [GitHub 링크](https://github.com/youngeunahn/springbootYeahn/commit/e2099bfef1de6a0818952e5d4e4e6515a6283cd7)

이 작업이 JDK 21 업그레이드 자체에 필요한 것은 아니지만, Spring Boot 3.x로 넘어가려면 어차피 정리해야 하는 부분이었다.  
미리 Security 설정을 옮겨두면 다음 단계에서 문제가 생겼을 때 JDK 문제인지, 인증/인가 설정 문제인지 나눠서 볼 수 있다.

</details>

<details class="topic-details" markdown="1">
<summary>5-3) 2단계: JDK 21 + Spring Boot 3.5.14 / MyBatis 3.0.3 기반 전환</summary>

두 번째 단계에서는 JDK 21과 Spring Boot 3.5.14, MyBatis Spring Boot Starter 3.0.3 전환을 같이 진행했다.
특히 Spring Boot 3.x로 넘어가면서 `javax.*`에서 `jakarta.*`로 바뀌는 패키지 변경을 주로 확인했다.

이 단계에서는 다음 항목을 확인했다.

- JDK 21 환경에서 빌드가 성공하는지
- 애플리케이션이 정상적으로 기동되는지
- 파일 입출력에서 인코딩 문제가 없는지
- 주요 API 호출 결과가 기존과 동일한지
- DB 연결과 MyBatis 쿼리 실행이 정상적으로 동작하는지
- `javax.*` import가 남아 있는지

</details>

<details class="topic-details" markdown="1">
<summary>5-4) Spring Boot 3.x 기준 Security 설정 재정리</summary>

1단계에서는 `WebSecurityConfigurerAdapter`를 제거하고 `SecurityFilterChain` 구조로 먼저 옮겼지만, URL 매칭 방식은 기존 `antMatchers` 기반을 그대로 유지했다.  
JDK 21 + Spring Boot 3.5.14 기반으로 올리면서는 Spring Security 6.x 기준에 맞게 남은 설정도 다시 바꿔야 했다.

이 단계에서 실제로 바꾼 주요 내용은 다음과 같다.

- `cors().and()` 대신 `cors(Customizer.withDefaults())` 사용
- `csrf().disable()` 대신 `csrf(AbstractHttpConfigurer::disable)` 사용
- `authorizeRequests()` 대신 `authorizeHttpRequests()` 사용
- `antMatchers()` 대신 `requestMatchers()` 사용

핵심 부분만 남기면 아래처럼 정리된다.

```java
@EnableWebSecurity
@Configuration
@RequiredArgsConstructor
public class SecurityConfig {

    private final UserService userService;
    private final LoginSuccessHandler loginSuccessHandler;
    private final LoginFailureHandler loginFailureHandler;

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .cors(Customizer.withDefaults())
            .csrf(AbstractHttpConfigurer::disable)
            .authorizeHttpRequests(authorize -> authorize
                .requestMatchers("/login", "/api/user/**", "/admin/signUp*", "/admin/signUp/checkId", "/css/**", "/js/**").permitAll()
                .requestMatchers("/*").hasRole("ADMIN")
                .anyRequest().authenticated())
            .formLogin(formLogin -> formLogin
                .loginPage("/login")
                .loginProcessingUrl("/login")
                .successHandler(loginSuccessHandler)
                .failureHandler(loginFailureHandler)
                .permitAll())
            .logout(logout -> logout
                .logoutUrl("/logout")
                .invalidateHttpSession(true)
                .clearAuthentication(true)
                .deleteCookies("JSESSIONID")
                .logoutSuccessHandler((request, response, authentication) -> {
                    String accept = request.getHeader("Accept");
                    if (accept != null && accept.contains("text/html")) {
                        response.sendRedirect("/login?logout");
                        return;
                    }
                    response.setStatus(HttpServletResponse.SC_NO_CONTENT);
                })
                .permitAll());

        http.userDetailsService(userService);

        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

1단계에서 구조를 먼저 옮겨둔 덕분에, 이 단계에서는 Spring Security 6.x에서 더 이상 사용할 수 없는 API를 정리하는 데 집중할 수 있었다.  
권한 규칙 자체는 기존과 동일하게 유지하되, Spring Boot 3.x에서 동작하는 방식으로 표현만 바꾼 셈이다.

Spring Boot 3.x 업그레이드는 단순히 버전 숫자를 올리는 작업이라기보다, 프레임워크의 기본 규칙이 바뀐 부분을 하나씩 확인하는 작업에 가까웠다.  
특히 보안 설정처럼 운영 중 동작에 영향을 줄 수 있는 부분은 빌드 성공만으로 판단하지 않고 실제 요청 흐름까지 확인하는 것이 중요했다.

</details>

<details class="topic-details" markdown="1">
<summary>5-5) 단계별 진행 결과 정리</summary>

앞에서 정한 순서대로 실제 작업을 진행해보니, 각 단계의 역할은 아래처럼 정리할 수 있었다.

| 단계 | 변경 범위                                                     | 확인 목적 |
| --- |-----------------------------------------------------------| --- |
| 1단계 | Spring Boot 2.7.18 / MyBatis 2.3.2, Spring Security 설정 전환 | JDK 변경 없이 프레임워크, 라이브러리, 인증/인가 설정 변경 영향 확인 |
| 2단계 | JDK 21 / Spring Boot 3.5.14 / MyBatis 3.0.3               | 빌드, 기동, 인코딩, Jakarta, Security 등 최종 전환 영향 확인 |

1단계에서 Spring Boot 2.7.x 기준으로 한 번 정리해둔 덕분에, 2단계에서는 JDK 21과 Spring Boot 3.x 전환에 더 집중할 수 있었다.  
특히 Security 설정은 1단계에서 구조를 먼저 옮기고, 2단계에서 Spring Security 6.x 기준으로 다시 정리하는 흐름이 되었다.

</details>

---
<a id="conclusion"></a>
## 6. 마치며

이번 작업을 하면서 느낀 점은, JDK 업그레이드는 JDK 버전만 올리는 작업으로 끝나지 않는다는 것이다.  
특히 Spring Boot 2.x에서 3.x로 넘어가는 경우에는 Jakarta 패키지 변경과 Spring Security 설정 변경처럼 코드 수정이 필요한 부분이 같이 따라온다.

개인 프로젝트라서 변경 범위가 크지는 않았지만, 그래도 바로 최종 버전으로 올리지 않고 Spring Boot 2.7.x 단계를 먼저 거친 것이 도움이 됐다.  
Security 설정도 한 번에 끝내기보다는, 먼저 `SecurityFilterChain` 구조로 옮기고 이후 Spring Security 6.x 방식으로 다시 정리하면서 문제 범위를 나눠볼 수 있었다.

결국 버전 업그레이드에서 중요한 것은 의존성 버전을 맞추는 것뿐만 아니라, 실제로 애플리케이션이 기동되고 주요 기능이 기존처럼 동작하는지 확인하는 과정이었다.

---
참고 : https://twofootdog.tistory.com/348

</div>

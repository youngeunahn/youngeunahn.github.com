# GEMINI.md

## 프로젝트 개요
이 프로젝트는 Jekyll을 사용하는 개인 기술 블로그 및 기록용 정적 사이트입니다. 이전의 복잡한 컬렉션 구조를 단순화하여, 모든 포스트를 `_posts` 폴더에서 통합 관리하도록 개선되었습니다.

### 주요 기술 스택
- **Static Site Generator:** Jekyll (v4.2.2)
- **Language:** Ruby
- **Template Engine:** Liquid
- **Markdown Parser:** Kramdown
- **Syntax Highlighter:** Rouge (Monokai 테마)
- **Styling:** CSS (assets/css/main.css, palette.css)
- **Search:** Tipue Search

## 프로젝트 구조
- `_config.yml`: 사이트 전역 설정. 포스트 카테고리에 따른 자동 레이아웃 설정이 포함되어 있습니다.
- `_posts/`: **모든 블로그 포스트의 통합 저장소**입니다. (기존의 `_house`, `_legacy`, `_gitblog`는 폐쇄됨)
- `_layouts/`: 페이지 레이아웃 템플릿.
- `_includes/`: 재사용 가능한 UI 컴포넌트.
- `_data/`: 사이트 데이터 파일 (navigation.yml, toc.yml).
- `assets/`: 정적 자원 (이미지, CSS, JavaScript).
- `pages/`: 카테고리별 목록 페이지 및 고정 페이지.

## 빌드 및 실행 방법

### 로컬 개발 환경 실행
1. 의존성 설치:
   ```bash
   bundle install
   ```
2. 로컬 서버 실행:
   ```bash
   bundle exec jekyll serve
   ```
   *참고: `_config.yml` 수정 시 서버를 재시작해야 반영됩니다.*

## 개발 및 운영 규칙

### 1. 포스트 통합 관리 (중요)
모든 새로운 글은 오직 `_posts/` 폴더 내에 생성합니다. 중복 파일을 다른 폴더에 만들 필요가 없습니다.

### 2. 카테고리 기반 자동 분류
글을 작성할 때 상단 Front Matter의 `categories` 설정에 따라 해당 메뉴 페이지에 자동으로 노출되며, 전용 레이아웃이 적용됩니다.

| 카테고리명 (categories) | 적용 레이아웃 | 노출 페이지 |
| :--- | :--- | :--- |
| `부동산` | `house` | `/house/` |
| `깃블로그운영` | `gitblog` | `/gitblog/` |
| `스프링부트_레거시프로젝트` | `legacy` | `/legacy/` |
| (기타) | `post` | 일반 포스트 목록 |

**작성 예시:**
```yaml
---
layout: post (또는 생략 가능 - 설정에 의해 자동 적용됨)
title: 포스트 제목
categories: 부동산
---
```

### 3. 파일 명명 및 이미지 규칙
- 포스트 파일명: `YYYY-MM-DD-제목.markdown` 형식을 따릅니다.
- 이미지 경로: `assets/img/posts/{포스트파일명}/` 폴더 내에 저장하는 것을 권장합니다.
  - 예: `_posts/2026-04-04-test.markdown` 작성 시 이미지는 `assets/img/posts/2026-04-04-test/image1.png`에 저장.
  - 포스트에서 참조 시: `![설명](/assets/img/posts/2026-04-04-test/image1.png)` 형태.

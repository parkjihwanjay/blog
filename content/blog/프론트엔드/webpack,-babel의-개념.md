---
title: Webpack, Babel의 개념
date: 2020-09-06 12:09:40
category: 프론트엔드
thumbnail: { thumbnailSrc }
draft: false
---

# Webpack

<hr>

## Webpack이란?

static module bundler for modern javascript applications.

webpack은 자바스크립트의 [모듈 번들러](##모듈 번들러)입니다.

- webpackd은 Node.js 8버전 이상에서 돌아갑니다.

### 모듈 번들러란?

웹 애플리케이션에 필요한 모듈들을 하나의 파일로 묶는 도구입니다.

이때 모듈은 웹 애플리케이션을 구성하는 모든 자원(HTML, CSS, JAVASCRIPT, Images등)에 해당합니다. 즉, 웹 애플리케이션을 제작하는데 사용되는 파일을 말합니다.

기본적으로 webpack은 자바스크립트 파일과 JSON 파일을 이해하지만 loader를 이용해 다른 타입의 파일을 처리할 수 있습니다.

#### 모듈 번들링의 원리

Entry point부터 시작해서 dependency graph를 만들고 이 graph를 기반으로 모듈들을 몇개의 bundle로 만듭니다.(보통 하나)

1. [entry point](#### entry point)의 의존성을 분석합니다.
2. 그 다음 파일의 의존성을 파악합니다.

모든 모듈의 의존성을 파악할 때까지 2단계를 계속 반복합니다.

구체적인 동작 원리는 아래와 같습니다

https://github.com/hg-pyun/minipack-kr/blob/master/src/minipack.js

#### entry point

- 웹 애플리케이션의 메인 파일
- 애플리케이션을 실행하는 진입점
- webpackd은 entry point를 시작으로 dependency graph를 그려나갑니다.

#### Loader

Loader는 자바스크립트, JSON 파일 외의 파일들을 webpack이 처리하고 module로 변환해 dependency graph에 추가하게 해줍니다.

#### Plugin

번들링 최적화, 파일 관리, 환경변수 주입등의 역할을 합니다.

# Babel

<hr>

## Babel이란?

Babel is a JavaScript compiler

Babel은 ES6이상의 문법 코드들을 예전 브라우저에서도 돌아갈 수 있도록 바꿔주는 도구입니다.

바벨은 plugin의 모음으로 동작합니다.

바벨이 하는 일

- 구문을 바꿔줍니다.
- Polyfill을 넣어줍니다.
  - polyfill은 특정 브라우저에서 동작하지 않는 코드의 기능을 실행시켜주는 다른 코드입니다.
- 코드를 바꿔주기

바벨의 동작원리

1. Lexical analysis
   - tokenizer를 이용해 코드를 의미있는 조각(토큰)으로 나눕니다.
2. Syntax analysis

   - 흔히 파싱이라고 한다.
   - 1단계에서 추출한 토큰을 이용해 소스 코드의 문법 구조를 서술하는 abstract syntax tree를 생성한다.
   - abstract syntax tree는 토큰을 node로 가지며 토큰간의 관계를 나타낸다.
   - 이때 토큰 간의 관계가 올바른지 검사한다.

3. Transformation
   - 앞서 만들어진 AST(Abstract Syntax Tree)를 목표 언어에 따라 변환한다.
   - AST의 각 node를 거치며 동작한다.
   - AST의 node를 변경하거나 새로운 AST를 만들 수 있다.
4. Code Generation
   - 앞선 단계에서 변경된 또는 새로 생성된 AST를 문자열로 된 소스코드로 만든다.

#### Plugin

Plugin은 babel을 돌아가게 하는 작은 프로그램입니다.

#### Preset

Preset은 plugins의 모음입니다.

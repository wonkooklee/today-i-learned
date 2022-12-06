[TEST] Learning Jest and RTL(React-Test-Library)

*Dec 07 2022*

*Author: [Wonkook Lee](https://github.com/wonkooklee/today-i-learned)*

<br />

```jsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders learn react link", () => {
  render(<App />);

  const linkElement = screen.getByText(/learn react/i);

  expect(linkElement).toBeInTheDocument();
});

test("ewrwerwer", () => {
  render(<App />);

  const element = screen.getByText(/learn react/i);
  expect(element).toHaveClass("App-link");
});
```


```jsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders learn react link", () => {
  // render method는 가상 돔 생성
  render(<App />);

  // render, screen 등은 RTL (React-testing-library)에서 가져옴
  // screen.prototype.getByText 텍스트 요소 기반으로 엘리먼트를 찾아옴

  const linkElement = screen.getByText(/learn react/i);

  // Assertions - expect 메서드로 시작
  // Matcher = type of assertion

  // Assertion(subject).Matcher(matcherArguments)
  expect(linkElement).toBeInTheDocument();

  // More assertion examples
});

test("ewrwerwer", () => {
  render(<App />);

  const element = screen.getByText(/learn react/i);
  expect(element).toHaveClass("App-link");
});

```


### Assertion의 예
```jsx
expect(element.textContent).toBe('hello');
expect(elementsArray).toHaveLength(7);
```


### jest-dom
- CRA 설치하면 자동 설치됨
- src/setupTest.js가 테스트 전에 미리 jest-dom을 가져오게 함
- 따라서 전역에 등록된 assertion, matcher 등을 사용할 수 있음
- DOM-based matchers임 (가상 DOM)
 - toBeVisible() or toBeChecked()



### RTL은
- 컴포넌트를 가상 DOM으로 렌더링한다.
- 가상 DOM을 검색하는데 도움을 준다.
- 가상 DOM과 상호작용하여 요소를 클릭하거나 텍스트를 입힐 수 있다(Manipulation)

### 테스트 러너가 필요하다. (Jest)
- 이는 테스트를 찾고 실행시키며 단언(Assertion)하는 역할
- Jest 뿐만 아니라 모카, 자스민 등이 있다.
- Jest는 테스팅 프레임워크라고 한다.

> watch 모드가 기본적으로 적용되어 있다. (react-scripts test)

### Watch모드란
- 마지막 커밋 이후 변경된 파일을 적용한다.
- 변경 사항을 확인하고 onSave 시점에 다시 테스트를 실행시킨다.
- 아래와 같은 CLI를 프롬프트한다.

### Watch Usage
```bash
  › Press f to run only failed tests.
  › Press o to only run tests related to changed files.
  › Press q to quit watch mode.
  › Press p to filter by a filename regex pattern.
  › Press t to filter by a test name regex pattern.
  › Press Enter to trigger a test run.
```


### How does Jest Work?

- 전역 메소드인 `test`는 두 개의 인자를 가진다.
  - 테스트를 설명하기 위한 디스크립션 문자열(string description)
  - 테스트 함수(test function)
- 테스트 함수를 실행할때 에러가 발생되면 실패로 간주한다.
  - Assertion은 결과가 예상과 다를때 에러를 발생시킨다
- 에러가 없다면 테스트에 통과한 것으로 간주한다.
- 빈 테스트(Empty test)도 통과해야 한다.


### TDD (Test-Driven Development)

- TDD란 코드 작성 전에 테스트를 작성하고
  - 테스트에 통과하도록 코드를 작성하는 것이다.
- 흔히 레드-그린 테스트라고 한다.
  - 코드 작성 전에 테스트에 실패하는 레드 테스트를 먼저 실행하고
  - 그린 테스트로 확인한다.

### 순서

1. 아무것도 없는 Shell function을 만든다.
2. 테스트를 만든다.
3. 테스트에 떨어진다.
4. 코드를 작성한다.
5. 코드를 통과한다.

### 왜 TDD를 사용할까?

- 테스트를 작성하는 것이 프로세스의 일부로 통합되어야 하기 떄문
- 앱을 실행해서 직접 수동 테스트하는 것보다 효율적이다.
- 변경 사항이 생길 떄마다 모든 테스트를 다시 실행해서 자동 회귀 테스트를 할 수 있다. (For Free Test)

### RTL의 철학

> RTL은 완고(Opinionated)하다고 할 수 있다.

- RTL 테스트를 위한 가상 DOM을 제공한다.
- DOM과 상호 작용 하기 위한 유틸리티를 제공한다.
- 브라우저 없이도 테스트가 가능하다.

### 테스트의 종류

- 유닛 테스트(Unit tests)
  - 함수나 별개의 컴포넌트 단위로 테스트한다.
  - 다른 코드의 유닛과 상호 작용을 테스트하지 않는다.
- 통합 테스트(Integration tests)
  - 여러 유닛이 함께 작동하는 상호작용 단위의 테스트
  - 유닛 간 상호작용 테스트
  - 컴포넌트, 마이크로 서비스 간의 상호작용 등
- 기능 테스트(Functional Tests)
  - 소프트웨어의 특정 기능 단위의 테스트
  - 코드가 아닌 동작을 테스트한다.
- 인수(Acceptance) 테스트 / End-to-End(E2E) 테스트
  - 실제 브라우저가 필요하고 앱과 연결된 서버도 필요하다.
  - Cypress, Selenium과 같은 툴을 사용한다.
  - RTL을 위해 설계된 테스트는 아니다.


### 기능 테스트 vs. 유닛 테스트

#### 유닛 테스트

- 유닛 테스트는 테스트를 최대한 격리(isolate)시킨다.
- 그래서 함수나 컴포넌트를 테스트할 때 **의존성**을 표시한다.
- 의존성이 있다면 실제 버전 대신 테스트 버전을 사용한다.
- 격리를 통해 실패시 특정 유닛이 문제임을 알 수 있다.

- 실패를 쉽고 정확하게 파악할 수 있다.
- 하지만 사용자 중심이 아닌 개발자 중심의 테스트이다.
- 리팩토링으로 실패할 가능성도 있다.
  - 리팩토링은 동작을 변경하지 않고 소프트웨어 작성 방식을 개선하는 것
- 동작이 변경되지 않아도 테스트에 실패할 수 있음

### 기능 테스트
- 테스트하는 특정 동작이나 유저 플로우와 연관된 모든 단위를 포함한다.
- 사용자의 상호 작용 방식과 밀접하다. (사용자 중심)
- 테스트가 견고하다는(Robust) 의미이기도 하다.
- 실패한 테스트의 디버깅이 어렵다.

### 전반적으로
RTL은 기능 테스트에 가깝다고 볼 수 있다.


## TDD(테스트 주도 개발) vs. BDD(행동 주도 개발)

- Test/Behavior Driven Development
- BDD의 명확한 의미
  - 다양한 역할 간의 협업이 필요하다.
  - 개발자 QA 등
  - 서로 다른 그룹이 상호 작용하는 방식에 관한 프로세스도 정의되어야 함
- 우리가 하는 것은 TDD


## [테스팅 라이브러리와 접근성(A11y)](https://testing-library.com/docs/guide-which-query)

- 접근성으로 요소를 찾거나 요소를 찾을 수 있는 스크린 리더와 같은 보조기술을 사용할 것을 권장한다. (guide-which-query)
- 접근성에 따라 역할의 우선 순위가 다르다. 화면을 시각적으로 인지할 수 있고 마우스 등의 입력장치를 사용할 수 있는 사람이 첫번째 우선 순위이다. (누구나 액세스 가능한 쿼리) `getByRole`
- 폼에 관해서는 `getByLabelText`를 사용함.
- 대화형이 아닌 디스플레이는 `getByText`를 사용하고
...

- 시맨틱 쿼리도 있다. (alt, title)
- 최후 수단으로 테스트 아이디를 사용한다.

```jsx
const linkElement = screen.getByRole("link", { name: /learn react/i });
```
위와 같이 실제 요소의 역할로써 요소를 찾을 수 있게 한다.

요소의 정의는 [W3C Roles documentation](https://www.w3.org/TR/wai-aria/#role_definitions)에서 찾아볼 수 있다.

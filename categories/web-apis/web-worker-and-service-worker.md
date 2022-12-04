# [Web APIs] Web Worker and Service Worker

*Dec 4 2022*

*Author: [Wonkook Lee](https://github.com/wonkooklee/today-i-learned)*

<br />

![image from Unsplash](thumbnail.jpg)

<br />


# Web Worker and Service Worker

현재 개발하고있는 프로덕트의 웹뷰(e.g. Webkit Browser for iOS)가 다른 독립된 웹뷰와도 브라우징 컨텍스트를 공유하도록 `Broadcast Channel API`를 사용해보고 있었다.

그런데 어떤 원리로 JS 영역 바깥의 컨텍스트끼리 소통이 가능한지 궁금했다. 살펴보니 `Broadcast Channel API`는 `Web Workers`라는 다소 생소한 피쳐에 의존하고 있었다.

내친김에 `Web Worker`와 `Service Worker`는 무엇인지 공부해봤다.

---

<br />

## 1) Web Worker API

> Web Workers makes it possible to run a script operation in a background thread separate from the main execution thread of a web application. The advantage of this is that laborious processing can be performed in a separate thread, allowing the main (usually the UI) thread to run without being blocked/slowed down.
> 
> \- mdn web docs

<br />

### > 브라우저 환경의 자바스크립트 프로세싱을 돕는 Web Worker

간단하게 말하자면, Web Worker는 백그라운드 스레드(Thread)에서 스크립트를 실행시켜주는 웹 컨텐츠다.

주지하다시피 자바스크립트는 싱글 스레드로 작동되며 call stack의 태스크를 one by one으로 처리하기 때문에 (특히 브라우저 런타임에서) 부하가 많은 작업에 취약하다.

Web Worker는 웹앱이 실행되는 메인 스레드에서 분리되어 백그라운드에서 스크립트가 동작할 수 있도록 도와 메인 스레드의 UI 페인팅 등 주요 연산이 block되지 않도록 한다.

따라서 웹사이트에서 적절히 Web Worker를 사용하면 스크립트 실행을 멀티스레드로 처리하는 효과를 가져온다.

(사용 예: 네트워크 통신, 파일 시스템 I/O 등 리소스가 많이 드는 작업)

<br />

### > Web Worker의 실행 환경

Web Worker도 자바스크립트 실행 환경(Execution Environment)을 제공하는 런타임 스레드이기 때문에 직접적인 DOM 조작(Manipulation), window 객체 접근 등 일부 기능을 제외하고 스크립트 코드를 실행시킬 수 있다.

각 Worker와 메인 스레드는 `postMessage()`와 `onmessage` 핸들러를 사용하여 서로 데이터를 전달하고, 특정 동작을 트리깅할 수 있다. 이런 소통 방식을 `message massing`이라고 한다.

<br />

### > Worker Types

역할에 따라 Worker는 서로 다른 타입으로 분류된다.

- `Dedicated workers`는 전용 워커라고도 불리며 Web Worker의 가장 일반적인 타입이다. worker가 작성된 스크립트(*.js, etc...)를 통해 사용한다.<br />
- [`Share workers`](https://developer.mozilla.org/en-US/docs/Web/API/SharedWorker)는 서로 다른 윈도우, 탭, 아이프레임 등 다른 스크립트 환경이더라도 같은 도메인(same-origin)을 origin일 경우 활성화된 포트를 통해 서로 통신할 수 있다. Dedicated worker보다는 조금 더 복잡한 구조.
- `Service Workers`는 웹앱, 프라우저, 네트워크 사이의 프록시 서버(proxy server)로써 작동하며 다음 세션에서 자세히 설명한다.

<br />

### > Worker 타입별 전역 컨텍스트 객체 및 함수

- [`DedicatedWorkerGlobalScope`](https://developer.mozilla.org/en-US/docs/Web/API/DedicatedWorkerGlobalScope) for dedicated workers
- [`SharedWorkerGlobalScope`](https://developer.mozilla.org/en-US/docs/Web/API/SharedWorkerGlobalScope) for shared workers
- [`ServiceWorkerGlobalScope`](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerGlobalScope) for service workers


<br /><br />

---

<br />

## 2) Service Worker API

> Service workers essentially act as proxy servers that sit between web applications, the browser, and the network (when available). They are intended, among other things, to enable the creation of effective offline experiences, intercept network requests and take appropriate action based on whether the network is available, and update assets residing on the server. They will also allow access to push notifications and background sync APIs.
> 
> \- mdn web docs

<br />

### > 웹앱의 캐시를 클라이언트에서 관리하기 위한 Service Worker

간단히 말해, `Service Worker`는 웹앱의 캐시를 관리하기 위해 웹 브라우저에서 실행되는 스크립트를 말한다.

위에서 언급했듯 프록시 서버 역할도 한다. 앱에서 보내는 HTTP 요청을 인터셉트(Intercept)해서 요청에 대한 응답을 반환하거나, 응답을 로컬에 캐싱해서 재사용 할수도 있다.

- 웹 푸시 알림 서비스
- 백그라운드 동기화 기능
- 네트워크 인터셉트 및 캐싱

프록시 기능은 `fetch`와 같은 Web API 뿐만 아니라 HTML 파일이 참조하는 리소스(stylesheet, script, etc...)에도 적용할 수 있다. 서비스 워커를 호라용하면 모든 캐시를 클라이언트에서 코드로 조작할 수 있다.

오프라인 캐싱을 통해 웹앱이 마치 앱처럼 동작하도록 오프라인 사용자 경험을 개선하는 것을 목표로 한다.

<br />

### > Service Worker의 특징

- Service Worker의 생명 주기(Life Cycles)는 웹페이지와 별개로 작용한다.
- 도메인당 한 개의 Service Worker를 등록하고 활성화 시킬 수 있다.
- 별개의 스레드에서 작동하기 때문에 스레드를 Web Worker와 같이 종료하는 등 직접적인 제어는 안된다.
- 웹페이지가 닫히더라도 비활성화되지 않는다. 따라서 브라우저 창이 닫히더라도 푸시 알림을 받을 수 있다.
- DOM이나 window 전역 객체를 참조 또는 접근할 수 없다.
- 오프라인에서도 서비스를 사용할 수 있어서, 작업 도중 다시 온라인 상태로 연결 되었을때 해당 작업을 완료할 수 있다. (e.g. 오프라인 상태에서 채팅 앱에서 메시지를 보냈을때 실패하지 않고, 인터넷에 연결되었을때 완료되는 등)
- 오프라인 엑세스 제공을 위한 신뢰성을 높이는 것이 목적이기 때문에, Service Worker는 웹앱이 앱과 유사하게 동작시키게 하기 위해 점진적(Progressive)으로 웹사이트를 향상시키는 역할을 한다. (PWA 개념)




<br /><br /><br /><br />

---
## References
- https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API
- https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API
- https://angular.io/guide/service-worker-intro
- https://so-so.dev/web/service-worker/
- https://blog.woolta.com/categories/3/posts/154
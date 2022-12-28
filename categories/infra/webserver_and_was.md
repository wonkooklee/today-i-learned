# Web Server and WAS(Web Application Server) in a nutshell

*Dec 29 2022*

*Author: [Wonkook Lee](https://github.com/wonkooklee/today-i-learned)*

<br />

---

[참조: 얄팍한 코딩사전](https://www.youtube.com/watch?v=Zimhvf2B7Es)

## 웹서버란?

- 어떤 컴퓨터로 하여금 서버 역할을 해주는 소프트웨어도 통칭 서버라고 부른다.
- 웹사이트를 제공하는데 특화된 서버를 웹서버라고 하는데, 아파치, NGINX, IIS 등이 있다.
- 결국 http 요청을 처리하는 것이 웹서버다.
- 웹서버의 기본적인 역할은 브라우저가 읽을 수 있는 파일들(HTML, CSS, JS, Static Files)을 외부에서 특정 포트로 요청했을때 제공해주는 것이다.
- 정적 웹을 위한 정적 에셋을 제공하는것 뿐만 아니라 동적 웹도 웹서버 프로그램으로 제공할 수 있다. (예: 아파치, PHP, MySQL을 연동시킨 동적 PHP 웹사이트 - APM)

## 톰캣이란?

- 아파치 소프트웨어 재단에서 제공하는 WAS(Web Application Server).
- 자바 서블릿을 실행시키고 JSP코드가 포함되어 있는 웹 페이지를 만들어준다.
- 웹서버와 내장 서버 사이에서 동적인 데이터를 처리해주는 서버.

## 정리

### 개념 정리

- 웹서버와 WAS는 동의어가 아니다.
- 웹서버는 정적인 데이터를 처리한다. 정적 파일이나 리소스를 제공하는 서버는 웹서버를 주로 이용한다.
- WAS는 동적인 데이터를 처리한다. DB와 연결되어 데이터를 주고받거나 자바 등을 통해 데이터 조작이 필요할때 사용한다.
- 자바 외 언어 진영에선 WAS의 정의와 역할이 각각 다르다 (Node.js는 어플리케이션이 WAS 역할까지 할 수 있음)

### 웹서버를 사용하는 이유

> **caching + reverse proxy + load balancing**
> 
- 웹서버가 WAS 역할을 할 수도, 반대로 WAS가 웹서버 역할을 할 수도 있다.
- 하지만 웹서버가 앞단에 배치되고 역할이 굳이 나눠진 이유는 웹서버가 다양한 기능을 제공하기 때문이다.
- 그중 Reverse Proxy가 있다. (클라이언트가 서버에 요청할때 Proxy 서버를 이용할땐 이를 Forward Proxy라고 한다.)
- 서버의 포트와 리소스를 드러내지 않도록 숨겨주는 역할을 한다.
- 어떤 웹 프레임워크든 앞단에 웹서버를 두는게 **보안 측면에서의 이유**가 크다.
- 로드밸런서 역할도 겸하기 때문에 하나의 서버에 트래픽이 몰리는 것을 방지한다. (OSL 7계층 LB)
- 리버시 프록시 역할도 겸하기 때문에 한번 요청된 데이터를 캐싱하여 다음 요청때 캐싱된 데이터를 보내주어 응답 속도와 본 서버의 부하를 줄일 수 있다.
- WAS가 정상 작동하는지 주기적으로 health check를 하는 등 관리의 역할도 맡고 있음.

### 아파치와 NGINX의 차이

- 아파치는 다중 프로세스(MPM), NGINX는 이벤트(Event Driven)로 일을 처리한다.
- 성능과 가벼움이 중요 = NGINX
- 다양한 기능과 검증된 안정성 = 아파치


## (읽어보기 좋은 질문) AWS ALB를 사용하면 이제 NGINX와 같은 웹서버는 필요 없나요?
## **[Does Nginx becomes redundant if we have AWS Application Load balancer for a Node application?](https://stackoverflow.com/questions/58586141/does-nginx-becomes-redundant-if-we-have-aws-application-load-balancer-for-a-node)**

> It depends on how you are using the NGINX for load balancing. Application Load balancer surely brings a lot of features that can make NGINX redundant in your architecture but it is not exactly as advanced as NGINX. For example ALB only use round robin load balancing, while you can configure nginx for round-robin, least connnection, etc. ALB does not have any caching capabilities while nginx provides static content caching. ALB only uses path based routing while nginx can route on request headers, cookies, or arguments, as well as the request URL.
> 
> 
> For further reading and source : [https://www.nginx.com/blog/aws-alb-vs-nginx-plus/](https://www.nginx.com/blog/aws-alb-vs-nginx-plus/)
> 
> **Note** : One other important fact of using nginx is cloud agnostic. So if you plan to switch cloud provider, you can take the nginx settings with you.
>


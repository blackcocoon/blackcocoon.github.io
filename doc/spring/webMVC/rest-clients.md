---
sort: 4
---

# Rest Clients

이 절에서는 REST 엔드 포인트에 대한 클라이언트 측 액세스 옵션에 대해 설명합니다.

## `RestTemplate`
https://docs.spring.io/spring-framework/docs/current/reference/html/integration.html#rest-client-access

`RestTemplate`은 HTTP 요청을 수행하는 동기 클라이언트입니다. 원래 Spring REST 클라이언트이며 기본 HTTP 클라이언트 라이브러리를 통해 간단한 템플릿 메서드 API를 노출합니다.

```note
As of 5.0 the `RestTemplate` is in maintenance mode, with only minor requests for changes and bugs to be accepted going forward. Please, consider using the [WebClient](https://docs.spring.io/spring-framework/docs/current/reference/html/web-reactive.html#webflux-client) which offers a more modern API and supports sync, async, and streaming scenarios. 
```

See [REST Endpoints](https://docs.spring.io/spring-framework/docs/current/reference/html/integration.html#rest-client-access) for details.

## `WebClient`
https://docs.spring.io/spring-framework/docs/current/reference/html/web-reactive.html#webflux-client

`WebClient`는 HTTP 요청을 수행하는 non-blocking, reactive 클라이언트입니다. 5.0에서 도입되었으며 `RestTemplate`에 대한 현대적인 대안을 제공하며, 동기식 및 비동기식뿐 아니라 스트리밍 시나리오를 효율적으로 지원합니다.

In contrast to `RestTemplate`, `WebClient` supports the following:

- Non-blocking I/O.
- Reactive Streams back pressure.
- High concurrency with fewer hardware resources.
- Functional-style, fluent API that takes advantage of Java 8 lambdas.
- Synchronous and asynchronous interactions.
- Streaming up to or streaming down from a server.

See [WebClient](https://docs.spring.io/spring-framework/docs/current/reference/html/web-reactive.html#webflux-client) for more details.

--- 

참고 

### RestTemplate

connection pool 적용
참조문서 : https://stackoverflow.com/questions/31869193/using-spring-rest-template-either-creating-too-many-connections-or-slow/

언젠가 만날 것 같아서 기록.. RestTemplate 은 기본적으로 connection pool 을 사용하지 않는다. 따라서 연결할 때 마다, 로컬 포트를 열고 tcp connection 을 맺는다. 이때 문제는 close() 이후에 사용된 소켓은 TIME_WAIT 상태가 되는데, 요청량이 많다면 이런 소켓들을 재사용하지 못하고 소켓이 오링나서 응답이 지연될 것이다.

이런 경우 connection pool 을 사용해서 해결할 수 있는데, DBCP마냥 소켓의 갯수를 정해서 재사용하는 것이다. RestTemplate 에서 connection pool 을 적용하려면, 위와 같이 HttpClient 를 만들고 setHttpClient() 를 해야한다.

setMaxConnPerRoute : IP,포트 1쌍에 대해 수행 할 연결 수를 제한한다.
setMaxConnTotal : 최대 오픈되는 커넥션 수를 제한한다.

출처:  [빨간색코딩]
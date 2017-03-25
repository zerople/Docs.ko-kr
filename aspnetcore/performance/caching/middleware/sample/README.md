# <a name="aspnet-core-response-caching-sample"></a>ASP.NET Core 응답 캐싱 샘플

이 샘플 ASP.NET 핵심 응답 캐싱을 미들웨어를 많이 사용을 하는 방법을 보여 줍니다. 응용 프로그램은 보냅니다는 `Hello World!` 메시지와 함께 현재 시간을 `Cache-Control` 캐싱 동작을 구성 하는 헤더입니다. 응용 프로그램은 또한 보냅니다는 `Vary` 경우에만 응답을 제공 하는 캐시를 구성 하는 헤더는 `Accept-Encoding` 원래 요청에서의 후속 요청 헤더와 일치 합니다.

샘플을 실행 하는 경우 응답 가능한 경우 캐시에서 제공 되 고 최대 10 초 동안 저장 됩니다.

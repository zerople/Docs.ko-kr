# <a name="aspnet-core-response-caching-sample-aspnet-core-1x"></a>ASP.NET Core 응답 캐싱 예제 (ASP.NET 1.x를 코어)

이 샘플에는 ASP.NET Core의 사용 방법을 보여 줍니다. [응답 캐싱 미들웨어](xref:performance/caching/middleware) ASP.NET 코어 1.x 합니다. ASP.NET Core 2.x 샘플을 보려면 [ASP.NET Core 응답 캐싱 예제 (ASP.NET Core 2.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/middleware/samples/2.x)합니다.

응용 프로그램은 보냅니다는 `Hello World!` 메시지와 함께 현재 시간을 `Cache-Control` 캐싱 동작을 구성 하는 헤더입니다. 응용 프로그램은 또한 보냅니다는 `Vary` 를 경우에만 응답을 처리 하는 캐시를 구성 하는 헤더는 `Accept-Encoding` 이후의 요청 헤더에서 원래 요청 하는 일치 항목입니다.

이 샘플을 실행 하는 경우 응답은 캐시에서 제공 가능 하 고 저장 하는 경우 최대 10 초 동안 합니다.

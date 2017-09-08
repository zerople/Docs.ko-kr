# <a name="response-compression-sample-application-aspnet-core-1x"></a>응답 압축 샘플 응용 프로그램 (ASP.NET Core 1.x)

이 샘플에서는 ASP.NET Core 1.x 응답 압축 미들웨어에 대 한 HTTP 응답을 압축 합니다. 샘플 Gzip 및 응답 텍스트 및 이미지에 대 한 사용자 지정 압축 공급자를 보여 줍니다. 및 압축에 대 한 MIME 형식을 추가 하는 방법을 보여 줍니다. ASP.NET Core 2.x 샘플을 보려면 [응답 압축 샘플 응용 프로그램 (ASP.NET Core 2.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples/2.x)합니다.

## <a name="examples-in-this-sample"></a>이 샘플의 예제
* `GzipCompressionProvider`
  * `text/plain`
    * **/**-927 바이트를 압축 하는 바이트의 2,044 Lorem Ipsum 텍스트 파일 응답
    * **/testfile1kb.txt** -1,033 바이트로 47 바이트를 압축 하는 텍스트 파일 응답
    * **trickle/** -1 초 간격 단일 문자로 발급 응답 
  * `image/svg+xml`
    * **/banner.svg** -4,459 바이트를 압축 하는 바이트의 9,707 A 그래픽 SVG (Scalable Vector) 이미지 응답
* `CustomCompressionProvider`<br>미들웨어와 사용 하기 위해 사용자 지정 압축 공급자를 구현 하는 방법을 보여 줍니다.

요청에 포함 된 경우는 `Accept-Encoding` 샘플 추가 하는 헤더는 `Vary: Accept-Encoding` 헤더를 응답 합니다. `Vary` 헤더의 대체 값을 기반으로 하는 응답의 여러 복사본을 유지 하는 캐시는 `Accept-Encoding`에 유지할 수 있는 시스템의 압축을 사용할 압축된 (gzip) 및 압축 되지 않은 버전을 모두 캐시에 저장 된, 또는 압축 되지 않은 응답입니다.

## <a name="using-the-sample"></a>샘플 사용
1. 사용 하 여 요청 [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), 또는 [우체부](https://www.getpostman.com/) 없이 응용 프로그램에는 `Accept-Encoding` 헤더 및 응답 크기는 응답 페이로드를 참고 하 고 응답 헤더입니다.
2. 추가 `Accept-Encoding: gzip` 헤더 응답 헤더 및 압축 된 응답 크기를 확인 합니다. 를 삭제 하는 응답 크기를 표시 및 `Content-Encoding: gzip` 응답 헤더는 샘플 응용 프로그램으로 포함 합니다. Lorem Ipsum에 대 한 응답 본문에서 볼 때 또는 **testfile1kb.txt** 응답을 표시의 텍스트를 압축 된 백업과 읽을 수 없습니다.
3. 추가 `Accept-Encoding: mycustomcompression` 헤더 응답 헤더를 기록해 둡니다. `CustomCompressionProvider` 는 응답을 압축 실제로 되지 않는 빈 구현에 대 한 사용자 지정 압축 스트림을 래퍼를 만들 수 있지만 `CreateStream()` 메서드.

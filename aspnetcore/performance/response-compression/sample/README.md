# <a name="response-compression-sample-application"></a>응답 압축 샘플 응용 프로그램

이 샘플에서는 ASP.NET 핵심 응답 압축 미들웨어 HTTP 응답을 압축 하를 사용 합니다. Gzip 및 텍스트와 이미지에 대 한 응답에 대 한 사용자 지정 압축 공급자를 보여 줍니다. 샘플과 압축에 대 한 MIME 형식을 추가 하는 방법을 보여 줍니다.

## <a name="examples-in-this-sample"></a>이 샘플의 예제
* `GzipCompressionProvider`
  * `text/plain`
    * **/**-927 바이트를 압축 하는 바이트의 2,044 Lorem Ipsum 텍스트 파일 응답
    * **/testfile1kb.txt** -47 바이트를 압축 하는 1,033 바이트에서 텍스트 파일 응답
    * **반영/** -1 초 간격으로 단일 문자 발급 응답 
  * `image/svg+xml`
    * **/banner.svg** -4,459 바이트를 압축 하는 바이트의 9,707는 확장 가능한 SVG (벡터 그래픽) 이미지 응답
* `CustomCompressionProvider`<br>미들웨어 사용에 대 한 사용자 지정 압축 공급자를 구현 하는 방법을 보여 줍니다.

요청에 포함 된 경우는 `Accept-Encoding` 샘플에서는 추가 헤더는 `Vary: Accept-Encoding` 헤더를 응답 합니다. `Vary` 헤더의 대체 값을 기반으로 하는 응답의 여러 복사본을 유지 하는 캐시는 `Accept-Encoding`이므로 압축된 (gzip)와 압축 되지 않은 버전 모두에 저장 될 캐시에는 압축 되거나 압축 되지 않은 응답을 사용할 수 있는 시스템입니다.

## <a name="using-the-sample"></a>샘플 사용
1. 사용 하 여 요청 [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), 또는 [Postman](https://www.getpostman.com/) 없이 응용 프로그램에는 `Accept-Encoding` 헤더 응답 페이로드, 응답 크기 및 응답 헤더를 확인 합니다.
2. 추가 `Accept-Encoding: gzip` 헤더 응답 헤더 및 압축 된 응답 크기를 확인 합니다. Drop 응답 크기를 보면 및 `Content-Encoding: gzip` 응답 헤더입니다. Lorem Ipsum에 대 한 응답 본문에서 볼 때 또는 **testfile1kb.txt** 응답을 보면 텍스트를 압축 하 고 읽을 수 없는 합니다.
3. 추가 `Accept-Encoding: mycustomcompression` 헤더 및 응답 헤더를 확인 합니다. `CustomCompressionProvider` 응답 압축 실제로 되지 않는 빈 구현을 이지만 대 한 사용자 지정 압축 스트림 래퍼를 만들 수는 `CreateStream()` 메서드.

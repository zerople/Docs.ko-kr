# <a name="aspnet-core-url-rewriting-sample-aspnet-core-1x"></a>ASP.NET Core URL 샘플을 다시 작성 (ASP.NET 1.x를 코어)

이 샘플에는 ASP.NET Core의 사용 방법을 보여 줍니다. 1.x URL 다시 쓰기 미들웨어입니다. 응용 프로그램 URL 리디렉션 및 URL 재작성 옵션을 보여 줍니다. ASP.NET Core 2.x 샘플을 보려면 [ASP.NET Core URL 다시 쓰기 샘플 (ASP.NET Core 2.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/2.x)합니다.

샘플을 실행할 규칙 중 하나는 요청 URL에 적용 될 때 다시 작성 또는 리디렉션 URL을 보여 주는 응답은 처리 됩니다.

## <a name="examples-in-this-sample"></a>이 샘플의 예제

* `AddRedirect("redirect-rule/(.*)", "$1")`
  - 성공 상태 코드: 302 (있음)
  - 예 (리디렉션): **/redirect-rule / {capture_group}** 를 **/redirected/ {capture_group}**
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - 성공 상태 코드: 200 (정상)
  - 예 (재작성): **/rewrite-rule / {capture_group_1} / {capture_group_2}** 를 **다시 작성 /? var1 = {capture_group_1} & var2 = {capture_group_2}**
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - 성공 상태 코드: 302 (있음)
  - 예 (리디렉션): **/apache-mod-rules-redirect / {capture_group}** 를 **redirected /? id = {capture_group}**
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - 성공 상태 코드: 200 (정상)
  - 예 (재작성): **/iis-rules-rewrite / {capture_group}** 를 **다시 작성 /? id = {capture_group}**
* `Add(RedirectXMLRequests)`
  - 성공 상태 코드: 301 (영구적 이동)
  - 예 (리디렉션): **/file.xml** 를 **/xmlfiles/file.xml**
* `Add(new RedirectPNGRequests(".png", "/png-images")))`<br>`Add(new RedirectPNGRequests(".jpg", "/jpg-images")))`
  - 성공 상태 코드: 301 (영구적 이동)
  - 예 (리디렉션): **/image.png** 를 **/png-images/image.png**
  - 예 (리디렉션): **/image.jpg** 를 **/jpg-images/image.jpg**

## <a name="using-a-physicalfileprovider"></a>사용 하는`PhysicalFileProvider`
가져올 수도 있습니다는 `IFileProvider` 만들어는 `PhysicalFileProvider` 에 전달 하는 `AddApacheModRewrite()` 및 `AddIISUrlRewrite()` 메서드:
```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```
## <a name="secure-redirection-extensions"></a>보안 리디렉션 확장
이 샘플에 포함 되어 `WebHostBuilder` Url을 사용 하도록 응용 프로그램에 대 한 구성 (**https://localhost:5001**, **https://localhost**) 하 고 테스트 인증서 (**testCert.pfx**) 지원 하기 위해 이러한 탐색 리디렉션하면 메서드. 추가 하는 `RewriteOptions()` 에 **Startup.cs** 를 살펴보려면 해당 명령의 동작은 합니다.

메서드 | 상태 코드 | 포트
--- | :---: | :---:
`.AddRedirectToHttpsPermanent()` | 301 | null (465)
`.AddRedirectToHttps()` | 302 | null (465)
`.AddRedirectToHttps(301)` | 301 | null (465)
`.AddRedirectToHttps(301, 5001)` | 301 | 5001

# <a name="aspnet-core-url-rewriting-sample-aspnet-core-2x"></a><span data-ttu-id="3e507-101">ASP.NET Core URL 샘플을 다시 작성 (ASP.NET 2.x를 코어)</span><span class="sxs-lookup"><span data-stu-id="3e507-101">ASP.NET Core URL Rewriting Sample (ASP.NET Core 2.x)</span></span>

<span data-ttu-id="3e507-102">이 샘플에는 ASP.NET Core의 사용 방법을 보여 줍니다. 2.x URL 다시 쓰기 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="3e507-102">This sample illustrates usage of ASP.NET Core 2.x URL Rewriting Middleware.</span></span> <span data-ttu-id="3e507-103">응용 프로그램 URL 리디렉션 및 URL 재작성 옵션을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3e507-103">The application demonstrates URL redirect and URL rewriting options.</span></span> <span data-ttu-id="3e507-104">ASP.NET Core 1.x 샘플을 보려면 [ASP.NET Core URL 다시 쓰기 샘플 (ASP.NET Core 1.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/1.x)합니다.</span><span class="sxs-lookup"><span data-stu-id="3e507-104">For the ASP.NET Core 1.x sample, see [ASP.NET Core URL Rewriting Sample (ASP.NET Core 1.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/1.x).</span></span>

<span data-ttu-id="3e507-105">샘플을 실행할 규칙 중 하나는 요청 URL에 적용 될 때 다시 작성 또는 리디렉션 URL을 보여 주는 응답은 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e507-105">When running the sample, a response will be served that shows the rewritten or redirected URL when one of the rules is applied to a request URL.</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="3e507-106">이 샘플의 예제</span><span class="sxs-lookup"><span data-stu-id="3e507-106">Examples in this sample</span></span>

* `AddRedirect("redirect-rule/(.*)", "$1")`
  - <span data-ttu-id="3e507-107">성공 상태 코드: 302 (있음)</span><span class="sxs-lookup"><span data-stu-id="3e507-107">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="3e507-108">예 (리디렉션): **/redirect-rule / {capture_group}** 를 **/redirected/ {capture_group}**</span><span class="sxs-lookup"><span data-stu-id="3e507-108">Example (redirect): **/redirect-rule/{capture_group}** to **/redirected/{capture_group}**</span></span>
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - <span data-ttu-id="3e507-109">성공 상태 코드: 200 (정상)</span><span class="sxs-lookup"><span data-stu-id="3e507-109">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="3e507-110">예 (재작성): **/rewrite-rule / {capture_group_1} / {capture_group_2}** 를 **다시 작성 /? var1 = {capture_group_1} & var2 = {capture_group_2}**</span><span class="sxs-lookup"><span data-stu-id="3e507-110">Example (rewrite): **/rewrite-rule/{capture_group_1}/{capture_group_2}** to **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span></span>
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - <span data-ttu-id="3e507-111">성공 상태 코드: 302 (있음)</span><span class="sxs-lookup"><span data-stu-id="3e507-111">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="3e507-112">예 (리디렉션): **/apache-mod-rules-redirect / {capture_group}** 를 **redirected /? id = {capture_group}**</span><span class="sxs-lookup"><span data-stu-id="3e507-112">Example (redirect): **/apache-mod-rules-redirect/{capture_group}** to **/redirected?id={capture_group}**</span></span>
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - <span data-ttu-id="3e507-113">성공 상태 코드: 200 (정상)</span><span class="sxs-lookup"><span data-stu-id="3e507-113">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="3e507-114">예 (재작성): **/iis-rules-rewrite / {capture_group}** 를 **다시 작성 /? id = {capture_group}**</span><span class="sxs-lookup"><span data-stu-id="3e507-114">Example (rewrite): **/iis-rules-rewrite/{capture_group}** to **/rewritten?id={capture_group}**</span></span>
* `Add(RedirectXMLRequests)`
  - <span data-ttu-id="3e507-115">성공 상태 코드: 301 (영구적 이동)</span><span class="sxs-lookup"><span data-stu-id="3e507-115">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="3e507-116">예 (리디렉션): **/file.xml** 를 **/xmlfiles/file.xml**</span><span class="sxs-lookup"><span data-stu-id="3e507-116">Example (redirect): **/file.xml** to **/xmlfiles/file.xml**</span></span>
* `Add(new RedirectPNGRequests(".png", "/png-images")))`<br>`Add(new RedirectPNGRequests(".jpg", "/jpg-images")))`
  - <span data-ttu-id="3e507-117">성공 상태 코드: 301 (영구적 이동)</span><span class="sxs-lookup"><span data-stu-id="3e507-117">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="3e507-118">예 (리디렉션): **/image.png** 를 **/png-images/image.png**</span><span class="sxs-lookup"><span data-stu-id="3e507-118">Example (redirect): **/image.png** to **/png-images/image.png**</span></span>
  - <span data-ttu-id="3e507-119">예 (리디렉션): **/image.jpg** 를 **/jpg-images/image.jpg**</span><span class="sxs-lookup"><span data-stu-id="3e507-119">Example (redirect): **/image.jpg** to **/jpg-images/image.jpg**</span></span>

## <a name="using-a-physicalfileprovider"></a><span data-ttu-id="3e507-120">사용 하는`PhysicalFileProvider`</span><span class="sxs-lookup"><span data-stu-id="3e507-120">Using a `PhysicalFileProvider`</span></span>
<span data-ttu-id="3e507-121">가져올 수도 있습니다는 `IFileProvider` 만들어는 `PhysicalFileProvider` 에 전달 하는 `AddApacheModRewrite()` 및 `AddIISUrlRewrite()` 메서드:</span><span class="sxs-lookup"><span data-stu-id="3e507-121">You can also obtain an `IFileProvider` by creating a `PhysicalFileProvider` to pass into the `AddApacheModRewrite()` and `AddIISUrlRewrite()` methods:</span></span>
```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```
## <a name="secure-redirection-extensions"></a><span data-ttu-id="3e507-122">보안 리디렉션 확장</span><span class="sxs-lookup"><span data-stu-id="3e507-122">Secure redirection extensions</span></span>
<span data-ttu-id="3e507-123">이 샘플에 포함 되어 `WebHostBuilder` Url을 사용 하도록 응용 프로그램에 대 한 구성 (**https://localhost:5001**, **https://localhost**) 하 고 테스트 인증서 (**testCert.pfx**) 지원 하기 위해 이러한 탐색 리디렉션하면 메서드.</span><span class="sxs-lookup"><span data-stu-id="3e507-123">This sample includes `WebHostBuilder` configuration for the app to use URLs (**https://localhost:5001**, **https://localhost**) and a test certificate (**testCert.pfx**) to assist you in exploring these redirect methods.</span></span> <span data-ttu-id="3e507-124">추가 하는 `RewriteOptions()` 에 **Startup.cs** 를 살펴보려면 해당 명령의 동작은 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e507-124">Add any of them to the `RewriteOptions()` in **Startup.cs** to study their behavior.</span></span>

<span data-ttu-id="3e507-125">메서드</span><span class="sxs-lookup"><span data-stu-id="3e507-125">Method</span></span> | <span data-ttu-id="3e507-126">상태 코드</span><span class="sxs-lookup"><span data-stu-id="3e507-126">Status Code</span></span> | <span data-ttu-id="3e507-127">포트</span><span class="sxs-lookup"><span data-stu-id="3e507-127">Port</span></span>
--- | :---: | :---:
`.AddRedirectToHttpsPermanent()` | <span data-ttu-id="3e507-128">301</span><span class="sxs-lookup"><span data-stu-id="3e507-128">301</span></span> | <span data-ttu-id="3e507-129">null (465)</span><span class="sxs-lookup"><span data-stu-id="3e507-129">null (465)</span></span>
`.AddRedirectToHttps()` | <span data-ttu-id="3e507-130">302</span><span class="sxs-lookup"><span data-stu-id="3e507-130">302</span></span> | <span data-ttu-id="3e507-131">null (465)</span><span class="sxs-lookup"><span data-stu-id="3e507-131">null (465)</span></span>
`.AddRedirectToHttps(301)` | <span data-ttu-id="3e507-132">301</span><span class="sxs-lookup"><span data-stu-id="3e507-132">301</span></span> | <span data-ttu-id="3e507-133">null (465)</span><span class="sxs-lookup"><span data-stu-id="3e507-133">null (465)</span></span>
`.AddRedirectToHttps(301, 5001)` | <span data-ttu-id="3e507-134">301</span><span class="sxs-lookup"><span data-stu-id="3e507-134">301</span></span> | <span data-ttu-id="3e507-135">5001</span><span class="sxs-lookup"><span data-stu-id="3e507-135">5001</span></span>

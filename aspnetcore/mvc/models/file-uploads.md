---
title: "ASP.NET Core에서 파일 업로드"
author: ardalis
description: "ASP.NET Core MVC에서 파일을 업로드 하려면 모델 바인딩 및 스트리밍를 사용 하는 방법입니다."
keywords: "ASP.NET Core, 파일 업로드, 바인딩, 스트리밍 IFormFile 모델"
ms.author: riande
manager: wpickett
ms.date: 07/05/2017
ms.topic: article
ms.assetid: ebc98159-a028-4a94-b06c-43981c79c6be
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/file-uploads
ms.openlocfilehash: e8608a46d6688df8da6c665a25b6f4db5f480461
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="file-uploads-in-aspnet-core"></a><span data-ttu-id="374f3-104">ASP.NET Core에서 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="374f3-104">File uploads in ASP.NET Core</span></span>

<span data-ttu-id="374f3-105">으로 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="374f3-105">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="374f3-106">ASP.NET MVC 작업은 더 작은 파일에 대 한 바인딩 또는 더 큰 파일에 대 한 스트리밍 간단한 모델을 사용 하 여 하나 이상의 파일 업로드를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-106">ASP.NET MVC actions support uploading of one or more files using simple model binding for smaller files or streaming for larger files.</span></span>

[<span data-ttu-id="374f3-107">GitHub에서 보거나 다운로드 샘플</span><span class="sxs-lookup"><span data-stu-id="374f3-107">View or download sample from GitHub</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample)

## <a name="uploading-small-files-with-model-binding"></a><span data-ttu-id="374f3-108">모델 바인딩을 작은 파일을 업로드 하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-108">Uploading small files with model binding</span></span>

<span data-ttu-id="374f3-109">작은 파일을 업로드 하려면 다중 파트 HTML 폼을 사용 하거나 JavaScript를 사용 하 여 POST 요청을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-109">To upload small files, you can use a multi-part HTML form or construct a POST request using JavaScript.</span></span> <span data-ttu-id="374f3-110">Razor를 지 원하는 여러 업로드 된 파일을 사용 하 여 폼 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-110">An example form using Razor, which supports multiple uploaded files, is shown below:</span></span>

```html
<form method="post" enctype="multipart/form-data" asp-controller="UploadFiles" asp-action="Index">
    <div class="form-group">
        <div class="col-md-10">
            <p>Upload one or more files using this form:</p>
            <input type="file" name="files" multiple />
        </div>
    </div>
    <div class="form-group">
        <div class="col-md-10">
            <input type="submit" value="Upload" />
        </div>
    </div>
</form>
```

<span data-ttu-id="374f3-111">파일 업로드를 지원 하려면 HTML 양식을 지정 해야 합니다는 `enctype` 의 `multipart/form-data`합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-111">In order to support file uploads, HTML forms must specify an `enctype` of `multipart/form-data`.</span></span> <span data-ttu-id="374f3-112">`files` input 요소 위에 표시 된 여러 파일 업로드를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-112">The `files` input element shown above supports uploading multiple files.</span></span> <span data-ttu-id="374f3-113">생략 된 `multiple` 업로드할 단일 파일 수 있도록이 입력된 요소에 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-113">Omit the `multiple` attribute on this input element to allow just a single file to be uploaded.</span></span> <span data-ttu-id="374f3-114">위의 태그도 브라우저에서 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-114">The above markup renders in a browser as:</span></span>

![양식 파일 업로드](file-uploads/_static/upload-form.png)

<span data-ttu-id="374f3-116">서버에 업로드 한 개별 파일을 통해 액세스할 수 있습니다 [모델 바인딩](xref:mvc/models/model-binding) 를 사용 하는 [IFormFile](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.iformfile) 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-116">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using the [IFormFile](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.iformfile) interface.</span></span> <span data-ttu-id="374f3-117">`IFormFile`다음과 같은 구조에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-117">`IFormFile` has the following structure:</span></span>

```csharp
public interface IFormFile
{
    string ContentType { get; }
    string ContentDisposition { get; }
    IHeaderDictionary Headers { get; }
    long Length { get; }
    string Name { get; }
    string FileName { get; }
    Stream OpenReadStream();
    void CopyTo(Stream target);
    Task CopyToAsync(Stream target, CancellationToken cancellationToken = null);
}
```

> [!WARNING]
> <span data-ttu-id="374f3-118">에 의존 하거나 신뢰 하지는 `FileName` 유효성 검사 없이 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-118">Don't rely on or trust the `FileName` property without validation.</span></span> <span data-ttu-id="374f3-119">`FileName` 속성 표시 목적 으로만 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-119">The `FileName` property should only be used for display purposes.</span></span>

<span data-ttu-id="374f3-120">모델 바인딩을 사용 하 여 파일을 업로드 하는 경우 및 `IFormFile` 인터페이스, 작업 메서드는 단일을 사용할 수 `IFormFile` 또는 `IEnumerable<IFormFile>` (또는 `List<IFormFile>`) 여러 파일을 나타내는입니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-120">When uploading files using model binding and the `IFormFile` interface, the action method can accept either a single `IFormFile` or an `IEnumerable<IFormFile>` (or `List<IFormFile>`) representing several files.</span></span> <span data-ttu-id="374f3-121">다음 예제에서는 하나 이상의 업로드 된 파일을 반복 하 고, 로컬 파일 시스템에 저장, 업로드 된 파일의 크기와 총 수를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-121">The following example loops through one or more uploaded files, saves them to the local file system, and returns the total number and size of files uploaded.</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]

<span data-ttu-id="374f3-122">사용 하 여 업로드 된 파일의 `IFormFile` 기술을 처리 되기 전에 메모리에 또는 웹 서버에서 디스크를 버퍼링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-122">Files uploaded using the `IFormFile` technique are buffered in memory or on disk on the web server before being processed.</span></span> <span data-ttu-id="374f3-123">동작 메서드 내부에서 `IFormFile` 내용이 스트림으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-123">Inside the action method, the `IFormFile` contents are accessible as a stream.</span></span> <span data-ttu-id="374f3-124">로컬 파일 시스템 외에도 파일에 스트리밍할 수 있습니다 [Azure Blob 저장소](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-blobs/) 또는 [Entity Framework](https://docs.microsoft.com/ef/core/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-124">In addition to the local file system, files can be streamed to [Azure Blob storage](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-blobs/) or [Entity Framework](https://docs.microsoft.com/ef/core/index).</span></span>

<span data-ttu-id="374f3-125">Entity Framework를 사용 하 여 데이터베이스에 이진 파일 데이터를 저장 하려면 형식의 속성을 정의 `byte[]` 엔터티의:</span><span class="sxs-lookup"><span data-stu-id="374f3-125">To store binary file data in a database using Entity Framework, define a property of type `byte[]` on the entity:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public byte[] AvatarImage { get; set; }
}
```

<span data-ttu-id="374f3-126">형식의 viewmodel 속성 지정 `IFormFile`:</span><span class="sxs-lookup"><span data-stu-id="374f3-126">Specify a viewmodel property of type `IFormFile`:</span></span>

```csharp
public class RegisterViewModel
{
    // other properties omitted

    public IFormFile AvatarImage { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="374f3-127">`IFormFile`용도 동작 메서드 매개 변수 또는 viewmodel 속성으로 직접 위와 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-127">`IFormFile` can be used directly as an action method parameter or as a viewmodel property, as shown above.</span></span>

<span data-ttu-id="374f3-128">복사는 `IFormFile` 스트림으로 고 바이트 배열에 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-128">Copy the `IFormFile` to a stream and save it to the byte array:</span></span>

```csharp
// POST: /Account/Register
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Register(RegisterViewModel model)
{
    ViewData["ReturnUrl"] = returnUrl;
    if  (ModelState.IsValid)
    {
        var user = new ApplicationUser {
          UserName = model.Email,
          Email = model.Email
        };
        using (var memoryStream = new MemoryStream())
        {
            await model.AvatarImage.CopyToAsync(memoryStream);
            user.AvatarImage = memoryStream.ToArray();
        }
    // additional logic omitted
    
    // Don't rely on or trust the model.AvatarImage.FileName property 
    // without validation.
}
```

> [!NOTE]
> <span data-ttu-id="374f3-129">사용 하 여 주의 관계형 데이터베이스에서 이진 데이터를 저장할 때 처럼 성능이 저하 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-129">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>

## <a name="uploading-large-files-with-streaming"></a><span data-ttu-id="374f3-130">스트리밍 큰 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="374f3-130">Uploading large files with streaming</span></span>

<span data-ttu-id="374f3-131">크기 또는 파일 업로드 빈도 앱에 대 한 리소스 문제를 발생 시킨 경우 고려 전체를 버퍼링 하는 것이 아니라 파일 업로드를 스트리밍 위에 표시 된 모델 바인딩 방법을 마찬가지로 합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-131">If the size or frequency of file uploads is causing resource problems for the app, consider streaming the file upload rather than buffering it in its entirety, as the model binding approach shown above does.</span></span> <span data-ttu-id="374f3-132">사용 하는 동안 `IFormFile` 모델 바인딩은 훨씬 간단한 해결책을 제대로 구현 하는 단계 수가 스트리밍을 사용 하려면 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-132">While using `IFormFile` and model binding is a much simpler solution, streaming requires a number of steps to implement properly.</span></span>

> [!NOTE]
> <span data-ttu-id="374f3-133">모든 단일 버퍼링된 파일 64KB를 초과 서버에 디스크에 임시 파일 RAM에서 이동할 수 됩니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-133">Any single buffered file exceeding 64KB will be moved from RAM to a temp file on disk on the server.</span></span> <span data-ttu-id="374f3-134">파일 업로드에서 사용 되는 리소스 (디스크, RAM)의 동시 파일 업로드 크기와 수에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-134">The resources (disk, RAM) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="374f3-135">스트리밍가 아닙니다. 성능에 대 한 수많은 눈금에 관한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-135">Streaming is not so much about perf, it's about scale.</span></span> <span data-ttu-id="374f3-136">너무 많은 업로드 버퍼링 하려고 하면 사이트에 메모리 또는 디스크 공간이 부족할 때 작동이 중단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-136">If you try to buffer too many uploads, your site will crash when it runs out of memory or disk space.</span></span>

<span data-ttu-id="374f3-137">다음 예제에서는 컨트롤러 작업에 stream을 JavaScript/각을 사용 하 여 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-137">The following example demonstrates using JavaScript/Angular to stream to a controller action.</span></span> <span data-ttu-id="374f3-138">사용자 지정 필터 특성을 사용 하 고 대신 요청 본문에 HTTP 헤더에 전달 된 파일의 antiforgery 토큰이 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-138">The file's antiforgery token is generated using a custom filter attribute and passed in HTTP headers instead of in the request body.</span></span> <span data-ttu-id="374f3-139">동작 메서드가 업로드 된 데이터를 직접 처리 하기 때문에 모델 바인딩은 다른 필터에 의해 비활성화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-139">Because the action method processes the uploaded data directly, model binding is disabled by another filter.</span></span> <span data-ttu-id="374f3-140">작업, 내에서 폼의 읽은 내용을 사용 하는 `MultipartReader`, 각 개인을 읽는 `MultipartSection`, 파일을 처리 하거나 적절 하 게 콘텐츠를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-140">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="374f3-141">모든 섹션을 읽은 후 동작 자체 모델 바인딩을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-141">Once all sections have been read, the action performs its own model binding.</span></span>

<span data-ttu-id="374f3-142">폼을 로드 하 고 antiforgery 토큰 쿠키에 저장 하는 첫 번째 작업 (통해는 `GenerateAntiforgeryTokenCookieForAjax` 특성):</span><span class="sxs-lookup"><span data-stu-id="374f3-142">The initial action loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieForAjax` attribute):</span></span>

```csharp
[HttpGet]
[GenerateAntiforgeryTokenCookieForAjax]
public IActionResult Index()
{
    return View();
}
```

<span data-ttu-id="374f3-143">ASP.NET Core 기본 제공을 사용 하 여 특성 [Antiforgery](xref:security/anti-request-forgery) 지원 요청 토큰으로 쿠키를 설정 하려면:</span><span class="sxs-lookup"><span data-stu-id="374f3-143">The attribute uses ASP.NET Core's built-in [Antiforgery](xref:security/anti-request-forgery) support to set a cookie with a request token:</span></span>

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]

<span data-ttu-id="374f3-144">각 명명 된 요청 헤더에 antiforgery 토큰을 자동으로 전달 `X-XSRF-TOKEN`합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-144">Angular automatically passes an antiforgery token in a request header named `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="374f3-145">ASP.NET Core MVC 응용 프로그램의 구성에서이 헤더를 참조 하도록 구성 된 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="374f3-145">The ASP.NET Core MVC app is configured to refer to this header in its configuration in *Startup.cs*:</span></span>

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]

<span data-ttu-id="374f3-146">`DisableFormValueModelBinding` 아래 표시 된 특성을 바인딩하는 모델을 사용 하지 않도록 설정 하는 데 사용 되는 `Upload` 동작 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-146">The `DisableFormValueModelBinding` attribute, shown below, is used to disable model binding for the `Upload` action method.</span></span>

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]

<span data-ttu-id="374f3-147">모델 바인딩을 사용 하지 않으면 하므로 `Upload` 동작 메서드 매개 변수를 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-147">Since model binding is disabled, the `Upload` action method doesn't accept parameters.</span></span> <span data-ttu-id="374f3-148">와 직접 통신은 `Request` 속성 `ControllerBase`합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-148">It works directly with the `Request` property of `ControllerBase`.</span></span> <span data-ttu-id="374f3-149">A `MultipartReader` 각 섹션을 읽는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-149">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="374f3-150">파일 GUID 파일 이름으로 저장 되 고 키/값 데이터에 저장 됩니다는 `KeyValueAccumulator`합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-150">The file is saved with a GUID filename and the key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="374f3-151">모든 섹션을 읽은 내용의 되 면는 `KeyValueAccumulator` 양식 데이터 모델 형식에 바인딩하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-151">Once all sections have been read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="374f3-152">전체 `Upload` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-152">The complete `Upload` method is shown below:</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]

## <a name="troubleshooting"></a><span data-ttu-id="374f3-153">문제 해결</span><span class="sxs-lookup"><span data-stu-id="374f3-153">Troubleshooting</span></span>

<span data-ttu-id="374f3-154">다음은 파일 및 가능한 해결 방법 업로드 작업할 때 발생 하는 몇 가지 일반적인 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-154">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="unexpected-not-found-error-with-iis"></a><span data-ttu-id="374f3-155">IIS와 예기치 않은 찾을 수 없음 오류</span><span class="sxs-lookup"><span data-stu-id="374f3-155">Unexpected Not Found error with IIS</span></span>

<span data-ttu-id="374f3-156">다음과 같은 오류가 파일 업로드 서버를 초과 했음을 나타내는 구성한 `maxAllowedContentLength`:</span><span class="sxs-lookup"><span data-stu-id="374f3-156">The following error indicates your file upload exceeds the server's configured `maxAllowedContentLength`:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="374f3-157">기본 설정은 `30000000`, 약 28.6 m B 인 합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-157">The default setting is `30000000`, which is approximately 28.6MB.</span></span> <span data-ttu-id="374f3-158">값을 편집 하 여 사용자 지정할 수 있습니다 *web.config*:</span><span class="sxs-lookup"><span data-stu-id="374f3-158">The value can be customized by editing *web.config*:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- This will handle requests up to 50MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="374f3-159">이 설정은 IIS에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-159">This setting only applies to IIS.</span></span> <span data-ttu-id="374f3-160">동작은 Kestrel에 호스트 하는 경우 기본적으로 발생 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-160">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="374f3-161">자세한 내용은 참조 [요청 제한 \<requestLimits\>](https://docs.microsoft.com/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-161">For more information, see [Request Limits \<requestLimits\>](https://docs.microsoft.com/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="374f3-162">IFormFile 사용 하 여 null 참조 예외</span><span class="sxs-lookup"><span data-stu-id="374f3-162">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="374f3-163">컨트롤러는 수락 하는 경우 사용 하 여 파일 업로드 `IFormFile` HTML 폼이 지정 되 고 있는지 확인, 값은 항상 null을 찾을 수 있지만 `enctype` 값 `multipart/form-data`합니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-163">If your controller is accepting uploaded files using `IFormFile` but you find that the value is always null, confirm that your HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="374f3-164">이 특성이 설정 되지 않은 경우는 `<form>` 요소, 파일 업로드 수행 되지 것입니다 및 모든 바운드 `IFormFile` 인수는 null이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="374f3-164">If this attribute is not set on the `<form>` element, the file upload will not occur and any bound `IFormFile` arguments will be null.</span></span>

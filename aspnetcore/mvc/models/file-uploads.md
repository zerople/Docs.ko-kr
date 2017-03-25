---
title: "파일 업로드 | Microsoft 문서"
author: ardalis
description: "ASP.NET 핵심 MVC의 파일을 업로드 하려면 모델 바인딩 및 스트리밍를 사용 하는 방법입니다."
keywords: "ASP.NET Core, 파일 업로드, 바인딩, 스트리밍 IFormFile 모델"
ms.author: riande
manager: wpickett
ms.date: 11/10/2016
ms.topic: article
ms.assetid: ebc98159-a028-4a94-b06c-43981c79c6be
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/file-uploads
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 98951e55738a179f1ad3b6f2dda1ac680ebface5
ms.lasthandoff: 03/23/2017

---
# <a name="file-uploads"></a>파일 업로드

[Steve Smith](http://ardalis.com)

ASP.NET MVC 작업 지원의 작은 파일에 대 한 바인딩 또는 더 큰 파일에 대 한 스트리밍 간단한 모델을 사용 하 여 하나 이상의 파일을 업로드 합니다.

[보기 또는 GitHub에서 샘플을 다운로드 합니다.](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample)

## <a name="uploading-small-files-with-model-binding"></a>모델 바인딩을 사용 하 여 작은 파일 업로드

작은 파일을 업로드 하려면 다중 파트 HTML 폼을 사용할 수도 있고 JavaScript를 사용 하 여 POST 요청을 생성할 수 있습니다. Razor를 지 원하는 여러 업로드 된 파일을 사용 하 여 예제 폼은 다음과 같습니다.

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

파일 업로드를 지원 하려면 HTML 폼을 지정 해야는 `enctype` 의 `multipart/form-data`합니다. `files` 입력된 요소 위에 표시 된 여러 파일 업로드를 지원 합니다. 생략 된 `multiple` 업로드할 단일 파일을 허용 하도록 입력된이 요소의 특성입니다. 위의 태그도 브라우저에 렌더링합니다.

![파일 업로드 양식](file-uploads/_static/upload-form.png)

서버에 업로드 하는 개별 파일을 통해 액세스할 수 있습니다 [모델 바인딩](xref:mvc/models/model-binding) 를 사용 하는 [IFormFile](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.iformfile) 인터페이스입니다. `IFormFile`다음과 같은 구조로 되어 있습니다.

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
> 에 의존 하거나 신뢰 하지는 `FileName` 유효성 검사 없이 속성입니다. `FileName` 속성 표시 용도로 사용 해야 합니다.

모델 바인딩을 사용 하 여 파일을 업로드 하는 경우 및 `IFormFile` 인터페이스를 작업 메서드는 단일 응하기 `IFormFile` 또는 `IEnumerable<IFormFile>` (또는 `List<IFormFile>`) 여러 파일을 나타내는 합니다. 다음 예제에서는 하나 이상의 업로드 된 파일을 반복 하 고, 로컬 파일 시스템에 저장, 업로드 된 파일의 크기와 총 수를 반환 합니다.

[!code-csharp[주](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]

사용 하 여 업로드 된 파일은 `IFormFile` 기법에 메모리 또는 디스크 웹 서버에서 처리 되기 전에 버퍼링 됩니다. 작업 메서드 내에서 `IFormFile` 내용이 스트림으로 액세스할 수 있습니다. 로컬 파일 시스템 외에도 파일에 스트리밍할 수 있는 [Azure Blob 저장소](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-blobs) 또는 [Entity Framework](https://docs.microsoft.com/ef/core/index)합니다.

Entity Framework를 사용 하 여 데이터베이스에 이진 파일 데이터를 저장 하기 위해 형식의 속성을 정의 `byte[]` 엔터티:

```csharp
public class ApplicationUser : IdentityUser
{
    public byte[] AvatarImage { get; set; }
}
```

Viewmodel 형식의 속성을 지정 `IFormFile`:

```csharp
public class RegisterViewModel
{
    // other properties omitted

    public IFormFile AvatarImage { get; set; }
}
```

> [!NOTE]
> `IFormFile`사용할 수는 작업 메서드에 매개 변수로 직접 또는 viewmodel 속성으로 위와 같이 합니다.

복사는 `IFormFile` 스트림으로 및 바이트 배열에 저장 합니다.

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
> 주의 관계형 데이터베이스에 이진 데이터를 저장 하는 경우 처럼 성능이 저하 될 수 있습니다.

## <a name="uploading-large-files-with-streaming"></a>스트리밍 큰 파일 업로드

크기 또는 파일 업로드 빈도 응용 프로그램에 대 한 리소스 문제가 발생 하는 경우 고려해 전체를 버퍼링 하는 것이 아니라 파일 업로드를 스트리밍 위에 표시 된 모델 바인딩 방식을 마찬가지로 합니다. 사용 하는 동안 `IFormFile` 모델 바인딩에 훨씬 간단한 해결책을 올바르게 구현 하는 단계 수가 필요 스트리밍.

> [!NOTE]
> 모든 단일 버퍼링된 파일 64KB를 초과 서버에 임시 디스크의 파일에 RAM에서 이동할 수 됩니다. 파일 업로드에서 사용 되는 리소스 (디스크, RAM)의 동시 파일 업로드 크기와 수에 따라 달라 집니다. 스트리밍 없는 보다 성능, 크기 조정에 대 한입니다. 버퍼가 너무 많은 업로드 하려고 하면 메모리 나 디스크 공간이 부족할 때 사이트가 작동이 중단 됩니다.

다음 예제에서는 JavaScript/Angular를 사용 하 여 컨트롤러 작업을 하 게 하는 방법을 보여 줍니다. 사용자 지정 필터 특성을 사용 하 고 대신 HTTP 헤더를 요청 본문에 전달 된 파일의 antiforgery 토큰이 생성 됩니다. 작업 메서드 업로드 된 데이터를 직접 처리 하기 때문에 모델 바인딩은 다른 필터에 의해 비활성화 됩니다. 작업, 내 폼의 내용을 읽습니다.이 사용 하는 `MultipartReader`, 각 개별 읽는 `MultipartSection`, 파일을 처리 또는 콘텐츠를 적절 하 게 저장 합니다. 모든 섹션을 읽은 후 작업에는 자체 모델 바인딩을 수행 합니다.

폼을 로드 하 고 쿠키에는 antiforgery 토큰을 저장 하는 첫 번째 작업 (통해는 `GenerateAntiforgeryTokenCookieForAjax` 특성):

```csharp
[HttpGet]
[GenerateAntiforgeryTokenCookieForAjax]
public IActionResult Index()
{
    return View();
}
```

ASP.NET Core의 기본 제공을 사용 하 여 특성 [Antiforgery](xref:security/anti-request-forgery) 지원 요청 토큰으로 쿠키를 설정 하려면:

[!code-csharp[주](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]

Angular 라는 요청 헤더에는 antiforgery 토큰을 자동으로 전달 `X-XSRF-TOKEN`합니다. ASP.NET Core MVC 응용 프로그램의 구성에서이 헤더를 참조 하도록 구성 된 *Startup.cs*:

[!code-csharp[주](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]

`DisableFormValueModelBinding` 아래에 표시 된 특성을 바인딩하는 모델을 사용 하지 않도록 설정 하는 데 사용 되는 `Upload` 작업 메서드.

[!code-csharp[주](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]

모델 바인딩을 사용 하지 않으면 이후는 `Upload` 작업 메서드에 매개 변수를 허용 하지 않습니다. 와 직접 통신은 `Request` 속성 `ControllerBase`합니다. A `MultipartReader` 각 섹션을 읽는 데 사용 됩니다. 파일은 GUID 파일 이름으로 저장 및 키/값 데이터에 저장 됩니다는 `KeyValueAccumulator`합니다. 모든 섹션을 읽은 후 면는 `KeyValueAccumulator` 양식 데이터 모델 형식에 바인딩하는 데 사용 됩니다.

전체 `Upload` 메서드는 다음과 같습니다.

[!code-csharp[주](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]

## <a name="troubleshooting"></a>문제 해결

다음은 파일 및 가능한 해결 방법 업로드 작업할 때 발생 하는 몇 가지 일반적인 문제입니다.

### <a name="unexpected-not-found-error-with-iis"></a>IIS와 예기치 않은 찾을 수 없음 오류

다음 오류는 서버를 초과 하는 파일 업로드 나타냅니다에서 구성한 `maxAllowedContentLength`:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

기본 설정은 `30000000`, 약 28.6 m B입니다. 값을 편집 하 여 사용자 지정할 수 있습니다 *web.config*:

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

이 설정은 IIS에만 적용 됩니다. 동작은 Kestrel에서 호스트할 때 기본적으로 발생 하지 않습니다. 자세한 내용은 참조 [요청 제한 \<requestLimits\>](https://www.iis.net/configreference/system.webserver/security/requestfiltering/requestlimits)합니다.

### <a name="null-reference-exception-with-iformfile"></a>IFormFile 사용 하 여 null 참조 예외

컨트롤러는 수락 하는 경우 사용 하 여 파일 업로드 `IFormFile` HTML 폼을 지정 하는 확인, 값은 항상 null을 찾을 수 있지만 `enctype` 값 `multipart/form-data`합니다. 이 특성에 설정 되지 않은 경우는 `<form>` 요소, 파일 업로드 발생 하지 것입니다 및 모든 바운드 `IFormFile` 인수는 null이 됩니다.


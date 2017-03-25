---
title: "ASP.NET Core MVC에서 응답 데이터 서식 지정 | Microsoft 문서"
author: ardalis
description: "ASP.NET Core MVC에서 응답 데이터 형식을 지정 하는 방법에 알아봅니다."
keywords: "ASP.NET Core, IOutputFormatter, IActionResult 응답 데이터"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: c056df45-d013-4826-91a1-4a092bae1ea5
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/formatting
ms.custom: H1Hack27Feb2017
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: f8b83546e5e76e0d5e5a210329c4ce217c81333a
ms.lasthandoff: 03/23/2017

---
# <a name="introduction-to-formatting-response-data-in-aspnet-core-mvc"></a>ASP.NET 핵심 MVC의 서식 응답 데이터 소개

[Steve Smith](http://ardalis.com)

ASP.NET Core MVC에서 응답 데이터를 고정된 형식을 사용 하 여 서식을 지정 하거나 클라이언트 사양에 대 한 응답으로 기본적으로 지원 합니다.

[보기 또는 GitHub에서 샘플을 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/formatting/sample)합니다.

## <a name="format-specific-action-results"></a>형식에 따른 작업 결과

일부 작업 결과 유형은 특정 형식으로, 특정 같은 `JsonResult` 및 `ContentResult`합니다. 작업은 항상 특정 한 방식으로 형식이 지정 된 특정 결과 반환할 수 있습니다. 예를 들어, 반환 된 `JsonResult` 클라이언트 기본 설정에 관계 없이 JSON 형식 데이터를 반환 합니다. 마찬가지로, 반환 된 `ContentResult` (단순히 문자열을 반환 합니다)으로 일반 텍스트 형식 문자열 데이터를 반환 합니다.

> [!NOTE]
> 작업은 모든 특정 형식을 반환 하는 데 필요 하지 MVC는 모든 개체 반환 값을 지원합니다. 작업에서 반환 하는 경우는 `IActionResult` 구현과 컨트롤러에서 상속 `Controller`, 개발자는 여러 선택 항목에 해당 하는 많은 도우미 메서드. 개체를 반환 하는 작업의에서 결과는 없는 `IActionResult` 적절 한 사용 하 여 형식을 serialize 할 `IOutputFormatter` 구현 합니다.

특정 형식에서에서 상속 하는 컨트롤러에서 데이터를 반환 하는 `Controller` 기본 클래스, 기본 제공 도우미 메서드를 사용 `Json` JSON을 반환 하 고 `Content` 일반 텍스트에 대 한. 작업 메서드는 특정 결과 형식을 반환 해야 (예를 들어, `JsonResult`) 또는 `IActionResult`합니다.

JSON 형식의 데이터를 반환 합니다.

[!code-csharp[주](./formatting/sample/src/ResponseFormattingSample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]

이 작업의 예제 응답:

![application/json 응답의 콘텐츠 형식을 보여 주는 Microsoft Edge에 대 한 개발자 도구의 네트워크 탭](formatting/_static/json-response.png)

응답의 콘텐츠 형식을 `application/json`목록의 네트워크 요청 및 응답 헤더 섹션에 나와 있습니다. 또한 note 요청 헤더 섹션에 Accept 헤더에서에 (이 경우 Microsoft Edge) 브라우저에서 제공 하는 옵션의 목록입니다. 기술을이 헤더를 무시 합니다. 따르면 그 아래에 설명 되어 있습니다.

사용 하 여 서식이 지정 된 일반 텍스트 데이터를 반환 하려면 `ContentResult` 및 `Content` 도우미:

[!code-csharp[주](./formatting/sample/src/ResponseFormattingSample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]

이 작업의 응답:

![응답의 콘텐츠 형식을 보여 주는 Microsoft Edge에 대 한 개발자 도구의 네트워크 탭은 텍스트/일반](formatting/_static/text-response.png)

이 경우는 `Content-Type` 반환 된 `text/plain`합니다. 또한 문자열 응답 형식만 사용 하 여이 동작을 얻을 수 있습니다.

[!code-csharp[주](./formatting/sample/src/ResponseFormattingSample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]

>[!TIP]
> 여러 개를 사용 하 여 특수 동작에 대 한 반환 형식 또는 옵션 (예: 수행 된 작업의 결과에 따라 다른 HTTP 상태 코드), 원하는 `IActionResult` 반환 형식으로.

## <a name="content-negotiation"></a>콘텐츠 협상

콘텐츠 협상 (*conneg* 줄여서) 클라이언트를 지정 하는 경우에 발생 한 [Accept 헤더](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)합니다. ASP.NET 핵심 MVC에서 사용 하는 기본 형식은 JSON입니다. 콘텐츠 협상에 의해 구현 됩니다 `ObjectResult`합니다. 또한 도우미 메서드에서 반환 된 특정 작업 결과 상태 코드에 기본적으로 (모두를 기반으로 하 `ObjectResult`). 모델 형식 (데이터 전송 형식으로 정의한 클래스)를 반환할 수도 있습니다 및 프레임 워크는 자동으로 줄 바꿈에 `ObjectResult` 드립니다.

다음과 같은 작업 메서드를 사용 하는 `Ok` 및 `NotFound` 도우미 메서드.

[!code-csharp[주](./formatting/sample/src/ResponseFormattingSample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]

다른 형식 요청 된 서버에 요청된 된 형식을 반환 수 하지 않는 한 JSON 형식의 응답을 반환 됩니다. 와 같은 도구를 사용할 수 있습니다 [Fiddler](http://www.telerik.com/fiddler) Accept 헤더를 포함 하는 요청을 만들고 다른 형식을 지정 합니다. 경우, 서버에 있는 경우에 *포맷터* , 요청 된 형식에 대 한 응답을 생성할 수 있는, 결과에 반환 됩니다 클라이언트 기본 형식입니다.

![수동으로 만든 보여 주는 fiddler 콘솔 GET 요청 Accept 헤더 값이 응용 프로그램/x m l 인](formatting/_static/fiddler-composer.png)

위의 스크린샷에 Fiddler 작성기를 사용 하는 요청을 생성할 지정 `Accept: application/xml`합니다. 기본적으로 ASP.NET 핵심 MVC만 지원 JSON도 다른 서식도 지정 하는 경우 반환 된 결과 여전히 JSON 형식입니다. 다음 섹션에 추가 하는 포맷터를 추가 하는 방법에 표시 됩니다.

컨트롤러 작업을 반환할 수 있습니다 Poco (Plain Old CLR Objects), ASP.NET MVC에서 자동으로 만듭니다 있는 경우는 `ObjectResult` 하는 개체를 래핑합니다. 클라이언트는 서식이 지정 된 직렬화 된 개체를 가져옵니다 (JSON 형식은 기본, XML 또는 기타 형식을 구성할 수 있습니다). 가 반환 하 고 있는 개체 `null`, 면 프레임 워크는 반환는 `204 No Content` 응답 합니다.

개체 유형을 반환 합니다.

[!code-csharp[주](./formatting/sample/src/ResponseFormattingSample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]

이 샘플에서는 유효한 작성자 별칭에 대 한 요청 작성자의 데이터와 함께 200 OK 응답을 받게 됩니다. 잘못 된 별칭에 대 한 요청 204 콘텐츠 없음 응답을 받게 됩니다. XML 및 JSON 형식의 응답을 보여 주는 스크린샷 아래에 나와 있습니다.

### <a name="content-negotiation-process"></a>콘텐츠 협상 프로세스

콘텐츠 *협상* 만 수행 하는 경우는 `Accept` 헤더는 요청에 나타납니다. 포함 된 경우 요청 accept 헤더, 프레임 워크 기본 설정 순서 대로 accept 헤더에 미디어 유형을 열거 합니다 고 accept 헤더에 지정 된 형식 중 하나에 대 한 응답을 생성할 수 있는 포맷터를 찾으려고 시도 합니다. 프레임 워크는 응답을 생성할 수 있는 첫 번째 포맷터를 찾으려고 시도 합니다 없는 포맷터는 클라이언트의 요청을 만족 시킬 수 있는 발견 되는 경우 (개발자가 옵션에서 구성한 경우가 아니면 `MvcOptions` 반환할 406 허용 되지 대신). 요청 XML을 지정 하는 경우 XML 포맷터 구성 되지 않은 JSON 포맷터 사용 됩니다. 보다 일반적으로 없는 포맷터 구성 된 경우 요청 된 형식으로 제공할 수 있는 다음의 첫 번째 포맷터 개체의 서식을 지정할 수 있는 보다 사용 됩니다. 헤더가 없으면를 지정 하는 경우 반환 되는 개체를 처리할 수 있는 첫 번째 포맷터 응답을 serialize 하는 데 사용 됩니다. 이 경우 모든 협상 수행 하 고 없는-서버에 사용할 형식을 결정 하는 합니다.

> [!NOTE]
> Accept 헤더를 포함 하는 경우 `*/*`, 않으면 헤더는 무시 됩니다 `RespectBrowserAcceptHeader` 로 설정 된 경우에 `MvcOptions`합니다.

### <a name="browsers-and-content-negotiation"></a>브라우저와 콘텐츠 협상

클라이언트와 달리 일반적인 API, 웹 브라우저에 제공 하는 하는 경향이 `Accept` 다양 한 와일드 카드를 포함 한 형식으로 포함 하는 헤더입니다. 기본적으로 프레임 워크는 브라우저에서 요청이 들어오는지 감지할 때 무시 됩니다는 `Accept` 헤더 구성과 대신 반환 콘텐츠 응용 프로그램에서의 기본 형식 (JSON 달리 구성 하지 않는 한). 이 다른 브라우저를 사용 하 여 Api를 사용 하는 보다 일관 된 환경을 제공 합니다.

응용 프로그램 명예 브라우저 헤더 수락 하려는 경우 구성할 수 있습니다이 MVC의 구성의 일부로 설정 하 여 `RespectBrowserAcceptHeader` 를 `true` 에 `ConfigureServices` 메서드에서 *Startup.cs*합니다.

```csharp
services.AddMvc(options =>
{
  options.RespectBrowserAcceptHeader = true; // false by default
}
```

## <a name="configuring-formatters"></a>포맷터를 구성합니다.

JSON 기본값 외 추가 형식도 지원 하도록 응용 프로그램에 필요한 경우에 NuGet 패키지를 추가 하 고 MVC 지원 하도록 구성할 수 있습니다. 입력 및 출력에 대해 별도 포맷터 있습니다. 입력된 포맷터에서 사용 [모델 바인딩](model-binding.md); 출력 포맷터는 응답 형식을 지정 하는 데 사용 됩니다. 구성할 수도 있습니다 [ 🔧 사용자 지정 포맷터](../advanced/custom-formatters.md)합니다.

### <a name="adding-xml-format-support"></a>XML 형식 지원 추가

XML 서식 지정에 대 한 지원을 추가 하려면 설치는 `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet 패키지입니다.

MVC의 구성에는 XmlSerializerFormatters 추가 *Startup.cs*:

[!code-csharp[주](./formatting/sample/src/ResponseFormattingSample/Startup.cs?highlight=4&range=30-36)]

또는 출력 포맷터만 추가할 수 있습니다.

```csharp
services.AddMvc(options =>
{
  options.OutputFormatters.Add(new XmlSerializerOutputFormatter());
});
```

두 가지 방법을 사용 하 여 결과 serialize 하 `System.Xml.Serialization.XmlSerializer`합니다. 원하는 경우 사용할 수 있습니다는 `System.Runtime.Serialization.DataContractSerializer` 해당 연결 된 포맷터를 추가 하 여:

```csharp
services.AddMvc(options =>
{
  options.OutputFormatters.Add(new XmlDataContractSerializerOutputFormatter());
});
```

컨트롤러 메서드는 요청에 따라 적절 한 형식을 반환 해야 XML 서식 지정에 대 한 지원을 추가 하면 `Accept` 헤더 예제에서는이 Fiddler로:

![Fiddler 콘솔: 요청에는 원시 탭 Accept 헤더 값은 application/xml에 표시 합니다. 응답에 대 한 원시 탭 application/xml 콘텐츠 형식 헤더 값을 보여 줍니다.](formatting/_static/xml-response.png)

원시 GET 요청이 이루어졌다는 Inspectors 탭에서 볼 수는 `Accept: application/xml` 헤더 집합입니다. 응답 창 표시는 `Content-Type: application/xml` 헤더 및 `Author` XML로 serialize 된 개체입니다.

Composer 탭을 사용 하 여 지정 하는 요청을 수정 하려면 `application/json` 에 `Accept` 헤더입니다. 요청을 실행 하 고 응답 JSON으로 형식이 지정 됩니다.

![Fiddler 콘솔: 요청에는 원시 탭 Accept 헤더 값은 application/json에 표시 합니다. 응답에 대 한 원시 탭 application/json 콘텐츠 형식 헤더 값을 보여 줍니다.](formatting/_static/json-response-fiddler.png)

이 스크린 샷에 표시의 헤더를 설정 하는 요청 `Accept: application/json` 동일 응답은 지정 하 고 해당 `Content-Type`합니다. `Author` 개체는 JSON 형식으로 응답의 본문에 표시 됩니다.

### <a name="forcing-a-particular-format"></a>특정 형식 강제

특정 작업에 대 한 응답 형식을 제한 하려는 경우 할 수 있습니다, 적용할 수는 `[Produces]` 필터입니다. `[Produces]` 필터는 특정 작업 (또는 컨트롤러)에 대 한 응답 형식을 지정 합니다. 대부분 원하는 [필터](../controllers/filters.md), 작업, 컨트롤러 또는 전역 범위에서이 적용할 수 있습니다.

```csharp
[Produces("application/json")]
public class AuthorsController
```

`[Produces]` 필터를 내의 모든 작업을 강제로 `AuthorsController` 다른 포맷터는 응용 프로그램 및 제공 된 클라이언트에 대해 구성 된 경우에 JSON 형식의 응답을 반환 하는 `Accept` 을 서로 다른 사용 가능한 형식 요청 헤더입니다. 참조 [필터](../controllers/filters.md) 필터를 전역으로 적용 하는 방법을 비롯 하 여 자세한 내용을 보려면 합니다.

### <a name="special-case-formatters"></a>특수 한 사례 포맷터

일부 특수 한 경우 기본 제공 포맷터를 사용 하 여 구현 됩니다. 기본적으로 `string` 반환 형식으로 형식이 지정 됩니다 *텍스트/일반* (*텍스트/html* 통해 요청 된 경우 `Accept` 헤더). 이 문제를 제거 하 여 제거할 수는 `TextOutputFormatter`합니다. 포맷터를 제거 하면는 `Configure` 메서드에서 *Startup.cs* (아래 참조). 모델 개체에 영향을 주는 작업 반환 형식을 반환 합니다는 204 콘텐츠 응답이 반환 하는 경우 `null`합니다. 이 문제를 제거 하 여 제거할 수는 `HttpNoContentOutputFormatter`합니다. 다음 코드 제거는 `TextOutputFormatter` 및 `HttpNoContentOutputFormatter`합니다.

```csharp
services.AddMvc(options =>
{
  options.OutputFormatters.RemoveType<TextOutputFormatter>();
  options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

없이 `TextOutputFormatter`, `string` 반환 형식을 반환 406 예를 들어 허용 되지 않습니다. XML 포맷터 있으면 것은 서식을 지정 참고 `string` 경우 반환 형식은 `TextOutputFormatter` 제거 됩니다.

없이 `HttpNoContentOutputFormatter`, 구성 된 포맷터를 사용 하 여 null 개체의 형식이 지정 됩니다. 예를 들어 JSON 포맷터 응답의 본문으로 반환 하기만 합니다 `null`XML 포맷터는 XML 특성을 가진 빈 요소를 반환 하는 반면, `xsi:nil="true"` 설정 합니다.

## <a name="response-format-url-mappings"></a>응답 형식 URL 매핑

클라이언트에서 쿼리 문자열 또는 부분 경로 또는.xml 또는.json 같은 형식별 파일 확장명을 사용 하 여 특정 형식을 URL의 일부로 같은 요청할 수 있습니다. 요청 경로에서 매핑 API를 사용 하 여 경로에 지정 해야 합니다. 예:

```csharp
[FormatFilter]
public class ProductsController
{
  [Route("[controller]/[action]/{id}.{format?}")]
  public Product GetById(int id)
```

이 경로 사용 하면 요청 된 형식에 선택적 파일 확장명으로 지정할 수 있습니다. `[FormatFilter]` 특성에 형식 값의 존재 여부를 검사는 `RouteData` 응답 만들어질 때 응답 형식을 적절 한 포맷터에 매핑합니다.

|경로|포맷터|
|--- |--- |
|`/products/GetById/5`|기본 출력 포맷터|
|`/products/GetById/5.json`|JSON 포맷터 (구성 된 경우)|
|`/products/GetById/5.xml`|XML 포맷터 (구성 된 경우)|


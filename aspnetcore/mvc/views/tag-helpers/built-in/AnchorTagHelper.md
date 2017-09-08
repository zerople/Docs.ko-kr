---
title: "앵커 태그 도우미 | Microsoft Docs"
author: pkellner
description: "앵커 태그 도우미를 사용 하는 방법을 보여 줍니다."
keywords: "ASP.NET Core, 태그 도우미"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: c045d485-d1dc-4cea-a675-46be83b7a011
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/AnchorTagHelper
ms.openlocfilehash: f08e6a5288076d56b55843f1872bcfa8104f3923
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="anchor-tag-helper"></a>앵커 태그 도우미

으로 [Peter Kellner](http://peterkellner.net) 

앵커 태그 도우미 향상 HTML 앵커 (`<a ... ></a>`) 새 특성을 추가 하 여는 태그입니다. 생성 된 링크 (에 `href` 태그) 새 특성을 사용 하 여 만들어집니다. 해당 URL에 https와 같은 선택적 프로토콜은 포함할 수 있습니다.

다음 스피커 컨트롤러는이 문서에는 샘플에서 사용 됩니다.

<br/>
**SpeakerController.cs** 

[!code-csharp[SpeakerController](sample/TagHelpersBuiltInAspNetCore/src/TagHelpersBuiltInAspNetCore/Controllers/SpeakerController.cs)]


## <a name="anchor-tag-helper-attributes"></a>앵커 태그 도우미 특성

- - -

### <a name="asp-controller"></a>asp 컨트롤러

`asp-controller`URL을 생성 하는 컨트롤러를 연결 하는 데 사용 됩니다. 지정 된 컨트롤러는 현재 프로젝트에 존재 해야 합니다. 다음 코드는 모든 스피커를 나열합니다. 

```cshtml
<a asp-controller="Speaker" asp-action="Index">All Speakers</a>
```

생성된 된 태그가 됩니다.

```html
<a href="/Speaker">All Speakers</a>
```

경우는 `asp-controller` 지정 및 `asp-action` 않습니다 기본 `asp-action` 현재 실행 중인 뷰의 기본 컨트롤러 메서드가 됩니다. 위 예에서 경우 `asp-action` 생략이 앵커 태그 도우미에서 생성 되 고 *HomeController*의 `Index` 보기 (**/홈**), 생성된 된 태그 됩니다:


```html
<a href="/Home">All Speakers</a>
```

- - -
  
### <a name="asp-action"></a>asp 동작

`asp-action`포함할 컨트롤러의 동작 메서드의 이름으로 생성 된 `href`합니다. 예를 들어 다음 코드는 생성 된 설정 `href` 스피커 세부 정보 페이지를 가리키도록 합니다.

```html
<a asp-controller="Speaker" asp-action="Detail">Speaker Detail</a>
```

생성된 된 태그가 됩니다.

```html
<a href="/Speaker/Detail">Speaker Detail</a>
```

되지 않은 경우 `asp-controller` 특성 지정, 호출 현재 뷰를 실행 하는 보기 기본 컨트롤러가 사용 됩니다.  
 
경우 특성 `asp-action` 은 `Index`, 아무 작업도 앞에 기본 URL에 추가 됩니다 `Index` 메서드를 호출 합니다. 작업이 지정 된 (또는 기본 설정)에서 참조 되는 컨트롤러에 있어야 `asp-controller`합니다.

- - -
  
<a name="route"></a>
### <a name="asp-route-value"></a>asp-경로-{value}

`asp-route-`와일드 카드 경로 접두사입니다. 모든 값을 추가한 후 뒤에 오는 대시 잠재적 경로 매개 변수로 해석 됩니다. 기본 경로가 없는 경우이 경로 접두사 요청 매개 변수 및 값으로 생성 된 href에 추가 됩니다. 그렇지 않은 경우에 경로 템플릿을 대체 됩니다.

가정 하 고 있는 컨트롤러 메서드가 다음과 같이 정의.

```csharp
public IActionResult AnchorTagHelper(string id)
{
    var speaker = new SpeakerData()
    {
        SpeakerId = 12
    };      
    return View(viewName, speaker);
}
```

에 정의 된 기본 경로 템플릿을 있고 프로그램 *Startup.cs* 다음과 같습니다.

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
});

```

**cshtml** 앵커 태그 도우미를 사용 하는 데 필요한 포함 된 파일의 **스피커** 보기에는 컨트롤러에서 전달 하는 모델 매개 변수는 다음과 같습니다.

```cshtml
@model SpeakerData
<!DOCTYPE html>
<html><body>
<a asp-controller='Speaker' asp-action='Detail' asp-route-id=@Model.SpeakerId>SpeakerId: @Model.SpeakerId</a>
<body></html>
```

생성 된 HTML 때문에 다음과 같이 높아집니다 다음 **id** 기본 경로에서 찾았습니다.

```html
<a href='/Speaker/Detail/12'>SpeakerId: 12</a>
```

경로 접두사 발견 라우팅 서식 파일의 일부가 아닌 경우, 즉 다음과 같은 **cshtml** 파일:

```cshtml
@model SpeakerData
<!DOCTYPE html>
<html><body>
<a asp-controller='Speaker' asp-action='Detail' asp-route-speakerid=@Model.SpeakerId>SpeakerId: @Model.SpeakerId</a>
<body></html>
```

생성 된 HTML 때문에 다음과 같이 높아집니다 다음 **speakerid** 일치 경로에서 찾을 수 없습니다.


```html
<a href='/Speaker/Detail?speakerid=12'>SpeakerId: 12</a>
```

어느 경우 `asp-controller` 또는 `asp-action` 는 지정 하지 않으면 되 고 뒤에 동일한 기본 처리는 `asp-route` 특성입니다.

- - -

### <a name="asp-route"></a>asp 경로

`asp-route`명명 된 경로에 직접 연결 되는 URL을 만들 방법을 제공 합니다. 라우팅 특성을 사용 하는 경로 이름을 지정할 수 있습니다에 표시 된 대로 `SpeakerController` 에서 사용 하 고 해당 `Evaluations` 메서드.

`Name = "speakerevals"`URL을 사용 하 여 해당 컨트롤러 메서드에 대 한 직접 경로 생성 하는 앵커 태그 도우미 지시 `/Speaker/Evaluations`합니다. 경우 `asp-controller` 또는 `asp-action` 외에 지정 `asp-route`, 예상 대로 경로가 생성 되지 않을 수 있습니다. `asp-route`특성 중 하나를 사용할 수 없습니다 `asp-controller` 또는 `asp-action` 경로 충돌 하지 않도록 합니다.

- - -

### <a name="asp-all-route-data"></a>모든 경로 데이터 asp

`asp-all-route-data`키/값 쌍에 키가 매개 변수 이름 및 값은 해당 키와 연결 된 값의 사전을 만들 수 있습니다.

다음 예제와 인라인 사전이 만들어지고 razor 보기에 데이터가 전달 됩니다. 대신 데이터 모델을 사용 하 여 전달할 수도 있습니다.

```cshtml
@{
    var dict =
        new Dictionary<string, string>
        {
            {"speakerId", "11"},
            {"currentYear", "true"}
        };
}
<a asp-route="speakerevalscurrent" 
   asp-all-route-data="dict">SpeakerEvals</a>
```

위의 코드에서는 다음 URL을 생성 합니다: http://localhost/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true

링크를 클릭할 때, 컨트롤러 메서드에 `EvaluationsCurrent` 호출 됩니다. 해당 컨트롤러에서 생성 된 어떤 일치 하는 두 개의 문자열 매개 변수를에 있기 때문에 호출 되는 `asp-all-route-data` 사전입니다.

사전 일치 항목의 키는 매개 변수를 라우팅할 적절 하 게 경로에 해당 값이 대체 됩니다 및 요청 매개 변수도 생성 됩니다. 다른 일치 하지 않는 값입니다.

- - -

### <a name="asp-fragment"></a>asp 조각

`asp-fragment`URL에 추가할 URL 조각을 정의 합니다. 앵커 태그 도우미 해시 문자를 추가 합니다 (#). 태그를 만들면:

```cshtml
<a asp-action="Evaluations" asp-controller="Speaker"  
   asp-fragment="SpeakerEvaluations">About Speaker Evals</a>
```

생성 된 URL 됩니다: http://localhost/Speaker/Evaluations#SpeakerEvaluations

해시 태그는 클라이언트 쪽 응용 프로그램을 작성할 때 유용 합니다. 쉽게 표시 하 고 예를 들어 JavaScript에서 검색에 사용할 수 있습니다.

- - -

### <a name="asp-area"></a>asp 영역

`asp-area`ASP.NET Core 사용 하 여 적절 한 경로 설정 하는 영역 이름을 설정 합니다. 어떻게 영역 특성으로 인해 경로 다시 매핑하여의 예는 다음과 같습니다. 설정 `asp-area` 블로그에 디렉터리 접두사 `Areas/Blogs` 관련된 컨트롤러와이 앵커 태그에 대 한 뷰 경로에 있습니다.

* 프로젝트 이름

  * *wwwroot*

  * *영역*

    * *블로그*

      * *컨트롤러*

        * *HomeController.cs*

      * *레이아웃*

        * *홈*

          * *Index.cshtml*
          
          * *AboutBlog.cshtml*
          
  * *컨트롤러*
  

        
와 같은 유효한 영역 태그 지정 ```area="Blogs"``` 참조 하는 경우는 ```AboutBlog.cshtml``` 파일은 다음과 같습니다 앵커 태그 도우미를 사용 하 여 합니다.

```cshtml
<a asp-action="AboutBlog" asp-controller="Home" asp-area="Blogs">Blogs About</a>
```

생성 된 HTML 영역 세그먼트를 포함 됩니다 및 다음과 같습니다.

```html
<a href="/Blogs/Home/AboutBlog">Blogs About</a>
```

> [!TIP]
> 웹 응용 프로그램에서 작동 하도록 MVC 영역 경로 템플릿이 있는 경우 영역에 대 한 참조가 포함 되어야 합니다. 두 번째 매개 변수는 해당 서식 파일의는 `routes.MapRoute` 메서드 호출으로 표시 됩니다.`template: '"{area:exists}/{controller=Home}/{action=Index}"'`

- - -

### <a name="asp-protocol"></a>asp 프로토콜

`asp-protocol` 프로토콜을 지정 하는 (예: `https`) URL에서 합니다. 앵커 태그 도우미 프로토콜을 포함 하는 예제는 다음과 같이 표시 됩니다.

```<a asp-protocol="https" asp-action="About" asp-controller="Home">About</a>```

및 HTML을 다음과 같이 생성 됩니다.

```<a href="https://localhost/Home/About">About</a>```

예제에서 도메인 localhost, 하지만 앵커 태그 도우미의 URL을 생성 하는 경우 웹 사이트의 공용 도메인 사용 하 여 합니다.

- - -

## <a name="additional-resources"></a>추가 리소스

* [영역](xref:mvc/controllers/areas)
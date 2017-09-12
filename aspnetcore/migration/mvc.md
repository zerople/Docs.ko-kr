---
title: "ASP.NET MVC에서 ASP.NET Core MVC로 마이그레이션"
author: ardalis
description: 
keywords: "ASP.NET MVC에서 코어, 마이그레이션"
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: article
ms.assetid: 3155cc9e-d0c9-424b-886c-35c0ec6f9f4e
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/mvc
ms.openlocfilehash: 2bd689626e867e0ea82fbebdf92447a6029aa35b
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="migrating-from-aspnet-mvc-to-aspnet-core-mvc"></a>ASP.NET MVC에서 ASP.NET Core MVC로 마이그레이션

여 [Rick Anderson](https://twitter.com/RickAndMSFT), [김 Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), 및 [Scott Addie](https://scottaddie.com)

이 문서에서는 마이그레이션하는 ASP.NET MVC 프로젝트를 시작 하는 방법을 보여 줍니다. [ASP.NET Core MVC](../mvc/overview.md)합니다. 프로세스에서 강조 표시 다양 한 ASP.NET MVC에서 변경 된 사항입니다. ASP.NET MVC에서 마이그레이션 프로세스는 여러 단계 이며이 문서에서는 초기 설정, 기본 컨트롤러와 뷰, 정적 콘텐츠 및 클라이언트 쪽 종속성에 설명 합니다. 추가 문서 구성 마이그레이션 및 identity 코드 많은 ASP.NET MVC 프로젝트에 다룹니다.

> [!NOTE]
> 샘플에서 버전 번호는 현재 아닐 수 있습니다. 프로젝트를 적절 하 게 업데이트 해야 할 수 있습니다.

## <a name="create-the-starter-aspnet-mvc-project"></a>스타터 ASP.NET MVC 프로젝트 만들기

업그레이드를 보여 주기 위해 ASP.NET MVC 앱을 만들어 시작 합니다. 만들 이름의 *WebApp1* 되므로 네임 스페이스는 다음 단계에서 생성 하는 ASP.NET Core 프로젝트와 일치 합니다.

![Visual Studio 새 프로젝트 대화 상자](mvc/_static/new-project.png)

![새 웹 응용 프로그램 대화 상자: ASP.NET 템플릿 패널에서 선택한 MVC 프로젝트 템플릿](mvc/_static/new-project-select-mvc-template.png)

*선택 사항:* 에서 솔루션의 이름을 변경 *WebApp1* 를 *m v c 5*합니다. Visual Studio 새 솔루션 이름으로 표시 됩니다 (*m v c 5*)는 쉽게 다음 프로젝트에서이 프로젝트를 알 수 있습니다.

## <a name="create-the-aspnet-core-project"></a>ASP.NET Core 프로젝트 만들기

새 *빈* 이전 프로젝트와 이름이 같은 ASP.NET Core 웹 앱 (*WebApp1*) 되므로 두 프로젝트의 네임 스페이스와 일치 합니다. 동일한 네임 스페이스 하면를 손쉽게 두 프로젝트 간에 코드를 복사할 수 있습니다. 동일한 이름을 사용 하 여 이전 프로젝트 이외의 다른 디렉터리에서이 프로젝트를 만드는 해야 합니다.

![새 프로젝트 대화 상자](mvc/_static/new_core.png)

![새 ASP.NET 웹 응용 프로그램 대화 상자: ASP.NET Core 템플릿 패널에서 선택 하는 빈 프로젝트 템플릿](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *선택 사항:* 사용 하 여 새 ASP.NET Core 응용 프로그램 만들기는 *웹 응용 프로그램* 서식 파일 프로젝트. 프로젝트 이름을 *WebApp1*의 인증 옵션을 선택 하 고 **개별 사용자 계정**합니다. 이 앱을 이름 바꾸기 *FullAspNetCore*합니다. 이 프로젝트에는 시간을 절약할 수 변환을 만드는 것입니다. 최종 결과 확인 하거나 변환 프로젝트에 코드를 복사 하려면 서식 파일에서 생성 된 코드를 살펴볼 수 있습니다. 템플릿에서 생성 된 프로젝트와 비교할 개체 변환 단계에 갇 하는 경우에 도움이 됩니다.

## <a name="configure-the-site-to-use-mvc"></a>MVC를 사용 하도록 사이트 구성

* 설치는 `Microsoft.AspNetCore.Mvc` 및 `Microsoft.AspNetCore.StaticFiles` NuGet 패키지 합니다.

  `Microsoft.AspNetCore.Mvc`ASP.NET Core MVC 프레임 워크가입니다. `Microsoft.AspNetCore.StaticFiles`정적 파일 처리기입니다. ASP.NET 런타임은 모듈식 및 정적 파일을 제공 하려면에 명시적으로 선택 해야 합니다 (참조 [정적 파일 작업](../fundamentals/static-files.md)).

* 열기는 *.csproj* 파일 (에서 프로젝트를 마우스 오른쪽 단추로 클릭 **솔루션 탐색기** 선택 **편집 WebApp1.csproj**) 추가 하 고는 `PrepareForPublish` 대상:

  [!code-xml[Main](mvc/sample/WebApp1.csproj?range=21-23)]

  `PrepareForPublish` 대상 Bower 통한 클라이언트 라이브러리를 획득 하기 위해 필요 합니다. 알아보겠습니다이 대해서는 나중에.

* 열기는 *Startup.cs* 파일을 다음과 일치 하도록 코드를 변경 합니다.

  [!code-csharp[Main](mvc/sample/Startup.cs?highlight=14,27-34)]

  `UseStaticFiles` 확장 메서드는 정적 파일 처리기를 추가 합니다. ASP.NET 런타임이 모듈식 앞에서 설명한 대로 및 정적 파일을 제공 하려면에 명시적으로 선택 해야 합니다. `UseMvc` 라우팅 확장 메서드를 추가 합니다. 자세한 내용은 참조 [응용 프로그램 시작](../fundamentals/startup.md) 및 [라우팅](../fundamentals/routing.md)합니다.

## <a name="add-a-controller-and-view"></a>컨트롤러와 뷰를 추가 합니다.

이 섹션에서는 최소한의 컨트롤러 및 ASP.NET MVC 컨트롤러에 대 한 자리 표시자로 사용 하는 보기 및 보기는 다음 섹션에서 마이그레이션할 수를 추가 합니다.

* 추가 *컨트롤러* 폴더입니다.

* 추가 **MVC 컨트롤러 클래스** 이름의 *HomeController.cs* 에 *컨트롤러* 폴더입니다.

![새 항목 추가 대화 상자](mvc/_static/add_mvc_ctl.png)

* 추가 *뷰* 폴더입니다.

* 추가 *뷰/홈* 폴더입니다.

* 추가 *Index.cshtml* 에 MVC 뷰 페이지는 *뷰/홈* 폴더입니다.

![새 항목 추가 대화 상자](mvc/_static/view.png)

프로젝트 구조는 다음과 같습니다.

![파일 및 폴더의 WebApp1 나타내는 솔루션 탐색기](mvc/_static/project-structure-controller-view.png)

내용을 대체는 *Views/Home/Index.cshtml* 다음 파일:

```html
<h1>Hello world!</h1>
```

응용 프로그램을 실행 합니다.

![웹 응용 프로그램에서 Microsoft Edge 열기](mvc/_static/hello-world.png)

참조 [컨트롤러](../mvc/controllers/index.md) 및 [뷰](../mvc/views/index.md) 자세한 정보에 대 한 합니다.

최소 작업 ASP.NET Core 프로젝트를 만들었으므로 이제 해당 ASP.NET MVC 프로젝트에서 기능을 마이그레이션할 시작할 수 있습니다. 다음 이동 해야 합니다.

* 클라이언트 쪽 콘텐츠 (CSS, 글꼴 및 스크립트)

* 컨트롤러

* 뷰

* 모델

* 번들

* 필터

* In/out 로그, id (이 수행 됩니다 다음 자습서에서.)

## <a name="controllers-and-views"></a>컨트롤러와 뷰

* ASP.NET MVC에서 복사 된 각 메서드의 `HomeController` 새 `HomeController`합니다. ASP.NET MVC에서 기본 제공 템플릿의 컨트롤러 동작 메서드 반환 형식은 [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); ASP.NET Core mvc에서 반환 하는 작업 메서드 `IActionResult` 대신 합니다. `ActionResult`구현 `IActionResult`이므로 동작 메서드의 반환 형식을 변경할 필요가 없습니다.

* 복사는 *About.cshtml*, *Contact.cshtml*, 및 *Index.cshtml* Razor 파일 보기 ASP.NET MVC 프로젝트에서 ASP.NET Core 프로젝트에 있습니다.

* ASP.NET Core 응용 프로그램을 실행 하 고 각 메서드를 테스트 합니다. 하지 않은 마이그레이션할 레이아웃 파일 또는 스타일 아직 있으므로 렌더링 된 뷰 보기 파일의 콘텐츠를만 포함 됩니다. 에 대 한 레이아웃 파일 생성 된 링크를 가질 수 없습니다는 `About` 및 `Contact` 브라우저에서이 호출 하도록 설정 해야 하므로 뷰 (대체 **4492** 프로젝트에서 사용 하는 포트 번호로 바꿉니다).

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![연락처 페이지](mvc/_static/contact-page.png)

Note 스타일 지정 및 메뉴 항목의 부족 합니다. 다음 섹션에서이 문제를 해결 합니다 했습니다.

## <a name="static-content"></a>정적 콘텐츠

이전 버전의 ASP.NET MVC에서는 정적 콘텐츠 웹 프로젝트의 루트에서 호스팅된 및 서버 쪽 파일와 결합 된 되었습니다. ASP.NET Core 정적 콘텐츠가 담긴에서 호스트 되는 *wwwroot* 폴더입니다. 정적 콘텐츠를 이전 ASP.NET MVC 응용 프로그램에서 복사 하려는 *wwwroot* ASP.NET Core 프로젝트의 폴더에에서 있습니다. 이 샘플 구현:

* 복사는 *favicon.ico* 파일에 이전 MVC 프로젝트에서는 *wwwroot* ASP.NET Core 프로젝트의 폴더에에서 있습니다.

ASP.NET MVC 이전 프로젝트에서 사용 [부트스트랩](http://getbootstrap.com/) 부트스트랩에 파일의 스타일 지정 및 저장소는 *콘텐츠* 및 *스크립트* 폴더입니다. 부트스트랩 레이아웃 파일에서 참조 하는 이전 ASP.NET MVC 프로젝트를 생성 하는 템플릿을 (*Views/Shared/_Layout.cshtml*). 복사할 수는 *bootstrap.js* 및 *bootstrap.css* ASP.NET MVC에서 파일에 프로젝트는 *wwwroot* 폴더에 새 프로젝트는이 방법을 사용 하지는 ASP.NET Core의 클라이언트 쪽 종속성을 관리 하기 위한 향상 된 메커니즘입니다.

새 프로젝트를 사용 하 여 부트스트랩 (및 다른 클라이언트 라이브러리)에 대 한 지원을 추가 [Bower](https://bower.io/):

* 추가 [Bower](https://bower.io/) 라는 구성 파일 *bower.json* 프로젝트 루트에 (에서 프로젝트를 마우스 오른쪽 단추로 클릭 한 다음 **추가 > 새 항목 > Bower 구성 파일**). 추가 [부트스트랩](http://getbootstrap.com/) 및 [jQuery](https://jquery.com/) 파일 (아래의 강조 표시 된 줄 참조).

  [!code-json[Main](mvc/sample/bower.json?highlight=5-6)]

파일을 저장할 때 Bower에서는 자동으로 다운로드를 종속성의 *wwwroot/lib* 폴더입니다. 사용할 수는 **솔루션 탐색기 검색** 상자 자산의 경로 찾을 수 있습니다.

![솔루션 탐색기 검색 결과에 표시 된 jquery 자산](mvc/_static/search.png)

참조 [Bower 클라이언트 쪽 패키지 관리](../client-side/bower.md) 자세한 정보에 대 한 합니다.

<a name=migrate-layout-file></a>

## <a name="migrate-the-layout-file"></a>레이아웃 파일 마이그레이션

* 복사는 *_viewstart.vbhtml* 이전 ASP.NET MVC 프로젝트에서 파일 *뷰* ASP.NET Core 프로젝트에 폴더 *뷰* 폴더입니다. *_viewstart.vbhtml* ASP.NET Core MVC에서 파일 변경 되지 않았습니다.

* 만들기는 *뷰/공유* 폴더입니다.

* *선택 사항:* 복사 *_ViewImports.cshtml* 에서 *FullAspNetCore* MVC 프로젝트의 *뷰* ASP.NET Core 프로젝트 폴더*뷰* 폴더입니다. 모든 네임 스페이스 선언을 제거는 *_ViewImports.cshtml* 파일입니다. *_ViewImports.cshtml* 파일 모든 보기 파일에 대 한 네임 스페이스를 제공 하 고는에서 [태그 도우미](../mvc/views/tag-helpers/index.md)합니다. 태그 도우미 새 레이아웃 파일에 사용 됩니다. *_ViewImports.cshtml* ASP.NET Core에 대 한 새 파일을 합니다.

* 복사는 *_Layout.cshtml* 이전 ASP.NET MVC 프로젝트에서 파일 *뷰/공유* ASP.NET Core 프로젝트에 폴더 *뷰/공유* 폴더입니다.

열기 *_Layout.cshtml* 파일을 다음과 같이 변경 (완성 된 코드는 아래 표시):

   * 대체 `@Styles.Render("~/Content/css")` 와 `<link>` 로드할 요소 *bootstrap.css* (아래 참조).

   * 제거 `@Scripts.Render("~/bundles/modernizr")`합니다.

   * 주석으로 처리는 `@Html.Partial("_LoginPartial")` 줄 (한 줄으로 둘러싸고 `@*...*@`). 이후 자습서에서를 반환 합니다.

   * 대체 `@Scripts.Render("~/bundles/jquery")` 와 `<script>` 요소 (아래 참조).

   * 대체 `@Scripts.Render("~/bundles/bootstrap")` 와 `<script>` 요소 (아래 참조).

새 CSS 링크:

```html
<link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
```

대체 스크립트 태그:

```html
<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
```

업데이트 된 *_Layout.cshtml* 파일은 다음과 같습니다.

[!code-html[Main](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7,27,39-40)]

브라우저에서 사이트를 검토 합니다. 원위치에서 예상된 스타일과는 올바르게 로드 이제 해야 합니다.

* *선택 사항:* 새 레이아웃 파일을 사용 하는 것이 좋습니다. 이 프로젝트에 대 한 레이아웃 파일에서 복사할 수 있습니다는 *FullAspNetCore* 프로젝트. 사용 하는 새 레이아웃 파일 [태그 도우미](../mvc/views/tag-helpers/index.md) 있으며 다른 개선 합니다.

## <a name="configure-bundling--minification"></a>구성 묶음 및 축소

묶음 및 축소를 구성 하는 방법에 대 한 정보를 참조 하십시오. [묶음 및 축소](../client-side/bundling-and-minification.md)합니다.

## <a name="solving-http-500-errors"></a>HTTP 500 오류를 해결합니다.

문제의 소스에 없는 정보가 포함 된 HTTP 500 오류 메시지를 발생 시킬 수 있는 많은 문제가 있습니다. 예를 들어 경우는 *Views/_ViewImports.cshtml* 프로젝트에 존재 하지 않는 네임 스페이스를 포함 하는 파일, HTTP 500 오류를 얻게 됩니다. 자세한 오류 메시지를 가져오려면 다음 코드를 추가 합니다.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    if (env.IsDevelopment())
    {
         app.UseDeveloperExceptionPage();
    }

    app.UseStaticFiles();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

참조 **개발자 예외 페이지를 사용 하 여** 에 [의 오류 처리](../fundamentals/error-handling.md) 자세한 정보에 대 한 합니다.

## <a name="additional-resources"></a>추가 리소스

* [클라이언트 쪽 개발](../client-side/index.md)

* [태그 도우미](../mvc/views/tag-helpers/index.md)

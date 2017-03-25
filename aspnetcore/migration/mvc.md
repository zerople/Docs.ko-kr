---
title: "ASP.NET MVC에서 ASP.NET 핵심 MVC로 마이그레이션 | Microsoft 문서"
author: ardalis
description: 
keywords: "ASP.NET MVC, 코어, 마이그레이션"
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: article
ms.assetid: 3155cc9e-d0c9-424b-886c-35c0ec6f9f4e
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/mvc
translationtype: Machine Translation
ms.sourcegitcommit: 0ebb9b63931ccb26126740de08270eda9b1ea486
ms.openlocfilehash: 2743eff6a44b3004454ea9bd9fd027fcf5212613
ms.lasthandoff: 03/23/2017

---
# <a name="migrating-from-aspnet-mvc-to-aspnet-core-mvc"></a>ASP.NET MVC에서 ASP.NET 핵심 MVC로 마이그레이션

여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](http://ardalis.com), 및 [Scott Addie](https://scottaddie.com)

이 문서에서는 마이그레이션하는 ASP.NET MVC 프로젝트를 시작 하는 방법을 보여 줍니다. [ASP.NET 핵심 MVC](../mvc/overview.md)합니다. 프로세스에서 강조 표시 다양 한 ASP.NET MVC에서 변경 된 사항입니다. ASP.NET MVC에서 마이그레이션하는 여러 단계의 프로세스 이며 초기 설정, 기본 컨트롤러 및 뷰, 정적 콘텐츠 및 클라이언트 쪽 종속성이이 문서를 설명 합니다. 마이그레이션 구성 및 많은 ASP.NET MVC 프로젝트 파일에 있는 identity 코드 추가 문서에 설명 합니다.

> [!NOTE]
> 샘플에서 버전 번호는 현재 아닐 수도 있습니다. 그에 따라 프로젝트를 업데이트 해야 합니다.

## <a name="create-the-starter-aspnet-mvc-project"></a>시작 ASP.NET MVC 프로젝트 만들기

업그레이드를 보여 주기 위해 ASP.NET MVC 앱을 만드는 것으로 시작 하겠습니다. 이름으로 만들 *WebApp1* 네임 스페이스는 다음 단계에서 생성 하는 ASP.NET 핵심 프로젝트 일치 됩니다.

![Visual Studio 새 프로젝트 대화 상자](mvc/_static/new-project.png)

![새 웹 응용 프로그램 대화 상자: ASP.NET 템플릿 패널에서 선택한 MVC 프로젝트 템플릿](mvc/_static/new-project-select-mvc-template.png)

*선택 사항:* 에서 솔루션의 이름을 변경 *WebApp1* 를 *Mvc5*합니다. Visual Studio에서 새 솔루션 이름 표시 (*Mvc5*)를 더 쉽게 다음 프로젝트에서이 프로젝트를 알 수 있습니다.

## <a name="create-the-aspnet-core-project"></a>ASP.NET Core 프로젝트 만들기

새 *빈* 이전 프로젝트와 동일한 이름 가진 ASP.NET 핵심 웹 응용 프로그램 (*WebApp1*) 다르므로 두 프로젝트의 네임 스페이스와 일치 합니다. 동일한 네임 스페이스를 사용 하면 두 프로젝트 간에 코드를 복사 하는 보다 쉽게 합니다. 동일한 이름을 사용 하 여 이전 프로젝트와 다른 디렉터리에이 프로젝트를 만들 해야 합니다.

![새 프로젝트 대화 상자](mvc/_static/new_core.png)

![새 ASP.NET 웹 응용 프로그램 대화 상자: ASP.NET 핵심 템플릿을 패널에서 선택 하는 빈 프로젝트 템플릿](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *선택 사항:* 사용 하 여 새 ASP.NET 핵심 응용 프로그램 만들기는 *웹 응용 프로그램* 프로젝트 템플릿. 프로젝트 이름을 *WebApp1*의 인증 옵션을 선택 하 고 **개별 사용자 계정**합니다. 이 앱의 이름을 *FullAspNetCore*합니다. 변환 과정에서이 프로젝트에는 시간을 절약할 수 만드는 중입니다. 최종 결과 확인 하거나 변환 프로젝트에 코드를 복사 하려면 서식 파일에서 생성 된 코드를 살펴볼 수 있습니다. 템플릿에서 생성 된 프로젝트와 비교할 변환 단계에 갇 힐 경우에 유용 합니다.

## <a name="configure-the-site-to-use-mvc"></a>MVC를 사용 하도록 사이트 구성

* 설치는 `Microsoft.AspNetCore.Mvc` 및 `Microsoft.AspNetCore.StaticFiles` NuGet 패키지입니다.

  `Microsoft.AspNetCore.Mvc`ASP.NET Core MVC 프레임 워크가입니다. `Microsoft.AspNetCore.StaticFiles`정적 파일 처리기가입니다. ASP.NET 런타임에서 모듈식 및 정적 파일을 제공 하려면 옵트인 명시적으로 해야 합니다 (참조 [정적 파일 작업](../fundamentals/static-files.md)).

* 열기는 *.csproj* 파일 (에서 프로젝트를 마우스 오른쪽 단추로 클릭 **솔루션 탐색기** 선택한 **편집 WebApp1.csproj**) 추가 하 고는 `PrepareForPublish` 대상:

  [!code-json[주](mvc/sample/WebApp1.csproj?range=22-24)]

  `PrepareForPublish` 대상 Bower 통한 클라이언트 쪽 라이브러리를 확보 하기 위해 필요 합니다. 여기서 조금 뒤에 설명입니다.

* 열기는 *Startup.cs* 파일을 다음과 일치 하도록 코드를 변경 합니다.

  [!code-csharp[주](mvc/sample/Startup.cs?highlight=14,27-34)]

  `UseStaticFiles` 확장 메서드는 정적 파일 처리기를 추가 합니다. 이전에 언급 했 듯이 ASP.NET 런타임이 모듈화, 이며 정적 파일을 제공 하려면 옵트인 명시적으로 해야 합니다. `UseMvc` 라우팅 확장 메서드를 추가 합니다. 자세한 내용은 참조 [응용 프로그램 시작](../fundamentals/startup.md) 및 [라우팅](../fundamentals/routing.md)합니다.

## <a name="add-a-controller-and-view"></a>컨트롤러 및 뷰 추가

이 섹션에서는 최소한의 컨트롤러와 ASP.NET MVC 컨트롤러에 대 한 자리 표시자로 사용 하는 보기 및 다음 섹션에서 마이그레이션할 수는 뷰를 추가 합니다.

* 추가 *컨트롤러* 폴더입니다.

* 추가 **MVC 컨트롤러 클래스** 이름의 *HomeController.cs* 에 *컨트롤러* 폴더입니다.

![새 항목 추가 대화 상자](mvc/_static/add_mvc_ctl.png)

* 추가 *뷰* 폴더입니다.

* 추가 *뷰/홈* 폴더입니다.

* 추가 *Index.cshtml* MVC 뷰 페이지에는 *뷰/홈* 폴더입니다.

![새 항목 추가 대화 상자](mvc/_static/view.png)

프로젝트 구조는 다음과 같습니다.

![파일 및 폴더의 WebApp1 보여 주는 솔루션 탐색기](mvc/_static/project-structure-controller-view.png)

내용을 대체는 *Views/Home/Index.cshtml* 다음 파일:

```html
<h1>Hello world!</h1>
```

응용 프로그램을 실행 합니다.

![Microsoft Edge에 열려 있는 웹 응용 프로그램](mvc/_static/hello-world.png)

참조 [컨트롤러](../mvc/controllers/index.md) 및 [뷰](../mvc/views/index.md) 에 대 한 자세한 내용은 합니다.

최소한의 작업 ASP.NET 핵심 프로젝트 되었으므로, 이제는 ASP.NET MVC 프로젝트에서 기능을 마이그레이션 시작할 수 있습니다. 다음 이동 해야 합니다.

* 클라이언트 쪽 콘텐츠 (예: CSS, 글꼴 및 스크립트)

* 컨트롤러

* 뷰

* 모델

* 묶음

* 필터

* 로그 출력/입력, id (이 수행 됩니다 다음 자습서에서.)

## <a name="controllers-and-views"></a>컨트롤러 및 보기

* ASP.NET MVC에서 복사 된 각 메서드의 `HomeController` 새 `HomeController`합니다. ASP.NET MVC의 기본 제공 된 서식 파일의 컨트롤러 작업 메서드 반환 형식은 [ActionResult](https://msdn.microsoft.com/en-us/library/system.web.mvc.actionresult(v=vs.118).aspx); ASP.NET 핵심 mvc에서 반환 하 여 작업 메서드에 `IActionResult` 대신 합니다. `ActionResult`구현 `IActionResult`이므로 작업 메서드의 반환 형식을 변경할 필요가 없습니다.

* 복사는 *About.cshtml*, *Contact.cshtml*, 및 *Index.cshtml* ASP.NET MVC 프로젝트의 Razor 뷰 파일을 ASP.NET 핵심 프로젝트입니다.

* ASP.NET 핵심 응용 프로그램을 실행 하 고 각 메서드를 테스트 합니다. 아직 마이그레이션할 레이아웃 파일 또는 스타일 아직 렌더링 된 뷰는만 보기 파일의 콘텐츠를 포함 하도록 합니다. 에 대 한 레이아웃 파일 생성 된 링크는 없습니다는 `About` 및 `Contact` 보기, 브라우저에서 호출할 수 있어 (대체 **4492** 프로젝트에 사용 되는 포트 번호로).

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![연락처 페이지](mvc/_static/contact-page.png)

Note 스타일 지정 및 메뉴 항목의 부족 합니다. 다음 섹션에서이 문제를 수정 하겠습니다 했습니다.

## <a name="static-content"></a>정적 콘텐츠

ASP.NET MVC의 이전 버전에서는 정적 콘텐츠 웹 프로젝트의 루트에서 호스팅 되었습니다 및 서버 쪽 파일와 결합 된 되었습니다. ASP.NET Core 정적 콘텐츠에서 호스트 되는 *wwwroot* 폴더입니다. 이전 ASP.NET MVC 앱에서 정적 콘텐츠를 복사 하려는 *wwwroot* ASP.NET 핵심 프로젝트의 폴더에에서 있습니다. 이 샘플 구현 합니다.

* 복사는 *favicon.ico* 이전 MVC 프로젝트에서 파일의 *wwwroot* ASP.NET 핵심 프로젝트 폴더에에서 있습니다.

이전 ASP.NET MVC 프로젝트에서 사용 [부트스트랩](http://getbootstrap.com/) 부트스트랩 파일에 스타일 지정 및 저장소는 *콘텐츠* 및 *스크립트* 폴더입니다. 이전 ASP.NET MVC 프로젝트를 생성 하는 템플릿을 참조 부트스트랩 레이아웃 파일에서 (*Views/Shared/_Layout.cshtml*). 복사할 수는 *bootstrap.js* 및 *bootstrap.css* ASP.NET MVC에서 파일을 프로젝트는 *wwwroot* 폴더에 새 프로젝트를 하지만 방법은 ASP.NET 코어의 클라이언트 쪽 종속성을 관리 하기 위한 향상 된 메커니즘을 사용 하지 않습니다.

새 프로젝트를 사용 하 여 부트스트랩 (및 기타 클라이언트 라이브러리)에 대 한 지원을 추가 [Bower](http://bower.io/):

* 추가 [Bower](http://bower.io/) 라는 구성 파일 *bower.json* 프로젝트 루트에 (에서 프로젝트를 마우스 오른쪽 단추로 클릭 한 다음 **추가 > 새 항목 > Bower 구성 파일**). 추가 [부트스트랩](http://getbootstrap.com/) 및 [jQuery](https://jquery.com/) 파일 (아래의 강조 표시 된 줄 참조).

  [!code-json[주](mvc/sample/bower.json?highlight=5-6)]

파일을 저장 시 Bower는 자동으로 다운로드 종속성을는 *wwwroot/lib* 폴더입니다. 사용할 수는 **솔루션 탐색기 검색** 자산의 경로 찾을 수 있도록 상자:

![솔루션 탐색기 검색 결과에 표시 된 jquery 자산](mvc/_static/search.png)

참조 [Bower와 함께 클라이언트 쪽 패키지 관리](../client-side/bower.md) 에 대 한 자세한 내용은 합니다.

<a name=migrate-layout-file></a>

## <a name="migrate-the-layout-file"></a>레이아웃 파일 마이그레이션

* 복사는 *_ViewStart.cshtml* 이전 ASP.NET MVC 프로젝트에서 파일 *뷰* ASP.NET 핵심 프로젝트의 폴더 *뷰* 폴더입니다. *_ViewStart.cshtml* 파일 ASP.NET 핵심 MVC에서 변경 되지 않았습니다.

* 만들기는 *뷰/공유* 폴더입니다.

* *선택 사항:* 복사 *_ViewImports.cshtml* 에서 *FullAspNetCore* MVC 프로젝트의 *뷰* ASP.NET 핵심 프로젝트의 폴더 *뷰* 폴더입니다. 모든 네임 스페이스 선언을 제거는 *_ViewImports.cshtml* 파일입니다. *_ViewImports.cshtml* 파일의 모든 파일 보기에 대 한 네임 스페이스를 제공 하 고에서 제공 [태그 도우미](../mvc/views/tag-helpers/index.md)합니다. 태그 도우미 새 레이아웃 파일에 사용 됩니다. *_ViewImports.cshtml* ASP.NET 핵심에 대 한 새 파일을 합니다.

* 복사는 *_Layout.cshtml* 이전 ASP.NET MVC 프로젝트에서 파일 *뷰/공유* ASP.NET 핵심 프로젝트의 폴더 *뷰/공유* 폴더입니다.

열기 *_Layout.cshtml* 파일을 다음과 같이 변경 (완성 된 코드는 아래 표시 됨):

   * 대체 `@Styles.Render("~/Content/css")` 와 `<link>` 로드 하는 요소 *bootstrap.css* (아래 참조).

   * 제거 `@Scripts.Render("~/bundles/modernizr")`합니다.

   * 주석으로 처리는 `@Html.Partial("_LoginPartial")` 줄 (줄을 포함 시킬 `@*...*@`). 이후 자습서에서를 반환 합니다.

   * 대체 `@Scripts.Render("~/bundles/jquery")` 와 `<script>` 요소 (아래 참조).

   * 대체 `@Scripts.Render("~/bundles/bootstrap")` 와 `<script>` 요소 (아래 참조).

대체 CSS 링크:

```html
<link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
```

대체 스크립트 태그:

```html
<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
```

업데이트 된 *_Layout.cshtml* 파일은 다음과 같습니다.

[!code-html[주](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7,27,39-40)]

브라우저에서 사이트를 봅니다. 원위치 계측 예상된 스타일과는 올바르게 로드 이제 해야 합니다.

* *선택 사항:* 새 레이아웃 파일을 사용 하는 것이 좋습니다. 이 프로젝트에 대 한 레이아웃 파일을 복사할 수는 있지만 *FullAspNetCore* 프로젝트입니다. 사용 하는 새 레이아웃 파일 [태그 도우미](../mvc/views/tag-helpers/index.md) 있으며 다른 개선 합니다.

## <a name="configure-bundling--minification"></a>묶음 구성 / 축소

묶음 및 축소를 구성 하는 방법에 대 한 정보를 참조 하십시오. [묶음 및 축소](../client-side/bundling-and-minification.md)합니다.

## <a name="solving-http-500-errors"></a>HTTP 500 오류를 해결합니다.

문제의 원인에 없는 정보가 포함 된 HTTP 500 오류 메시지를 발생 시킬 수 있는 많은 문제가 있습니다. 예를 들어 하는 경우는 *Views/_ViewImports.cshtml* 프로젝트에 존재 하지 않는 네임 스페이스를 포함 하는 파일, HTTP 500 오류가 표시 됩니다. 자세한 오류 메시지를 가져오려면 다음 코드를 추가 합니다.

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

참조 **개발자 예외 페이지를 사용 하 여** 에서 [오류 처리](../fundamentals/error-handling.md) 에 대 한 자세한 내용은 합니다.

## <a name="additional-resources"></a>추가 리소스

* [클라이언트 쪽 개발](../client-side/index.md)

* [태그 도우미](../mvc/views/tag-helpers/index.md)


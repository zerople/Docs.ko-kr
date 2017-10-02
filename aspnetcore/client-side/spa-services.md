---
title: "단일 페이지 응용 프로그램을 만들기 위한 JavaScriptServices를 사용 하 여"
author: scottaddie
description: "단일 페이지 응용 프로그램 (SPA) 뒷받침 되며 ASP.NET Core 만들려는 JavaScriptServices 사용의 이점에 알아봅니다."
keywords: "ASP.NET Core 각도, SPA, JavaScriptServices, SpaServices"
ms.author: scaddie
manager: wpickett
ms.date: 08/02/2017
ms.topic: article
ms.assetid: 4b30576b-2718-4c39-9253-a59966747893
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/spa-services
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a93dae3edec73f1b5254aa60662834ca83de62fd
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2017
---
# <a name="using-javascriptservices-for-creating-single-page-applications-with-aspnet-core"></a>ASP.NET Core 사용 단일 페이지 응용 프로그램을 만들기 위한 JavaScriptServices를 사용 하 여

여 [Scott Addie](https://github.com/scottaddie) 및 [Fiyaz Hasan](http://fiyazhasan.me/)

단일 페이지 응용 프로그램 (SPA)에 내재 된 풍부한 사용자 환경이 때문에 웹 응용 프로그램의 인기 있는 형식입니다. 와 같은 클라이언트 쪽 SPA 프레임 워크 또는 라이브러리가, 통합 [각](https://angular.io/) 또는 [반응](https://facebook.github.io/react/), ASP.NET Core 것은 어려울 수와 같은 서버 쪽 프레임 워크입니다. [JavaScriptServices](https://github.com/aspnet/JavaScriptServices) 통합 프로세스에서 충돌을 줄이기 위해 개발 되었습니다. 다른 클라이언트와 서버 기술 스택 간에 원활 하 게 작업 수 있습니다.

[보거나 다운로드 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/spa-services/sample) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample))

<a name="what-is-js-services"></a>

## <a name="what-is-javascriptservices"></a>JavaScriptServices 란?

JavaScriptServices은 ASP.NET Core에 대 한 클라이언트 쪽 기술과의 컬렉션입니다. 목표는 ASP.NET Core SPAs를 구축 하기 위한 개발자의 기본 서버 쪽 플랫폼으로 위치를 지정 합니다.

JavaScriptServices 세 가지 고유한 NuGet 패키지 이루어져 있습니다.
* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)
* [Microsoft.AspNetCore.SpaTemplates](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaTemplates/) (SpaTemplates)

이러한 패키지는 유용한 경우 있습니다.
* 서버에서 JavaScript를 실행 합니다.
* SPA 프레임 워크 또는 라이브러리 사용
* 시스템용을 사용 하 여 클라이언트 쪽 자산 빌드

이 문서에 포커스를 많이 SpaServices 패키지를 사용 하 여에 배치 됩니다.

<a name="what-is-spa-services"></a>

## <a name="what-is-spaservices"></a>SpaServices 란?

ASP.NET Core SPAs를 구축 하기 위한 개발자의 기본 서버 쪽 플랫폼으로 배치 하려면 SpaServices 만들어졌습니다. SpaServices ASP.NET 코어 SPAs 개발할 필요가 없습니다 및 특정 클라이언트 프레임 워크에 고정 하지 않는 것입니다.

SpaServices와 같은 유용한 인프라를 제공 합니다.
* [서버 쪽 사전 렌더링이](#server-prerendering)
* [시스템용 Dev 미들웨어입니다.](#webpack-dev-middleware)
* [핫 모듈 교체](#hot-module-replacement)
* [라우팅 도우미](#routing-helpers)

전체적으로, 이러한 인프라 구성 요소 개발 워크플로 및 런타임 환경을 모두 향상 합니다. 구성 요소를 개별적으로 사용할 수 있습니다.

<a name="spa-services-prereqs"></a>

## <a name="prerequisites-for-using-spaservices"></a>SpaServices 사용 하기 위한 필수 구성 요소

SpaServices를 사용 하려면 다음을 설치 합니다.
* [Node.js](https://nodejs.org/) (버전 6) npm와
    * 이러한 구성 요소 설치 되 고 있습니다를 확인 하려면 명령줄에서 다음을 실행 합니다.

    ```console
    node -v && npm -v
    ```

참고: Azure 웹 사이트를 배포 하는 경우 않아도 합니까 여기 &mdash; Node.js 설치 되어 서버 환경에서 사용할 수 있습니다.

* [.NET core SDK](https://www.microsoft.com/net/download/core) 1.0 (이상)
    * Windows를 사용 하는 경우 설치 Visual Studio 2017을 선택 하 여 **.NET Core 플랫폼 간 개발** 작업 합니다.

* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet 패키지

<a name="server-prerendering"></a>

## <a name="server-side-prerendering"></a>서버 쪽 사전 렌더링이

범용 (해 라고도 함) 응용 프로그램은 JavaScript 응용 프로그램 서버와 클라이언트 둘 다 실행 될 수 있습니다. 각, React, 및 기타 인기 있는 프레임 워크가 응용 프로그램 개발 스타일에 대 한 유니버설 플랫폼을 제공합니다. 개념은 먼저 Node.js 통해 서버에서 프레임 워크 구성 요소를 렌더링 하 고 클라이언트에 실행 한 다음 위임 추가 하는 것입니다.

ASP.NET Core [태그 도우미](xref:mvc/views/tag-helpers/intro) 제공한 SpaServices 서버에서 JavaScript 함수를 호출 하 여 서버 쪽 사전 렌더링이의 구현을 단순화 합니다.

### <a name="prerequisites"></a>필수 구성 요소

다음을 설치합니다.
* [aspnet 사전 렌더링이](https://www.npmjs.com/package/aspnet-prerendering) npm 패키지:

    ```console
    npm i -S aspnet-prerendering
    ```

### <a name="configuration"></a>구성

태그 도우미 프로젝트의 네임 스페이스 등록을 통해 검색 가능한 내용이 *_ViewImports.cshtml* 파일:

[!code-csharp[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

이러한 태그 도우미 Razor 뷰 내에 HTML 형식의 구문을 활용 하 여 하위 수준 Api와 직접 통신 하는 복잡 한을 추상화 합니다.

[!code-html[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="the-asp-prerender-module-tag-helper"></a>`asp-prerender-module` 태그 도우미

`asp-prerender-module` 태그 도우미, 앞의 코드 예제에 사용 된 실행 *ClientApp/dist/main-server.js* Node.js 통해 서버에서 합니다. 확실히 하기 위해서 *main server.js* 파일은 JavaScript를 TypeScript transpilation 작업 아티팩트는 [시스템용](http://webpack.github.io/) 빌드 프로세스입니다. 한 항목이 지점 별칭을 정의 하는 시스템용 `main-server`;에서이 별칭에 대 한 종속성 그래프의 순회를 시작 및는 *ClientApp/부팅-server.ts* 파일:

[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

다음 각 예제에서는 *ClientApp/부팅-server.ts* 파일에서 사용는 `createServerRenderer` 함수 및 `RenderResult` 유형의 `aspnet-prerendering` npm 구성 하려면 패키지 Node.js 통해 서버 렌더링 합니다. 보내는 서버 쪽 렌더링을 강력한 형식의 JavaScript에 겹쳐서 표시 하는 해결 함수 호출에 전달 되는 HTML 태그 `Promise` 개체입니다. `Promise` 개체의 중요 한 이유는 DOM의 자리 표시자 요소에는 삽입에 대 한 페이지에는 HTML 태그를 비동기적으로 제공 합니다.

[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="the-asp-prerender-data-tag-helper"></a>`asp-prerender-data` 태그 도우미

함께 사용 하면는 `asp-prerender-module` 태그 도우미의 `asp-prerender-data` Razor 보기에서 서버 쪽 JavaScript를 컨텍스트 정보를 전달 하 태그 도우미를 사용할 수 있습니다. 다음 태그를 사용자 데이터를 전달 하는 예를 들어는 `main-server` 모듈:

[!code-html[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

받은 `UserName` 인수 기본 제공 JSON 직렬 변환기를 사용 하 여 직렬화 되 고에 저장 되는 `params.data` 개체입니다. 다음 각 예제에서 데이터 내에서 개인 설정 된 인사말을 생성 하는 데 사용 된 `h1` 요소:

[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

참고: 태그 도우미에 전달 된 속성 이름으로 표시 됩니다 **표시 방법이 PascalCase** 표기법입니다. JavaScript에서 동일한 속성 이름으로 표시 됩니다는 여기서 즉 **camelCase**합니다. 기본 JSON serialization 구성은 이러한 차이 담당 합니다.

연장 하 여 이전 코드 예제, 데이터 전달할 수 있습니다는 서버에서 보기에 hydrating 여는 `globals` 속성에 제공 되는 `resolve` 함수:

[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

`postList` 배열 내에 정의 된는 `globals` 개체는 글로벌 브라우저의 연결 된 `window` 개체입니다. 전역 범위에이 변수 호이스팅와 특히 서버에서 한 번를 다시 클라이언트에 동일한 데이터를 로드 하 관련해 서 작업이, 중복을 제거 합니다.

![창 개체에 연결 된 전역 postList 변수](spa-services/_static/global_variable.png)

<a name="webpack-dev-middleware"></a>

## <a name="webpack-dev-middleware"></a>시스템용 Dev 미들웨어입니다.

[시스템용 Dev 미들웨어](https://webpack.github.io/docs/webpack-dev-middleware.html) 에서는 시스템용 필요에 따라 리소스를 작성 하는 그에 따라 효율적인된 개발 워크플로 제공 합니다. 미들웨어가 자동으로 컴파일하고 브라우저에서 페이지를 다시 로드 하는 경우 클라이언트 쪽 리소스를 사용 합니다. 타사 종속성 또는 사용자 지정 코드 변경 될 때 프로젝트의 npm 빌드 스크립트를 통해 시스템용을 수동으로 호출할 하는 대체 방법이입니다. npm에서 스크립트를 작성은 *package.json* 파일은 다음 예제에 표시 됩니다.

[!code-json[Main](../client-side/spa-services/sample/SpaServicesSampleApp/package.json?range=5)]

### <a name="prerequisites"></a>필수 구성 요소

다음을 설치합니다.
* [aspnet 시스템용](https://www.npmjs.com/package/aspnet-webpack) npm 패키지:

    ```console
    npm i -D aspnet-webpack
    ```

### <a name="configuration"></a>구성

시스템용 Dev 미들웨어에 다음 코드를 통해 HTTP 요청 파이프라인에 등록 되 고 *Startup.cs* 파일의 `Configure` 메서드:

[!code-csharp[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=webpack-middleware-registration&highlight=4)]

`UseWebpackDevMiddleware` 하기 전에 확장 메서드를 호출 해야 [정적 파일 호스팅 등록](xref:fundamentals/static-files) 통해는 `UseStaticFiles` 확장 메서드. 보안상의 이유로 응용 프로그램 개발 모드에서 실행 하는 경우에 미들웨어를 등록 합니다.

*webpack.config.js* 파일의 `output.publicPath` 속성이 있으면 조사할 미들웨어는 `dist` 변경에 대 한 폴더:

[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

<a name="hot-module-replacement"></a>

## <a name="hot-module-replacement"></a>핫 모듈 교체

시스템용의 간주할 [핫 모듈 교체](https://webpack.github.io/docs/hot-module-replacement-with-webpack.html) 의 진화 (HMR) 기능 [시스템용 Dev 미들웨어](#webpack-dev-middleware)합니다. HMR는 모두 같은 유용한 기능이 도입 되었지만 더 이상 자동으로 변경 내용을 컴파일한 후 페이지 콘텐츠를 업데이트 하 여 개발 워크플로 간소화 합니다. 현재 메모리 상태와는 SPA의 디버깅 세션을 방해 하 게 하는 브라우저의 새로 고침으로이 혼동 하지 마십시오. 시스템용 Dev 미들웨어 서비스와 변경이 즉 브라우저 간의 라이브 링크가 ~ 단순히 다른 금지 된 단어 ~ 브라우저에 전달 합니다.

### <a name="prerequisites"></a>필수 구성 요소

다음을 설치합니다.
* [시스템용 핫 미들웨어](https://www.npmjs.com/package/webpack-hot-middleware) npm 패키지:

    ```console
    npm i -D webpack-hot-middleware
    ```

### <a name="configuration"></a>구성

HMR 구성 요소에서 MVC의 HTTP 요청 파이프라인으로 등록 되어야 합니다는 `Configure` 메서드:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

마찬가지로 true와 [시스템용 Dev 미들웨어](#webpack-dev-middleware), `UseWebpackDevMiddleware` 하기 전에 확장 메서드를 호출 해야 합니다는 `UseStaticFiles` 확장 메서드. 보안상의 이유로 응용 프로그램 개발 모드에서 실행 하는 경우에 미들웨어를 등록 합니다.

*webpack.config.js* 파일 정의 해야 합니다는 `plugins` 빈 상태일 경우에 배열:

[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

브라우저에서 응용 프로그램을 로드 한 후 개발자 도구 콘솔 탭 HMR 정품 인증에 대 한 확인을 제공 합니다.

![핫 모듈 교체 연결 된 메시지](spa-services/_static/hmr_connected.png)

<a name="routing-helpers"></a>

## <a name="routing-helpers"></a>라우팅 도우미

대부분의 ASP.NET Core 기반 SPAs 서버 쪽 라우팅과 함께 클라이언트 쪽 라우팅 합니다. SPA 및 MVC 라우팅 시스템 방해를 받지 않고 독립적으로 작업할 수 있습니다. 그러나가 문제점을 게시 한 특별 한 경우: 404 HTTP 응답을 식별 합니다.

시나리오를 고려해 야의 확장명은 경로 `/some/page` 사용 됩니다. 해당 패턴 일치는 클라이언트 쪽 경로 않지만 요청 하지 않는 패턴 일치 한 서버 쪽 경로 가정 합니다. 에 대 한 들어오는 요청에 알아보겠습니다 `/images/user-512.png`, 일반적으로 서버에서 이미지 파일을 찾으려고 시도 합니다. 클라이언트 쪽 응용 프로그램에서 처리할 것 있음을 그럴 가능성은 해당 요청 된 리소스 경로는 모든 서버 쪽 경로 또는 정적 파일와 일치 하지 않으면,-404 HTTP 상태 코드를 반환 하려면 일반적으로 합니다.

### <a name="prerequisites"></a>필수 구성 요소

다음을 설치합니다.
* 클라이언트 쪽 라우팅 npm 패키지입니다. 예를 들어 각 사용:

    ```console
    npm i -S @angular/router
    ```

### <a name="configuration"></a>구성

라는 확장 메서드 `MapSpaFallbackRoute` 에 사용 되는 `Configure` 메서드:

[!code-csharp[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=mvc-routing-table&highlight=7-9)]

팁: 경로 구성 하는 순서로 평가 됩니다. 따라서는 `default` 패턴 일치를 위해 이전 코드 예제에서 경로 먼저 사용 합니다.

<a name="new-project-creation"></a>

## <a name="creating-a-new-project"></a>새 프로젝트 만들기

JavaScriptServices 미리 구성 된 응용 프로그램 템플릿을 제공합니다. SpaServices 다양 한 프레임 워크 및 각, Aurelia, Knockout, React, 및 Vue 등의 라이브러리와 함께에서 이러한 서식 파일에 사용 됩니다.

다음 명령을 실행 하 여.NET Core CLI를 통해 이러한 서식 파일을 설치할 수 있습니다.

```console
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

사용 가능한 SPA 템플릿 목록이 표시 됩니다.

| 템플릿                                 | 짧은 이름 | 언어 | Tags        |
|:------------------------------------------|:-----------|:---------|:------------|
| 각도와 MVC ASP.NET Core             | angular    | [C#]     | 웹/MVC/SPA |
| MVC ASP.NET Core Aurelia와             | aurelia    | [C#]     | 웹/MVC/SPA |
| Knockout.js와 MVC ASP.NET Core         | knockout   | [C#]     | 웹/MVC/SPA |
| MVC ASP.NET Core React.js와            | react      | [C#]     | 웹/MVC/SPA |
| MVC ASP.NET Core React.js 및 Redux  | reactredux | [C#]     | 웹/MVC/SPA |
| MVC ASP.NET Core Vue.js와              | vue        | [C#]     | 웹/MVC/SPA | 

SPA 템플릿 중 하나를 사용 하 여 새 프로젝트를 만들려면 포함는 **약식 이름** 에 서식 파일의는 `dotnet new` 명령입니다. 다음 명령은 서버 측에 대해 구성 된 ASP.NET Core MVC와 함께 각 응용 프로그램를 만듭니다.

```console
dotnet new angular
```

<a name="runtime-config-mode"></a>

### <a name="set-the-runtime-configuration-mode"></a>런타임 구성 모드를 설정 합니다.

다음과 같은 두 가지 기본 런타임 구성 모드
* **개발**:
    * 디버깅을 쉽게 수행 하려면 소스 맵이 포함 되어 있습니다.
    * 성능에 대 한 클라이언트 쪽 코드를 최적화 하지 않습니다.
* **프로덕션**:
    * 소스 맵이 제외 됩니다.
    * 묶음 및 축소를 통해 클라이언트 코드를 최적화합니다.

ASP.NET Core 라는 환경 변수를 사용 하 여 `ASPNETCORE_ENVIRONMENT` 구성 모드를 저장할 수 있습니다. 참조  **[환경 설정](xref:fundamentals/environments#setting-the-environment)**  자세한 정보에 대 한 합니다.

### <a name="running-with-net-core-cli"></a>.NET core CLI 실행

프로젝트 루트에서 다음 명령을 실행 하 여 필요한 NuGet 및 npm 패키지를 복원 합니다.

```console
dotnet restore && npm i
```

빌드 및 응용 프로그램을 실행 합니다.

```console
dotnet run
```

응용 프로그램에 따라 로컬 호스트에서 시작 된 [런타임 구성 모드](#runtime-config-mode)합니다. 로 이동 `http://localhost:5000` 브라우저에 방문 페이지를 표시 합니다.

### <a name="running-with-visual-studio-2017"></a>Visual Studio 2017 함께 실행

열기는 *.csproj* 에서 생성 된 파일은 `dotnet new` 명령입니다. 데 필요한 NuGet 및 npm 패키지는 프로젝트를 열고 시 자동으로 복원 됩니다. 이 복원 프로세스 최대 몇 분 정도 걸릴 수 있습니다 및 응용 프로그램은 작업이 완료 될 때 실행 하도록 준비 합니다. 녹색 실행된 단추 또는 키를 눌러 `Ctrl + F5`, 브라우저 응용 프로그램의 방문 페이지에 열립니다. 응용 프로그램에 따라 로컬 호스트에서 실행 되는 [런타임 구성 모드](#runtime-config-mode)합니다. 

<a name="app-testing"></a>

## <a name="testing-the-app"></a>응용 프로그램 테스트

SpaServices 템플릿은 사용 하 여 클라이언트 쪽 테스트를 실행 하는 미리 구성 된 [Karma](https://karma-runner.github.io/1.0/index.html) 및 [Jasmine](https://jasmine.github.io/)합니다. Karma test runner를 해당 테스트에 대 한 반면 jasmine는 인기 있는 단위 테스트 프레임 워크에 JavaScript를입니다. Karma 함께 작동 하도록 구성 되는 [시스템용 Dev 미들웨어](#webpack-dev-middleware) 중지 하 고 변경 내용이 적용 될 때마다 테스트를 실행 하지 않아도 되도록 합니다. 테스트 사례 또는 테스트 사례 자체에 대해 실행 되는 코드 인지 테스트가 자동으로 실행 합니다.

각 응용 프로그램을 사용 하 여 예를 들어, 두 개의 자스민 테스트 사례가 이미 제공에 대 한는 `CounterComponent` 에 *counter.component.spec.ts* 파일:

[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

프로젝트 루트에 명령 프롬프트를 열고 다음 명령을 실행 합니다.

```console
npm test
```

스크립트에 정의 된 설정을 읽는 Karma test runner를 시작 합니다.는 *karma.conf.js* 파일입니다. 다른 설정 보다는 *karma.conf.js* 를 통해 실행할 테스트 파일을 식별 하는 `files` 배열:

[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

<a name="app-publishing"></a>

## <a name="publishing-the-application"></a>응용 프로그램 게시

생성 된 클라이언트 쪽 자산 및 게시 된 ASP.NET Core 아티팩트를 준비 하려면 배포 패키지에 결합은 복잡할 수 있습니다. 다행히도 SpaServices 라는 사용자 지정 MSBuild 대상을 사용 하 여 해당 전체 게시 프로세스를 조정 `RunWebpack`:

[!code-xml[Main](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

MSBuild 대상에는 다음 책임이 있습니다.
1. Npm 패키지를 복원 합니다.
1. 제 3 자, 클라이언트 쪽 자산 프로덕션 수준의 빌드를 만듭니다.
1. 사용자 지정 클라이언트 측 자산 프로덕션 수준의 빌드를 만듭니다.
1. 시스템용에서 생성 된 자산을 게시 폴더에 복사 합니다.

MSBuild 대상 실행 하는 경우 호출 됩니다.

```console
dotnet publish -c Release
```

## <a name="additional-resources"></a>추가 리소스

* [Angular Docs](https://angular.io/docs)

---
title: "묶음 및 축소 ASP.NET 핵심에서 | Microsoft 문서"
author: spboyer
description: 
keywords: "ASP.NET 핵심 묶음 및 축소, CSS, JavaScript, 축소, BuildBundlerMinifier"
ms.author: riande
manager: wpickett
ms.date: 02/28/2017
ms.topic: article
ms.assetid: d54230f9-8e5f-4861-a29c-1d3a14e0b0d9
ms.technology: aspnet
ms.prod: aspnet-core
uid: client-side/bundling-and-minification
translationtype: Machine Translation
ms.sourcegitcommit: 4347a7fba9bbafa44accc9d6df2ac4c760913f2e
ms.openlocfilehash: 7e39fbcf6637206014ecc1f7343bffc89dd3671d
ms.lasthandoff: 03/23/2017

---
# <a name="bundling-and-minification-in-aspnet-core"></a>묶음 및 축소 ASP.NET 코어에서

묶음 및 축소는 두 가지 방법은 웹 응용 프로그램에 대 한 페이지 로드 성능 향상을 위해 ASP.NET에서 사용할 수 있습니다. 묶음 단일 파일에 여러 파일 결합합니다. 다양 한 스크립트 및 CSS를 페이로드가 작을수록 결과를 다른 코드 최적화를 수행 하는 축소 합니다. 함께 사용 되는 묶음 및 축소 부하 시간 여 성능을 향상 시킵니다 서버에 요청 수가 감소 하 고 요청 된 자산 (예: CSS 및 JavaScript 파일)의 크기를 줄이면 됩니다.

이 문서에서는 묶음 및 축소, ASP.NET 핵심 응용 프로그램과 함께 이러한 기능을 사용할 수 있는 방법을 포함 하 여 사용의 이점을 설명 합니다.

## <a name="overview"></a>개요

ASP.NET 핵심 응용 프로그램의 묶음 및 축소에 클라이언트 측 리소스가 사용 되는 방법은 여러 가지가 있습니다. MVC에 대 한 핵심 템플릿을 BuildBundlerMinifier NuGet 패키지와 구성 파일을 사용 하 여 기본적으로 솔루션을 제공 합니다. 와 같은 타사 도구 [Gulp](using-gulp.md) 및 [Grunt](using-grunt.md) 추가 워크플로 또는 복잡 한 프로세스 필요는 동일한 작업을 수행 하는를 사용할 수도 있습니다. 디자인 타임 묶음 및 축소를 사용 하 여 축소 된 파일은 응용 프로그램의 배포 하기 전에 생성 됩니다. 묶음 및 축소에 배포 하기 전에 사용 되는 이점이 서버 부하 감소 합니다. 그러나 해당 디자인 타임 묶음을 인식 해야 하 고 축소 빌드 복잡성을 증가 하 고 정적 파일 에서만 작동 합니다.

묶음 및 축소 주로 첫 번째 페이지 요청 로드 시간을 개선 합니다. 웹 페이지를 요청한 브라우저 (예: JavaScript, CSS 및 이미지) 자산 캐시 되므로 동일한 사이트 동일한 자산을 요청 하거나 묶음 및 축소 같은 페이지를 요청할 때 모든 성능 개선 효과 제공 하지 않습니다. 설정 하지 않으면는 헤더가 아니라 자산에 올바르게에 만료 되 고 묶음 및 축소를 사용 하지 않는, 브라우저의 새로 고침은 추론을 표시 자산 부실 몇 일 후 하 고 브라우저 각 자산에 대 한 유효성 검사 요청 해야 합니다. 이 경우 묶음 및 축소 첫 번째 페이지 요청 후에 성능 향상을 제공합니다.

### <a name="bundling"></a>묶음

묶음는 결합 하거나 단일 파일에 여러 파일을 번들 활용할 수 있는 기능입니다. 단일 파일에 여러 파일 결합 묶음을 하기 때문에 검색 하 고 웹 페이지와 같은 웹 자산을 표시 하는 데 필요한 서버에 요청 수가 줄어듭니다. CSS, JavaScript 및 다른 번들을 만들 수 있습니다. 파일 수를 줄인 응용 프로그램을 제공 하는 서비스 또는 서버에 브라우저에서 HTTP 요청을 의미 합니다. 이 인해 첫 번째 페이지 부하 성능이 향상 되었습니다.

### <a name="minification"></a>축소

다양 한 요청 된 자산 (예: CSS, 이미지, JavaScript 파일)의 크기를 줄이기 위해 서로 다른 코드 최적화를 수행 하는 축소 합니다. 축소의 일반적인 결과는 불필요 한 공백 및 의견을 제거 하 고 한 문자 변수 이름을 짧게 포함 됩니다.

다음 JavaScript 함수를 살펴보세요.

```javascript
AddAltToImg = function (imageTagAndImageID, imageContext) {
  ///<signature>
  ///<summary> Adds an alt tab to the image
  // </summary>
  //<param name="imgElement" type="String">The image selector.</param>
  //<param name="ContextForImage" type="String">The image context.</param>
  ///</signature>
  var imageElement = $(imageTagAndImageID, imageContext);
  imageElement.attr('alt', imageElement.attr('id').replace(/ID/, ''));
}
```

축소 후 함수는 다음과 감소 됩니다.

```javascript
AddAltToImg=function(t,a){var r=$(t,a);r.attr("alt",r.attr("id").replace(/ID/,""))};
```

주석 및 불필요 한 공백을 제거 하는 것 외에도 다음과 같은 매개 변수 및 변수 이름을 바뀌었습니다 (축소) 다음과 같이 합니다.

원래 색 | 이름이 바뀜
--- | :---:
imageTagAndImageID | t
이미지 컨텍스트 | a
imageElement | r

## <a name="impact-of-bundling-and-minification"></a>묶음 및 축소의 영향

다음 표에서 모든 자산을 개별적으로 나열 하 고 간단한 웹 페이지에 묶음 및 축소를 사용 하 여 간의 몇 가지 중요 한 차이점을 보여 줍니다.

작업 | M B/으로 | M B/없이 | 변경
--- | :---: | :---: | :---:
파일 요청 |7 | 18 | 157%
전송 (kb) | 156 | 264.68 | 70%
로드 시간 (밀리초) | 885 | 2360 | 167%

보낸 바이트 브라우저는 요청에 적용 되는 HTTP 헤더와 매우 자세한 정보 표시와 마찬가지로 번들 상당히 감소를 했습니다. 이 예제에서는 로컬로 실행 되지만 로드 시간이 상당히 개선을 보여 줍니다. 자산을 묶음 및 축소를 사용 하 여 네트워크를 통해 전송 될 때 성능에 큰 향상을 가져오게 됩니다.

## <a name="using-bundling-and-minification-in-a-project"></a>묶음 및 축소를 사용 하 여 프로젝트에서

MVC 프로젝트 템플릿 제공는 `bundleconfig.json` 각 번들에 대 한 옵션을 정의 하는 구성 파일입니다. 기본적으로 단일 번들 구성 사용자 지정 JavaScript에 대 한 정의 (`wwwroot/js/site.js`) 및 스타일 시트 (`wwwroot/css/site.css`) 파일입니다.

[!code-json[주](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig.json)]

번들 옵션은 다음과 같습니다.

* outputFileName-출력 번들 파일의 이름입니다. 상대 경로 포함할 수는 `bundleconfig.json` 파일입니다. **필수**
* inputFiles-함께 번들로 묶는 파일의 배열입니다. 이들은 구성 파일에 대 한 상대 경로입니다. **선택적**, * 빈 출력 파일에 빈 값이 발생 합니다. [와일드 카드 사용](http://www.tldp.org/LDP/abs/html/globbingref.html) 패턴이 지원 됩니다.
* 축소-축소는 출력 옵션을 입력 합니다. **선택적**, *-기본`minify: { enabled: true }`*
  * 구성 옵션은 출력 파일 형식을 사용할 수 있습니다.
    * [CSS Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/jsminifier)
    * [HTML Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/htmlminifier)
* includeInProject-프로젝트 파일에 생성 된 파일을 추가 합니다. **선택적**, *기본값-false*
* sourceMaps-해당 번들된 파일에 대 한 소스 맵을 생성 합니다. **선택적**, *기본값-false*

### <a name="visual-studio-2015--2017"></a>Visual Studio 2015 2017 /

열기 `bundleconfig.json` Visual Studio에서 사용자 환경에서 설치 된 확장 하지 않은 경우는 프롬프트가 표시 됩니다가 하나씩이 파일 형식으로 사용할 수 있는 방법을 제안 합니다.

![BuildBundlerMinifier 확장 제안](../client-side/bundling-and-minification/_static/bundler-extension-suggestion.png)

확장 보기를 선택 하 고 설치는 **번들러 / / Minifier** 확장 (Visual Studio 필요한 다시 시작)입니다.

![BuildBundlerMinifier 확장 제안](../client-side/bundling-and-minification/_static/view-extension.png)

다시 시작이 완료 되 면 클라이언트 쪽 자산 묶음 및 축소에 사용 되는 프로세스를 실행 하 여 빌드를 구성 해야 합니다. 마우스 오른쪽 단추로 클릭는 `bundleconfig.json` 파일을 선택 *빌드에 사용 번들 중...*.

프로젝트 빌드 및 `bundleconfig.json` 구성에 따라 출력 파일을 생성 하는 빌드 프로세스에 포함 됩니다.

```console
1>------ Build started: Project: BuildBundlerMinifierExample, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierExample -> C:\BuildBundlerMinifierExample\bin\Debug\netcoreapp1.1\BuildBundlerMinifierExample.dll
========== Build: 1 succeeded or up-to-date, 0 failed, 0 skipped ==========
```

### <a name="visual-studio-code-or-command-line"></a>Visual Studio 코드 또는 명령줄

Visual Studio 및 확장 드라이브 GUI 제스처;를 사용 하 여 묶음 및 축소 프로세스 그러나 같은 기능은 사용할 수는 `dotnet` CLI 및 BuildBundlerMinifier NuGet 패키지입니다.

프로젝트에 NuGet 패키지를 추가 합니다.

```console
dotnet add package BuildBundlerMinifier
```

종속성을 복원 합니다.

```console
dotnet restore
```

응용 프로그램을 빌드하십시오.

```console
dotnet build
```

빌드 명령의 출력에에서는 축소 및/또는 구성 된 기능에 따라 묶음의 결과 보여 줍니다.

```console
Microsoft (R) Build Engine version 15.1.545.13942
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Begin processing bundleconfig.json
     Minified wwwroot/css/site.min.css
  Bundler: Done processing bundleconfig.json
  BuildBundlerMinifierExample -> /BuildBundlerMinifierExample/bin/Debug/netcoreapp1.0/BuildBundlerMinifierExample.dll
```

## <a name="adding-files"></a>파일 추가

이 예제에서는 CSS 파일을 추가로 호출 추가 됩니다 `custom.css` 묶음 및 축소를 위해 구성 하 고 `site.css`단일에서 결과, `site.min.css`합니다.

custom.css

```css
.about, [role=main], [role=complementary]
{
    margin-top: 60px;
}

footer
{
    margin-top: 10px;
}
```

상대 경로를 추가 `bundleconfig.json`합니다.

[!code-json[주](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig2.json)]

> [!NOTE]
> 또는 와일드 카드 사용 패턴을 사용할 수 없습니다- `"inputFiles": ["wwwroot/**/*(*.css|!(*.min.css)"]` 는 모든 css 파일을 가져오고 축소 된 파일 패턴에서 제외 됩니다.

응용 프로그램을 빌드합니다 열면 및 `site.min.css`는 이제 알 수의 내용을 `custom.css` 파일의 끝에 추가 된 합니다.

## <a name="controlling-bundling-and-minification"></a>묶음 및 축소를 제어합니다.

일반적으로 프로덕션 환경에만 앱의 묶이고 파일을 사용 하려면. 개발 하는 동안 앱이 쉽게 디버깅할 수 있도록 원본 파일을 사용 하려면.

스크립트 및 CSS 파일 레이아웃 페이지에서 환경 태그 도우미를 사용 하 여 페이지에 포함 하도록 지정할 수 있습니다 (참조 [태그 도우미](../mvc/views/tag-helpers/index.md)). 환경 태그 도우미는 특정 환경에서 실행 될 때 해당 내용을 렌더링만 됩니다. 참조 [여러 환경 작업](../fundamentals/environments.md) 현재 환경 지정에 대 한 내용은 합니다.

실행할 때 다음과 같은 환경 태그 처리 되지 않은 CSS 파일을 렌더링 합니다는 `Development` 환경:

[!code-html[주](../client-side/bundling-and-minification/samples/WebApplication1/src/WebApplication1/Views/Shared/_Layout.cshtml?highlight=3&range=8-11)]

실행 하는 경우에이 환경 태그 묶이고 CSS 파일을 렌더링 합니다 `Production` 또는 `Staging`:

[!code-html[주](../client-side/bundling-and-minification/samples/WebApplication1/src/WebApplication1/Views/Shared/_Layout.cshtml?highlight=5&range=12-17)]

## <a name="consuming-bundleconfigjson-from-gulp"></a>Gulp에서 bundleconfig.json 사용

이미지 처리, 캐시 busting, CDN assest 처리 등와 같은 추가 프로세스를 해야 하는 응용 프로그램 묶음 및 축소 과정 번들 및 Minify 프로세스 Gulp를 변환할 수 있습니다.

> [!NOTE]
> 변환 옵션 Visual Studio 2015 및 2017 에서만 사용할 수 있습니다.

마우스 오른쪽 단추로 클릭는 `bundleconfig.json` 선택한 **Gulp 변환 중...**. 이 생성 됩니다는 `gulpfile.js` 필요한 npm 패키지를 설치 합니다.

![Gulp 변환](../client-side/bundling-and-minification/_static/convert-togulp.png)

`gulpfile.js` 읽기 생성 되는 `bundleconfig.json` 파일에는 구성에 따라서를 계속 입력/출력 및 설정에 사용할 수 있습니다.

[!code-javascript[주](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/gulpfile.js)]

Visual Studio 2017에서 프로젝트가 빌드될 때 Gulp 있도록 *.csproj 파일에 다음을 추가 합니다.

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
    <Exec Command="gulp min" />
</Target>
```

Visual Studio 2015에서 프로젝트가 빌드될 때 Gulp를 사용 하려면 다음을 추가 `project.json` 파일:

```json
"scripts": {
    "precompile": "gulp min"
}
```

## <a name="additional-resources"></a>추가 리소스

* [Gulp 사용](using-gulp.md)
* [Grunt를 사용 하 여](using-grunt.md)
* [여러 환경 작업](../fundamentals/environments.md)
* [태그 도우미](../mvc/views/tag-helpers/index.md)

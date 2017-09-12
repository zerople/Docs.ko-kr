---
title: "ASP.NET Core에서 Gulp를 사용 하 여"
author: rick-anderson
description: "ASP.NET Core에서 Gulp를 사용 하는 방법을 알아봅니다."
keywords: "ASP.NET Core를 Gulp"
ms.author: riande
manager: wpickett
ms.date: 02/28/2017
ms.topic: article
ms.assetid: 4095d273-bf3f-46cf-bdcc-18cf6815cbad
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/using-gulp
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd617a99ad348c545cbf50f6aa461ca8f456d3a8
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="introduction-to-using-gulp-in-aspnet-core"></a>ASP.NET Core에서 Gulp를 사용 하 여 소개 

여 [Erik Reitan](https://github.com/Erikre), [Scott Addie](https://scottaddie.com), [김 Roth](https://github.com/danroth27), 및 [Shayne 보이 어](https://twitter.com/spboyer)

일반적인 최신 웹 응용 프로그램에서 빌드 프로세스 수: 있습니다.

* 번들 하 고 JavaScript 및 CSS 파일을 축소할 합니다.
* 각 빌드 전에 묶음 및 축소 작업을 호출 하는 도구를 실행 합니다.
* CSS에 덜 컴파일 또는 SASS 파일입니다.
* JavaScript에 CoffeeScript 또는 TypeScript 파일을 컴파일하십시오.

A *task runner가* 이러한 일상적인 개발 작업 및 기타 작업을 자동화 하는 도구입니다. Visual Studio에서는 두 명의 인기 있는 JavaScript 기반 task runner에 대 한 기본 제공 지원: [Gulp](https://gulpjs.com/) 및 [Grunt](using-grunt.md)합니다.

## <a name="gulp"></a>Gulp

Gulp는 JavaScript 기반 스트리밍 빌드 도구 키트 클라이언트 측 코드에 대 한 합니다. 빌드 환경에서 특정 이벤트가 트리거되면 일련의 프로세스를 통해 클라이언트 쪽 파일을 스트리밍하려면 일반적으로 사용 됩니다. Gulp 수 자동화 하는 데 예를 들어, [묶음 및 축소](bundling-and-minification.md) 또는 새 빌드를 하기 전에 개발 환경의 정리 합니다.

Gulp 작업 집합에 정의 된 *gulpfile.js*합니다. 다음 JavaScript Gulp 모듈을 포함 하 고 파일 경로 곧 출시 예정인 작업 내에서 참조할 수를 지정 합니다.

```javascript
/// <binding Clean='clean' />
"use strict";

var gulp = require("gulp"),
  rimraf = require("rimraf"),
  concat = require("gulp-concat"),
  cssmin = require("gulp-cssmin"),
  uglify = require("gulp-uglify");

var paths = {
  webroot: "./wwwroot/"
};

paths.js = paths.webroot + "js/**/*.js";
paths.minJs = paths.webroot + "js/**/*.min.js";
paths.css = paths.webroot + "css/**/*.css";
paths.minCss = paths.webroot + "css/**/*.min.css";
paths.concatJsDest = paths.webroot + "js/site.min.js";
paths.concatCssDest = paths.webroot + "css/site.min.css";
```

위의 코드 지정 되는 노드 모듈 필요 합니다. `require` 함수는 종속 태스크의 기능을 활용할 수 있도록 각 모듈을 가져옵니다. 가져온된 모듈의 각 변수에 할당 합니다. 모듈 이름 또는 경로 중 하나에 있을 수 있습니다. 이 예제에서는 모듈 이름이 `gulp`, `rimraf`, `gulp-concat`, `gulp-cssmin`, 및 `gulp-uglify` 이름별으로 검색 됩니다. 또한, CSS 및 JavaScript 파일의 위치를 다시 사용 하 고 작업 내에서 참조할 수 있도록 경로의 일련이 만들어집니다. 다음 표에서에 포함 된 모듈 설명 *gulpfile.js*합니다.

|모듈 이름|설명|
|---|---|
|gulp|Gulp 스트리밍 빌드 시스템입니다. 자세한 내용은 참조 [gulp](https://www.npmjs.com/package/gulp)합니다.|
|rimraf|노드 삭제 모듈입니다. 자세한 내용은 참조 [rimraf](https://www.npmjs.com/package/rimraf)합니다.|
|gulp concat|운영 체제의 줄 바꿈 문자에 따라 파일을 연결 하는 모듈입니다. 자세한 내용은 참조 [gulp concat](https://www.npmjs.com/package/gulp-concat)합니다.|
|gulp cssmin|CSS 파일을 축소 하는 모듈입니다. 자세한 내용은 참조 [gulp cssmin](https://www.npmjs.com/package/gulp-cssmin)합니다.|
|gulp uglify|축소 하는 모듈 *.js* 파일입니다. 자세한 내용은 참조 [gulp uglify](https://www.npmjs.com/package/gulp-uglify)합니다.|

필수 모듈을 가져온 후 작업을 지정할 수 있습니다. 6 가지 작업 같습니다 등록, 다음 코드에 의해 표현:

```javascript
gulp.task("clean:js", function (cb) {
  rimraf(paths.concatJsDest, cb);
});

gulp.task("clean:css", function (cb) {
  rimraf(paths.concatCssDest, cb);
});

gulp.task("clean", ["clean:js", "clean:css"]);

gulp.task("min:js", function () {
  return gulp.src([paths.js, "!" + paths.minJs], { base: "." })
    .pipe(concat(paths.concatJsDest))
    .pipe(uglify())
    .pipe(gulp.dest("."));
});

gulp.task("min:css", function () {
  return gulp.src([paths.css, "!" + paths.minCss])
    .pipe(concat(paths.concatCssDest))
    .pipe(cssmin())
    .pipe(gulp.dest("."));
});

gulp.task("min", ["min:js", "min:css"]);
```

다음 표에서 위의 코드에 지정 된 작업에 대해 설명 합니다.

|작업 이름|설명|
|--- |--- |
|정리: js|Site.js 파일의 축소 된 버전을 제거 하려면 rimraf 노드 삭제 모듈을 사용 하는 작업입니다.|
|정리: css|Site.css 파일의 축소 된 버전을 제거 하려면 rimraf 노드 삭제 모듈을 사용 하는 작업입니다.|
|정리|호출 하는 태스크는 `clean:js` 이어서 작업은 `clean:css` 작업 합니다.|
|min:js|축소 및 js 폴더 내의 모든.js 파일을 연결 하는 작업입니다. . min.js 파일은 제외 됩니다.|
|min:css|축소 및 css 폴더 내의 모든.css 파일을 연결 하는 작업입니다. . min.css 파일은 제외 됩니다.|
|분|호출 하는 태스크는 `min:js` 이어서 작업은 `min:css` 작업 합니다.|

## <a name="running-default-tasks"></a>기본 작업 실행

새 웹 응용 프로그램을 아직 만들지 않은 경우 Visual Studio에서 새 ASP.NET 웹 응용 프로그램 프로젝트를 만듭니다.

1.  새 JavaScript 파일을 프로젝트에 추가 하 고 이름을 *gulpfile.js*, 다음 코드를 복사 합니다.

    ```javascript
    /// <binding Clean='clean' />
    "use strict";
    
    var gulp = require("gulp"),
      rimraf = require("rimraf"),
      concat = require("gulp-concat"),
      cssmin = require("gulp-cssmin"),
      uglify = require("gulp-uglify");
    
    var paths = {
      webroot: "./wwwroot/"
    };
    
    paths.js = paths.webroot + "js/**/*.js";
    paths.minJs = paths.webroot + "js/**/*.min.js";
    paths.css = paths.webroot + "css/**/*.css";
    paths.minCss = paths.webroot + "css/**/*.min.css";
    paths.concatJsDest = paths.webroot + "js/site.min.js";
    paths.concatCssDest = paths.webroot + "css/site.min.css";
    
    gulp.task("clean:js", function (cb) {
      rimraf(paths.concatJsDest, cb);
    });
    
    gulp.task("clean:css", function (cb) {
      rimraf(paths.concatCssDest, cb);
    });
    
    gulp.task("clean", ["clean:js", "clean:css"]);
    
    gulp.task("min:js", function () {
      return gulp.src([paths.js, "!" + paths.minJs], { base: "." })
        .pipe(concat(paths.concatJsDest))
        .pipe(uglify())
        .pipe(gulp.dest("."));
    });
    
    gulp.task("min:css", function () {
      return gulp.src([paths.css, "!" + paths.minCss])
        .pipe(concat(paths.concatCssDest))
        .pipe(cssmin())
        .pipe(gulp.dest("."));
    });
    
    gulp.task("min", ["min:js", "min:css"]);
    ```

2.  열기는 *package.json* 파일 (추가 하지 않은 경우 있습니다) 다음 줄을 추가 합니다.

    ```json
    {
      "devDependencies": {
        "gulp": "3.9.1",
        "gulp-concat": "2.6.1",
        "gulp-cssmin": "0.1.7",
        "gulp-uglify": "2.0.1",
        "rimraf": "2.6.1"
      }
    }
    ```

3.  **솔루션 탐색기**를 마우스 오른쪽 단추로 클릭 *gulpfile.js*를 선택 하 고 **Task Runner 탐색기**합니다.
    
    ![솔루션 탐색기에서 열기 작업 러너 탐색기](using-gulp/_static/02-SolutionExplorer-TaskRunnerExplorer.png)
    
    **작업 러너 탐색기** Gulp 작업의 목록을 보여 줍니다. (클릭 해야 할 수는 **새로 고침** 프로젝트 이름의 왼쪽에 나타나는 단추입니다.)
    
    ![작업 러너 탐색기](using-gulp/_static/03-TaskRunnerExplorer.png)

4.  아래 **작업** 에 **Task Runner 탐색기**를 마우스 오른쪽 단추로 클릭 **클린**, 선택한 **실행** 팝업 메뉴에서 합니다.

    ![작업 러너 탐색기 정리 작업](using-gulp/_static/04-TaskRunner-clean.png)

    **작업 러너 탐색기** 이라는 새 탭 만들어집니다 **클린** 에 정의 된 대로 clean 작업이 실행 *gulpfile.js*합니다.

5.  마우스 오른쪽 단추로 클릭는 **클린** 의 작업을 선택한 다음 선택 **바인딩** > **하기 전에 빌드**합니다.

    ![작업 러너 탐색기 BeforeBuild 바인딩](using-gulp/_static/05-TaskRunner-BeforeBuild.png)

    **하기 전에 빌드** 바인딩 clean 작업이 각 프로젝트의 빌드 하기 전에 자동으로 실행 되도록 구성 합니다.

바인딩을로 설정 하기 **Task Runner 탐색기** 는 맨 위에 있는 주석 형태로 저장 프로그램 *gulpfile.js* 및 Visual Studio에만 적용 됩니다. Visual Studio 필요 하지 않은 대신 gulp 태스크의 자동 실행을 구성 하는 프로그램 *.csproj* 파일입니다. 예를 들어,이를 배치 하면 *.csproj* 파일:

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
  <Exec Command="gulp clean" />
</Target>
```

사용 하 여 명령 프롬프트 또는 Visual Studio에서 프로젝트를 실행할 때 clean 작업이 실행 되는 이제는 `dotnet run` 명령 (실행 `npm install` 첫 번째).

## <a name="defining-and-running-a-new-task"></a>정의 하 고 새 작업 실행

새 Gulp 작업을 정의 하려면 수정 *gulpfile.js*합니다.

1.  끝에 다음 JavaScript를 추가 *gulpfile.js*:

    ```javascript
    gulp.task("first", function () {
      console.log('first task! <-----');
    });
    ```

    이 태스크의 이름은 `first`, 단순히 문자열을 표시 하 고 있습니다.

2.  저장 *gulpfile.js*합니다.

3.  **솔루션 탐색기**를 마우스 오른쪽 단추로 클릭 *gulpfile.js*를 선택 하 고 *Task Runner 탐색기*합니다.

4.  **Task Runner 탐색기**를 마우스 오른쪽 단추로 클릭 **첫 번째**를 선택 하 고 **실행**합니다.

    ![첫 번째 작업을 실행 하는 task Runner 탐색기](using-gulp/_static/06-TaskRunner-First.png)

    출력 텍스트가 표시 되도록 표시 됩니다. 일반적인 시나리오에 따라 예제에 관심이 Gulp 레시피를 참조 하십시오.

## <a name="defining-and-running-tasks-in-a-series"></a>정의 하 고 일련의 작업을 실행 합니다.

여러 작업을 실행 하면 작업은 기본적으로 동시에 실행 합니다. 그러나 특정 순서에 따라 작업을 실행 해야 하는 경우 지정 해야 각 작업이 완료 되 면,도 다른 작업의 완료에 종속 되는 작업으로 합니다.

1.  일련의 순서로 실행 하는 작업을 정의 하려면 대체는 `first` 작업에서 위에 추가한 *gulpfile.js* 다음:

    ```javascript
    gulp.task("series:first", function () {
      console.log('first task! <-----');
    });
 
    gulp.task("series:second", ["series:first"], function () {
      console.log('second task! <-----');
    });
 
    gulp.task("series", ["series:first", "series:second"], function () {});
    ```
 
    이제 작업 3 가지: `series:first`, `series:second`, 및 `series`합니다. `series:second` 을 실행 하 고 이전에 완료 작업의 배열을 지정 하는 두 번째 매개 변수를 포함 하는 작업은 `series:second` 작업이 실행 됩니다.  만 위의 코드에 지정 된 대로 `series:first` 작업을 먼저 완료 해야 합니다는 `series:second` 작업이 실행 됩니다.

2.  저장 *gulpfile.js*합니다.

3.  **솔루션 탐색기**를 마우스 오른쪽 단추로 클릭 *gulpfile.js* 선택 **Task Runner 탐색기** 아직 열려 있지 않은 경우.

4.  **Task Runner 탐색기**를 마우스 오른쪽 단추로 클릭 **시리즈** 선택 **실행**합니다.

    ![작업 러너 탐색기 시리즈 작업 실행](using-gulp/_static/07-TaskRunner-Series.png)

## <a name="intellisense"></a>IntelliSense

IntelliSense 코드 완성, 매개 변수 설명 및 생산성을 향상 하 고 오류를 줄이기 위해 다른 기능을 제공 합니다. Gulp 작업; JavaScript로 작성 된 따라서 IntelliSense 개발 하는 동안 지원을 제공할 수 있습니다. JavaScript를 사용할 때는 개체, 함수, 속성, IntelliSense에 나열 및 현재 컨텍스트를 기반으로 사용할 수 있는 매개 변수입니다. 코드를 완료 하는 IntelliSense에서 제공 되는 팝업 목록에서 코딩 옵션을 선택 합니다.

![IntelliSense gulp](using-gulp/_static/08-IntelliSense.png)

IntelliSense에 대 한 자세한 내용은 참조 [JavaScript IntelliSense](https://docs.microsoft.com/visualstudio/ide/javascript-intellisense)합니다.

## <a name="development-staging-and-production-environments"></a>개발, 스테이징 및 프로덕션 환경

Gulp를 사용 하 여 스테이징 및 프로덕션에 대 한 클라이언트 파일을 최적화할 수를 처리 하는 파일 스테이징 및 프로덕션 로컬 위치에 저장 됩니다. *_Layout.cshtml* 사용 하 여 파일의 **환경** CSS 파일의 두 가지 버전을 제공 하는 도우미 태그를 지정 합니다. CSS 파일의 한 버전 개발 되며 다른 버전 스테이징 슬롯과 프로덕션에 대 한 최적화 됩니다. Visual Studio 2017 변경한 경우에 **ASPNETCORE_ENVIRONMENT** 환경 변수를 `Production`, Visual Studio에서 웹 응용 프로그램 및 링크를 최소화 하는 CSS 파일을 빌드합니다. 에서는 다음 태그는 **환경** 태그 도우미 링크 태그를 포함 하는 `Development` CSS 파일과 축소 된 `Staging, Production` CSS 파일입니다.

```html
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
    <script src="~/js/site.js" asp-append-version="true"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.2.0.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery"
            crossorigin="anonymous"
            integrity="sha384-K+ctZQ+LL8q6tP7I94W+qzQsfRV2a+AfHIi9k8z8l9ggpc8X+Ytst4yBo/hH+8Fk">
    </script>
    <script src="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/bootstrap.min.js"
            asp-fallback-src="~/lib/bootstrap/dist/js/bootstrap.min.js"
            asp-fallback-test="window.jQuery && window.jQuery.fn && window.jQuery.fn.modal"
            crossorigin="anonymous"
            integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa">
    </script>
    <script src="~/js/site.min.js" asp-append-version="true"></script>
</environment>
```

## <a name="switching-between-environments"></a>환경 간에 전환

를 다른 환경에 대 한 컴파일 사이 전환 하려면 수정 된 **ASPNETCORE_ENVIRONMENT** 환경 변수의 값입니다.

1.  **Task Runner 탐색기**, 되어 있는지 확인은 **min** 작업이 실행 되도록 설정 **하기 전에 빌드**합니다.

2.  **솔루션 탐색기**프로젝트 이름을 마우스 오른쪽 단추로 클릭 하 고 선택 **속성**합니다.

    웹 앱에 대 한 속성 시트가 표시 됩니다.

3.  클릭는 **디버그** 탭 합니다.

4.  값으로 설정 된 **호스팅: 환경** 환경 변수를 `Production`합니다.

5.  키를 눌러 **F5** 브라우저에서 응용 프로그램을 실행 합니다.

6.  브라우저 창에서 페이지를 마우스 오른쪽 단추로 클릭 하 고 선택 **소스 보기** 페이지에 대 한 HTML을 표시 합니다.

    스타일 시트 링크 최소화 된 CSS 파일을 가리키는지 확인 합니다.

7.  웹 앱을 중지 하려면 브라우저를 닫습니다.

8.  Visual Studio에서 웹 앱에 대 한 속성 시트를 반환 하 고 변경 된 **호스팅: 환경** 환경 변수로 다시 `Development`합니다.

9.  키를 눌러 **F5** 브라우저에서 응용 프로그램을 다시 실행 합니다.

10. 브라우저 창에서 페이지를 마우스 오른쪽 단추로 클릭 하 고 선택 **소스 보기** HTML 페이지를 볼 수 있습니다.

    연결 된 스타일 시트는 CSS 파일의 축소 되지 않은 버전은를 가리키는지 확인 합니다.

환경에서 ASP.NET Core와 관련 된 자세한 내용은 참조 하십시오. [여러 환경 작업](../fundamentals/environments.md)합니다.

## <a name="task-and-module-details"></a>작업 및 모듈 세부 정보

Gulp 작업은 함수 이름으로 등록 됩니다.  다른 작업은 현재 작업 보다 먼저 실행 해야 하는 경우 종속성을 지정할 수 있습니다. 추가 함수를 사용 하면 실행 및 조사식 Gulp 작업으로 소스를 설정 하려면 (*src*)와 대상 (*dest*) 수정 하 고 파일의 합니다. 다음은 기본 Gulp API 함수입니다.

|Gulp 함수|구문|설명|
|---   |--- |--- |
|작업  |`gulp.task(name[, deps], fn) { }`|`task` 함수는 작업을 만듭니다. `name` 매개 변수는 작업의 이름을 정의 합니다. `deps` 매개 변수에이 작업이 실행 되기 전에 완료할 작업의 배열을 포함 합니다. `fn` 매개 변수는 작업의 작업 수행 하는 콜백 함수를 나타냅니다.|
|조사식 |`gulp.watch(glob [, opts], tasks) { }`|`watch` 함수는 파일 변경이 발생할 때 파일 및 실행 작업을 모니터링 합니다. `glob` 매개 변수는 한 `string` 또는 `array` 결정 하는 파일 보기에 있습니다. `opts` 매개 변수 추가 파일을 감시 하는 옵션을 제공 합니다.|
|src   |`gulp.src(globs[, options]) { }`|`src` 함수 glob 값이 값은 일치 하는 파일을 제공 합니다. `glob` 매개 변수는 한 `string` 또는 `array` 결정 하는 파일 읽기에 있습니다. `options` 매개 변수 추가 파일 옵션을 제공 합니다.|
|대상  |`gulp.dest(path[, options]) { }`|`dest` 파일을 작성할 수 있는 위치를 정의 하는 함수입니다. `path` 매개 변수는 문자열이 나 대상 폴더를 결정 하는 함수입니다. `options` 매개 변수는 출력 폴더 옵션을 지정 하는 개체입니다.|

추가 Gulp API 참조 정보를 참조 하십시오. [Docs API Gulp](https://github.com/gulpjs/gulp/blob/master/docs/API.md)합니다.

## <a name="gulp-recipes"></a>Gulp 레시피

Gulp 커뮤니티 제공 Gulp [레시피](https://github.com/gulpjs/gulp/blob/master/docs/recipes/README.md)합니다. 이러한 레시피는 일반적인 시나리오를 처리 하도록 Gulp 작업으로 구성 됩니다.

## <a name="additional-resources"></a>추가 리소스

* [Gulp 설명서](https://github.com/gulpjs/gulp/blob/master/docs/README.md)
* [묶음 및 축소에서 ASP.NET Core](bundling-and-minification.md)
* [ASP.NET Core에서 Grunt를 사용 하 여](using-grunt.md)

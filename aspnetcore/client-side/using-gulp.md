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
# <a name="introduction-to-using-gulp-in-aspnet-core"></a><span data-ttu-id="3274b-104">ASP.NET Core에서 Gulp를 사용 하 여 소개</span><span class="sxs-lookup"><span data-stu-id="3274b-104">Introduction to using Gulp in ASP.NET Core</span></span> 

<span data-ttu-id="3274b-105">여 [Erik Reitan](https://github.com/Erikre), [Scott Addie](https://scottaddie.com), [김 Roth](https://github.com/danroth27), 및 [Shayne 보이 어](https://twitter.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="3274b-105">By [Erik Reitan](https://github.com/Erikre), [Scott Addie](https://scottaddie.com), [Daniel Roth](https://github.com/danroth27), and [Shayne Boyer](https://twitter.com/spboyer)</span></span>

<span data-ttu-id="3274b-106">일반적인 최신 웹 응용 프로그램에서 빌드 프로세스 수: 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-106">In a typical modern web application, the build process might:</span></span>

* <span data-ttu-id="3274b-107">번들 하 고 JavaScript 및 CSS 파일을 축소할 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-107">Bundle and minify JavaScript and CSS files.</span></span>
* <span data-ttu-id="3274b-108">각 빌드 전에 묶음 및 축소 작업을 호출 하는 도구를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-108">Run tools to call the bundling and minification tasks before each build.</span></span>
* <span data-ttu-id="3274b-109">CSS에 덜 컴파일 또는 SASS 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-109">Compile LESS or SASS files to CSS.</span></span>
* <span data-ttu-id="3274b-110">JavaScript에 CoffeeScript 또는 TypeScript 파일을 컴파일하십시오.</span><span class="sxs-lookup"><span data-stu-id="3274b-110">Compile CoffeeScript or TypeScript files to JavaScript.</span></span>

<span data-ttu-id="3274b-111">A *task runner가* 이러한 일상적인 개발 작업 및 기타 작업을 자동화 하는 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-111">A *task runner* is a tool which automates these routine development tasks and more.</span></span> <span data-ttu-id="3274b-112">Visual Studio에서는 두 명의 인기 있는 JavaScript 기반 task runner에 대 한 기본 제공 지원: [Gulp](https://gulpjs.com/) 및 [Grunt](using-grunt.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-112">Visual Studio provides built-in support for two popular JavaScript-based task runners: [Gulp](https://gulpjs.com/) and [Grunt](using-grunt.md).</span></span>

## <a name="gulp"></a><span data-ttu-id="3274b-113">Gulp</span><span class="sxs-lookup"><span data-stu-id="3274b-113">Gulp</span></span>

<span data-ttu-id="3274b-114">Gulp는 JavaScript 기반 스트리밍 빌드 도구 키트 클라이언트 측 코드에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-114">Gulp is a JavaScript-based streaming build toolkit for client-side code.</span></span> <span data-ttu-id="3274b-115">빌드 환경에서 특정 이벤트가 트리거되면 일련의 프로세스를 통해 클라이언트 쪽 파일을 스트리밍하려면 일반적으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-115">It is commonly used to stream client-side files through a series of processes when a specific event is triggered in a build environment.</span></span> <span data-ttu-id="3274b-116">Gulp 수 자동화 하는 데 예를 들어, [묶음 및 축소](bundling-and-minification.md) 또는 새 빌드를 하기 전에 개발 환경의 정리 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-116">For instance, Gulp can be used to automate [bundling and minification](bundling-and-minification.md) or the cleansing of a development environment before a new build.</span></span>

<span data-ttu-id="3274b-117">Gulp 작업 집합에 정의 된 *gulpfile.js*합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-117">A set of Gulp tasks is defined in *gulpfile.js*.</span></span> <span data-ttu-id="3274b-118">다음 JavaScript Gulp 모듈을 포함 하 고 파일 경로 곧 출시 예정인 작업 내에서 참조할 수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-118">The following JavaScript includes Gulp modules and specifies file paths to be referenced within the forthcoming tasks:</span></span>

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

<span data-ttu-id="3274b-119">위의 코드 지정 되는 노드 모듈 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-119">The above code specifies which Node modules are required.</span></span> <span data-ttu-id="3274b-120">`require` 함수는 종속 태스크의 기능을 활용할 수 있도록 각 모듈을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-120">The `require` function imports each module so that the dependent tasks can utilize their features.</span></span> <span data-ttu-id="3274b-121">가져온된 모듈의 각 변수에 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-121">Each of the imported modules is assigned to a variable.</span></span> <span data-ttu-id="3274b-122">모듈 이름 또는 경로 중 하나에 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-122">The modules can be located either by name or path.</span></span> <span data-ttu-id="3274b-123">이 예제에서는 모듈 이름이 `gulp`, `rimraf`, `gulp-concat`, `gulp-cssmin`, 및 `gulp-uglify` 이름별으로 검색 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-123">In this example, the modules named `gulp`, `rimraf`, `gulp-concat`, `gulp-cssmin`, and `gulp-uglify` are retrieved by name.</span></span> <span data-ttu-id="3274b-124">또한, CSS 및 JavaScript 파일의 위치를 다시 사용 하 고 작업 내에서 참조할 수 있도록 경로의 일련이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-124">Additionally, a series of paths are created so that the locations of CSS and JavaScript files can be reused and referenced within the tasks.</span></span> <span data-ttu-id="3274b-125">다음 표에서에 포함 된 모듈 설명 *gulpfile.js*합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-125">The following table provides descriptions of the modules included in *gulpfile.js*.</span></span>

|<span data-ttu-id="3274b-126">모듈 이름</span><span class="sxs-lookup"><span data-stu-id="3274b-126">Module Name</span></span>|<span data-ttu-id="3274b-127">설명</span><span class="sxs-lookup"><span data-stu-id="3274b-127">Description</span></span>|
|---|---|
|<span data-ttu-id="3274b-128">gulp</span><span class="sxs-lookup"><span data-stu-id="3274b-128">gulp</span></span>|<span data-ttu-id="3274b-129">Gulp 스트리밍 빌드 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-129">The Gulp streaming build system.</span></span> <span data-ttu-id="3274b-130">자세한 내용은 참조 [gulp](https://www.npmjs.com/package/gulp)합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-130">For more information, see [gulp](https://www.npmjs.com/package/gulp).</span></span>|
|<span data-ttu-id="3274b-131">rimraf</span><span class="sxs-lookup"><span data-stu-id="3274b-131">rimraf</span></span>|<span data-ttu-id="3274b-132">노드 삭제 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-132">A Node deletion module.</span></span> <span data-ttu-id="3274b-133">자세한 내용은 참조 [rimraf](https://www.npmjs.com/package/rimraf)합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-133">For more information, see [rimraf](https://www.npmjs.com/package/rimraf).</span></span>|
|<span data-ttu-id="3274b-134">gulp concat</span><span class="sxs-lookup"><span data-stu-id="3274b-134">gulp-concat</span></span>|<span data-ttu-id="3274b-135">운영 체제의 줄 바꿈 문자에 따라 파일을 연결 하는 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-135">A module that concatenates files based on the operating system’s newline character.</span></span> <span data-ttu-id="3274b-136">자세한 내용은 참조 [gulp concat](https://www.npmjs.com/package/gulp-concat)합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-136">For more information, see [gulp-concat](https://www.npmjs.com/package/gulp-concat).</span></span>|
|<span data-ttu-id="3274b-137">gulp cssmin</span><span class="sxs-lookup"><span data-stu-id="3274b-137">gulp-cssmin</span></span>|<span data-ttu-id="3274b-138">CSS 파일을 축소 하는 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-138">A module that minifies CSS files.</span></span> <span data-ttu-id="3274b-139">자세한 내용은 참조 [gulp cssmin](https://www.npmjs.com/package/gulp-cssmin)합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-139">For more information, see [gulp-cssmin](https://www.npmjs.com/package/gulp-cssmin).</span></span>|
|<span data-ttu-id="3274b-140">gulp uglify</span><span class="sxs-lookup"><span data-stu-id="3274b-140">gulp-uglify</span></span>|<span data-ttu-id="3274b-141">축소 하는 모듈 *.js* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-141">A module that minifies *.js* files.</span></span> <span data-ttu-id="3274b-142">자세한 내용은 참조 [gulp uglify](https://www.npmjs.com/package/gulp-uglify)합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-142">For more information, see [gulp-uglify](https://www.npmjs.com/package/gulp-uglify).</span></span>|

<span data-ttu-id="3274b-143">필수 모듈을 가져온 후 작업을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-143">Once the requisite modules are imported, the tasks can be specified.</span></span> <span data-ttu-id="3274b-144">6 가지 작업 같습니다 등록, 다음 코드에 의해 표현:</span><span class="sxs-lookup"><span data-stu-id="3274b-144">Here there are six tasks registered, represented by the following code:</span></span>

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

<span data-ttu-id="3274b-145">다음 표에서 위의 코드에 지정 된 작업에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-145">The following table provides an explanation of the tasks specified in the code above:</span></span>

|<span data-ttu-id="3274b-146">작업 이름</span><span class="sxs-lookup"><span data-stu-id="3274b-146">Task Name</span></span>|<span data-ttu-id="3274b-147">설명</span><span class="sxs-lookup"><span data-stu-id="3274b-147">Description</span></span>|
|--- |--- |
|<span data-ttu-id="3274b-148">정리: js</span><span class="sxs-lookup"><span data-stu-id="3274b-148">clean:js</span></span>|<span data-ttu-id="3274b-149">Site.js 파일의 축소 된 버전을 제거 하려면 rimraf 노드 삭제 모듈을 사용 하는 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-149">A task that uses the rimraf Node deletion module to remove the minified version of the site.js file.</span></span>|
|<span data-ttu-id="3274b-150">정리: css</span><span class="sxs-lookup"><span data-stu-id="3274b-150">clean:css</span></span>|<span data-ttu-id="3274b-151">Site.css 파일의 축소 된 버전을 제거 하려면 rimraf 노드 삭제 모듈을 사용 하는 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-151">A task that uses the rimraf Node deletion module to remove the minified version of the site.css file.</span></span>|
|<span data-ttu-id="3274b-152">정리</span><span class="sxs-lookup"><span data-stu-id="3274b-152">clean</span></span>|<span data-ttu-id="3274b-153">호출 하는 태스크는 `clean:js` 이어서 작업은 `clean:css` 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-153">A task that calls the `clean:js` task, followed by the `clean:css` task.</span></span>|
|<span data-ttu-id="3274b-154">min:js</span><span class="sxs-lookup"><span data-stu-id="3274b-154">min:js</span></span>|<span data-ttu-id="3274b-155">축소 및 js 폴더 내의 모든.js 파일을 연결 하는 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-155">A task that minifies and concatenates all .js files within the js folder.</span></span> <span data-ttu-id="3274b-156">. min.js 파일은 제외 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-156">The .min.js files are excluded.</span></span>|
|<span data-ttu-id="3274b-157">min:css</span><span class="sxs-lookup"><span data-stu-id="3274b-157">min:css</span></span>|<span data-ttu-id="3274b-158">축소 및 css 폴더 내의 모든.css 파일을 연결 하는 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-158">A task that minifies and concatenates all .css files within the css folder.</span></span> <span data-ttu-id="3274b-159">. min.css 파일은 제외 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-159">The .min.css files are excluded.</span></span>|
|<span data-ttu-id="3274b-160">분</span><span class="sxs-lookup"><span data-stu-id="3274b-160">min</span></span>|<span data-ttu-id="3274b-161">호출 하는 태스크는 `min:js` 이어서 작업은 `min:css` 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-161">A task that calls the `min:js` task, followed by the `min:css` task.</span></span>|

## <a name="running-default-tasks"></a><span data-ttu-id="3274b-162">기본 작업 실행</span><span class="sxs-lookup"><span data-stu-id="3274b-162">Running default tasks</span></span>

<span data-ttu-id="3274b-163">새 웹 응용 프로그램을 아직 만들지 않은 경우 Visual Studio에서 새 ASP.NET 웹 응용 프로그램 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-163">If you haven’t already created a new Web app, create a new ASP.NET Web Application project in Visual Studio.</span></span>

1.  <span data-ttu-id="3274b-164">새 JavaScript 파일을 프로젝트에 추가 하 고 이름을 *gulpfile.js*, 다음 코드를 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-164">Add a new JavaScript file to your project and name it *gulpfile.js*, then copy the following code.</span></span>

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

2.  <span data-ttu-id="3274b-165">열기는 *package.json* 파일 (추가 하지 않은 경우 있습니다) 다음 줄을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-165">Open the *package.json* file (add if not there) and add the following.</span></span>

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

3.  <span data-ttu-id="3274b-166">**솔루션 탐색기**를 마우스 오른쪽 단추로 클릭 *gulpfile.js*를 선택 하 고 **Task Runner 탐색기**합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-166">In **Solution Explorer**, right-click *gulpfile.js*, and select **Task Runner Explorer**.</span></span>
    
    ![솔루션 탐색기에서 열기 작업 러너 탐색기](using-gulp/_static/02-SolutionExplorer-TaskRunnerExplorer.png)
    
    <span data-ttu-id="3274b-168">**작업 러너 탐색기** Gulp 작업의 목록을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-168">**Task Runner Explorer** shows the list of Gulp tasks.</span></span> <span data-ttu-id="3274b-169">(클릭 해야 할 수는 **새로 고침** 프로젝트 이름의 왼쪽에 나타나는 단추입니다.)</span><span class="sxs-lookup"><span data-stu-id="3274b-169">(You might have to click the **Refresh** button that appears to the left of the project name.)</span></span>
    
    ![작업 러너 탐색기](using-gulp/_static/03-TaskRunnerExplorer.png)

4.  <span data-ttu-id="3274b-171">아래 **작업** 에 **Task Runner 탐색기**를 마우스 오른쪽 단추로 클릭 **클린**, 선택한 **실행** 팝업 메뉴에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-171">Underneath **Tasks** in **Task Runner Explorer**, right-click **clean**, and select **Run** from the pop-up menu.</span></span>

    ![작업 러너 탐색기 정리 작업](using-gulp/_static/04-TaskRunner-clean.png)

    <span data-ttu-id="3274b-173">**작업 러너 탐색기** 이라는 새 탭 만들어집니다 **클린** 에 정의 된 대로 clean 작업이 실행 *gulpfile.js*합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-173">**Task Runner Explorer** will create a new tab named **clean** and execute the clean task as it is defined in *gulpfile.js*.</span></span>

5.  <span data-ttu-id="3274b-174">마우스 오른쪽 단추로 클릭는 **클린** 의 작업을 선택한 다음 선택 **바인딩** > **하기 전에 빌드**합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-174">Right-click the **clean** task, then select **Bindings** > **Before Build**.</span></span>

    ![작업 러너 탐색기 BeforeBuild 바인딩](using-gulp/_static/05-TaskRunner-BeforeBuild.png)

    <span data-ttu-id="3274b-176">**하기 전에 빌드** 바인딩 clean 작업이 각 프로젝트의 빌드 하기 전에 자동으로 실행 되도록 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-176">The **Before Build** binding configures the clean task to run automatically before each build of the project.</span></span>

<span data-ttu-id="3274b-177">바인딩을로 설정 하기 **Task Runner 탐색기** 는 맨 위에 있는 주석 형태로 저장 프로그램 *gulpfile.js* 및 Visual Studio에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-177">The bindings you set up with **Task Runner Explorer** are stored in the form of a comment at the top of your *gulpfile.js* and are effective only in Visual Studio.</span></span> <span data-ttu-id="3274b-178">Visual Studio 필요 하지 않은 대신 gulp 태스크의 자동 실행을 구성 하는 프로그램 *.csproj* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-178">An alternative that doesn't require Visual Studio is to configure automatic execution of gulp tasks in your *.csproj* file.</span></span> <span data-ttu-id="3274b-179">예를 들어,이를 배치 하면 *.csproj* 파일:</span><span class="sxs-lookup"><span data-stu-id="3274b-179">For example, put this in your *.csproj* file:</span></span>

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
  <Exec Command="gulp clean" />
</Target>
```

<span data-ttu-id="3274b-180">사용 하 여 명령 프롬프트 또는 Visual Studio에서 프로젝트를 실행할 때 clean 작업이 실행 되는 이제는 `dotnet run` 명령 (실행 `npm install` 첫 번째).</span><span class="sxs-lookup"><span data-stu-id="3274b-180">Now the clean task is executed when you run the project in Visual Studio or from a command prompt using the `dotnet run` command (run `npm install` first).</span></span>

## <a name="defining-and-running-a-new-task"></a><span data-ttu-id="3274b-181">정의 하 고 새 작업 실행</span><span class="sxs-lookup"><span data-stu-id="3274b-181">Defining and running a new task</span></span>

<span data-ttu-id="3274b-182">새 Gulp 작업을 정의 하려면 수정 *gulpfile.js*합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-182">To define a new Gulp task, modify *gulpfile.js*.</span></span>

1.  <span data-ttu-id="3274b-183">끝에 다음 JavaScript를 추가 *gulpfile.js*:</span><span class="sxs-lookup"><span data-stu-id="3274b-183">Add the following JavaScript to the end of *gulpfile.js*:</span></span>

    ```javascript
    gulp.task("first", function () {
      console.log('first task! <-----');
    });
    ```

    <span data-ttu-id="3274b-184">이 태스크의 이름은 `first`, 단순히 문자열을 표시 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-184">This task is named `first`, and it simply displays a string.</span></span>

2.  <span data-ttu-id="3274b-185">저장 *gulpfile.js*합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-185">Save *gulpfile.js*.</span></span>

3.  <span data-ttu-id="3274b-186">**솔루션 탐색기**를 마우스 오른쪽 단추로 클릭 *gulpfile.js*를 선택 하 고 *Task Runner 탐색기*합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-186">In **Solution Explorer**, right-click *gulpfile.js*, and select *Task Runner Explorer*.</span></span>

4.  <span data-ttu-id="3274b-187">**Task Runner 탐색기**를 마우스 오른쪽 단추로 클릭 **첫 번째**를 선택 하 고 **실행**합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-187">In **Task Runner Explorer**, right-click **first**, and select **Run**.</span></span>

    ![첫 번째 작업을 실행 하는 task Runner 탐색기](using-gulp/_static/06-TaskRunner-First.png)

    <span data-ttu-id="3274b-189">출력 텍스트가 표시 되도록 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-189">You’ll see that the output text is displayed.</span></span> <span data-ttu-id="3274b-190">일반적인 시나리오에 따라 예제에 관심이 Gulp 레시피를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="3274b-190">If you are interested in examples based on a common scenario, see Gulp Recipes.</span></span>

## <a name="defining-and-running-tasks-in-a-series"></a><span data-ttu-id="3274b-191">정의 하 고 일련의 작업을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-191">Defining and running tasks in a series</span></span>

<span data-ttu-id="3274b-192">여러 작업을 실행 하면 작업은 기본적으로 동시에 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-192">When you run multiple tasks, the tasks run concurrently by default.</span></span> <span data-ttu-id="3274b-193">그러나 특정 순서에 따라 작업을 실행 해야 하는 경우 지정 해야 각 작업이 완료 되 면,도 다른 작업의 완료에 종속 되는 작업으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-193">However, if you need to run tasks in a specific order, you must specify when each task is complete, as well as which tasks depend on the completion of another task.</span></span>

1.  <span data-ttu-id="3274b-194">일련의 순서로 실행 하는 작업을 정의 하려면 대체는 `first` 작업에서 위에 추가한 *gulpfile.js* 다음:</span><span class="sxs-lookup"><span data-stu-id="3274b-194">To define a series of tasks to run in order, replace the `first` task that you added above in *gulpfile.js* with the following:</span></span>

    ```javascript
    gulp.task("series:first", function () {
      console.log('first task! <-----');
    });
 
    gulp.task("series:second", ["series:first"], function () {
      console.log('second task! <-----');
    });
 
    gulp.task("series", ["series:first", "series:second"], function () {});
    ```
 
    <span data-ttu-id="3274b-195">이제 작업 3 가지: `series:first`, `series:second`, 및 `series`합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-195">You now have three tasks: `series:first`, `series:second`, and `series`.</span></span> <span data-ttu-id="3274b-196">`series:second` 을 실행 하 고 이전에 완료 작업의 배열을 지정 하는 두 번째 매개 변수를 포함 하는 작업은 `series:second` 작업이 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-196">The `series:second` task includes a second parameter which specifies an array of tasks to be run and completed before the `series:second` task will run.</span></span>  <span data-ttu-id="3274b-197">만 위의 코드에 지정 된 대로 `series:first` 작업을 먼저 완료 해야 합니다는 `series:second` 작업이 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-197">As specified in the code above, only the `series:first` task must be completed before the `series:second` task will run.</span></span>

2.  <span data-ttu-id="3274b-198">저장 *gulpfile.js*합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-198">Save *gulpfile.js*.</span></span>

3.  <span data-ttu-id="3274b-199">**솔루션 탐색기**를 마우스 오른쪽 단추로 클릭 *gulpfile.js* 선택 **Task Runner 탐색기** 아직 열려 있지 않은 경우.</span><span class="sxs-lookup"><span data-stu-id="3274b-199">In **Solution Explorer**, right-click *gulpfile.js* and select **Task Runner Explorer** if it isn’t already open.</span></span>

4.  <span data-ttu-id="3274b-200">**Task Runner 탐색기**를 마우스 오른쪽 단추로 클릭 **시리즈** 선택 **실행**합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-200">In **Task Runner Explorer**, right-click **series** and select **Run**.</span></span>

    ![작업 러너 탐색기 시리즈 작업 실행](using-gulp/_static/07-TaskRunner-Series.png)

## <a name="intellisense"></a><span data-ttu-id="3274b-202">IntelliSense</span><span class="sxs-lookup"><span data-stu-id="3274b-202">IntelliSense</span></span>

<span data-ttu-id="3274b-203">IntelliSense 코드 완성, 매개 변수 설명 및 생산성을 향상 하 고 오류를 줄이기 위해 다른 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-203">IntelliSense provides code completion, parameter descriptions, and other features to boost productivity and to decrease errors.</span></span> <span data-ttu-id="3274b-204">Gulp 작업; JavaScript로 작성 된 따라서 IntelliSense 개발 하는 동안 지원을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-204">Gulp tasks are written in JavaScript; therefore, IntelliSense can provide assistance while developing.</span></span> <span data-ttu-id="3274b-205">JavaScript를 사용할 때는 개체, 함수, 속성, IntelliSense에 나열 및 현재 컨텍스트를 기반으로 사용할 수 있는 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-205">As you work with JavaScript, IntelliSense lists the objects, functions, properties, and parameters that are available based on your current context.</span></span> <span data-ttu-id="3274b-206">코드를 완료 하는 IntelliSense에서 제공 되는 팝업 목록에서 코딩 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-206">Select a coding option from the pop-up list provided by IntelliSense to complete the code.</span></span>

![IntelliSense gulp](using-gulp/_static/08-IntelliSense.png)

<span data-ttu-id="3274b-208">IntelliSense에 대 한 자세한 내용은 참조 [JavaScript IntelliSense](https://docs.microsoft.com/visualstudio/ide/javascript-intellisense)합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-208">For more information about IntelliSense, see [JavaScript IntelliSense](https://docs.microsoft.com/visualstudio/ide/javascript-intellisense).</span></span>

## <a name="development-staging-and-production-environments"></a><span data-ttu-id="3274b-209">개발, 스테이징 및 프로덕션 환경</span><span class="sxs-lookup"><span data-stu-id="3274b-209">Development, staging, and production environments</span></span>

<span data-ttu-id="3274b-210">Gulp를 사용 하 여 스테이징 및 프로덕션에 대 한 클라이언트 파일을 최적화할 수를 처리 하는 파일 스테이징 및 프로덕션 로컬 위치에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-210">When Gulp is used to optimize client-side files for staging and production, the processed files are saved to a local staging and production location.</span></span> <span data-ttu-id="3274b-211">*_Layout.cshtml* 사용 하 여 파일의 **환경** CSS 파일의 두 가지 버전을 제공 하는 도우미 태그를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-211">The *_Layout.cshtml* file uses the **environment** tag helper to provide two different versions of CSS files.</span></span> <span data-ttu-id="3274b-212">CSS 파일의 한 버전 개발 되며 다른 버전 스테이징 슬롯과 프로덕션에 대 한 최적화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-212">One version of CSS files is for development and the other version is optimized for both staging and production.</span></span> <span data-ttu-id="3274b-213">Visual Studio 2017 변경한 경우에 **ASPNETCORE_ENVIRONMENT** 환경 변수를 `Production`, Visual Studio에서 웹 응용 프로그램 및 링크를 최소화 하는 CSS 파일을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-213">In Visual Studio 2017, when you change the **ASPNETCORE_ENVIRONMENT** environment variable to `Production`, Visual Studio will build the Web app and link to the minimized CSS files.</span></span> <span data-ttu-id="3274b-214">에서는 다음 태그는 **환경** 태그 도우미 링크 태그를 포함 하는 `Development` CSS 파일과 축소 된 `Staging, Production` CSS 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-214">The following markup shows the **environment** tag helpers containing link tags to the `Development` CSS files and the minified `Staging, Production` CSS files.</span></span>

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

## <a name="switching-between-environments"></a><span data-ttu-id="3274b-215">환경 간에 전환</span><span class="sxs-lookup"><span data-stu-id="3274b-215">Switching between environments</span></span>

<span data-ttu-id="3274b-216">를 다른 환경에 대 한 컴파일 사이 전환 하려면 수정 된 **ASPNETCORE_ENVIRONMENT** 환경 변수의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-216">To switch between compiling for different environments, modify the **ASPNETCORE_ENVIRONMENT** environment variable's value.</span></span>

1.  <span data-ttu-id="3274b-217">**Task Runner 탐색기**, 되어 있는지 확인은 **min** 작업이 실행 되도록 설정 **하기 전에 빌드**합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-217">In **Task Runner Explorer**, verify that the **min** task has been set to run **Before Build**.</span></span>

2.  <span data-ttu-id="3274b-218">**솔루션 탐색기**프로젝트 이름을 마우스 오른쪽 단추로 클릭 하 고 선택 **속성**합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-218">In **Solution Explorer**, right-click the project name and select **Properties**.</span></span>

    <span data-ttu-id="3274b-219">웹 앱에 대 한 속성 시트가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-219">The property sheet for the Web app is displayed.</span></span>

3.  <span data-ttu-id="3274b-220">클릭는 **디버그** 탭 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-220">Click the **Debug** tab.</span></span>

4.  <span data-ttu-id="3274b-221">값으로 설정 된 **호스팅: 환경** 환경 변수를 `Production`합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-221">Set the value of the **Hosting:Environment** environment variable to `Production`.</span></span>

5.  <span data-ttu-id="3274b-222">키를 눌러 **F5** 브라우저에서 응용 프로그램을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-222">Press **F5** to run the application in a browser.</span></span>

6.  <span data-ttu-id="3274b-223">브라우저 창에서 페이지를 마우스 오른쪽 단추로 클릭 하 고 선택 **소스 보기** 페이지에 대 한 HTML을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-223">In the browser window, right-click the page and select **View Source** to view the HTML for the page.</span></span>

    <span data-ttu-id="3274b-224">스타일 시트 링크 최소화 된 CSS 파일을 가리키는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-224">Notice that the stylesheet links point to the minified CSS files.</span></span>

7.  <span data-ttu-id="3274b-225">웹 앱을 중지 하려면 브라우저를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-225">Close the browser to stop the Web app.</span></span>

8.  <span data-ttu-id="3274b-226">Visual Studio에서 웹 앱에 대 한 속성 시트를 반환 하 고 변경 된 **호스팅: 환경** 환경 변수로 다시 `Development`합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-226">In Visual Studio, return to the property sheet for the Web app and change the **Hosting:Environment** environment variable back to `Development`.</span></span>

9.  <span data-ttu-id="3274b-227">키를 눌러 **F5** 브라우저에서 응용 프로그램을 다시 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-227">Press **F5** to run the application in a browser again.</span></span>

10. <span data-ttu-id="3274b-228">브라우저 창에서 페이지를 마우스 오른쪽 단추로 클릭 하 고 선택 **소스 보기** HTML 페이지를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-228">In the browser window, right-click the page and select **View Source** to see the HTML for the page.</span></span>

    <span data-ttu-id="3274b-229">연결 된 스타일 시트는 CSS 파일의 축소 되지 않은 버전은를 가리키는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-229">Notice that the stylesheet links point to the unminified versions of the CSS files.</span></span>

<span data-ttu-id="3274b-230">환경에서 ASP.NET Core와 관련 된 자세한 내용은 참조 하십시오. [여러 환경 작업](../fundamentals/environments.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-230">For more information related to environments in ASP.NET Core, see [Working with Multiple Environments](../fundamentals/environments.md).</span></span>

## <a name="task-and-module-details"></a><span data-ttu-id="3274b-231">작업 및 모듈 세부 정보</span><span class="sxs-lookup"><span data-stu-id="3274b-231">Task and module details</span></span>

<span data-ttu-id="3274b-232">Gulp 작업은 함수 이름으로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-232">A Gulp task is registered with a function name.</span></span>  <span data-ttu-id="3274b-233">다른 작업은 현재 작업 보다 먼저 실행 해야 하는 경우 종속성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-233">You can specify dependencies if other tasks must run before the current task.</span></span> <span data-ttu-id="3274b-234">추가 함수를 사용 하면 실행 및 조사식 Gulp 작업으로 소스를 설정 하려면 (*src*)와 대상 (*dest*) 수정 하 고 파일의 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-234">Additional functions allow you to run and watch the Gulp tasks, as well as set the source (*src*) and destination (*dest*) of the files being modified.</span></span> <span data-ttu-id="3274b-235">다음은 기본 Gulp API 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-235">The following are the primary Gulp API functions:</span></span>

|<span data-ttu-id="3274b-236">Gulp 함수</span><span class="sxs-lookup"><span data-stu-id="3274b-236">Gulp Function</span></span>|<span data-ttu-id="3274b-237">구문</span><span class="sxs-lookup"><span data-stu-id="3274b-237">Syntax</span></span>|<span data-ttu-id="3274b-238">설명</span><span class="sxs-lookup"><span data-stu-id="3274b-238">Description</span></span>|
|---   |--- |--- |
|<span data-ttu-id="3274b-239">작업</span><span class="sxs-lookup"><span data-stu-id="3274b-239">task</span></span>  |`gulp.task(name[, deps], fn) { }`|<span data-ttu-id="3274b-240">`task` 함수는 작업을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-240">The `task` function creates a task.</span></span> <span data-ttu-id="3274b-241">`name` 매개 변수는 작업의 이름을 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-241">The `name` parameter defines the name of the task.</span></span> <span data-ttu-id="3274b-242">`deps` 매개 변수에이 작업이 실행 되기 전에 완료할 작업의 배열을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-242">The `deps` parameter contains an array of tasks to be completed before this task runs.</span></span> <span data-ttu-id="3274b-243">`fn` 매개 변수는 작업의 작업 수행 하는 콜백 함수를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-243">The `fn` parameter represents a callback function which performs the operations of the task.</span></span>|
|<span data-ttu-id="3274b-244">조사식</span><span class="sxs-lookup"><span data-stu-id="3274b-244">watch</span></span> |`gulp.watch(glob [, opts], tasks) { }`|<span data-ttu-id="3274b-245">`watch` 함수는 파일 변경이 발생할 때 파일 및 실행 작업을 모니터링 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-245">The `watch` function monitors files and runs tasks when a file change occurs.</span></span> <span data-ttu-id="3274b-246">`glob` 매개 변수는 한 `string` 또는 `array` 결정 하는 파일 보기에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-246">The `glob` parameter is a `string` or `array` that determines which files to watch.</span></span> <span data-ttu-id="3274b-247">`opts` 매개 변수 추가 파일을 감시 하는 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-247">The `opts` parameter provides additional file watching options.</span></span>|
|<span data-ttu-id="3274b-248">src</span><span class="sxs-lookup"><span data-stu-id="3274b-248">src</span></span>   |`gulp.src(globs[, options]) { }`|<span data-ttu-id="3274b-249">`src` 함수 glob 값이 값은 일치 하는 파일을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-249">The `src` function provides files that match the glob value(s).</span></span> <span data-ttu-id="3274b-250">`glob` 매개 변수는 한 `string` 또는 `array` 결정 하는 파일 읽기에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-250">The `glob` parameter is a `string` or `array` that determines which files to read.</span></span> <span data-ttu-id="3274b-251">`options` 매개 변수 추가 파일 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-251">The `options` parameter provides additional file options.</span></span>|
|<span data-ttu-id="3274b-252">대상</span><span class="sxs-lookup"><span data-stu-id="3274b-252">dest</span></span>  |`gulp.dest(path[, options]) { }`|<span data-ttu-id="3274b-253">`dest` 파일을 작성할 수 있는 위치를 정의 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-253">The `dest` function defines a location to which files can be written.</span></span> <span data-ttu-id="3274b-254">`path` 매개 변수는 문자열이 나 대상 폴더를 결정 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-254">The `path` parameter is a string or function that determines the destination folder.</span></span> <span data-ttu-id="3274b-255">`options` 매개 변수는 출력 폴더 옵션을 지정 하는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-255">The `options` parameter is an object that specifies output folder options.</span></span>|

<span data-ttu-id="3274b-256">추가 Gulp API 참조 정보를 참조 하십시오. [Docs API Gulp](https://github.com/gulpjs/gulp/blob/master/docs/API.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-256">For additional Gulp API reference information, see [Gulp Docs API](https://github.com/gulpjs/gulp/blob/master/docs/API.md).</span></span>

## <a name="gulp-recipes"></a><span data-ttu-id="3274b-257">Gulp 레시피</span><span class="sxs-lookup"><span data-stu-id="3274b-257">Gulp recipes</span></span>

<span data-ttu-id="3274b-258">Gulp 커뮤니티 제공 Gulp [레시피](https://github.com/gulpjs/gulp/blob/master/docs/recipes/README.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-258">The Gulp community provides Gulp [recipes](https://github.com/gulpjs/gulp/blob/master/docs/recipes/README.md).</span></span> <span data-ttu-id="3274b-259">이러한 레시피는 일반적인 시나리오를 처리 하도록 Gulp 작업으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3274b-259">These recipes consist of Gulp tasks to address common scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3274b-260">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="3274b-260">Additional resources</span></span>

* [<span data-ttu-id="3274b-261">Gulp 설명서</span><span class="sxs-lookup"><span data-stu-id="3274b-261">Gulp documentation</span></span>](https://github.com/gulpjs/gulp/blob/master/docs/README.md)
* [<span data-ttu-id="3274b-262">묶음 및 축소에서 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3274b-262">Bundling and minification in ASP.NET Core</span></span>](bundling-and-minification.md)
* [<span data-ttu-id="3274b-263">ASP.NET Core에서 Grunt를 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="3274b-263">Using Grunt in ASP.NET Core</span></span>](using-grunt.md)

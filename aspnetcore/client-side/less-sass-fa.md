---
title: "작은 Sass, 및 ASP.NET 코어의 놀라운 글꼴"
author: ardalis
description: "ASP.NET Core 응용 프로그램의 작은 Sass, 한 글꼴을 사용 하는 방법에 알아봅니다."
keywords: "ASP.NET Core Sass, 글꼴 놀라운, 전처리기 덜"
ms.author: tdykstra
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 94c988f9-95fd-425d-b37e-7f846598c6d4
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/less-sass-fa
ms.openlocfilehash: 159377300d33e98393fd6705d0fec578f8f6b735
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-styling-applications-with-less-sass-and-font-awesome-in-aspnet-core"></a><span data-ttu-id="942a4-104">더 적은 노력으로 응용 프로그램 스타일 지정, Sass, 및 ASP.NET 코어에서 글꼴 놀라운 소개</span><span class="sxs-lookup"><span data-stu-id="942a4-104">Introduction to styling applications with Less, Sass, and Font Awesome in ASP.NET Core</span></span>

<span data-ttu-id="942a4-105">으로 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="942a4-105">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="942a4-106">전반적인 환경 및 스타일 지정에 관한는 웹 응용 프로그램의 사용자는 점점 더 높은 기대 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-106">Users of web applications have increasingly high expectations when it comes to style and overall experience.</span></span> <span data-ttu-id="942a4-107">최신 웹 응용 프로그램에는 자주 풍부한 도구 및 프레임 워크 정의 하 고의 모양과 느낌을 일관 된 방식에서 관리에 대 한 활용 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-107">Modern web applications frequently leverage rich tools and frameworks for defining and managing their look and feel in a consistent manner.</span></span> <span data-ttu-id="942a4-108">같은 프레임 워크 [부트스트랩](http://getbootstrap.com/) 스타일 및 웹 사이트에 대 한 레이아웃 옵션의 공통 집합을 정의 하기 위한 이동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-108">Frameworks like [Bootstrap](http://getbootstrap.com/) can go a long way toward defining a common set of styles and layout options for web sites.</span></span> <span data-ttu-id="942a4-109">그러나 대부분의 특수 사이트 혜택을 받을 사이트의 인터페이스를 더 적합 하 게 해 주는 이미지가 아닌 아이콘에 쉽게 액세스할 수 있을 뿐만 아니라 효과적으로 정의 하 고 스타일 및 연계 스타일 시트 (CSS) 파일을 유지 하기 위해서는.</span><span class="sxs-lookup"><span data-stu-id="942a4-109">However, most non-trivial sites also benefit from being able to effectively define and maintain styles and cascading style sheet (CSS) files, as well as having easy access to non-image icons that help make the site's interface more intuitive.</span></span> <span data-ttu-id="942a4-110">정답입니다 언어와 도구를 지 원하는 [적은](http://lesscss.org/) 및 [Sass](http://sass-lang.com/), 라이브러리와 같은 및 [글꼴 놀라운](http://fontawesome.io/),와 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-110">That's where languages and tools that support [Less](http://lesscss.org/) and [Sass](http://sass-lang.com/), and libraries like [Font Awesome](http://fontawesome.io/), come in.</span></span>

## <a name="css-preprocessor-languages"></a><span data-ttu-id="942a4-111">CSS 전처리기 언어</span><span class="sxs-lookup"><span data-stu-id="942a4-111">CSS preprocessor languages</span></span>

<span data-ttu-id="942a4-112">기본 언어를 사용 하는 환경을 개선 하기 위해 다른 언어로 컴파일되는 언어는 전처리기 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-112">Languages that are compiled into other languages, in order to improve the experience of working with the underlying language, are referred to as preprocessors.</span></span> <span data-ttu-id="942a4-113">CSS에 대 한 두 개의 인기 있는 전처리기는: 덜 및 Sass 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-113">There are two popular preprocessors for CSS: Less and Sass.</span></span>  <span data-ttu-id="942a4-114">이러한 전처리기 기능 변수 및 대규모의 복잡 한 스타일 시트의 용이해 지 며 중첩 된 규칙에 대 한 지원 등의 CSS을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-114">These preprocessors add features to CSS, such as support for variables and nested rules, which improve the maintainability of large, complex stylesheets.</span></span> <span data-ttu-id="942a4-115">변수를 같은 간단한에 대해서도 지원 부족 한 언어로 CSS은 매우 기본적인이를 부분과 CSS 파일 반복적이 고 그 결과입니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-115">CSS as a language is very basic, lacking support even for something as simple as variables, and this tends to make CSS files repetitive and bloated.</span></span> <span data-ttu-id="942a4-116">전처리기를 통해 실제 프로그래밍 언어의 기능을 추가 하면 더 나은 조직 스타일 지정 규칙을 제공 하 고 중복을 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-116">Adding real programming language features via preprocessors can help reduce duplication and provide better organization of styling rules.</span></span> <span data-ttu-id="942a4-117">Visual Studio 둘 다 없는 대 한 기본 제공 지원 및 Sass, 뿐 아니라 이러한 언어를 작업할 때 개발 환경을 더욱 개선할 수 있는 확장을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-117">Visual Studio provides built-in support for both Less and Sass, as well as extensions that can further improve the development experience when working with these languages.</span></span>

<span data-ttu-id="942a4-118">이 CSS 스타일 정보가 가독성과 전처리기 개선할 수 있는 방법을의 간단한 예를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="942a4-118">As a quick example of how preprocessors can improve readability and maintainability of style information, consider this CSS:</span></span>

```css
.header {
    color: black;
    font-weight: bold;
    font-size: 18px;
    font-family: Helvetica, Arial, sans-serif;
}

.small-header {
    color: black;
    font-weight: bold;
    font-size: 14px;
    font-family: Helvetica, Arial, sans-serif;
}
```

<span data-ttu-id="942a4-119">작은 사용 하 여,이 다시 작성할 수 있습니다 모든, 복제 제거를 사용 하 여는 *mixin* ("혼합" 수 있기 때문에 그렇게 명명 된 클래스 또는 규칙 집합을 다른 속성):</span><span class="sxs-lookup"><span data-stu-id="942a4-119">Using Less, this can be rewritten to eliminate all of the duplication, using a *mixin* (so named because it allows you to "mix in" properties from one class or rule-set into another):</span></span>

```less
.header {
    color: black;
    font-weight: bold;
    font-size: 18px;
    font-family: Helvetica, Arial, sans-serif;
}

.small-header {
    .header;
    font-size: 14px;
}
```

## <a name="less"></a><span data-ttu-id="942a4-120">덜</span><span class="sxs-lookup"><span data-stu-id="942a4-120">Less</span></span>

<span data-ttu-id="942a4-121">전처리기는 덜 CSS Node.js를 사용 하 여 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-121">The Less CSS preprocessor runs using Node.js.</span></span> <span data-ttu-id="942a4-122">작은 설치 하려면 노드 패키지 관리자 (npm) 명령 프롬프트에서 사용 하 여 (-g 의미 "전역"):</span><span class="sxs-lookup"><span data-stu-id="942a4-122">To install Less, use Node Package Manager (npm) from a command prompt (-g means "global"):</span></span>

```console
npm install -g less
```

<span data-ttu-id="942a4-123">Visual Studio를 사용 하 여 시작할 수 있습니다 하나 이상의 Less 파일을 프로젝트에 추가 하 고 다음 컴파일 타임에 변수를 처리할 Gulp (또는 Grunt)를 구성 하 여 미만 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-123">If you're using Visual Studio, you can get started with Less by adding one or more Less files to your project, and then configuring Gulp (or Grunt) to process them at compile-time.</span></span> <span data-ttu-id="942a4-124">추가 *스타일* 폴더를 프로젝트에 새 덜 라는 파일을 추가 하 고 *main.less* 이 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-124">Add a *Styles* folder to your project, and then add a new Less file named *main.less* to this folder.</span></span>

![덜 파일 추가](less-sass-fa/_static/add-less-file.png)

<span data-ttu-id="942a4-126">추가 되 면 폴더 구조 코드는 다음과 같아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-126">Once added, your folder structure should look something like this:</span></span>

![폴더 구조](less-sass-fa/_static/folder-structure.png)

<span data-ttu-id="942a4-128">이제 몇 가지 기본 스타일 지정 CSS로 컴파일되고 Gulp 여 wwwroot 폴더에 배포 되는 파일을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-128">Now you can add some basic styling to the file, which will be compiled into CSS and deployed to the wwwroot folder by Gulp.</span></span>

<span data-ttu-id="942a4-129">수정 *main.less* 한 기본 색에서 간단한 색상표를 만드는 다음 콘텐츠를 포함 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-129">Modify *main.less* to include the following content, which creates a simple color palette from a single base color.</span></span>

```less
@base: #663333;
@background: spin(@base, 180);
@lighter: lighten(spin(@base, 5), 10%);
@lighter2: lighten(spin(@base, 10), 20%);
@darker: darken(spin(@base, -5), 10%);
@darker2: darken(spin(@base, -10), 20%);

body {
    background-color:@background;
}
.baseColor  {color:@base}
.bgLight    {color:@lighter}
.bgLight2   {color:@lighter2}
.bgDark     {color:@darker}
.bgDark2    {color:@darker2}
```

<span data-ttu-id="942a4-130">`@base`다른 @-prefixed 항목은 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-130">`@base` and the other @-prefixed items are variables.</span></span> <span data-ttu-id="942a4-131">각각의 색을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-131">Each of them represents a color.</span></span> <span data-ttu-id="942a4-132">제외 하 고 `@base`, 색 함수를 사용 하 여 설정 된: 밝게, 어둡게, 및 회전 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-132">Except for `@base`, they are set using color functions: lighten, darken, and spin.</span></span> <span data-ttu-id="942a4-133">밝게 및 어둡게 거의 예상 대로; 수행 스핀 (색상표) 중심으로 다양 한 색의 색상을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-133">Lighten and darken do pretty much what you would expect; spin adjusts the hue of a color by a number of degrees (around the color wheel).</span></span> <span data-ttu-id="942a4-134">작은 프로세서는 때문에 이러한 변수 작동 하는 방법을 설명 하기 위해 어딘가에 사용법이 사용 되지 않는 변수를 무시 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-134">The Less processor is smart enough to ignore variables that aren't used, so to demonstrate how these variables work, we need to use them somewhere.</span></span> <span data-ttu-id="942a4-135">클래스 `.baseColor`, 등에서는 변수가 생성 되는 CSS 파일에 각각의 계산된 된 값을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-135">The classes `.baseColor`, etc. will demonstrate the calculated values of each of the variables in the CSS file that is produced.</span></span>

### <a name="getting-started"></a><span data-ttu-id="942a4-136">시작</span><span class="sxs-lookup"><span data-stu-id="942a4-136">Getting started</span></span>

<span data-ttu-id="942a4-137">만들기는 **npm 구성 파일** (*package.json*) 프로젝트 폴더에서 참조 하도록 편집 하 고 `gulp` 및 `gulp-less`:</span><span class="sxs-lookup"><span data-stu-id="942a4-137">Create an **npm Configuration File** (*package.json*) in your project folder and edit it to reference `gulp` and `gulp-less`:</span></span>

```json
{
  "version": "1.0.0",
  "name": "asp.net",
  "private": true,
  "devDependencies": {
    "gulp": "3.9.1",
    "gulp-less": "3.3.0"
  }
}
```

<span data-ttu-id="942a4-138">프로젝트 폴더에서 나 Visual Studio 명령 프롬프트에서 종속성을 설치 **솔루션 탐색기** (**종속성 > npm > 패키지를 복원**).</span><span class="sxs-lookup"><span data-stu-id="942a4-138">Install the dependencies either at a command prompt in your project folder, or in Visual Studio **Solution Explorer** (**Dependencies > npm > Restore packages**).</span></span>

```console
npm install
```

![VS 패키지를 복원합니다.](less-sass-fa/_static/restore-packages.png)

<span data-ttu-id="942a4-140">프로젝트 폴더에 만듭니다는 **구성 파일 Gulp** (*gulpfile.js*) 자동화 된 프로세스를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-140">In the project folder, create a **Gulp Configuration File** (*gulpfile.js*) to define the automated process.</span></span>  <span data-ttu-id="942a4-141">나타내는 덜, 파일 및 작음 실행 되도록 작업을 맨 위에 있는 변수를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-141">Add a variable at the top of the file to represent Less, and a task to run Less:</span></span>

```javascript
var gulp = require("gulp"),
  fs = require("fs"),
  less = require("gulp-less");

gulp.task("less", function () {
  return gulp.src('Styles/main.less')
    .pipe(less())
    .pipe(gulp.dest('wwwroot/css'));
});
```

<span data-ttu-id="942a4-142">열기는 **작업 Runner 탐색기** (**보기 > 다른 Windows > 작업 Runner 탐색기**).</span><span class="sxs-lookup"><span data-stu-id="942a4-142">Open the **Task Runner Explorer** (**View > Other Windows > Task Runner Explorer**).</span></span> <span data-ttu-id="942a4-143">작업 간에 라는 새 작업을 표시 되어야 `less`합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-143">Among the tasks, you should see a new task named `less`.</span></span> <span data-ttu-id="942a4-144">창을 새로 고쳐야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-144">You might have to refresh the window.</span></span>

<span data-ttu-id="942a4-145">실행 된 `less` 작업에 여기 표시 된 출력이 표시:</span><span class="sxs-lookup"><span data-stu-id="942a4-145">Run the `less` task, and you see output similar to what is shown here:</span></span>

![task runner가 적은](less-sass-fa/_static/less-task-runner.png)

<span data-ttu-id="942a4-147">*wwwroot/css* 폴더에는 이제 새 파일을 포함 *main.css*:</span><span class="sxs-lookup"><span data-stu-id="942a4-147">The *wwwroot/css* folder now contains a new file, *main.css*:</span></span>

![생성 된 기본 css](less-sass-fa/_static/main-css-created.png)

<span data-ttu-id="942a4-149">열기 *main.css* 다음과 같은 표시 있습니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-149">Open *main.css* and you see something like the following:</span></span>

```css
body {
    background-color: #336666;
}
.baseColor {
    color: #663333;
}
.bgLight {
    color: #884a44;
}
.bgLight2 {
    color: #aa6355;
}
.bgDark {
    color: #442225;
}
.bgDark2 {
    color: #221114;
}
```

<span data-ttu-id="942a4-150">간단한 HTML 페이지를 추가 *wwwroot* 폴더 및 참조 *main.css* 동작의 색상표를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-150">Add a simple HTML page to the *wwwroot* folder, and reference *main.css* to see the color palette in action.</span></span>

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <link href="css/main.css" rel="stylesheet" />
    <title></title>
</head>
<body>
    <div>
        <div class="baseColor">BaseColor</div>
        <div class="bgLight">Light</div>
        <div class="bgLight2">Light2</div>
        <div class="bgDark">Dark</div>
        <div class="bgDark2">Dark2</div>
    </div>
</body>
</html>
```

<span data-ttu-id="942a4-151">확인할 수 있습니다를 180도 회전 하 `@base` 생성 하는 데 사용 `@background` 색상표 색의 반대편에 `@base`:</span><span class="sxs-lookup"><span data-stu-id="942a4-151">You can see that the 180 degree spin on `@base` used to produce `@background` resulted in the color wheel opposing color of `@base`:</span></span>

![덜 테스트 예제](less-sass-fa/_static/less-test-screenshot.png)

<span data-ttu-id="942a4-153">작은 중첩된 미디어 쿼리 뿐만 아니라 중첩 된 규칙에 대 한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-153">Less also provides support for nested rules, as well as nested media queries.</span></span> <span data-ttu-id="942a4-154">예를 들어 메뉴 CSS 규칙 세부 정보 표시 될 수와 같은 중첩 된 계층 구조 정의 처럼 이러한:</span><span class="sxs-lookup"><span data-stu-id="942a4-154">For example, defining nested hierarchies like menus can result in verbose CSS rules like these:</span></span>

```css
nav {
    height: 40px;
    width: 100%;
}
nav li {
    height: 38px;
    width: 100px;
}
nav li a:link {
    color: #000;
    text-decoration: none;
}
nav li a:visited {
    text-decoration: none;
    color: #CC3333;
}
nav li a:hover {
    text-decoration: underline;
    font-weight: bold;
}
nav li a:active {
    text-decoration: underline;
}
```

<span data-ttu-id="942a4-155">이상적으로 관련 된 스타일 규칙의 모든 됩니다 내에 존재 함께 CSS 파일 하는데 실제로 더 할 게 없습니다 규칙 및 아마도 블록 설명을 제외 하 고이 규칙을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-155">Ideally all of the related style rules will be placed together within the CSS file, but in practice there is nothing enforcing this rule except convention and perhaps block comments.</span></span>

<span data-ttu-id="942a4-156">작은 사용 하 여 이러한 동일한 규칙을 정의 하는 것은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-156">Defining these same rules using Less looks like this:</span></span>

```less
nav {
    height: 40px;
    width: 100%;
    li {
        height: 38px;
        width: 100px;
        a {
            color: #000;
            &:link { text-decoration:none}
            &:visited { color: #CC3333; text-decoration:none}
            &:hover { text-decoration:underline; font-weight:bold}
            &:active {text-decoration:underline}
        }
    }
}
```

<span data-ttu-id="942a4-157">이 경우의 모든 하위 요소 사항에 유의 `nav` 해당 범위 내에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-157">Note that in this case, all of the subordinate elements of `nav` are contained within its scope.</span></span> <span data-ttu-id="942a4-158">모든 반복의 부모 요소를 더 이상 (`nav`, `li`, `a`), 총 줄 수 삭제도 (하지만 중 일부를 두 번째 예에 있는 동일한 줄에 배치 하는 값의 결과).</span><span class="sxs-lookup"><span data-stu-id="942a4-158">There is no longer any repetition of parent elements (`nav`, `li`, `a`), and the total line count has dropped as well (though some of that is a result of putting values on the same lines in the second example).</span></span> <span data-ttu-id="942a4-159">것을 쉽게 조직으로,이 경우 명시적으로 제한 된 범위 내에서 지정된 된 UI 요소에 대 한 규칙을 모두 보려면을 off로 설정 파일의 나머지 부분에서 중괄호.</span><span class="sxs-lookup"><span data-stu-id="942a4-159">It can be very helpful, organizationally, to see all of the rules for a given UI element within an explicitly bounded scope, in this case set off from the rest of the file by curly braces.</span></span>

<span data-ttu-id="942a4-160">`&` 구문으로는 적은 선택기 기능, 및 현재 선택기 부모를 나타내는입니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-160">The `&` syntax is a Less selector feature, with & representing the current selector parent.</span></span> <span data-ttu-id="942a4-161">따라서 내에서 {...}</span><span class="sxs-lookup"><span data-stu-id="942a4-161">So, within the a {...}</span></span> <span data-ttu-id="942a4-162">블록 `&` 나타냅니다는 `a` 태그 이므로 `&:link` 같습니다 `a:link`합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-162">block, `&` represents an `a` tag, and thus `&:link` is equivalent to `a:link`.</span></span>

<span data-ttu-id="942a4-163">미디어 쿼리, 응답성이 뛰어난 디자인을 만드는 데 매우 유용 수에 영향을 많이 반복 및 복잡 한 CSS 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-163">Media queries, extremely useful in creating responsive designs, can also contribute heavily to repetition and complexity in CSS.</span></span> <span data-ttu-id="942a4-164">작은 하 라 수 클래스 내에 중첩할 수에 대 한 미디어 쿼리 있으므로 전체 클래스 정의 내에서 서로 다른 반복 될 필요가 없습니다 최상위 `@media` 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-164">Less allows media queries to be nested within classes, so that the entire class definition doesn't need to be repeated within different top-level `@media` elements.</span></span> <span data-ttu-id="942a4-165">예를 들어 다음과 같습니다 CSS 응답 메뉴에 대 한.</span><span class="sxs-lookup"><span data-stu-id="942a4-165">For example, here is CSS for a responsive menu:</span></span>

```css
.navigation {
    margin-top: 30%;
    width: 100%;
}
@media screen and (min-width: 40em) {
    .navigation {
        margin: 0;
    }
}
@media screen and (min-width: 62em) {
    .navigation {
        width: 960px;
        margin: 0;
    }
}
```

<span data-ttu-id="942a4-166">으로 내에 더 잘 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-166">This can be better defined in Less as:</span></span>

```less
.navigation {
    margin-top: 30%;
    width: 100%;
    @media screen and (min-width: 40em) {
        margin: 0;
    }
    @media screen and (min-width: 62em) {
        width: 960px;
        margin: 0;
    }
}
```

<span data-ttu-id="942a4-167">이미 살펴본 작은 다른 기능은는 미리 정의 된 변수에서 생성 되는 스타일 특성을 허용 하는 수학 연산에 대 한 지원입니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-167">Another feature of Less that we have already seen is its support for mathematical operations, allowing style attributes to be constructed from pre-defined variables.</span></span> <span data-ttu-id="942a4-168">이 모든 종속 값을 자동으로 변경 하 고 기본 변수를 수정할 수 있으므로 훨씬 쉽게 관련된 스타일을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-168">This makes updating related styles much easier, since the base variable can be modified and all dependent values change automatically.</span></span>

<span data-ttu-id="942a4-169">특히 대규모 사이트에 대 한 CSS 파일 (및 미디어 쿼리를 사용 하는 경우에 특히), 매우 커질 수 시간이 지남에 따라 작업을 반환 하는 경향이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-169">CSS files, especially for large sites (and especially if media queries are being used), tend to get quite large over time, making working with them unwieldy.</span></span> <span data-ttu-id="942a4-170">Less 파일 정의할 수 있습니다 별도로 사용 하 여 함께 끌어온 다음 `@import` 지시문입니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-170">Less files can be defined separately, then pulled together using `@import` directives.</span></span> <span data-ttu-id="942a4-171">작은 데도 사용할 수 있습니다를 개별 CSS 파일을 가져오며도 필요한 경우.</span><span class="sxs-lookup"><span data-stu-id="942a4-171">Less can also be used to import individual CSS files, as well, if desired.</span></span>

<span data-ttu-id="942a4-172">*Mixin* 수 매개 변수를 받아들이고 형태로 mixin 가드 특정 mixin 적용 시 정의 하는 선언적 방법을 제공 하는 조건부 논리를 지원 작습니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-172">*Mixins* can accept parameters, and Less supports conditional logic in the form of mixin guards, which provide a declarative way to define when certain mixins take effect.</span></span> <span data-ttu-id="942a4-173">Mixin 가드 사용 되는 일반적인 빛 방식에 따라 색을 조정 하려면 되거나 어두운 소스 색상입니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-173">A common use for mixin guards is to adjust colors based on how light or dark the source color is.</span></span> <span data-ttu-id="942a4-174">Mixin 색에 대 한 매개 변수를 허용 하는 지정 된 경우 mixin 가드 해당 색에 기반 mixin 수정에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-174">Given a mixin that accepts a parameter for color, a mixin guard can be used to modify the mixin based on that color:</span></span>

```less
.box (@color) when (lightness(@color) >= 50%) {
    background-color: #000;
}
.box (@color) when (lightness(@color) < 50%) {
    background-color: #FFF;
}
.box (@color) {
    color: @color;
}

.feature {
    .box (@base);
}
```

<span data-ttu-id="942a4-175">우리의 현재 지정 된 `@base` 의 값 `#663333`, 적은이 스크립트는 다음 CSS를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-175">Given our current `@base` value of `#663333`, this Less script will produce the following CSS:</span></span>

```css
.feature {
    background-color: #FFF;
    color: #663333;
}
```

<span data-ttu-id="942a4-176">다양 한 추가 기능을 제공 작은 있지만이 제공 해야이 전력의 몇 가지 아이디어 언어 전처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-176">Less provides a number of additional features, but this should give you some idea of the power of this preprocessing language.</span></span>

## <a name="sass"></a><span data-ttu-id="942a4-177">Sass</span><span class="sxs-lookup"><span data-stu-id="942a4-177">Sass</span></span>

<span data-ttu-id="942a4-178">Sass는 약간 다른 구문을 하면서도 동일한 기능을 대부분에 대 한 지원을 제공 하는, 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-178">Sass is similar to Less, providing support for many of the same features, but with slightly different syntax.</span></span> <span data-ttu-id="942a4-179">JavaScript 대신 Ruby를 사용 하 여 빌드하 있으며 다른 설치 요구 사항만 요구 하므로입니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-179">It is built using Ruby, rather than JavaScript, and so has different setup requirements.</span></span> <span data-ttu-id="942a4-180">원래 Sass 언어 중괄호 또는 세미콜론을 사용 하지 않고 있지만 대신 공백 및 들여쓰기를 사용 하 여 범위를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-180">The original Sass language did not use curly braces or semicolons, but instead defined scope using white space and indentation.</span></span> <span data-ttu-id="942a4-181">Sass 버전 3에서에서 새로운 구문 도입 되기 **SCSS** "Sassy CSS (").</span><span class="sxs-lookup"><span data-stu-id="942a4-181">In version 3 of Sass, a new syntax was introduced, **SCSS** ("Sassy CSS").</span></span> <span data-ttu-id="942a4-182">SCSS는 CSS 비슷합니다 수준 들여쓰기 및 공백 무시 하 고 대신 중괄호와 세미콜론을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-182">SCSS is similar to CSS in that it ignores indentation levels and whitespace, and instead uses semicolons and curly braces.</span></span>

<span data-ttu-id="942a4-183">Sass를 설치 하려면 일반적으로 사용자는 Ruby (사전 설치 Mac에서)를 먼저 설치을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-183">To install Sass, typically you would first install Ruby (pre-installed on Mac), and then run:</span></span>

```console
gem install sass
```

<span data-ttu-id="942a4-184">그러나 Visual Studio를 실행 하는 경우 있습니다 수 시작 Sass 거의 같은 방법으로 작은와 마찬가지로 하세요.</span><span class="sxs-lookup"><span data-stu-id="942a4-184">However, if you're running Visual Studio, you can get started with Sass in much the same way as you would with Less.</span></span> <span data-ttu-id="942a4-185">열기 *package.json* 에 "gulp sass" 패키지를 추가 하 고 `devDependencies`:</span><span class="sxs-lookup"><span data-stu-id="942a4-185">Open *package.json* and add the "gulp-sass" package to `devDependencies`:</span></span>

```json
"devDependencies": {
  "gulp": "3.9.1",
  "gulp-less": "3.3.0",
  "gulp-sass": "3.1.0"
}
```

<span data-ttu-id="942a4-186">다음으로 수정 *gulpfile.js* sass 변수 및 Sass 파일을 컴파일할 wwwroot 폴더에서 결과 저장 하는 작업을 추가 하려면:</span><span class="sxs-lookup"><span data-stu-id="942a4-186">Next, modify *gulpfile.js* to add a sass variable and a task to compile your Sass files and place the results in the wwwroot folder:</span></span>

```javascript
var gulp = require("gulp"),
  fs = require("fs"),
  less = require("gulp-less"),
  sass = require("gulp-sass");

// other content removed

gulp.task("sass", function () {
  return gulp.src('Styles/main2.scss')
    .pipe(sass())
    .pipe(gulp.dest('wwwroot/css'));
});
```

<span data-ttu-id="942a4-187">Sass 파일을 추가할 수 이제 *main2.scss* 에 *스타일* 프로젝트의 루트에 폴더:</span><span class="sxs-lookup"><span data-stu-id="942a4-187">Now you can add the Sass file *main2.scss* to the *Styles* folder in the root of the project:</span></span>

![scss 파일 추가](less-sass-fa/_static/add-scss-file.png)

<span data-ttu-id="942a4-189">열기 *main2.scss* 다음 줄을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-189">Open *main2.scss* and add the following:</span></span>

```sass
$base: #CC0000;
body {
    background-color: $base;
}
```

<span data-ttu-id="942a4-190">모든 파일을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-190">Save all of your files.</span></span> <span data-ttu-id="942a4-191">이제를 새로 고칠 때 **Task Runner 탐색기**, 표시는 `sass` 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-191">Now when you refresh **Task Runner Explorer**, you see a `sass` task.</span></span> <span data-ttu-id="942a4-192">찾는 위치 하 고 실행 된 */wwwroot/css* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-192">Run it, and look in the */wwwroot/css* folder.</span></span> <span data-ttu-id="942a4-193">이제는 *main2.css* 이러한 내용 인 파일에:</span><span class="sxs-lookup"><span data-stu-id="942a4-193">There is now a *main2.css* file, with these contents:</span></span>

```css
body {
    background-color: #CC0000;
}
```

<span data-ttu-id="942a4-194">Sass는 작은 수행 하는 이점을 제공 된 동일한 대부분의 중첩을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-194">Sass supports nesting in much the same was that Less does, providing similar benefits.</span></span> <span data-ttu-id="942a4-195">파일 함수에 의해 분할 되 고 사용 하 여 포함 된 `@import` 지시문:</span><span class="sxs-lookup"><span data-stu-id="942a4-195">Files can be split up by function and included using the `@import` directive:</span></span>

```sass
@import 'anotherfile';
```

<span data-ttu-id="942a4-196">Sass에서는 mixin도 사용할 수는 `@mixin` 키워드를 정의 하 고 `@include` ,이 예제에서와 같이 포함 하도록 [sass lang.com](http://sass-lang.com):</span><span class="sxs-lookup"><span data-stu-id="942a4-196">Sass supports mixins as well, using the `@mixin` keyword to define them and `@include` to include them, as in this example from [sass-lang.com](http://sass-lang.com):</span></span>

```sass
@mixin border-radius($radius) {
    -webkit-border-radius: $radius;
    -moz-border-radius: $radius;
    -ms-border-radius: $radius;
    border-radius: $radius;
}

.box { @include border-radius(10px); }
```

<span data-ttu-id="942a4-197">Mixin, 외에도 Sass도 지원 하므로 상속의 개념 하나의 클래스를 다른 확장 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-197">In addition to mixins, Sass also supports the concept of inheritance, allowing one class to extend another.</span></span> <span data-ttu-id="942a4-198">Mixin, 하지만 더 적은 CSS 코드를 개념적으로 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-198">It's conceptually similar to a mixin, but results in less CSS code.</span></span> <span data-ttu-id="942a4-199">사용 하 여 수행 되는 `@extend` 키워드입니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-199">It's accomplished using the `@extend` keyword.</span></span> <span data-ttu-id="942a4-200">Mixin를 실행 하려면 다음을 추가 하면 *main2.scss* 파일:</span><span class="sxs-lookup"><span data-stu-id="942a4-200">To try out mixins, add the following to your *main2.scss* file:</span></span>

```sass
@mixin alert {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
}

.success {
    @include alert;
    border-color: green;
}

.error {
    @include alert;
    color: red;
    border-color: red;
    font-weight:bold;
}
```

<span data-ttu-id="942a4-201">출력을 검토 *main2.css* 실행 된 후의 `sass` 에서 작업이 **Task Runner 탐색기**:</span><span class="sxs-lookup"><span data-stu-id="942a4-201">Examine the output in *main2.css* after running the `sass` task in **Task Runner Explorer**:</span></span>

```css
.success {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
    border-color: green;
 }

.error {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
    color: red;
    border-color: red;
    font-weight: bold;
}
```

<span data-ttu-id="942a4-202">각 클래스에 반복 되는 경고 mixin의 공용 속성의 모든를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-202">Notice that all of the common properties of the alert mixin are repeated in each class.</span></span> <span data-ttu-id="942a4-203">Mixin 개발 시에는 중복 제거 작업을 훌륭히 하지만, 필요한 CSS 파일-잠재적 성능 문제를 보다 큰 결과에서 중복 많이 CSS를 만들어서 계속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-203">The mixin did a good job of helping eliminate duplication at development time, but it's still creating CSS with a lot of duplication in it, resulting in larger than necessary CSS files - a potential performance issue.</span></span>

<span data-ttu-id="942a4-204">이제와 경고 mixin 대체는 `.alert` 클래스 하 고 변경 `@include` 를 `@extend` (확장를 `.alert`이 아니라 `alert`):</span><span class="sxs-lookup"><span data-stu-id="942a4-204">Now replace the alert mixin with a `.alert` class, and change `@include` to `@extend` (remembering to extend `.alert`, not `alert`):</span></span>

```sass
.alert {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
}

.success {
    @extend .alert;
    border-color: green;
}

.error {
    @extend .alert;
    color: red;
    border-color: red;
    font-weight:bold;
}
```

<span data-ttu-id="942a4-205">Sass, 실행 하 고 결과 CSS를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-205">Run Sass once more, and examine the resulting CSS:</span></span>

```css
.alert, .success, .error {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
}

.success {
    border-color: green;
}

.error {
    color: red;
    border-color: red;
    font-weight: bold;
}
```

<span data-ttu-id="942a4-206">필요에 따라 횟수 만큼만 속성은 정의 하는 이제 되며 더 잘 CSS 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-206">Now the properties are defined only as many times as needed, and better CSS is generated.</span></span>

<span data-ttu-id="942a4-207">Sass 함수 및 작은 비슷한 조건부 논리 연산을 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-207">Sass also includes functions and conditional logic operations, similar to Less.</span></span> <span data-ttu-id="942a4-208">실제로 두 언어의 기능은 매우 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-208">In fact, the two languages' capabilities are very similar.</span></span>

## <a name="less-or-sass"></a><span data-ttu-id="942a4-209">덜 또는 Sass?</span><span class="sxs-lookup"><span data-stu-id="942a4-209">Less or Sass?</span></span>

<span data-ttu-id="942a4-210">여전히 사용할지 여부를 결정을 덜 사용 나 Sass 일반적으로 더 나은 합의 없습니다 (또는 심지어 것인지 Sass 내에서 최신 SCSS 구문 또는 원래 Sass 선호).</span><span class="sxs-lookup"><span data-stu-id="942a4-210">There is still no consensus as to whether it's generally better to use Less or Sass (or even whether to prefer the original Sass or the newer SCSS syntax within Sass).</span></span> <span data-ttu-id="942a4-211">아마도 가장 중요 한 결정은 하는 **다음이 도구 중 하나를 사용 하 여**만 직접 코딩 CSS 파일을 반대로 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-211">Probably the most important decision is to **use one of these tools**, as opposed to just hand-coding your CSS files.</span></span> <span data-ttu-id="942a4-212">수행한 후 둘 다 없는 의사 결정 및 Sass 다 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-212">Once you've made that decision, both Less and Sass are good choices.</span></span>

## <a name="font-awesome"></a><span data-ttu-id="942a4-213">놀라운 글꼴</span><span class="sxs-lookup"><span data-stu-id="942a4-213">Font Awesome</span></span>

<span data-ttu-id="942a4-214">CSS 프로세서 아니라 최신 웹 응용 프로그램 스타일 지정에 대 한 또 다른 훌륭한 리소스는 놀라운 글꼴 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-214">In addition to CSS preprocessors, another great resource for styling modern web applications is Font Awesome.</span></span> <span data-ttu-id="942a4-215">글꼴 Awesome 웹 응용 프로그램에서 자유롭게 사용할 수 있는 500 개 스케일러블 벡터 아이콘을 제공 하는 도구 키트입니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-215">Font Awesome is a toolkit that provides over 500 scalable vector icons that can be freely used in your web applications.</span></span> <span data-ttu-id="942a4-216">원래 부트스트랩와 작동 하도록 설계 되었습니다 했으나 의존 하지 않고 모든 JavaScript 라이브러리 또는 해당 프레임 워크에서입니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-216">It was originally designed to work with Bootstrap, but it has no dependency on that framework or on any JavaScript libraries.</span></span>

<span data-ttu-id="942a4-217">놀라운 글꼴을 시작 하는 공용 콘텐츠 배달 네트워크 (CDN) 위치를 사용 하 여, 참조를 추가 하는 가장 쉬운 방법은:</span><span class="sxs-lookup"><span data-stu-id="942a4-217">The easiest way to get started with Font Awesome is to add a reference to it, using its public content delivery network (CDN) location:</span></span>

```html
<link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/font-awesome/4.3.0/css/font-awesome.min.css">
```

<span data-ttu-id="942a4-218">추가할 수 있습니다도 Visual Studio 프로젝트에 "종속성"을 추가 하 여에서 *bower.json*:</span><span class="sxs-lookup"><span data-stu-id="942a4-218">You can also add it to your Visual Studio project by adding it to the "dependencies" in *bower.json*:</span></span>

```json
{
  "name": "ASP.NET",
  "private": true,
  "dependencies": {
    "bootstrap": "3.0.0",
    "jquery": "1.10.2",
    "jquery-validation": "1.11.1",
    "jquery-validation-unobtrusive": "3.2.2",
    "hammer.js": "2.0.4",
    "bootstrap-touch-carousel": "0.8.0",
    "Font-Awesome": "4.3.0"
  }
}
```

<span data-ttu-id="942a4-219">글꼴 놀라운 클래스, 일반적으로 접두사로 "fa-", 인라인 HTML 요소에 적용 하 여 아이콘을 추가할 응용 프로그램에 수 페이지에 놀라운 글꼴에 대 한 참조를 만든 후 (예: `<span>` 또는 `<i>`).</span><span class="sxs-lookup"><span data-stu-id="942a4-219">Once you have a reference to Font Awesome on a page, you can add icons to your application by applying Font Awesome classes, typically prefixed with "fa-", to your inline HTML elements (such as `<span>` or `<i>`).</span></span>  <span data-ttu-id="942a4-220">예를 들어, 단순 목록 및 다음과 같은 코드를 사용 하 여 메뉴에 아이콘을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-220">For example, you can add icons to simple lists and menus using code like this:</span></span>

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title></title>
    <link href="lib/font-awesome/css/font-awesome.css" rel="stylesheet" />
</head>
<body>
    <ul class="fa-ul">
        <li><i class="fa fa-li fa-home"></i> Home</li>
        <li><i class="fa fa-li fa-cog"></i> Settings</li>
    </ul>
</body>
</html>
```

<span data-ttu-id="942a4-221">이렇게 하면 브라우저에서 다음이 생성-각 항목 옆의 아이콘에 유의 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-221">This produces the following in the browser - note the icon beside each item:</span></span>

![목록 아이콘](less-sass-fa/_static/list-icons-screenshot.png)

<span data-ttu-id="942a4-223">여기에 사용 가능한 아이콘의 전체 목록을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-223">You can view a complete list of the available icons here:</span></span>

<span data-ttu-id="942a4-224">http://fontawesome.io/icons/</span><span class="sxs-lookup"><span data-stu-id="942a4-224">http://fontawesome.io/icons/</span></span>

## <a name="summary"></a><span data-ttu-id="942a4-225">요약</span><span class="sxs-lookup"><span data-stu-id="942a4-225">Summary</span></span>

<span data-ttu-id="942a4-226">최신 웹 응용 프로그램에는 점점 더 명확 하 고, 직관적으로 다양 한 장치에서에서 쉽게 사용할 수 있는 응답을 유체 디자인 요구 합니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-226">Modern web applications increasingly demand responsive, fluid designs that are clean, intuitive, and easy to use from a variety of devices.</span></span> <span data-ttu-id="942a4-227">이러한 목표를 달성 하는 데 필요한 CSS 스타일 시트의 복잡 한 관리 덜 전처리기 like를 사용 하 여 또는 Sass 방법이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="942a4-227">Managing the complexity of the CSS stylesheets required to achieve these goals is best done using a preprocessor like Less or Sass.</span></span> <span data-ttu-id="942a4-228">또한 글꼴 놀라운 같은 도구 키트 잘 알려진 텍스트 탐색 메뉴 아이콘을 신속 하 게 제공 하 고 응용 프로그램의 전반적인 사용자 향상 단추 환경.</span><span class="sxs-lookup"><span data-stu-id="942a4-228">In addition, toolkits like Font Awesome quickly provide well-known icons to textual navigation menus and buttons, improving the overall user experience of your application.</span></span>

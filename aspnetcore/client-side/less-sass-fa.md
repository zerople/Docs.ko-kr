---
title: "더 적게 Sass, 및 ASP.NET 코어에서 놀라운 글꼴 | Microsoft 문서"
author: ardalis
description: "ASP.NET 핵심 응용 프로그램에서 더 적게 Sass, 한 글꼴을 사용 하는 방법에 알아봅니다."
keywords: "ASP.NET Core Sass, 글꼴 Awesome, 전처리기의 작은"
ms.author: tdykstra
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 94c988f9-95fd-425d-b37e-7f846598c6d4
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/less-sass-fa
translationtype: Machine Translation
ms.sourcegitcommit: 0ebb9b63931ccb26126740de08270eda9b1ea486
ms.openlocfilehash: f0b5376e9683ea8975e13912343398a5995b5e98
ms.lasthandoff: 03/23/2017

---
# <a name="introduction-to-styling-applications-with-less-sass-and-font-awesome-in-aspnet-core"></a>더 적은 노력으로 스타일 응용 프로그램, Sass, 및 ASP.NET 코어에서 글꼴 Awesome 소개

[Steve Smith](http://ardalis.com)

스타일 및 전반적인 경험에 웹 응용 프로그램의 사용자는 점점 더 높은 기대 합니다. 최신 웹 응용 프로그램 풍부한 도구 및 정의 하 고 일관 된 방식으로 자신의 모양과 느낌을 관리 하기 위한 프레임 워크에 자주 활용 합니다. 같은 프레임 워크를 [부트스트랩](http://getbootstrap.com/) 스타일 및 웹 사이트에 대 한 레이아웃 옵션의 공통 집합을 정의 하는 용도로 진전을 얻을 수 있습니다. 그러나 대부분의 특수 사이트도 이용할 수 있도록 사이트의 인터페이스 보다 직관적인 이미지가 아닌 아이콘에 쉽게 액세스할 수 있을 뿐만 아니라 효과적으로 정의 하 고 스타일과 연계 하는 스타일 시트 (CSS) 파일을 유지 관리할 수 있는 사용할 수 있습니다. 여기 언어 및 도구를 지 원하는 [덜](http://lesscss.org/) 및 [Sass](http://sass-lang.com/), 라이브러리와 같이 [글꼴 Awesome](http://fortawesome.github.io/Font-Awesome/),으로 제공 합니다.

## <a name="css-preprocessor-languages"></a>CSS 전처리기 언어

전처리기의 기본 언어를 사용 하는 환경을 개선 하기 위해 다른 언어로 컴파일되는 언어를 이라고 합니다. CSS에 대 한 인기 있는 전처리기의 두 가지: 덜 및 Sass 합니다.  이러한 전처리기의 변수 및 대규모의 복잡 한 스타일 시트의 관리 효율을 개선 하는 중첩 된 규칙에 대 한 지원 등 CSS에 기능을 추가 합니다. 변수를 같은 간단한 경우에 지원 부족 언어로 CSS는 매우 기본적이를 부분과 CSS 파일 반복적이 고 그 결과입니다. 전처리기의 통해 실제 프로그래밍 언어의 기능을 추가 하면 좋게 스타일 규칙을 제공 하 고 중복을 줄일 수 있습니다. Visual Studio는 기본적으로 모두 덜 지원 및 Sass, 뿐만 아니라 이러한 언어를 작업할 때 개발 환경을 더욱 개선할 수 있는 확장을 제공 합니다.

이 CSS를 스타일 정보의 가독성과 전처리기의 향상 시키는 방법의 간단한 예를 살펴보겠습니다.

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

작은 사용 하 여,이 다시 작성할 수 있습니다 모든 중복을 제거 하기 위해 사용 하 여는 *mixin* ("혼합" 수 있기 때문에 그렇게 명명 된 클래스 또는 규칙 집합을 다른 속성):

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

## <a name="less"></a>간단히

전처리기는 덜 CSS Node.js를 사용 하 여 실행 합니다. 작은 설치 하려면 명령 프롬프트에서 다음 노드 패키지 관리자 (npm)를 사용 (-g 의미 "전역"):

```console
npm install -g less
```

Visual Studio를 사용 하는 경우 프로젝트에 하나 이상의 작은 파일을 추가 하 고 다음 컴파일 타임에 처리 하도록 Gulp (또는 Grunt)를 구성 하 여 더 적은 노력으로 시작할 얻을 수 있습니다. 추가 *스타일* 폴더를 프로젝트에 새 덜 라는 파일을 추가 하 고 *main.less* 이 폴더에 있습니다.

![작은 파일 추가](less-sass-fa/_static/add-less-file.png)

추가 되 고 나면 폴더 구조 코드는 다음과 같아야 합니다.

![폴더 구조](less-sass-fa/_static/folder-structure.png)

이제 CSS로 컴파일되고 Gulp 여 wwwroot 폴더에 배포 하는 파일에 몇 가지 기본 스타일을 추가할 수 있습니다.

수정 *main.less* 한 기본 색에서 간단한 색상표를 만드는 다음 콘텐츠를 포함 하도록 합니다.

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

`@base`다른 @-prefixed 항목은 변수입니다. 각각의 색을 나타냅니다. 제외 하 고 `@base`, 색 함수를 사용 하 여 설정 된: 밝게, 어둡게, 및 회전 합니다. 어둡게 및 밝게 거의 예상 대로; 수행 회전 각도 (약 색상표) 번호로 색의 색상을 조정합니다. 작은 프로세서는 어딘가에 사용 해야 이러한 변수가 작동 하는 방법을 설명 하기 위해 사용 되지 않는 변수를 무시 합니다. 클래스 `.baseColor`, 등에서는 변수가 생성 되는 CSS 파일에 각각의 계산된 된 값을 보여 줍니다.

### <a name="getting-started"></a>시작

만들기는 **npm 구성 파일** (*package.json*) 프로젝트 폴더에 참조 하도록 편집 하 고 `gulp` 및 `gulp-less`:

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

프로젝트 폴더에서 나 Visual Studio 명령 프롬프트에서 종속성을 설치 **솔루션 탐색기** (**종속성 > npm > 패키지를 복원**).

```console
npm install
```

![VS 패키지를 복원합니다.](less-sass-fa/_static/restore-packages.png)

프로젝트 폴더에 만듭니다는 **구성 파일 Gulp** (*gulpfile.js*) 자동화 된 프로세스를 정의 합니다.  나타내는 Less 파일 및 작업을 실행 하는 작은 맨 위에 있는 변수를 추가 합니다.

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

열기는 **Task Runner 탐색기** (**보기 > 다른 Windows > Task Runner 탐색기**). 작업 간에 라는 새 작업이 표시 됩니다 `less`합니다. 창을 새로 해야 할 수 있습니다.

실행 된 `less` 작업 여기 보이는 것 유사한 출력이 표시 합니다.

![작업 러너 작음](less-sass-fa/_static/less-task-runner.png)

*wwwroot/css* 폴더에는 이제 새 파일을 포함 *main.css*:

![생성 된 기본 css](less-sass-fa/_static/main-css-created.png)

열기 *main.css* 다음과 같이 표시 합니다.

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

간단한 HTML 페이지에 추가 *wwwroot* 폴더 및 참조 *main.css* 색상표에서 작업을 볼 수 있습니다.

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

표시를 180도 회전 하 `@base` 생성 하는 데 사용 `@background` 색상표의 색을 반대편에 `@base`:

![작은 테스트 예제](less-sass-fa/_static/less-test-screenshot.png)

작은 중첩된 미디어 쿼리 뿐만 아니라 중첩 된 규칙에 대 한 지원을 제공합니다. 예를 들어 메뉴 CSS 규칙 세부 정보 표시 될 수와 같은 중첩 된 계층 구조 정의 다음과 같은 이러한.

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

이상적으로 관련 된 스타일 규칙의 모든 됩니다 내에 존재 함께 CSS 파일 하는데 연습 없는 규칙과 블록 주석이 아마도 제외 하 고이 규칙을 적용 합니다.

작은 사용 하 여 이러한 동일한 규칙을 정의 하는 것은 다음과 같습니다.

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

이 경우의 모든 하위 요소에 유의 `nav` 해당 범위 내에 포함 됩니다. 모든 반복의 부모 요소는 더 이상 (`nav`, `li`, `a`), 총 줄 수는 떨어진도 (하지만 중 일부를 두 번째 예제에서 동일한 줄에 배치 하는 값의 결과). 하기란 매우 유용한 조직으로,이 경우 명시적으로 제한 된 범위 내에서 지정된 된 UI 요소에 대 한 규칙을 모두 보려면 해제 파일의 나머지 부분에서 중괄호입니다.

`&` 구문은 작은 선택기 기능으로는 현재 선택기 부모를 나타내는 < /. 따라서 내에서 {...} 블록 `&` 나타냅니다는 `a` 태그 이므로 `&:link` 같습니다 `a:link`합니다.

미디어 쿼리, 응답성이 뛰어난 디자인을 만드는 데 매우 유용도 반복 및 CSS의 복잡성에 크게 기여할 수 있습니다. 작은 미디어 쿼리를 수 있습니다 클래스 내에 중첩 될 전체 클래스 정의 내에서 다른 반복 될 필요가 없는 있도록 최상위 `@media` 요소입니다. 예를 들어 다음과 같습니다 CSS 응답 메뉴.

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

으로 내에 더 잘 정의할 수 있습니다.

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

이미 살펴본 미만인의 또 다른 기능은 미리 정의 된 변수에서 생성 되는 스타일 특성을 허용 하는 수학 연산 지원 합니다. 이 모든 종속 값이 자동으로 변경 하 고 기본 변수를 수정할 수 있으므로 훨씬 쉽게 관련된 스타일을 업데이트할 수 있습니다.

CSS 파일, 특히 대규모 사이트 (및 미디어 쿼리에 사용 되는 경우에 특히), 매우 커질 수 시간이 지남에 따라 작업을 다루기 힘든 하는 경우가 많습니다. 작은 파일 정의할 수 있습니다 개별적으로 사용 하 여 함께 가져온 다음 `@import` 지시문입니다. 작은 수도 가져오는 데 사용할 개별 CSS 파일, 필요한 경우.

*Mixin* 수 매개 변수를 받아들이고 mixin 가드 특정 mixin 적용 시기를 정의 하는 선언적 방법을 제공 하는 양식에서 조건부 논리를 지원 작은 합니다. Mixin 보호에 대 한 일반적인 사용은 light 방식에 따라 색을 조정 하 또는 어두운 소스 색상입니다. 색에 대 한 매개 변수를 허용 하는 mixin 들어 mixin 가드 해당 색에 따라 mixin을 수정 하려면 사용할 수 있습니다.

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

우리의 현재 지정 된 `@base` 값 `#663333`, 덜이 스크립트는 다음 CSS를 생성 합니다.

```css
.feature {
    background-color: #FFF;
    color: #663333;
}
```

다양 한 추가 기능을 제공 작은 이지만이 해야이 전력의 몇 가지 아이디어 언어 전처리.

## <a name="sass"></a>Sass

Sass는 약간 다른 구문을 하지만 동일한 기능을 대부분에 대 한 지원을 제공 하는 더 적게 비슷합니다. JavaScript 대신 Ruby를 사용 하 여 작성 되었으며 다른 설치 요구 사항 하므로 해야 합니다. 원래 Sass 언어 중괄호 또는 세미콜론을 사용 하지 않고 있지만 그 대신 공백 및 들여쓰기를 사용 하 여 범위를 정의 합니다. Sass 버전 3에에서는 새 구문을 도입 된, **SCSS** "Sassy CSS ("). SCSS는 들여쓰기 수준 및 공백 무시 하 고 대신 세미콜론 및 중괄호를 사용 하 여 한다는 CSS와 비슷합니다.

Sass를 설치 하려면 일반적으로 먼저 (에 미리 설치 되어 Mac), Ruby를 설치 하는 다음 실행 합니다.

```console
gem install sass
```

그러나 Visual Studio를 실행 하는 경우 있습니다 수 시작 Sass 거의 같은 방법으로 더 적은 노력으로 하듯이 합니다. 열기 *package.json* 에 "gulp sass" 패키지를 추가 하 고 `devDependencies`:

```json
"devDependencies": {
  "gulp": "3.9.1",
  "gulp-less": "3.3.0",
  "gulp-sass": "3.1.0"
}
```

다음에 수정 *gulpfile.js* sass 변수와 Sass 파일을 컴파일하고 wwwroot 폴더에 결과 저장 하는 작업을 추가 하려면:

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

Sass 파일을 추가할 수는 이제 *main2.scss* 에 *스타일* 프로젝트의 루트 폴더:

![scss 파일 추가](less-sass-fa/_static/add-scss-file.png)

열기 *main2.scss* 다음 줄을 추가 합니다.

```sass
$base: #CC0000;
body {
    background-color: $base;
}
```

모든 파일을 저장 합니다. 이제을 새로 고칠 때 **Task Runner 탐색기**, 표시는 `sass` 작업 합니다. 찾는 위치 하 고 실행의 */wwwroot/css* 폴더입니다. 이제는 *main2.css* 파일을이 콘텐츠를 포함 합니다.

```css
body {
    background-color: #CC0000;
}
```

Sass 작은 않음을, 유사한 혜택을 제공 된 동일한 거의 중첩을 지원 합니다. 파일 함수에 의해 분할 되 고 사용 하 여 포함 된 `@import` 지시문:

```sass
@import 'anotherfile';
```

Sass mixin도 사용 하 여 지원는 `@mixin` 정의 하는 키워드와 `@include` ,이 예제에서와 같이 포함 하도록 [sass lang.com](http://sass-lang.com):

```sass
@mixin border-radius($radius) {
    -webkit-border-radius: $radius;
    -moz-border-radius: $radius;
    -ms-border-radius: $radius;
    border-radius: $radius;
}

.box { @include border-radius(10px); }
```

Mixin 외에도 Sass도 지원 하므로 상속의 개념 하나의 클래스를 다른 확장 합니다. Mixin을 하지만 덜 CSS 코드에서 결과를 개념적으로 비슷합니다. 사용 하 여 수행 되는 `@extend` 키워드입니다. Mixin를 실행 하려면 다음을 추가 하면 *main2.scss* 파일:

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

출력을 검사 *main2.css* 실행 된 후의 `sass` 에서 작업이 **Task Runner 탐색기**:

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

각 클래스에 반복 되는 경고 mixin의 공용 속성의 모든 것을 확인 합니다. Mixin 개발 시에는 중복 제거의 우수 하지만 여전히 CSS 필요한 CSS 파일-잠재적 성능 문제를 보다 큰 그 결과, 비효율적으로 작성 합니다.

이제 다음으로 경고 mixin는 `.alert` 클래스를 변경 `@include` 를 `@extend` (확장으로 `.alert`이 아니라 `alert`):

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

Sass를 한 번 더 실행 하 고 결과 CSS를 검사 합니다.

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

속성을 필요에 따라 여러 번만 정의 되며 이제 및 CSS 더 생성 됩니다.

Sass 함수 및 조건부 논리 작업을 작은 값으로 비슷한에 포함 됩니다. 사실 두 언어의 기능에는 매우 비슷합니다.

## <a name="less-or-sass"></a>덜 또는 Sass?

중임을 덜 사용 또는 Sass 일반적으로 더 나은 인지에 대 한 합의 없습니다 (또는 심지어 것인지 원래 Sass 또는 Sass에서 최신 SCSS 구문을 선호). 아마도 가장 중요 한 결정은 하는 **이러한 도구 중 하나를 사용 하 여**방금 직접 코딩 CSS 파일을 반대로 합니다. 변경한 후 두 없는 의사 결정 및 Sass 다 적합 합니다.

## <a name="font-awesome"></a>Awesome 글꼴

CSS 전처리기의 외에도 다른 훌륭한 리소스 스타일 최신 웹 응용 프로그램에 대 한 글꼴 Awesome입니다. 글꼴 Awesome 웹 응용 프로그램에서 자유롭게 사용할 수 있는 500 개 이상의 스케일러블 벡터 아이콘을 제공 하는 도구 키트입니다. Bootstrap을 사용 하도록 원래 설계 된 했으나 의존 하지 않고이 프레임 워크 또는 JavaScript 라이브러리입니다.

Awesome 글꼴을 시작 하는 가장 쉬운 방법은 해당 공용 콘텐츠 배달 네트워크 (CDN) 위치를 사용 하 여에 대 한 참조를 추가 하는 것:

```html
<link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/font-awesome/4.3.0/css/font-awesome.min.css">
```

추가할 수 있습니다 또한 Visual Studio 프로젝트에 "종속성"을 추가 하 여에서 *bower.json*:

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

글꼴 Awesome 클래스를 일반적으로 접두사로 "fa-", 인라인 HTML 요소에 적용 하 여 아이콘을 추가할 응용 프로그램에 수 페이지에서 놀라운 글꼴에 대 한 참조를 만든 후 (예: `<span>` 또는 `<i>`).  예를 들어, 단순 목록 및 다음과 같은 코드를 사용 하 여 메뉴에 아이콘을 추가할 수 있습니다.

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

이렇게 하면 브라우저에서 다음 생성-각 항목 옆에 있는 아이콘을 확인 합니다.

![목록 아이콘](less-sass-fa/_static/list-icons-screenshot.png)

여기에 사용 가능한 아이콘의 전체 목록을 볼 수 있습니다.

http://fontawesome.io/icons/

## <a name="summary"></a>요약

최신 웹 응용 프로그램에는 점점 더 명확 하 고, 직관적 이며, 다양 한 장치에서에서 쉽게 사용할 수 있는 응답을 유연한 디자인 요구 합니다. 이러한 목표를 달성 하는 데 필요한 CSS 스타일 시트의 복잡성을 관리 덜 전처리기 like를 사용 하 여 또는 Sass 방법이 가장 좋습니다. 또한 같은 글꼴 놀라운 도구 키트가 텍스트 탐색 메뉴에 잘 알려진 아이콘을 신속 하 게 제공 하 고 응용 프로그램의 단추, 향상 전반적인 사용자 환경을.


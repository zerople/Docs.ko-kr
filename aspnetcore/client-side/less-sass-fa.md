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
ms.openlocfilehash: 4282e992ea80420181f9d7a2e3d6a55d6614c4de
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="introduction-to-styling-applications-with-less-sass-and-font-awesome-in-aspnet-core"></a>더 적은 노력으로 응용 프로그램 스타일 지정, Sass, 및 ASP.NET 코어에서 글꼴 놀라운 소개

으로 [Steve Smith](http://ardalis.com)

전반적인 환경 및 스타일 지정에 관한는 웹 응용 프로그램의 사용자는 점점 더 높은 기대 합니다. 최신 웹 응용 프로그램에는 자주 풍부한 도구 및 프레임 워크 정의 하 고의 모양과 느낌을 일관 된 방식에서 관리에 대 한 활용 합니다. 같은 프레임 워크 [부트스트랩](http://getbootstrap.com/) 스타일 및 웹 사이트에 대 한 레이아웃 옵션의 공통 집합을 정의 하기 위한 이동할 수 있습니다. 그러나 대부분의 특수 사이트 혜택을 받을 사이트의 인터페이스를 더 적합 하 게 해 주는 이미지가 아닌 아이콘에 쉽게 액세스할 수 있을 뿐만 아니라 효과적으로 정의 하 고 스타일 및 연계 스타일 시트 (CSS) 파일을 유지 하기 위해서는. 정답입니다 언어와 도구를 지 원하는 [적은](http://lesscss.org/) 및 [Sass](http://sass-lang.com/), 라이브러리와 같은 및 [글꼴 놀라운](http://fortawesome.github.io/Font-Awesome/),와 야 합니다.

## <a name="css-preprocessor-languages"></a>CSS 전처리기 언어

기본 언어를 사용 하는 환경을 개선 하기 위해 다른 언어로 컴파일되는 언어는 전처리기 라고 합니다. CSS에 대 한 두 개의 인기 있는 전처리기는: 덜 및 Sass 합니다.  이러한 전처리기 기능 변수 및 대규모의 복잡 한 스타일 시트의 용이해 지 며 중첩 된 규칙에 대 한 지원 등의 CSS을 추가 합니다. 변수를 같은 간단한에 대해서도 지원 부족 한 언어로 CSS은 매우 기본적인이를 부분과 CSS 파일 반복적이 고 그 결과입니다. 전처리기를 통해 실제 프로그래밍 언어의 기능을 추가 하면 더 나은 조직 스타일 지정 규칙을 제공 하 고 중복을 줄일 수 있습니다. Visual Studio 둘 다 없는 대 한 기본 제공 지원 및 Sass, 뿐 아니라 이러한 언어를 작업할 때 개발 환경을 더욱 개선할 수 있는 확장을 제공 합니다.

이 CSS 스타일 정보가 가독성과 전처리기 개선할 수 있는 방법을의 간단한 예를 살펴보세요.

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

작은 사용 하 여,이 다시 작성할 수 있습니다 모든, 복제 제거를 사용 하 여는 *mixin* ("혼합" 수 있기 때문에 그렇게 명명 된 클래스 또는 규칙 집합을 다른 속성):

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

## <a name="less"></a>덜

전처리기는 덜 CSS Node.js를 사용 하 여 실행 합니다. 작은 설치 하려면 노드 패키지 관리자 (npm) 명령 프롬프트에서 사용 하 여 (-g 의미 "전역"):

```console
npm install -g less
```

Visual Studio를 사용 하 여 시작할 수 있습니다 하나 이상의 Less 파일을 프로젝트에 추가 하 고 다음 컴파일 타임에 변수를 처리할 Gulp (또는 Grunt)를 구성 하 여 미만 이어야 합니다. 추가 *스타일* 폴더를 프로젝트에 새 덜 라는 파일을 추가 하 고 *main.less* 이 폴더에 있습니다.

![덜 파일 추가](less-sass-fa/_static/add-less-file.png)

추가 되 면 폴더 구조 코드는 다음과 같아야 합니다.

![폴더 구조](less-sass-fa/_static/folder-structure.png)

이제 몇 가지 기본 스타일 지정 CSS로 컴파일되고 Gulp 여 wwwroot 폴더에 배포 되는 파일을 추가할 수 있습니다.

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

`@base`다른 @-prefixed 항목은 변수입니다. 각각의 색을 나타냅니다. 제외 하 고 `@base`, 색 함수를 사용 하 여 설정 된: 밝게, 어둡게, 및 회전 합니다. 밝게 및 어둡게 거의 예상 대로; 수행 스핀 (색상표) 중심으로 다양 한 색의 색상을 조정합니다. 작은 프로세서는 때문에 이러한 변수 작동 하는 방법을 설명 하기 위해 어딘가에 사용법이 사용 되지 않는 변수를 무시 합니다. 클래스 `.baseColor`, 등에서는 변수가 생성 되는 CSS 파일에 각각의 계산된 된 값을 보여 줍니다.

### <a name="getting-started"></a>시작

만들기는 **npm 구성 파일** (*package.json*) 프로젝트 폴더에서 참조 하도록 편집 하 고 `gulp` 및 `gulp-less`:

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

프로젝트 폴더에 만듭니다는 **구성 파일 Gulp** (*gulpfile.js*) 자동화 된 프로세스를 정의 합니다.  나타내는 덜, 파일 및 작음 실행 되도록 작업을 맨 위에 있는 변수를 추가 합니다.

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

열기는 **작업 Runner 탐색기** (**보기 > 다른 Windows > 작업 Runner 탐색기**). 작업 간에 라는 새 작업을 표시 되어야 `less`합니다. 창을 새로 고쳐야 할 수 있습니다.

실행 된 `less` 작업에 여기 표시 된 출력이 표시:

![task runner가 적은](less-sass-fa/_static/less-task-runner.png)

*wwwroot/css* 폴더에는 이제 새 파일을 포함 *main.css*:

![생성 된 기본 css](less-sass-fa/_static/main-css-created.png)

열기 *main.css* 다음과 같은 표시 있습니다.

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

간단한 HTML 페이지를 추가 *wwwroot* 폴더 및 참조 *main.css* 동작의 색상표를 볼 수 있습니다.

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

확인할 수 있습니다를 180도 회전 하 `@base` 생성 하는 데 사용 `@background` 색상표 색의 반대편에 `@base`:

![덜 테스트 예제](less-sass-fa/_static/less-test-screenshot.png)

작은 중첩된 미디어 쿼리 뿐만 아니라 중첩 된 규칙에 대 한 지원을 제공합니다. 예를 들어 메뉴 CSS 규칙 세부 정보 표시 될 수와 같은 중첩 된 계층 구조 정의 처럼 이러한:

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

이상적으로 관련 된 스타일 규칙의 모든 됩니다 내에 존재 함께 CSS 파일 하는데 실제로 더 할 게 없습니다 규칙 및 아마도 블록 설명을 제외 하 고이 규칙을 적용 합니다.

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

이 경우의 모든 하위 요소 사항에 유의 `nav` 해당 범위 내에 포함 됩니다. 모든 반복의 부모 요소를 더 이상 (`nav`, `li`, `a`), 총 줄 수 삭제도 (하지만 중 일부를 두 번째 예에 있는 동일한 줄에 배치 하는 값의 결과). 것을 쉽게 조직으로,이 경우 명시적으로 제한 된 범위 내에서 지정된 된 UI 요소에 대 한 규칙을 모두 보려면을 off로 설정 파일의 나머지 부분에서 중괄호.

`&` 구문으로는 적은 선택기 기능, 및 현재 선택기 부모를 나타내는입니다. 따라서 내에서 {...} 블록 `&` 나타냅니다는 `a` 태그 이므로 `&:link` 같습니다 `a:link`합니다.

미디어 쿼리, 응답성이 뛰어난 디자인을 만드는 데 매우 유용 수에 영향을 많이 반복 및 복잡 한 CSS 합니다. 작은 하 라 수 클래스 내에 중첩할 수에 대 한 미디어 쿼리 있으므로 전체 클래스 정의 내에서 서로 다른 반복 될 필요가 없습니다 최상위 `@media` 요소입니다. 예를 들어 다음과 같습니다 CSS 응답 메뉴에 대 한.

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

이미 살펴본 작은 다른 기능은는 미리 정의 된 변수에서 생성 되는 스타일 특성을 허용 하는 수학 연산에 대 한 지원입니다. 이 모든 종속 값을 자동으로 변경 하 고 기본 변수를 수정할 수 있으므로 훨씬 쉽게 관련된 스타일을 업데이트할 수 있습니다.

특히 대규모 사이트에 대 한 CSS 파일 (및 미디어 쿼리를 사용 하는 경우에 특히), 매우 커질 수 시간이 지남에 따라 작업을 반환 하는 경향이 있습니다. Less 파일 정의할 수 있습니다 별도로 사용 하 여 함께 끌어온 다음 `@import` 지시문입니다. 작은 데도 사용할 수 있습니다를 개별 CSS 파일을 가져오며도 필요한 경우.

*Mixin* 수 매개 변수를 받아들이고 형태로 mixin 가드 특정 mixin 적용 시 정의 하는 선언적 방법을 제공 하는 조건부 논리를 지원 작습니다. Mixin 가드 사용 되는 일반적인 빛 방식에 따라 색을 조정 하려면 되거나 어두운 소스 색상입니다. Mixin 색에 대 한 매개 변수를 허용 하는 지정 된 경우 mixin 가드 해당 색에 기반 mixin 수정에 사용할 수 있습니다.

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

우리의 현재 지정 된 `@base` 의 값 `#663333`, 적은이 스크립트는 다음 CSS를 생성 합니다.

```css
.feature {
    background-color: #FFF;
    color: #663333;
}
```

다양 한 추가 기능을 제공 작은 있지만이 제공 해야이 전력의 몇 가지 아이디어 언어 전처리 합니다.

## <a name="sass"></a>Sass

Sass는 약간 다른 구문을 하면서도 동일한 기능을 대부분에 대 한 지원을 제공 하는, 비슷합니다. JavaScript 대신 Ruby를 사용 하 여 빌드하 있으며 다른 설치 요구 사항만 요구 하므로입니다. 원래 Sass 언어 중괄호 또는 세미콜론을 사용 하지 않고 있지만 대신 공백 및 들여쓰기를 사용 하 여 범위를 정의 합니다. Sass 버전 3에서에서 새로운 구문 도입 되기 **SCSS** "Sassy CSS ("). SCSS는 CSS 비슷합니다 수준 들여쓰기 및 공백 무시 하 고 대신 중괄호와 세미콜론을 사용 합니다.

Sass를 설치 하려면 일반적으로 사용자는 Ruby (사전 설치 Mac에서)를 먼저 설치을 실행 합니다.

```console
gem install sass
```

그러나 Visual Studio를 실행 하는 경우 있습니다 수 시작 Sass 거의 같은 방법으로 작은와 마찬가지로 하세요. 열기 *package.json* 에 "gulp sass" 패키지를 추가 하 고 `devDependencies`:

```json
"devDependencies": {
  "gulp": "3.9.1",
  "gulp-less": "3.3.0",
  "gulp-sass": "3.1.0"
}
```

다음으로 수정 *gulpfile.js* sass 변수 및 Sass 파일을 컴파일할 wwwroot 폴더에서 결과 저장 하는 작업을 추가 하려면:

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

Sass 파일을 추가할 수 이제 *main2.scss* 에 *스타일* 프로젝트의 루트에 폴더:

![scss 파일 추가](less-sass-fa/_static/add-scss-file.png)

열기 *main2.scss* 다음 줄을 추가 합니다.

```sass
$base: #CC0000;
body {
    background-color: $base;
}
```

모든 파일을 저장 합니다. 이제를 새로 고칠 때 **Task Runner 탐색기**, 표시는 `sass` 작업 합니다. 찾는 위치 하 고 실행 된 */wwwroot/css* 폴더입니다. 이제는 *main2.css* 이러한 내용 인 파일에:

```css
body {
    background-color: #CC0000;
}
```

Sass는 작은 수행 하는 이점을 제공 된 동일한 대부분의 중첩을 지원 합니다. 파일 함수에 의해 분할 되 고 사용 하 여 포함 된 `@import` 지시문:

```sass
@import 'anotherfile';
```

Sass에서는 mixin도 사용할 수는 `@mixin` 키워드를 정의 하 고 `@include` ,이 예제에서와 같이 포함 하도록 [sass lang.com](http://sass-lang.com):

```sass
@mixin border-radius($radius) {
    -webkit-border-radius: $radius;
    -moz-border-radius: $radius;
    -ms-border-radius: $radius;
    border-radius: $radius;
}

.box { @include border-radius(10px); }
```

Mixin, 외에도 Sass도 지원 하므로 상속의 개념 하나의 클래스를 다른 확장 합니다. Mixin, 하지만 더 적은 CSS 코드를 개념적으로 비슷합니다. 사용 하 여 수행 되는 `@extend` 키워드입니다. Mixin를 실행 하려면 다음을 추가 하면 *main2.scss* 파일:

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

출력을 검토 *main2.css* 실행 된 후의 `sass` 에서 작업이 **Task Runner 탐색기**:

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

각 클래스에 반복 되는 경고 mixin의 공용 속성의 모든를 확인 합니다. Mixin 개발 시에는 중복 제거 작업을 훌륭히 하지만, 필요한 CSS 파일-잠재적 성능 문제를 보다 큰 결과에서 중복 많이 CSS를 만들어서 계속 됩니다.

이제와 경고 mixin 대체는 `.alert` 클래스 하 고 변경 `@include` 를 `@extend` (확장를 `.alert`이 아니라 `alert`):

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

Sass, 실행 하 고 결과 CSS를 검사 합니다.

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

필요에 따라 횟수 만큼만 속성은 정의 하는 이제 되며 더 잘 CSS 생성 됩니다.

Sass 함수 및 작은 비슷한 조건부 논리 연산을 포함 됩니다. 실제로 두 언어의 기능은 매우 유사 합니다.

## <a name="less-or-sass"></a>덜 또는 Sass?

여전히 사용할지 여부를 결정을 덜 사용 나 Sass 일반적으로 더 나은 합의 없습니다 (또는 심지어 것인지 Sass 내에서 최신 SCSS 구문 또는 원래 Sass 선호). 아마도 가장 중요 한 결정은 하는 **다음이 도구 중 하나를 사용 하 여**만 직접 코딩 CSS 파일을 반대로 합니다. 수행한 후 둘 다 없는 의사 결정 및 Sass 다 적합 합니다.

## <a name="font-awesome"></a>놀라운 글꼴

CSS 프로세서 아니라 최신 웹 응용 프로그램 스타일 지정에 대 한 또 다른 훌륭한 리소스는 놀라운 글꼴 합니다. 글꼴 Awesome 웹 응용 프로그램에서 자유롭게 사용할 수 있는 500 개 스케일러블 벡터 아이콘을 제공 하는 도구 키트입니다. 원래 부트스트랩와 작동 하도록 설계 되었습니다 했으나 의존 하지 않고 모든 JavaScript 라이브러리 또는 해당 프레임 워크에서입니다.

놀라운 글꼴을 시작 하는 공용 콘텐츠 배달 네트워크 (CDN) 위치를 사용 하 여, 참조를 추가 하는 가장 쉬운 방법은:

```html
<link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/font-awesome/4.3.0/css/font-awesome.min.css">
```

추가할 수 있습니다도 Visual Studio 프로젝트에 "종속성"을 추가 하 여에서 *bower.json*:

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

글꼴 놀라운 클래스, 일반적으로 접두사로 "fa-", 인라인 HTML 요소에 적용 하 여 아이콘을 추가할 응용 프로그램에 수 페이지에 놀라운 글꼴에 대 한 참조를 만든 후 (예: `<span>` 또는 `<i>`).  예를 들어, 단순 목록 및 다음과 같은 코드를 사용 하 여 메뉴에 아이콘을 추가할 수 있습니다.

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

이렇게 하면 브라우저에서 다음이 생성-각 항목 옆의 아이콘에 유의 합니다.

![목록 아이콘](less-sass-fa/_static/list-icons-screenshot.png)

여기에 사용 가능한 아이콘의 전체 목록을 볼 수 있습니다.

http://fontawesome.io/icons/

## <a name="summary"></a>요약

최신 웹 응용 프로그램에는 점점 더 명확 하 고, 직관적으로 다양 한 장치에서에서 쉽게 사용할 수 있는 응답을 유체 디자인 요구 합니다. 이러한 목표를 달성 하는 데 필요한 CSS 스타일 시트의 복잡 한 관리 덜 전처리기 like를 사용 하 여 또는 Sass 방법이 가장 좋습니다. 또한 글꼴 놀라운 같은 도구 키트 잘 알려진 텍스트 탐색 메뉴 아이콘을 신속 하 게 제공 하 고 응용 프로그램의 전반적인 사용자 향상 단추 환경.

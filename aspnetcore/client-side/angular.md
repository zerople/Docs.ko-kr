---
title: "AngularJS를 사용 하 여 단일 페이지 응용 프로그램 (SPAs)에 대 한"
author: rick-anderson
description: "AngularJS를 사용 하 여 SPA 스타일 ASP.NET 응용 프로그램을 빌드하는 방법에 알아봅니다"
keywords: "ASP.NET Core, 예: AngularJS, SPA"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4b30576b-2718-4c39-9253-a59966747893
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/angular
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2c7929976f0c9f8284ab397b1a87d576bcdd15b0
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="using-angularjs-for-single-page-applications-spas-with-aspnet-core"></a>AngularJS를 사용 하 여 ASP.NET Core 있는 단일 페이지 응용 프로그램 (SPAs)


여 [Venkata Koppaka](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/) 및 [Scott Addie](https://scottaddie.com)

이 문서에서는 AngularJS를 사용 하 여 SPA 스타일 ASP.NET 응용 프로그램을 빌드하는 방법에 설명 합니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample)

## <a name="what-is-angularjs"></a>AngularJS 란?

[AngularJS](https://angularjs.org/) 는 일반적으로 단일 페이지 응용 프로그램 (SPAs) 작업에 사용 되는 Google에서 최신 JavaScript 프레임 워크입니다. AngularJS는 MIT 라이선스에 따라을 기반으로 개방형이 고 AngularJS의 개발 진행 상황을 줄 수 [해당 GitHub 리포지토리에](https://github.com/angular/angular.js)합니다. 라이브러리는 HTML 각도 모양의 대괄호를 사용 하기 때문에 각 라고 합니다.

AngularJS jQuery, 같은 DOM 조작 라이브러리가 아닙니다. 하지만 jQuery jQLite 호출의 일부를 사용 합니다. AngularJS은 주로 HTML 태그에 추가할 수 있는 선언적 HTML 특성 기반으로 합니다. AngularJS를 사용 하 여 브라우저에서 볼 수 있습니다는 [코드 학교 웹 사이트](https://www.codeschool.com/courses/shaping-up-with-angularjs) 또는 [W3Schools 웹 사이트](https://www.w3schools.com/angular/)합니다.

이 문서에서는 AngularJS 각 머리글은 대 한 몇 가지 메모와 합니다.

## <a name="getting-started"></a>시작

AngularJS를 사용 하 여 ASP.NET 응용 프로그램에서 시작 하려면 프로젝트의 일부로 설치 하거나 (CDN) 콘텐츠 배달 네트워크에서 참조 해야 합니다.

### <a name="installation"></a>설치

AngularJS 응용 프로그램에 추가 하는 방법은 여러 가지가 있습니다. Visual Studio에서 새 ASP.NET Core 웹 응용 프로그램 시작 하는 경우에 기본 제공을 사용 하 여 AngularJS을 추가할 수 있습니다 [Bower](bower.md) 지원 합니다. 열기 *bower.json*, 항목을 추가 하 고는 `dependencies` 속성:

<a name=angular-bower-json></a>

[!code-json[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/bower.json?highlight=9)]

저장 중에 *bower.json* 를 프로젝트의 각 파일을 설치할 *wwwroot/lib* 폴더입니다. 또한 내에서 표시 됩니다는 `Dependencies/Bower` 폴더입니다. 아래 스크린샷을 참조 하세요.

![AngularJS 프로젝트와 솔루션 탐색기](angular/_static/angular-solution-explorer.png)

다음에 추가 `<script>` 의 맨 아래에 대 한 참조는 `<body>` HTML 페이지의 섹션 또는 *_Layout.cshtml* 파일을 다음과 같이 합니다.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=4&range=48-52)]

프로덕션 응용 프로그램 AngularJS과 같은 공용 라이브러리에 Cdn을 사용 하는 것이 좋습니다. 이 이와 같은 여러 가지 Cdn 중 하나에서 AngularJS를 참조할 수 있습니다.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=10&range=53-67)]

에 대 한 참조가 있으면는 *angular.js* 스크립트 파일을 준비가 AngularJS를 사용 하 여 웹 페이지에서 시작 합니다.

## <a name="key-components"></a>주요 구성 요소

AngularJS와 같은 다양 한 주요 구성 요소에 포함 *지시문*, *템플릿*, *반복기*, *모듈*, * 컨트롤러*, *구성 요소*, *구성 요소 라우터* 등입니다. 이러한 구성 요소가 함께 작동 하 여 웹 페이지에 동작을 추가 하는 방법을 살펴보겠습니다.

### <a name="directives"></a>지시문

AngularJS를 사용 하 여 [지시문](https://docs.angularjs.org/guide/directive) 사용자 지정 특성 및 요소가 함께 HTML을 확장 합니다. AngularJS 지시문을 통해 정의 된 `data-ng-*` 또는 `ng-*` 접두사 (`ng` 짧은 각도). AngularJS 지시문의는 다음과 같은 두 종류가 있습니다.

   1. **기본 지시문**: 이러한 미리 정의 된 각 팀 및 AngularJS 프레임 워크의 일부가 됩니다.

   2. **사용자 지정 지시문**: 정의할 수 있는 사용자 지정 지시문 이들은 합니다.

모든 AngularJS 응용 프로그램에서 사용 되는 기본 지시문 중 하나는 `ng-app` 지시어가 AngularJS 응용 프로그램 시작 되도록 합니다. 이 지시문에 적용할 수는 `<body>` 태그 또는 본문의 자식 요소입니다. 작업의 예제를 살펴보겠습니다. ASP.NET 프로젝트에 있을 경우 추가 하거나 HTML 파일에는 `wwwroot` 폴더 또는 새 컨트롤러 동작 및 관련 된 보기를 추가 합니다. 이 예에서 추가한 새 `Directives` 작업 메서드를 `HomeController.cs`합니다. 관련된 보기에는 다음과 같습니다.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Directives.cshtml?highlight=5,7)]

이 샘플을 서로 독립적으로 유지 하기 위해 공유 레이아웃 파일을 사용 하지 합니다. 와 body 태그를 데코레이팅 것을 볼 수 있습니다는 `ng-app` 는 AngularJS 응용 프로그램 지시문이이 페이지를 표시 합니다. `{{2+2}}` 배울 수 있는 더에 대 한 잠시 후에 각 데이터 바인딩 식입니다. 이 응용 프로그램을 실행 하는 경우 결과 다음과 같습니다.

![간단한 각도 지시문](angular/_static/simple-directive.png)

다른 기본 AngularJS 지시문은 다음과 같습니다.

`ng-controller`어떤 보기에는 JavaScript 컨트롤러 연결을 결정 합니다.

`ng-model`HTML 요소 속성의 값을 바인딩할 모델을 결정 합니다.

`ng-init`현재 범위에 대 한 식 형식에서 응용 프로그램 데이터를 초기화 하는 데 사용 합니다.

`ng-if`제거 하거나 제공 된 식이 truthiness에 따라 DOM에 지정된 된 HTML 요소를 다시 만듭니다.

`ng-repeat`데이터 집합에 대해 HTML 블록을 반복합니다.

`ng-show`표시 하거나 제공 된 식에 따라 지정된 된 HTML 요소를 숨깁니다.

AngularJS에서 지원 되는 모든 기본 지시문의 전체 목록은을 참조 하십시오는 [AngularJS 설명서 웹 사이트에서 지시문 설명서 섹션](https://docs.angularjs.org/api/ng/directive)합니다.

### <a name="data-binding"></a>데이터 바인딩

AngularJS 제공 [데이터 바인딩](https://docs.angularjs.org/guide/databinding) 지원의-즉시 하나를 사용 하는 `ng-bind` 지시문 또는 바인딩 식 구문와 같은 데이터 `{{expression}}`합니다. AngularJS 항상 뷰 템플릿 사용 하 여 동기화에 모델의 데이터를에서 보관할 양방향 데이터 바인딩을 지원 합니다. 보기에 변경 내용을 모델에 자동으로 반영 됩니다. 마찬가지로, 보기에는 모델의 모든 변경 내용은 반영 됩니다.

HTML 파일 또는 컨트롤러 작업이 라는 해당 보기와 만들기 `Databinding`합니다. 보기에서 다음을 포함:

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Databinding.cshtml?highlight=8,9,10)]

공지 지시문 또는 데이터 바인딩을 사용 하 여 모델 값을 표시할 수 있습니다 (`ng-bind`). 결과 페이지는 다음과 같이 표시 됩니다.

![단순 데이터 바인딩](angular/_static/simple-databinding.png)

### <a name="templates"></a>템플릿

[템플릿](https://docs.angularjs.org/guide/templates) AngularJS에은 셨 겠지만 HTML 페이지로 데코레이팅 AngularJS 지시문 및 아티팩트입니다. AngularJS에서 서식 파일은 지시문, 식, 필터 및 보기를 구성 하기 위해 HTML와 결합 하는 컨트롤의 혼합 합니다.

서식 파일을 설명 하기 위해 다른 보기를 추가 하 고 다음 것을 추가 합니다.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Templates.cshtml?highlight=8,9,10)]

서식 파일에 같은 AngularJS 지시문 `ng-app`, `ng-init`, `ng-model` 및 데이터 바인딩 식 구문에 바인딩하는 `personName` 속성입니다. 아래 스크린샷에서 같이 보기 표시 브라우저에서 실행 됩니다.

![단순 템플릿 예 1](angular/_static/simple-templates-1.png)

입력된 필드 옆에 있는 텍스트를 동적으로 표시 됩니다 입력된 필드에 입력 하 여 이름을 변경한 경우 각도 양방향 데이터 바인딩을 동작을 보여 주는 업데이트 합니다.

![단순 템플릿 예제 2](angular/_static/simple-templates-2.png)

### <a name="expressions"></a>식

[식](https://docs.angularjs.org/guide/expression) AngularJS JavaScript와 유사한 코드 조각 내에 기록 되는 `{{ expression }}` 구문입니다. 이 식의 데이터에 바인딩된 HTML과 마찬가지로 `ng-bind` 지시문입니다. AngularJS 식 및 JavaScript 정규식 간의 주요 차이점은 식에 대해 평가 되는 AngularJS는 `$scope` AngularJS의 개체입니다.

Bind 아래 샘플에서 AngularJS 식 `personName` 간단한 JavaScript 식 계산:

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Expressions.cshtml?highlight=8,9,10)]

이 예제에서는 표시 되는 경우 브라우저에서 실행 되는 `personName` 데이터 및 계산의 결과:

![단순 식](angular/_static/simple-expressions.png)

### <a name="repeaters"></a>반복기

호출 하는 기본 지시문을 통해 수행 됩니다 AngularJS에 반복 `ng-repeat`합니다. `ng-repeat` 지시문 반복 데이터 배열의 길이 대해이 보기에서 지정된 된 HTML 요소를 반복 합니다. AngularJS에서 반복기 문자열 또는 개체의 배열에 대해 반복할 수 있습니다. 문자열 배열을 통해 반복 되는 샘플 사용 다음과 같습니다.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters.cshtml?highlight=8,10,11)]

[repeat 지시문](https://docs.angularjs.org/api/ng/directive/ngRepeat) 이 스크린 샷에 표시 된 개발자 도구에서 볼 수 있듯이 일련의 순서가 없는 목록에 목록 항목을 출력 합니다.

![반복기 예제](angular/_static/repeater.png)

개체의 배열을 통해 반복 하는 예제는 다음과 같습니다. `ng-init` 지시문 설정는 `names` 배열에 있는 각 요소 먼저 포함 된 개체 이름과 성을 합니다. `ng-repeat` 할당 `name in names`, 모든 배열 요소에 대해 목록 항목을 출력 합니다.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters2.cshtml?highlight=8,9,10,11,13,14)]

이 경우의 출력은 앞의 예와 동일 합니다.

각 루프를 실행 중에 위치에 따라 동작을 제공 하는 데 도움이 되는 일부 추가 지시문을 제공 합니다.

`$index`

사용 하 여 `$index` 에 `ng-repeat` 인덱스 루프를 현재 위치를 결정 하기 위해 루프 켜져 있습니다.

`$even` 및 `$odd`

사용 하 여 `$even` 에 `ng-repeat` 루프 루프에서 현재 인덱스에도 인덱싱된 행 인지 여부를 확인 합니다. 마찬가지로 사용 하 여 `$odd` 홀수 인덱스 행을 현재 인덱스 인지 확인 하려면.

`$first` 및 `$last`

사용 하 여 `$first` 에 `ng-repeat` 루프 루프에서 현재 인덱스의 첫 번째 행 인지 여부를 확인 합니다. 마찬가지로 사용 하 여 `$last` 현재 인덱스의 마지막 행 인지 확인 하려면.

다음은 보여 주는 샘플 `$index`, `$even`, `$odd`, `$first`, 및 `$last` 실행에서:

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters3.cshtml?highlight=14,15,16,17,18)]

출력 결과 다음과 같습니다.

![반복기 예제 2](angular/_static/repeaters2.png)

### <a name="scope"></a>$scope

`$scope`역할을 연결 합니다 (아래에 설명) 컨트롤러 및 보기 (템플릿)을 하는 JavaScript 개체가입니다. AngularJS에서 보기 서식 파일에 연결 된 값만 인식는 `$scope` 컨트롤러에는 개체입니다.

> [!NOTE]
> MVVM 전 세계에서의 `$scope` AngularJS의 개체는 종종 ViewModel로 정의 됩니다. AngularJS 팀 참조 하는 `$scope` 데이터 모델로 개체입니다. [AngularJS의 범위에 대 한 자세한 정보](https://docs.angularjs.org/guide/scope)합니다.

다음 속성을 설정 하는 방법을 보여 주는 간단한 예를은 `$scope` 별도 JavaScript 파일 *scope.js*:

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/scope.js?highlight=2,3)]

관찰 된 `$scope` 매개 변수는 2 번 줄에는 컨트롤러에 전달 합니다. 이 개체에 대해 알고 보기는. 3 줄에 "Mary Jane"을 "name" 이라는 속성이 설정 합니다.

특정 속성은 보기에서 찾을 수 없는 경우 어떻게 되나요? 아래에 정의 된 보기 "name" 및 "age" 속성은 참조 합니다.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Scope.cshtml?highlight=9,10,14)]

줄 9 식 구문을 사용 하 여 "name" 속성을 표시 하는 각 요청 하는 것에 주의 하십시오. 다음 줄 10 "age", 존재 하지 않는 속성 참조 합니다. 실행 중인 예제에는 나이 대 한 "Mary Jane" 및 nothing으로 설정 이름을 보여 줍니다. 누락 된 속성은 무시 됩니다.

![범위 예](angular/_static/scope.png)

### <a name="modules"></a>모듈

A [모듈](https://docs.angularjs.org/guide/module) AngularJS 컨트롤러, 서비스, 지시문 등의 컬렉션이 있습니다. `angular.module()` 함수 호출은 생성, 등록 및에 AngularJS 모듈을 검색 하는 데 사용 됩니다. 사용 하 여 모든 모듈에 포함 하는 AngularJS 팀 및 타사 라이브러리에서 제공 하 여 등록 해야는 `angular.module()` 함수입니다.

다음은 AngularJS에 새 모듈을 만들어야 하는 방법을 보여 주는 코드 조각입니다. 첫 번째 매개 변수는 모듈의 이름입니다. 두 번째 매개 변수는 다른 모듈에 종속성을 정의합니다. 이 문서의 뒷부분에 나오는 우리가 보여 줄 이러한 종속성을 전달 하는 방법을 `angular.module()` 메서드를 호출 합니다.

```javascript
var personApp = angular.module('personApp', []);
```

사용 하 여는 `ng-app` 지시문을 페이지에는 AngularJS 모듈을 나타냅니다. 모듈을 사용 하려면 모듈의 이름을 지정 `personApp` 이 예제에 `ng-app` 우리의 템플릿에서 합니다.

```html
<body ng-app="personApp">
```

### <a name="controllers"></a>컨트롤러

[컨트롤러](https://docs.angularjs.org/guide/controller) AngularJS는 코드에 대 한 항목의 첫 번째 지점입니다. `<module name>.controller()` 함수 호출은 만들고에서 AngularJS 컨트롤러를 등록 하는 데 사용 됩니다. `ng-controller` 지시문 HTML 페이지에는 AngularJS 컨트롤러를 나타내는 데 사용 됩니다. 각 컨트롤러의 역할 상태 및 데이터 모델의 동작을 설정 하는 (`$scope`). 컨트롤러는 DOM를 직접 조작 하 쓰일 수 없습니다.

새 컨트롤러를 등록 하는 코드의 코드 조각은 다음과 같습니다. `personApp` 조각에서 변수가 참조 하는 각도 모듈 2 번 줄에 정의 되어 있습니다.

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/controllers.js?highlight=2,5)]

사용 하 여 뷰는 `ng-controller` 지시문에는 컨트롤러 이름을 할당 합니다.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Controllers.cshtml?highlight=8,14)]

에 해당 하는 "Jane" 및 "Mary"의 페이지에 표시는 `firstName` 및 `lastName` 에 연결 된 속성은 `$scope` 개체:

![컨트롤러 예](angular/_static/controllers.png)

### <a name="components"></a>구성 요소

[구성 요소](https://docs.angularjs.org/guide/component) 각 1.5. x는 캡슐화 및 개별 HTML 요소를 만드는 기능에 대 한 허용 합니다. 각도 1.4.x.directive() 메서드를 사용 하 여 동일한 기능을 얻을 수 있습니다.

.Component() 메서드를 사용 하 여 개발 지시문 및 컨트롤러의 기능을 얻는 간소화 됩니다. 다른 이점은 다음과 같습니다. 범위 격리 모범 사례는 고유, 및 각도 2로 마이그레이션 훨씬 쉬워집니다. `<module name>.component()` 함수 호출은 만들고 AngularJS의 구성 요소를 등록 하는 데 사용 됩니다.

다음은 새 구성 요소를 등록 하는 코드 조각입니다. `personApp` 조각에서 변수가 참조 하는 각도 모듈 2 번 줄에 정의 되어 있습니다.

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/components.js?highlight=2,5,13)]

사용자 지정 HTML 요소를 표시 하에서는 보기입니다.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Components.cshtml?highlight=8)]

구성 요소에서 사용 하는 연결 된 템플릿:

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personcomponent.html?highlight=2,3)]

"Aftab" 및 "Ansari"에 해당 하는 페이지에 표시 된 `firstName` 및 `lastName` 에 연결 된 속성은 `vm` 개체:

![구성 요소 예제](angular/_static/components.png)

### <a name="services"></a>서비스

[서비스](https://docs.angularjs.org/guide/services) AngularJS에 일반적으로 사용 되는 각 응용 프로그램의 수명 내내 사용할 수 있는 파일에 추출 된 하는 공유 코드에 대 한 합니다. 서비스 지연 초기화는 없습니다 것 서비스의 인스턴스는 서비스에 의존 하는 구성 요소를 가져옵니다 사용 하지 않는 경우를 의미 합니다. 팩터리는 AngularJS 응용 프로그램에서 사용 하는 서비스의 예입니다. 팩터리를 사용 하 여 만들어집니다는 `myApp.factory()` 함수 호출, 여기서 `myApp` 은 모듈입니다.

다음은에서 AngularJS 팩터리를 사용 하는 방법을 보여 주는 예:

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/simpleFactory.js?highlight=1)]

이 팩터리가 컨트롤러에서를 호출 하려면 전달 `personFactory` 매개 변수로 `controller` 함수:

```javascript
personApp.controller('personController', function($scope,personFactory) {
  $scope.name = personFactory.getName();
});
```

### <a name="using-services-to-talk-to-a-rest-endpoint"></a>REST 끝점에 설명 하는 서비스를 사용 하 여

다음은 종단 간 예제에서에서 서비스 사용 AngularJS ASP.NET Core 웹 API 끝점 상호 작용할 수입니다. 이 예제에서는 웹 API에서 데이터를 가져오고 보기 서식 파일의 데이터를 표시 합니다. 부터 시작 하겠습니다 보기 먼저:

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Index.cshtml?highlight=5,8,10,17,18,19)]

Angular 모듈 호출에이 보기에는 `PersonsApp` 컨트롤러를 호출 하 고 `personController`합니다. 사용 하 여 `ng-repeat` 사람 목록에 대 한 반복 합니다. 줄 17-19에 세 개의 사용자 지정 JavaScript 파일을 참조 하는 것입니다.

*personApp.js* 파일 등록을 사용 하는 `PersonsApp` 모듈 및 구문을 이전 예제와 비슷합니다. 사용 하는 `angular.module` 여기서 작업 하는 모듈의 새 인스턴스를 만들 함수입니다.

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personApp.js?highlight=3)]

에 대해 살펴보겠습니다 *personFactory.js*아래, 합니다. 모듈의 호출 `factory` 메서드 팩터리를 만들어야 합니다. 줄 12 표시 기본 제공 각 `$http` 서비스 웹 서비스에서 사용자 정보를 검색 합니다.

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personFactory.js?highlight=6,7,12)]

*personController.js*, 모듈의 호출 `controller` 컨트롤러를 만드는 메서드를 합니다. `$scope` 개체의 `people` 속성 personFactory (라인 13)에서 반환 된 데이터에 할당 됩니다.

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personController.js?highlight=6,7,13)]

웹 API와 그 뒤에 있는 모델에 대해 간략히 살펴보고를 보겠습니다. `Person` 모델와 POCO (Plain Old CLR Object)은 `Id`, `FirstName`, 및 `LastName` 속성:

[!code-csharp[Main](angular/sample/AngularJSSample/src/AngularJSSample/Models/Person.cs)]

`Person` 컨트롤러의 JSON 형식 목록을 반환 `Person` 개체:

[!code-csharp[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Controllers/Api/PersonController.cs?highlight=9,10,19)]

응용 프로그램을에서 살펴보겠습니다.

![나머지 결과 표시 하는 컨트롤러](angular/_static/rest-bound.png)

있습니다 수 [GitHub에서 응용 프로그램의 구조 보기](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample)합니다.

> [!NOTE]
> 에 대 한 자세한 AngularJS 응용 프로그램을 구성을 참조 하십시오. [John 예 각도 스타일 가이드](https://github.com/johnpapa/angular-styleguide)

&nbsp;

> [!NOTE]
> AngularJS 모듈, 컨트롤러 팩터리를 만들려면 지시문 및 보기 파일 쉽게 반드시 확인 Sayed Hashimi 아웃 [Visual Studio에 대 한 서식 파일 팩 SideWaffle](http://sidewaffle.com/)합니다. Sayed Hashimi microsoft Visual Studio 웹 팀의 수석 프로그램 관리자 이며 SideWaffle 템플릿 "gold" 표준 것으로 간주 됩니다. 이 문서 작성 당시의 SideWaffle는 Visual Studio 2012, 2013 및 2015 수 있습니다.

### <a name="routing-and-multiple-views"></a>라우팅 및 다중 뷰

AngularJS 기반 SPA (단일 페이지 응용 프로그램) 탐색을 처리 하는 기본 제공 경로 공급자를 있습니다. AngularJS 라우팅을 사용 하려면 추가 해야 합니다는 `angular-route` Bower를 사용 하 여 라이브러리입니다. 볼 수는 [bower.json](#angular-bower-json) 는 우리는 이미 참조 프로젝트에이 문서의 시작 부분에 참조 된 파일입니다.

패키지를 설치 하면 추가 스크립트 참조 (*각도 route.js*) 보기에 있습니다.

이제 작성 된 하 고 탐색 하 여 추가 사용자 앱을 살펴보겠습니다. 첫째, 해 드립니다 응용 프로그램의 복사본을 만들어 새 `PeopleController` 라는 동작 `Spa` 및 해당 `Spa.cshtml` Index.cshtml 보기에 복사 하 여 보기는 `People` 폴더입니다. 에 대 한 스크립트 참조를 추가 `angular-route` (11 줄 참조). 추가적으로 `div` 으로 표시는 `ng-view` 지시문 (6 행 참조)를 뷰에 배치할 자리 표시자로 합니다. 일부 추가 사용을 *.js* 13-16으로 줄에 참조 되는 파일입니다.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Spa.cshtml?highlight=6,11,12,13,14,15,16)]

에 대해 살펴보겠습니다 *personModule.js* 파일 म 라우팅을 사용 하 여 모듈을 인스턴스화하는 방법을 볼 수 있습니다. 전달 `ngRoute` 모듈로 라이브러리로 합니다. 이 모듈 응용 프로그램의 라우팅을 처리 합니다.

[!code-javascript[Main](angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personModule.js)]

*personRoutes.js* 파일을 아래의 경로 공급자에 따라 경로 정의 합니다. 줄 4-7 효과적으로 한다는 것으로 때 사용 하 여 URL 탐색 정의 `/persons` 은 호출 템플릿을 사용 하 여 요청 `partials/personlist` 통해 작업을 수행 하 여 `personListController`합니다. 8-11 선은 나타냅니다는 경로 매개 변수를 사용 하는 세부 정보 페이지 `personId`합니다. URL 패턴 중 하 나와 일치 하지 않습니다, 각 기본적으로 `/persons` 보기.

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personRoutes.js?highlight=4,5,6,7,8,9,10,11,13)]

`personlist.html` 파일은 사용자 목록을 표시 하는 데 필요한 HTML만 포함 된 부분 뷰.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personlist.html?highlight=3)]

컨트롤러 모듈의를 사용 하 여 정의 된 `controller` 함수 *personListController.js*합니다.

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personListController.js?highlight=1)]

이 응용 프로그램을 실행 하 고 탐색 하는 `people/spa#/persons` 확인해 보겠습니다 URL:

![사용자 목록 보기](angular/_static/spa-persons.png)

하는 경우 이동할 세부 정보 페이지에 예를 들어 `people/spa#/persons/2`, 세부 정보 부분 뷰를 살펴봅니다.

![사용자 세부 정보 보기](angular/_static/spa-persons-2.png)

전체 소스 및 모든 파일에이 문서에 표시 되지 않습니다 볼 수 있습니다 [GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample)합니다.

### <a name="event-handlers"></a>이벤트 처리기

HTML dom에서 입력된 요소에 이벤트 처리 기능을 추가 하는 AngularJS 지시문의 여러 가지 다음은 AngularJS에 내장 되어 있는 이벤트의 목록입니다.

   * `ng-click`

   * `ng-dbl-click`

   * `ng-mousedown`

   * `ng-mouseup`

   * `ng-mouseenter`

   * `ng-mouseleave`

   * `ng-mousemove`

   * `ng-keydown`

   * `ng-keyup`

   * `ng-keypress`

   * `ng-change`

> [!NOTE]
> 사용 하 여 사용자 고유의 이벤트 처리기를 추가할 수는 [AngularJS 기능 사용자 지정 지시문](https://docs.angularjs.org/guide/directive)합니다.

방법을 살펴보겠습니다는 `ng-click` 이벤트 연결 됩니다. 라는 새 JavaScript 파일을 만듭니다 *eventHandlerController.js*, 다음을 추가 합니다.

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/eventHandlerController.js?highlight=5,6,7)]

새 확인 `sayName` 함수 `eventHandlerController` 위 5 줄에 있습니다. 모든 메서드를 수행 하는 작업에 이제는 환영 메시지를 통해 사용자에 게 JavaScript 경고를 표시 합니다.

아래의 보기 AngularJS 이벤트에는 컨트롤러 함수를 바인딩합니다. 줄 9에 있는 단추에는 `ng-click` 각도 지시문 적용 되었습니다. 호출 우리의 `sayName` 에 추가 된 함수는 `$scope` 개체는이 보기에 전달 합니다.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Events.cshtml?highlight=9)]

실행 중인 예제에서는 컨트롤러의 `sayName` 함수 단추를 클릭할 때 자동으로 호출 됩니다.

![Click 이벤트](angular/_static/events.png)

에 대 한 자세한 내용은 AngularJS 기본 제공 이벤트 처리기 지시문, 해야 h e a d에 [설명서 웹 사이트](https://docs.angularjs.org/api/ng/directive/ngClick) AngularJS의 합니다.

## <a name="additional-resources"></a>추가 리소스

* [Angular Docs](https://docs.angularjs.org)

* [Angular 2 정보](https://angular.io/)

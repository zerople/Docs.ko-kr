---
title: "ASP.NET Core에서 Knockout.js MVVM 프레임 워크 | Microsoft 문서"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: b20e3b23-1c51-47bf-adac-91b5048567e0
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/knockout
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 181277513ea2bcddabeb9aedf9e81ab2eadd29fb
ms.lasthandoff: 03/23/2017

---
# <a name="knockoutjs-mvvm-framework-in-aspnet-core"></a>ASP.NET Core에서 Knockout.js MVVM 프레임 워크

[Steve Smith](http://ardalis.com)

Knockout 복잡 한 데이터 기반 사용자 인터페이스를 만드는 과정을 간소화 하는 인기 있는 JavaScript 라이브러리입니다. 단독으로 또는 jQuery와 같은 다른 라이브러리와 함께 사용할 수 있습니다. 기본 목적은 UI를 변경 하는 경우는 모델이 업데이트 되는 UI 요소는 JavaScript 개체로 정의 하는 기본 데이터 모델에 바인딩할 및 그 반대의 경우도 마찬가지입니다. Knockout 웹 응용 프로그램의 클라이언트 쪽 동작 모델-뷰-MVVM () 패턴을 사용을 하면 됩니다. 녹아웃의 MVVM 구현은 작업할 때 배워야 하나는 두 가지 주요 개념은 관찰 가능 개체 및 바인딩을입니다.

## <a name="getting-started"></a>시작

Knockout 설치 하므로 단일 JavaScript 파일을로 배포 되 고 사용 하는 매우 간단 하 고 사용 하 여 [bower](bower.md)합니다. 이미가지고 있다고 가정 [bower](bower.md) 및 [gulp](using-gulp.md) 열고 구성 *bower.json* 프로젝트에서 ASP.NET 핵심 하 고 다음과 같이 knockout 종속성을 추가 합니다.

```json
{
  "name": "KnockoutDemo",
  "private": true,
  "dependencies": {
    "knockout" : "^3.3.0"
  },
  "exportsOverride": {
  }
}
```

이 위치를 수동으로 bower (보기 ‣ 다른 창 ‣ Task Runner 탐색기) 아래 Task Runner 탐색기를 열어 실행 한 다음 작업에서 마우스 오른쪽 단추로 클릭 bower에 실행을 선택 하는 다음을 수 있습니다. 결과 다음과 유사 하 게 표시 됩니다.

![Task Runner 탐색기에서 실행 중인 knockout bower](knockout/_static/bower-knockout.png)

이번에는 프로젝트의 보면 `wwwroot` 폴더 lib 폴더 아래에 설치 된 knockout 표시 됩니다.

![녹아웃 lib 폴더에 설치](knockout/_static/wwwroot-knockout.png)

사용자가 파일의 캐시 된 복사본은 고 따라서 전혀 다운로드 않아도 됩니다 가능성 증가 하면이 프로덕션 환경에 콘텐츠 배달 네트워크 또는 CDN을 통해 녹아웃 참조할 것이 좋습니다. Knockout는 몇 가지 Cdn에서의 Microsoft Ajax CDN을 비롯 한 여기에 제공 됩니다.

[http://ajax.aspnetcdn.com/ajax/knockout/knockout-3.3.0.js](http://ajax.aspnetcdn.com/ajax/knockout/knockout-3.3.0.js)

Knockout는 사용 된 페이지를 포함 하려면 추가 하기만 하면는 `<script>` 아무 곳에 나 호스트할 것 (응용 프로그램 또는 CDN을 통해)에서 파일을 참조 하는 요소:

```html
<script type="text/javascript" src="knockout-3.3.0.js"></script>
```

## <a name="observables-viewmodels-and-simple-binding"></a>관찰 가능 개체, 여러 viewmodel 들 및 단순 바인딩

JavaScript를 사용 하 여 DOM에 직접 액세스를 통해 웹 페이지에 요소를 조작 하 또는 jQuery와 같은 라이브러리를 사용 하 여 이미 익숙할 수도 있습니다. 일반적으로 특정 사용자 동작에 대 한 응답으로 요소 값을 직접 설정 하는 코드를 작성 하 여 이러한 유형의 동작이 수행 됩니다. Knockout을 사용 선언적 접근법이 대신 사용는 페이지에서 요소 속성에 바인딩된 개체에 있습니다. DOM 요소를 조작 하는 코드를 작성 하는 대신 사용자 작업 단순히 ViewModel 개체와 상호 작용할 Knockout을 맡고 페이지 요소가 동기화를 보장 합니다.

간단한 예로 아래 페이지 목록을 것이 좋습니다. 포함는 `<span>` 요소는 `data-bind` 텍스트 콘텐츠를 authorName에 바인딩되어야 있는지를 나타내는 특성입니다. 다음 변수 viewModel 단일 속성으로 정의 된 JavaScript 블록에서 `authorName`, 몇 가지 값으로 설정 합니다. 마지막으로, 호출 `ko.applyBindings` 수행 되 면이 viewModel 변수를 전달 합니다.

```html
<html>
<head>
    <script type="text/javascript" src="lib/knockout/knockout.js"></script>
</head>
<body>
    <h1>Some Article</h1>
    <p>
        By <span data-bind="text: authorName"></span>
    </p>
    <script type="text/javascript">
      var viewModel = {
        authorName: 'Steve Smith'
      };
      ko.applyBindings(viewModel);
    </script>
</body>
</html>
```

내용 브라우저에서 볼 때는 <span> 요소 viewModel 변수의 값으로 대체 됩니다.

![knockout 단순 바인딩](knockout/_static/simple-binding-screenshot.png)

이제 간단한 단방향 바인딩을 작업을 했습니다. 곳은 코드에서 우리 작성 않은 범위의 내용에 값을 할당 하는 JavaScript를 확인 합니다. ViewModel을 조작 하려는 경우 우리 수이 한 걸음 더 및 HTML 입력된 textbox를 추가 하 고 같은 해당 값에 바인딩할 하도록 합니다.

```html
<p>
    Author Name: <input type="text" data-bind="value: authorName" />
</p>
```

이 값이 실제로 입력된 상자에 바인딩되어 있는지 참조 페이지를 다시 로드 했습니다.

![knockout 입력된 바인딩](knockout/_static/input-binding-screenshot.png)

그러나 텍스트 상자에 값을 변경 하면 해당 값은 `<span>` 요소 변경 되지 않습니다. 그 이유는 무엇입니까?

문제는 아무 것도 통보는 `<span>` 업데이트 하는 것입니다. ViewModel을 단순히 업데이트 ViewModel의 속성 특별 한 형식에 래핑되는 경우가 아니면 충분 자체로 아닙니다. 사용 하 여 **관찰 가능 개체** 변경한 내용으로 나타나는 순서 대로 자동으로 업데이트 해야 하는 모든 속성에 대해 ViewModel에 있습니다. 사용 하 여 ViewModel을 변경 하 여 `ko.observable("value")` 정당한 "value" 대신 ViewModel 변경 될 때마다 해당 값에 바인딩된 모든 HTML 요소를 업데이트 합니다. Note 입력된 상자는 되므로 변경 내용을 입력할 때 요소에 표시 되지 않습니다 포커스를 잃게 될 때까지 해당 값을 업데이트 하지 않습니다.

> [!NOTE]
> 키를 누를 때마다 단순히 추가 된 후 실시간 업데이트에 대 한 지원을 추가 하기 위한 `valueUpdate: "afterkeydown"` 에 `data-bind` 특성의 내용입니다. 사용 하 여이 동작을 가져올 수도 있습니다 `data-bind="textInput: authorName"` 값을 즉시 업데이트를 얻으려고 합니다. 

우리의 viewModel ko.observable를 사용 하 여 업데이트 한 후:

```javascript
var viewModel = {
  authorName: ko.observable('Steve Smith')
};
ko.applyBindings(viewModel);
```

녹아웃 바인딩 다른 종류의 숫자를 지원합니다. 지금까지에 바인딩하는 방법을 살펴본 `text` 및 `value`합니다. 지정 된 모든 특성을 바인딩할 수 있습니다. 예를 들어, 앵커 태그를 사용 하 여 하이퍼링크를 만들려면는 `src` viewModel에 특성을 바인딩할 수 있습니다. 녹아웃 바인딩 함수를 지원합니다. 이 보여 주기 위해 viewModel 저자의 twitter 핸들을 포함 하도록 업데이트 하 고 작성자의 twitter 페이지에 대 한 링크 twitter 핸들을 표시 해 보겠습니다. 다음 세 단계로이 작업을 수행 합니다 했습니다.

먼저, 작성자의 이름 뒤에 괄호 알아봅니다 하이퍼링크를 표시 하는 HTML을 추가 합니다.

```html
<h1>Some Article</h1>
<p>
    By <span data-bind="text: authorName"></span>
    (<a data-bind="attr: { href: twitterUrl}, text: twitterAlias" ></a>)
</p>
```

다음으로 viewModel twitterUrl 및 twitterAlias 속성을 포함 하도록 업데이트 합니다.

```javascript
var viewModel = {
  authorName: ko.observable('Steve Smith'),
  twitterAlias: ko.observable('@ardalis'),
  twitterUrl: ko.computed(function() {
    return "https://twitter.com/";
  }, this)
};
ko.applyBindings(viewModel);
```

이 시점에서이 twitter 별칭에 대 한 올바른 URL로 이동 하려면 twitterUrl 아직 업데이트 하지 않은 – 방금 twitter.com를 가리키고 있는지 확인 합니다. 또한 새 Knockout 함수를 사용 하는 것을 볼 `computed`, twitterUrl에 대 한 합니다. 이 변경 하는 경우 모든 UI 요소 사용자에 게는 observable 함수입니다. 그러나 viewModel에서 다른 속성에 액세스할 수 있으면 해야 각 속성은 자체 문 있도록 viewModel 생성 방법을 변경 합니다.

수정 된 viewModel 선언은 다음과 같습니다. 이제 함수로 선언 됩니다. 각 속성 자체 이제 문에서, 세미콜론으로 종료 되는지 확인 합니다. 또한 twitterAlias 속성 값에 액세스 하려면 해당 참조 ()를 포함 하므로 실행 필요한 것을 확인 하십시오.

```javascript
function viewModel() {
  this.authorName = ko.observable('Steve Smith');
  this.twitterAlias = ko.observable('@ardalis');

  this.twitterUrl = ko.computed(function() {
    return "https://twitter.com/" + this.twitterAlias().replace('@','');
  }, this)
};
ko.applyBindings(viewModel);
```

결과 브라우저에서 예상 대로 작동 합니다.

![녹아웃 하이퍼링크](knockout/_static/hyperlink-screenshot.png)

녹아웃은 click 이벤트와 같은 특정 UI 요소 이벤트에 대 한 바인딩을 지원합니다. 이 옵션을 사용 하면 쉽게 하 고 선언적으로 응용 프로그램의 viewModel 내에서 함수를 UI 요소를 바인딩할 수 있습니다. 간단한 예로 단추를 추가할 수 있습니다를 클릭 하면 수정 저자의 twitterAlias 모두 대문자로 지정 해야 합니다.

첫째, 단추 추가, 단추의 바인딩을 클릭 이벤트와 viewModel에 추가 하려는 함수 이름을 참조 합니다.

```html
<p>
    <button data-bind="click: capitalizeTwitterAlias">Capitalize</button>
</p>
```

그런 다음, viewModel에 함수를 추가 하 고 viewModel의 상태를 수정 하려면 연결 합니다. 눈에 띌 twitterAlias 속성을 새 값으로 설정 하려면 우리 메서드로 호출 새 값을 전달 합니다.

```javascript
function viewModel() {
  this.authorName = ko.observable('Steve Smith');
  this.twitterAlias = ko.observable('@ardalis');

  this.twitterUrl = ko.computed(function() {
    return "https://twitter.com/" + this.twitterAlias().replace('@','');
  }, this);

  this.capitalizeTwitterAlias = function() {
    var currentValue = this.twitterAlias();
    this.twitterAlias(currentValue.toUpperCase());
  }
};
ko.applyBindings(viewModel);
```

코드를 실행 하 고 단추를 클릭 하면 예상 대로 표시 되는 링크를 수정 합니다.

![하이퍼링크를 대문자로](knockout/_static/hyperlink-caps-screenshot.png)

## <a name="control-flow"></a>제어 흐름

Knockout 조건부 및 반복 작업을 수행할 수 있는 바인딩이 포함 되어 있습니다. 반복 작업은 UI 목록, 메뉴 및 표 또는 테이블 데이터의 목록을 바인딩하는 데 특히 유용 합니다. Foreach 바인딩 배열에 대해 반복 됩니다. Observable 배열과 함께 사용 하는 경우 항목을 추가 하거나 UI 트리의 모든 요소를 다시 만들지 않고는 배열에서 제거할 때 UI 요소는 자동으로 업데이트 됩니다. 다음 예제에서는 게임 결과의 observable 배열을 포함 하는 새로운 viewModel을 사용 합니다. 두 개의 열을 사용 하는 간단한 테이블에 바인딩되어는 `foreach` 에 있는 바인딩는 `<tbody>` 요소입니다. 각 `<tr>` 요소 내에서 `<tbody>` gameResults 컬렉션의 요소에 바인딩됩니다.

```html
<h1>Record</h1>
<table>
    <thead>
        <tr>
            <th>Opponent</th>
            <th>Result</th>
        </tr>
    </thead>
    <tbody data-bind="foreach: gameResults">
        <tr>
            <td data-bind="text:opponent"></td>
            <td data-bind="text:result"></td>
        </tr>
    </tbody>
</table>
<script type="text/javascript">
  function GameResult(opponent, result) {
    var self = this;
    self.opponent = opponent;
    self.result = ko.observable(result);
  }

  function ViewModel() {
    var self = this;

    self.resultChoices = ["Win", "Loss", "Tie"];

    self.gameResults = ko.observableArray([
      new GameResult("Brendan", self.resultChoices[0]),
      new GameResult("Brendan", self.resultChoices[0]),
      new GameResult("Michelle", self.resultChoices[1])
    ]);
  };
  ko.applyBindings(new ViewModel);
</script>
```

이 지금 우리를 사용 하는 ViewModel 대문자 "V"로 (applyBindings 호출)에서 "new"를 사용 하 여 생성 하 고 기대 하기 때문에 확인 합니다. 를 실행 하면 페이지는 다음과 같은 출력 발생 합니다.

![knockout 레코드 뷰 모델](knockout/_static/record-screenshot.png)

관찰 가능한 컬렉션 작동을 보여 주기 위해 더 많은 기능을 추가 해 보겠습니다. Viewmodel 다른 게임의 결과 기록 하는 기능이 포함 하 고 단추와이 새로운 함수를 사용 하도록 일부 UI를 추가 합니다 수 있습니다.  첫째, addResult 메서드를 만들어 보겠습니다.

```javascript
// add this to ViewModel()
self.addResult = function() {
  self.gameResults.push(new GameResult("", self.resultChoices[0]));
}
```

이 메서드를 사용 하 여 단추를 바인딩하는 `click` 바인딩:

```html
<button data-bind="click: addResult">Add New Result</button>
```

브라우저에서 페이지를 열고 단추를 두 번 클릭 하 여 누를 때마다 새 테이블 행을 생성 합니다.

![결과 추가 합니다.](knockout/_static/record-addresult-screenshot.png)

몇 가지 방법으로 인라인으로 일반적으로 UI에서 또는 별도 폼에 새 레코드 추가를 지원 합니다. 모든 것은 편집할 수 있도록 텍스트 상자 및 dropdownlists 사용할 테이블을 쉽게 수정할 수 있습니다. 로 변경 하기만 하면는 `<tr>` 같이 요소:

```html
<tbody data-bind="foreach: gameResults">
    <tr>
      <td><input data-bind="value:opponent" /></td>
      <td><select data-bind="options: $root.resultChoices, value:result, optionsText: $data"></select></td>
    </tr>
</tbody>
```

`$root` 가능한 선택 항목은 노출 되는 루트 ViewModel 가리킵니다. `$data`이 경우 참조는 각각은 단순 문자열 resultChoices 배열의 개별 요소에 지정 된 컨텍스트-내에서 모든 현재 모델은를 참조 합니다.

이 변경으로 전체 표 편집 가능한 상태가 됩니다.

![편집 가능한 표](knockout/_static/editable-grid-screenshot.png)

Knockout을 사용 하지 않는 우리를 수행 하 여이 모든 jQuery를 사용 하 여 하지만 하지는 대개 거의 효율적입니다. Knockout는 UI 요소에 바인딩된 항목 ViewModel에 해당 데이터를 추적 하 고 추가, 제거 또는 업데이트 하는 이러한 요소에만 업데이트 합니다. JQuery 또는 직접 DOM 조작을 사용 하 여 직접이 위해 많은 노력을 걸리는 및 그렇더라도 하 고 싶으면 다음 테이블의 데이터를 기반으로 (예: 업체 레코드) 집계 결과 표시, 필요가 한 번 더 반복 하 고 HTML 요소를 구문 분석 합니다.  Knockout을 win 손실 레코드를 표시 어렵지 않습니다. ViewModel 자체 내에서 계산을 수행 하 고 다음 간단한 텍스트 바인딩으로 표시 우리와 `<span>`합니다.

Win 손실 레코드 문자열을 작성 하려면 계산된 observable를 사용할 수 있습니다. ViewModel 내 관측 가능한 속성에 대 한 참조는 함수 호출 이어야 하며, 그렇지 않으면은 검색 하지 않지만 관찰 가능 개체의 값 (예: `gameResults()` 하지 `gameResults` 표시 된 코드에서).

```javascript
self.displayRecord = ko.computed(function () {
  var wins = self.gameResults().filter(function (value) { return value.result() == "Win"; }).length;
  var losses = self.gameResults().filter(function (value) { return value.result() == "Loss"; }).length;
  var ties = self.gameResults().filter(function (value) { return value.result() == "Tie"; }).length;
  return wins + " - " + losses + " - " + ties;
}, this);
```

이 함수는 범위 내에서 바인딩할는 `<h1>` 페이지 맨 위에 있는 요소:

```html
<h1>Record <span data-bind="text: displayRecord"></span></h1>
```

결과:

![Win 손실](knockout/_static/record-winloss-screenshot.png)

행을 추가 또는 수정 된 행의 결과 열에서 선택된 된 요소는 창의 맨 위에 표시 된 레코드를 업데이트 합니다.

값에 바인딩 외에도 거의 모든 법적 JavaScript 식 바인딩에 사용할 수 있습니다. 예를 들어, UI 요소에만 값이 특정 임계값을 초과 하는 등 특정 조건에서 나타나야 하는 경우 지정할 수 있습니다이 논리적으로 바인딩 식에 포함 합니다.

```html
<div data-bind="visible: customerValue > 100"></div>
```

이 `<div>` 볼 수는 customerValue는 100을 초과 하는 경우.

## <a name="templates"></a>템플릿

Knockout는 쉽게 사용자 동작에서 UI를 분리 하거나 증분 방식으로 필요에 따라 대형 응용 프로그램에 UI 요소를 로드할 수 있도록 서식 파일을 지원 합니다. 단순히 템플릿으로 아웃 HTML을 가져오는 일에 데이터 바인딩 호출에 대 한 이름으로 서식 파일을 지정 하 여 각 행을 고유 템플릿을 위해 이전 예에서는 업데이트할 수 `<tbody>`합니다.

```html
<tbody data-bind="template: { name: 'rowTemplate', foreach: gameResults }">
</tbody>
<script type="text/html" id="rowTemplate">
  <tr>
      <td><input data-bind="value:opponent" /></td>
      <td><select data-bind="options: $root.resultChoices, value:result, optionsText: $data"></select></td>
  </tr>
</script>
```

Knockout은 jQuery.tmpl 라이브러리와 같은 다른 템플릿 엔진와 Underscore.js의 템플릿 엔진을 지원합니다.

## <a name="components"></a>구성 요소

구성 요소를 사용 하 여 구성 하 고 UI 코드가 종속 되어 있는 ViewModel 데이터와 함께 일반적으로 UI 코드를 다시 사용할 수 있습니다. 구성 요소를 만들려면 하면 해당 템플릿 및 해당 viewModel을 지정 하 고 이름을 지정 합니다. 이를 위해 `ko.components.register()`를 호출합니다. 템플릿 및 viewmodel 인라인을 정의 하는 것 외에도 로드 될 수와 같은 라이브러리를 사용 하 여 외부 파일에서 *require.js*매우 명확 하 고 효율적인 코드에서 발생 합니다.

## <a name="communicating-with-apis"></a>Api와의 통신

Knockout는 JSON 형식으로 모든 데이터로 작업할 수 있습니다. JQuery 지원에 검색 Knockout을 사용 하 여 데이터를 저장 하는 일반적인 방법은 `$.getJSON()` 함수 데이터를 검색 하 고 `$.post()` API 끝점에는 브라우저에서 데이터를 보내는 방법을 합니다. 물론, JSON 데이터를 주고받을 수 있는 다른 방법을 원한다 면 Knockout 작동 것도 합니다.

## <a name="summary"></a>요약

녹아웃은 ViewModel에 정의 된 클라이언트 응용 프로그램의 현재 상태를 UI 요소를 바인딩하는 간단 하 고 세련 된 방법을 제공 합니다. Knockout의 바인딩 구문을 처리 해야 하는 HTML 요소에 적용 하는 데이터 바인딩 특성을 사용 합니다. Knockout를 효율적으로 렌더링 및 UI 요소를 추적 하 여 큰 데이터 집합을 업데이트 이며 요소에 영향을 받는 변경 사항을 처리 합니다. 대규모 응용 프로그램 템플릿 및 외부 파일에서 요청 시 로드할 수 있는 구성 요소를 사용 하 여 UI 논리를 중단할 수 있습니다. 현재 버전 3에서는 녹아웃 리치 클라이언트 상호 작용을 필요로 하는 웹 응용 프로그램을 향상 시킬 수 있는 안정적인 JavaScript 라이브러리입니다.


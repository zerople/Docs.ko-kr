---
title: "세부 정보를 검사 하 고 메서드를 삭제 합니다. | Microsoft 문서"
author: rick-anderson
description: "세부 정보 컨트롤러 메서드와 뷰를 간단한 ASP.NET 핵심 MVC 앱에서."
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: article
ms.assetid: 870192b4-8d4f-45c7-8c14-83d02bc0ad79
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/details
translationtype: Machine Translation
ms.sourcegitcommit: f93c93002fec0088a7040cd53f31fd5b5a62fea7
ms.openlocfilehash: 2224062796bcf10c90aa6edca817875408375b1d
ms.lasthandoff: 03/23/2017

---
# <a name="examining-the-details-and-delete-methods"></a>세부 정보를 검사 하 고 메서드를 삭제 합니다.

[Rick Anderson](https://twitter.com/RickAndMSFT)

영화 컨트롤러를 열고 검사는 `Details` 메서드:

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_details)]

메서드를 호출 하는 HTTP 요청을 보여 주는 메모를 추가 하는 작업 메서드에이 생성 하는 MVC 스 캐 폴딩 엔진입니다. 이 세 개의 URL 세그먼트로 된 GET 요청이 경우에 `Movies` 컨트롤러는 `Details` 메서드 및 `id` 값입니다. 이러한 세그먼트 시작에 정의 된 것을 기억 하십시오.

[!code-csharp[주](start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

EF를 사용 하면 쉽게 사용 하 여 데이터를 검색 하는 `SingleOrDefaultAsync` 메서드. 메서드에 구성 하는 중요 한 보안 기능 코드는 검색 방법에서 발견 한 편의 영화 것으로 작업을 수행 하기 전에 확인 하는 합니다. 예를 들어 해커 오류가 발생할 수 사이트로 링크에서 만든 URL을 변경 하 여 `http://localhost:xxxx/Movies/Details/1` 와 같이 `http://localhost:xxxx/Movies/Details/12345` (또는 실제 영화는 표시 되지 않은 다른 값)입니다. Null 영화에 대 한 확인 하지 않은 경우 응용 프로그램 예외를 throw 합니다.

검사는 `Delete` 및 `DeleteConfirmed` 메서드.

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete)]

이때는 `HTTP GET Delete` 메서드는 지정한 동영상 삭제 되지 않고 반환 합니다 동영상의 보기 (HttpPost)를 제출할 수 있습니다 삭제 합니다. GET에 대 한 응답에서 delete 작업 수행 요청 하거나 편집 작업을 수행 하는 매우 만드는 작업 또는 데이터를 변경 하는 기타 작업이 보안 허점을 열립니다.

`[HttpPost]` 메서드는 데이터를 삭제 하는 이름이 `DeleteConfirmed` 고유 서명 또는 이름을 HTTP POST 메서드를 제공 합니다. 두 메서드 시그니처는 다음과 같습니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]


공용 언어 런타임 (CLR) 오버 로드 된 메서드는 시그니처가 고유 매개 변수 (메서드 이름은 같지만 서로 다른 매개 변수 목록)에 필요 합니다. 그러나 여기 필요한 두 개의 `Delete` 메서드-GET-과 게시물에 대 한 동일한 매개 변수 시그니처에 있는 합니다. (둘 다 필요 단일 정수를 매개 변수로 허용 합니다.)

두 가지 방법으로이 문제에, 하나는 메서드를 서로 다른 이름을 지정 하는 것입니다. 앞의 예제에서 줘 스 캐 폴딩 메커니즘입니다. 그러나이 작은 문제가 발생 되었습니다: ASP.NET URL의 세그먼트 작업 메서드 이름으로 매핑되고 메서드 이름을 바꾸면 정상적으로 라우팅 못할 해당 메서드를 찾을 수 있습니다. 솔루션을 추가 하는 예제에서 확인할 수는 `ActionName("Delete")` 특성을 `DeleteConfirmed` 메서드가 있습니다. 해당 특성이 매핑하기 위해 라우팅 시스템에 대 한 POST 요청에 대 한 /Delete/를 포함 하는 URL을 찾을 수 있도록는 `DeleteConfirmed` 메서드.

동일한 이름 및 시그니처가 있는 메서드에 대 한 또 다른 일반적인 작업 주위 인위적으로 POST 메서드 추가 포함 하도록의 시그니처를 변경 하는 것 (사용 되지 않는) 매개 변수입니다. 즉, 어떤 작업이 이루어졌는지 이전 게시물에 추가 하는 경우는 `notUsed` 매개 변수입니다. 여기에 대해 동일한 작업을 수행할 수는 `[HttpPost] Delete` 메서드:

```csharp
// POST: Movies/Delete/6
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

ASP.NET 핵심 MVC에 대 한이 소개 완료 주셔서 감사 합니다. 두면 설명을 드립니다. [MVC 및 EF 코어 시작](xref:data/ef-mvc/intro) 이 자습서에는 뛰어난 부합 됩니다.

>[!div class="step-by-step"]
[이전](validation.md)

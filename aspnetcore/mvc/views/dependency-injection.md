---
title: "보기에는 종속성 주입"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 80fb9e43-e4db-4af2-b2a8-e1364a712f69
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/dependency-injection
ms.openlocfilehash: 05d64858dd70b45a1e2bb90a86ab3cbdc85264b1
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="dependency-injection-into-views"></a>보기에는 종속성 주입

으로 [Steve Smith](http://ardalis.com)

ASP.NET Core 지원 [종속성 주입](xref:fundamentals/dependency-injection) 뷰로 합니다. 이 지역화 또는 데이터 요소 보기를 채우는 경우에 필요 같은 보기 관련 서비스에 대 한 유용할 수 있습니다. 유지 관리 하는 것이 좋습니다 [문제의 분리](http://deviq.com/separation-of-concerns) 컨트롤러와 뷰 사이입니다. 컨트롤러에서 대부분의 보기를 표시 하는 데이터에 전달 되어야 합니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample)

## <a name="a-simple-example"></a>간단한 예

서비스를 사용 하 여 보기에 삽입할 수는 `@inject` 지시문입니다. 생각할 수 있으며 `@inject` 속성을 보기에 추가 하 고 채우기 DI를 사용 하는 속성으로.

에 대 한 구문 `@inject`:`@inject <type> <name>`

예로 `@inject` 실행에서:

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

이 보기의 목록이 표시 됩니다. `ToDoItem` 인스턴스에 대 한 전체 통계를 보여 주는 요약 정보와 함께 합니다. 요약 채워집니다는 삽입 된에서 `StatisticsService`합니다. 이 서비스의 종속성 주입에 등록 되어 `ConfigureServices` 에 *Startup.cs*:

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

`StatisticsService` 집합에 대해 몇 가지 계산을 수행한 `ToDoItem` 리포지토리를 통한 액세스 하는 인스턴스:

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,26)]

샘플 리포지토리는 메모리 내 컬렉션을 사용합니다. 위에 표시 된 구현 (에서 작동 하는 모든 데이터를 메모리에에서) 원격으로 액세스, 대규모 데이터 집합에 대 한 권장 되지 않습니다.

샘플 데이터를 보기에 바인딩된 모델 및 보기에 포함할 서비스를 표시 합니다.

![총 항목 나열 표시 작업을 실행 하려면 항목과 평균 우선 순위는 우선 순위 수준 및 완료를 나타내는 부울 값을 사용 하 여 작업의 목록이 완료 합니다.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a>조회 데이터 채우기

보기 주입 UI 요소를 드롭다운 목록에서 옵션을 채우는 데 유용할 수 있습니다. 성별, 상태 및 기타 기본 설정을 지정 하기 위한 옵션을 포함 하는 사용자 프로필 양식을 것이 좋습니다. 컨트롤러를 각 옵션의이 집합에 대 한 데이터 액세스 서비스를 요청 하 고 모델을 채울 필요 폼 표준 MVC 방식을 사용 하 여 렌더링 또는 `ViewBag` 각 바인딩하는 옵션 집합을 사용 합니다.

다른 접근 방식은 옵션이 적용 하 여 보기에 직접 서비스를 삽입 합니다. 이 뷰 자체에이 보기 요소 생성 논리는 이동 하는 컨트롤러에 필요한 코드의 양을 최소화 합니다. 컨트롤러 동작 프로필 편집 폼을 표시만 폼 프로필 인스턴스를 전달 해야 합니다.

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

이러한 기본 설정을 업데이트 하는 데 사용 하는 HTML 폼 속성에 대 한 드롭다운 목록이 포함 됩니다.

![이름, 성별, 상태 및 좋아하는 색을 입력할 수는 폼으로 프로필 보기를 업데이트 합니다.](dependency-injection/_static/updateprofile.png)

이러한 목록 보기에 삽입 되었는지를 서비스에서 채워집니다.

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

`ProfileOptionsService` 는이 형식에 필요한 데이터를 제공 하도록 설계 된 UI 수준 서비스:

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

>[!TIP]
> 종속성 주입을 통해 요청 하는 형식을 등록 하도록 설정 된 `ConfigureServices` 에서 메서드 *Startup.cs*합니다.

## <a name="overriding-services"></a>서비스를 재정의합니다.

새로운 서비스를 삽입 하는 것 외에도이 방법은 재정의 하는 페이지에서 이전에 삽입 된 서비스를 데도 수 있습니다. 아래 그림에서는 첫 번째 예에 사용 된 페이지에서 모든 사용 가능한 필드를 보여 줍니다.

![Intellisense 상황에 맞는 메뉴에서 형식화 된 @ 기호를 Html, 구성 요소, StatsService, 및 Url 필드를 나열 합니다.](dependency-injection/_static/razor-fields.png)

기본 필드를 포함 하는 볼 수 있듯이 `Html`, `Component`, 및 `Url` (으로 `StatsService` म 삽입 하는). 예를 들어 기본 HTML 도우미를 사용자의 대체 하 려 했다면, 수행할 수 있습니다 쉽게 사용 하 여 변경할 `@inject`:

[!code-html[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

기존 서비스를 확장 하려는 경우에서 상속 하거나 자신의 기존 구현을 배치 하는 동안 단순히이 기법을 사용할 수 있습니다.

## <a name="see-also"></a>참고 항목

* Simon Timms 블로그: [보기에 조회 데이터 가져오기](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)

---
title: "보기에 종속성 주입 | Microsoft 문서"
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
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 92251623fb139008374a6e1f481d01882850e98f
ms.lasthandoff: 03/23/2017

---
# <a name="dependency-injection-into-views"></a>보기에 종속성 주입

[Steve Smith](http://ardalis.com)

ASP.NET Core 지원 [종속성 주입](../../fundamentals/dependency-injection.md) 보기에 있습니다. 이 지역화 또는 데이터 요소 보기를 채우는 경우에 필요와 같은 보기 전용 서비스에 대 한 유용할 수 있습니다. 유지 관리 하는 것이 좋습니다 [중요 한 부분의 분리](http://deviq.com/separation-of-concerns) 컨트롤러와 뷰 사이입니다. 대부분의 데이터에 표시 된 사용자 보기 컨트롤러에서에 전달 되어야 합니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample)

## <a name="a-simple-example"></a>간단한 예

사용 하 여 보기에는 서비스를 삽입할 수는 `@inject` 지시문입니다. 상상할 수 있는 `@inject` DI를 사용 하 여 속성을 채우는 및 속성을 보기에 추가 합니다.

구문은 `@inject`:   `@inject <type> <name>`

예로 `@inject` 작동에서 합니다.

[!code-csharp[주](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

이 보기의 목록이 표시 됩니다. `ToDoItem` 전체 통계를 보여 주는 요약 정보와 함께 인스턴스. 요약 채워집니다는 삽입 된에서 `StatisticsService`합니다. 이 서비스의 종속성 주입을 위한 등록 `ConfigureServices` 에서 *Startup.cs*:

[!code-csharp[주](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

`StatisticsService` 집합에 대해 몇 가지 계산을 수행한 `ToDoItem` 리포지토리를 통한 액세스 하는 인스턴스:

[!code-csharp[주](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,26)]

샘플 리포지토리는 메모리 내 컬렉션을 사용합니다. 위에 표시 된 구현 (에서 작동 하는 모든 데이터를 메모리에에서) 원격으로 액세스, 대규모 데이터 집합에 권장 되지 않습니다.

이 샘플에는 보기에 바인딩된 모델 및 보기를 삽입 하는 서비스에서 데이터가 표시 됩니다.

![총 항목 나열 표시 작업을 실행 하려면 항목, 평균 우선 순위 및 우선 순위 수준 및 완료를 나타내는 부울 값 작업 목록이 완료 합니다.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a>조회 데이터 채우기

보기 주입 드롭다운 목록과 같은 UI 요소에 대 한 옵션을 채우는 데 유용할 수 있습니다. 성별, 상태 및 기타 기본 설정을 지정 하기 위한 옵션을 포함 하는 사용자 프로필 양식을 것이 좋습니다. 컨트롤러를 각 옵션의이 집합에 대 한 데이터 액세스 서비스를 요청 하 고 다음 모델을 채우는 폼 표준 MVC 접근 방식을 사용 하 여 렌더링 해야 또는 `ViewBag` 각 바인딩될 수 있는 옵션 집합을 사용 합니다.

서비스 옵션을 얻으려면 보기에 직접 삽입 하는 또 다른 방법은 합니다. 이렇게 하면 뷰 자체에이 보기 요소 생성 논리는 이동 하는 컨트롤러에 필요한 코드의 양을 최소화 합니다. 컨트롤러 작업 프로필 편집 폼을 표시만 프로필 인스턴스 전달 해야 합니다.

[!code-csharp[주](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

이러한 기본 설정을 업데이트 하는 데 사용 하는 HTML 폼의 속성에 대 한 드롭다운 목록이 포함 됩니다.

![이름, 성별, 상태 및 좋아하는 색의 항목 수 있는 폼으로 프로필 보기를 업데이트 합니다.](dependency-injection/_static/updateprofile.png)

이러한 목록 보기에 삽입 되었는지는 서비스에서 채워집니다.

[!code-csharp[주](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

`ProfileOptionsService` 는이 폼에 필요한 데이터만 제공 하도록 설계 하는 UI 수준 서비스:

[!code-csharp[주](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

>[!TIP]
> 종속성 주입을 통해 요청 하는 형식을 등록 잊지는 `ConfigureServices` 에서 메서드 *Startup.cs*합니다.

## <a name="overriding-services"></a>서비스를 재정의합니다.

새 서비스를 삽입 하는 것 외에도이 기술은 페이지에서 이전에 삽입 된 서비스를 재정의 하 데도 수 있습니다. 아래 그림에는 첫 번째 예제에 사용 되는 페이지에 포함 된 모든 사용 가능한 필드 보여 줍니다.

![Intellisense 상황에 맞는 메뉴에는 형식화 된 @ 기호 Html, 구성 요소, StatsService, 및 Url 필드를 나열 합니다.](dependency-injection/_static/razor-fields.png)

기본 필드를 포함 하는 볼 수 있듯이 `Html`, `Component`, 및 `Url` (인스턴스뿐만 아니라 `StatsService` 삽입 했습니다). 예를 들어 자신의 기본 HTML 도우미를 대체 하려면, 수 쉽게 사용 하 여 있습니다 `@inject`:

[!code-html[주](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

기존 서비스를 확장 하려는 경우에서 상속 하거나 기존 구현에 직접 배치 하는 동안이 기법을 간단히 사용할 수 있습니다.

## <a name="see-also"></a>참고 항목

* Simon Timms 블로그: [보기에 조회 데이터 가져오기](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)


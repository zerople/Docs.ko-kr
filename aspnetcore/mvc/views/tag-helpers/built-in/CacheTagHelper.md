---
title: "ASP.NET Core MVC에서에서 태그 도우미를 캐시 합니다."
author: pkellner
description: "캐시 태그 도우미를 사용 하는 방법을 보여 줍니다."
keywords: "ASP.NET Core, 태그 도우미"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: c045d485-d1dc-4cea-a675-46be83b7a012
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/CacheTagHelper
ms.openlocfilehash: 31c362a70e44c7178efe1c35b28a02fd712ac2b4
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2017
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a>ASP.NET Core MVC에서에서 태그 도우미를 캐시 합니다.

작성자: [Peter Kellner](http://peterkellner.net) 


캐시 태그 도우미의 내부 ASP.NET Core 캐시 공급자를 해당 콘텐츠를 캐시 하 여 ASP.NET Core 응용 프로그램의 성능이 크게 향상 하는 기능을 제공 합니다.

Razor 뷰 엔진 기본 설정 `expires-after` 20 분입니다.

날짜/시간을 캐시 하는 다음 Razor 태그:

```cshtml
<Cache>@DateTime.Now<Cache>
```

첫 번째 요청을 포함 하는 페이지 `CacheTagHelper` 현재 날짜/시간을 표시 됩니다. 추가 요청은 캐시 (기본값: 20 분)을 만료 되거나 메모리가 중에 의해 제거 될 때까지 캐시 된 값이 표시 합니다.

다음 특성을 가진 캐시 기간을 설정할 수 있습니다.

## <a name="cache-tag-helper-attributes"></a>캐시 태그 도우미 특성

- - -

### <a name="enabled"></a>사용    


| 특성 유형    | 유효한 값      |
|----------------   |----------------   |
| boolean           | "true" (기본값)  |
|                   | "false"   |


캐시 태그 도우미 포함 되는 콘텐츠가 캐시 되 고 있는지 여부를 결정 합니다. 기본값은 `true`입니다.  경우 설정 `false` 이 캐시 태그 도우미 렌더링된 된 출력에 캐싱 영향을 미치지 것입니다.

예제:

```cshtml
<Cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="expires-on"></a>만료 

| 특성 유형    | 예제 값     |
|----------------   |----------------   |
| DateTimeOffset    | "@new DateTime(2025,1,29,17,02,0)"    |


절대 만료 날짜를 설정합니다. 다음 예제는 2025 년 1 월 29, 오후 5시 02분 될 때까지 캐시 태그 도우미의 콘텐츠를 캐시 합니다.

예제:

```cshtml
<Cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="expires-after"></a>이후에 만료

| 특성 유형    | 예제 값     |
|----------------   |----------------   |
| TimeSpan    | "@TimeSpan.FromSeconds(120)"    |


콘텐츠를 캐시할 첫 번째 요청 시간에서 시간을 설정 합니다. 

예제:

```cshtml
<Cache expires-on="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="expires-sliding"></a>슬라이딩 만료

| 특성 유형    | 예제 값     |
|----------------   |----------------   |
| TimeSpan    | "@TimeSpan.FromSeconds(60)"     |


액세스 하지 않은 경우 캐시 엔트리를 제거 해야 하는 시간을 설정 합니다.

예제:

```cshtml
<Cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="vary-by-header"></a>vary 헤더

| 특성 유형    | 예제 값                |
|----------------   |----------------               |
| 문자열            | "사용자-에이전트"                  |
|                   | "사용자 에이전트, 콘텐츠 인코딩" |

변경 될 때 캐시 새로 고침을 트리거하는 헤더 값의 쉼표로 구분 된 목록 또는 단일 헤더 값을 허용 합니다. 다음 예제에서는 헤더 값을 모니터링 `User-Agent`합니다. 예제는에 대 한 콘텐츠를 캐시 하는 모든 다른 `User-Agent` 웹 서버에 제공 합니다.

예제:

```cshtml
<Cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="vary-by-query"></a>다 쿼리

| 특성 유형    | 예제 값                |
|----------------   |----------------               |
| 문자열            | "확인"                |
|                   | "모델 만들기" |

헤더 값이 변경 될 때 캐시 새로 고침을 트리거하는 헤더 값의 쉼표로 구분 된 목록 또는 단일 헤더 값을 허용 합니다. 다음 예의 값을 검사 `Make` 및 `Model`합니다.

예제:

```cshtml
<Cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="vary-by-route"></a>다 경로

| 특성 유형    | 예제 값                |
|----------------   |----------------               |
| 문자열            | "확인"                |
|                   | "모델 만들기" |

경로 데이터 매개 변수 값 변경 하는 경우 캐시 새로 고침을 트리거하는 헤더 값의 쉼표로 구분 된 목록 또는 단일 헤더 값을 허용 합니다. 예제:

*Startup.cs* 

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```
  
*Index.cshtml*

```cshtml
<Cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="vary-by-cookie"></a>다 쿠키

| 특성 유형    | 예제 값                |
|----------------   |----------------               |
| 문자열            | ". AspNetCore.Identity.Application "                |
|                   | ". AspNetCore.Identity.Application,HairColor " |

헤더 값 (s) 변경 하는 경우 캐시 새로 고침을 트리거하는 헤더 값의 쉼표로 구분 된 목록 또는 단일 헤더 값을 허용 합니다. 다음 예제에서는 ASP.NET Id와 연관 된 쿠키를 살펴봅니다. 사용자가 인증 하는 경우 캐시 새로 고침을 트리거하는 요청 쿠키 설정 해야 합니다.

예제:

```cshtml
<Cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="vary-by-user"></a>사용자가 변경

| 특성 유형    | 예제 값                |
|----------------   |----------------               |
| 부울             | "true"                  |
|                     | "false" (기본값) |

캐시에 로그인 한 사용자 (또는 컨텍스트 보안 주체)이 변경 될 때 다시 설정 해야 여부를 지정 합니다. 현재 사용자의 요청 컨텍스트 보안 주체 라고도 하 고 참조 하 여 Razor 보기에서 볼 수 있습니다 `@User.Identity.Name`합니다.

다음 예에서는 현재 로그인 한 사용자를 살펴봅니다.  

예제:

```cshtml
<Cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

이 특성을 사용 하 여 로그인 및 로그 아웃 주기를 통해 캐시에서 콘텐츠를 유지 관리 합니다.  사용 하는 경우 `vary-by-user="true"`, 로그인 및 로그 아웃 작업을 인증된 된 사용자에 대 한 캐시를 무효화 합니다.  로그인에 새 고유한 쿠키 값을 생성 하기 때문에 캐시를 무효화 합니다. 쿠키가 없는 없거나 만료 된 경우 익명 상태에 대 한 캐시 유지 관리 됩니다. 즉, 사용자가 로그인 하는 경우 캐시는 유지 됩니다.

- - -

### <a name="vary-by"></a>변경

| 특성 유형    | 예제 값                |
|----------------   |----------------               |
| 문자열             | "@Model"                 |


데이터를 가져옵니다 캐시의 사용자 지정을 허용 합니다. 특성의 문자열 값이 변경 되는 캐시 태그 도우미의 내용 참조 하는 개체를 업데이트 하는 경우 종종 모델 값의 문자열 연결을이 특성에 할당 됩니다.  실제로 캐시를 무효화 하는 연결 된 값 중 하나에 대 한 업데이트 것입니다.

다음 예에서는 두 개의 경로 매개 변수는 정수 값 보기 합계 렌더링 컨트롤러 메서드를 가정 `myParam1` 및 `myParam2`, 하는 단일 모델 속성을 반환 합니다. 이 Sum이 변경 될 때 캐시 태그 도우미의 내용은 렌더링 이며 다시 캐시 됩니다.  

예제:

해결 방법:

```csharp
public IActionResult Index(string myParam1,string myParam2,string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

*Index.cshtml*

```cshtml
<Cache vary-by="@Model"">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="priority"></a>priority

| 특성 유형    | 예제 값                |
|----------------   |----------------               |
| CacheItemPriority  | "높음"                   |
|                    | "낮음" |
|                    | "NeverRemove" |
|                    | "Normal" |

기본 제공 캐시 공급자를 캐시 제거 지침을 제공 합니다. 웹 서버를 제거 하 `Low` 메모리가 부족할 때 먼저 항목을 캐시 합니다.

예제:

```cshtml
<Cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

`priority` 특성 특정 수준의 캐시 보존을 보장 하지 않습니다. `CacheItemPriority`뿐입니다. 이 특성을 설정 `NeverRemove` 캐시는 항상 유지 되어야 하는 것을 보장 하지 않습니다. 참조 [추가 리소스](#additional-resources) 자세한 정보에 대 한 합니다.

캐시 태그 도우미에 따라 달라 집니다.는 [메모리 캐시 서비스](xref:performance/caching/memory)합니다. 캐시 태그 도우미 추가 되지 않은 경우 서비스를 추가 합니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>

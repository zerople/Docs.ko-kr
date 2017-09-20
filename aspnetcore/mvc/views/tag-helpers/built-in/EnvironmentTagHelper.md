---
title: "ASP.NET Core에서 환경 태그 도우미"
author: pkellner
description: "ASP.Net Core 환경 태그 도우미 정의 된 모든 속성을 포함 하 여"
keywords: "ASP.NET Core, 태그 도우미"
ms.author: riande
manager: wpickett
ms.date: 07/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/EnvironmentTagHelper
ms.openlocfilehash: e16f942b4a42ef495c7a7daff6724ff071bb35a1
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2017
---
# <a name="environment-tag-helper-in-aspnet-core"></a>ASP.NET Core에서 환경 태그 도우미

여 [Peter Kellner](http://peterkellner.net) 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)

환경 태그 도우미 조건에 따라 현재 호스팅 환경에 따라 포함된 된 콘텐츠에 렌더링 합니다. 단일 특성 `names` 쉼표로 구분 된 목록이 환경 이름, 하는 모든 포함 된 콘텐츠를 렌더링할 수를 트리거할 현재 환경에 일치 하는 경우.

## <a name="environment-tag-helper-attributes"></a>환경 태그 도우미 특성

### <a name="names"></a>이름

단일 호스팅 환경 이름 또는 호스팅 환경 이름의 포함 된 콘텐츠를 렌더링을 트리거하는 쉼표로 구분 된 목록을 허용 합니다.

이러한 값은 ASP.NET Core 정적 속성에서 반환 된 현재 값과 비교할 `HostingEnvironment.EnvironmentName`합니다.  이 값은 다음 중 하나: **준비**; **개발** 또는 **프로덕션**합니다. 비교는 대/소문자를 무시합니다.

유효한의 예로 `environment` 태그 도우미는:

```cshtml
<environment names="Staging,Production">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

## <a name="include-and-exclude-attributes"></a>include 및 exclude 특성

ASP.NET Core 2.x 추가 `include`  &  `exclude` 특성입니다. 포함 또는 제외 호스팅 환경 이름을 기반으로 포함 된 콘텐츠를 렌더링 하 이러한 특성 제어 합니다.

### <a name="include-aspnet-core-20-and-later"></a>2.0 이상 ASP.NET Core를 포함 합니다.

`include` 의 비슷한 동작을 포함 하는 속성은 `names` ASP.NET Core 1.0의 특성입니다.

```cshtml
<environment include="Staging,Production">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude-aspnet-core-20-and-later"></a>ASP.NET Core 2.0 이상 제외

반면,는 `exclude` 속성을 사용은 `EnvironmentTagHelper` 지정한 유형 제외한 모든 호스팅 환경 이름에 대해 포함 된 콘텐츠를 렌더링 합니다.

```cshtml
<environment exclude="Development">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/environments>
* <xref:fundamentals/dependency-injection#service-lifetimes-and-registration-options>

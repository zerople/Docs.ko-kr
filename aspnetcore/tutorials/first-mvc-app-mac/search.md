---
title: "ASP.NET Core MVC 앱에 검색 추가"
author: rick-anderson
description: "간단한 ASP.NET Core MVC 앱에 검색을 추가하는 방법을 보여 줍니다."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 04/07/2017
ms.topic: get-started-article
ms.assetid: d69e5529-ffff-4628-855d-200202096269
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-mac/search
ms.openlocfilehash: 8dab5293ab6a6fe65288bb230e4f39af462ba28b
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
[!INCLUDE[adding-model](../../includes/mvc-intro/search1.md)]

참고: SQLlite는 대소문자를 구분하기 때문에 "ghost"가 아닌 "Ghost"에 대해 검색해야 합니다.

[!INCLUDE[adding-model](../../includes/mvc-intro/search2.md)]

*Views\movie\Index.cshtml* Razor 보기에서 `<form>` 태그를 `method="get"`을 지정하도록 변경합니다.

```html
<form asp-controller="Movies" asp-action="Index" method="get">
```

[!INCLUDE[adding-model](../../includes/mvc-intro/search3.md)]

>[!div class="step-by-step"]
[이전 - 컨트롤러 메서드 및 보기](controller-methods-views.md)
[다음 - 필드 추가](new-field.md)

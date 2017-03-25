---
title: "컴파일 보기 | Microsoft 문서"
author: rick-anderson
description: "보기 컴파일"
keywords: "ASP.NET Core, 보기 컴파일, 미리 컴파일 미리 컴파일"
ms.author: riande
manager: wpickett
ms.date: 03/24/2017
ms.topic: article
ms.assetid: ab4705b7-1638-1638-bc97-ea7f292fe92a
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/view-compilation
translationtype: Machine Translation
ms.sourcegitcommit: 4151c0561303eb2fae6d29ee3aa4019da365c86e
ms.openlocfilehash: b660970604bd8df33c46aa80af8ea8af3a76ea5d
ms.lasthandoff: 03/23/2017

---
# <a name="view-compilation"></a>보기 컴파일

[Rick Anderson](https://twitter.com/RickAndMSFT)

# <a name="view-compilation"></a>보기 컴파일

Razor 뷰에서 뷰를 호출 하면 런타임에 컴파일됩니다. ASP.NET 핵심 1.1.0 및 더 높은 수 필요에 따라 Razor 뷰에서 컴파일하고 앱와 함께 배포할 합니다. 이 집합을 수행 하려면 `MvcRazorCompileOnPublish` true에 대 한 패키지 참조를 포함 하려면 `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`합니다. 다음 *.csproj* 샘플에서는 이러한 설정을 설명 합니다.

[!code-html[주](view-compilation\sample\MvcRazorCompileOnPublish.csproj?highlight=5,12)]


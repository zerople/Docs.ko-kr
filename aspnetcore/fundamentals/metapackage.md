---
title: "ASP.NET Core에 대 한 Microsoft.AspNetCore.All metapackage 2.x 이상"
author: Rick-Anderson
description: "Microsoft.AspNetCore.All metapackage 지원 되는 모든 패키지에 포함 됩니다."
keywords: "ASP.NET Core, NuGet을 패키지 하 고, Microsoft.AspNetCore.All, metapackage"
ms.author: riande
manager: wpickett
ms.date: 07/16/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/metapackage
ms.openlocfilehash: 255438a4ce36ce4978f8c8ee298388a25ac00d17
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
#<a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-2x"></a>ASP.NET Core에 대 한 Microsoft.AspNetCore.All metapackage 2.x

이 기능을 사용 하려면 ASP.NET Core 2.x 합니다.

[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) metapackage ASP.NET Core에 대 한 포함 됩니다.

* 모든 ASP.NET Core 팀에서 패키지를 지원 합니다.
* 지원 되는 모든 패키지는 Entity Framework 코어 
* ASP.NET Core 및 Entity Framework 코어에서 사용 하는 내부 및 타사 종속성입니다. 

ASP.NET Core의 모든 기능 2.x 및 Entity Framework Core 2.x에 포함 되는 `Microsoft.AspNetCore.All` 패키지 합니다. 기본 프로젝트 템플릿에이 패키지를 사용 합니다.

버전 번호는 `Microsoft.AspNetCore.All` metapackage ASP.NET Core 버전 및 Entity Framework Core 버전 (.NET Core 버전으로 정렬 됨)를 나타냅니다.

사용 하는 응용 프로그램의 `Microsoft.AspNetCore.All` metapackage 자동으로 사용할.NET Core 런타임 저장소입니다. 런타임 저장소 ASP.NET Core 2.x 응용 프로그램을 실행 하는 데 필요한 모든 런타임 자산을 포함 합니다. 사용 하는 경우는 `Microsoft.AspNetCore.All` metapackage, **없습니다** 참조 ASP.NET Core NuGet 패키지가에서 자산에는 응용 프로그램과 함께 배포 되 &mdash; .NET Core 런타임 저장소 이러한 자산을 포함 합니다. <!-- todo add link to Runtime store -->응용 프로그램 시작 시간을 개선 하기 위해 자산 런타임 저장소에 미리 컴파일되어 있습니다.

사용 하지 않는 패키지를 제거 하려면 패키지 해제 프로세스를 사용할 수 있습니다. 트리밍된 패키지는 게시 된 응용 프로그램 출력에서 제외 됩니다.

다음 *.csproj* 파일 참조는 `Microsoft.AspNetCore.All` metapackage ASP.NET Core 용:

[!code-xml[Main](..\mvc\views\view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=9)]

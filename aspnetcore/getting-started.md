---
title: "ASP.NET Core 시작 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 73543e9d-d9d5-47d6-9664-17a9beea6cd3
ms.technology: aspnet
ms.prod: asp.net-core
uid: getting-started
translationtype: Machine Translation
ms.sourcegitcommit: 6c8955d9a80bf614f4d5b4284a2f09792ea1152e
ms.openlocfilehash: 5b4760da70b79684852de92f2e7a677a3c586868
ms.lasthandoff: 03/23/2017

---
# <a name="getting-started-with-aspnet-core"></a>ASP.NET Core 시작 하기

1.  설치 [.NET Core](https://microsoft.com/net/core)

2.  새.NET Core 프로젝트를 만듭니다.

    ```console
    mkdir aspnetcoreapp
    cd aspnetcoreapp
    dotnet new web
    ```

3.  패키지를 복원 합니다.

    ```console
    dotnet restore
    ```

4.  앱을 실행 (의 `dotnet run` 명령 만료 되었을 때 응용 프로그램을 빌드할 수):

    ```console
    dotnet run
    ```

5.  로 이동`http://localhost:5000`

## <a name="next-steps"></a>다음 단계

더 많은 시작 자습서를 참조 하십시오. [ASP.NET 핵심 자습서](tutorials/index.md)

ASP.NET 핵심 개념과 아키텍처에 대 한 소개를 참조 하십시오. [ASP.NET 핵심 소개](index.md) 및 [ASP.NET 핵심 기본](fundamentals/index.md)합니다.

ASP.NET 핵심 응용 프로그램에.NET Core 또는.NET Framework 런타임을 사용할 수 있습니다. 자세한 내용은 참조 [.NET Core와.NET Framework 간의 선택](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)합니다.


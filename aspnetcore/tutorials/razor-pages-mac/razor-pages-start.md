---
title: "Mac의 ASP.NET Core에서 Razor 페이지 시작"
author: rick-anderson
description: "Mac용 Visual Studio를 사용하여 ASP.NET Core에서 Razor 페이지 시작"
keywords: "ASP.NET Core, Razor 페이지, 스캐폴딩, Entity Framework Core, EF, EF Core, 데이터베이스, mac, macOS, Mac용 Visual Studio"
ms.author: riande
manager: wpickett
ms.date: 7/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages-mac/razor-pages-start
ms.openlocfilehash: caadc3fcb3bb71abe0773aed4f6ff60a043e3a02
ms.sourcegitcommit: d9ec19e5452af83648074db5d96c0a0f4f9e7f9a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2017
---
# <a name="getting-started-with-razor-pages-in-aspnet-core-with-visual-studio-for-mac"></a>Mac용 Visual Studio를 사용하여 ASP.NET Core에서 Razor 페이지 시작

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서는 ASP.NET Core Razor 페이지 웹앱을 빌드하는 작업의 기본 사항을 설명합니다. 이 자습서를 시작하기 전에 [Razor 페이지 소개](xref:mvc/razor-pages/index)를 완료하는 것이 좋습니다. Razor 페이지는 ASP.NET Core에서 웹 응용 프로그램 UI를 빌드하는 좋은 방법입니다.

## <a name="prerequisites"></a>필수 구성 요소

다음을 설치합니다.

* [.NET Core 2.0.0 SDK](https://dot.net/core) 이상
* [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="create-a-razor-web-app"></a>Razor 웹앱 만들기

터미널에서 다음 명령을 실행합니다.

```console
dotnet new razor -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

이전 명령은 [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/dotnet)를 사용하여 Razor 페이지 프로젝트를 만들고 실행합니다. 브라우저를 열고 http://localhost:5000으로 이동하여 응용 프로그램을 봅니다.

![홈 또는 인덱스 페이지](../razor-pages/razor-pages-start/_static/home.png)

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

## <a name="open-the-project"></a>프로젝트 열기

Ctrl+C를 눌러 응용 프로그램을 종료합니다.

Visual Studio에서 **파일 > 열기**를 선택하고 *RazorPagesMovie.csproj* 파일을 선택합니다.

### <a name="launch-the-app"></a>앱 시작

Visual Studio에서 **실행 > 디버깅하지 않고 시작**을 선택하여 앱을 시작합니다. Visual Studio가 [IIS Express](http://www.iis.net/learn/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고, 브라우저를 시작하고, `http://localhost:5000`으로 이동합니다.

다음 자습서에서는 프로젝트에 모델을 추가합니다.

>[!div class="step-by-step"]
[다음: 모델 추가](xref:tutorials/razor-pages-mac/model)

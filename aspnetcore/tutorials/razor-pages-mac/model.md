---
title: "Mac용 Visual Studio를 사용하여 Razor 페이지 앱에 모델 추가"
author: rick-anderson
description: "Mac용 Visual Studio를 사용하여 ASP.NET Core에서 Razor 페이지 앱에 모델 추가"
keywords: "ASP.NET Core, Razor 페이지, Razor, MVC, 모델"
ms.author: riande
manager: wpickett
ms.date: 08/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages-mac/model
ms.openlocfilehash: 648ecd3a782fa489b727982ce5f7a2087539bf38
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="adding-a-model-to-a-razor-pages-app-in-aspnet-core-with-visual-studio-for-mac"></a>Mac용 Visual Studio를 사용하여 ASP.NET Core에서 Razor 페이지 앱에 모델 추가

[!INCLUDE[model1](../../includes/RP/model1.md)]

## <a name="add-a-data-model"></a>데이터 모델 추가

* 솔루션 탐색기에서 **RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다. 폴더 이름을 *Models*로 지정합니다.
* *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일**을 선택합니다.
* **새 파일** 대화 상자에서:

  * 왼쪽 창에서 **일반**을 선택합니다.
  * 가운데 창에서 **빈 클래스**를 선택합니다.
  * 클래스 이름을 **Movie**로 지정하고 **새로 만들기**를 선택합니다.

[!INCLUDE[model 2](../../includes/RP/model2.md)]
[!INCLUDE[model 2a](../../includes/RP/model2a.md)]

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices2&highlight=3-6)]

줄 `services.AddDbContext<MovieContext>(options =>`에서 빨간색 물결선을 마우스 오른쪽 단추로 클릭합니다(예: `MovieContext`). **빠른 수정 > using RazorPagesMovie.Models;**을 선택합니다. Visual Studio가 using 문을 추가합니다.

프로젝트를 빌드하여 오류가 없는지 확인합니다.

![페이지 만들기](model/red.png)

### <a name="entity-framework-core-nuget-packages-for-migrations"></a>마이그레이션을 위한 Entity Framework Core NuGet 패키지

CLI(명령줄 인터페이스)용 EF 도구는 [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet)에서 제공됩니다. 이 패키지를 설치하려면 *.csproj* 파일의 `DotNetCliToolReference` 컬렉션에 패키지를 추가합니다. **참고:** *.csproj* 파일을 편집하여 이 패키지를 설치해야 합니다. `install-package` 명령이나 패키지 관리자 GUI를 사용할 수 없습니다.

*.csproj* 파일을 편집하려면:

* **파일** > **열기**를 선택한 다음, *.csproj* 파일을 선택합니다.
* **옵션**을 선택합니다.
* **연결 프로그램**을 **소스 코드 편집기**로 변경합니다.

![csproj 파일 편집](model/csproj.png)

`Microsoft.EntityFrameworkCore.Tools.DotNet` 도구 참조를 두 번째 **\<ItemGroup>**에 추가합니다.

[!code-xml[Main](../../tutorials/razor-pages/razor-pages-start/snapshot_cli_sample/RazorPagesMovie/RazorPagesMovie.cli.csproj?range=12-16&highlight=4)]

[!INCLUDE[model3](../../includes/RP/model3.md)]
[!INCLUDE[model 4x](../../includes/RP/model4x.md)]

[!INCLUDE[model 4 exit](../../includes/RP/model4exit.md)]

[!INCLUDE[model 4](../../includes/RP/model4.md)]

### <a name="add-the-pagesmovies-files-to-the-project"></a>프로젝트에 Pages/Movies 파일 추가

* Visual Studio에서 *Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 > 기존 폴더 추가**를 선택합니다.
* *Movies* 폴더를 선택합니다.
* *프로젝트에 포함할 파일 선택* 대화 상자에서 **모두 포함**을 선택합니다.

다음 자습서에서는 스캐폴딩을 통해 만들어진 파일을 설명합니다.

>[!div class="step-by-step"]
[이전: 시작](xref:tutorials/razor-pages-mac/razor-pages-start)
[다음: 스캐폴드된 Razor 페이지](xref:tutorials/razor-pages/page)

---
title: "dotnet watch를 사용하여 ASP.NET Core 앱 개발"
author: rick-anderson
description: "이 자습서에서는 ASP.NET Core 응용 프로그램에서 .NET Core CLI 파일 감시자(dotnet 감시자) 도구를 사용하는 방법을 보여줍니다."
keywords: "ASP.NET Core, dotnet watch 사용"
ms.author: riande
manager: wpickett
ms.date: 10/05/2017
ms.topic: article
ms.assetid: 563ffb3f-d369-4aa5-bf0a-7300b4e7832c
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/dotnet-watch
ms.openlocfilehash: 9baf2ce2a1270a728616a8a2ab45deca9a9cde6f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="developing-aspnet-core-apps-using-dotnet-watch"></a>dotnet watch를 사용하여 ASP.NET Core 앱 개발

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

`dotnet watch`은 원본 파일을 변경할 때 [.NET Core CLI](/dotnet/core/tools) 명령을 실행하는 도구입니다. 예를 들어 파일 변경은 컴파일, 테스트 실행 또는 배포를 트리거할 수 있습니다.

이 자습서에서는 합계를 반환하는 끝점과 제품을 반환하는 끝점인 두 개의 끝점으로 기존 Web API 앱을 사용합니다. 제품 메서드는 이 자습서의 일부로 해결할 버그를 포함합니다.

[샘플 앱](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)을 다운로드합니다. *WebApp*(ASP.NET Core Web API) 및 *WebAppTests*(Web API의 단위 테스트)라는 두 개의 프로젝트가 포함됩니다.

명령 셸에서 *WebApp* 폴더로 이동하고 다음 명령을 실행합니다.

```console
dotnet run
```

콘솔 출력은 다음과 유사한 메시지를 표시합니다(앱이 실행되고 요청을 대기하는 것을 나타냄).

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

웹 브라우저에서 `http://localhost:<port number>/api/math/sum?a=4&b=5`로 이동합니다. `9`라는 결과가 표시됩니다.

API 제품으로 이동합니다(`http://localhost:<port number>/api/math/product?a=4&b=5`). 예상한 대로 `20`이 아니라 `9`를 반환합니다. 자습서의 뒷부분에서 이를 해결합니다.

## <a name="add-dotnet-watch-to-a-project"></a>프로젝트에 `dotnet watch` 추가

1. `Microsoft.DotNet.Watcher.Tools` 패키지 참조를 *.csproj* 파일에 추가합니다.

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup> 
    ```

1. 다음 명령을 실행하여 `Microsoft.DotNet.Watcher.Tools` 패키지를 설치합니다.
    
    ```console
    dotnet restore
    ```

## <a name="running-net-core-cli-commands-using-dotnet-watch"></a>`dotnet watch`을 사용하여 .NET Core CLI 명령 실행

모든 [.NET Core CLI 명령](/dotnet/core/tools#cli-commands)을 `dotnet watch`로 실행할 수 있습니다. 예:

| 명령 | watch를 사용하는 명령 |
| ---- | ----- |
| dotnet run | dotnet watch run |
| dotnet run -f netcoreapp2.0 | dotnet watch run -f netcoreapp2.0 |
| dotnet run -f netcoreapp2.0 -- --arg1 | dotnet watch run -f netcoreapp2.0 -- --arg1 |
| dotnet test | dotnet watch 테스트 |

*WebApp* 폴더에서 `dotnet watch run`을 실행합니다. 콘솔 출력은 `watch`가 시작했음을 나타냅니다.

## <a name="making-changes-with-dotnet-watch"></a>`dotnet watch`를 사용하여 변경

`dotnet watch`가 실행되고 있는지 확인합니다.

합계가 아닌 제품을 반환하도록 *MathController.cs*의 `Product` 메서드에서 버그를 수정합니다.

```csharp
public static int Product(int a, int b)
{
  return a * b;
} 
```

파일을 저장합니다. 콘솔 출력은 `dotnet watch`에서 파일 변경을 검색했고 앱을 다시 시작했음을 나타냅니다.

`http://localhost:<port number>/api/math/product?a=4&b=5`에서 올바른 결과를 반환하는지 확인합니다.

## <a name="running-tests-using-dotnet-watch"></a>`dotnet watch`를 사용하여 테스트 실행

1. *MathController.cs*의 `Product` 메서드가 합계를 반환하도록 변경하고 파일을 저장합니다.
1. 명령 셸에서 *WebAppTests* 폴더로 이동합니다.
1. `dotnet restore`를 실행합니다.
1. `dotnet watch test`를 실행합니다. 이 출력은 테스트에 실패했으며 감시자가 파일 변경을 대기 중임을 나타냅니다.

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. 제품을 반환하도록 `Product` 메서드 코드를 수정합니다. 파일을 저장합니다.

`dotnet watch`는 파일 변경을 검색하고 테스트를 다시 실행합니다. 콘솔 출력은 통과된 테스트를 나타냅니다.

## <a name="dotnet-watch-in-github"></a>GitHub에서 dotnet-watch

dotnet-watch는 GitHub [DotNetTools 리포지토리](https://github.com/aspnet/DotNetTools/tree/dev/src/Microsoft.DotNet.Watcher.Tools)의 일부입니다.

[dotnet-watch 추가 정보](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md)의 [MSBuild 섹션](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md#msbuild)은 dotnet-watch가 조사되는 MSBuild 프로젝트 파일에서 구성될 수 있는 방법을 설명합니다. [dotnet-watch 추가 정보](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md)는 이 자습서에서 다루지 않는 dotnet-watch에 대한 정보를 포함합니다.

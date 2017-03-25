---
title: "Dotnet 감시를 사용 하 여 ASP.NET 핵심 앱 개발 | Microsoft 문서"
author: rick-anderson
description: "Dotnet 감시를 사용 하는 방법을 보여 줍니다."
keywords: "Dotnet 감시를 사용 하 여 ASP.NET 핵심,"
ms.author: riande
manager: wpickett
ms.date: 03/09/2017
ms.topic: article
ms.assetid: 563ffb3f-d369-4aa5-bf0a-7300b4e7832c
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/dotnet-watch
translationtype: Machine Translation
ms.sourcegitcommit: 38d4c0e385aa8d68440118a12d6dd1e94aa7bfd2
ms.openlocfilehash: ac1c3304f018eb2cee7c42abc215932d8d953c54
ms.lasthandoff: 03/23/2017

---
# <a name="developing-aspnet-core-apps-using-dotnet-watch"></a>Dotnet 감시를 사용 하 여 ASP.NET 핵심 앱 개발


여 [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

`dotnet watch`실행 되는 도구는 `dotnet` 명령을 소스 파일을 변경 합니다. 예를 들어 변경 내용이 파일 컴파일, 테스트 또는 배포를 트리거할 수 있습니다.

이 자습서에서는 두 개의 끝점으로는 기존 웹 API 응용 프로그램 사용:을 제품을 반환 하 고 합계를 반환 합니다. 이 자습서의 일부로 수정 하겠습니다는 버그를 포함 하는 제품 메서드입니다.

다운로드는 [샘플 응용 프로그램](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)합니다. 두 개의 프로젝트가 `WebApp` (웹 응용 프로그램) 및 `WebAppTests` (웹 응용 프로그램에 대 한 단위 테스트).

콘솔에서 WebApp 폴더를 찾아 다음 명령을 실행 합니다.

- `dotnet restore`
- `dotnet run`

콘솔 출력은 다음과 유사 하 게 메시지를 표시 합니다 (응용 프로그램을 실행 하 고 요청을 대기 하는 나타냄):

```console
$ dotnet run
Hosting environment: Production
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

웹 브라우저를에서 이동 `http://localhost:5000/api/math/sum?a=4&b=5`, 결과 확인 해야 `9`합니다.

API 제품으로 이동 (`http://localhost:5000/api/math/product?a=4&b=5`)를 반환 `9`이 아니라 `20` 짐작할 수 있습니다. 이 자습서 뒷부분에서 문제를 수정 하겠습니다 했습니다.

## <a name="add-dotnet-watch-to-a-project"></a>추가 `dotnet watch` 프로젝트에

- 추가 `Microsoft.DotNet.Watcher.Tools` 에 *.csproj* 파일:
 ```xml
 <ItemGroup>
   <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="1.0.0" />
 </ItemGroup> 
 ```

- `dotnet restore`를 실행합니다.

## <a name="running-dotnet-commands-using-dotnet-watch"></a>실행 `dotnet` 를 사용 하는 명령`dotnet watch`

모든 `dotnet` 명령으로 실행할 수 있습니다 `dotnet watch`, 예:

| 명령 | 조사식 명령과 |
| ---- | ----- |
| dotnet run | dotnet 조사식 실행 |
| dotnet-f net451를 실행 합니다. | -f net451를 실행 하는 dotnet 조사식 |
| -f net451-실행 dotnet-arg1 | -f net451-실행 dotnet 조사식-arg1 |
| dotnet 테스트 | dotnet 조사식 테스트 |

실행 `dotnet watch run` 에 `WebApp` 폴더입니다. 콘솔 출력 나타납니다 `watch` 시작 했습니다.

## <a name="making-changes-with-dotnet-watch"></a>으로 변경`dotnet watch`

확인 `dotnet watch` 실행 되 고 있습니다.

버그를 수정는 `Product` 의 메서드는 `MathController` 제품과 합계가 아닌을 반환 합니다.

```csharp
public static int Product(int a, int b)
{
  return a * b;
} ```

Save the file. The console output will show messages indicating that `dotnet watch` detected a file change and restarted the app.

Verify `http://localhost:5000/api/math/product?a=4&b=5` returns the correct result.

## Running tests using `dotnet watch`

- Change the `Product` method of the `MathController` back to returning the sum and save the file.
- In a command window, naviagate to the `WebAppTests` folder.
- Run `dotnet restore`
- Run `dotnet watch test`. You see output indicating that a test failed and that watcher is waiting for file changes:

 ```console
 Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
 Test Run Failed.
  ```
- 수정 된 `Product` 메서드 코드는 제품을 반환 합니다. 파일을 저장합니다.

`dotnet watch`파일 변경 사항을 감지 하 고 테스트를 다시 실행 합니다. 콘솔 출력에는 테스트를 통과 표시 됩니다.

## <a name="dotnet-watch-in-github"></a>GitHub에서 dotnet 감시

GitHub의 일부인 dotnet 조사식 [DotNetTools 리포지토리](https://github.com/aspnet/DotNetTools/tree/dev/src/Microsoft.DotNet.Watcher.Tools)합니다.

[MSBuild 섹션](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md#msbuild) 의 [dotnet 조사식 추가 정보](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md) 조사할 MSBuild 프로젝트 파일에서 영문 감시를 구성 하는 방법에 대해 설명 합니다. [dotnet 조사식 추가 정보](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md) dotnet 조사식이이 자습서에서 다루지 않는 정보를 포함 합니다.


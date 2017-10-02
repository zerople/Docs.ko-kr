---
title: "Windows 서비스의 호스트"
author: tdykstra
description: "Windows 서비스에서 ASP.NET Core 응용 프로그램을 호스트 하는 방법을 알아봅니다."
keywords: "ASP.NET Core, Windows 서비스 호스팅"
ms.author: tdykstra
manager: wpickett
ms.date: 03/30/2017
ms.topic: article
ms.assetid: d9a65066-d7cb-47df-b046-64629c4d2c6f
ms.technology: aspnet
ms.prod: aspnet-core
uid: hosting/windows-service
ms.openlocfilehash: ca3b98f0b0405fcd5751cb7d9bc7a40257739084
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2017
---
# <a name="host-an-aspnet-core-app-in-a-windows-service"></a>Windows 서비스에서 ASP.NET Core 응용 프로그램 호스트

으로 [Tom Dykstra](https://github.com/tdykstra)

IIS를 사용 하지 않는 경우 Windows에서 ASP.NET Core 응용 프로그램을 호스트 하는 권장된 방법은 실행 하는 것는 [Windows 서비스](https://docs.microsoft.com/dotnet/framework/windows-services/introduction-to-windows-service-applications)합니다. 이런 방식으로 시작할 수 있습니다 자동으로 다시 부팅 하 고 충돌 후 다른 사용자가 로그인 할 때까지 기다리지 않고 합니다.

[보거나 다운로드 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/hosting/windows-service/sample) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample)). 참조는 [다음 단계](#next-steps) 실행 하는 방법에 대 한 지침은 섹션.

## <a name="prerequisites"></a>필수 구성 요소

* 앱이.NET framework 런타임에서 실행 해야 합니다.  에 *.csproj* 파일,이 대 한 적절 한 값을 지정 [TargetFramework](https://docs.microsoft.com/nuget/schema/target-frameworks) 및 [RuntimeIdentifier](https://docs.microsoft.com/dotnet/articles/core/rid-catalog)합니다. 예를 들면 다음과 같습니다.

  [!code-xml[](windows-service/sample/AspNetCoreService.csproj?range=3-6)]

  Visual Studio에서 프로젝트를 만들 때 사용 된 **ASP.NET Core 응용 프로그램 (.NET Framework)** 서식 파일입니다.

* 응용 프로그램 요청 (뿐 아니라 내부 네트워크)에서 인터넷을 통해을 발생 사용 해야 합니다는 [WebListener](xref:fundamentals/servers/weblistener) 웹 서버 대신 [Kestrel](xref:fundamentals/servers/kestrel)합니다.  Kestrel은 가장자리 배포에 대 한 IIS 함께 사용 해야 합니다.  자세한 내용은 [Kestrel를 역방향 프록시와 함께 사용할 경우](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)를 참조하세요.

## <a name="getting-started"></a>시작

이 섹션에서는 서비스에서 실행 되도록 기존 ASP.NET Core 프로젝트를 설정 하는 데 필요한 최소한의 내용을 설명 합니다.

* NuGet 패키지 설치 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices/)합니다.

* 다음과 같이 변경 `Program.Main`:
  
  * 호출 `host.RunAsService` 대신 `host.Run`합니다.
  
  * 코드가 호출 `UseContentRoot`, 대신 게시 위치에 경로 사용 합니다.`Directory.GetCurrentDirectory()` 
  
  [!code-csharp[](windows-service/sample/Program.cs?name=ServiceOnly&highlight=3-4,8,14)]

* 폴더에 응용 프로그램을 게시 합니다.

  사용 하 여 [dotnet 게시](https://docs.microsoft.com/dotnet/articles/core/tools/dotnet-publish) 또는 [Visual Studio 게시 프로필](xref:publishing/web-publishing-vs) 폴더에 게시 하는 합니다.

* 만들고 서비스를 시작 하 여 테스트 합니다.

  사용 하려면 관리자 명령 프롬프트 창을 엽니다는 [sc.exe](https://technet.microsoft.com/library/bb490995) 명령줄 도구를 만들고 서비스를 시작 합니다.  
  
  앱을 게시 MyService 서비스에 이름을 지정할 경우 `c:\svc`, 및 앱 자체가 AspNetCoreService 라는, 명령은 다음과 같습니다.

  ```console
  sc create MyService binPath="C:\Svc\AspNetCoreService.exe"
  sc start MyService
  ```
  `binPath` 값은 실행 파일 이름 자체를 포함 하 여 앱의 실행 파일의 경로입니다.

  ![콘솔 창에서 만들고 시작 예제](windows-service/_static/create-start.png)

  이러한 명령은 완료 되 면 콘솔 응용 프로그램으로 실행 하는 경우와 같은 경로를 찾아볼 수 있습니다 (기본적으로 `http://localhost:5000`)

  ![서비스에서 실행](windows-service/_static/running-in-service.png)


## <a name="provide-a-way-to-run-outside-of-a-service"></a>서비스 외부에서 실행 하는 방법을 제공합니다

테스트 하 고 호출 하는 코드를 추가 하는 일반적인 되기 때문에 외부 서비스를 실행 하는 경우 디버깅할 쉽게 `host.RunAsService` 특정 조건 에서만.  예를 들어 실행할 수 있습니다는 콘솔 응용 프로그램으로 발생 하는 경우는 `--console` 명령줄 인수나는 디버거가 연결 됩니다.

[!code-csharp[](windows-service/sample/Program.cs?name=ServiceOrConsole)]

## <a name="handle-stopping-and-starting-events"></a>중지 및 시작 이벤트를 처리 합니다.

처리 하려는 경우 `OnStarting`, `OnStarted`, 및 `OnStopping` 이벤트를 다음과 같이 변경 추가:

* `WebHostService`에서 파생되는 클래스를 만듭니다.

  [!code-csharp[](windows-service/sample/CustomWebHostService.cs?name=NoLogging)]

* 에 대 한 확장 메서드를 만들어 `IWebHost` 사용자 지정을 통과 하 `WebHostService` 를 `ServiceBase.Run`합니다.

  [!code-csharp[](windows-service/sample/WebHostServiceExtensions.cs?name=ExtensionsClass)]

* `Program.Main` 대신 새 확장 메서드를 호출 하는 변경 `host.RunAsService`합니다.

  [!code-csharp[](windows-service/sample/Program.cs?name=HandleStopStart&highlight=26)]

하는 경우 사용자 지정 `WebHostService` 종속성 주입 (예:로 거)에서 서비스 하는 데 필요한 코드에서 가져올 수 있습니다는 `Services` 속성 `IWebHost`합니다.

[!code-csharp[](windows-service/sample/CustomWebHostService.cs?name=Logging&highlight=7)]

## <a name="next-steps"></a>다음 단계

[샘플 응용 프로그램](https://github.com/aspnet/Docs/tree/master/aspnetcore/hosting/windows-service/sample) 이 함께 제공 되는 문서는 이전 코드 예제에서와 같이 수정 된 간단한 MVC 웹 응용 프로그램입니다.  도구를 서비스에서 실행 하려면 다음 단계를 수행 합니다.

* 에 게시 *c:\svc*합니다.

* 관리자 창을 엽니다.

* 다음 명령을 입력 합니다.

  ```console
  sc create MyService binPath="c:\svc\aspnetcoreservice.exe"
  sc start MyService
  ```

  * 브라우저에서 실행 중인지 확인 http://localhost:5000 이동 합니다.

오류 메시지에 액세스할 수 있도록 하는 빠른 방법을 같은 로깅 공급자를 추가 하는 응용 프로그램은 서비스에서 실행 하는 경우 예상 대로 최대 시작 되지 않으면는 [Windows 이벤트 로그 공급자](xref:fundamentals/logging#eventlog)합니다.

## <a name="acknowledgments"></a>승인

이미 게시 된 원본의 도움을 받아가이 문서의 작성 되었습니다. 시작와 그 중 가장 유용한 이러한 다음과 같았습니다.

* [ASP.NET Core Windows 서비스 호스팅](https://stackoverflow.com/questions/37346383/hosting-asp-net-core-as-windows-service/37464074)
* [Windows 서비스에서 ASP.NET Core 프로그램을 호스트 하는 방법](https://dotnetthoughts.net/how-to-host-your-aspnet-core-in-a-windows-service/)

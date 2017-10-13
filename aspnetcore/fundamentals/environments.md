---
title: "여러 환경에서 ASP.NET Core 작업"
author: ardalis
description: "ASP.NET Core 여러 환경에 걸쳐 응용 프로그램 동작을 제어 하는 것에 대 한 지원에 제공 하는 방법에 대해 알아봅니다."
keywords: "ASP.NET Core, 환경 설정, ASPNETCORE_ENVIRONMENT"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: b5bba985-be12-4464-9a01-df3599b2a6f1
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/environments
ms.openlocfilehash: becdfa647acb6483b39f5421ab881c4817f31c40
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2017
---
# <a name="working-with-multiple-environments"></a>여러 환경 작업

으로 [Steve Smith](https://ardalis.com/)

ASP.NET Core 개발, 스테이징 및 프로덕션 등의 여러 환경에 걸쳐 앱 동작을 제어 하기 위한 지원을 제공 합니다. 환경 변수는 해당 환경에 구성 된 응용 프로그램 런타임 환경을 나타내는 데 사용 됩니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample)([다운로드 방법](xref:tutorials/index#how-to-download-a-sample))

## <a name="development-staging-production"></a>개발, 스테이징 프로덕션

특정 환경 변수를 참조 하는 ASP.NET Core `ASPNETCORE_ENVIRONMENT` 를 응용 프로그램에서 현재 실행 중인 환경에 설명 합니다. 이 변수를 설정 값을 원하는 하지만 규칙에 따라 3 개의 값 사용: `Development`, `Staging`, 및 `Production`합니다. 이러한 샘플에서 사용 되는 값 및 ASP.NET Core와 함께 제공 되는 서식 파일을 찾을 수 있습니다.

현재 환경 설정은 응용 프로그램 내에서 프로그래밍 방식으로 검색할 수 있습니다. 환경을 사용할 수는 또한 [태그 도우미](../mvc/views/tag-helpers/index.md) 의 특정 섹션에 포함 하도록 프로그램 [보기](../mvc/views/index.md) 현재 응용 프로그램 환경에 따라 합니다.

참고: Windows 및 macOS에서 지정한 환경 이름을 대/소문자 구분. 이 변수를 설정 하는지 여부를 `Development` 또는 `development` 또는 `DEVELOPMENT` 결과 같은 됩니다. 그러나 Linux는는 **대/소문자 구분** 기본적으로 운영 체제. 환경 변수, 파일 이름 및 설정을 대/소문자 구분을 해야합니다.

### <a name="development"></a>개발

이 응용 프로그램을 개발할 때 사용 되는 환경 이어야 합니다. 앱을 실행할 때 프로덕션 환경에서와 같은 사용 가능 하도록 해서는 안 되는 기능을 활성화 하는 일반적으로 [개발자 예외 페이지](xref:fundamentals/error-handling#the-developer-exception-page)합니다.

Visual Studio를 사용 하는 경우 프로젝트의 디버그 프로필에 환경을 구성할 수 있습니다. 디버그 프로필 지정는 [서버](xref:fundamentals/servers/index) 를 설정할 응용 프로그램 및 모든 환경 변수를 시작할 때 사용 하도록 합니다. 프로젝트는 환경 변수를 서로 다르게 설정 하는 여러 디버그 프로필을 가질 수 있습니다. 사용 하 여이 프로필을 관리 하는 **디버그** 웹 응용 프로그램 프로젝트의 탭 **속성** 메뉴. 프로젝트 속성에 설정 된 값에 유지 되는 *launchSettings.json* 파일과 있습니다 또한 프로필을 구성할 수 해당 파일을 직접 편집 하 여 합니다.

IIS Express에 대 한 프로필은 다음과 같습니다.

![프로젝트 속성 설정 환경 변수](environments/_static/project-properties-debug.png)

다음은 한 `launchSettings.json` 프로필에 대 한 포함 된 파일 `Development` 및 `Staging`:

[!code-json[Main](../fundamentals/environments/sample/src/Environments/Properties/launchSettings.json?highlight=15,22)]

사용 되는 웹 서버를 다시 시작 될 때까지 프로젝트 프로필에 대 한 변경 내용이 적용 되지 않을 수 있습니다 (특히 Kestrel를 다시 시작 해야 해당 환경에 대해 변경 내용을 감지 합니다).

>[!WARNING]
> 에 저장 된 환경 변수가 *launchSettings.json* 어떤 방식으로든에서 보호 하지 않는 하 고 사용 하는 경우 프로젝트에 대 한 소스 코드 리포지토리의 일부가 됩니다. **이 파일에 자격 증명 또는 기타 보안 데이터를 저장 하지 마십시오.** 사용 하 여 이러한 데이터를 저장 해야 할 경우는 *암호 관리자* 에 설명 된 도구 [개발 하는 동안 앱 암호의 안전한 저장소](xref:security/app-secrets)합니다.

### <a name="staging"></a>스테이징

일반적으로는 `Staging` 환경은 프로덕션에 배포 하기 전에 최종 테스트에 사용 되는 사전 프로덕션 환경입니다. 이상적으로 실제 특성도 그대로 반영 해야 프로덕션, 프로덕션 환경에서 발생할 수 있는 모든 문제는 사용자에 게 영향을 주지 않고 해결할 수 있습니다를 스테이징 환경에서 먼저 발생 합니다.

### <a name="production"></a>프로덕션

`Production` 환경은 응용 프로그램이 라이브 상태일 때 실행 하는 환경 이며 최종 사용자가 사용 되 고 있습니다. 이 환경 보안, 성능 및 응용 프로그램의 견고성을 최대화 하기 위해 구성 되어야 합니다. 다음과 같은 몇 가지 일반적인 프로덕션 환경에 있을 수 있는 것 다른 설정을 개발

* 캐싱 설정

* 모든 클라이언트 측 리소스가 번들로, 축소, 및 CDN에서 잠재적으로 제공

* 진단 ErrorPages 해제

* 오류 페이지 설정

* 프로덕션 로깅 및 모니터링을 사용 하도록 설정 (예를 들어 [Application Insights](https://azure.microsoft.com/documentation/articles/app-insights-asp-net-five/))

이 속성은 결코 전체 목록은으로 제공 됩니다. 응용 프로그램의 많은 부분에서 환경 검사 분산 되지 않도록 하는 것이 좋습니다. 응용 프로그램의 내에서 이러한 검사를 수행 하는 방법이 권장된 되는 대신, `Startup` 클래스가 가능

## <a name="setting-the-environment"></a>환경 설정

환경 설정에 대 한 메서드는 운영 체제에 따라 달라 집니다.

### <a name="windows"></a>Windows
설정 하는 `ASPNETCORE_ENVIRONMENT` 를 사용 하 여 앱이 시작 하는 경우 현재 세션에 대 한 `dotnet run`, 다음 명령을 사용 하는

**명령줄**
```
set ASPNETCORE_ENVIRONMENT=Development
```
**PowerShell**
```
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

이 명령은 현재 창에 대해서만 적용 됩니다. 창이 닫히면 ASPNETCORE_ENVIRONMENT 설정을 기본 설정 또는 컴퓨터 값 되돌아갑니다. Windows 열 값을 전역으로 설정 된 **제어판** > **시스템** > **고급 시스템 설정** 추가 하거나는 편집`ASPNETCORE_ENVIRONMENT` 값입니다.

![시스템의 고급 속성](environments/_static/systemsetting_environment.png)

![ASPNET 코어 환경 변수](environments/_static/windows_aspnetcore_environment.png) 

**web.config**

참조는 *환경 변수 설정* 의 섹션은 [ASP.NET Core 모듈 구성 참조](xref:hosting/aspnet-core-module#setting-environment-variables) 항목입니다.

**IIS 응용 프로그램 풀 당**

격리된 응용 프로그램 풀(IIS 10.0 이상에서 지원됨)에서 실행되는 개별 응용 프로그램에 대한 환경 변수를 설정해야 하는 경우 [\<environmentVariables> 환경 변수](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 *AppCmd.exe 명령* 섹션을 참조하세요.

### <a name="macos"></a>MacOS
MacOS에 대 한 현재 환경 설정 구성 방법은 인라인 응용 프로그램을 실행 하는 경우

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```
사용 하 여 또는 `export` 응용 프로그램을 실행 하기 전에 설정할 수 있습니다.

```bash
export ASPNETCORE_ENVIRONMENT=Development
``` 
시스템 수준 환경 변수 설정는 *.bashrc* 또는 *.bash_profile* 파일입니다. 임의의 텍스트 편집기를 사용 하 여 파일을 편집 하 고 다음 문을 추가 합니다.

```
export ASPNETCORE_ENVIRONMENT=Development
```  

### <a name="linux"></a>Linux
사용 하 여 Linux 배포판는 `export` 세션 변수 설정을 기반으로 하는 명령줄에서 명령 및 *bash_profile* 시스템 수준 환경 설정에 대 한 파일입니다.

## <a name="determining-the-environment-at-runtime"></a>런타임 시 환경 확인

`IHostingEnvironment` 서비스 환경 작업에 대 한 핵심 추상화를 제공 합니다. 이 서비스 제공 되며 ASP.NET에서 호스팅 계층을 통해 시작 논리에 삽입할 수 [종속성 주입](dependency-injection.md)합니다. Visual Studio에서 ASP.NET Core 웹 사이트 템플릿을 사용 하는이 방식 (있는 경우) 환경 관련 구성 파일을 로드 하 고 응용 프로그램의 오류 처리 설정을 사용자 지정할 수 있습니다. 두 경우 모두이 문제를 호출 하 여 현재 지정 된 환경 참조에 의해 이루어집니다 `EnvironmentName` 또는 `IsEnvironment` 인스턴스의 `IHostingEnvironment` 적절 한 메서드에 전달 합니다.

> [!NOTE]
> 응용 프로그램이 사용 하 여 특정 한 환경에서 실행 되 고 있는지 여부를 확인 하는 경우 `env.IsEnvironment("environmentname")` 이후 대/소문자를 무시 올바르게 됩니다 (하는 경우를 확인 하는 대신 `env.EnvironmentName == "Development"` 예를 들어).

예를 들어 Configure 메서드 환경 특정 오류 처리를 설정 하려면 다음 코드를 사용할 수 있습니다.

[!code-csharp[Main](environments/sample/src/Environments/Startup.cs?range=19-30)]

응용 프로그램에서 실행 중인 경우는 `Development` 이면 환경 사용 하면 Visual Studio, 개발 특정 오류 페이지 (일반적으로 실행 해서는 안 프로덕션 환경에서) 및 특별 한 데이터베이스 오류 "BrowserLink" 기능을 사용 하는 데 필요한 런타임 지원 페이지 (마이그레이션을 적용 하는 방법을 제공 하 고 개발에만 사용 하므로 해야) 합니다. 그렇지 않으면 응용 프로그램 개발 환경에서 실행 되지 않는 경우 처리 되지 않은 예외에 대 한 응답에 표시 되는 표준 오류 처리 페이지 구성 됩니다.

현재 환경에 따라 런타임에 클라이언트에 보내도록 내용을 결정 해야 합니다. 예를 들어 개발 환경에서 하면 일반적으로 사용할 최소화 하지 않은 스크립트 및 스타일 시트를 디버깅을 보다 쉽게 해줍니다. 프로덕션 환경과 테스트 환경 축소 된 버전을 제공 해야 하 고 CDN에서 일반적으로 합니다. 환경을 사용 하 여 수행할 수 있습니다 [태그 도우미](../mvc/views/tag-helpers/intro.md)합니다. 환경 태그 도우미 됩니다 현재 환경을 사용 하 여 지정 하는 환경 중 하 나와 일치 하는 경우에 해당 콘텐츠를 렌더링 된 `names` 특성입니다.

[!code-html[Main](environments/sample/src/Environments/Views/Shared/_Layout.cshtml?range=13-22)]

태그 도우미를 사용 하 여 응용 프로그램 보고를 시작 하려면 [태그 도우미 소개](../mvc/views/tag-helpers/intro.md)합니다.

## <a name="startup-conventions"></a>시작 규칙

ASP.NET Core 응용 프로그램의 시작을 현재 환경에 따라 구성 하는 규칙 기반 방식을 지원 합니다. 또한 프로그래밍 방식으로 환경의을 만들고 사용자 지정 규칙을 관리할 수에 따라 응용 프로그램의 동작을 제어할 수 있습니다.

ASP.NET Core 응용 프로그램 시작 되 면는 `Startup` 클래스 부트스트랩 하는 응용 프로그램, 등 구성 설정을 로드 하는 데 사용 됩니다 ([ASP.NET 시작에 대 한 자세한](startup.md)). 그러나 클래스가 있는 경우 라는 `Startup{EnvironmentName}` (예를 들어 `StartupDevelopment`), 및 `ASPNETCORE_ENVIRONMENT` 해당 이름 다음에 일치 하는 환경 변수 `Startup` 클래스 대신 사용 됩니다. 따라서 구성할 수 있습니다. `Startup` 개발을 위한 별도 있지만 저마다 `StartupProduction` 프로덕션 환경에서 앱을 실행할 때 사용할 수는 있습니다. 또는 그 반대의 경우도 마찬가지입니다.

> [!NOTE]
> 호출 `WebHostBuilder.UseStartup<TStartup>()` 구성 섹션을 재정의 합니다.

사용 하 여 완전히 별개의 `Startup` 현재 환경에 따라 클래스 내에서 응용 프로그램은 구성 하는 방법에 대 한 조정이 만들 수도 있습니다는 `Startup` 클래스입니다. `Configure()` 및 `ConfigureServices()` 유사한 환경 관련 버전을 지원 하는 메서드는 `Startup` 클래스 형식의 자체 `Configure{EnvironmentName}()` 및 `Configure{EnvironmentName}Services()`합니다. 메서드를 정의 하는 경우 `ConfigureDevelopment()` 대신 호출 됩니다 `Configure()` 개발 환경 설정 된 경우. 마찬가지로, `ConfigureDevelopmentServices()` 대신 이라고 `ConfigureServices()` 동일한 환경에서 합니다.

## <a name="summary"></a>요약

ASP.NET Core 다양 한 기능 및 다양 한 환경에서 응용 프로그램의 동작 방식을 쉽게 제어할 수 있는 규칙을 제공 합니다. 프로덕션 환경에 스테이징 개발에서 응용 프로그램을 게시할 때 환경 변수 설정 적절 하 게 환경에 대 한 허용 적절 하 게 디버깅, 테스트 또는 프로덕션 용도로 응용 프로그램의 최적화를 위해 합니다.

## <a name="additional-resources"></a>추가 리소스

* [구성](configuration.md)

* [태그 도우미 소개](../mvc/views/tag-helpers/intro.md)

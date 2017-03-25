---
title: "여러 환경 작업 | Microsoft 문서"
author: ardalis
description: 
keywords: "ASP.NET Core, ASPNETCORE_ENVIRONMENT 환경 설정"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: b5bba985-be12-4464-9a01-df3599b2a6f1
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/environments
translationtype: Machine Translation
ms.sourcegitcommit: bff0c6a3f481fa4c6ec73c98d5048fb82e4eacc3
ms.openlocfilehash: 2c61b1353c8f0956c1eb050d14fcd09e3216bb20
ms.lasthandoff: 03/23/2017

---
# <a name="working-with-multiple-environments"></a>여러 환경 작업

[Steve Smith](http://ardalis.com)

ASP.NET Core에서는 개발, 스테이징 및 프로덕션와 같은 여러 환경에서 응용 프로그램 동작을 제어 하기 위한 향상 된 지원 합니다. 환경 변수는 앱을 적절 하 게 구성할 수 있도록 응용 프로그램에서 실행 되는 환경을 나타내는 데 사용 됩니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample)

## <a name="development-staging-production"></a>개발, 스테이징, 프로덕션

ASP.NET 핵심 참조는 특정 [환경 변수](https://github.com/aspnet/Home/wiki/Environment-Variables), `ASPNETCORE_ENVIRONMENT` 를 응용 프로그램에서 현재 실행 중인 환경에 설명 합니다. 이 변수를 설정할 수 값으로 있지만 규칙에 따라 세 개의 값이 사용 됩니다: `Development`, `Staging`, 및 `Production`합니다. 이러한 샘플에 사용 되는 값 및 ASP.NET Core와 함께 제공 되는 서식 파일을 찾을 수 있습니다.

현재 환경 설정이 응용 프로그램 내에서 프로그래밍 방식으로 검색할 수 있습니다. 또한 환경에 사용할 수 있습니다 [태그 도우미](../mvc/views/tag-helpers/index.md) 의 특정 섹션에 포함 하도록 프로그램 [보기](../mvc/views/index.md) 현재 응용 프로그램 환경을 기반으로 합니다.

> [!NOTE]
> Windows 및 macOS에서 지정한 환경 이름은 대/소문자 구분입니다. 변수 설정할 수 있는지 여부를 `Development` 또는 `development` 또는 `DEVELOPMENT` 결과가 동일 합니다. 그러나 Linux는는 **대/소문자 구분** 기본적으로 운영 체제입니다. 환경 변수, 파일 이름 및 설정을 모범 사례에 대 한 대/소문자 구분을 가정해 야 합니다.

### <a name="development"></a>개발

이 응용 프로그램을 개발할 때 사용 되는 환경 이어야 합니다. Visual Studio를 사용 하는 경우 IIS express의 경우 여기에 표시 된 같은 프로젝트의 디버그 프로필에이 설정을 지정할 수 있습니다.

![프로젝트 속성 설정 환경 변수](environments/_static/project-properties-debug.png)

변경 내용을에 유지 되는 프로젝트를 사용 하 여 만든 기본 설정을 수정 하면 *변경* 에 `Properties` 폴더입니다. 이 파일 설정을 사용 해야 하는 환경 변수를 포함 하 여 응용 프로그램을 시작 하는 데 Visual Studio 구성 된 각 프로필에 특정 한 유지 합니다. (디버그 프로필에 자세히 설명 [서버](servers/index.md)). 예를 들어 IIS Express를 사용 하도록 구성 하는 다른 프로필에 추가한 후 하지만 사용 하 여는 `ASPNETCORE_ENVIRONMENT` 값 `Staging`, `launchSettings.json` 샘플 프로젝트에서 파일은 다음과 같습니다.

변경

[!code-json[주](../fundamentals/environments/sample/src/Environments/Properties/launchSettings.json?highlight=15,22)]

> [!NOTE]
> 프로젝트 프로필에 나 변경 내용이 *변경* 직접 사항이 적용 되지 않습니다 사용 되는 웹 서버를 다시 시작 될 때까지 (특히 Kestrel를 다시 시작 해야 해당 환경의 변경 감지 합니다).

응용 프로그램에서 다른 환경 변수를 필요로 하는 등의 다양 한 다른 구성에 대 한 여러 다른 실행 프로필을 만들 수 있습니다.

>[!WARNING]
> 에 저장 된 환경 변수 *변경* 하나를 사용 하는 경우 프로젝트에 대 한 소스 코드 리포지토리의 일부가 될 및 어떤 식으로든에서 보호 되지 않습니다. **이 파일에 자격 증명 또는 기타 비밀 데이터를 저장 하지 마십시오.** 사용 하 여 이러한 데이터를 저장할 위치를 해야 하는 경우는 *암호 관리자* 에 설명 된 도구 [개발 하는 동안 앱 암호의 안전한 저장소](../security/app-secrets.md#security-app-secrets)합니다.

### <a name="staging"></a>스테이징

규칙에 따라 한 `Staging` 환경은 프로덕션에 배포 하기 전에 최종 테스트에 사용 되는 프로덕션 전 환경입니다. 이상적으로 실제 특성도 그대로 반영 해야 생산는 프로덕션 환경에서 발생할 수 있는 모든 문제를 사용자에 게 영향을 주지 않고 호출할 수 있습니다, 스테이징 환경에서 먼저 발생 합니다.

### <a name="production"></a>프로덕션

`Production` 환경은 라이브 되었을 때 응용 프로그램이 실행 되는 환경 이며 최종 사용자가 사용 되 고 있습니다. 이 환경 보안, 성능 및 응용 프로그램의 견고성을 최대화 하기 위해 구성 되어야 합니다. 몇 가지 일반적인 설정 프로덕션 환경에 있을 수 있는 개발 다른 것은 다음과 같습니다.

* 캐싱 설정

* 모든 클라이언트 쪽 리소스는 번들로 제공, 축소 된, 및 CDN에서 잠재적으로 제공

* 진단 알려주면 해제

* 친숙 한 오류 페이지 설정

* 프로덕션 로깅 및 모니터링을 사용 하도록 설정 (예를 들어 [Application Insights](https://azure.microsoft.com/en-us/documentation/articles/app-insights-asp-net-five/))

이 작업은 결코 전체 목록을 의미 합니다. 응용 프로그램의 많은 부분에서 환경 검사 분산 되지 않도록 하는 것이 좋습니다. 응용 프로그램의 내에서 이러한 검사를 수행 하는 방법이 권장된 되는 대신, `Startup` 클래스 가능

## <a name="setting-the-environment"></a>환경 설정

환경 설정에 대 한 운영 체제에 따라 다릅니다.

### <a name="windows"></a>창
설정 하는 `ASPNETCORE_ENVIRONMENT` 를 사용 하 여 앱이 시작 하는 경우 현재 세션에 대 한 `dotnet run`, 다음 명령을 사용 하는

**명령줄**
```
set ASPNETCORE_ENVIRONMENT="Development"
```
**PowerShell**
```
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

이 명령은 현재 창에 대해서만 적용 됩니다. 창이 닫혀 있는, ASPNETCORE_ENVIRONMENT 설정을 기본 설정 또는 컴퓨터 값으로 되돌아갑니다. 창 열어에서 값을 전역적으로 설정 하기 위해는 **제어판** > **시스템** > **고급 시스템 설정** 추가 또는 편집 하 고는 `ASPNETCORE_ENVIRONMENT` 값입니다.

![고급 속성 시스템](environments/_static/systemsetting_environment.png)

![ASPNET 코어 환경 변수](environments/_static/windows_aspnetcore_environment.png) 

### <a name="macos"></a>macOS
MacOS에 대 한 현재 환경 설정 가능 인라인 응용 프로그램을 실행 하는 경우

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```
사용 하 여 또는 `export` 응용 프로그램을 실행 하기 전에 설정할 수 있습니다.

```bash
export ASPNETCORE_ENVIRONMENT=Development
``` 
컴퓨터 수준 환경 변수 설정 하는 *.bashrc* 또는 *.bash_profile* 파일입니다. 임의의 텍스트 편집기를 사용 하 여 파일을 편집 하 고 다음 문을 추가 합니다.

```
export ASPNETCORE_ENVIRONMENT=Development
```  

### <a name="linux"></a>Linux
Linux 배포판에 대 한 사용는 `export` 세션 변수 설정을 기반으로 하는 명령줄에서 명령 및 *bash_profile* 시스템 수준 환경 설정에 대 한 파일입니다.

## <a name="determining-the-environment-at-runtime"></a>런타임 시 환경 확인

`IHostingEnvironment` 서비스 환경을 사용 하기 위한 핵심 추상화를 제공 합니다. 이 서비스 제공 되며 ASP.NET에서 호스팅 계층을 통해 시작 논리에 삽입할 수 [종속성 주입](dependency-injection.md)합니다. Visual Studio에서 ASP.NET 핵심 웹 사이트 서식 파일 (있는 경우) 환경 관련 구성 파일을 로드 하 고 응용 프로그램의 오류 처리 설정을 사용자 지정 하려면이 접근 방식을 사용 합니다. 두 경우 모두,이 동작을 높일 수는 현재 지정 된 환경을 호출 하 여 참조 하 여 `EnvironmentName` 또는 `IsEnvironment` 인스턴스의 `IHostingEnvironment` 적절 한 메서드로 전달 합니다.

> [!NOTE]
> 응용 프로그램 사용 하 여 특정 한 환경에서 실행 되 고 있는지 여부를 확인 하는 경우 `env.IsEnvironment("environmentname")` 되므로 대/소문자를 무시 올바르게 됩니다 (인지를 검사 하는 대신 `env.EnvironmentName == "Development"` 예).

예를 들어 환경 특정 오류 처리를 설정 하려면 구성 메서드에 다음 코드를 사용할 수 있습니다.

[!code-csharp[주](environments/sample/src/Environments/Startup.cs?range=19-30)]

앱에서 실행 중인 경우는 `Development` 환경 이면을 Visual Studio, 개발 관련 오류 페이지 (일반적으로 실행 해서는 안 프로덕션 환경에서) 및 특수 데이터베이스 오류 페이지 (마이그레이션을 적용 하는 방법을 제공 하 고만 사용 해야 개발에서)에서 "BrowserLink" 기능을 사용 하는 데 필요한 런타임 지원할 수 있도록 합니다. 그렇지 않으면 응용 프로그램 개발 환경에서 실행 되지 않는, 처리 되지 않은 모든 예외에 대 한 응답에 표시 되는 표준 오류 처리 페이지 구성 됩니다.

현재 환경에 따라 런타임에 클라이언트에 보낼 내용을 결정 해야 합니다. 예를 들어 개발 환경에서 일반적으로 제공할 있습니다 최소화 하지 않은 스크립트 및 스타일 시트 디버깅을 쉽게 해줍니다. 프로덕션 환경과 테스트 환경 축소 된 버전을 사용 하 고 CDN에서 일반적으로 합니다. 환경을 사용 하 여 수행할 수 있습니다 [태그 도우미](../mvc/views/tag-helpers/intro.md)합니다. 환경 태그 도우미는 현재 환경을 사용 하 여 지정 하는 환경 중 하 나와 일치 하는 경우 해당 콘텐츠를 렌더링만 `names` 특성입니다.

[!code-html[주](environments/sample/src/Environments/Views/Shared/_Layout.cshtml?range=13-22)]

태그 도우미를 사용 하 여 응용 프로그램 참조에서 시작 하려면 [태그 도우미 소개](../mvc/views/tag-helpers/intro.md)합니다.

## <a name="startup-conventions"></a>시작 규칙

ASP.NET Core 현재 환경에 따라 응용 프로그램의 시작을 구성 하는 규칙 기반 접근 방식을 지원 합니다. 또한 프로그래밍 방식으로 어떤 환경에 중인를 만들고 사용자 고유의 규칙을 관리할 수 있도록에 따라 응용 프로그램의 동작을 제어할 수 있습니다.

ASP.NET 핵심 응용 프로그램이 시작 하는 경우는 `Startup` 클래스는 응용 프로그램을 부트스트랩, 등 구성 설정을 로드 하는 데 사용 됩니다 ([ASP.NET 시작에 대 한 자세한](startup.md)). 그러나 클래스가 있는 경우 라는 `Startup{EnvironmentName}` (예를 들어 `StartupDevelopment`), 및 `ASPNETCORE_ENVIRONMENT` 해당 이름 다음에 일치 하는 환경 변수 `Startup` 클래스 대신 사용 됩니다. 따라서 구성할 수 있습니다. `Startup` 개발을 위한 별도 하지만 `StartupProduction` 프로덕션 응용 프로그램을 실행할 때 사용할 수는 있습니다. 또는 그 반대의 경우도 마찬가지입니다.

> [!NOTE]
> 호출 `WebHostBuilder.UseStartup<TStartup>()` 구성 섹션을 재정의 합니다.

완전히 별도 사용 이외에도 `Startup` 현재 환경에 따라 클래스 내에서 응용 프로그램 구성 방식에 조정 가능는 `Startup` 클래스입니다. `Configure()` 및 `ConfigureServices()` 유사한 환경 관련 버전을 지원 하는 메서드는 `Startup` 클래스는 폼의 자체 `Configure{EnvironmentName}()` 및 `Configure{EnvironmentName}Services()`합니다. 메서드를 정의 하는 경우 `ConfigureDevelopment()` 대신 호출할지 `Configure()` 개발 환경을 설정 하는 경우. 마찬가지로, `ConfigureDevelopmentServices()` 대신 호출 됩니다 `ConfigureServices()` 동일한 환경에서.

## <a name="summary"></a>요약

ASP.NET Core 다양 한 기능 및 개발자가 다양 한 환경에서 응용 프로그램의 동작 방식을 손쉽게 제어할 수 있도록 하는 규칙을 제공 합니다. 를 프로덕션에 스테이징 개발에서 응용 프로그램을 게시할 때 환경 변수 설정 적절 하 게 환경에 대 한 허용 적절 하 게 디버깅, 테스트 또는 프로덕션 사용에 대 한 응용 프로그램의 최적화 합니다.

## <a name="additional-resources"></a>추가 리소스

* [구성](configuration.md)

* [태그 도우미 소개](../mvc/views/tag-helpers/intro.md)


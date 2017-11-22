---
title: "IIS가 있는 Windows에서 ASP.NET Core 호스팅"
author: guardrex
description: "Windows Server IIS(인터넷 정보 서비스)를 구성하고 ASP.NET Core 응용 프로그램을 배포합니다."
keywords: "ASP.NET Core, 인터넷 정보 서비스, IIS, Windows Server, 호스팅 번들, ASP.NET Core 모듈, 웹 배포"
ms.author: riande
manager: wpickett
ms.date: 03/13/2017
ms.topic: article
ms.assetid: a4449ad3-5bad-410c-afa7-dc32d832b552
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/iis
ms.openlocfilehash: e9e9019d5b879498e8800bb579c177dd3ad64061
ms.sourcegitcommit: 96af03c9f44f7c206e68ae3ef8596068e6b4e5fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>IIS가 있는 Windows에서 ASP.NET Core 호스팅

이 문서의 작성자: [Luke Latham](https://github.com/guardrex) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="supported-operating-systems"></a>지원되는 운영 체제

지원되는 운영 체제는 다음과 같습니다.

* Windows 7 이상
* Windows Server 2008 R2 이상&#8224;

&#8224;개념적으로 이 문서에서 설명하는 IIS 구성은 Nano Server IIS에서 ASP.NET Core 응용 프로그램을 호스팅하는 데에도 적용되지만 구체적인 지침은 [Nano 서버에서 실행되는 IIS가 있는 ASP.NET Core](xref:tutorials/nano-server)를 참조하세요.

[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전에는 [WebListener](xref:fundamentals/servers/weblistener)라고 함)는 IIS 역방향 프록시 구성에서 작동하지 않습니다. 이에 따라 [Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용해야 합니다.

## <a name="iis-configuration"></a>IIS 구성

**웹 서버(IIS)** 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.

### <a name="windows-desktop-operating-systems"></a>Windows 데스크톱 운영 체제

**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다. **인터넷 정보 서비스** 및 **웹 관리 도구** 그룹을 엽니다. **IIS 관리 콘솔** 확인란을 선택합니다. **World Wide Web 서비스** 확인란을 선택합니다. **World Wide Web 서비스**의 기본 기능을 허용하거나 필요에 따라 IIS 기능을 사용자 지정합니다.

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](iis/_static/windows-features-win10.png)

### <a name="windows-server-operating-systems"></a>Windows Server 운영 체제

서버 운영 체제의 경우 **관리** 메뉴 또는 **서버 관리자**의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다. **서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.

![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](iis/_static/server-roles-ws2016.png)

**역할 서비스** 단계에서 원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 받아들입니다.

![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](iis/_static/role-services-ws2016.png)

**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다. 웹 서버(IIS) 역할을 설치한 후에 서버/IIS를 다시 시작할 필요가 없습니다.

## <a name="install-the-net-core-windows-server-hosting-bundle"></a>.NET Core Windows Server 호스팅 번들 설치

1. 호스팅 시스템에 [.NET Core Windows Server 호스팅 번들](https://download.microsoft.com/download/5/C/1/5C190037-632B-443D-842D-39085F02E1E8/DotNetCore.2.0.3-WindowsHosting.exe)을 설치합니다. 번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:fundamentals/servers/aspnet-core-module)을 설치합니다. 이 모듈은 IIS와 Kestrel 서버 사이에 역방향 프록시를 만듭니다. 시스템이 인터넷에 연결되지 않은 경우 [Microsoft Visual C++ 2015 재배포 가능 패키지](https://www.microsoft.com/download/details.aspx?id=53840)를 설치한 후에 .NET Core Windows Server 호스팅 번들을 설치합니다.

2. 시스템을 다시 시작하거나 명령 프롬프트에서 **net stop was /y**에 이어 **net start w3svc**를 실행하여 시스템 PATH에 대한 변경 내용을 선택합니다.

> [!NOTE]
> IIS 공유 구성을 사용하는 경우 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:hosting/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Visual Studio을 사용하여 게시할 때 웹 배포 설치

[Visual Studio](https://www.visualstudio.com/vs/)에서 [웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)를 사용하여 응용 프로그램을 배포하려는 경우 호스팅 시스템에 최신 버전의 웹 배포를 설치합니다. 웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 설치 관리자를 직접 구할 수 있습니다. WebPI를 사용하는 것이 좋습니다. WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.

## <a name="application-configuration"></a>응용 프로그램 구성

### <a name="enabling-the-iisintegration-components"></a>IISIntegration 구성 요소 사용

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

일반적인 *Program.cs*는 [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder)를 호출하여 호스트 설정을 시작합니다. `CreateDefaultBuilder`는 [Kestrel](xref:fundamentals/servers/kestrel)을 웹 서버로 구성하고 [ASP.NET Core 모듈](xref:fundamentals/servers/aspnet-core-module)의 기본 경로 및 포트를 구성하여 IIS 통합을 구현합니다.

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

앱 종속성에 [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) 패키지에 대한 종속성을 포함시킵니다. *UseIISIntegration* 확장 메서드를 *WebHostBuilder*에 추가하여 IIS 통합 미들웨어를 앱에 통합합니다.

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .UseIISIntegration()
    ...
```

`UseKestrel` 및 `UseIISIntegration`이 둘 다 필요합니다. *UseIISIntegration*을 호출하는 코드는 코드 이식성에 영향을 주지 않습니다. 앱이 IIS 배후에서 실행되지 않는 경우(예를 들어 앱이 Kestrel에서 바로 실행되는 경우) `UseIISIntegration`은 작동하지 않습니다.

---

호스팅에 대한 자세한 내용은 [ASP.NET Core에서 호스팅](xref:fundamentals/hosting)을 참조하세요.

### <a name="iis-options"></a>IIS 옵션

*IISIntegration* 서비스 옵션을 구성하려면 *ConfigureServices*에 *IISOptions*에 대한 서비스 구성을 포함합니다.

```csharp
services.Configure<IISOptions>(options => 
{
    ...
});
```

| 옵션                         | 기본 | 설정 |
| ------------------------------ | ------- | ------- |
| `AutomaticAuthentication`      | `true`  | `true`면 인증 미들웨어가 `HttpContext.User`를 설정하고 제네릭 과제에 응답합니다. `false`면 인증 미들웨어가 ID(`HttpContext.User`)만 제공하고, `AuthenticationScheme`에 의해 명시적으로 요청될 때 과제에 응답합니다. IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다. |
| `AuthenticationDisplayName`    | `null`  | 로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다. |
| `ForwardClientCertificate`     | `true`  | `true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다. |

### <a name="webconfig"></a>web.config

*web.config* 파일은 주로 ASP.NET Core 모듈을 구성합니다. 선택적으로 추가 IIS 구성 설정을 제공할 수 있습니다. *web.config*의 생성, 변환 및 게시는 .NET Core Web SDK(`Microsoft.NET.Sdk.Web`)에서 처리됩니다. SDK는 프로젝트 파일의 맨 위(*.csproj*), `<Project Sdk="Microsoft.NET.Sdk.Web">`에서 설정됩니다. SDK가 *web.config* 파일을 변환하지 못하게 하려면 프로젝트 파일에 `true` 설정을 갖는 **\<IsTransformWebConfigDisabled>** 속성을 추가합니다.

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

*dotnet publish* 또는 Visual Studio 게시를 사용하여 게시할 때 프로젝트에 *web.config* 파일이 없으면 [게시되는 출력](xref:hosting/directory-structure)에 해당 파일이 만들어집니다. 프로젝트에 *web.config* 파일이 있는 경우 올바른 *processPath* 및 *arguments*를 통해 변환되어 [ASP.NET Core 모듈](xref:fundamentals/servers/aspnet-core-module)을 구성하고 게시된 출력으로 이동합니다. 변환은 파일에 포함된 IIS 구성 설정에 영향을 주지 않습니다.

## <a name="create-the-iis-website"></a>IIS 웹 사이트 만들기

1. 대상 IIS 시스템에서는 [디렉터리 구조](xref:hosting/directory-structure)에서 설명하는 앱의 게시된 폴더와 파일을 포함할 폴더를 만듭니다.

2. 만든 폴더 내에서 *logs* 폴더를 만들어 stdout 로그를 보관합니다(시작 문제를 해결하도록 로깅을 사용하려는 경우). 페이로드에 *logs* 폴더가 있는 응용 프로그램을 배포하려는 경우 이 단계를 건너뛸 수 있습니다. [폴더를 자동으로 만드는 미해결 문제](https://github.com/aspnet/AspNetCoreModule/issues/30)가 있습니다. MSBuild에서 *log* 폴더를 만들려면 다음 `Target`을 프로젝트 파일에 추가합니다.

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="AfterPublish">
     <MakeDir Directories="$(PublishDir)logs" Condition="!Exists('$(PublishDir)logs')" />
     <MakeDir Directories="$(PublishUrl)logs" Condition="!Exists('$(PublishUrl)logs')" />
   </Target>
   ```

3. **IIS 관리자**에서 새 웹 사이트를 만듭니다. **사이트 이름**을 입력하고 **실제 경로**를 만든 앱의 배포 폴더로 설정합니다. **바인딩** 구성을 제공하고 웹 사이트를 만듭니다.

4. 응용 프로그램 풀을 **관리 코드 없음**으로 설정합니다. ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.

5. **웹 사이트 추가** 창을 엽니다.

   ![[사이트] 상황에 맞는 메뉴에서 [웹 사이트 추가]를 클릭합니다.](iis/_static/add-website-context-menu-ws2016.png)

6. 웹 사이트를 구성합니다.

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](iis/_static/add-website-ws2016.png)

7. **응용 프로그램 풀** 패널에서 웹 사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 팝업 메뉴에서 **기본 설정...**을 선택하여 **응용 프로그램 풀 편집** 창을 엽니다.

   ![[응용 프로그램 풀]의 상황에 맞는 메뉴에서 [기본 설정]을 선택합니다.](iis/_static/apppools-basic-settings-ws2016.png)

8. **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.

   ![관리 코드 없음으로 설정된 .NET CLR 버전](iis/_static/edit-apppool-ws2016.png)
     
    참고: **.NET CLR 버전**을 **관리 코드 없음**으로 설정하는 것은 선택 사항입니다. ASP.NET Core에서는 데스크톱 CLR을 로드할 필요가 없습니다.

9. 프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.

   앱 풀의 기본 ID(**프로세스 모델** > **ID**)를 **ApplicationPoolIdentity**에서 다른 ID로 변경하는 경우, 새 ID에 앱 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.
   
## <a name="deploy-the-application"></a>응용 프로그램 배포
대상 IIS 시스템에서 만든 폴더에 응용 프로그램을 배포합니다. [웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포에 권장되는 메커니즘입니다. 웹 배포에 대한 대안은 아래와 같습니다.

게시된 배포용 앱이 실행되고 있지 않은지 확인합니다. 앱이 실행 중이면 *publish* 폴더의 파일이 잠겨 있습니다. 잠긴 파일을 복사할 수 없으므로 배포를 수행할 수 없습니다.

### <a name="web-deploy-with-visual-studio"></a>Visual Studio를 사용한 웹 배포
웹 배포에서 사용할 게시 프로필을 만드는 방법은 [Visual Studio 및 MSBuild용 게시 프로필을 만들어 ASP.NET Core 앱 배포](xref:publishing/web-publishing-vs#publish-profiles) 항목을 참조하세요. 호스팅 공급자에서 게시 프로필을 제공하거나 이 프로필을 만들 수 있도록 지원하는 경우 Visual Studio **게시** 대화 상자를 사용하여 해당 프로필을 다운로드하고 가져옵니다.

![게시 대화 상자 페이지](iis/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Visual Studio 외부에서 웹 배포
명령줄을 통해 Visual Studio 외부에서 [웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)를 사용할 수도 있습니다. 자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.

### <a name="alternatives-to-web-deploy"></a>웹 배포에 대한 대안
웹 배포를 사용하지 않거나 Visual Studio를 사용하지 않으려면 Xcopy, Robocopy 또는 PowerShell과 같이 여러 가지 방법 중 하나를 사용하여 응용 프로그램을 호스팅 시스템으로 이동할 수 있습니다. Visual Studio 사용자는 [게시 샘플(영문)](https://github.com/aspnet/vsweb-publish/blob/master/samples/samples.md)을 사용할 수 있습니다.

## <a name="browse-the-website"></a>웹 사이트 찾아보기
![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](iis/_static/browsewebsite.png)
   
> [!WARNING]
> .NET Core 앱은 IIS와 Kestrel 서버 간의 역방향 프록시를 통해 호스팅됩니다. 역방향 프록시를 만들려면 *web.config* 파일이 IIS에 제공되는 웹 사이트 실제 경로인 배포된 응용 프로그램의 콘텐츠 루트 경로(일반적으로 응용 프로그램 기본 경로)에 있어야 합니다. 중요한 파일은 *my_application.runtimeconfig.json*, *my_application.xml*(XML 문서 주석) 및 *my_application.deps.json*과 같은 하위 폴더를 포함한 앱의 실제 경로에 있습니다. *web.config* 파일은 Kestrel에 대한 역방향 프록시를 만들어 IIS에서 이러한 파일 및 기타 중요한 파일을 제공하지 못하도록 하는 데 필요합니다. **따라서 *web.config* 파일을 실수로 이름을 변경하거나 배포에서 제거하지 않아야 합니다.**

## <a name="data-protection"></a>데이터 보호

ASP.NET Core 응용 프로그램은 다음 조건에서 키 링을 메모리에 저장합니다.

* 웹 사이트가 IIS 뒤에 호스팅됩니다.
* 데이터 보호 스택이 영구 저장소에 키 링을 저장하도록 구성되지 않았습니다.

키 링이 메모리에 저장되는 경우 앱을 다시 시작할 때 다음과 같이 됩니다.

* 모든 폼 인증 토큰이 유효하지 않습니다. 
* 사용자가 다음 요청에서 다시 로그인해야 합니다. 
* 키 링으로 보호한 데이터가 더 이상 보호되지 않습니다.

> [!WARNING]
> 데이터 보호는 인증에 사용되는 것을 포함하여 여러 ASP.NET 미들웨어에서 사용됩니다. 사용자 고유 코드에서 데이터 보호 API를 호출하지 않아도 배포 스크립트 또는 사용자 코드를 사용하여 데이터 보호를 구성해야 합니다. 데이터 보호를 구성하지 않으면 기본적으로 키가 메모리에 보관되어 앱을 다시 시작할 때 삭제됩니다. 다시 시작하면 쿠키 인증 미들웨어로 작성된 모든 쿠키가 무효화되고 사용자가 다시 로그인해야 합니다.

IIS에서 데이터 보호를 구성하려면 다음 방법 중 하나를 사용해야 합니다.

* [PowerShell 스크립트](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1)를 실행하여 적절한 레지스트리 항목을 만듭니다(예: `.\Provision-AutoGenKeys.ps1 DefaultAppPool`). 이렇게 하면 키가 레지스트리에 저장되고, 컴퓨터 수준 키가 있는 DPAPI를 사용하여 보호됩니다.
* 사용자 프로필을 로드하도록 IIS 응용 프로그램 풀을 구성합니다. 이 설정은 응용 프로그램 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다. **사용자 프로필**을 `True`로 설정합니다. 이렇게 하면 사용자 프로필 디렉터리 아래에 키가 저장되고, 앱 풀에 사용되는 사용자 계정과 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.
* [파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다. X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다. 자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치해야 합니다.

웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.

* 모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.
* 각 시스템에 X509 인증서를 배포합니다. [코드에 데이터 보호](https://docs.microsoft.com/aspnet/core/security/data-protection/configuration/overview)를 구성합니다.

### <a name="1-create-a-data-protection-registry-hive"></a>1. 데이터 보호 레지스트리 하이브 만들기

ASP.NET 응용 프로그램에서 사용하는 데이터 보호 키는 응용 프로그램 외부의 레지스트리 하이브에 저장됩니다. 지정된 응용 프로그램의 키를 유지하려면 해당 앱의 응용 프로그램 풀에 대한 레지스트리 하이브를 만들어야 합니다.

독립 실행형 IIS 설치의 경우 ASP.NET Core 앱에서 사용되는 각 앱 풀에 대해 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1)를 사용할 수 있습니다. 이 스크립트는 작업자 프로세스 계정에만 ACL로 지정된 특수 레지스트리 키를 HKLM 레지스트리에 만듭니다. 미사용 키는 DPAPI를 사용하여 암호화됩니다.

웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다. 기본적으로 데이터 보호 키는 암호화되지 않습니다. 이러한 공유에 대한 파일 권한은 앱이 실행되는 Windows 계정으로 제한되어야 합니다. 또한 X509 인증서를 사용하여 미사용 키를 보호하도록 선택할 수 있습니다. 사용자가 인증서를 업로드하여 사용자의 신뢰할 수 있는 인증서 저장소에 배치하고 사용자의 앱이 실행될 모든 컴퓨터에서 인증서를 사용할 수 있도록 하는 메커니즘을 고려해야 할 수 있습니다. 자세한 내용은 [데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.

### <a name="2-configure-the-iis-application-pool-to-load-the-user-profile"></a>2. 사용자 프로필을 로드하도록 IIS 응용 프로그램 풀 구성

이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다. 사용자 프로필 로드를 `True`로 설정합니다. 이렇게 하면 사용자 프로필 디렉터리 아래에 키가 저장되고, 앱 풀에 사용되는 사용자 계정과 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.

### <a name="3-machine-wide-policy-for-data-protection"></a>3. 데이터 보호에 대한 컴퓨터 수준 정책

데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다. 자세한 내용은 [데이터 보호](xref:security/data-protection/index) 설명서를 참조하세요.

## <a name="configuration-of-sub-applications"></a>하위 응용 프로그램 구성

루트 응용 프로그램 아래에 추가된 하위 응용 프로그램은 ASP.NET Core 모듈을 처리기로 포함하지 않아야 합니다. 하위 응용 프로그램의 *web.config* 파일에 모듈을 처리기로 추가하면 하위 앱을 찾아보려고 할 때 잘못된 구성 파일을 참조하는 500.19(내부 서버 오류)가 표시됩니다. 다음 예제에서는 ASP.NET Core 하위 앱에 대해 게시된 *web.config* 파일의 내용을 보여 줍니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

ASP.NET Core 앱 아래에 비ASP .NET Core 하위 앱을 호스팅하려는 경우 하위 앱의 *web.config* 파일에서 상속된 처리기를 명시적으로 제거해야 합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore"/>
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

*web.config* 파일을 사용하여 ASP.NET Core 모듈을 구성하는 방법에 대한 자세한 내용은 [ASP.NET Core 모듈 소개](xref:fundamentals/servers/aspnet-core-module) 항목 및 [ASP.NET Core 모듈 구성 참조](xref:hosting/aspnet-core-module)를 참조하세요.

## <a name="configuration-of-iis-with-webconfig"></a>web.config를 사용하여 IIS 구성

IIS 구성은 여전히 역방향 프록시 구성에 적용되는 IIS 기능에 대한 *web.config*에 포함된 `<system.webServer>` 섹션의 영향을 받습니다. 예를 들어 시스템 수준에서 동적 압축을 사용하도록 IIS를 구성했을 수 있지만, 응용 프로그램의 *web.config* 파일에서 `<urlCompression>` 요소를 사용하여 응용 프로그램의 해당 설정을 사용하지 않도록 설정할 수 있습니다. 자세한 내용은 [`<system.webServer>`에 대한 구성 참조](https://docs.microsoft.com/iis/configuration/system.webServer/), [ASP.NET Core 모듈 구성 참조](xref:hosting/aspnet-core-module) 및 [ASP.NET Core와 함께 IIS 모듈 사용](xref:hosting/iis-modules)을 참조하세요. 격리된 응용 프로그램 풀(IIS 10.0 이상에서 지원됨)에서 실행되는 개별 응용 프로그램에 대한 환경 변수를 설정해야 하는 경우 [\<environmentVariables> 환경 변수](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 *AppCmd.exe 명령* 섹션을 참조하세요.

## <a name="configuration-sections-of-webconfig"></a>web.config 구성 섹션

*web.config*의 `<system.web>`, `<appSettings>`, `<connectionStrings>` 및 `<location>` 요소로 구성된 .NET Framework 응용 프로그램과 달리 ASP.NET Core 응용 프로그램은 다른 구성 공급자를 사용하여 구성됩니다. 자세한 내용은 [구성](xref:fundamentals/configuration)을 참조하세요.

## <a name="application-pools"></a>응용 프로그램 풀

단일 시스템에서 여러 웹 사이트를 호스팅하는 경우 각 앱을 자체의 응용 프로그램 풀에서 실행하여 앱을 서로 격리해야 합니다. IIS **웹 사이트 추가** 대화 상자에서는 이 동작이 기본적으로 설정되어 있습니다. **사이트 이름**을 제공하면 해당 텍스트가 **응용 프로그램 풀** 텍스트 상자로 자동으로 전송됩니다. 웹 사이트를 추가할 때 해당 사이트 이름을 사용하여 새 응용 프로그램 풀이 만들어집니다.

## <a name="application-pool-identity"></a>응용 프로그램 풀 ID

응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 응용 프로그램을 실행할 수 있습니다. IIS 8.0 이상에서 IIS 관리 작업자 프로세스(WAS)는 새 응용 프로그램 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다. IIS 관리 콘솔의 앱 풀에 대한 **고급 설정**에서 아래 이미지와 같이 **ID**에서 **ApplicationPoolIdentity**를 사용하도록 설정되어 있는지 확인합니다.

![응용 프로그램 풀 고급 설정 대화 상자](iis/_static/apppool-identity.png)

IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다. 리소스는 이 ID를 사용하여 보호할 수 있습니다. 그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.

앱에 대한 높은 액세스 권한을 IIS 작업자 프로세스에 부여해야 하는 경우 응용 프로그램이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정해야 합니다.

1. [Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.

2. 디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.

3. **보안** 탭 아래에서 **편집** 단추를 클릭한 다음 **추가** 단추를 클릭합니다.

4. **위치** 단추를 클릭하고 시스템이 선택되어 있는지 확인합니다.

5. **선택할 개체 이름 입력** 텍스트 상자에서 **IIS AppPool\DefaultAppPool**을 입력합니다.

  ![응용 프로그램 폴더에 대한 사용자 또는 그룹 선택 대화 상자](iis/_static/select-users-or-groups-1.png)

6. **이름 확인** 단추를 클릭한 다음 **확인**을 클릭합니다.

  ![응용 프로그램 폴더에 대한 사용자 또는 그룹 선택 대화 상자](iis/_static/select-users-or-groups-2.png)

**ICACLS** 도구를 사용하여 명령 프롬프트를 통해 이 작업을 수행할 수도 있습니다.

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

## <a name="troubleshooting-tips"></a>문제 해결 팁

IIS 배포로 문제를 진단하려면 다음을 수행합니다.

* 브라우저 출력을 검토합니다.
* **이벤트 뷰어**를 통해 시스템의 **응용 프로그램** 로그를 검사합니다.
* `stdout` 로깅을 사용하도록 설정합니다. **ASP.NET Core 모듈** 로그는 *web.config*에 있는 `<aspNetCore>` 요소의 *stdoutLogFile* 특성에 제공된 경로에 있습니다. 특성 값에 제공된 경로의 모든 폴더가 배포에 있어야 합니다. *stdoutLogEnabled="true"*도 설정해야 합니다. `Microsoft.NET.Sdk.Web` SDK를 사용하여 *web.config* 파일을 만드는 앱은 기본적으로 *stdoutLogEnabled* 설정을 *false*로 설정하므로 직접 *web.config* 파일을 제공하거나 `stdout` 로깅을 사용하도록 파일을 수정합니다.

*startupTimeLimit*(기본값: 120초) 및 *startupRetryCount*(기본값: 2) 모듈이 통과할 때까지 일반적인 몇 가지 오류는 브라우저, 응용 프로그램 로그 및 ASP.NET Core 모듈 로그에 표시되지 않습니다. 따라서 모듈에서 앱의 프로세스를 시작하지 못했다고 추론될 때까지 6분 정도 기다립니다.

앱이 제대로 작동하는지 확인하는 빠른 방법은 Kestrel에서 직접 앱을 실행하는 것입니다. 앱이 FDD(프레임워크 종속 배포)로 게시된 경우 앱의 IIS 실제 경로인 배포 폴더에 있는 `dotnet my_application.dll`을 실행합니다. 앱이 SCD(자체 포함 배포)로 게시된 경우 명령 프롬프트에서 직접 앱의 실행 파일, 즉 배포 폴더에 있는 `my_application.exe`를 실행합니다. Kestrel이 5000 기본 포트에서 수신 대기중인 경우 `http://localhost:5000/`에서 앱을 찾아볼 수 있습니다. 앱이 Kestrel 끝점 주소에서 정상적으로 응답하는 경우, 문제는 IIS-ASP.NET Core 모듈-Kestrel 구성과 관련이 있으며 앱 자체 내에서 관련되었을 가능성은 낮습니다.

Kestrel 서버에 대한 IIS 역방향 프록시가 제대로 작동하는지 확인하는 한 가지 방법은 [정적 파일 미들웨어](xref:fundamentals/static-files)를 사용하여 *wwwroot*에 있는 앱의 정적 파일에서 스타일시트, 스크립트 또는 이미지에 대한 간단한 정적 파일 요청을 수행하는 것입니다. 앱에서 정적 파일을 제공할 수 있지만 MVC보기와 다른 끝점에서 오류가 발생하는 경우, 문제는 IIS-ASP.NET Core 모듈-Kestrel 구성과 관련이 없으며 앱 자체 내에서 관련되었을 가능성이 높습니다(예: MVC 라우팅 또는 500 내부 서버 오류).

Kestrel이 IIS 뒤에서 정상적으로 시작되지만 로컬에서 성공적으로 실행한 후에 시스템에서 응용 프로그램이 실행되지 않으면 임시로 *web.config*에 환경 변수를 추가하여 `ASPNETCORE_ENVIRONMENT`를 `Development`로 설정할 수 있습니다. 앱 시작에서 환경을 재정의하지 않는다면 시스템에서 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시됩니다. 이 방식으로 `ASPNETCORE_ENVIRONMENT`에 대한 환경 변수를 설정하는 것은 인터넷에 노출되지 않은 준비/테스트 시스템에만 적용하는 것이 좋습니다. 완료되면 반드시 *web.config* 파일에서 해당 환경 변수를 제거해야 합니다. *web.config*를 통해 역방향 프록시에 대한 환경 변수를 설정하는 방법에 대한 자세한 내용은[aspNetCore의 environmentVariables 자식 요소](xref:hosting/aspnet-core-module#setting-environment-variables)를 참조하세요.

응용 프로그램 로깅을 사용하면 대부분의 경우에서 앱 또는 역방향 프록시 문제를 해결하는 데 도움이 됩니다. 자세한 내용은 [로깅](xref:fundamentals/logging/index)을 참조하세요.

마지막 문제 해결 팁은 개발 컴퓨터의 .NET Core SDK 또는 응용프로그램 내의 패키지 버전을 업그레이드한 후에 실행되지 않는 응용프로그램에 관한 것입니다. 경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다. 프로젝트의 `bin` 및 `obj` 폴더를 삭제하고, `%UserProfile%\.nuget\packages\` 및 `%LocalAppData%\Nuget\v3-cache`에 있는 패키지 캐시를 지우고, 프로젝트를 복원하고, 해당 응용 프로그램을 다시 배포하기 전에 시스템의 이전 배포가 완전히 삭제되었는지 확인하여 이러한 문제를 대부분 수정할 수 있습니다.

> [!TIP]
> 패키지 캐시를 지우는 편리한 방법은 [NuGet.org](https://www.nuget.org/)에서 *NuGet.exe* 도구를 구하여 시스템 PATH에 추가하고 명령 프롬프트에서 `nuget locals all -clear`를 실행하는 것입니다. *NuGet.exe*를 가져오지 않고도 명령 프롬프트에서 `dotnet nuget locals all --clear` 명령을 실행할 수 있습니다.

## <a name="common-errors"></a>일반적인 오류

다음은 모든 오류를 나열한 목록이 아닙니다. 여기에 나열되지 않은 오류가 발생하면 아래의 참고 섹션에 자세한 오류 메시지를 남겨 주세요.

### <a name="installer-unable-to-obtain-vc-redistributable"></a>설치 관리자에서 VC++ 재배포 가능 패키지를 가져올 수 없음

* **설치 관리자 예외:** 0x80072efd 또는 0x80072f76 - 지정되지 않은 오류

* **설치 관리자 로그 예외&#8224;:** 오류 0x80072efd 또는 0x80072f76: EXE 패키지를 실행하지 못했습니다.

  &#8224;로그는 C:\Users\\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{timestamp}.log에 있습니다.

문제 해결:

* 시스템에서 서버 호스팅 번들을 설치하지만 인터넷에 액세스할 수 없는 경우 이 예외는 설치 관리자에서 *Microsoft Visual C++ 2015 재배포 가능 패키지*를 얻을 수 없을 때 발생합니다. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=53840)에서 설치 관리자를 얻을 수 있습니다. 설치 관리자에 오류가 발생하면 FDD(프레임워크 종속 배포)를 호스팅하는 데 필요한 .NET Core 런타임을 받지 못할 수 있습니다. FDD를 호스팅하려면 프로그램 &amp; 기능에서 런타임이 설치되어 있는지 확인합니다. [.NET 다운로드](https://www.microsoft.com/net/download/core)에서 런타임 설치 관리자를 얻을 수 있습니다. 런타임을 설치한 후 시스템을 다시 시작하거나 명령 프롬프트에서 **net stop was /y**에 이어 **net start w3svc**를 실행하여 IIS를 다시 시작합니다.

### <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>OS 업그레이드에서 32비트 ASP.NET Core 모듈이 제거됨

* **응용 프로그램 로그:** 모듈 DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll**을(를) 로드하지 못했습니다. 데이터 오류입니다.

문제 해결:

* OS를 업그레이드하는 동안 **C:\Windows\SysWOW64\inetsrv** 디렉터리에 있는 비OS 파일은 보존되지 않습니다. OS를 업그레이드하기 전에 ASP.NET Core 모듈을 설치한 다음 OS를 업그레이드한 후에 32비트 모드에서 AppPool을 실행하려고 하면 이 문제가 발생합니다. OS를 업그레이드한 후에 ASP.NET Core 모듈을 복구합니다. [.NET Core Windows Server 호스팅 번들 설치](#install-the-net-core-windows-server-hosting-bundle)를 참조하세요. 설치 관리자를 실행할 때 **복구**를 선택합니다.

### <a name="platform-conflicts-with-rid"></a>플랫폼이 RID와 충돌함

* **브라우저:** HTTP 오류 502.5 - 프로세스 오류

* **응용 프로그램 로그:** 실제 루트 'C:\{PATH}\'이(가) 있는 응용 프로그램 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION'에서 "C:\\{PATH}\my_application.{exe|dll}" 명령줄로 프로세스를 시작하지 못했습니다., 오류 코드 = '0x80004005 : ff.

* **ASP.NET Core 모듈 로그:** 처리되지 않은 예외: System.BadImageFormatException: 파일이나 어셈블리 'my_application.dll'을(를) 로드할 수 없습니다. 프로그램을 잘못된 형식으로 로드하려고 했습니다.

문제 해결:

* 응용 프로그램이 Kestrel에서 로컬로 실행되는지 확인합니다. 프로세스 오류는 응용 프로그램 내의 문제 때문일 수 있습니다. 자세한 내용은 [문제 해결 팁](#troubleshooting-tips)을 참조하세요.

* *.csproj*에서 RID와 충돌하는 `<PlatformTarget>`을 설정하지 않았는지 확인합니다. 예를 들어 *dotnet publish -c Release -r win10-x64*를 사용하거나 *.csproj*의 `<RuntimeIdentifiers>`를 `win10-x64`로 설정하여 `x86`인 `<PlatformTarget>`를 지정하지 않고 `win10-x64`인 RID를 사용하여 게시하지 않습니다. 프로젝트는 경고 또는 오류 없이 게시되지만 시스템에서 위와 같이 로깅된 예외와 함께 실패합니다.

* Azure 앱 배포에서 응용 프로그램을 업그레이드하고 새 어셈블리를 배포할 때 이 예외가 발생하면 이전 배포에서 모든 파일을 수동으로 삭제합니다. 호환되지 않는 어셈블리가 오랫동안 남아 있으면 업그레이드된 응용 프로그램을 배포할 때 `System.BadImageFormatException` 예외가 발생할 수 있습니다.

### <a name="uri-endpoint-wrong-or-stopped-website"></a>URI 끝점이 잘못되었거나 중지된 웹 사이트

* **브라우저:** ERR_CONNECTION_REFUSED

* **응용 프로그램 로그:** 항목 없음

* **ASP.NET Core 모듈 로그:** 로그 파일이 만들어지지 않았습니다.

문제 해결:

* 응용 프로그램에 올바른 URI 끝점을 사용하고 있는지 확인합니다. 바인딩을 확인합니다.

* IIS 웹 사이트가 *중지됨* 상태가 아닌지 확인합니다.

### <a name="corewebengine-or-w3svc-server-features-disabled"></a>CoreWebEngine 또는 W3SVC 서버 기능이 사용되지 않음

* **OS 예외:** ASP.NET Core 모듈을 사용하려면 IIS 7.0 CoreWebEngine 및 W3SVC 기능이 설치되어 있어야 합니다.

문제 해결:

* 적절한 역할과 기능을 사용하도록 설정했는지 확인합니다. [IIS 구성](#iis-configuration)을 참조하세요.

### <a name="incorrect-website-physical-path-or-application-missing"></a>잘못된 웹 사이트 실제 경로 또는 누락된 응용 프로그램

* **브라우저:** 403 사용할 수 없음 - 액세스가 거부되었습니다.  **- 또는 -**  403.14 사용할 수 없음 - 웹 서버가 이 디렉터리의 내용을 표시하지 못하도록 구성되었습니다.

* **응용 프로그램 로그:** 항목 없음

* **ASP.NET Core 모듈 로그:** 로그 파일이 만들어지지 않았습니다.

문제 해결:

* IIS 웹 사이트 **기본 설정**과 실제 응용 프로그램 폴더를 확인합니다. 응용 프로그램이 IIS 웹 사이트 **실제 경로**의 폴더에 있는지 확인합니다.

### <a name="incorrect-role-module-not-installed-or-incorrect-permissions"></a>잘못된 역할, 설치되지 않은 모듈 또는 잘못된 권한

* **브라우저:** 500.19 내부 서버 오류 - 요청된 페이지와 관련된 구성 데이터가 잘못되어 해당 페이지에 액세스할 수 없습니다.

* **응용 프로그램 로그:** 항목 없음

* **ASP.NET Core 모듈 로그:** 로그 파일이 만들어지지 않았습니다.

문제 해결:

* 적절한 역할을 사용하도록 설정했는지 확인합니다. [IIS 구성](#iis-configuration)을 참조하세요.

* **프로그램 &amp; 기능**을 확인하고 **Microsoft ASP.NET Core 모듈**이 설치되어 있는지 확인합니다. **Microsoft ASP.NET Core 모듈**이 설치된 프로그램 목록에 없으면 해당 모듈을 설치합니다. [.NET Core Windows Server 호스팅 번들 설치](#install-the-net-core-windows-server-hosting-bundle)를 참조하세요.

* **응용 프로그램 풀** > **프로세스 모델** > **ID**가 **ApplicationPoolIdentity**로 설정되어 있는지 또는 사용자 지정 ID에 앱의 배포 폴더에 액세스할 수 있는 올바른 권한이 있는지 확인합니다.

### <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>잘못된 processPath, 누락된 PATH 변수, 설치되지 않은 호스팅 번들, 다시 시작되지 않은 시스템/IIS, 설치되지 않은 VC++ 재배포 가능 패키지 또는 dotnet.exe 액세스 위반

* **브라우저:** HTTP 오류 502.5 - 프로세스 오류

* **응용 프로그램 로그:** 실제 루트 'C:\\{PATH}\'이(가) 있는 응용 프로그램 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION'에서 '".\my_application.exe"' 명령줄로 프로세스를 시작하지 못했습니다., 오류 코드 = '0x80070002 : 0.

* **ASP.NET Core 모듈 로그:** 로그 파일이 만들어졌지만 비어 있습니다.

문제 해결:

* 응용 프로그램이 Kestrel에서 로컬로 실행되는지 확인합니다. 프로세스 오류는 응용 프로그램 내의 문제 때문일 수 있습니다. 자세한 내용은 [문제 해결 팁](#troubleshooting-tips)을 참조하세요.

* *web.config*의 `<aspNetCore>` 요소에서 *processPath* 특성을 확인하여 FDD(프레임워크 종속 배포)에 대한 *dotnet*인지 또는 SCD(자체 포함 배포)에 대한 *\my_application.exe*인지 확인합니다.

* FDD의 경우 *dotnet.exe*에서 PATH 설정을 통해 액세스하지 못할 수 있습니다. 시스템 PATH 설정에 *C:\Program Files\dotnet\*이 있는지 확인합니다.

* FDD의 경우 *dotnet.exe*에서 응용 프로그램 풀의 사용자 ID에 액세스하지 못할 수 있습니다. AppPool 사용자 ID에 *C:\Program Files\dotnet* 디렉터리에 대한 액세스 권한이 있는지 확인합니다. *C:\Program Files\dotnet* 및 응용 프로그램 디렉터리에 AppPool 사용자 ID에 대해 구성된 거부 규칙이 없는지 확인합니다.

* FDD를 배포한 다음 IIS를 다시 시작하지 않고 .NET Core를 설치했을 수 있습니다. 서버를 다시 시작하거나 명령 프롬프트에서 **net stop was /y**에 이어 **net start w3svc**를 실행하여 IIS를 다시 시작합니다.

* 호스팅 시스템에 .NET Core 런타임을 설치하지 않고 FDD를 배포했을 수 있습니다. FDD를 배포하려고 하지만 .NET Core 런타임을 설치하지 않은 경우 시스템에서 **.NET Core Windows Server 호스팅 번들 설치 관리자**를 실행합니다. [.NET Core Windows Server 호스팅 번들 설치](#install-the-net-core-windows-server-hosting-bundle)를 참조하세요. 인터넷에 연결되지 않은 시스템에 .NET Core 런타임을 설치하려는 경우 [.NET 다운로드](https://www.microsoft.com/net/download/core)에서 런타임을 얻은 다음 호스팅 번들 설치 관리자를 실행하여 ASP.NET Core 모듈을 설치합니다. 설치를 완료하려면 시스템을 다시 시작하거나 명령 프롬프트에서 **net stop was /y**에 이어 **net start w3svc**를 실행하여 IIS를 다시 시작합니다.

* FDD를 배포한 다음 시스템/IIS를 다시 시작하지 않고 .NET Core를 설치했을 수 있습니다. 시스템을 다시 시작하거나 명령 프롬프트에서 **net stop was /y**에 이어 **net start w3svc**를 실행하여 IIS를 다시 시작합니다.

* FDD를 배포했을 수 있지만 *Microsoft Visual C++ 2015 재배포 가능 패키지(x64)*가 시스템에 설치되어 있지 않습니다. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=53840)에서 설치 관리자를 얻을 수 있습니다.

### <a name="incorrect-arguments-of-aspnetcore-element"></a>\<aspNetCore\> 요소의 잘못된 인수

* **브라우저:** HTTP 오류 502.5 - 프로세스 오류

* **응용 프로그램 로그:** 실제 루트 'C:\\{PATH}\'이(가) 있는 응용 프로그램 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION'에서 '"dotnet" .\my_application.dll' 명령줄로 프로세스를 시작하지 못했습니다., 오류 코드 = '0x80004005 : 80008081.

* **ASP.NET Core 모듈 로그:** 실행할 응용 프로그램이 없습니다.: 'PATH\my_application.dll'

문제 해결:

* 응용 프로그램이 Kestrel에서 로컬로 실행되는지 확인합니다. 프로세스 오류는 응용 프로그램 내의 문제 때문일 수 있습니다. 자세한 내용은 [문제 해결 팁](#troubleshooting-tips)을 참조하세요.

* *web.config*의 `<aspNetCore>` 요소에서 *arguments* 특성을 검사하여 (a) FDD(프레임워크 종속 배포)에 대한 *.\my_application.dll*인지, 또는 (b) 없는 경우 빈 문자열(*arguments=“”*)이거나 SCD(자체 포함 배포)에 대한 앱의 인수(*arguments=“arg1, arg2, ...”*) 목록인지 확인합니다.

### <a name="missing-net-framework-version"></a>누락된 .NET Framework 버전

* **브라우저:** 502.3 잘못된 게이트웨이 - 요청을 라우팅하는 동안 연결 오류가 발생했습니다.

* **응용 프로그램 로그:** 오류 코드 = 실제 루트 'C:\\{PATH}\'이(가) 있는 응용 프로그램 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION'에서 '"dotnet" .\my_application.dll' 명령줄로 프로세스를 시작하지 못했습니다., 오류 코드 = '0x80004005 : 80008081.

* **ASP.NET Core 모듈 로그:** 메서드, 파일 또는 어셈블리 예외가 없습니다. 예외에 지정된 메서드, 파일 또는 어셈블리는 .NET Framework 메서드, 파일 또는 어셈블리입니다.

문제 해결:

* 시스템에서 누락된 .NET Framework 버전을 설치합니다.

* FDD(프레임워크 종속 배포)의 경우 시스템에 올바른 런타임이 설치되어 있는지 확인합니다. 프로젝트를 1.1에서 2.0으로 업그레이드하고 호스팅 시스템에 배포한 다음 이 예외가 발생하면 호스팅 시스템에 Framework 2.0을 설치해야 합니다.

### <a name="stopped-application-pool"></a>중지된 응용 프로그램 풀

* **브라우저:** 503 서비스를 사용할 수 없음

* **응용 프로그램 로그:** 항목 없음

* **ASP.NET Core 모듈 로그:** 로그 파일이 만들어지지 않았습니다.

문제 해결

* 응용 프로그램 풀이 *중지됨* 상태가 아닌지 확인합니다.

### <a name="iis-integration-middleware-not-implemented"></a>IIS 통합 미들웨어가 구현되지 않음

* **브라우저:** HTTP 오류 502.5 - 프로세스 오류

* **응용 프로그램 로그:** 실제 루트 'C:\\{PATH}\'이(가) 있는 응용 프로그램 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION'에서 '"C:\\{PATH}\my_application.{exe|dll}"'명령줄로 프로세스를 만들었지만 지정된 포트 '{PORT}'에서 충돌하거나 응답하지 않거나 수신 대기하지 않습니다., 오류 코드 = '0x800705b4'

* **ASP.NET Core 모듈 로그:**  로그 파일이 만들어졌고 정상 작동을 보여 줍니다.

문제 해결

* 앱이 Kestrel에서 로컬로 실행되는지 확인합니다. 프로세스 오류는 앱 내의 문제 때문일 수 있습니다. 자세한 내용은 [문제 해결 팁](#troubleshooting-tips)을 참조하세요.

* 응용 프로그램의 *WebHostBuilder()*(ASP.NET Core 1.x)에서 *.UseIISIntegration()* 메서드를 호출하거나 `CreateDefaultBuilder` 메서드(ASP.NET Core 2.x)를 사용하는 IIS 통합 미들웨어를 올바르게 참조했는지 확인합니다. [ASP.NET Core에서의 호스팅](xref:fundamentals/hosting)에서 자세한 내용을 참조하세요.

### <a name="sub-application-includes-a-handlers-section"></a>하위 응용 프로그램에 \<handlers\> 섹션이 포함되어 있음

* **브라우저:** HTTP 오류 500.19 - 내부 서버 오류

* **응용 프로그램 로그:** 항목 없음

* **ASP.NET Core 모듈 로그:** 로그 파일이 만들어졌고 루트 응용 프로그램에 대해 정상 작동을 보여 줍니다. 하위 응용 프로그램에 대한 로그 파일이 만들어지지 않았습니다.

문제 해결

* 하위 앱의 *web.config* 파일에 `<handlers>` 섹션이 포함되어 있지 않은지 확인합니다.

### <a name="application-configuration-general-issue"></a>일반적인 응용 프로그램 구성 문제

* **브라우저:** HTTP 오류 502.5 - 프로세스 오류

* **응용 프로그램 로그:** 실제 루트 'C:\\{PATH}\'이(가) 있는 응용 프로그램 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION'에서 '"C:\\{PATH}\my_application.{exe|dll}"'명령줄로 프로세스를 만들었지만 지정된 포트 '{PORT}'에서 충돌하거나 응답하지 않거나 수신 대기하지 않습니다., 오류 코드 = '0x800705b4'

* **ASP.NET Core 모듈 로그:** 로그 파일이 만들어졌지만 비어 있습니다.

문제 해결

* 이 일반 예외는 응용 프로그램 구성 문제로 인해 프로세스가 시작되지 못했음을 나타냅니다. [디렉터리 구조](xref:hosting/directory-structure)를 참조하여 앱의 배포된 파일과 폴더가 적절하고, 앱의 구성 파일이 있고, 앱과 환경에 대한 올바른 설정을 포함하고 있는지 확인합니다. 자세한 내용은 [문제 해결 팁](#troubleshooting-tips)을 참조하세요.

## <a name="resources"></a>리소스

* [ASP.NET Core 모듈 소개](xref:fundamentals/servers/aspnet-core-module)
* [ASP.NET Core 모듈 구성 참조](xref:hosting/aspnet-core-module)
* [ASP.NET Core와 함께 IIS 모듈 사용](xref:hosting/iis-modules)
* [ASP.NET Core 소개](../index.md)
* [공식 Microsoft IIS 사이트](https://www.iis.net/)
* [Microsoft TechNet 라이브러리: Windows Server](https://docs.microsoft.com/windows-server/windows-server-versions)

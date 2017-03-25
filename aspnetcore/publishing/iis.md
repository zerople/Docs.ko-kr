---
title: "IIS에 게시 합니다. | Microsoft 문서"
author: guardrex
description: "Windows Server 인터넷 정보 서비스 (IIS) 구성 및 ASP.NET 핵심 응용 프로그램의 배포 합니다."
keywords: "ASP.NET Core, 인터넷 정보 서비스, iis, windows server 번들, asp.net 핵심 모듈, 웹 호스팅 배포"
ms.author: riande
manager: wpickett
ms.date: 03/13/2017
ms.topic: article
ms.assetid: a4449ad3-5bad-410c-afa7-dc32d832b552
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/iis
translationtype: Machine Translation
ms.sourcegitcommit: 1894789727a7c3ae66f25040d1a70c4f1e64c3da
ms.openlocfilehash: 0f861d87200f95c80c224460e6411736953fe2b5
ms.lasthandoff: 03/23/2017

---
# <a name="publishing-to-iis"></a>IIS에 게시

여 [Luke Latham](https://github.com/GuardRex) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="supported-operating-systems"></a>지원되는 운영 체제

다음 운영 체제가 지원 됩니다.

* Windows 7 이상

* Windows Server 2008 R2 및 newer†

†Conceptually,이 문서에 설명 된 IIS 구성 Nano 서버 IIS에서 ASP.NET 핵심 응용 프로그램 호스팅에 적용 되지만 참조 [Nano 서버에 IIS와 ASP.NET 핵심](xref:tutorials/nano-server) 구체적인 지침은 합니다.

[WebListener 서버](xref:fundamentals/servers/weblistener) iis 역방향 프록시 구성에서 작동 하지 것입니다. 사용 해야는 [Kestrel 서버](xref:fundamentals/servers/kestrel)합니다.

## <a name="iis-configuration"></a>IIS 구성

사용 하도록 설정 된 **웹 서버 (IIS)** 역할 역할 서비스를 설정 합니다.

### <a name="windows-desktop-operating-systems"></a>Windows 데스크톱 운영 체제

이동 **제어판 > 프로그램 > 프로그램 및 기능 > Windows 기능 설정 또는 해제** (왼쪽 화면). 에 대 한 그룹을 열고 **인터넷 정보 서비스** 및 **웹 관리 도구**합니다. 에 대 한 확인란 **IIS 관리 콘솔**합니다. 에 대 한 확인란 **World Wide Web 서비스**합니다. 에 대 한 기본 기능을 그대로 **World Wide Web 서비스** 하거나 필요에 따라 IIS 기능을 사용자 지정 합니다.

![IIS 관리 콘솔 및 World Wide Web 서비스는 Windows 기능에서 선택 됩니다.](iis/_static/windows-features-win10.png)

### <a name="windows-server-operating-systems"></a>Windows Server 운영 체제

서버 운영 체제에 대 한 사용은 **역할 및 기능 추가** 통해 마법사는 **관리** 메뉴 또는에 있는 링크 **서버 관리자**합니다. 에 **서버 역할** 단계에서 확인란을 **웹 서버 (IIS)**합니다.

![웹 서버 IIS 역할을 서버 선택 역할 단계에서 선택 합니다.](iis/_static/server-roles-ws2016.png)

에 **역할 서비스** 단계에서 원하는 또는 기본 역할 서비스 제공에 동의 IIS 역할 서비스를 선택 합니다.

![역할 선택 서비스 단계에서 기본 역할 서비스 선택 됩니다.](iis/_static/role-services-ws2016.png)

진행의 **확인** 웹 서버 역할 및 서비스를 설치 하는 단계입니다. 웹 서버 (IIS) 역할을 설치한 후 서버/IIS 다시 시작 필요 하지 않습니다.

## <a name="install-the-net-core-windows-server-hosting-bundle"></a>.NET Core Windows 서버 호스팅 번들을 설치

1. 설치는 [.NET 핵심 Windows Server 호스팅](https://go.microsoft.com/fwlink/?linkid=837808) 호스팅 시스템에 번들. 번들은.NET Core 런타임,.NET 핵심 라이브러리를 설치 및 [ASP.NET 핵심 모듈](xref:fundamentals/servers/aspnet-core-module)합니다. 모듈은 IIS와 Kestrel 서버 간의 역방향 프록시를 만듭니다. 참고: 시스템으로 지정 되지 않은 인터넷에 연결 하는 경우 가져오기 및 설치는 *[Microsoft Visual c + + 2015 재배포 가능](https://www.microsoft.com/download/details.aspx?id=53840)* .NET 핵심 Windows Server 호스팅 번들을 설치 하기 전에 합니다.

2. 시스템 다시 시작 하거나 실행 **net stop가 /y** 뒤 **net 시작 w3svc** 시스템 경로에 대 한 변경을 선택 하도록 명령 프롬프트에서.

> [!NOTE]
> 만 하려는 경우 호스트 [자체 포함된 배포](https://docs.microsoft.com/dotnet/articles/core/deploying/) 및 따라서 하지 않아도 시스템에.NET Core 런타임만 관리자 명령 프롬프트에서 설치 관리자를 실행 하 여 ASP.NET 핵심 모듈을 설치할 수 있는: **DotNetCore.1.0.3_1.1.0 WindowsHosting.exe OPT_INSTALL_LTS_REDIST&0; OPT_INSTALL_FTS_REDIST = =&0;**

> [!NOTE]
> IIS 구성을 공유를 사용 하는 경우 참조 [IIS 구성을 공유를 사용 하 여 ASP.NET 핵심 모듈](xref:hosting/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)합니다.

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Visual Studio와 함께 게시 하는 경우 웹 배포 설치

Visual Studio에서 웹 배포로 응용 프로그램을 배포 하려는 경우 호스팅 시스템에 최신 버전의 웹 배포를 설치 합니다. 웹 배포를 설치 하려면 사용할 수는 [웹 플랫폼 설치 관리자 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 하거나 설치 관리자에서 직접 얻습니다는 [Microsoft 다운로드 센터](https://www.microsoft.com/search/result.aspx?q=webdeploy&form=dlc)합니다. WebPI를 사용 하 여 것이 좋습니다. WebPI는 독립 실행형 설치 프로그램 및 호스팅 공급자에 대 한 구성을 제공 합니다.

## <a name="application-configuration"></a>응용 프로그램 구성

### <a name="enabling-the-iisintegration-components"></a>IISIntegration 구성 요소를 사용 하도록 설정

종속성을 포함 하는 *Microsoft.AspNetCore.Server.IISIntegration* 응용 프로그램 종속성에 패키지 합니다. IIS 통합 미들웨어를 추가 하 여 응용 프로그램에 통합 된 *합니다. UseIISIntegration()* 확장 메서드를 *WebHostBuilder()*합니다. 코드 호출 *합니다. UseIISIntegration()* 코드 이식성에 영향을 주지 않습니다.

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .UseContentRoot(Directory.GetCurrentDirectory())
    .UseIISIntegration()
    .UseStartup<Startup>()
    .Build();
```

### <a name="setting-iisoptions-for-the-iisintegration-service"></a>IISOptions IISIntegration 서비스에 대 한 설정

구성 하려면 *IISIntegration* 서비스 옵션에 대 한 서비스 구성을 포함, *IISOptions* 에서 *ConfigureServices*합니다.

```csharp
services.Configure<IISOptions>(options => {
  ...
});
```

| 옵션 | 설정|
| --- | --- | 
| AutomaticAuthentication | True 이면 인증 미들웨어를 도착 요청 사용자 alter 제네릭 과제에 응답 합니다. False 이면 인증 미들웨어만 id 제공 되며 챌린지에 theAuthenticationScheme 하 여 명시적으로 지정 하는 경우 응답 |
| ForwardClientCertificate | True 및 `MS-ASPNETCORE-CLIENTCERT` 가 있으면 요청 헤더는 `ITLSConnectionFeature` 채워집니다. |
| ForwardWindowsAuthentication | True 이면 인증 미들웨어 플랫폼 처리기 windows 인증을 사용 하 여 인증 하려고 합니다. False 이면 인증 미들웨어 추가 되지 않습니다. |

### <a name="webconfig"></a>web.config

*web.config* 파일 ASP.NET 핵심 모듈을 구성 하 고 다른 IIS 구성을 제공 합니다. 만들기, 변환 및 게시 *web.config* 에 의해 처리 됩니다 `Microsoft.NET.Sdk.Web`, 맨 위에 있는 프로젝트의 SDK를 설치할 때 포함 된 프로그램 *.csproj* 파일인 `<Project Sdk="Microsoft.NET.Sdk.Web">`합니다.

없는 경우는 *web.config* 함께 게시 하면 프로젝트의 파일 *dotnet 게시* 또는 Visual Studio와 함께 게시, 게시 된 출력에서을 파일이 만들어집니다. 올바른 변환 되는 파일이 프로젝트에 있는 경우 *processPath* 및 *인수* ASP.NET 핵심 모듈을 구성 하려면 게시 된 출력으로 이동 합니다. 변환 파일에 포함 되어 있는 IIS 구성 설정을 수정 하지 않는 합니다.

## <a name="deploy-the-application"></a>응용 프로그램 배포

1. 대상 IIS 시스템에서 응용 프로그램의 게시 된 폴더와 설명 되어 있는 파일을 포함할 폴더를 만듭니다 [디렉터리 구조](xref:hosting/directory-structure)합니다.

2. 만든 폴더를 만듭니다를 *로그* (로깅을 사용 하도록 설정 하려면) 하는 경우 응용 프로그램을 보관할 폴더를 기록 합니다. 응용 프로그램을 배포 하려는 경우는 *로그* 폴더 페이로드에이 단계를 건너뛸 수 있습니다.

3. **IIS 관리자**, 새 웹 사이트를 만듭니다. 제공을 **사이트 이름** 설정 하 고는 **실제 경로** 만든 응용 프로그램의 배포 폴더에 있습니다. 제공 된 **바인딩** 구성 웹 사이트를 만듭니다.

4. 응용 프로그램 풀 설정 **No 관리 코드**합니다. ASP.NET Core 별도 프로세스에서 실행 하 고 런타임에 관리 합니다.

5. 열기는 **웹 사이트 추가** 창입니다.

   ![웹 사이트의 사이트 상황에 맞는 메뉴에서 추가 클릭 합니다.](iis/_static/add-website-context-menu-ws2016.png)

6. 웹 사이트를 구성 합니다.

   ![사이트 이름, 실제 경로 및 웹 사이트 추가 단계에서 호스트 이름을 제공 합니다.](iis/_static/add-website-ws2016.png)

7. 에 **응용 프로그램 풀** 패널의 **응용 프로그램 풀 편집** 웹 사이트의 응용 프로그램 풀을 마우스 오른쪽 단추로 클릭 하 고 선택 하 여 창 **기본 설정...** 팝업 메뉴에서 합니다.

   ![응용 프로그램 풀의 상황에 맞는 메뉴에서 기본 설정을 선택 합니다.](iis/_static/apppools-basic-settings-ws2016.png)

8. 설정의 **.NET CLR 버전** 를 **No 관리 코드**합니다.

   ![.NET CLR 버전에 대 한 관리 되는 코드를 설정 합니다.](iis/_static/edit-apppool-ws2016.png)
   
> [!NOTE]
> 응용 프로그램 풀의 기본 id를 변경 하는 경우 **ApplicationPoolIdentity**, 새 id에는 응용 프로그램의 폴더 및 데이터베이스에 액세스 하는 데 필요한 권한이 있는지 확인 합니다.

## <a name="deploy-the-application"></a>응용 프로그램 배포
대상 IIS 시스템에서 만든 폴더에 응용 프로그램을 배포 합니다. 웹 배포는 배포에 대 한 권장 되는 메커니즘입니다. 웹 배포에 대 한 대안 다음과 같습니다.

### <a name="web-deploy-with-visual-studio"></a>Visual Studio와 함께 웹 배포
만들기는 [Visual Studio에서 게시 프로필](https://msdn.microsoft.com/library/dd465337(v=vs.110).aspx#Anchor_0) 클릭 하 고는 **게시** 응용 프로그램을 배포 하는 단추입니다. 호스팅 공급자가 게시 프로필 또는 만들기에 대 한 지원을 제공 하는 경우 해당 프로필을 다운로드 하 여 Visual Studio를 사용 하 여 가져옵니다 **웹 게시** 대화 합니다.

![대화 상자 페이지를 게시 합니다.](iis/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Visual Studio 외부에서 웹 배포
명령줄에서 Visual Studio 외부에서 웹 배포을 사용할 수도 있습니다. 자세한 내용은 참조 [웹 배포 도구](https://technet.microsoft.com/library/dd568996(WS.10).aspx)합니다.

### <a name="alternatives-to-web-deploy"></a>웹에 대 한 대안을 배포
웹 배포를 사용 하지 않으려는 하거나 Visual Studio를 사용 하지 않는 경우 Xcopy, Robocopy, PowerShell 등 호스팅 시스템에 응용 프로그램을 이동 하려면 여러 가지 방법 중 하나를 사용할 수 있습니다. Visual Studio 사용자가 사용할 수는 [게시할 샘플](https://github.com/aspnet/vsweb-publish/blob/master/samples/samples.md)합니다.

## <a name="browse-the-website"></a>웹 사이트 찾아보기
![Microsoft Edge 브라우저 IIS 시작 페이지를 로드 했습니다.](iis/_static/browsewebsite.png)
   
>[!WARNING]
> .NET core 응용 프로그램은 IIS와 Kestrel 서버 간의 역방향 프록시를 통해 호스트 됩니다. 역방향 프록시를 만들기 위해는 *web.config* 파일은 IIS에 제공 된 웹 사이트 실제 경로 배포 된 응용 프로그램의 콘텐츠 루트 경로 (일반적으로 응용 프로그램 기본 경로)에 있어야 합니다. 등의 하위 폴더를 비롯 하 여 응용 프로그램의 실제 경로에 중요 한 파일이 있는 *my_application.runtimeconfig.json*, *my_application.xml* (XML 문서 주석) 및 *my_application.deps.json*합니다. *web.config* 파일에 IIS가 이러한 파일 및 기타 중요 한 파일을 처리 하지 않도록 하는 Kestrel 역방향 프록시를 만드는 데 필요 합니다. **따라서 것이 중요 하는*web.config* 이름이 바뀌거나 제거에서 deployment.* * 파일은 실수로 되지

## <a name="create-a-data-protection-registry-hive"></a>데이터 보호 레지스트리 하이브를 만들기

ASP.NET 응용 프로그램에서 사용 되는 데이터 보호 키 레지스트리 하이브는 응용 프로그램에 대 한 외부에 저장 됩니다. 지정된 된 응용 프로그램에 대 한 키를 유지 하기 위해 응용 프로그램의 응용 프로그램 풀에 대 한 레지스트리 하이브를 만들어야 합니다.

독립 실행형 IIS 설치를 사용할 수는 [데이터 보호 조항 AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1) ASP.NET 핵심 응용 프로그램과 함께 사용 되는 각 응용 프로그램 풀입니다. 레지스트리에서 키를 유지 됩니다.

웹 팜 시나리오에 해당 데이터 보호 키 고리를 저장 하는 UNC 경로 사용 하 여 응용 프로그램을 구성할 수 있습니다. 기본적으로 데이터 보호 키를 암호화 되지 됩니다. x509를 배포할 수 있습니다 키 고리를 암호화 하는 각 컴퓨터에는 인증서입니다. 참조 [데이터 보호 구성](xref:security/data-protection/configuration/overview#data-protection-configuring) 대 한 자세한 내용은 합니다.

> [!WARNING]
> 데이터 보호는 인증에 사용 되는 포함 하 여 다양 한 ASP.NET 미들웨어를 사용 합니다. 사용자 고유의 코드에서 데이터 보호 Api를 구체적으로 호출 하지 않으면 경우에 배포 스크립트 또는 사용자 코드에서 데이터 보호를 구성 해야 합니다. 기본적으로 IIS를 사용 하는 경우 데이터 보호를 구성 하지 않은 경우 키를 메모리에 보관 하 고 응용 프로그램이 종료 되거나 다시 시작 되 면 삭제 됩니다. 이 다음 예를 들어, 무효화 쿠키 인증으로 작성 된 쿠키와 사용자가 다시 로그인 해야 합니다.

## <a name="configuration-of-sub-applications"></a>하위 응용 프로그램의 구성

응용 프로그램 루트 응용 프로그램이 IIS 사이트의 루트 응용 프로그램을 추가 하는 경우 *web.config* 파일에 포함 되어야는 `<handlers>` 섹션에서 응용 프로그램에 대 한 처리기로 ASP.NET 핵심 모듈을 추가 합니다. 응용 프로그램 루트 응용 프로그램에 추가 포함 하지 않아야는 `<handlers>` 섹션입니다. 반복 하는 경우는 `<handlers>` 하위-응용 프로그램에서의 섹션 *web.config* 파일을 하위 응용 프로그램을 탐색 하려고 할 때 잘못 된 구성 파일을 참조 하는 500.19 (내부 서버 오류) 표시 됩니다.

## <a name="configuration-of-iis-with-webconfig"></a>Web.config의 IIS 구성

IIS 구성을 계속 받습니다는 `<system.webServer>` 섹션 *web.config* 역방향 프록시 구성에 적용 하는 IIS 기능에 대 한 합니다. 예를 들어 동적 압축을 사용 하 여 IIS가 시스템 수준에서 구성 하지만 있습니다 해당 설정을 사용 하 여 앱에 대 한 사용 하지 않도록 설정할 수는 `<urlCompression>` 앱의 요소 *web.config* 파일입니다. 자세한 내용은 참조는 [에 대 한 구성 참조 `<system.webServer>` ](https://www.iis.net/configreference/system.webserver), [ASP.NET 핵심 모듈 구성 참조](xref:hosting/aspnet-core-module), 및 [ASP.NET 코어를 사용 하 여 IIS 모듈](xref:hosting/iis-modules)합니다.

## <a name="configuration-sections-of-webconfig"></a>Web.config의 구성 섹션

으로 구성 된.NET Framework 응용 프로그램과 달리는 `<system.web>`, `<appSettings>`, `<connectionStrings>`, 및 `<location>` 요소 *web.config*, ASP.NET 핵심 응용 프로그램은 다른 구성 공급자를 사용 하 여 구성 됩니다. 자세한 내용은 참조 [구성](xref:fundamentals/configuration)합니다.

## <a name="application-pools"></a>응용 프로그램 풀

단일 시스템에 여러 웹 사이트를 호스팅하는 경우 자체 응용 프로그램 풀에서 각 응용 프로그램을 실행 하 여 응용 프로그램을 서로 격리 해야 합니다. IIS **웹 사이트 추가** 대화 기본적으로이 동작 합니다. 제공 하는 경우는 **사이트 이름**, 텍스트는 자동으로 전송 되는 **응용 프로그램 풀** 텍스트 상자에 붙여넣습니다. 새 응용 프로그램 풀 사이트 이름을 사용 하 여 웹 사이트를 추가할 때 생성 됩니다.

## <a name="application-pool-identity"></a>응용 프로그램 풀 Id

응용 프로그램 풀 id 계정을 만들고 도메인 또는 로컬 계정을 관리 하지 않고도 고유 계정으로 응용 프로그램을 실행할 수 있습니다. IIS 8.0 이상에 IIS Admin 작업자 프로세스 (WAS)는 가상 계정이 새 응용 프로그램 풀의 이름으로를 만들어 기본적으로이 계정으로 응용 프로그램 풀의 작업자 프로세스를 실행 합니다. IIS 관리 콘솔의 응용 프로그램 풀에 대 한 고급 설정에서 Id를 사용 하도록 설정 되어 있는지를 확인 **ApplicationPoolIdentity** 아래 그림과 같이 합니다.

![응용 프로그램 풀에 대 한 고급 설정 대화 상자](iis/_static/apppool-identity.png)

IIS 관리 프로세스는 Windows 보안 시스템에서 응용 프로그램 풀의 이름으로 한 보안 식별자를 만듭니다. 이 id;를 사용 하 여 리소스를 보호할 수 있습니다. 그러나이 id는 실제 사용자 계정이 아닙니다 및 Windows 사용자 관리 콘솔에 나타나지 않습니다.

IIS 작업자 프로세스 응용 프로그램에 액세스를 상승 된 권한을 부여 해야 할 경우에 응용 프로그램이 포함 된 디렉터리에 대 한 액세스 제어 목록 (ACL)을 수정 해야 합니다.

1. Windows 탐색기를 열고 디렉터리로 이동 합니다.

2. 마우스 오른쪽 단추로 클릭 하 여 디렉터리 **속성**합니다.

3. 아래는 **보안** 탭을 클릭는 **편집** 단추 차례로 **추가** 단추입니다.

4. 클릭 된 **위치** 단추 및 시스템을 선택 했는지 확인 합니다.

5. Enter **IIS AppPool\DefaultAppPool** 에서 **선택할 개체 이름을 입력** 텍스트 상자에 붙여넣습니다.

  ![사용자 또는 응용 프로그램 폴더에 대 한 그룹 대화 상자를 선택 합니다.](iis/_static/select-users-or-groups-1.png)

6. 클릭 하 고 **이름 확인** 단추 클릭 한 후 **확인**합니다.

  ![사용자 또는 응용 프로그램 폴더에 대 한 그룹 대화 상자를 선택 합니다.](iis/_static/select-users-or-groups-2.png)

또한 이렇게 하려면 사용 하 여 명령 프롬프트를 통해 **ICACLS** 도구:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool" :F
```

## <a name="troubleshooting-tips"></a>문제 해결 팁

IIS 배포 문제를 진단 하려는 브라우저 출력 연구, 시스템의 검사 **응용 프로그램** 를 통해 로그온 **이벤트 뷰어**를 사용 하도록 설정 하 고 `stdout` 로깅. **ASP.NET 핵심 모듈** 로그 섹션에 제공 된 경로에 *stdoutLogFile* 의 특성은 `<aspNetCore>` 요소에 *web.config*합니다. 특성 값에 제공 된 경로에 있는 모든 폴더는 배포에 존재 해야 합니다. 으로 설정 해야 *stdoutLogEnabled = "true"*합니다. 사용 하는 응용 프로그램은 `Microsoft.NET.Sdk.Web` 만들려면 SDK를는 *web.config* 파일의 기본값은 *stdoutLogEnabled* 설정을 *false*수동으로 제공 해야 하므로 *web.config* 파일을 사용 하도록 설정 하기 위해 파일을 수정 하거나 `stdout` 로깅.

일반적인 오류에 표시 되지 않습니다 브라우저, 응용 프로그램 로그 및 ASP.NET 핵심 모듈 로그 모듈 될 때까지 *startupTimeLimit* (기본값: 120 초) 및 *startupRetryCount* (기본값: 2)를 통과 합니다. 따라서 모듈에서 응용 프로그램에 대 한 프로세스를 시작 하지를 추론 하기 전에 전체&6; 분을 기다리십시오.

빠르게 응용 프로그램이 제대로 작동 하는지 확인 하려면 Kestrel에서 직접 응용 프로그램을 실행 하는 것입니다. 응용 프로그램 프레임 워크 종속 배포로 게시 하는 경우 실행 **dotnet my_application.dll** 배포 폴더에 있는 가장 응용 프로그램에 IIS 실제 경로입니다. 응용 프로그램 배포는 자체 포함 된 배포로 게시 하는 경우 명령 프롬프트에서 직접 응용 프로그램의 실행 파일을 실행 **my_application.exe**, 배포 폴더에 있습니다. 응용 프로그램을 찾을 수 있어야 Kestrel 기본 포트 5000에서 수신 하는 경우 `http://localhost:5000/`합니다. 응용 프로그램 응답 Kestrel 끝점 주소에서 일반적으로 IIS ASP.NET 핵심 모듈 Kestrel 구성 하 고 자체 응용 프로그램 내에서 가능성이 적은 문제 가능성이 높습니다 관련 되어 있습니다.

스타일 시트, 스크립트 또는 응용 프로그램의 정적 파일에서 이미지에 대 한 간단한 정적 파일 요청을 수행 하는 Kestrel 서버가 제대로 작동 하 고 IIS 역방향 프록시에 있는지 확인 하는 한 가지 방법은 *wwwroot* 를 사용 하 여 [정적 파일 미들웨어](xref:fundamentals/static-files)합니다. 응용 프로그램 정적 파일을 사용할 수 있어도 MVC 뷰 및 기타 끝점 실패 하는 IIS ASP.NET 핵심 모듈 Kestrel 구성 하 고 응용 프로그램 (예: MVC 라우팅 또는 500 내부 서버 오류) 자체 내에서 간단할수록 가능성이 관련이 더 문제가입니다.

환경 변수를 추가 하 고 일시적으로 수 Kestrel IIS 뒤 정상적으로 시작 하는 경우 응용 프로그램은 성공적으로 로컬로 실행 한 후 시스템에서 실행 되지 않습니다 *web.config* 설정 하는 `ASPNETCORE_ENVIRONMENT` 를 `Development`합니다. 이 통해 환경에 응용 프로그램 시작, 재정의 하지 않는으로 [개발자 예외 페이지](xref:fundamentals/error-handling) 표시 응용 프로그램이 실행 될 때 시스템에 있습니다. 에 대 한 환경 변수를 설정 `ASPNETCORE_ENVIRONMENT` 이렇게에서만 권장 됩니다 노출 되지 않은 준비/테스트 시스템에 대 한 인터넷에 있습니다. 환경 변수를 제거 해야는 *web.config* 파일을 완료 합니다. 통해 환경 변수 설정에 대 한 내용은 *web.config* 역방향 프록시를 참조 하십시오. [environmentVariables 자식 요소의 aspNetCore](xref:hosting/aspnet-core-module#setting-environment-variables)합니다.

대부분의 경우에서 응용 프로그램 로깅은 응용 프로그램 또는 역방향 프록시 문제 해결에 도움이 됩니다. 참조 [로깅](xref:fundamentals/logging) 에 대 한 자세한 내용은 합니다.

마지막 문제 해결 팁 앱 내에서 개발 컴퓨터 또는 패키지 버전에.NET Core SDK 중 하나를 업그레이드 한 후 실행에 실패 하는 앱에 적용 됩니다. 일부 경우에 모순 된 패키지 주요 업그레이드를 수행할 때 응용 프로그램을 중단 될 수 있습니다. 삭제 하 여 이러한 문제를 대부분 해결할 수 있습니다는 `bin` 및 `obj` 의 선택을 취소 하는 프로젝트의 폴더에서 캐시 패키지 `%UserProfile%\.nuget\packages\` 및 `%LocalAppData%\Nuget\v3-cache`, 프로젝트, 복원 및 시스템에서 이전 배포 응용 프로그램을 다시 배포 하기 전에 완전히 삭제 되었는지 확인 합니다.

>[!TIP]
> 패키지 캐시의 선택을 취소 하는 편리한 방법을 얻을 수는 `NuGet.exe` 에서 도구를 [NuGet.org](https://www.nuget.org/), 시스템 경로 추가 하 고 실행 `nuget locals all -clear` 명령 프롬프트에서.

## <a name="common-errors"></a>일반적인 오류

다음은 하지 오류의 전체 목록입니다. 아래에 있는 의견 섹션에 자세한 오류 메시지를을 남겨 주세요 여기에 나열 되지 오류가 발생 해야 합니다.

### <a name="installer-unable-to-obtain-vc-redistributable"></a>설치 관리자를 VC + + 재배포 가능 패키지를 가져올 수 없습니다.

* **설치 관리자 예외:** 0x80072efd 또는 0x80072f76-알 수 없는 오류

* **설치 관리자 로그 Exception†:** 오류 0x80072efd 또는 0x80072f76: EXE 패키지를 실행 하지 못했습니다.

  †The 로그는 C:\Users\\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{timestamp}.log 합니다.

문제 해결:

* 시스템 번들을 호스팅하는 서버를 설치 하는 동안 인터넷에 액세스할 수 없는 경우이 예외는 설치 관리자를 얻을 수 없거나 하는 경우는 *Microsoft Visual c + + 2015 재배포 가능*합니다. 설치 관리자를 얻을 수 있습니다는 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=53840)합니다. 설치 관리자에서 알 수 없으면 호스트 프레임 워크 종속 배포 하는 데 필요한.NET Core 런타임을 받지 못할 수 있습니다. 프레임 워크 종속 배포 호스트, 프로그램에서 런타임이 설치 되어 있는지 확인 하려는 경우 &amp; 기능입니다. 런타임 설치 관리자를 얻을 수 있습니다 [.NET 다운로드](https://www.microsoft.com/net/download/core)합니다. 런타임에 설치한 후 시스템 다시 시작 하거나 실행 하 여 IIS를 다시 시작 **net stop가 /y** 뒤 **net 시작 w3svc** 명령 프롬프트에서.

### <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>OS 업그레이드는 32 비트 ASP.NET 핵심 모듈 제거

* **응용 프로그램 로그:** 모듈 DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** 를 로드 하지 못했습니다. 데이터 오류입니다.

문제 해결:

* 비-운영 체제 파일에 **C:\Windows\SysWOW64\inetsrv** 디렉터리는 운영 체제 동안 보존 되지 않습니다 업그레이드 합니다. ASP.NET Core 모듈이 OS 업그레이드를 하기 전에 설치 되어 있어야 하 고 다음 운영 체제 업그레이드 한 후 모든 AppPool 32 비트 모드에서 실행 하려고 하는 경우이 문제가 발생 합니다. OS 업그레이드 후 ASP.NET 핵심 모듈을 복구 합니다. 참조 [.NET 핵심 Windows Server 호스팅 번들을 설치](#install-the-net-core-windows-server-hosting-bundle)합니다. 선택 **복구** 설치 관리자를 실행 하는 경우.

### <a name="platform-conflicts-with-rid"></a>RID 사용 하 여 플랫폼 충돌

* **브라우저:** HTTP 오류 502.5-프로세스 실패

* **응용 프로그램 로그:** 응용 프로그램 ' MACHINE/WEBROOT/APPHOST/MY_APPLICATION ' 실제 루트와 ' c:\{경로}\' 명령줄으로 프로세스를 시작 하지 못했습니다. ' "c:\\{경로} \my_application. { exe | dll을 (를) "', 오류 코드 = '&0;x80004005: ff입니다.

* **ASP.NET 핵심 모듈 로그:** 처리 되지 않은 예외: System.BadImageFormatException: 'my_application.dll' 파일 또는 어셈블리를 로드할 수 없습니다. 잘못 된 형식으로 프로그램을 로드 하려고 했습니다.

문제 해결:

* Kestrel 응용 프로그램이 로컬로 실행을 확인 합니다. 프로세스 오류를 응용 프로그램 내의 문제 때문일 수 있습니다. 자세한 내용은 참조 [문제 해결 팁](#troubleshooting-tips)합니다.

* 설정 하지 않은 있는지 확인 한 `<PlatformTarget>` 에 프로그램 *.csproj* RID와 충돌 하는 합니다. 예를 들어 지정 하지 않는 한 `<PlatformTarget>` 의 `x86` 의 RID를 사용 하 여 게시 및 `win10-x64`를 사용 하 여 *dotnet 게시-c-r win10-x64 릴리스* 하거나 설정 하 여는 `<RuntimeIdentifiers>` 에 프로그램 *.csproj* 를 `win10-x64`. 프로젝트 경고나 오류 없이 게시 않지만 시스템에 위의 기록 된 예외와 함께 실패 합니다.

* 이 예외가 응용 프로그램을 업그레이드할 때 Azure 앱 배포에 대해 발생 하 고 이전 배포에서 모든 파일을 수동으로 삭제 최신 어셈블리를 배포 합니다. 호환 되지 않는 어셈블리 느린 될 수는 `System.BadImageFormatException` 업그레이드 된 응용 프로그램을 배포 하는 경우는 예외입니다.

### <a name="uri-endpoint-wrong-or-stopped-website"></a>URI 끝점 잘못 되었거나 중지 된 웹 사이트

* **브라우저:** ERR_CONNECTION_REFUSED

* **응용 프로그램 로그:** 항목이

* **ASP.NET 핵심 모듈 로그:** 로그 파일이 생성 되지 않았습니다

문제 해결:

* 응용 프로그램에 대 한 올바른 URI 끝점을 사용 하는 것을 확인 합니다. 바인딩을 확인 합니다.

* IIS 웹 사이트에 없는 확인는 *Stopped* 상태입니다.

### <a name="corewebengine-or-w3svc-server-features-disabled"></a>CoreWebEngine 또는 W3SVC 서버 기능 사용 안 함

* **OS 예외:** ASP.NET 핵심 모듈을 사용 하 여 IIS 7.0 CoreWebEngine 및 W3SVC 기능을 설치 해야 합니다.

문제 해결:

* 적절 한 역할 및 기능을 설정 했는지 확인 합니다. 참조 [IIS 구성](#iis-configuration)합니다.

### <a name="incorrect-website-physical-path-or-application-missing"></a>실제 경로 잘못 된 웹 사이트 또는 응용 프로그램이 없습니다.

* **브라우저:** 403 사용할 수 없음-액세스가 거부 되었습니다 **-또는--** 403.14 사용할 수 없음-웹 서버가이 디렉터리의 내용을 표시 하지 못하도록 구성 됩니다.

* **응용 프로그램 로그:** 항목이

* **ASP.NET 핵심 모듈 로그:** 로그 파일이 생성 되지 않았습니다

문제 해결:

* IIS 웹 사이트 확인 **기본 설정** 및 실제 응용 프로그램 폴더입니다. IIS 웹 사이트에 있는 폴더에 응용 프로그램 인지 확인 **실제 경로**합니다.

### <a name="incorrect-role-module-not-installed-or-incorrect-permissions"></a>잘못 된 역할, 설치 되지 않은 모듈 또는 잘못 된 사용 권한

* **브라우저:** 내부 서버 오류 500.19-페이지에 대 한 관련된 구성 데이터가 잘못 되어 요청된 된 페이지를 액세스할 수 없습니다.

* **응용 프로그램 로그:** 항목이

* **ASP.NET 핵심 모듈 로그:** 로그 파일이 생성 되지 않았습니다

문제 해결:

* 적절 한 역할을 설정 했는지 확인 합니다. 참조 [IIS 구성](#iis-configuration)합니다.

* 확인 **프로그램 &amp; 기능** 있는지 확인 하 고는 **Microsoft ASP.NET 핵심 모듈** 설치가 완료 된 후입니다. 하는 경우는 **Microsoft ASP.NET 핵심 모듈** 있지 않으면 설치 된 프로그램 목록에 있는 모듈을 설치 합니다. 참조 [.NET 핵심 Windows Server 호스팅 번들을 설치](#install-the-net-core-windows-server-hosting-bundle)합니다.

* 다음 사항을 확인는 **응용 프로그램 풀 > 프로세스 모델 > Identity** 로 설정 된 **ApplicationPoolIdentity** 또는 사용자의 사용자 지정 id에 응용 프로그램의 배포 폴더에 액세스할 수 있는 권한이 있습니다.

### <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>잘못 된 processPath, 누락 된 PATH 변수, 설치 되지 않은 호스팅 번들, 시스템/IIS를 다시 시작 하지, VC + + 재배포 가능 패키지를 설치 하지 또는 dotnet.exe 액세스 위반

* **브라우저:** HTTP 오류 502.5-프로세스 실패

* **응용 프로그램 로그:** 응용 프로그램 ' MACHINE/WEBROOT/APPHOST/MY_APPLICATION ' 실제 루트와 ' c:\\{경로}\' commandline '".\my_application.exe" ' 오류 코드와 프로세스를 시작 하지 못했습니다 ' 0x80070002 =: 0.

* **ASP.NET 핵심 모듈 로그:** 로그 파일 생성 되지만 비어

문제 해결:

* Kestrel 응용 프로그램이 로컬로 실행을 확인 합니다. 프로세스 오류를 응용 프로그램 내의 문제 때문일 수 있습니다. 자세한 내용은 참조 [문제 해결 팁](#troubleshooting-tips)합니다.

* 확인는 *processPath* 특성에 `<aspNetCore>` 요소에 *web.config* 이 있는지 확인 하려면 *dotnet* 프레임 워크 종속 배포 또는 *.\my_application.exe* 자체 포함 된 배포에 대 한 합니다.

* 프레임 워크에 종속적인 배포용 *dotnet.exe* 경로 설정을 통해 액세스할 수 없습니다. 확인 하는 * C:\Program Files\dotnet\* 시스템 경로 설정에 있습니다.

* 프레임 워크에 종속적인 배포용 *dotnet.exe* 응용 프로그램 풀의 사용자 id에 액세스할 수 없습니다. AppPool 사용자 id에 대 한 액세스에 확인 된 *C:\Program Files\dotnet* 디렉터리입니다. AppPool 사용자 id에 대해 구성 된 거부 규칙 확인은 *C:\Program Files\dotnet* 및 응용 프로그램 디렉터리입니다.

* 프레임 워크 종속 배포 배포 및.NET Core IIS를 다시 시작 하지 않고 설치 않은 될 수 있습니다. 서버를 다시 시작 하거나 실행 하 여 IIS를 다시 시작 **net stop가 /y** 뒤 **net 시작 w3svc** 명령 프롬프트에서.

* .NET Core 런타임 호스팅 시스템에 설치 하지 않고 프레임 워크 종속 배포를 배포 했을 수 있습니다. 경우 하려는 프레임 워크 종속 배포를 배포 하 고 실행 하는.NET Core 런타임 설치 하지 않은 **번들 installer.NET 핵심 Windows Server 호스팅** 시스템에 있습니다. 참조 [.NET 핵심 Windows Server 호스팅 번들을 설치](#install-the-net-core-windows-server-hosting-bundle)합니다. 인터넷에 연결 하지 않고 시스템에.NET Core 런타임을 설치를 시도 하는 경우 가져올에서 런타임을 [.NET 다운로드](https://www.microsoft.com/net/download/core) 호스팅 번들 설치 관리자를 실행 관리자 명령 프롬프트에서 설치를 사용 하 여 모듈 및 **DotNetCore.1.1.0 WindowsHosting.exe OPT_INSTALL_LTS_REDIST&0; OPT_INSTALL_FTS_REDIST = =&0;**합니다. 시스템을 다시 시작 하거나 실행 하 여 IIS를 다시 시작 하 여 설치를 완료 **net stop가 /y** 뒤 **net 시작 w3svc** 명령 프롬프트에서.

* 프레임 워크 종속 배포 배포 및.NET Core 시스템/IIS를 다시 시작 하지 않고 설치 않은 될 수 있습니다. 시스템을 다시 시작 하거나 실행 하 여 IIS를 다시 시작 **net stop가 /y** 뒤 **net 시작 w3svc** 명령 프롬프트에서.

* 프레임 워크 종속 배포 배포 했을 수 및 *Microsoft Visual c + + 2015 재배포 가능 (x64)* 시스템에 설치 되지 않았습니다. 설치 관리자를 얻을 수 있습니다는 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=53840)합니다.

### <a name="incorrect-arguments-of-aspnetcore-element"></a>잘못 된 인수를 \<aspNetCore\> 요소

* **브라우저:** HTTP 오류 502.5-프로세스 실패

* **응용 프로그램 로그:** 응용 프로그램 ' MACHINE/WEBROOT/APPHOST/MY_APPLICATION ' 실제 루트와 ' c:\\{경로}\' 명령줄 오류 코드 ' "dotnet".\my_application.dll'와 프로세스를 시작 하지 못했습니다 ' 0x80004005 =: 80008081 합니다.

* **ASP.NET 핵심 모듈 로그:** 실행 하려면 응용 프로그램이 없습니다: 'PATH\my_application.dll'

문제 해결:

* Kestrel 응용 프로그램이 로컬로 실행을 확인 합니다. 프로세스 오류를 응용 프로그램 내의 문제 때문일 수 있습니다. 자세한 내용은 참조 [문제 해결 팁](#troubleshooting-tips)합니다.

* 검사는 *인수* 특성에 `<aspNetCore>` 요소에 *web.config* 중 하나 인지 확인 하려면 (a) *.\my_applciation.dll* 프레임 워크 종속 배포; 또는 (b) 현재, 빈 문자열에 대 한 (*인수 = ""*), 또는 응용 프로그램의 인수 목록 (*인수 = "arg1, arg2,..."*) 자체 포함 된 배포에 대 한.

### <a name="missing-net-framework-version"></a>누락 된.NET Framework 버전

* **브라우저:** 502.3 잘못 된 게이트웨이-요청을 라우팅하는 동안 연결 오류가 했습니다.

* **응용 프로그램 로그:** ErrorCode 물리적 루트와 응용 프로그램 ' MACHINE/WEBROOT/APPHOST/MY_APPLICATION ' = ' c:\\{경로}\' 명령줄 오류 코드 ' "dotnet".\my_application.dll'와 프로세스를 시작 하지 못했습니다 ' 0x80004005 =: 80008081 합니다.

* **ASP.NET 핵심 모듈 로그:** 메서드, 파일 또는 어셈블리 예외 없습니다. 메서드, 파일 또는 예외에 지정 된 어셈블리는.NET Framework 메서드, 파일 또는 어셈블리.

문제 해결:

* 시스템에서 누락 된.NET Framework 버전을 설치 합니다.

* 프레임 워크 종속 배포의 경우 올바른 런타임 시스템에 설치 되어 있는지 확인 합니다. 예제에서는 1.0에서 1.1로 프로젝트를 업그레이드, 호스팅 시스템에 배포 하 고이 예외를 수신 하는 경우 확인에 대 한 호스팅 시스템에 1.1 framework를 설치 합니다.

### <a name="stopped-application-pool"></a>중지 된 응용 프로그램 풀

* **브라우저:** 503 서비스를 사용할 수 없음

* **응용 프로그램 로그:** 항목이

* **ASP.NET 핵심 모듈 로그:** 로그 파일이 생성 되지 않았습니다

문제 해결

* 응용 프로그램 풀에 없는 확인은 *Stopped* 상태입니다.

### <a name="iis-integration-middleware-not-implemented"></a>IIS 통합 미들웨어 구현 되지 않았습니다

* **브라우저:** HTTP 오류 502.5-프로세스 실패

* **응용 프로그램 로그:** 응용 프로그램 ' MACHINE/WEBROOT/APPHOST/MY_APPLICATION ' 실제 루트와 ' c:\\{경로}\' 명령줄을 사용 하 여 프로세스를 만든 ' "c:\\{경로} \my_application. { exe | dll을 (를) "' 하지만 손상 또는 응답 하지 않았습니다 또는 ErrorCode 주어진된 포트 '{PORT}'에서 수신 하지 않은 '0x800705b4' =

* **ASP.NET 핵심 모듈 로그:** 로그 파일 생성 되 고 정상 작동을 보여 줍니다.

문제 해결

* Kestrel 응용 프로그램이 로컬로 실행을 확인 합니다. 프로세스 오류를 응용 프로그램 내의 문제 때문일 수 있습니다. 자세한 내용은 참조 [문제 해결 팁](#troubleshooting-tips)합니다.

* 호출 하 여 IIS 통합 미들웨어를 올바르게 참조 한 확인은 *합니다. UseIISIntegration()* 메서드를 응용 프로그램의 *WebHostBuilder()*합니다.

### <a name="sub-application-includes-a-handlers-section"></a>하위 응용 프로그램에는 \<처리기\> 섹션

* **브라우저:** HTTP 오류 500.19-내부 서버 오류

* **응용 프로그램 로그:** 항목이

* **ASP.NET 핵심 모듈 로그:** 로그 파일이 만들어지고 루트 응용 프로그램에 대 한 일반 작업을 보여 줍니다. 로그 파일을 하위 응용 프로그램을 위해 만들어지지 않았습니다.

문제 해결

* 확인 하는 하위-응용 프로그램의 *web.config* 파일에 포함 되지 않습니다는 `<handlers>` 섹션입니다.

### <a name="application-configuration-general-issue"></a>응용 프로그램 구성에 대 한 일반 문제

* **브라우저:** HTTP 오류 502.5-프로세스 실패

* **응용 프로그램 로그:** 응용 프로그램 ' MACHINE/WEBROOT/APPHOST/MY_APPLICATION ' 실제 루트와 ' c:\\{경로}\' 명령줄을 사용 하 여 프로세스를 만든 ' "c:\\{경로} \my_application. { exe | dll을 (를) "' 하지만 손상 또는 응답 하지 않았습니다 또는 ErrorCode 주어진된 포트 '{PORT}'에서 수신 하지 않은 '0x800705b4' =

* **ASP.NET 핵심 모듈 로그:** 로그 파일 생성 되지만 비어

문제 해결

* 이 일반 예외는 프로세스를 대부분 응용 프로그램 구성 문제 때문에 시작 하지 못했음을 나타냅니다. 참조 [디렉터리 구조](xref:hosting/directory-structure)응용 프로그램의 파일을 배포 하는 적절 한 폴더 및 응용 프로그램의 구성 파일이 있는지 확인 하 고 응용 프로그램 및 환경에 대 한 올바른 설정을 포함 합니다. 자세한 내용은 참조 [문제 해결 팁](#troubleshooting-tips)합니다.

## <a name="resources"></a>리소스

* [ASP.NET 핵심 모듈 개요](xref:fundamentals/servers/aspnet-core-module)

* [ASP.NET 핵심 모듈 구성 참조](xref:hosting/aspnet-core-module)

* [ASP.NET Core IIS 모듈 사용](xref:hosting/iis-modules)

* [ASP.NET Core 소개](../index.md)

* [공식 Microsoft IIS 사이트](http://www.iis.net/)

* [Microsoft TechNet 라이브러리: Windows Server](https://technet.microsoft.com/library/bb625087.aspx)


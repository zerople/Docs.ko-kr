---
title: "Nano Server의 ASP.NET Core"
author: shirhatti
description: "기존 ASP.NET Core 앱을 사용하고 IIS가 실행되는 Nano Server 인스턴스에 배포하는 방법을 알아봅니다."
keywords: ASP.NET Core, nano server
ms.author: riande
manager: wpickett
ms.date: 11/04/2016
ms.topic: article
ms.assetid: 50922cf1-ca58-4006-9236-99b7ff2dd0cf
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/nano-server
ms.openlocfilehash: 337cc69ef522452c17cdd6ea4a5e71cd122035dc
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="aspnet-core-with-iis-on-nano-server"></a>Nano Server의 ASP.NET Core 및 IIS

작성자: [Sourabh Shirhatti](https://twitter.com/sshirhatti)

이 자습서에서는 기존 ASP.NET Core 앱을 사용하고 IIS가 실행되는 Nano Server 인스턴스에 배포합니다.

## <a name="introduction"></a>소개

Nano Server는 Server Core 또는 전체 서버보다 작은 공간, 강화된 보안 및 향상된 서비스를 지원하는 Windows Server 2016의 설치 옵션입니다. 180일 평가 버전에 대한 자세한 내용은 다운로드 링크는 [Nano Server documentation](https://docs.microsoft.com/windows-server/get-started/getting-started-with-nano-server)(Nano Server 설명서)을 참조하세요. 

Nano Server를 사용해 볼 수 있는 세 가지 간편한 방법이 있습니다. MS 계정으로 로그인할 경우:

1. Windows Server 2016 ISO 파일을 다운로드하고 Nano Server 이미지를 빌드할 수 있습니다.

2. Nano Server VHD를 다운로드합니다.

3. Azure 갤러리에서 Nano Server 이미지를 사용하여 Azure에서 VM을 만듭니다. Azure 계정이 없는 경우 30일 평가판을 다운로드할 수 있습니다.

이 자습서에서는 두 번째 옵션인 Windows Server 2016에서 미리 빌드된 Nano Server VHD를 사용합니다.

이 자습서를 진행하기 전에 기존 ASP.NET Core 응용 프로그램의 [게시된 출력](xref:hosting/directory-structure)이 필요합니다. 응용 프로그램이 **64비트** 프로세스에서 실행되도록 빌드되는지를 확인합니다.

## <a name="setting-up-the-nano-server-instance"></a>Nano Server 인스턴스 설정

이전에 다운로드한 VHD를 통해 개발 컴퓨터에서 [Hyper-V를 사용하여 새 가상 컴퓨터를 만듭니다](https://technet.microsoft.com/library/hh846766.aspx). 컴퓨터에서 로그온하기 전에 관리자 암호를 설정해야 합니다. VM 콘솔에서 F11 키를 눌러 처음 로그인하기 전에 암호를 설정합니다. 또한 VM을 프로비전하는 동안 제공된 DHCP 서버의 고정 IP를 확인하거나 Nano Server 복구 콘솔의 네트워킹 설정에서 새 VM IP 주소를 확인해야 합니다.

> [!NOTE]
> 새 VM이 로컬 V4 IP 주소 192.168.1.10을 사용하여 실행된다고 가정해 봅니다.

이제 PowerShell 원격을 사용하여 VM을 관리할 수 있습니다. Nano Server를 완벽하게 관리하는 유일한 방법입니다.

## <a name="connecting-to-your-nano-server-instance-using-powershell-remoting"></a>PowerShell 원격을 사용하여 Nano Server 인스턴스에 연결

관리자 권한으로 PowerShell 창을 열어 원격 Nano Server 인스턴스를 `TrustedHosts` 목록에 추가합니다.

```PowerShell
$nanoServerIpAddress = "192.168.1.10"
Set-Item WSMan:\localhost\Client\TrustedHosts "$nanoServerIpAddress" -Concatenate -Force
```

> [!NOTE]
> 변수 `$nanoServerIpAddress`를 올바른 IP 주소로 바꿉니다.

Nano Server 인스턴스를 `TrustedHosts`에 추가한 후 PowerShell 원격을 사용하여 인스턴스에 연결할 수 있습니다.

```PowerShell
$nanoServerSession = New-PSSession -ComputerName $nanoServerIpAddress -Credential ~\Administrator
Enter-PSSession $nanoServerSession
```

연결에 성공하면 다음과 같은 형식이 포함된 프롬프트가 표시됩니다. `[192.168.1.10]: PS C:\Users\Administrator\Documents>`

## <a name="creating-a-file-share"></a>파일 공유 만들기

게시된 응용 프로그램을 복사할 수 있도록 Nano Server에서 파일 공유를 만듭니다. 원격 세션에서 다음 명령을 실행합니다.

```PowerShell
New-Item C:\PublishedApps\AspNetCoreSampleForNano -type directory
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=yes
net share AspNetCoreSampleForNano=c:\PublishedApps\AspNetCoreSampleForNano /GRANT:EVERYONE`,FULL
```

위 명령을 실행한 후 호스트 컴퓨터의 Windows 탐색기에서 `\\192.168.1.10\AspNetCoreSampleForNano`를 방문하여 이 공유에 액세스할 수 있어야 합니다.

## <a name="open-port-in-the-firewall"></a>방화벽에서 포트 열기

IIS가 포트 TCP/8000에서 TCP 트래픽을 수신할 수 있도록 원격 세션에서 다음 명령을 실행하여 방화벽에서 포트를 엽니다.

```PowerShell
New-NetFirewallRule -Name "AspNet5 IIS" -DisplayName "Allow HTTP on TCP/8000" -Protocol TCP -LocalPort 8000 -Action Allow -Enabled True
```

## <a name="installing-iis"></a>IIS 설치

PowerShell 갤러리에서 `NanoServerPackage` 공급자를 추가합니다. 공급자를 설치하고 가져온 후 Windows 패키지를 설치할 수 있습니다.

이전에 만든 PowerShell 세션에서 다음 명령을 실행합니다.

```PowerShell
Install-PackageProvider NanoServerPackage
Import-PackageProvider NanoServerPackage
Install-NanoServerPackage -Name Microsoft-NanoServer-Storage-Package
Install-NanoServerPackage -Name Microsoft-NanoServer-IIS-Package
```

IIS가 제대로 설정되어 있는지 빠르게 확인하려면 URL `http://192.168.1.10/`을 방문합니다. 시작 페이지가 표시되어야 합니다. IIS가 설치된 경우 포트 80을 수신하는 `Default Web Site`라는 웹 사이트가 기본적으로 만들어집니다.

## <a name="installing-the-aspnet-core-module-ancm"></a>ANCM(ASP.NET Core 모듈) 설치

ASP.NET Core 모듈은 ASP.NET Core HTTP 수신기의 프로세스 관리를 수행하는 IIS 7.5+ 모듈로서, 관리하는 프로세스에 대한 요청을 프록시하는 데 사용됩니다. 현재는 IIS에 대한 ASP.NET Core 모듈을 설치하는 과정이 수동입니다. Nano가 아닌 일반 컴퓨터에서 [.NET Core Windows Server 호스팅 번들](https://download.microsoft.com/download/B/1/D/B1D7D5BF-3920-47AA-94BD-7A6E48822F18/DotNetCore.2.0.0-WindowsHosting.exe)을 설치해야 합니다. 일반 컴퓨터에 번들을 설치한 후 이전에 만든 파일 공유에 다음 파일을 복사해야 합니다.

IIS를 사용하는 일반 서버(Nano가 아님)에서 다음 복사 명령을 실행합니다.

```PowerShell
Copy-Item -Path  C:\windows\system32\inetsrv\aspnetcore.dll -Destination `\\<nanoserver-ip-address>\AspNetCoreSampleForNano`
Copy-Item -Path  C:\windows\system32\inetsrv\config\schema\aspnetcore_schema.xml -Destination `\\<nanoserver-ip-address>\AspNetCoreSampleForNano`
```

Windows 10 컴퓨터에서 `C:\windows\system32\inetsrv`를 `C:\Program Files\IIS Express`로 바꿉니다.

Nano 쪽에서 이전에 만든 파일 공유에서 유효한 위치로 다음 파일을 복사해야 합니다. 따라서 다음 복사 명령을 실행합니다.

```PowerShell
Copy-Item -Path C:\PublishedApps\AspNetCoreSampleForNano\aspnetcore.dll -Destination C:\windows\system32\inetsrv\
Copy-Item -Path C:\PublishedApps\AspNetCoreSampleForNano\aspnetcore_schema.xml -Destination C:\windows\system32\inetsrv\config\schema\
```

원격 세션에서 다음 스크립트를 실행합니다.

```PowerShell
# Backup existing applicationHost.config
Copy-Item -Path C:\Windows\System32\inetsrv\config\applicationHost.config -Destination  C:\Windows\System32\inetsrv\config\applicationHost_BeforeInstallingANCM.config

Import-Module IISAdministration

# Initialize variables
$aspNetCoreHandlerFilePath="C:\windows\system32\inetsrv\aspnetcore.dll"
Reset-IISServerManager -confirm:$false
$sm = Get-IISServerManager

# Add AppSettings section 
$sm.GetApplicationHostConfiguration().RootSectionGroup.Sections.Add("appSettings")

# Set Allow for handlers section
$appHostconfig = $sm.GetApplicationHostConfiguration()
$section = $appHostconfig.GetSection("system.webServer/handlers")
$section.OverrideMode="Allow"

# Add aspNetCore section to system.webServer
$sectionaspNetCore = $appHostConfig.RootSectionGroup.SectionGroups["system.webServer"].Sections.Add("aspNetCore")
$sectionaspNetCore.OverrideModeDefault = "Allow"
$sm.CommitChanges()

# Configure globalModule
Reset-IISServerManager -confirm:$false
$globalModules = Get-IISConfigSection "system.webServer/globalModules" | Get-IISConfigCollection
New-IISConfigCollectionElement $globalModules -ConfigAttribute @{"name"="AspNetCoreModule";"image"=$aspNetCoreHandlerFilePath}

# Configure module
$modules = Get-IISConfigSection "system.webServer/modules" | Get-IISConfigCollection
New-IISConfigCollectionElement $modules -ConfigAttribute @{"name"="AspNetCoreModule"}
```

> [!NOTE]
> 위 단계를 수행한 후 공유에서 *aspnetcore.dll* 및 *aspnetcore_schema.xml* 파일을 삭제합니다.

## <a name="installing-net-core-framework"></a>.NET Core Framework 설치

앱이 [FDD(프레임워크 종속 배포)](/dotnet/core/deploying/#framework-dependent-deployments-fdd)로 게시되는 경우 서버에 .NET Core를 설치해야 합니다. 원격 PowerShell 세션에서 [dotnet-install.ps1 PowerShell 스크립트](https://dot.net/v1/dotnet-install.ps1)를 사용하여 Nano Server에 .NET Core를 설치합니다. `-Version` 스위치를 사용하여 CLI 버전을 전달합니다.

```console
dotnet-install.ps1 -Version 2.0.0
```

## <a name="publishing-the-application"></a>응용 프로그램 게시

기존 응용 프로그램의 게시된 출력을 파일 공유의 루트에 복사합니다.

*dotnet.exe*를 추출한 위치를 가리키려면 *web.config*를 변경해야 할 수 있습니다. 또는 PATH에 *dotnet.exe*를 추가할 수 있습니다.

*dotnet.exe*가 PATH에 **없을** 경우 *web.config*는 다음과 같이 표시될 수 있습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="C:\dotnet\dotnet.exe" arguments=".\AspNetCoreSampleForNano.dll" stdoutLogEnabled="false" stdoutLogFile=".\logs\aspnetcore-stdout" forwardWindowsAuthToken="true" />
  </system.webServer>
</configuration>
```

원격 세션에서 다음 명령을 실행하여 기본 웹 사이트와는 다른 포트에서 게시된 앱에 대한 새 사이트를 IIS에서 만듭니다. 웹에 액세스하려면 해당 포트를 열어야 합니다. 이 스크립트에서는 간단한 설명을 위해 `DefaultAppPool`을 사용합니다. 응용 프로그램 풀에서 실행할 경우의 추가 고려 사항은 [응용 프로그램 풀](xref:publishing/iis#application-pools)을 참조하세요.

```PowerShell
Import-module IISAdministration
New-IISSite -Name "AspNetCore" -PhysicalPath c:\PublishedApps\AspNetCoreSampleForNano -BindingInformation "*:8000:"
```

## <a name="known-issue-running-net-core-cli-on-nano-server-and-workaround"></a>Nano Server에서 .NET Core CLI를 실행할 경우 알려진 문제 및 해결 방법
```PowerShell
New-NetFirewallRule -Name "AspNetCore Port 81 IIS" -DisplayName "Allow HTTP on TCP/81" -Protocol TCP -LocalPort 81 -Action Allow -Enabled True
```

## <a name="running-the-application"></a>응용 프로그램 실행

게시된 웹앱은 브라우저를 통해 `http://192.168.1.10:8000`에서 액세스할 수 있습니다. [로그 만들기 및 리디렉션](xref:hosting/aspnet-core-module#log-creation-and-redirection)에 설명된 대로 로깅을 설정한 경우 *C:\PublishedApps\AspNetCoreSampleForNano\logs*에서 로그를 확인할 수 있습니다.

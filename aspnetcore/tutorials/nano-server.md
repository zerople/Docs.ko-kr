---
title: "ASP.NET Core Nano 서버에 | Microsoft 문서"
author: shirhatti
description: "기존 ASP.NET 핵심 응용 프로그램에 IIS를 실행 하는 Nano 서버 인스턴스를 배포 하는 방법에 알아봅니다."
keywords: "ASP.NET Core, nano 서버"
ms.author: riande
manager: wpickett
ms.date: 11/04/2016
ms.topic: article
ms.assetid: 50922cf1-ca58-4006-9236-99b7ff2dd0cf
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/nano-server
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 585a22c6597790c41774b553f632f42a3837dcb4
ms.lasthandoff: 03/23/2017

---
# <a name="aspnet-core-with-iis-on-nano-server"></a>Nano 서버에 IIS와 ASP.NET 코어

[Sourabh Shirhatti](https://twitter.com/sshirhatti)

이 자습서는 기존 ASP.NET 핵심 응용 프로그램을 사용 하 고 IIS를 실행 하는 Nano 서버 인스턴스에 배포 합니다.

## <a name="introduction"></a>소개

Nano 서버에이 Windows Server 2016, 작은 공간, 향상 된 보안 및 Server Core 또는 전체 서버 보다 더 나은 서비스를 제공 하는 설치 옵션입니다. 공식에 게 문의 [Nano 서버 설명서](https://technet.microsoft.com/library/mt126167.aspx) 자세한 내용 및 다운로드 링크를 180 일 평가판 버전입니다. 

세 가지 쉬운 방법으로 Nano 서버 사용해볼 수 있습니다. 때 MS 계정으로 로그인 합니다.

1. Windows Server 2016 ISO 파일을 다운로드 하 고 Nano 서버 이미지를 빌드할 수 있습니다.

2. Nano 서버 VHD를 다운로드 합니다.

3. Azure 갤러리의 Nano 서버 이미지를 사용 하 여 Azure에서 VM을 만듭니다. Azure 계정이 없으면 무료 30 일 평가판을 얻을 수 있습니다.

이 자습서에서는 Windows Server 2016에서 미리 작성 된 Nano 서버 VHD에서 두 번째 옵션을 사용 합니다.

이 자습서를 계속 하기 전에 필요 합니다는 [게시 출력](xref:hosting/directory-structure) 기존의 ASP.NET 핵심 응용 프로그램입니다. 확인 응용 프로그램 실행 되도록 빌드되는 **64 비트** 프로세스입니다.

## <a name="setting-up-the-nano-server-instance"></a>Nano 서버 인스턴스를 설정합니다.

[Hyper-v를 사용 하 여 새 가상 컴퓨터를 만들고](https://technet.microsoft.com/library/hh846766.aspx) 이전에 다운로드 한 VHD를 사용 하 여 개발 컴퓨터에 있습니다. 컴퓨터에 로그온 하기 전에 관리자 암호를 설정 해야 합니다. VM 콘솔에서 F11 키를 눌러 처음으로 로그인 하기 전에 암호를 설정 합니다. 새 VM의 IP 주소를 확인 하거나 DHCP 서버의 고정 IP Nano 서버 또는 VM을 프로 비전 할 때 제공 된 네트워킹 설정을 복구 콘솔의 내 확인 해야 합니다.

> [!NOTE]
> 로컬 IP V4 주소 192.168.1.10으로 실행 되는 새 VM을 가정해 보겠습니다.

이제 완벽 하 게 Nano 서버를 관리 하는 유일한 방법은 변수인 PowerShell 원격을 사용 하 여 관리할 수 있습니다.

## <a name="connecting-to-your-nano-server-instance-using-powershell-remoting"></a>PowerShell 원격을 사용 하 여 Nano 서버 인스턴스에 연결

원격 Nano 서버 인스턴스를 추가 하려면 관리자 권한 PowerShell 창을 열고 프로그램 `TrustedHosts` 목록입니다.

```PowerShell
$nanoServerIpAddress = "192.168.1.10"
Set-Item WSMan:\localhost\Client\TrustedHosts "$nanoServerIpAddress" -Concatenate -Force
```

> [!NOTE]
> 변수 교체 `$nanoServerIpAddress` 올바른 IP 주소를 사용 합니다.

Nano 서버 인스턴스를 추가 하 고 나면 사용자 `TrustedHosts`, PowerShell 원격을 사용 하 여 연결할 수 있습니다.

```PowerShell
$nanoServerSession = New-PSSession -ComputerName $nanoServerIpAddress -Credential ~\Administrator
Enter-PSSession $nanoServerSession
```

성공적으로 연결 처럼 보이는 형식으로 프롬프트에서 발생 합니다.`[192.168.1.10]: PS C:\Users\Administrator\Documents>`

## <a name="creating-a-file-share"></a>파일 공유 만들기

게시 된 응용 프로그램에 복사할 수 있도록 Nano 서버에 파일 공유를 만듭니다. 원격 세션에서 다음 명령을 실행 합니다.

```PowerShell
New-Item C:\PublishedApps\AspNetCoreSampleForNano -type directory
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=yes
net share AspNetCoreSampleForNano=c:\PublishedApps\AspNetCoreSampleForNano /GRANT:EVERYONE`,FULL
```

위의 명령을 실행 한 후 있어야 방문 하 여이 공유에 액세스 하려면 `\\192.168.1.10\AspNetCoreSampleForNano` 호스트 컴퓨터의 Windows 탐색기에서.

## <a name="open-port-in-the-firewall"></a>방화벽에서 열려 있는 포트

IIS 포트 TCP/8000에서 TCP 트래픽을 수신 대기할 수 있도록 방화벽에서 포트를 열려는 원격 세션에서 다음 명령을 실행 합니다.

```PowerShell
New-NetFirewallRule -Name "AspNet5 IIS" -DisplayName "Allow HTTP on TCP/8000" -Protocol TCP -LocalPort 8000 -Action Allow -Enabled True
```

## <a name="installing-iis"></a>IIS 설치

추가 된 `NanoServerPackage` PowerShell 갤러리에서 공급자입니다. 공급자가 설치 되 고 가져온, Windows 패키지를 설치할 수 있습니다.

이전에 생성 된 PowerShell 세션에서 다음 명령을 실행 합니다.

```PowerShell
Install-PackageProvider NanoServerPackage
Import-PackageProvider NanoServerPackage
Install-NanoServerPackage -Name Microsoft-NanoServer-Storage-Package
Install-NanoServerPackage -Name Microsoft-NanoServer-IIS-Package
```

신속 하 게 확인 IIS가 제대로 설치, 경우에 URL을 방문할 수 있습니다 `http://192.168.1.10/` 시작 페이지가 표시 됩니다. IIS가 설치 된 경우 웹 사이트 라는 `Default Web Site` 포트 80에서 수신은 기본적으로 생성 합니다.

## <a name="installing-the-aspnet-core-module-ancm"></a>ASP.NET 핵심 모듈 (ANCM) 설치

ASP.NET 핵심 모듈은 IIS 7.5 + 모듈 담당 하는 요청을 프록시 하 고 ASP.NET 핵심 HTTP 수신기 프로세스 관리 하는 관리 하는 프로세스입니다. IIS에 대 한 ASP.NET 핵심 모듈을 설치 하는 프로세스는 시간이 수동입니다. 설치 해야 합니다는 [.NET 핵심 Windows Server 호스팅 번들](https://go.microsoft.com/fwlink/?linkid=837808) (하지 Nano) 일반 컴퓨터입니다. 일반 컴퓨터에서 번들을 설치한 후 앞에서 만든 파일 공유에 다음 파일을 복사 해야 합니다.

IIS와 일반 (하지 Nano) 서버에서 다음 복사 명령을 실행 합니다.

```PowerShell
Copy-Item -Path  C:\windows\system32\inetsrv\aspnetcore.dll -Destination `\\<nanoserver-ip-address>\AspNetCoreSampleForNano`
Copy-Item -Path  C:\windows\system32\inetsrv\config\schema\aspnetcore_schema.xml -Destination `\\<nanoserver-ip-address>\AspNetCoreSampleForNano`
```

대체 `C:\windows\system32\inetsrv` 와 `C:\Program Files\IIS Express` Windows 10 컴퓨터에 있습니다.

Nano 쪽에서 올바른 위치에 이전에 생성 된 파일 공유에서 다음 파일을 복사 해야 합니다. 따라서 다음 복사 명령을 실행 합니다.

```PowerShell
Copy-Item -Path C:\PublishedApps\AspNetCoreSampleForNano\aspnetcore.dll -Destination C:\windows\system32\inetsrv\
Copy-Item -Path C:\PublishedApps\AspNetCoreSampleForNano\aspnetcore_schema.xml -Destination C:\windows\system32\inetsrv\config\schema\
```

원격 세션에서 다음 스크립트를 실행 합니다.

```PowerShell
# Backup existing applicationHost.config
Copy-Item -Path C:\Windows\System32\inetsrv\config\applicationHost.config -Destination  C:\Windows\System32\inetsrv\config\applicationHost_BeforeInstallingANCM.config

Import-Module IISAdministration

# Initialize variables
$aspNetCoreHandlerFilePath="C:\windows\system32\inetsrv\aspnetcore.dll"
Reset-IISServerManager -confirm:$false   $sm = Get-IISServerManager

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
> 파일을 삭제 *aspnetcore.dll* 및 *aspnetcore_schema.xml* 에서 위의 단계를 수행한 후 공유 합니다.

## <a name="installing-net-core-framework"></a>.NET Core 프레임 워크를 설치합니다.

프레임 워크에 따라 다릅니다 (휴대용) 응용 프로그램을 게시 하는 경우.NET Core는 대상 컴퓨터에 설치 되어야 합니다. Nano 서버에.NET Framework를 설치 하려면 원격 PowerShell 세션에서 다음 PowerShell 스크립트를 실행 합니다.

> [!NOTE]
> 프레임 워크 종속 배포 (FDD) 및 자체 포함 된 배포 (SCD) 간의 차이점을 이해 하려면 참조 [배포 옵션](https://docs.microsoft.com/dotnet/articles/core/deploying/)합니다.

[!code-powershell[주](nano-server/Download-Dotnet.ps1)]

## <a name="publishing-the-application"></a>응용 프로그램 게시

파일 공유의 루트에 기존 응용 프로그램의 게시 된 출력을 복사 합니다.

변경 하도록 할 수 여 *web.config* 압축을 푼를 가리키도록 *dotnet.exe*합니다. 추가할 수 있습니다 *dotnet.exe* 경로에 있습니다.

방법의 예는 *web.config* 표시 될 수 있습니다 *dotnet.exe* 는 **하지** 경로에 있습니다.

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

IIS에서 기본 웹 사이트 보다 다른 포트에서 게시 된 응용 프로그램을 위한 새 사이트를 만들기 위해 원격 세션에서 다음 명령을 실행 합니다. 웹 액세스에 해당 포트를 열 수 있어야 합니다. 다음을 사용 하 여이 스크립트는 `DefaultAppPool` 편의상 합니다. 응용 프로그램 풀에서 실행 하는에서 더 많은 고려 사항에 대 한 참조 [응용 프로그램 풀](xref:publishing/iis#application-pools)합니다.

```PowerShell
Import-module IISAdministration
New-IISSite -Name "AspNetCore" -PhysicalPath c:\PublishedApps\AspNetCoreSampleForNano -BindingInformation "*:8000:"
```

## <a name="known-issue-running-net-core-cli-on-nano-server-and-workaround"></a>알려진 Nano 서버와 해결 방법에.NET Core CLI를 실행 하는 문제가 있습니다.
```PowerShell
New-NetFirewallRule -Name "AspNetCore Port 81 IIS" -DisplayName "Allow HTTP on TCP/81" -Protocol TCP -LocalPort 81 -Action Allow -Enabled True
```

## <a name="running-the-application"></a>응용 프로그램 실행

게시 된 웹 앱이 브라우저에서 액세스할 수 있는 `http://192.168.1.10:8000`합니다. 에 설명 된 대로 로깅을 설정한 경우 [로그 만들기 및 리디렉션](xref:hosting/aspnet-core-module#log-creation-and-redirection)에서 로그를 볼 수 있습니다 *C:\PublishedApps\AspNetCoreSampleForNano\logs*합니다.


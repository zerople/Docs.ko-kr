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
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="aspnet-core-with-iis-on-nano-server"></a><span data-ttu-id="cb750-104">Nano Server의 ASP.NET Core 및 IIS</span><span class="sxs-lookup"><span data-stu-id="cb750-104">ASP.NET Core with IIS on Nano Server</span></span>

<span data-ttu-id="cb750-105">작성자: [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="cb750-105">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="cb750-106">이 자습서에서는 기존 ASP.NET Core 앱을 사용하고 IIS가 실행되는 Nano Server 인스턴스에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-106">In this tutorial, you'll take an existing ASP.NET Core app and deploy it to a Nano Server instance running IIS.</span></span>

## <a name="introduction"></a><span data-ttu-id="cb750-107">소개</span><span class="sxs-lookup"><span data-stu-id="cb750-107">Introduction</span></span>

<span data-ttu-id="cb750-108">Nano Server는 Server Core 또는 전체 서버보다 작은 공간, 강화된 보안 및 향상된 서비스를 지원하는 Windows Server 2016의 설치 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-108">Nano Server is an installation option in Windows Server 2016, offering a tiny footprint, better security, and better servicing than Server Core or full Server.</span></span> <span data-ttu-id="cb750-109">180일 평가 버전에 대한 자세한 내용은 다운로드 링크는 [Nano Server documentation](https://docs.microsoft.com/windows-server/get-started/getting-started-with-nano-server)(Nano Server 설명서)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb750-109">Please consult the official [Nano Server documentation](https://docs.microsoft.com/windows-server/get-started/getting-started-with-nano-server) for more details and download links for 180 Days evaluation versions.</span></span> 

<span data-ttu-id="cb750-110">Nano Server를 사용해 볼 수 있는 세 가지 간편한 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-110">There are three easy ways for you to try out Nano Server.</span></span> <span data-ttu-id="cb750-111">MS 계정으로 로그인할 경우:</span><span class="sxs-lookup"><span data-stu-id="cb750-111">When you sign in with your MS account:</span></span>

1. <span data-ttu-id="cb750-112">Windows Server 2016 ISO 파일을 다운로드하고 Nano Server 이미지를 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-112">You can download the Windows Server 2016 ISO file and build a Nano Server image.</span></span>

2. <span data-ttu-id="cb750-113">Nano Server VHD를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-113">Download the Nano Server VHD.</span></span>

3. <span data-ttu-id="cb750-114">Azure 갤러리에서 Nano Server 이미지를 사용하여 Azure에서 VM을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-114">Create a VM in Azure using the Nano Server image in the Azure Gallery.</span></span> <span data-ttu-id="cb750-115">Azure 계정이 없는 경우 30일 평가판을 다운로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-115">If you don’t have an Azure account, you can get a free 30-day trial.</span></span>

<span data-ttu-id="cb750-116">이 자습서에서는 두 번째 옵션인 Windows Server 2016에서 미리 빌드된 Nano Server VHD를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-116">In this tutorial, we will be using the 2nd option, the pre-built Nano Server VHD from Windows Server 2016.</span></span>

<span data-ttu-id="cb750-117">이 자습서를 진행하기 전에 기존 ASP.NET Core 응용 프로그램의 [게시된 출력](xref:hosting/directory-structure)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-117">Before proceeding with this tutorial, you will need the [published output](xref:hosting/directory-structure) of an existing ASP.NET Core application.</span></span> <span data-ttu-id="cb750-118">응용 프로그램이 **64비트** 프로세스에서 실행되도록 빌드되는지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-118">Ensure your application is built to run in a **64-bit** process.</span></span>

## <a name="setting-up-the-nano-server-instance"></a><span data-ttu-id="cb750-119">Nano Server 인스턴스 설정</span><span class="sxs-lookup"><span data-stu-id="cb750-119">Setting up the Nano Server instance</span></span>

<span data-ttu-id="cb750-120">이전에 다운로드한 VHD를 통해 개발 컴퓨터에서 [Hyper-V를 사용하여 새 가상 컴퓨터를 만듭니다](https://technet.microsoft.com/library/hh846766.aspx).</span><span class="sxs-lookup"><span data-stu-id="cb750-120">[Create a new Virtual Machine using Hyper-V](https://technet.microsoft.com/library/hh846766.aspx) on your development machine using the previously downloaded VHD.</span></span> <span data-ttu-id="cb750-121">컴퓨터에서 로그온하기 전에 관리자 암호를 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-121">The machine will require you to set an administrator password before logging on.</span></span> <span data-ttu-id="cb750-122">VM 콘솔에서 F11 키를 눌러 처음 로그인하기 전에 암호를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-122">At the VM console, press F11 to set the password before the first log in.</span></span> <span data-ttu-id="cb750-123">또한 VM을 프로비전하는 동안 제공된 DHCP 서버의 고정 IP를 확인하거나 Nano Server 복구 콘솔의 네트워킹 설정에서 새 VM IP 주소를 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-123">You also need to check your new VM's IP address either my checking your DHCP server's fixed IP supplied while provisioning your VM or in Nano Server recovery console's networking settings.</span></span>

> [!NOTE]
> <span data-ttu-id="cb750-124">새 VM이 로컬 V4 IP 주소 192.168.1.10을 사용하여 실행된다고 가정해 봅니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-124">Let's assume your new VM runs with the local V4 IP address 192.168.1.10.</span></span>

<span data-ttu-id="cb750-125">이제 PowerShell 원격을 사용하여 VM을 관리할 수 있습니다. Nano Server를 완벽하게 관리하는 유일한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-125">Now you're able to manage it using PowerShell remoting, which is the only way to fully administer your Nano Server.</span></span>

## <a name="connecting-to-your-nano-server-instance-using-powershell-remoting"></a><span data-ttu-id="cb750-126">PowerShell 원격을 사용하여 Nano Server 인스턴스에 연결</span><span class="sxs-lookup"><span data-stu-id="cb750-126">Connecting to your Nano Server instance using PowerShell Remoting</span></span>

<span data-ttu-id="cb750-127">관리자 권한으로 PowerShell 창을 열어 원격 Nano Server 인스턴스를 `TrustedHosts` 목록에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-127">Open an elevated PowerShell window to add your remote Nano Server instance to your `TrustedHosts` list.</span></span>

```PowerShell
$nanoServerIpAddress = "192.168.1.10"
Set-Item WSMan:\localhost\Client\TrustedHosts "$nanoServerIpAddress" -Concatenate -Force
```

> [!NOTE]
> <span data-ttu-id="cb750-128">변수 `$nanoServerIpAddress`를 올바른 IP 주소로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-128">Replace the variable `$nanoServerIpAddress` with the correct IP address.</span></span>

<span data-ttu-id="cb750-129">Nano Server 인스턴스를 `TrustedHosts`에 추가한 후 PowerShell 원격을 사용하여 인스턴스에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-129">Once you have added your Nano Server instance to your `TrustedHosts`, you can connect to it using PowerShell remoting.</span></span>

```PowerShell
$nanoServerSession = New-PSSession -ComputerName $nanoServerIpAddress -Credential ~\Administrator
Enter-PSSession $nanoServerSession
```

<span data-ttu-id="cb750-130">연결에 성공하면 다음과 같은 형식이 포함된 프롬프트가 표시됩니다. `[192.168.1.10]: PS C:\Users\Administrator\Documents>`</span><span class="sxs-lookup"><span data-stu-id="cb750-130">A successful connection results in a prompt with a format looking like: `[192.168.1.10]: PS C:\Users\Administrator\Documents>`</span></span>

## <a name="creating-a-file-share"></a><span data-ttu-id="cb750-131">파일 공유 만들기</span><span class="sxs-lookup"><span data-stu-id="cb750-131">Creating a file share</span></span>

<span data-ttu-id="cb750-132">게시된 응용 프로그램을 복사할 수 있도록 Nano Server에서 파일 공유를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-132">Create a file share on the Nano server so that the published application can be copied to it.</span></span> <span data-ttu-id="cb750-133">원격 세션에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-133">Run the following commands in the remote session:</span></span>

```PowerShell
New-Item C:\PublishedApps\AspNetCoreSampleForNano -type directory
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=yes
net share AspNetCoreSampleForNano=c:\PublishedApps\AspNetCoreSampleForNano /GRANT:EVERYONE`,FULL
```

<span data-ttu-id="cb750-134">위 명령을 실행한 후 호스트 컴퓨터의 Windows 탐색기에서 `\\192.168.1.10\AspNetCoreSampleForNano`를 방문하여 이 공유에 액세스할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-134">After running the above commands, you should be able to access this share by visiting `\\192.168.1.10\AspNetCoreSampleForNano` in the host machine's Windows Explorer.</span></span>

## <a name="open-port-in-the-firewall"></a><span data-ttu-id="cb750-135">방화벽에서 포트 열기</span><span class="sxs-lookup"><span data-stu-id="cb750-135">Open port in the firewall</span></span>

<span data-ttu-id="cb750-136">IIS가 포트 TCP/8000에서 TCP 트래픽을 수신할 수 있도록 원격 세션에서 다음 명령을 실행하여 방화벽에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-136">Run the following commands in the remote session to open up a port in the firewall to let IIS listen for TCP traffic on port TCP/8000.</span></span>

```PowerShell
New-NetFirewallRule -Name "AspNet5 IIS" -DisplayName "Allow HTTP on TCP/8000" -Protocol TCP -LocalPort 8000 -Action Allow -Enabled True
```

## <a name="installing-iis"></a><span data-ttu-id="cb750-137">IIS 설치</span><span class="sxs-lookup"><span data-stu-id="cb750-137">Installing IIS</span></span>

<span data-ttu-id="cb750-138">PowerShell 갤러리에서 `NanoServerPackage` 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-138">Add the `NanoServerPackage` provider from the PowerShell Gallery.</span></span> <span data-ttu-id="cb750-139">공급자를 설치하고 가져온 후 Windows 패키지를 설치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-139">Once the provider is installed and imported, you can install Windows packages.</span></span>

<span data-ttu-id="cb750-140">이전에 만든 PowerShell 세션에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-140">Run the following commands in the PowerShell session that was created earlier:</span></span>

```PowerShell
Install-PackageProvider NanoServerPackage
Import-PackageProvider NanoServerPackage
Install-NanoServerPackage -Name Microsoft-NanoServer-Storage-Package
Install-NanoServerPackage -Name Microsoft-NanoServer-IIS-Package
```

<span data-ttu-id="cb750-141">IIS가 제대로 설정되어 있는지 빠르게 확인하려면 URL `http://192.168.1.10/`을 방문합니다. 시작 페이지가 표시되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-141">To quickly verify if IIS is setup correctly, you can visit the URL `http://192.168.1.10/` and should see a welcome page.</span></span> <span data-ttu-id="cb750-142">IIS가 설치된 경우 포트 80을 수신하는 `Default Web Site`라는 웹 사이트가 기본적으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-142">When IIS is installed, a website called `Default Web Site` listening on port 80 is created by default.</span></span>

## <a name="installing-the-aspnet-core-module-ancm"></a><span data-ttu-id="cb750-143">ANCM(ASP.NET Core 모듈) 설치</span><span class="sxs-lookup"><span data-stu-id="cb750-143">Installing the ASP.NET Core Module (ANCM)</span></span>

<span data-ttu-id="cb750-144">ASP.NET Core 모듈은 ASP.NET Core HTTP 수신기의 프로세스 관리를 수행하는 IIS 7.5+ 모듈로서, 관리하는 프로세스에 대한 요청을 프록시하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-144">The ASP.NET Core Module is an IIS 7.5+ module which is responsible for process management of ASP.NET Core HTTP listeners and to proxy requests to processes that it manages.</span></span> <span data-ttu-id="cb750-145">현재는 IIS에 대한 ASP.NET Core 모듈을 설치하는 과정이 수동입니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-145">At the moment, the process to install the ASP.NET Core Module for IIS is manual.</span></span> <span data-ttu-id="cb750-146">Nano가 아닌 일반 컴퓨터에서 [.NET Core Windows Server 호스팅 번들](https://download.microsoft.com/download/B/1/D/B1D7D5BF-3920-47AA-94BD-7A6E48822F18/DotNetCore.2.0.0-WindowsHosting.exe)을 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-146">You will need to install the [.NET Core Windows Server Hosting bundle](https://download.microsoft.com/download/B/1/D/B1D7D5BF-3920-47AA-94BD-7A6E48822F18/DotNetCore.2.0.0-WindowsHosting.exe) on a regular (not Nano) machine.</span></span> <span data-ttu-id="cb750-147">일반 컴퓨터에 번들을 설치한 후 이전에 만든 파일 공유에 다음 파일을 복사해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-147">After installing the bundle on a regular machine, you will need to copy the following files to the file share that we created earlier.</span></span>

<span data-ttu-id="cb750-148">IIS를 사용하는 일반 서버(Nano가 아님)에서 다음 복사 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-148">On a regular (not Nano) server with IIS, run the following copy commands:</span></span>

```PowerShell
Copy-Item -Path  C:\windows\system32\inetsrv\aspnetcore.dll -Destination `\\<nanoserver-ip-address>\AspNetCoreSampleForNano`
Copy-Item -Path  C:\windows\system32\inetsrv\config\schema\aspnetcore_schema.xml -Destination `\\<nanoserver-ip-address>\AspNetCoreSampleForNano`
```

<span data-ttu-id="cb750-149">Windows 10 컴퓨터에서 `C:\windows\system32\inetsrv`를 `C:\Program Files\IIS Express`로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-149">Replace `C:\windows\system32\inetsrv` with `C:\Program Files\IIS Express` on a Windows 10 machine.</span></span>

<span data-ttu-id="cb750-150">Nano 쪽에서 이전에 만든 파일 공유에서 유효한 위치로 다음 파일을 복사해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-150">On the Nano side, you will need to copy the following files from the file share that we created earlier to the valid locations.</span></span> <span data-ttu-id="cb750-151">따라서 다음 복사 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-151">So, run the following copy commands:</span></span>

```PowerShell
Copy-Item -Path C:\PublishedApps\AspNetCoreSampleForNano\aspnetcore.dll -Destination C:\windows\system32\inetsrv\
Copy-Item -Path C:\PublishedApps\AspNetCoreSampleForNano\aspnetcore_schema.xml -Destination C:\windows\system32\inetsrv\config\schema\
```

<span data-ttu-id="cb750-152">원격 세션에서 다음 스크립트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-152">Run the following script in the remote session:</span></span>

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
> <span data-ttu-id="cb750-153">위 단계를 수행한 후 공유에서 *aspnetcore.dll* 및 *aspnetcore_schema.xml* 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-153">Delete the files *aspnetcore.dll* and *aspnetcore_schema.xml* from the share after the above step.</span></span>

## <a name="installing-net-core-framework"></a><span data-ttu-id="cb750-154">.NET Core Framework 설치</span><span class="sxs-lookup"><span data-stu-id="cb750-154">Installing .NET Core Framework</span></span>

<span data-ttu-id="cb750-155">앱이 [FDD(프레임워크 종속 배포)](/dotnet/core/deploying/#framework-dependent-deployments-fdd)로 게시되는 경우 서버에 .NET Core를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-155">If your app is published as a [framework-dependent deployment (FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd), .NET Core must be installed on the server.</span></span> <span data-ttu-id="cb750-156">원격 PowerShell 세션에서 [dotnet-install.ps1 PowerShell 스크립트](https://dot.net/v1/dotnet-install.ps1)를 사용하여 Nano Server에 .NET Core를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-156">Use the [dotnet-install.ps1 PowerShell script](https://dot.net/v1/dotnet-install.ps1) in a remote PowerShell session to install .NET Core on your Nano Server.</span></span> <span data-ttu-id="cb750-157">`-Version` 스위치를 사용하여 CLI 버전을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-157">Pass the CLI version with the `-Version` switch:</span></span>

```console
dotnet-install.ps1 -Version 2.0.0
```

## <a name="publishing-the-application"></a><span data-ttu-id="cb750-158">응용 프로그램 게시</span><span class="sxs-lookup"><span data-stu-id="cb750-158">Publishing the application</span></span>

<span data-ttu-id="cb750-159">기존 응용 프로그램의 게시된 출력을 파일 공유의 루트에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-159">Copy the published output of your existing application to the file share's root.</span></span>

<span data-ttu-id="cb750-160">*dotnet.exe*를 추출한 위치를 가리키려면 *web.config*를 변경해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-160">You may need to make changes to your *web.config* to point to where you extracted *dotnet.exe*.</span></span> <span data-ttu-id="cb750-161">또는 PATH에 *dotnet.exe*를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-161">Alternatively, you can add *dotnet.exe* to your PATH.</span></span>

<span data-ttu-id="cb750-162">*dotnet.exe*가 PATH에 **없을** 경우 *web.config*는 다음과 같이 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-162">Example of how a *web.config* might look if *dotnet.exe* is **not** on the PATH:</span></span>

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

<span data-ttu-id="cb750-163">원격 세션에서 다음 명령을 실행하여 기본 웹 사이트와는 다른 포트에서 게시된 앱에 대한 새 사이트를 IIS에서 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-163">Run the following commands in the remote session to create a new site in IIS for the published app on a different port than the default website.</span></span> <span data-ttu-id="cb750-164">웹에 액세스하려면 해당 포트를 열어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-164">You also need to open that port to access the web.</span></span> <span data-ttu-id="cb750-165">이 스크립트에서는 간단한 설명을 위해 `DefaultAppPool`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-165">This script uses the `DefaultAppPool` for simplicity.</span></span> <span data-ttu-id="cb750-166">응용 프로그램 풀에서 실행할 경우의 추가 고려 사항은 [응용 프로그램 풀](xref:publishing/iis#application-pools)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb750-166">For more considerations on running under an application pool, see [Application Pools](xref:publishing/iis#application-pools).</span></span>

```PowerShell
Import-module IISAdministration
New-IISSite -Name "AspNetCore" -PhysicalPath c:\PublishedApps\AspNetCoreSampleForNano -BindingInformation "*:8000:"
```

## <a name="known-issue-running-net-core-cli-on-nano-server-and-workaround"></a><span data-ttu-id="cb750-167">Nano Server에서 .NET Core CLI를 실행할 경우 알려진 문제 및 해결 방법</span><span class="sxs-lookup"><span data-stu-id="cb750-167">Known issue running .NET Core CLI on Nano Server and workaround</span></span>
```PowerShell
New-NetFirewallRule -Name "AspNetCore Port 81 IIS" -DisplayName "Allow HTTP on TCP/81" -Protocol TCP -LocalPort 81 -Action Allow -Enabled True
```

## <a name="running-the-application"></a><span data-ttu-id="cb750-168">응용 프로그램 실행</span><span class="sxs-lookup"><span data-stu-id="cb750-168">Running the application</span></span>

<span data-ttu-id="cb750-169">게시된 웹앱은 브라우저를 통해 `http://192.168.1.10:8000`에서 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-169">The published web app is accessible in a browser at `http://192.168.1.10:8000`.</span></span> <span data-ttu-id="cb750-170">[로그 만들기 및 리디렉션](xref:hosting/aspnet-core-module#log-creation-and-redirection)에 설명된 대로 로깅을 설정한 경우 *C:\PublishedApps\AspNetCoreSampleForNano\logs*에서 로그를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb750-170">If you've set up logging as described in [Log creation and redirection](xref:hosting/aspnet-core-module#log-creation-and-redirection), you can view your logs at *C:\PublishedApps\AspNetCoreSampleForNano\logs*.</span></span>

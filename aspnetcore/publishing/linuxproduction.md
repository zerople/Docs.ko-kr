---
title: "Nginx를 사용하여 Linux에서 ASP.NET Core 호스트"
description: "Ubuntu 16.04에서 Nginx를 역방향 프록시로 설정하여 Kestrel에서 실행되는 ASP.NET Core 웹 응용 프로그램에 HTTP 트래픽을 전달하는 방법을 설명합니다."
keywords: "ASP.NET Core, Linux, nginx, Ubuntu, 역방향 프록시"
author: rick-anderson
ms.author: riande
manager: wpickett
ms.date: 08/21/2017
ms.topic: article
ms.assetid: 1c33e576-33de-481a-8ad3-896b94fde0e3
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/linuxproduction
ms.openlocfilehash: 01768263fe82dc75a7da0e113b1850c8d788bfd3
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="set-up-a-hosting-environment-for-aspnet-core-on-linux-with-nginx-and-deploy-to-it"></a><span data-ttu-id="56313-104">Linux에서 Nginx를 사용하여 ASP.NET Core에 대한 호스팅 환경을 설정하고 해당 환경에 배포</span><span class="sxs-lookup"><span data-stu-id="56313-104">Set up a hosting environment for ASP.NET Core on Linux with Nginx, and deploy to it</span></span>

<span data-ttu-id="56313-105">작성자: [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="56313-105">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="56313-106">이 가이드에서는 Ubuntu 16.04 Server에서 프로덕션 준비 ASP.NET Core 환경을 설정하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-106">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 Server.</span></span>

<span data-ttu-id="56313-107">**참고:** Ubuntu 14.04의 경우 Kestrel 프로세스를 모니터링하기 위한 솔루션으로 supervisord를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-107">**Note:** For Ubuntu 14.04, supervisord is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="56313-108">systemd는 Ubuntu 14.04에서 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-108">systemd is not available on Ubuntu 14.04.</span></span> <span data-ttu-id="56313-109">[이 문서의 이전 버전을 참조하세요](https://github.com/aspnet/Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span><span class="sxs-lookup"><span data-stu-id="56313-109">[See previous version of this document](https://github.com/aspnet/Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)</span></span>

<span data-ttu-id="56313-110">이 가이드의 내용:</span><span class="sxs-lookup"><span data-stu-id="56313-110">This guide:</span></span>

* <span data-ttu-id="56313-111">기존 ASP.NET Core 응용 프로그램을 역방향 프록시 서버 뒤에 배치</span><span class="sxs-lookup"><span data-stu-id="56313-111">Places an existing ASP.NET Core application behind a reverse proxy server</span></span>
* <span data-ttu-id="56313-112">역방향 프록시 서버를 설정하여 Kestrel 웹 서버에 요청 전달</span><span class="sxs-lookup"><span data-stu-id="56313-112">Sets up the reverse proxy server to forward requests to the Kestrel web server</span></span>
* <span data-ttu-id="56313-113">웹 응용 프로그램이 시작 시 디먼으로 실행되는지 확인</span><span class="sxs-lookup"><span data-stu-id="56313-113">Ensures the web application runs on startup as a daemon</span></span>
* <span data-ttu-id="56313-114">웹 응용 프로그램 다시 시작을 지원하도록 프로세스 관리 도구 구성</span><span class="sxs-lookup"><span data-stu-id="56313-114">Configures a process management tool to help restart the web application</span></span>

## <a name="prerequisites"></a><span data-ttu-id="56313-115">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="56313-115">Prerequisites</span></span>

1. <span data-ttu-id="56313-116">sudo 권한을 가진 표준 사용자 계정으로 Ubuntu 16.04 Server에 액세스</span><span class="sxs-lookup"><span data-stu-id="56313-116">Access to an Ubuntu 16.04 Server with a standard user account with sudo privilege</span></span>
2. <span data-ttu-id="56313-117">기존 ASP.NET Core 응용 프로그램</span><span class="sxs-lookup"><span data-stu-id="56313-117">An existing ASP.NET Core application</span></span>

## <a name="copy-over-your-app"></a><span data-ttu-id="56313-118">앱을 통해 복사</span><span class="sxs-lookup"><span data-stu-id="56313-118">Copy over your app</span></span>

<span data-ttu-id="56313-119">개발 환경에서 `dotnet publish`를 실행하여 앱을 서버에서 실행될 수 있는 자체 포함된 디렉터리로 패키지합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-119">Run `dotnet publish` from the dev environment to package an app into a self-contained directory that can run on the server.</span></span>

<span data-ttu-id="56313-120">무엇이든 워크플로에 통합된 도구(SCP, FTP 등)를 사용하여 ASP.NET Core 앱을 서버에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-120">Copy the ASP.NET Core app to the server using whatever tool (SCP, FTP, etc.) integrates into your workflow.</span></span> <span data-ttu-id="56313-121">다음과 같이 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-121">Test the app, for example:</span></span>
 - <span data-ttu-id="56313-122">명령줄에서 `dotnet yourapp.dll`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-122">From the command line, run `dotnet yourapp.dll`</span></span>
 - <span data-ttu-id="56313-123">브라우저에서 `http://<serveraddress>:<port>`로 이동하여 앱이 Linux에서 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-123">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux.</span></span> 
 
<span data-ttu-id="56313-124">**참고:** [Yeoman](xref:client-side/yeoman)을 사용하여 새 프로젝트에 대한 새 ASP.NET Core 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="56313-124">**Note:** Use [Yeoman](xref:client-side/yeoman) to create a new ASP.NET Core app for a new project.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="56313-125">역방향 프록시 서버 구성</span><span class="sxs-lookup"><span data-stu-id="56313-125">Configure a reverse proxy server</span></span>

<span data-ttu-id="56313-126">역방향 프록시는 동적 웹 응용 프로그램을 지원하기 위한 일반적인 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="56313-126">A reverse proxy is a common setup for serving dynamic web applications.</span></span> <span data-ttu-id="56313-127">역방향 프록시는 HTTP 요청을 종료하고 이 요청을 ASP.NET Core 응용 프로그램에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-127">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core application.</span></span>

### <a name="why-use-a-reverse-proxy-server"></a><span data-ttu-id="56313-128">역방향 프록시 서버를 사용하는 이유는 무엇인가요?</span><span class="sxs-lookup"><span data-stu-id="56313-128">Why use a reverse proxy server?</span></span>

<span data-ttu-id="56313-129">Kestrel은 ASP.NET Core에서 동적 콘텐츠를 지원할 수 있는 유용한 기능이지만, 웹 지원 부분은 IIS, Apache 또는 Nginx 같은 서버만큼 기능이 다양하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-129">Kestrel is great for serving dynamic content from ASP.NET Core; however, the web serving parts aren’t as feature rich as servers like IIS, Apache, or Nginx.</span></span> <span data-ttu-id="56313-130">역방향 프록시 서버는 정적 콘텐츠 지원, 요청 캐시, 요청 압축 및 HTTP 서버에서 SSL 종료 같은 작업을 오프로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-130">A reverse proxy server can offload work like serving static content, caching requests, compressing requests, and SSL termination from the HTTP server.</span></span> <span data-ttu-id="56313-131">역방향 프록시 서버는 전용 컴퓨터에 있거나 HTTP 서버와 함께 배포될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-131">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="56313-132">이 가이드에서는 Nginx의 단일 인스턴스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="56313-132">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="56313-133">이 인스턴스는 HTTP 서버와 함께 동일한 서버에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="56313-133">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="56313-134">요구 사항에 따라 다른 설정을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-134">Based on your requirements, you may choose a different setup.</span></span>

<span data-ttu-id="56313-135">요청이 역방향 프록시를 통해 전달되므로 `Microsoft.AspNetCore.HttpOverrides` 패키지에서 `ForwardedHeaders` 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-135">Because requests are forwarded by reverse proxy, use the `ForwardedHeaders` middleware from the `Microsoft.AspNetCore.HttpOverrides` package.</span></span> <span data-ttu-id="56313-136">이 미들웨어는 `X-Forwarded-Proto` 헤더를 사용하여 `Request.Scheme`을 업데이트하므로 리디렉션 URI 및 기타 보안 정책이 제대로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-136">This middleware updates `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="56313-137">역방향 프록시 서버를 설정할 경우 인증 미들웨어는 `UseForwardedHeaders`를 먼저 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-137">When setting up a reverse proxy server, the authentication middleware needs `UseForwardedHeaders` to run first.</span></span> <span data-ttu-id="56313-138">이렇게 순서를 지정하면 인증 미들웨어가 영향받는 값을 사용하고 올바른 리디렉션 URI를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-138">This ordering ensures that the authentication middleware can consume the affected values and generate correct redirect URIs.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="56313-139">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="56313-139">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="56313-140">*Startup.cs*의 `Configure` 메서드에서 `UseForwardedHeaders` 메서드를 호출한 후 `UseAuthentication` 또는 비슷한 인증 체계 미들웨어를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-140">Invoke the `UseForwardedHeaders` method (in the `Configure` method of *Startup.cs*) before calling `UseAuthentication` or similar authentication scheme middleware:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="56313-141">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="56313-141">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="56313-142">*Startup.cs*의 `Configure` 메서드에서 `UseForwardedHeaders` 메서드를 호출한 후 `UseIdentity`와 `UseFacebookAuthentication` 또는 비슷한 인증 체계 미들웨어를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-142">Invoke the `UseForwardedHeaders` method (in the `Configure` method of *Startup.cs*) before calling `UseIdentity` and `UseFacebookAuthentication` or similar authentication scheme middleware:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseIdentity();
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

---

### <a name="install-nginx"></a><span data-ttu-id="56313-143">Nginx 설치</span><span class="sxs-lookup"><span data-stu-id="56313-143">Install Nginx</span></span>

```bash
sudo apt-get install nginx
```

> [!NOTE]
> <span data-ttu-id="56313-144">선택적 Nginx 모듈을 설치하려면 소스에서 Nginx를 빌드해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-144">If you plan to install optional Nginx modules, you may be required to build Nginx from source.</span></span>

<span data-ttu-id="56313-145">`apt-get`을 사용하여 Nginx를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-145">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="56313-146">설치 관리자는 시스템 시작 시 Nginx를 디먼으로 실행하는 System V init 스크립트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="56313-146">The installer creates a System V init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="56313-147">Nginx가 처음 설치되었으므로 다음을 실행하여 명시적으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-147">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="56313-148">브라우저에 Nginx에 대한 기본 방문 페이지가 표시되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-148">Verify a browser displays the default landing page for Nginx.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="56313-149">Nginx 구성</span><span class="sxs-lookup"><span data-stu-id="56313-149">Configure Nginx</span></span>

<span data-ttu-id="56313-150">Nginx를 역방향 프록시로 구성하여 요청을 ASP.NET Core 응용 프로그램에 전달하려면 `/etc/nginx/sites-available/default`를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-150">To configure Nginx as a reverse proxy to forward requests to our ASP.NET Core application, modify `/etc/nginx/sites-available/default`.</span></span> <span data-ttu-id="56313-151">텍스트 편집기에서 해당 항목을 열고 콘텐츠를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="56313-151">Open it in a text editor, and replace the contents with the following:</span></span>

```nginx
server {
    listen 80;
    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection keep-alive;
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

<span data-ttu-id="56313-152">이 Nginx 구성 파일은 들어오는 공용 트래픽을 포트 `80`에서 포트 `5000`으로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-152">This Nginx configuration file forwards incoming public traffic from port `80` to port `5000`.</span></span>

<span data-ttu-id="56313-153">Nginx 구성 변경을 완료한 후 `sudo nginx -t`를 실행하여 구성 파일의 구문을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-153">Once you have completed making changes to your Nginx configuration, you can run `sudo nginx -t` to verify the syntax of your configuration files.</span></span> <span data-ttu-id="56313-154">구성 파일 테스트에 성공하면 Nginx를 통해 `sudo nginx -s reload`를 실행하여 변경 내용을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-154">If the configuration file test is successful, you can ask Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

## <a name="monitoring-our-application"></a><span data-ttu-id="56313-155">응용 프로그램 모니터링</span><span class="sxs-lookup"><span data-stu-id="56313-155">Monitoring our application</span></span>

<span data-ttu-id="56313-156">이제 Nginx는 `http://yourhost:80`에 대해 만들어진 요청을 `http://127.0.0.1:5000`의 Kestrel에서 실행되는 ASP.NET Core 응용 프로그램에 전달하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="56313-156">Nginx is now setup to forward requests made to `http://yourhost:80` on to the ASP.NET Core application running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="56313-157">그러나 Nginx는 Kestrel 프로세스를 관리하도록 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-157">However, Nginx is not set up to manage the Kestrel process.</span></span> <span data-ttu-id="56313-158">*systemd*를 사용하고 서비스 파일을 만들어 기본 웹앱을 시작 및 모니터링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-158">You can use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="56313-159">*systemd*는 프로세스를 시작, 중지 및 관리하기 위한 다양하고 강력한 기능을 제공하는 init 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="56313-159">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="56313-160">서비스 파일 만들기</span><span class="sxs-lookup"><span data-stu-id="56313-160">Create the service file</span></span>

<span data-ttu-id="56313-161">서비스 정의 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="56313-161">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

<span data-ttu-id="56313-162">응용 프로그램에 대한 예제 서비스 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-162">The following is an example service file for our application:</span></span>

```ini
[Unit]
Description=Example .NET Web API Application running on Ubuntu

[Service]
WorkingDirectory=/var/aspnetcore/hellomvc
ExecStart=/usr/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
Restart=always
RestartSec=10  # Restart service after 10 seconds if dotnet service crashes
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="56313-163">**참고:** 사용자 *www-data*가 구성에서 사용되지 않을 경우 여기서 정의된 사용자를 먼저 만들고 파일에 대한 적절한 소유권을 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-163">**Note:** If the user *www-data* is not used by your configuration, the user defined here must be created first and given proper ownership for files.</span></span>

<span data-ttu-id="56313-164">파일을 저장하고 서비스를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-164">Save the file, and enable the service.</span></span>

```bash
systemctl enable kestrel-hellomvc.service
```

<span data-ttu-id="56313-165">서비스를 시작하고 실행 중인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-165">Start the service and verify that it is running.</span></span>

```
systemctl start kestrel-hellomvc.service
systemctl status kestrel-hellomvc.service

● kestrel-hellomvc.service - Example .NET Web API Application running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-hellomvc.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-hellomvc.service
            └─9021 /usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
```

<span data-ttu-id="56313-166">역방향 프록시를 구성하고 systemd를 통해 Kestrel을 관리하면 웹 응용 프로그램이 완전히 구성되고 로컬 컴퓨터(`http://localhost`)의 브라우저에서 웹 응용 프로그램에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-166">With the reverse proxy configured and Kestrel managed through systemd, the web application is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="56313-167">차단 중인 방화벽이 없다면 원격 컴퓨터에서 액세스할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-167">It is also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="56313-168">응답 헤더를 검사하는 `Server` 헤더는 Kestrel에서 지원하는 ASP.NET Core 응용 프로그램을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="56313-168">Inspecting the response headers, the `Server` header shows the ASP.NET Core application being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a><span data-ttu-id="56313-169">로그 보기</span><span class="sxs-lookup"><span data-stu-id="56313-169">Viewing logs</span></span>

<span data-ttu-id="56313-170">Kestrel을 사용하는 웹 응용 프로그램은 `systemd`를 사용하여 관리되므로 모든 이벤트 및 프로세스가 중앙형 저널에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="56313-170">Since the web application using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="56313-171">그러나 이 저널에는 `systemd`를 통해 관리하는 모든 서비스 및 프로세스에 대한 모든 항목이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="56313-171">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="56313-172">`kestrel-hellomvc.service` 관련 항목을 보려면 다음 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-172">To view the `kestrel-hellomvc.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-hellomvc.service
```

<span data-ttu-id="56313-173">추가 필터링을 위해 `--since today`, `--until 1 hour ago` 같은 시간 옵션이나 이러한 옵션의 조합을 사용하여 반환되는 항목 수를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-173">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-our-application"></a><span data-ttu-id="56313-174">응용 프로그램 보안</span><span class="sxs-lookup"><span data-stu-id="56313-174">Securing our application</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="56313-175">AppArmor 사용</span><span class="sxs-lookup"><span data-stu-id="56313-175">Enable AppArmor</span></span>

<span data-ttu-id="56313-176">LSM(Linux Security Modules)은 Linux 2.6 이후 Linux 커널에 포함된 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="56313-176">Linux Security Modules (LSM) is a framework that is part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="56313-177">LSM은 보안 모듈의 다양한 구현을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-177">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="56313-178">[AppArmor](https://wiki.ubuntu.com/AppArmor)는 프로그램을 제한된 리소스 집합으로 한정할 수 있는 필수 Access Control 시스템을 구현하는 LSM입니다.</span><span class="sxs-lookup"><span data-stu-id="56313-178">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="56313-179">AppArmor가 사용하도록 설정되고 제대로 구성되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-179">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configuring-our-firewall"></a><span data-ttu-id="56313-180">방화벽 구성</span><span class="sxs-lookup"><span data-stu-id="56313-180">Configuring our firewall</span></span>

<span data-ttu-id="56313-181">사용되지 않는 모든 외부 포트를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-181">Close off all external ports that are not in use.</span></span> <span data-ttu-id="56313-182">복잡하지 않은 방화벽(ufw)은 방화벽을 구성하기 위한 명령줄 인터페이스를 제공하여 `iptables`에 대한 프런트 엔드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-182">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a command line interface for configuring the firewall.</span></span> <span data-ttu-id="56313-183">`ufw`가 필요한 모든 포트에서 트래픽을 허용하도록 구성되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-183">Verify that `ufw` is configured to allow traffic on any ports you need.</span></span>

```bash
sudo apt-get install ufw
sudo ufw enable

sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

### <a name="securing-nginx"></a><span data-ttu-id="56313-184">Nginx 보안</span><span class="sxs-lookup"><span data-stu-id="56313-184">Securing Nginx</span></span>

<span data-ttu-id="56313-185">Nginx의 기본 배포 시에는 SSL이 사용하도록 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="56313-185">The default distribution of Nginx doesn't enable SSL.</span></span> <span data-ttu-id="56313-186">추가 보안 기능을 사용하도록 설정하려면 소스에서 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-186">To enable additional security features, build from source.</span></span>

#### <a name="download-the-source-and-install-the-build-dependencies"></a><span data-ttu-id="56313-187">소스 다운로드 및 빌드 종속성 설치</span><span class="sxs-lookup"><span data-stu-id="56313-187">Download the source and install the build dependencies</span></span>

```bash
# Install the build dependencies
sudo apt-get update
sudo apt-get install build-essential zlib1g-dev libpcre3-dev libssl-dev libxslt1-dev libxml2-dev libgd2-xpm-dev libgeoip-dev libgoogle-perftools-dev libperl-dev

# Download nginx 1.10.0 or latest
wget http://www.nginx.org/download/nginx-1.10.0.tar.gz
tar zxf nginx-1.10.0.tar.gz
```

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="56313-188">Nginx 응답 이름 변경</span><span class="sxs-lookup"><span data-stu-id="56313-188">Change the Nginx response name</span></span>

<span data-ttu-id="56313-189">*src/http/ngx_http_header_filter_module.c*를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-189">Edit *src/http/ngx_http_header_filter_module.c*:</span></span>

```c
static char ngx_http_server_string[] = "Server: Your Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Your Web Server" CRLF;
```

#### <a name="configure-the-options-and-build"></a><span data-ttu-id="56313-190">옵션 구성 및 빌드</span><span class="sxs-lookup"><span data-stu-id="56313-190">Configure the options and build</span></span>

<span data-ttu-id="56313-191">정규식의 경우 PCRE 라이브러리가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-191">The PCRE library is required for regular expressions.</span></span> <span data-ttu-id="56313-192">정규식은 ngx_http_rewrite_module에 대한 위치 지시문에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="56313-192">Regular expressions are used in the location directive for the ngx_http_rewrite_module.</span></span> <span data-ttu-id="56313-193">http_ssl_module은 HTTPS 프로토콜 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-193">The http_ssl_module adds HTTPS protocol support.</span></span>

<span data-ttu-id="56313-194">*ModSecurity* 같은 웹 응용 프로그램 방화벽을 사용하여 응용 프로그램을 강화해 보세요.</span><span class="sxs-lookup"><span data-stu-id="56313-194">Consider using a web application firewall like *ModSecurity* to harden your application.</span></span>

```bash
./configure
--with-pcre=../pcre-8.38
--with-zlib=../zlib-1.2.8
--with-http_ssl_module
--with-stream
--with-mail=dynamic
```

#### <a name="configure-ssl"></a><span data-ttu-id="56313-195">SSL 구성</span><span class="sxs-lookup"><span data-stu-id="56313-195">Configure SSL</span></span>

* <span data-ttu-id="56313-196">신뢰할 수 있는 CA(인증 기관)에서 발급된 유효한 인증서를 지정하여 포트 `443`에서 HTTPS 트래픽을 수신하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-196">Configure your server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="56313-197">다음 */etc/nginx/nginx.conf* 파일에 설명된 일부 사례를 채택하여 보안을 강화합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-197">Harden your security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="56313-198">예를 들어 더 강력한 암호화를 선택하고 HTTP를 사용한 모든 트래픽을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-198">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="56313-199">HSTS(`HTTP Strict-Transport-Security`) 헤더를 추가하면 클라이언트에서 만든 모든 후속 요청에 HTTPS만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="56313-199">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS only.</span></span>

* <span data-ttu-id="56313-200">Strict-Transport-Security 헤더를 추가하지 않거나, 나중에 SSL을 사용하지 않도록 설정하려면 적절한 `max-age`를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-200">Do not add the Strict-Transport-Security header or chose an appropriate `max-age` if you plan to disable SSL in the future.</span></span>

<span data-ttu-id="56313-201">*/etc/nginx/proxy.conf* 구성 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-201">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[Main](linuxproduction/proxy.conf)]

<span data-ttu-id="56313-202">*/etc/nginx/nginx.conf* 구성 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-202">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="56313-203">예제에서는 `http` 및 `server` 섹션이 하나의 구성 파일에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="56313-203">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[Main](../publishing/linuxproduction/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="56313-204">클릭재킹(clickjacking)으로부터 Nginx 보호</span><span class="sxs-lookup"><span data-stu-id="56313-204">Secure Nginx from clickjacking</span></span>
<span data-ttu-id="56313-205">클릭재킹은 감염된 사용자의 클릭을 수집하는 악의적인 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="56313-205">Clickjacking is a malicious technique to collect an infected user's clicks.</span></span> <span data-ttu-id="56313-206">클릭재킹은 희생자(방문자)를 속여서 감염된 사이트를 클릭하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-206">Clickjacking tricks the victim (visitor) into clicking on an infected site.</span></span> <span data-ttu-id="56313-207">X-FRAME-OPTIONS를 사용하여 사이트를 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-207">Use X-FRAME-OPTIONS to secure your site.</span></span>

<span data-ttu-id="56313-208">*nginx.conf* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-208">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="56313-209">줄 `add_header X-Frame-Options "SAMEORIGIN";`을 추가하고 파일을 저장한 다음 Nginx를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-209">Add the line `add_header X-Frame-Options "SAMEORIGIN";` and save the file, then restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="56313-210">MIME 형식 검색</span><span class="sxs-lookup"><span data-stu-id="56313-210">MIME-type sniffing</span></span>

<span data-ttu-id="56313-211">이 헤더는 응답 콘텐츠 형식을 재정의하지 않도록 브라우저에 지시하므로 대부분의 브라우저에서 선언된 콘텐츠 형식이 아닌 응답에 대한 MIME 검색을 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-211">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="56313-212">`nosniff` 옵션을 사용하면 서버에 콘텐츠가 “text/html”이라고 표시될 경우 브라우저는 콘텐츠를 “text/html”으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-212">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="56313-213">*nginx.conf* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-213">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="56313-214">줄 `add_header X-Content-Type-Options "nosniff";`를 추가하고 파일을 저장한 다음 Nginx를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="56313-214">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>

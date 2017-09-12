---
title: "ASP.NET Core에 Windows 인증을 구성 합니다."
author: ardalis
description: "ASP.NET Core에 Windows 인증을 구성 하는 방법"
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 7/5/2017
ms.topic: article
ms.assetid: cf119f21-1a2b-49a2-b052-548ccb66ee83
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/windowsauth
ms.openlocfilehash: aa401f956d74680efd3964203af3e8866b129887
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="954fd-104">ASP.NET Core에 Windows 인증을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-104">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="954fd-105">으로 [Steve Smith](https://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="954fd-105">By [Steve Smith](https://ardalis.com)</span></span>

<span data-ttu-id="954fd-106">IIS 또는 WebListener로 호스트 되는 ASP.NET Core 응용 프로그램에 대 한 Windows 인증을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-106">Windows authentication can be configured for ASP.NET Core apps hosted with IIS or WebListener.</span></span>

## <a name="what-is-windows-authentication"></a><span data-ttu-id="954fd-107">Windows 인증 이란</span><span class="sxs-lookup"><span data-stu-id="954fd-107">What is Windows authentication</span></span>

<span data-ttu-id="954fd-108">ASP.NET Core 응용 프로그램의 사용자를 인증 하는 운영 체제는 Windows 인증 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-108">Windows authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="954fd-109">서버에 다른 Windows 계정 또는 Active Directory 도메인 id를 사용 하 여 사용자를 식별 하는 회사 네트워크에서 실행 될 때 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-109">You can use Windows authentication when your server runs on a corporate network using Active Directory domain identities or other Windows accounts to identify users.</span></span> <span data-ttu-id="954fd-110">Windows 인증은 사용자, 클라이언트 응용 프로그램 및 웹 서버는 동일한 Windows 도메인에 속해 있는 인트라넷 환경에 적합 한 인증 가장 안전한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-110">Windows authentication is a secure form of authentication best suited to intranet environments where users, client applications, and web servers belong to the same Windows domain.</span></span>

<span data-ttu-id="954fd-111">[Windows 인증에 대 한 자세한 정보 및 IIS에 대 한 설치](https://docs.microsoft.com/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-111">[Learn more about Windows Authentication and installing it for IIS](https://docs.microsoft.com/iis/configuration/system.webServer/security/authentication/windowsAuthentication/).</span></span>

## <a name="enabling-windows-authentication-in-an-aspnet-core-application"></a><span data-ttu-id="954fd-112">ASP.NET Core 응용 프로그램에서 Windows 인증을 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="954fd-112">Enabling Windows authentication in an ASP.NET Core application</span></span>

<span data-ttu-id="954fd-113">Visual Studio 웹 응용 프로그램 템플릿은 Windows 인증을 지원 하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-113">The Visual Studio Web Application template can be configured to support Windows authentication.</span></span>

### <a name="using-the-windows-authentication-app-template"></a><span data-ttu-id="954fd-114">Windows 인증 앱 템플릿을 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="954fd-114">Using the Windows authentication app template</span></span>

<span data-ttu-id="954fd-115">Visual Studio에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-115">In Visual Studio:</span></span>
* <span data-ttu-id="954fd-116">새 ASP.NET Core 웹 응용 프로그램을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-116">Create a new ASP.NET Core Web Application.</span></span> 
* <span data-ttu-id="954fd-117">템플릿 목록에서 웹 응용 프로그램을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-117">Select Web Application from the list of templates.</span></span>
* <span data-ttu-id="954fd-118">인증 변경 단추를 선택 하 고 선택 **Windows 인증**합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-118">Select the Change Authentication button and select **Windows Authentication**.</span></span> 

<span data-ttu-id="954fd-119">응용 프로그램을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-119">Run the app.</span></span> <span data-ttu-id="954fd-120">사용자 이름 상단에 표시 됩니다. 응용 프로그램의 오른쪽입니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-120">The username appears in the top right of the app.</span></span>

![Windows 인증에 대 한 브라우저 스크린 샷](windowsauth/_static/browser-screenshot.png)

<span data-ttu-id="954fd-122">IIS Express를 사용 하 여 개발 작업에서는 서식 파일에 Windows 인증을 사용 하는 데 필요한 모든 구성을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-122">For development work using IIS Express, the template provides all the configuration necessary to use Windows authentication.</span></span> <span data-ttu-id="954fd-123">다음 섹션에는 Windows 인증에 대해 수동으로 ASP.NET Core 응용 프로그램을 구성 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-123">The next section shows how to configure an ASP.NET Core app manually for Windows authentication.</span></span>

### <a name="visual-studio-settings-for-windows-and-anonymous-authentication"></a><span data-ttu-id="954fd-124">Windows 및 익명 인증에 대 한 visual Studio 설정</span><span class="sxs-lookup"><span data-stu-id="954fd-124">Visual Studio settings for Windows and anonymous authentication</span></span>

<span data-ttu-id="954fd-125">Visual Studio 속성 페이지, 디버그 탭 Windows 인증 및 익명 인증에 대 한 확인란을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-125">The Visual Studio properties page, debug tab provides check boxes for Windows authentication and anonymous authentication.</span></span>

![Windows 인증에 대 한 브라우저 스크린 샷](windowsauth/_static/vs-auth-property-menu.png)

<span data-ttu-id="954fd-127">이러한 속성을 구성할 수도 있습니다는 `launchSettings.json` 파일:</span><span class="sxs-lookup"><span data-stu-id="954fd-127">You can also configure these properties in the `launchSettings.json` file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": true,
    "anonymousAuthentication": false,
    "iisExpress": {
      "applicationUrl": "http://localhost:52171/",
      "sslPort": 0
    }
  } // additional options trimmed
}
```

## <a name="enabling-windows-authentication-with-iis"></a><span data-ttu-id="954fd-128">Iis Windows 인증을 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="954fd-128">Enabling Windows Authentication with IIS</span></span>

<span data-ttu-id="954fd-129">IIS에서 사용 하는 [ASP.NET Core 모듈](xref:fundamentals/servers/aspnet-core-module) ASP.NET Core 응용 프로그램을 호스트할 (ANCM).</span><span class="sxs-lookup"><span data-stu-id="954fd-129">IIS uses the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) (ANCM) to host ASP.NET Core apps.</span></span> <span data-ttu-id="954fd-130">ANCM 흐름 Windows 인증을 IIS에 기본적으로입니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-130">The ANCM flows Windows authentication to IIS by default.</span></span> <span data-ttu-id="954fd-131">Windows 인증의 구성이 IIS에서 응용 프로그램 프로젝트 내에서 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-131">Configuration of Windows authentication is done within IIS, not the application project.</span></span> <span data-ttu-id="954fd-132">다음 섹션에서는 Windows 인증을 사용 하도록 ASP.NET Core 응용 프로그램을 구성 하려면 IIS 관리자를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-132">The following sections show how to use IIS Manager to configure an ASP.NET Core app to use Windows authentication:</span></span>

### <a name="create-a-new-iis-site"></a><span data-ttu-id="954fd-133">새 IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="954fd-133">Create a new IIS site</span></span>

<span data-ttu-id="954fd-134">이름 및 폴더를 지정 하 고 새 응용 프로그램 풀을 만들 수 있도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-134">Specify a name and folder and allow it to create a new application pool.</span></span>

### <a name="customize-authentication"></a><span data-ttu-id="954fd-135">인증을 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="954fd-135">Customize Authentication</span></span>

<span data-ttu-id="954fd-136">사이트에 대 한 인증 메뉴를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-136">Open the Authentication menu for the site.</span></span>

![IIS 인증 메뉴](windowsauth/_static/iis-authentication-menu.png)

<span data-ttu-id="954fd-138">익명 인증을 사용 하지 않도록 설정 하 고 Windows 인증을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-138">Disable Anonymous Authentication and enable Windows Authentication.</span></span>

![IIS 인증 설정](windowsauth/_static/iis-auth-settings.png)

### <a name="publish-your-project-to-the-iis-site-folder"></a><span data-ttu-id="954fd-140">IIS 사이트 폴더에 프로젝트를 게시 합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-140">Publish your project to the IIS site folder</span></span>

<span data-ttu-id="954fd-141">Visual Studio 또는.NET Core CLI를 사용 하 여 *게시* 대상 폴더에 응용 프로그램입니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-141">Using Visual Studio or the .NET Core CLI, *publish* your app to the destination folder.</span></span>

![Visual Studio 게시 대화 상자](windowsauth/_static/vs-publish-app.png)

<span data-ttu-id="954fd-143">에 대 한 자세한 내용은 [를 IIS에 게시](xref:publishing/iis)합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-143">Learn more about [publishing to IIS](xref:publishing/iis).</span></span>

<span data-ttu-id="954fd-144">Windows 인증 작동 확인 응용 프로그램을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-144">Launch the app to verify Windows authentication is working.</span></span>

## <a name="enabling-windows-authentication-with-weblistener"></a><span data-ttu-id="954fd-145">WebListener와 함께 Windows 인증을 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="954fd-145">Enabling Windows authentication with WebListener</span></span>

<span data-ttu-id="954fd-146">Kestrel Windows 인증을 지원 하지 않지만 사용할 수 있습니다 [WebListener](xref:fundamentals/servers/weblistener) Windows에서 자체 호스팅된 시나리오를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-146">Although Kestrel doesn't support Windows authentication, you can use [WebListener](xref:fundamentals/servers/weblistener) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="954fd-147">다음 예제에서는 Windows 인증과 함께 WebListener를 사용 하도록 응용 프로그램의 웹 호스트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-147">The following example configures the app's web host to use WebListener with Windows authentication:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = new WebHostBuilder()
            .UseWebListener(options =>
            {
                options.ListenerSettings.Authentication.Schemes = 
                    AuthenticationSchemes.Negotiate | AuthenticationSchemes.NTLM;
                options.ListenerSettings.Authentication.AllowAnonymous = false;
            })
            .UseContentRoot(Directory.GetCurrentDirectory())
            .UseStartup<Startup>()
            .Build();

        host.Run();
    }
}
```

## <a name="working-with-windows-authentication"></a><span data-ttu-id="954fd-148">Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="954fd-148">Working with Windows authentication</span></span>

<span data-ttu-id="954fd-149">앱에서 Windows 인증 및 익명 액세스를 사용 하는 경우 사용할 수 있습니다는 ``[Authorize]`` 및 ``[AllowAnonymous]`` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-149">If your app uses Windows authentication and anonymous access, you can use the ``[Authorize]`` and ``[AllowAnonymous]`` attributes.</span></span> <span data-ttu-id="954fd-150">익명 사용된 안 함 필요 하지 않아도 앱 ``[Authorize]``앱 필수 인증으로 처리 됩니다., 익명 요청이 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-150">Apps that do not have anonymous enabled do not require ``[Authorize]``; the  app is treated as requiring authentication, anonymous requests are rejected.</span></span> <span data-ttu-id="954fd-151">IIS 사이트를 구성 하는 경우 로컬 **하지** 익명 액세스를 허용 하는 ``[AllowAnonymous]`` 특성은 **하지** 익명 요청을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-151">Note, if the IIS site is configured **not** to allow anonymous access, the ``[AllowAnonymous]`` attribute does **not** allow anonymous requests.</span></span> <span data-ttu-id="954fd-152">``[AllowAnonymous]`` 특성 재정의 ``[Authorize]`` 특성 익명 액세스를 허용 하는 앱 내에서 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-152">The ``[AllowAnonymous]`` attribute overrides ``[Authorize]`` attribute usage within apps that allow anonymous access.</span></span>

### <a name="impersonation"></a><span data-ttu-id="954fd-153">가장</span><span class="sxs-lookup"><span data-stu-id="954fd-153">Impersonation</span></span>

<span data-ttu-id="954fd-154">ASP.NET Core 가장을 구현 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-154">ASP.NET Core does not implement impersonation.</span></span> <span data-ttu-id="954fd-155">앱에 대 한 모든 요청을 응용 프로그램 풀 또는 프로세스 id를 사용 하 여 응용 프로그램 id로 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-155">Apps run with the application identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="954fd-156">사용 하 여 명시적으로 사용자를 대신 하 여 작업을 수행 해야 할 경우 ``WindowsIdentity.RunImpersonated``합니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-156">If you need to explicitly perform an action on behalf of a user, use ``WindowsIdentity.RunImpersonated``.</span></span> <span data-ttu-id="954fd-157">이 컨텍스트에서 단일 작업을 실행 하 고 컨텍스트를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-157">Run a single action in this context and then close the context.</span></span> <span data-ttu-id="954fd-158">``RunImpersonated`` 비동기 지원 하지 않으며 복잡 한 시나리오에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-158">Note that ``RunImpersonated`` does not support async and should not be used for complex scenarios.</span></span> <span data-ttu-id="954fd-159">예를 들어 전체 요청 또는 미들웨어 체인 래핑은 지원 되지 않거나 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="954fd-159">For example, wrapping entire requests or middleware chains is not supported or recommended.</span></span>

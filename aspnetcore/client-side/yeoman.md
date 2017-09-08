---
title: "Yeoman ASP.NET 코어에서 사용한 프로젝트 빌드"
author: spboyer
description: "이 문서에서 ASP.NET Core는 Yeoman를 사용 하 여 웹 응용 프로그램을 구축 안내 macOS에서 생성기입니다."
keywords: "ASP.NET Core, Yeoman, 플랫폼 간 yo aspnet"
ms.author: spboyer
manager: wpickett
ms.date: 07/05/2017
ms.topic: article
ms.assetid: fda0c2a8-1743-4505-be1a-7f8ceeef8647
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/yeoman
ms.openlocfilehash: 3a7cd83becc570d2f73014b356977fedb16f29bb
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="introduction-to-building-projects-with-yeoman-in-aspnet-core"></a><span data-ttu-id="d22d2-104">프로젝트에서 ASP.NET Core Yeoman와 빌드 소개</span><span class="sxs-lookup"><span data-stu-id="d22d2-104">Introduction to building projects with Yeoman in ASP.NET Core</span></span>

<span data-ttu-id="d22d2-105">[Yeoman](http://yeoman.io/) 는 많은 종류의 응용 프로그램을 만들기 위한 프로젝트 스 캐 폴딩 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-105">[Yeoman](http://yeoman.io/) is a project scaffolding system for creating many kinds of applications.</span></span> <span data-ttu-id="d22d2-106">Yeoman ASP.NET Core에 대 한 생성기에 다양 한 새 웹, MVC 또는 콘솔 응용 프로그램을 시작 하기 위한 프로젝트 템플릿이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-106">The Yeoman generator for ASP.NET Core contains a variety of project templates for starting a new web, MVC, or console application.</span></span>

## <a name="install-nodejs-npm-and-yeoman"></a><span data-ttu-id="d22d2-107">Node.js, npm 및 Yeoman 설치</span><span class="sxs-lookup"><span data-stu-id="d22d2-107">Install Node.js, npm, and Yeoman</span></span>

### <a name="prerequisites"></a><span data-ttu-id="d22d2-108">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="d22d2-108">Prerequisites</span></span>

<span data-ttu-id="d22d2-109">Node.js 및 npm Yeoman 필요 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-109">Node.js and npm are required for Yeoman.</span></span> <span data-ttu-id="d22d2-110">다운로드 [Node.js](https://nodejs.org/en/)합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-110">Download from [Node.js](https://nodejs.org/en/).</span></span> <span data-ttu-id="d22d2-111">설치 관리자 포함 [Node.js](https://nodejs.org/en/) 및 [npm](https://www.npmjs.com/)합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-111">The installer includes [Node.js](https://nodejs.org/en/) and [npm](https://www.npmjs.com/).</span></span> <span data-ttu-id="d22d2-112">Bower 역시 부트스트랩와 같은 UI 프레임 워크를 설치 하는 데 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-112">Bower is also required for installing UI frameworks like Bootstrap.</span></span>

<span data-ttu-id="d22d2-113">Yeoman 및 Bower를 설치 하려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-113">To install Yeoman and Bower, run the following command:</span></span>

```console
npm install -g yo bower
```

>[!Note]
><span data-ttu-id="d22d2-114">오류가 발생 하는 경우 `npm ERR! Please try running this command again as root/Administrator.` macOS 등에서 사용 하 여 다음 명령을 실행 [sudo](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/sudo.8.html):`sudo npm install -g yo bower`</span><span class="sxs-lookup"><span data-stu-id="d22d2-114">If you get the error `npm ERR! Please try running this command again as root/Administrator.` on macOS, run the following command using [sudo](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/sudo.8.html): `sudo npm install -g yo bower`</span></span>

<span data-ttu-id="d22d2-115">명령 프롬프트에서 ASP.NET 생성기를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-115">From a command prompt, install the ASP.NET generator:</span></span>

```console
npm install -g generator-aspnet
```

> [!NOTE]
> <span data-ttu-id="d22d2-116">명령을 실행 하는 사용 권한 오류가 발생할 경우 `sudo` 위에서 설명한 것 처럼 합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-116">If you get a permission error, run the command under `sudo` as described above.</span></span>

<span data-ttu-id="d22d2-117">`–g` 플래그 설치 생성기 전체적으로 하므로 모든 경로에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-117">The `–g` flag installs the generator globally, so that it can be used from any path.</span></span>

## <a name="create-an-aspnet-app"></a><span data-ttu-id="d22d2-118">ASP.NET 응용 프로그램 만들기</span><span class="sxs-lookup"><span data-stu-id="d22d2-118">Create an ASP.NET app</span></span>

<span data-ttu-id="d22d2-119">ASP.NET Yeoman 기반 생성기를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-119">Run the Yeoman-based ASP.NET generator:</span></span>

```console
yo aspnet
```

<span data-ttu-id="d22d2-120">생성자에 메뉴가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-120">The generator displays a menu.</span></span> <span data-ttu-id="d22d2-121">아래쪽 화살표는 **멤버 자격 및 권한 부여] [없이 웹 응용 프로그램 기본** 프로젝트 및 탭 **Enter**:</span><span class="sxs-lookup"><span data-stu-id="d22d2-121">Arrow down to the **Web Application Basic [without Membership and Authorization]** project and tap **Enter**:</span></span>

![명령 창: 원하는 어떤 유형의 응용 프로그램?](yeoman/_static/yeoman-yo-aspnet.png)

<span data-ttu-id="d22d2-124">UI 프레임 워크도 부트스트랩을 선택 하 고 탭 **Enter**합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-124">Select Bootstrap as the UI Framework and tap **Enter**.</span></span>

<span data-ttu-id="d22d2-125">사용 하 여 "**MyWebApp**" 응용 프로그램 이름 및 다음 tap에 대 한 **Enter**합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-125">Use "**MyWebApp**" for the app name and then tap **Enter**.</span></span>

<span data-ttu-id="d22d2-126">Yeoman은 프로젝트와 해당 지원 파일 스 캐 폴드 할 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-126">Yeoman will scaffold the project and its supporting files.</span></span> <span data-ttu-id="d22d2-127">권장 되는 다음 단계 명령의 형태로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-127">Suggested next steps are also provided in the form of commands.</span></span>

![명령 창: ASP.NET 응용 프로그램의 이름은 무엇입니까?](yeoman/_static/yeoman-yo-aspnet-created.png)

<span data-ttu-id="d22d2-130">[ASP.NET 생성기](https://www.npmjs.com/package/generator-aspnet) ASP.NET Core Visual Studio 코드를 Visual Studio에 로드 하거나 명령줄에서 실행할 수 있는 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-130">The [ASP.NET generator](https://www.npmjs.com/package/generator-aspnet) creates ASP.NET Core projects that can be loaded into Visual Studio Code, Visual Studio, or run from the command line.</span></span>

## <a name="restore-build-and-run"></a><span data-ttu-id="d22d2-131">복원, 빌드 및 실행</span><span class="sxs-lookup"><span data-stu-id="d22d2-131">Restore, build, and run</span></span>

<span data-ttu-id="d22d2-132">디렉터리를 변경 하 여 제안 된 명령에 따라는 `MyWebApp` 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-132">Follow the suggested commands by changing directories to the `MyWebApp` directory.</span></span> <span data-ttu-id="d22d2-133">그러고 나 서 `dotnet restore`합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-133">Then run `dotnet restore`.</span></span>

![명령 창](yeoman/_static/dotnet-restore.png)

<span data-ttu-id="d22d2-135">빌드 및 사용 하 여 앱 실행 `dotnet build` 및 `dotnet run`:</span><span class="sxs-lookup"><span data-stu-id="d22d2-135">Build and run the app using `dotnet build` and `dotnet run`:</span></span>

![명령 창](yeoman/_static/dotnet-build-run.png)

<span data-ttu-id="d22d2-137">이 시점에서 새로 만든 ASP.NET Core 응용 프로그램을 테스트 하려면 표시 된 URL로 이동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-137">At this point, you can navigate to the URL shown to test the newly-created ASP.NET Core app.</span></span>

## <a name="client-side-packages"></a><span data-ttu-id="d22d2-138">클라이언트 패키지</span><span class="sxs-lookup"><span data-stu-id="d22d2-138">Client-side packages</span></span>

<span data-ttu-id="d22d2-139">프런트 엔드 리소스는 Yeoman에서 템플릿에 의해 제공 되며 생성기를 사용 하는 [Bower](xref:client-side/bower) , 추가 클라이언트 쪽 패키지 관리자 *bower.json* 및 *.bowerrc* Bower를 사용 하 여 클라이언트 패키지를 복원 하는 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-139">The front-end resources are provided by the templates from the Yeoman generator using the [Bower](xref:client-side/bower) client-side package manager, adding *bower.json* and *.bowerrc* files to restore client-side packages using Bower.</span></span>

<span data-ttu-id="d22d2-140">[BundlerMinifier](xref:client-side/bundling-and-minification) 구성 요소 (묶음) 연결의 용이성 및 CSS, JavaScript 및 HTML의 축소에 대해 기본적으로도 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-140">The [BundlerMinifier](xref:client-side/bundling-and-minification) component is also included by default for ease of concatenation (bundling) and minification of CSS, JavaScript, and HTML.</span></span>

## <a name="building-and-running-from-visual-studio"></a><span data-ttu-id="d22d2-141">Visual Studio에서 빌드하고 실행</span><span class="sxs-lookup"><span data-stu-id="d22d2-141">Building and running from Visual Studio</span></span>

<span data-ttu-id="d22d2-142">생성 된 ASP.NET Core 웹 프로젝트를 Visual Studio를 직접 로드 지정 합니다 빌드 및에서 프로젝트를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-142">You can load your generated ASP.NET Core web project directly into Visual Studio, then build and run your project from there.</span></span> <span data-ttu-id="d22d2-143">새 ASP.NET Core 응용 프로그램 Yeoman를 사용 하 여 스 캐 폴드 하려면 위의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-143">Follow the instructions above to scaffold a new ASP.NET Core app using Yeoman.</span></span> <span data-ttu-id="d22d2-144">이 시간 선택 **웹 응용 프로그램** 응용 프로그램 이름을 확인 하 고 메뉴 `MyWebApp`합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-144">This time, choose **Web Application** from the menu and name the app `MyWebApp`.</span></span>

<span data-ttu-id="d22d2-145">Visual Studio를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-145">Open Visual Studio.</span></span> <span data-ttu-id="d22d2-146">파일 메뉴에서 열기 ‣ 프로젝트/솔루션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-146">From the File menu, select Open ‣ Project/Solution.</span></span>

<span data-ttu-id="d22d2-147">프로젝트 열기 대화 상자에서로 이동 된 *.csproj* 파일을 선택 하 고 클릭는 **열려** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-147">In the Open Project dialog, navigate to the *.csproj* file, select it, and click the **Open** button.</span></span> <span data-ttu-id="d22d2-148">솔루션 탐색기에서 프로젝트 아래 스크린샷과 같이 다음과 같아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-148">In the Solution Explorer, the project should look something like the screenshot below.</span></span>

![파일 및 폴더의 솔루션 탐색기에서 새 프로젝트](yeoman/_static/yeoman-solution.png)

<span data-ttu-id="d22d2-150">Yeoman scaffolds MVC 웹 응용 프로그램, 완벽 하 게 둘 다 서버 및 클라이언트 쪽 빌드 지원입니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-150">Yeoman scaffolds a MVC web application, complete with both server- and client-side build support.</span></span> <span data-ttu-id="d22d2-151">서버 쪽 종속성 아래에 나열 됩니다는 **종속성/NuGet** 노드와의 클라이언트 쪽 종속성의 **종속성/Bower** 솔루션 탐색기의 노드.</span><span class="sxs-lookup"><span data-stu-id="d22d2-151">Server-side dependencies are listed under the **Dependencies/NuGet** node, and client-side dependencies in the **Dependencies/Bower** node of Solution Explorer.</span></span> <span data-ttu-id="d22d2-152">종속성은 프로젝트가 로드 되 면 자동으로 복원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-152">Dependencies are restored automatically when the project is loaded.</span></span>

![솔루션 탐색기 트리 뷰에서 종속성 노드에서 Bower 폴더는 해당 종속성을 나열 열려 있습니다.](yeoman/_static/yeoman-loading-dependencies.png)

<span data-ttu-id="d22d2-154">모든 종속 관계를 복원할 때 키를 눌러 **F5** 프로젝트를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-154">When all the dependencies are restored, press **F5** to run the project.</span></span> <span data-ttu-id="d22d2-155">기본 홈 페이지가 브라우저에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-155">The default home page displays in the browser.</span></span>

![웹 응용 프로그램에서 Microsoft Edge 열기](yeoman/_static/yeoman-home-page.png)

## <a name="restoring-building-and-hosting-from-a-command-line"></a><span data-ttu-id="d22d2-157">복원, 건물, 및 명령줄에서 호스팅</span><span class="sxs-lookup"><span data-stu-id="d22d2-157">Restoring, building, and hosting from a command line</span></span>

<span data-ttu-id="d22d2-158">준비 하 고.NET Core CLI를 사용 하 여 웹 응용 프로그램을 호스팅할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-158">You can prepare and host your web application using the .NET Core CLI.</span></span>

<span data-ttu-id="d22d2-159">명령 프롬프트에서 프로젝트가 포함 된 폴더에 현재 디렉터리를 변경 (즉, 포함 된 폴더로 *.csproj* 파일):</span><span class="sxs-lookup"><span data-stu-id="d22d2-159">At a command prompt, change the current directory to the folder containing the project (that is, the folder containing the *.csproj* file):</span></span>

```console
cd src\MyWebApp
```

<span data-ttu-id="d22d2-160">프로젝트의 NuGet 패키지 종속 파일을 복원 합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-160">Restore the project's NuGet package dependencies:</span></span>

```console
dotnet restore
```

<span data-ttu-id="d22d2-161">응용 프로그램을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-161">Run the application:</span></span>

```console
dotnet run
```

<span data-ttu-id="d22d2-162">플랫폼 간 [Kestrel](xref:fundamentals/servers/kestrel) 웹 서버에서 5000 포트에서 수신 대기를 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-162">The cross-platform [Kestrel](xref:fundamentals/servers/kestrel) web server will begin listening on port 5000.</span></span>

<span data-ttu-id="d22d2-163">웹 브라우저를 열고 이동 `http://localhost:5000`합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-163">Open a web browser, and navigate to `http://localhost:5000`.</span></span>

![웹 응용 프로그램에서 Microsoft Edge 열기](yeoman/_static/yeoman-home-page_5000.png)

## <a name="adding-to-your-project-with-sub-generators"></a><span data-ttu-id="d22d2-165">Sub 생성기에 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-165">Adding to your project with sub generators</span></span>

<span data-ttu-id="d22d2-166">Yeoman를 사용 하 여 [생성기 하위](https://www.github.com/omnisharp/generator-aspnet#sub-generators)를 추가할 수 있습니다는 `nuget.config` 또는 `web.config` 프로젝트가 만들어진 후 합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-166">Using Yeoman [sub generators](https://www.github.com/omnisharp/generator-aspnet#sub-generators), you can add either a `nuget.config` or a `web.config` after the project is created.</span></span> <span data-ttu-id="d22d2-167">예를 들어 파일 만들지 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d22d2-167">For example, execute the following command from the directory in which the file should be created:</span></span>

```console
yo aspnet:nugetconfig
```

<span data-ttu-id="d22d2-168">결과 라는 NuGet 구성 파일 `nuget.config` 다음 내용을 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="d22d2-168">The result is a NuGet configuration file named `nuget.config` with the following content:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
 <packageSources>
    <!--To inherit the global NuGet package sources remove the <clear/> line below -->
    <clear />
 </packageSources>
</configuration>
```

## <a name="additional-resources"></a><span data-ttu-id="d22d2-169">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="d22d2-169">Additional resources</span></span>

* [<span data-ttu-id="d22d2-170">서버 (Kestrel 및 WebListener)</span><span class="sxs-lookup"><span data-stu-id="d22d2-170">Servers (Kestrel and WebListener)</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="d22d2-171">기본 사항</span><span class="sxs-lookup"><span data-stu-id="d22d2-171">Fundamentals</span></span>](xref:fundamentals/index)

---
title: "Visual Studio 및 Git을 사용하여 Azure에 연속 배포"
author: rick-anderson
description: "Visual Studio를 사용하여 ASP.NET Core 웹앱을 만들고 연속 배포를 위한 Git를 사용하여 Azure App Service에 배포하는 방법을 알아봅니다."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 2707c7a8-2350-4304-9856-fda58e5c0a16
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/azure-continuous-deployment
ms.openlocfilehash: f7ea2e76fdee19a3d964e42053f0060a0a505e5b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="continuous-deployment-to-azure-for-aspnet-core-with-visual-studio-and-git"></a><span data-ttu-id="4f53a-104">Visual Studio 및 Git을 사용하여 Azure에 ASP.NET Core 연속 배포</span><span class="sxs-lookup"><span data-stu-id="4f53a-104">Continuous deployment to Azure for ASP.NET Core, with Visual Studio and Git</span></span>

<span data-ttu-id="4f53a-105">작성자: [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="4f53a-105">By [Erik Reitan](https://github.com/Erikre)</span></span>

<span data-ttu-id="4f53a-106">이 자습서에서는 Visual Studio를 사용하여 ASP.NET Core 웹앱을 만들고 연속 배포를 사용하여 Visual Studio에서 Azure App Service에 배포하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-106">This tutorial shows you how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span> 

<span data-ttu-id="4f53a-107">또한 [연속 배포를 사용하여 Azure Web App을 빌드하고 게시하기 위해 VSTS 사용](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)을 참조하세요. 여기서는 Visual Studio Team Services를 사용하여 [Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/)에 대한 CD(지속적인 업데이트) 워크플로를 구성하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-107">See also [Use VSTS to Build and Publish to an Azure Web App with Continuous Deployment](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/) using Visual Studio Team Services.</span></span> <span data-ttu-id="4f53a-108">Team Services의 Azure 지속적인 업데이트는 Azure App Service에 앱의 업데이트를 게시하는 강력한 배포 파이프라인을 간단하게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-108">Azure Continuous Delivery in Team Services simplifies setting up a robust deployment pipeline to publish updates for your app to Azure App Service.</span></span> <span data-ttu-id="4f53a-109">파이프라인을 빌드하고, 테스트를 실행하고, 스테이징 슬롯에 배포하고, 프로덕션에 배포하도록 Azure Portal에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-109">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot,  and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="4f53a-110">이 자습서를 완료하려면 Microsoft Azure 계정이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-110">To complete this tutorial, you need a Microsoft Azure account.</span></span> <span data-ttu-id="4f53a-111">계정이 없으면 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)하거나 [평가판에 등록](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-111">If you don't have an account, you can [activate your MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4f53a-112">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="4f53a-112">Prerequisites</span></span>

<span data-ttu-id="4f53a-113">이 자습서에서는 이미 다음 항목을 설치했다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-113">This tutorial assumes you have already installed the following:</span></span>

* [<span data-ttu-id="4f53a-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f53a-114">Visual Studio</span></span>](https://www.visualstudio.com)

* <span data-ttu-id="4f53a-115">[ASP.NET Core](https://www.microsoft.com/net/download/core)(런타임 및 도구)</span><span class="sxs-lookup"><span data-stu-id="4f53a-115">[ASP.NET Core](https://www.microsoft.com/net/download/core) (runtime and tooling)</span></span>

* <span data-ttu-id="4f53a-116">Windows용 [Git](https://git-scm.com/downloads)</span><span class="sxs-lookup"><span data-stu-id="4f53a-116">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="4f53a-117">ASP.NET Core 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="4f53a-117">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="4f53a-118">Visual Studio를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-118">Start Visual Studio.</span></span>

2. <span data-ttu-id="4f53a-119">**파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-119">From the **File** menu, select **New** > **Project**.</span></span>

3. <span data-ttu-id="4f53a-120">**ASP.NET Core 웹 응용 프로그램** 프로젝트 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-120">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="4f53a-121">**설치됨** > **템플릿** > **Visual C#** > **.NET Core** 아래에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-121">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="4f53a-122">프로젝트 이름을 `SampleWebAppDemo`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-122">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="4f53a-123">**새 Git 리포지토리 만들기** 옵션을 선택하고 **확인**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-123">Select the **Create new Git respository** option and click **OK**.</span></span>

   ![새 프로젝트 대화 상자](azure-continuous-deployment/_static/01-new-project.png)

4. <span data-ttu-id="4f53a-125">**새 ASP.NET Core 프로젝트** 대화 상자에서 ASP.NET Core **빈** 템플릿을 선택한 후 **확인**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-125">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![새 ASP.NET 프로젝트 대화 상자](azure-continuous-deployment/_static/02-web-site-template.png)

>[!NOTE]
    ><span data-ttu-id="4f53a-127">최신 .NET Core 릴리스는 2.0입니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-127">Most recent release of .NET Core is 2.0</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="4f53a-128">로컬로 웹앱 실행</span><span class="sxs-lookup"><span data-stu-id="4f53a-128">Running the web app locally</span></span>

1. <span data-ttu-id="4f53a-129">Visual Studio에서 앱 만들기를 완료하면 **디버그** -> **디버깅 시작**을 선택하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-129">Once Visual Studio finishes creating the app, run the app by selecting **Debug** -> **Start Debugging**.</span></span> <span data-ttu-id="4f53a-130">대안으로 **F5** 키를 눌러도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-130">As an alternative, you can press **F5**.</span></span>

   <span data-ttu-id="4f53a-131">Visual Studio 및 새 앱을 초기화하는 데 시간이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-131">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="4f53a-132">작업이 완료되면 브라우저는 실행 중인 앱을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-132">Once it is complete, the browser will show the running app.</span></span>

   ![브라우저 창에서는 'Hello World!'을 표시하는 응용 프로그램이 실행 중이라고 표시합니다.](azure-continuous-deployment/_static/04-browser-runapp.png)

2. <span data-ttu-id="4f53a-134">실행 중인 웹앱을 검토한 후 브라우저를 닫고 Visual Studio의 도구 모음에서 "디버깅 중지" 아이콘을 클릭하여 앱을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-134">After reviewing the running Web app, close the browser and click the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="4f53a-135">Azure Portal에서 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="4f53a-135">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="4f53a-136">다음 단계에서는 Azure Portal에서 웹앱을 만드는 과정을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-136">The following steps will guide you through creating a web app in the Azure Portal.</span></span>

1. <span data-ttu-id="4f53a-137">[Azure Portal](https://portal.azure.com)에 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-137">Log in to the [Azure Portal](https://portal.azure.com)</span></span>
2. <span data-ttu-id="4f53a-138">포털의 왼쪽 맨 위에 있는 **새로 만들기**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-138">TAP **NEW** at the top left of the Portal</span></span>
3. <span data-ttu-id="4f53a-139">**웹 + 모바일** > **Web App**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-139">TAP **Web + Mobile** > **Web App**</span></span>

    ![Microsoft Azure Portal: 새 단추: Marketplace 아래에서 웹 + 모바일: 주요 앱 아래에서 Web App 단추](azure-continuous-deployment/_static/05-azure-newwebapp.png)

4.  <span data-ttu-id="4f53a-141">**Web App** 블레이드에서 **App Service 이름**에 고유한 값을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-141">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

    ![Web App 블레이드](azure-continuous-deployment/_static/06-azure-newappblade.png)

    >[!NOTE]
    ><span data-ttu-id="4f53a-143">**App Service 이름** 이름은 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-143">The **App Service Name** name needs to be unique.</span></span> <span data-ttu-id="4f53a-144">이름을 입력하려는 경우 포털은 이 규칙을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-144">The portal will enforce this rule when you attempt to enter the name.</span></span> <span data-ttu-id="4f53a-145">다른 값을 입력한 후 이 자습서에서 표시되는 **SampleWebAppDemo**의 각 항목에 해당 값을 대체해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-145">After you enter a different value, you'll need to substitute that value for each occurrence of **SampleWebAppDemo** that you see in this tutorial.</span></span>

    &nbsp;
    
    <span data-ttu-id="4f53a-146">또한 **Web App** 블레이드에서 기존 **App Service 계획/위치**를 선택하거나 새로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-146">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="4f53a-147">새 계획을 만들면 가격 책정 계층, 위치 및 기타 옵션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-147">If you create a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="4f53a-148">App Service 계획에 대한 자세한 내용은 [Azure App Service 계획 세부 개요](https://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4f53a-148">For more information on App Service plans, [Azure App Service plans in-depth overview](https://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/).</span></span>

5.  <span data-ttu-id="4f53a-149">
              **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-149">Click **Create**.</span></span> <span data-ttu-id="4f53a-150">Azure에서는 웹앱을 프로비전하고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-150">Azure will provision and start your web app.</span></span>

    ![Azure Portal: 샘플 Web App 데모 01 Essentials 블레이드](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="4f53a-152">새 웹앱에 대한 Git 게시 사용</span><span class="sxs-lookup"><span data-stu-id="4f53a-152">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="4f53a-153">Git는 Azure App Service 웹앱을 배포하는 데 사용할 수 있는 분산된 버전 제어 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-153">Git is a distributed version control system that you can use to deploy your Azure App Service web app.</span></span> <span data-ttu-id="4f53a-154">로컬 Git 리포지토리에서 웹앱에 작성하는 코드를 저장하고 원격 리포지토리에 푸시하여 Azure에 코드를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-154">You'll store the code you write for your web app in a local Git repository, and you'll deploy your code to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="4f53a-155">아직 로그인하지 않은 경우 [Azure Portal](https://portal.azure.com)에 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-155">Log into the [Azure Portal](https://portal.azure.com), if you're not already logged in.</span></span>

2. <span data-ttu-id="4f53a-156">**App Services**를 클릭하여 Azure 구독과 연결된 앱 서비스 목록을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-156">Click **App Services** to view a list of the app services associated with your Azure subscription.</span></span>

3. <span data-ttu-id="4f53a-157">이 자습서의 이전 섹션에서 만든 웹앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-157">Select the web app you created in the previous section of this tutorial.</span></span>

4. <span data-ttu-id="4f53a-158">**배포** 블레이드에서 **배포 옵션** > **원본 선택** > **로컬 Git 리포지토리**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-158">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![설정 블레이드: 배포 원본 블레이드: 원본 블레이드 선택](azure-continuous-deployment/_static/deployment-options.png)

5. <span data-ttu-id="4f53a-160">**확인**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-160">Click **OK**.</span></span>

6. <span data-ttu-id="4f53a-161">이전에 웹앱 또는 다른 App Service 앱을 게시하기 위해 배포 자격 증명을 설정하지 않은 경우 지금 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-161">If you have not previously set up deployment credentials for publishing a web app or other App Service app, set them up now:</span></span>

   * <span data-ttu-id="4f53a-162">**설정** > **배포 자격 증명**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-162">Click **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="4f53a-163">**배포 자격 증명 설정** 블레이드가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-163">The **Set deployment credentials** blade will be displayed.</span></span>

   * <span data-ttu-id="4f53a-164">사용자 이름 및 암호를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-164">Create a user name and password.</span></span>  <span data-ttu-id="4f53a-165">나중에 Git를 설정할 때 이 암호가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-165">You'll need this password later when setting up Git.</span></span>

   * <span data-ttu-id="4f53a-166">**저장**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-166">Click **Save**.</span></span>

7. <span data-ttu-id="4f53a-167">**Web App** 블레이드에서 **설정** > **속성**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-167">In the **Web App** blade, click **Settings** > **Properties**.</span></span> <span data-ttu-id="4f53a-168">배포할 원격 Git 리포지토리 URL이 **GIT URL** 아래에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-168">The URL of the remote Git repository that you'll deploy to is shown under **GIT URL**.</span></span>

8. <span data-ttu-id="4f53a-169">나중에 자습서에서 사용할 **GIT URL** 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-169">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Azure Portal: 응용 프로그램 속성 블레이드](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-your-web-app-to-azure-app-service"></a><span data-ttu-id="4f53a-171">Azure App Service에 웹앱 게시</span><span class="sxs-lookup"><span data-stu-id="4f53a-171">Publish your web app to Azure App Service</span></span>

<span data-ttu-id="4f53a-172">이 섹션에서는 Visual Studio를 사용하여 로컬 Git 리포지토리를 만들고 해당 리포지토리로부터 Azure에 푸시하여 웹앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-172">In this section, you will create a local Git repository using Visual Studio and push from that repository to Azure to deploy your web app.</span></span> <span data-ttu-id="4f53a-173">관련 단계에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-173">The steps involved include the following:</span></span>

   * <span data-ttu-id="4f53a-174">Azure에 로컬 리포지토리를 배포할 수 있도록 GIT URL 값을 사용하여 원격 리포지토리 설정을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-174">Add the remote repository setting using your GIT URL value, so you can deploy your local repository to Azure.</span></span>

   * <span data-ttu-id="4f53a-175">프로젝트에 변경 내용이 커밋됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-175">Commit your project changes.</span></span>

   * <span data-ttu-id="4f53a-176">로컬 리포지토리로부터 Azure의 원격 리포지토리에 프로젝트 변경 내용이 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-176">Push your project changes from your local repository to your remote repository on Azure.</span></span>

&nbsp;
   
1.  <span data-ttu-id="4f53a-177">**솔루션 탐색기**에서 **솔루션 'SampleWebAppDemo'**를 마우스 오른쪽 단추로 클릭하고 **커밋**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-177">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="4f53a-178">**팀 탐색기**가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-178">The **Team Explorer** will be displayed.</span></span>

    ![팀 탐색기 연결 탭](azure-continuous-deployment/_static/10-team-explorer.png)

2.  <span data-ttu-id="4f53a-180">**팀 탐색기**에서 **홈**(홈 아이콘) > **설정** > **리포지토리 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-180">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

3.  <span data-ttu-id="4f53a-181">**리포지토리 설정**의 **원격** 섹션에서 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-181">In the **Remotes** section of the **Repository Settings** select **Add**.</span></span> <span data-ttu-id="4f53a-182">**원격 추가** 대화 상자가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-182">The **Add Remote** dialog box will be displayed.</span></span>

4.  <span data-ttu-id="4f53a-183">원격 리포지토리의 **이름**을 **Azure SampleApp**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-183">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

5.  <span data-ttu-id="4f53a-184">**인출**의 값을 이 자습서의 앞부분에 나오는 Azure에서 복사한 **Git URL**로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-184">Set the value for **Fetch** to the **Git URL** that you copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="4f53a-185">이 URL은 **.git**로 끝납니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-185">Note that this is the URL that ends with **.git**.</span></span>

    ![원격 대화 상자 편집](azure-continuous-deployment/_static/11-add-remote.png)

    >[!NOTE]
    ><span data-ttu-id="4f53a-187">대안으로 **명령 창**을 열고,프로젝트 디렉터리를 변경하고, 명령을 입력하여 **명령 창**의 원격 저장소를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-187">As an alternative, you can specify the remote repository from the **Command Window** by opening the **Command Window**, changing to your project directory, and entering the command.</span></span> <span data-ttu-id="4f53a-188">예를 들면 다음과 같습니다.`git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`</span><span class="sxs-lookup"><span data-stu-id="4f53a-188">For example:`git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`</span></span>

6.  <span data-ttu-id="4f53a-189">**홈**(홈 아이콘) > **설정** > **전역 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-189">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="4f53a-190">사용자 이름 및 전자 메일 주소를 설정했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-190">Make sure you have your name and your email address set.</span></span> <span data-ttu-id="4f53a-191">**업데이트**를 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-191">You may also need to select **Update**.</span></span>

7.  <span data-ttu-id="4f53a-192">**홈** > **변경 내용**을 선택하여 **변경 내용** 보기로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-192">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

8.  <span data-ttu-id="4f53a-193">**초기 푸시 #1**과 같은 커밋 메시지를 입력하고 **커밋**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-193">Enter a commit message, such as **Initial Push #1** and click **Commit**.</span></span> <span data-ttu-id="4f53a-194">이렇게 하면 로컬로 *커밋*을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-194">This action will create a *commit* locally.</span></span> <span data-ttu-id="4f53a-195">다음으로 Azure와 *동기화*해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-195">Next, you need to *sync* with Azure.</span></span>

    ![팀 탐색기 연결 탭](azure-continuous-deployment/_static/12-initial-commit.png)

    >[!NOTE]
    ><span data-ttu-id="4f53a-197">대안으로 **명령 창**을 열고,프로젝트 디렉터리를 변경하고, git 명령을 입력하여 **명령 창**의 변경 내용을 커밋할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-197">As an alternative, you can commit your changes from the **Command Window** by opening the **Command Window**, changing to your project directory, and entering the git commands.</span></span> <span data-ttu-id="4f53a-198">예:</span><span class="sxs-lookup"><span data-stu-id="4f53a-198">For example:</span></span>
    >
    >`git add .`
    >
    >`git commit -am "Initial Push #1"`

9.  <span data-ttu-id="4f53a-199">**홈** > **동기화** > **동작** > **명령 프롬프트 열기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-199">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="4f53a-200">프로젝트 디렉터리에 명령 프롬프트가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-200">The command prompt will open to your project directory.</span></span>

10.  <span data-ttu-id="4f53a-201">명령 창에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-201">Enter the following command in the command window:</span></span>

    `git push -u Azure-SampleApp master`

11.  <span data-ttu-id="4f53a-202">Azure에서 만든 Azure **배포 자격 증명** 암호를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-202">Enter your Azure **deployment credentials** password that you created earlier in Azure.</span></span>

    >[!NOTE]
    ><span data-ttu-id="4f53a-203">사용자가 입력할 때 암호는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-203">Your password will not be visible as you enter it.</span></span>

    <span data-ttu-id="4f53a-204">이 명령은 로컬 프로젝트 파일을 Azure로 푸시하는 프로세스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-204">This command will start the process of pushing your local project files to Azure.</span></span> <span data-ttu-id="4f53a-205">위 명령의 출력은 성공적으로 배포했다는 메시지와 함께 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-205">The output from the above command ends with a message that deployment was successful.</span></span>
        
    ```
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
    * [new branch]      master -> master
    Branch master set up to track remote branch master from Azure-SampleApp.
    ```
    > [!NOTE]
    > <span data-ttu-id="4f53a-206">프로젝트에서 공동 작업해야 하는 경우 Azure에 푸시 푸시하는 동안 [GitHub](https://github.com)에 푸시할지를 고려해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-206">If you need to collaborate on a project, you should consider pushing to [GitHub](https://github.com) in between pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="4f53a-207">활성 배포 확인</span><span class="sxs-lookup"><span data-stu-id="4f53a-207">Verify the Active Deployment</span></span>

<span data-ttu-id="4f53a-208">로컬 환경으로부터 Azure로 웹앱을 성공적으로 전송했는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-208">You can verify that you successfully transferred the web app from your local environment to Azure.</span></span> <span data-ttu-id="4f53a-209">배포 성공이 나열됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-209">You'll see the listed successful deployment.</span></span>

1. <span data-ttu-id="4f53a-210">[Azure Portal](https://portal.azure.com)에서 웹앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-210">In the [Azure Portal](https://portal.azure.com), select your web app.</span></span> <span data-ttu-id="4f53a-211">그런 다음 **배포** > **배포 옵션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-211">Then, select **Deployment** > **Deployment options**.</span></span>

   ![Azure Portal: 설정 블레이드: 성공적인 배포를 보여주는 배포 블레이드](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="4f53a-213">Azure에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="4f53a-213">Run the app in Azure</span></span>

<span data-ttu-id="4f53a-214">이제 Azure에 웹앱을 배포했으므로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-214">Now that you have deployed your web app to Azure, you can run the app.</span></span>

<span data-ttu-id="4f53a-215">이 작업은 다음 두 가지 방법으로 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-215">This can be done in two ways:</span></span>

* <span data-ttu-id="4f53a-216">Azure Portal에서 웹앱의 웹앱 블레이드를 찾고 **찾아보기**를 클릭하여 기본 브라우저에서 앱을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-216">In the Azure Portal, locate the web app blade for your web app, and click **Browse** to view your app in your default browser.</span></span>

* <span data-ttu-id="4f53a-217">브라우저를 열고 웹앱의 URL을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-217">Open a browser and enter the URL for your web app.</span></span> <span data-ttu-id="4f53a-218">예:</span><span class="sxs-lookup"><span data-stu-id="4f53a-218">For example:</span></span>

  `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-your-web-app-and-republish"></a><span data-ttu-id="4f53a-219">웹앱 업데이트 및 다시 게시</span><span class="sxs-lookup"><span data-stu-id="4f53a-219">Update your web app and republish</span></span>

<span data-ttu-id="4f53a-220">로컬 코드를 변경한 후에 다시 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-220">After you make changes to your local code, you can republish.</span></span>

1.  <span data-ttu-id="4f53a-221">Visual Studio의 **솔루션 탐색기**에서 *Startup.cs* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-221">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

2.  <span data-ttu-id="4f53a-222">`Configure` 메서드에서 `Response.WriteAsync` 메서드를 수정하여 다음과 같이 표시되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-222">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

    ```aspx-cs
    await context.Response.WriteAsync("Hello World! Deploy to Azure.");
    ```
3.  <span data-ttu-id="4f53a-223">변경 내용을 *Startup.cs*에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-223">Save changes to *Startup.cs*.</span></span>

4.  <span data-ttu-id="4f53a-224">**솔루션 탐색기**에서 **솔루션 'SampleWebAppDemo'**를 마우스 오른쪽 단추로 클릭하고 **커밋**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-224">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="4f53a-225">**팀 탐색기**가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-225">The **Team Explorer** will be displayed.</span></span>

5.  <span data-ttu-id="4f53a-226">다음과 같이 커밋 메시지를 입력하세요.</span><span class="sxs-lookup"><span data-stu-id="4f53a-226">Enter a commit message, such as:</span></span>

    ```none
    Update #2
    ```

6.  <span data-ttu-id="4f53a-227">**커밋** 단추를 눌러 프로젝트 변경 내용을 커밋합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-227">Press the **Commit** button to commit the project changes.</span></span>

7.  <span data-ttu-id="4f53a-228">**홈** > **동기화** > **작업** > **푸시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-228">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

>[!NOTE]
><span data-ttu-id="4f53a-229">대안으로 **명령 창**을 열고,프로젝트 디렉터리를 변경하고, git 명령을 입력하여 **명령 창**의 변경 내용을 푸시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-229">As an alternative, you can push your changes from the **Command Window** by opening the **Command Window**, changing to your project directory, and entering a git command.</span></span> <span data-ttu-id="4f53a-230">예:</span><span class="sxs-lookup"><span data-stu-id="4f53a-230">For example:</span></span>
>
>`git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="4f53a-231">Azure에서 업데이트된 웹앱 보기</span><span class="sxs-lookup"><span data-stu-id="4f53a-231">View the updated web app in Azure</span></span>

<span data-ttu-id="4f53a-232">Azure Portal에서 웹앱 블레이드의 **찾아보기**를 선택하거나 브라우저를 열고 웹앱의 URL을 입력하여 업데이트된 웹앱을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="4f53a-232">View your updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for your web app.</span></span> <span data-ttu-id="4f53a-233">예:</span><span class="sxs-lookup"><span data-stu-id="4f53a-233">For example:</span></span>

   `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a><span data-ttu-id="4f53a-234">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="4f53a-234">Additional Resources</span></span>

* [<span data-ttu-id="4f53a-235">게시 및 배포</span><span class="sxs-lookup"><span data-stu-id="4f53a-235">Publishing and Deployment</span></span>](index.md)

* [<span data-ttu-id="4f53a-236">프로젝트 Kudu</span><span class="sxs-lookup"><span data-stu-id="4f53a-236">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)

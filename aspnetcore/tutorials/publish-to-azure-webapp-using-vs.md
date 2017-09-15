---
title: "Visual Studio를 사용하여 Azure에 ASP.NET Core 앱 게시"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: get-started-article
ms.assetid: 78571e4a-a143-452d-9cf2-0860f85972e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: c4df5f4551760fbc4ed4b11362d249b24f186e00
ms.sourcegitcommit: 8f5277871eff86134ebf68d3737196cfd4a62c2c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2017
---
# <a name="publish-an-aspnet-core-web-app-to-azure-app-service-using-visual-studio"></a><span data-ttu-id="8181a-103">Visual Studio를 사용하여 Azure App Service에 ASP.NET Core 웹앱 게시</span><span class="sxs-lookup"><span data-stu-id="8181a-103">Publish an ASP.NET Core web app to Azure App Service using Visual Studio</span></span>

<span data-ttu-id="8181a-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Cesar Blum Silveira](https://github.com/cesarbs)</span><span class="sxs-lookup"><span data-stu-id="8181a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Cesar Blum Silveira](https://github.com/cesarbs)</span></span>

## <a name="set-up-the-development-environment"></a><span data-ttu-id="8181a-105">개발 환경 설정</span><span class="sxs-lookup"><span data-stu-id="8181a-105">Set up the development environment</span></span>

* <span data-ttu-id="8181a-106">최신 [Azure SDK for Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-106">Install the latest [Azure SDK for Visual Studio](https://www.visualstudio.com/features/azure-tools-vs).</span></span> <span data-ttu-id="8181a-107">SDK는 아직 없는 경우 Visual Studio를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-107">The SDK installs Visual Studio if you don't already have it.</span></span>

> [!NOTE]
> <span data-ttu-id="8181a-108">SDK 설치는 컴퓨터에 많은 종속성이 없는 경우 30분 이상 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-108">The SDK installation can take more than 30 minutes if your machine doesn't have many of the dependencies.</span></span>

* <span data-ttu-id="8181a-109">[.NET Core + Visual Studio 도구](http://go.microsoft.com/fwlink/?LinkID=798306) 설치</span><span class="sxs-lookup"><span data-stu-id="8181a-109">Install [.NET Core + Visual Studio tooling](http://go.microsoft.com/fwlink/?LinkID=798306)</span></span>

* <span data-ttu-id="8181a-110">[Azure 계정](https://portal.azure.com/)을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-110">Verify your [Azure account](https://portal.azure.com/).</span></span> <span data-ttu-id="8181a-111">[Azure 계정을 열](https://azure.microsoft.com/pricing/free-trial/)거나 [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-111">You can [open a free Azure account](https://azure.microsoft.com/pricing/free-trial/) or [Activate Visual Studio subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).</span></span>

## <a name="create-a-web-app"></a><span data-ttu-id="8181a-112">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="8181a-112">Create a web app</span></span>

<span data-ttu-id="8181a-113">Visual Studio 시작 페이지에서 **새 프로젝트...**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-113">In the Visual Studio Start Page, tap **New Project...**.</span></span>

![시작 페이지](publish-to-azure-webapp-using-vs/_static/new_project.png)

<span data-ttu-id="8181a-115">또는 메뉴를 사용하여 새 프로젝트를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-115">Alternatively, you can use the menus to create a new project.</span></span> <span data-ttu-id="8181a-116">**파일 > 새로 만들기 > 프로젝트...**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-116">Tap **File > New > Project...**.</span></span>

![파일 메뉴](publish-to-azure-webapp-using-vs/_static/alt_new_project.png)

<span data-ttu-id="8181a-118">**새 프로젝트** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-118">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="8181a-119">왼쪽 창에서 **웹**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-119">In the left pane, tap **Web**</span></span>

* <span data-ttu-id="8181a-120">가운데 창에서 **ASP.NET Core 웹 응용 프로그램(.NET Core)**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-120">In the center pane, tap **ASP.NET Core Web Application (.NET Core)**</span></span>

* <span data-ttu-id="8181a-121">**확인**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-121">Tap **OK**</span></span>

![새 프로젝트 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="8181a-123">**새 ASP.NET Core 웹 응용 프로그램(.NET Core)** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="8181a-123">In the **New ASP.NET Core Web Application (.NET Core)** dialog:</span></span>

* <span data-ttu-id="8181a-124">**웹 응용 프로그램**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-124">Tap **Web Application**</span></span>

* <span data-ttu-id="8181a-125">**인증**이 **개별 사용자 계정**으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-125">Verify **Authentication** is set to **Individual User Accounts**</span></span>

* <span data-ttu-id="8181a-126">**클라우드에서 호스트**가 선택되지 **않았는지** 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-126">Verify **Host in the cloud** is **not** checked</span></span>

* <span data-ttu-id="8181a-127">**확인**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-127">Tap **OK**</span></span>

![새 ASP.NET Core 웹 응용 프로그램(.NET Core) 대화 상자](publish-to-azure-webapp-using-vs/_static/noath.png)

## <a name="test-the-app-locally"></a><span data-ttu-id="8181a-129">로컬로 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="8181a-129">Test the app locally</span></span>

* <span data-ttu-id="8181a-130">**Ctrl+F5** 키를 눌러 로컬로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-130">Press **Ctrl-F5** to run the app locally</span></span>

* <span data-ttu-id="8181a-131">**정보** 및 **연락처** 링크를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-131">Tap the **About** and **Contact** links.</span></span> <span data-ttu-id="8181a-132">장치 크기에 따라 탐색 아이콘을 눌러 링크를 표시해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-132">Depending on the size of your device, you might need to tap the navigation icon to show the links</span></span>

![localhost의 Microsoft Edge에서 열린 웹 응용 프로그램](publish-to-azure-webapp-using-vs/_static/show.png)

* <span data-ttu-id="8181a-134">**등록**을 누르고 새 사용자를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-134">Tap **Register** and register a new user.</span></span> <span data-ttu-id="8181a-135">가상의 전자 메일 주소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-135">You can use a fictitious email address.</span></span> <span data-ttu-id="8181a-136">제출하면 다음과 같은 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-136">When you submit, you'll get the following error:</span></span>

![내부 서버 오류: 요청을 처리하는 동안 데이터베이스 작업이 실패했습니다.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="8181a-140">두 가지 방법으로 문제를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-140">You can fix the problem in two different ways:</span></span>

* <span data-ttu-id="8181a-141">**마이그레이션 적용**을 누르고 페이지 업데이트를 누르고, 페이지를 새로 고칩니다. 또는</span><span class="sxs-lookup"><span data-stu-id="8181a-141">Tap **Apply Migrations** and, once the page updates, refresh the page; or</span></span>

* <span data-ttu-id="8181a-142">프로젝트 디렉터리의 명령 프롬프트에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-142">Run the following from a command prompt in the project's directory:</span></span>

  <!-- literal_block {"ids": [], "xml:space": "preserve"} -->

  ```
  dotnet ef database update
     ```

<span data-ttu-id="8181a-143">앱은 새 사용자를 등록하는 데 사용한 전자 메일 및 **로그오프** 링크를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-143">The app displays the email used to register the new user and a **Log off** link.</span></span>

![Microsoft Edge에서 열린 웹 응용 프로그램.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="8181a-146">Azure에 앱 배포</span><span class="sxs-lookup"><span data-stu-id="8181a-146">Deploy the app to Azure</span></span>

<span data-ttu-id="8181a-147">배포할 게시된 앱이 실행되고 있지 않은지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-147">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="8181a-148">앱이 실행 중이면 *publish* 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-148">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="8181a-149">잠긴 파일을 복사할 수 없으므로 배포를 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-149">Deployment can't occur because locked files can't be copied.</span></span>

<span data-ttu-id="8181a-150">솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-150">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![강조 표시된 게시 링크로 열린 바로 가기 메뉴](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="8181a-152">**게시** 대화 상자에서 **Microsoft Azure App Service**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-152">In the **Publish** dialog, tap **Microsoft Azure App Service**.</span></span>

![게시 대화 상자](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="8181a-154">**새로 만들기...**를 눌러 새 리소스 그룹을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-154">Tap **New...** to create a new resource group.</span></span> <span data-ttu-id="8181a-155">새 리소스 그룹 만들기를 통해 이 자습서에서 만드는 모든 Azure 리소스를 더욱 쉽게 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-155">Creating a new resource group will make it easier to delete all the Azure resources you create in this tutorial.</span></span>

![App Service 대화 상자](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="8181a-157">새 리소스 그룹 및 앱 서비스 계획을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-157">Create a new resource group and app service plan:</span></span>

* <span data-ttu-id="8181a-158">리소스 그룹에 대해 **새로 만들기...**를 누르고 새 리소스 그룹에 대한 이름을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-158">Tap **New...** for the resource group and enter a name for the new resource group</span></span>

* <span data-ttu-id="8181a-159">앱 서비스 계획에 대해 **새로 만들기...**를 누르고 가까운 위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-159">Tap **New...** for the  app service plan and select a location near you.</span></span> <span data-ttu-id="8181a-160">생성된 기본 이름을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-160">You can keep the default generated name</span></span>

* <span data-ttu-id="8181a-161">**추가 Azure 서비스 탐색**을 눌러 새 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-161">Tap **Explore additional Azure services** to create a new database</span></span>

![새 리소스 그룹 대화 상자: 호스팅 패널](publish-to-azure-webapp-using-vs/_static/cas.png)

* <span data-ttu-id="8181a-163">녹색 **+** 아이콘을 눌러 새 SQL Database를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-163">Tap the green **+** icon to create a new SQL Database</span></span>

![새 리소스 그룹 대화 상자: 서비스 패널](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="8181a-165">**SQL Database 구성** 대화 상자에서 **새로 만들기...**를 눌러 새 데이터베이스 서버를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-165">Tap **New...** on the **Configure SQL Database** dialog to create a new database server.</span></span>

![SQL Database 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/conf.png)

* <span data-ttu-id="8181a-167">관리자 사용자 이름 및 암호를 입력한 다음 **확인**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-167">Enter an administrator user name and password, and then tap **OK**.</span></span> <span data-ttu-id="8181a-168">이 단계에서 만드는 사용자 이름 및 암호를 잊지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="8181a-168">Don't forget the user name and password you create in this step.</span></span> <span data-ttu-id="8181a-169">기본 **서버 이름**을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-169">You can keep the default **Server Name**</span></span>

![SQL Server 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

> [!NOTE]
> <span data-ttu-id="8181a-171">"관리자"는 관리자 사용자 이름으로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-171">"admin" is not allowed as the administrator user name.</span></span>

* <span data-ttu-id="8181a-172">**SQL Database 구성** 대화 상자에서 **확인**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-172">Tap **OK** on the  **Configure SQL Database** dialog</span></span>

![SQL Database 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/conf_final.png)

* <span data-ttu-id="8181a-174">**App Service 만들기** 대화 상자에서 **만들기**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-174">Tap **Create** on the **Create App Service** dialog</span></span>

![App Service 만들기 대화 상자](publish-to-azure-webapp-using-vs/_static/create_as.png)

* <span data-ttu-id="8181a-176">**게시** 대화 상자에서 **다음**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-176">Tap **Next** in the **Publish** dialog</span></span>

![게시 대화 상자: 연결 패널](publish-to-azure-webapp-using-vs/_static/pubc.png)

* <span data-ttu-id="8181a-178">**게시** 대화 상자의 **설정** 단계에서:</span><span class="sxs-lookup"><span data-stu-id="8181a-178">On the **Settings** stage of the **Publish** dialog:</span></span>

  * <span data-ttu-id="8181a-179">**데이터베이스**를 확장하고 **런타임 시 이 연결 문자열 사용**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-179">Expand **Databases** and check **Use this connection string at runtime**</span></span>

  * <span data-ttu-id="8181a-180">**Entity Framework 마이그레이션**을 확장하고 **게시에 이 마이그레이션 적용**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-180">Expand **Entity Framework Migrations** and check **Apply this migration on publish**</span></span>

* <span data-ttu-id="8181a-181">**게시**를 누르고 Visual Studio에서 앱 게시를 완료할 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-181">Tap **Publish** and wait until Visual Studio finishes publishing your app</span></span>

![게시 대화 상자: 설정 패널](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="8181a-183">Visual Studio는 앱을 Azure에 게시하고 브라우저에서 클라우드 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-183">Visual Studio will publish your app to Azure and launch the cloud app in your browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="8181a-184">Azure에서 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="8181a-184">Test your app in Azure</span></span>

* <span data-ttu-id="8181a-185">**정보** 및 **연락처** 링크 테스트</span><span class="sxs-lookup"><span data-stu-id="8181a-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="8181a-186">새 사용자 등록</span><span class="sxs-lookup"><span data-stu-id="8181a-186">Register a new user</span></span>

![Azure App Service의 Microsoft Edge에서 열린 웹 응용 프로그램](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="update-the-app"></a><span data-ttu-id="8181a-188">앱 업데이트</span><span class="sxs-lookup"><span data-stu-id="8181a-188">Update the app</span></span>

* <span data-ttu-id="8181a-189">`Views/Home/About.cshtml` Razor 보기 파일을 편집하고 해당 내용을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-189">Edit the `Views/Home/About.cshtml` Razor view file and change its contents.</span></span> <span data-ttu-id="8181a-190">예:</span><span class="sxs-lookup"><span data-stu-id="8181a-190">For example:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [7]}} -->

```html
@{
       ViewData["Title"] = "About";
   }
   <h2>@ViewData["Title"].</h2>
   <h3>@ViewData["Message"]</h3>

   <p>My updated about page.</p>
   ```

* <span data-ttu-id="8181a-191">프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-191">Right-click on the project and tap **Publish...** again</span></span>

![강조 표시된 게시 링크로 열린 바로 가기 메뉴](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="8181a-193">앱이 게시된 후 변경 내용이 Azure에서 제공되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-193">After the app is published, verify the changes you made are available on Azure</span></span>

### <a name="clean-up"></a><span data-ttu-id="8181a-194">정리</span><span class="sxs-lookup"><span data-stu-id="8181a-194">Clean up</span></span>

<span data-ttu-id="8181a-195">앱 테스트를 완료하면 [Azure Portal](https://portal.azure.com/)로 이동하고 앱을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-195">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="8181a-196">**리소스 그룹**을 선택한 다음 만든 리소스 그룹을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-196">Select **Resource groups**, then tap the resource group you created</span></span>

![Azure Portal: 사이드바 메뉴에 있는 리소스 그룹](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="8181a-198">**리소스 그룹** 블레이드에서 **삭제**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-198">In the **Resource group** blade, tap **Delete**</span></span>

![Azure Portal: 리소스 그룹 블레이드](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="8181a-200">리소스 그룹의 이름을 입력하고 **삭제**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-200">Enter the name of the resource group and tap **Delete**.</span></span> <span data-ttu-id="8181a-201">이 자습서에서 만든 앱과 다른 모든 리소스는 이제 Azure에서 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="8181a-201">Your app and all other resources created in this tutorial are now deleted from Azure</span></span>

### <a name="next-steps"></a><span data-ttu-id="8181a-202">다음 단계</span><span class="sxs-lookup"><span data-stu-id="8181a-202">Next steps</span></span>

* [<span data-ttu-id="8181a-203">ASP.NET Core MVC 및 Visual Studio 시작</span><span class="sxs-lookup"><span data-stu-id="8181a-203">Getting started with ASP.NET Core MVC and Visual Studio</span></span>](first-mvc-app/start-mvc.md)

* [<span data-ttu-id="8181a-204">ASP.NET Core 소개</span><span class="sxs-lookup"><span data-stu-id="8181a-204">Introduction to ASP.NET Core</span></span>](../index.md)

* [<span data-ttu-id="8181a-205">기본 사항</span><span class="sxs-lookup"><span data-stu-id="8181a-205">Fundamentals</span></span>](../fundamentals/index.md)

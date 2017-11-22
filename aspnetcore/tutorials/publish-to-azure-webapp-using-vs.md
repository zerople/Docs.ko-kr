---
title: "Visual Studio를 사용하여 Azure에 ASP.NET Core 앱 게시"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/05/2017
ms.topic: get-started-article
ms.assetid: 78571e4a-a143-452d-9cf2-0860f85972e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 6f697ed4d8876a19cd058533e4f6a5d4f7cdc2fb
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="publish-an-aspnet-core-web-app-to-azure-app-service-using-visual-studio"></a><span data-ttu-id="19f84-103">Visual Studio를 사용하여 Azure App Service에 ASP.NET Core 웹앱 게시</span><span class="sxs-lookup"><span data-stu-id="19f84-103">Publish an ASP.NET Core web app to Azure App Service using Visual Studio</span></span>

<span data-ttu-id="19f84-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs) 및 [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="19f84-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs), and [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="19f84-105">Mac에서 작업하고 있는 경우 [Mac용 Visual Studio에서 Azure에 게시](https://blog.xamarin.com/publish-azure-visual-studio-mac/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="19f84-105">See [Publish to Azure from Visual Studio for Mac](https://blog.xamarin.com/publish-azure-visual-studio-mac/) if you are working on a Mac.</span></span>

## <a name="set-up"></a><span data-ttu-id="19f84-106">설치</span><span class="sxs-lookup"><span data-stu-id="19f84-106">Set up</span></span>

* <span data-ttu-id="19f84-107">계정이 없는 경우 [체험 Azure 계정](https://aka.ms/K5y5yh)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-107">Open a [free Azure account](https://aka.ms/K5y5yh) if you do not have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="19f84-108">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="19f84-108">Create a web app</span></span>

<span data-ttu-id="19f84-109">Visual Studio 시작 페이지에서 **파일 > 새로 만들기 > 프로젝트...**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-109">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![파일 메뉴](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="19f84-111">**새 프로젝트** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-111">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="19f84-112">왼쪽 창에서 **.NET Core**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-112">In the left pane, select **.NET Core**.</span></span>

* <span data-ttu-id="19f84-113">가운데 창에서 **ASP.NET Core 웹 응용 프로그램**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-113">In the center pane, select **ASP.NET Core Web Application**.</span></span>

* <span data-ttu-id="19f84-114">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-114">Select **OK**.</span></span>

![새 프로젝트 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="19f84-116">**새 ASP.NET Core 웹 응용 프로그램** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="19f84-116">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="19f84-117">**웹 응용 프로그램**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-117">Select **Web Application**.</span></span>

* <span data-ttu-id="19f84-118">**인증 변경**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-118">Select **Change Authentication**.</span></span>

![새 프로젝트 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="19f84-120">**인증 변경** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-120">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="19f84-121">**개별 사용자 계정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-121">Select **Individual User Accounts**.</span></span>

* <span data-ttu-id="19f84-122">**확인**을 선택하여 **새 ASP.NET Core 웹 응용 프로그램**으로 돌아간 다음 다시 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-122">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![새 ASP.NET Core 웹 인증 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="19f84-124">Visual Studio는 솔루션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-124">Visual Studio creates the solution.</span></span>

## <a name="run-the-app-locally"></a><span data-ttu-id="19f84-125">로컬에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="19f84-125">Run the app locally</span></span>

* <span data-ttu-id="19f84-126">**디버그**를 선택한 다음 **디버깅하지 않고 시작**을 선택하여 앱을 로컬에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-126">Choose **Debug** then **Start Without Debugging** to run the app locally.</span></span>

* <span data-ttu-id="19f84-127">**정보**와 **연락처** 링크를 클릭하여 웹 응용 프로그램 작업을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-127">Click the **About** and **Contact** links to verify the web application works.</span></span>

![localhost의 Microsoft Edge에서 열린 웹 응용 프로그램](publish-to-azure-webapp-using-vs/_static/show.png)

* <span data-ttu-id="19f84-129">**등록**을 선택하고 새 사용자를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-129">Select **Register** and register a new user.</span></span> <span data-ttu-id="19f84-130">가상의 전자 메일 주소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-130">You can use a fictitious email address.</span></span> <span data-ttu-id="19f84-131">제출하면 페이지에 다음과 같은 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-131">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="19f84-132">*“내부 서버 오류: 요청을 처리하는 동안 데이터베이스 작업이 실패했습니다. SQL 예외: 데이터베이스를 열 수 없습니다. 응용 프로그램 DB 컨텍스트에 대한 기존 마이그레이션 적용으로 이 문제를 해결할 수 있습니다.”*</span><span class="sxs-lookup"><span data-stu-id="19f84-132">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>

* <span data-ttu-id="19f84-133">**마이그레이션 적용**을 선택한 다음 페이지가 업데이트되면 페이지를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-133">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![내부 서버 오류: 요청을 처리하는 동안 데이터베이스 작업이 실패했습니다.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="19f84-137">앱은 새 사용자를 등록하는 데 사용한 전자 메일 및 **로그아웃** 링크를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-137">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Microsoft Edge에서 열린 웹 응용 프로그램.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="19f84-140">Azure에 앱 배포</span><span class="sxs-lookup"><span data-stu-id="19f84-140">Deploy the app to Azure</span></span>

<span data-ttu-id="19f84-141">웹 페이지를 닫고 Visual Studio로 돌아간 다음 **디버그** 메뉴에서 **디버깅 중지**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-141">Close the web page, return to Visual Studio, and select **Stop Debugging** from the **Debug** menu.</span></span>

<span data-ttu-id="19f84-142">솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-142">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![강조 표시된 게시 링크로 열린 바로 가기 메뉴](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="19f84-144">**게시** 대화 상자에서 **Microsoft Azure App Service**를 선택하고 **게시**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-144">In the **Publish** dialog, select **Microsoft Azure App Service** and click **Publish**.</span></span>

![게시 대화 상자](publish-to-azure-webapp-using-vs/_static/maas1.png)

* <span data-ttu-id="19f84-146">앱에 고유한 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-146">Name the app a unique name.</span></span> 

* <span data-ttu-id="19f84-147">구독을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-147">Select a subscription.</span></span>

* <span data-ttu-id="19f84-148">리소스 그룹에 대해 **새로 만들기...**를 선택하고 새 리소스 그룹에 대한 이름을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-148">Select **New...** for the resource group and enter a name for the new resource group.</span></span>

* <span data-ttu-id="19f84-149">App Service 계획에 대해 **새로 만들기...**를 선택하고 가까운 위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-149">Select **New...** for the app service plan and select a location near you.</span></span> <span data-ttu-id="19f84-150">기본적으로 생성되는 이름을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-150">You can keep the name that is generated by default.</span></span>

![App Service 대화 상자](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="19f84-152">**서비스** 탭을 선택하여 새 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-152">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="19f84-153">녹색 **+** 아이콘을 선택하여 새 SQL Database를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-153">Select the green **+** icon to create a new SQL Database</span></span>

![새 SQL Database](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="19f84-155">**SQL Database 구성** 대화 상자에서 **새로 만들기...**를 선택하여 새 데이터베이스 서버를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-155">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![새 SQL Database 및 서버](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="19f84-157">**SQL Server 구성** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-157">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="19f84-158">관리자 사용자 이름 및 암호를 입력한 다음 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-158">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="19f84-159">이 단계에서 만드는 사용자 이름 및 암호를 잊지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="19f84-159">Don't forget the user name and password you create in this step.</span></span> <span data-ttu-id="19f84-160">기본 **서버 이름**을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-160">You can keep the default **Server Name**.</span></span> 

* <span data-ttu-id="19f84-161">데이터베이스 및 연결 문자열에 대한 이름을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-161">Enter names for the database and connection string.</span></span>

> [!NOTE]
> <span data-ttu-id="19f84-162">"관리자"는 관리자 사용자 이름으로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-162">"admin" is not allowed as the administrator user name.</span></span>

![SQL Server 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="19f84-164">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-164">Select **OK**.</span></span>

<span data-ttu-id="19f84-165">Visual Studio가 **App Service 만들기** 대화 상자로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-165">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="19f84-166">**App Service 만들기** 대화 상자에서 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-166">Select **Create** on the **Create App Service** dialog.</span></span>

![SQL Database 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/conf_final.png)

* <span data-ttu-id="19f84-168">**게시** 대화 상자에서 **설정** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-168">Click the **Settings** link in the **Publish** dialog.</span></span>

![게시 대화 상자: 연결 패널](publish-to-azure-webapp-using-vs/_static/pubc.png)

<span data-ttu-id="19f84-170">**게시** 대화 상자의 **설정** 페이지에서:</span><span class="sxs-lookup"><span data-stu-id="19f84-170">On the **Settings** page of the **Publish** dialog:</span></span>

  * <span data-ttu-id="19f84-171">**데이터베이스**를 확장하고 **런타임 시 이 연결 문자열 사용**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-171">Expand **Databases** and check **Use this connection string at runtime**.</span></span>

  * <span data-ttu-id="19f84-172">**Entity Framework 마이그레이션**을 확장하고 **게시에 이 마이그레이션 적용**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-172">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="19f84-173">**저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-173">Select **Save**.</span></span> <span data-ttu-id="19f84-174">Visual Studio가 **게시** 대화 상자로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-174">Visual Studio returns to the **Publish** dialog.</span></span> 

![게시 대화 상자: 설정 패널](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="19f84-176">**게시**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-176">Click **Publish**.</span></span> <span data-ttu-id="19f84-177">Visual Studio는 앱을 Azure에 게시하고 브라우저에서 클라우드 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-177">Visual Studio will publish your app to Azure and launch the cloud app in your browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="19f84-178">Azure에서 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="19f84-178">Test your app in Azure</span></span>

* <span data-ttu-id="19f84-179">**정보** 및 **연락처** 링크 테스트</span><span class="sxs-lookup"><span data-stu-id="19f84-179">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="19f84-180">새 사용자 등록</span><span class="sxs-lookup"><span data-stu-id="19f84-180">Register a new user</span></span>

![Azure App Service의 Microsoft Edge에서 열린 웹 응용 프로그램](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="19f84-182">앱 업데이트</span><span class="sxs-lookup"><span data-stu-id="19f84-182">Update the app</span></span>

* <span data-ttu-id="19f84-183">*Pages/About.cshtml* Razor 페이지를 편집하고 내용을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-183">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="19f84-184">예를 들어 단락을 수정하여 “Hello ASP.NET Core!” 문구를 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-184">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="19f84-185">프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-185">Right-click on the project and select **Publish...** again.</span></span>

![강조 표시된 게시 링크로 열린 바로 가기 메뉴](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="19f84-187">앱이 게시된 후 변경 내용이 Azure에서 제공되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-187">After the app is published, verify the changes you made are available on Azure.</span></span>

![작업이 완료되었는지 확인합니다.](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="19f84-189">정리</span><span class="sxs-lookup"><span data-stu-id="19f84-189">Clean up</span></span>

<span data-ttu-id="19f84-190">앱 테스트를 완료하면 [Azure Portal](https://portal.azure.com/)로 이동하고 앱을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-190">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="19f84-191">**리소스 그룹**을 선택한 다음 만든 리소스 그룹을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-191">Select **Resource groups**, then select the resource group you created.</span></span>

![Azure Portal: 사이드바 메뉴에 있는 리소스 그룹](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="19f84-193">**리소스 그룹** 페이지에서 **삭제**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-193">In the **Resource groups** page, select **Delete**.</span></span>

![Azure Portal: 리소스 그룹 페이지](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="19f84-195">리소스 그룹의 이름을 입력하고 **삭제**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-195">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="19f84-196">이 자습서에서 만든 앱과 다른 모든 리소스는 이제 Azure에서 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="19f84-196">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="19f84-197">다음 단계</span><span class="sxs-lookup"><span data-stu-id="19f84-197">Next steps</span></span>

* [<span data-ttu-id="19f84-198">Visual Studio 및 Git을 사용하여 Azure에 연속 배포</span><span class="sxs-lookup"><span data-stu-id="19f84-198">Continuous Deployment to Azure with Visual Studio and Git</span></span>](../publishing/azure-continuous-deployment.md)

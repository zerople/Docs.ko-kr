---
title: "명령줄 도구를 사용하여 Azure에 ASP.NET Core 앱 게시 | Microsoft Docs"
description: "ASP.NET Core 및 Git 명령줄 클라이언트를 사용하여 Microsoft Azure 앱을 빌드하고 배포하는 방법을 알아봅니다."
services: multiple
keywords: "ASP.NET Core, Azure, App Service, Git, 명령줄"
author: camsoper
ms.author: casoper
manager: wpickett
ms.date: 11/03/2017
ms.topic: get-started-article
ms.prod: asp.net-core
ms.technology: aspnet
ms.custom: mvc
ms.devlang: dotnet
uid: tutorials/publish-to-azure-webapp-using-cli
ms.openlocfilehash: 0bcff4f79356b960f663dcebb1d79a108417dbd2
ms.sourcegitcommit: f017f940a164dbaf84307410c78eb14e0f3ac811
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-an-aspnet-core-application-to-azure-app-service-from-the-command-line"></a>명령줄에서 Azure App Service에 ASP.NET Core 응용 프로그램 배포

작성자: [Cam Soper](https://twitter.com/camsoper)

이 자습서에서는 명령줄 도구를 사용하여 Microsoft Azure App Service에 ASP.NET Core 응용 프로그램을 빌드하고 배포하는 방법을 보여줍니다.  완료되면 Azure App Service 웹앱으로 호스트된 ASP.NET MVC Core에서 웹 응용 프로그램이 빌드된 결과를 얻을 수 있습니다.  이 자습서는 Windows 명령줄 도구를 사용하여 작성되지만 macOS 및 Linux 환경에도 적용할 수 있습니다.  

이 자습서에서는 다음 방법을 학습합니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure App Service 웹 사이트 만들기
> * Git 명령줄 도구를 사용하여 ASP.NET Core 응용 프로그램을 Azure App Service 배포

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

* [Microsoft Azure 구독](https://azure.microsoft.com/free/)
* [.NET Core](https://www.microsoft.com/net/download/core)
* [Git](https://www.git-scm.com/) 명령줄 클라이언트

## <a name="create-a-web-application"></a>웹 응용 프로그램 만들기

웹 응용 프로그램의 새 디렉터리를 만들고 새 ASP.NET Core MVC 응용 프로그램을 만든 다음 웹 사이트를 로컬로 실행합니다.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
```cmd
REM Create a new ASP.NET Core MVC application
dotnet new razor -o MyApplication

REM Change to the new directory that was just created
cd MyApplication

REM Run the application
dotnet run
```

# <a name="othertabother"></a>[기타](#tab/other)
```bash
# Create a new ASP.NET Core MVC application
dotnet new razor -o MyApplication

# Change to the new directory that was just created
cd MyApplication

# Run the application
dotnet run
```
---

![명령줄 출력](publish-to-azure-webapp-using-cli/_static/new_prj.png)

http://localhost:5000으로 이동하여 응용 프로그램을 테스트합니다.

![로컬로 실행되는 웹 사이트](publish-to-azure-webapp-using-cli/_static/app_test.png)


## <a name="create-the-azure-app-service-instance"></a>Azure App Service 인스턴스 만들기

[Azure Cloud Shell](/azure/cloud-shell/quickstart)을 사용하여 리소스 그룹, App Service 계획 및 App Service 웹앱을 만듭니다.

```azurecli-interactive
# Generate a unique Web App name
let randomNum=$RANDOM*$RANDOM
webappname=tutorialApp$randomNum

# Create the DotNetAzureTutorial resource group
az group create --name DotNetAzureTutorial --location EastUS

# Create an App Service plan.
az appservice plan create --name $webappname --resource-group DotNetAzureTutorial --sku FREE

# Create the Web App
az webapp create --name $webappname --resource-group DotNetAzureTutorial --plan $webappname
```

배포 전에 다음 명령을 사용하여 계정 수준 배포 자격 증명을 설정합니다.

```azurecli-interactive
az webapp deployment user set --user-name <desired user name> --password <desired password>
```

Git을 사용하여 응용 프로그램을 배포하려면 배포 URL이 필요합니다.  다음과 같이 URL을 검색합니다.

```azurecli-interactive
az webapp deployment source config-local-git -n $webappname -g DotNetAzureTutorial --query [url] -o tsv
```
표시된 URL이 `.git`으로 끝나는 것을 알 수 있습니다. 다음 단계에서 사용됩니다.

## <a name="deploy-the-application-using-git"></a>Git을 사용하여 응용 프로그램 배포

Git을 사용하여 로컬 컴퓨터에서 배포할 준비가 되었습니다.

> [!NOTE]
> 줄 끝에 대한 Git 경고는 무시해도 됩니다.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
```cmd
REM Initialize the local Git repository
git init

REM Add the contents of the working directory to the repo
git add --all

REM Commit the changes to the local repo
git commit -a -m "Initial commit"

REM Add the URL as a Git remote repository
git remote add azure <THE GIT URL YOU NOTED EARLIER>

REM Push the local repository to the remote
git push azure master
```

# <a name="othertabother"></a>[기타](#tab/other)
```bash
# Initialize the local Git repository
git init

# Add the contents of the working directory to the repo
git add --all

# Commit the changes to the local repo
git commit -a -m "Initial commit"

# Add the URL as a Git remote repository
git remote add azure <THE GIT URL YOU NOTED EARLIER>

# Push the local repository to the remote
git push azure master
```
---

Git은 이전에 설정된 배포 자격 증명을 요구합니다.  인증 후에 응용 프로그램은 원격 위치로 푸시되고, 빌드되며 배포됩니다.

![Git 배포 출력](publish-to-azure-webapp-using-cli/_static/post_deploy.png)

## <a name="test-the-application"></a>응용 프로그램 테스트

`https://<web app name>.azurewebsites.net`으로 이동하여 응용 프로그램을 테스트합니다.  Cloud Shell(또는 Azure CLI)에 주소를 표시하려면 다음을 사용합니다.

```azurecli-interactive
az webapp show -n $webappname -g DotNetAzureTutorial --query defaultHostName -o tsv
```

![Azure에서 실행 중인 앱](publish-to-azure-webapp-using-cli/_static/app_deployed.png)

## <a name="clean-up"></a>정리

앱 테스트와 코드 및 리소스를 검사가 끝나면 리소스 그룹을 삭제하여 웹앱 및 계획을 삭제합니다.

```azurecli-interactive
az group delete -n DotNetAzureTutorial
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법을 학습했습니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure App Service 웹 사이트 만들기
> * Git 명령줄 도구를 사용하여 ASP.NET Core 응용 프로그램을 Azure App Service 배포

다음으로, 명령줄을 사용하여 CosmosDB를 사용하는 기존 웹앱을 배포하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [.NET Core를 사용하여 명령줄에서 Azure에 배포](/dotnet/azure/dotnet-quickstart-xplat)

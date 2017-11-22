---
title: "Visual Studio Tools for Docker 및 ASP.NET Core"
description: "이 문서에서는 Visual Studio 2017 도구 및 Windows용 Docker를 사용하여 ASP.NET Core 응용 프로그램 컨테이너화에 대해 살펴봅니다."
keywords: "Docker,ASP.NET Core,Visual Studio,컨테이너"
author: spboyer
ms.author: scaddie
manager: wpickett
ms.date: 09/26/2017
ms.topic: article
ms.prod: asp.net-core
ms.technology: aspnet
ms.assetid: 1f3b9a68-4dea-4b60-8cb3-f46164eedbbf
uid: publishing/vs-tools-for-docker
ms.openlocfilehash: 2d8e337141ae4e0d0258f1d7546510b0ab077e39
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="visual-studio-tools-for-docker"></a><span data-ttu-id="42a8b-104">Docker용 Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="42a8b-104">Visual Studio Tools for Docker</span></span>

<span data-ttu-id="42a8b-105">[Microsoft Visual Studio 2017](https://www.visualstudio.com/) 및 [Windows용 Docker](https://docs.docker.com/docker-for-windows/install/)는 Windows 및 Linux 컨테이너를 사용하여 .NET Framework 및 .NET Core 웹 응용 프로그램과 콘솔 응용 프로그램을 빌드, 디버그, 실행하도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-105">[Microsoft Visual Studio 2017](https://www.visualstudio.com/) with [Docker for Windows](https://docs.docker.com/docker-for-windows/install/) supports building, debugging, and running .NET Framework and .NET Core web and console applications using Windows and Linux containers.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="42a8b-106">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="42a8b-106">Prerequisites</span></span>

- <span data-ttu-id="42a8b-107">[Microsoft Visual Studio 2017](https://www.visualstudio.com/) 및 .NET Core 워크로드</span><span class="sxs-lookup"><span data-stu-id="42a8b-107">[Microsoft Visual Studio 2017](https://www.visualstudio.com/) with .NET Core workload</span></span>
- [<span data-ttu-id="42a8b-108">Windows용 Docker</span><span class="sxs-lookup"><span data-stu-id="42a8b-108">Docker for Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

## <a name="installation-and-setup"></a><span data-ttu-id="42a8b-109">설치 및 설정</span><span class="sxs-lookup"><span data-stu-id="42a8b-109">Installation and setup</span></span>

<span data-ttu-id="42a8b-110">[Microsoft Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 및 .NET Core 워크로드를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-110">Install [Microsoft Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) with the .NET Core workload.</span></span> <span data-ttu-id="42a8b-111">Visual Studio가 이미 설치되어 있는 경우 [Visual Studio 설치를 수정](https://docs.microsoft.com/visualstudio/install/modify-visual-studio)하여 .NET Core 워크로드를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-111">If you already have Visual Studio installed, you can [modify your Visual Studio installation](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) to add the .NET Core workload.</span></span>

<span data-ttu-id="42a8b-112">Docker 설치의 경우 [Windows용 Docker: 설치하기 전에 알아야 할 사항](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)의 정보를 검토하고 [Windows용 Docker](https://docs.docker.com/docker-for-windows/install/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-112">For Docker installation, review the information at [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) and install [Docker For Windows](https://docs.docker.com/docker-for-windows/install/).</span></span>

<span data-ttu-id="42a8b-113">필요한 구성은 Windows용 Docker에서 **[공유 드라이브](https://docs.docker.com/docker-for-windows/#shared-drives)**를 설정하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-113">A required configuration is to setup **[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker for Windows.</span></span> <span data-ttu-id="42a8b-114">이 설정은 볼륨 매핑 및 디버깅 지원을 위해 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-114">The setting is required for the volume mapping and debugging support.</span></span>

<span data-ttu-id="42a8b-115">시스템 트레이에서 Docker 아이콘을 마우스 오른쪽 단추로 클릭하고 **설정**을 클릭한 다음 **공유 드라이브**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-115">Right-click the Docker icon in the System Tray, click **Settings**, and select **Shared Drives**.</span></span> <span data-ttu-id="42a8b-116">Docker에서 파일을 저장할 드라이브를 선택하고 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-116">Select the drive where Docker will store your files and apply changes.</span></span>

![공유 드라이브](./visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

## <a name="create-an-aspnet-web-application-and-add-docker-support"></a><span data-ttu-id="42a8b-118">ASP.NET 웹 응용 프로그램을 만들고 Docker 지원 추가</span><span class="sxs-lookup"><span data-stu-id="42a8b-118">Create an ASP.NET Web Application and add Docker Support</span></span>

<span data-ttu-id="42a8b-119">Visual Studio를 사용하여 새로운 ASP.NET Core 웹 응용 프로그램을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-119">Using Visual Studio, create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="42a8b-120">응용 프로그램이 로드되면 **프로젝트 메뉴**에서 **Docker 지원 추가**를 선택하거나, 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **Docker 지원**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-120">When the application is loaded, either select **Add Docker Support** from the **Project Menu** or right-click the project from the Solution Explorer and select **Add** > **Docker Support**.</span></span>

<span data-ttu-id="42a8b-121">*프로젝트 메뉴*</span><span class="sxs-lookup"><span data-stu-id="42a8b-121">*Project Menu*</span></span>

![프로젝트 Docker 지원 추가](./visual-studio-tools-for-docker/_static/project-add-docker-support.png)

<span data-ttu-id="42a8b-123">*프로젝트 상황에 맞는 메뉴*</span><span class="sxs-lookup"><span data-stu-id="42a8b-123">*Project Context Menu*</span></span>

![[Docker 지원 추가]를 마우스 오른쪽 단추로 클릭](./visual-studio-tools-for-docker/_static/right-click-add-docker-support.png)

<span data-ttu-id="42a8b-125">프로젝트에 Docker 지원을 추가할 때 Windows 컨테이너 또는 Linux 컨테이너를 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-125">When you add Docker support to your project, you can choose either Windows or Linux containers.</span></span> <span data-ttu-id="42a8b-126">(Docker 호스트는 동일한 컨테이너 형식을 실행 중이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-126">(The Docker host must be running the same container type.</span></span> <span data-ttu-id="42a8b-127">실행 중인 Docker 인스턴스에서 컨테이너 형식을 변경해야 하는 경우 시스템 트레이의 **Docker** 아이콘을 마우스 오른쪽 단추로 클릭하고 **Windows 컨테이너로 전환** 또는 **Linux 컨테이너로 전환**을 선택합니다.)</span><span class="sxs-lookup"><span data-stu-id="42a8b-127">If you need change the container type in the running Docker instance, right-click the **Docker** icon in the System Tray, and choose **Switch to Windows containers** or **Switch to Linux containers**.)</span></span> 

<span data-ttu-id="42a8b-128">다음 파일이 프로젝트에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-128">The following files are added to the project:</span></span>

- <span data-ttu-id="42a8b-129">**Dockerfile**: ASP.NET Core 응용 프로그램용 Docker 파일은 [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore) 이미지를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-129">**Dockerfile**: the Docker file for ASP.NET Core applications is based on the [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore) image.</span></span> <span data-ttu-id="42a8b-130">이 이미지는 시작 성능을 개선하도록 prejit된 ASP.NET Core NuGet 패키지를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-130">This image includes the ASP.NET Core NuGet packages, which have been pre-jitted improving startup performance.</span></span> <span data-ttu-id="42a8b-131">.NET Core 콘솔 응용 프로그램을 빌드할 때 Dockerfile FROM은 가장 최근의 [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet) 이미지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-131">When building .NET Core Console Applications, the Dockerfile FROM will reference the most recent [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet) image.</span></span>   
- <span data-ttu-id="42a8b-132">**docker-compose.yml**: 빌드되어 docker-compose build/run으로 실행할 이미지 컬렉션을 정의하는 데 사용되는 기본 Docker Compose 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-132">**docker-compose.yml**: base Docker Compose file used to define the collection of images to be built and run with docker-compose build/run.</span></span>   
- <span data-ttu-id="42a8b-133">**docker compose.dev.debug.yml**: 디버그를 위해 구성을 설정할 때 반복되는 변경 사항에 사용할 추가 docker-compose 파일.</span><span class="sxs-lookup"><span data-stu-id="42a8b-133">**docker-compose.dev.debug.yml**: additional docker-compose file with for iterative changes when your configuration is set to debug.</span></span> <span data-ttu-id="42a8b-134">Visual Studio는 -f docker-compose.yml -f docker-compose.dev.debug.yml을 호출하여 이들을 병합합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-134">Visual Studio will call -f docker-compose.yml -f docker-compose.dev.debug.yml to merge these together.</span></span> <span data-ttu-id="42a8b-135">Visual Studio 개발 도구에서 이 compose 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-135">This compose file is used by Visual Studio development tools.</span></span>   
- <span data-ttu-id="42a8b-136">**docker compose.dev.release.yml**: 릴리스 정의를 디버그하기 위한 추가 Docker Compose 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-136">**docker-compose.dev.release.yml**: additional Docker Compose file to debug your release definition.</span></span> <span data-ttu-id="42a8b-137">프로덕션 이미지의 내용을 변경하지 않도록 디버거를 볼륨 탑재합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-137">It will volume mount the debugger so it doesn't change the contents of the production image.</span></span>  

<span data-ttu-id="42a8b-138">*docker-compose.yml* 파일에는 프로젝트를 실행할 때 생성되는 이미지의 이름이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-138">The *docker-compose.yml* file contains the name of the image that is created when project is run.</span></span> 

```
version '2'

services:
  hellodockertools:
    image:  user/hellodockertools${TAG}
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "80"
``` 

<span data-ttu-id="42a8b-139">이 예제에서 `image: user/hellodockertools${TAG}`는 응용 프로그램이 **디버그** 모드에서 실행될 때 이미지 `user/hellodockertools:dev`, **릴리스** 모드에서 실행될 때 `user/hellodockertools:latest`를 각각 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-139">In this example, `image: user/hellodockertools${TAG}` generates the image `user/hellodockertools:dev` when the application is run in **Debug** mode and `user/hellodockertools:latest` in **Release** mode respectively.</span></span> 

<span data-ttu-id="42a8b-140">레지스트리에 이미지를 푸시하려는 경우 `user`를 [Docker 허브](https://hub.docker.com/) 사용자 이름으로 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-140">You will want to change the `user` to your [Docker Hub](https://hub.docker.com/) username if you plan to push the image to the registry.</span></span> <span data-ttu-id="42a8b-141">예를 들어 구성에 따라 `spboyer/hellodockertools`로 변경하거나, 개인 레지스트리 URL `privateregistry.domain.com/`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-141">For example, `spboyer/hellodockertools`, or change to your private registry URL `privateregistry.domain.com/` depending on your configuration.</span></span>

### <a name="debugging"></a><span data-ttu-id="42a8b-142">디버깅</span><span class="sxs-lookup"><span data-stu-id="42a8b-142">Debugging</span></span>

<span data-ttu-id="42a8b-143">도구 모음의 디버그 드롭다운에서 **Docker**를 선택하고 F5 키를 눌러 응용 프로그램에서 디버깅을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-143">Select **Docker** from the debug drop-down in the toolbar and use F5 to start debugging the application.</span></span> 

- <span data-ttu-id="42a8b-144">*microsoft/aspnetcore* 이미지를 가져옵니다(캐시에 아직 없는 경우).</span><span class="sxs-lookup"><span data-stu-id="42a8b-144">The *microsoft/aspnetcore* image is acquired (if not already in your cache)</span></span>
- <span data-ttu-id="42a8b-145">*ASPNETCORE_ENVIRONMENT*가 컨테이너 내에서 Development로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-145">*ASPNETCORE_ENVIRONMENT* is set to Development within the container</span></span>
- <span data-ttu-id="42a8b-146">포트 80이 노출되고 동적으로 할당된 localhost용 포트에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-146">PORT 80 is EXPOSED and mapped to a dynamically assigned port for localhost.</span></span> <span data-ttu-id="42a8b-147">포트는 docker 호스트에 의해 결정되며 docker ps를 사용하여 쿼리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-147">The port is determined by the docker host and can be queried with docker ps.</span></span> 
- <span data-ttu-id="42a8b-148">응용 프로그램이 컨테이너에 복사됩니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-148">Your application is copied to the container</span></span>
- <span data-ttu-id="42a8b-149">컨테이너에 연결된 디버거와 함께 기본 브라우저가 시작되며 동적으로 할당된 포트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-149">Default browser is launched with the debugger attached to the container, using the dynamically assigned port.</span></span> 

<span data-ttu-id="42a8b-150">빌드된 결과 Docker 이미지는 응용 프로그램의 *dev* 이미지이며 *microsoft/aspnetcore* 이미지가 기본 이미지입니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-150">The resulting Docker image built is the *dev* image of your application with the *microsoft/aspnetcore* images as the base image.</span></span>

<span data-ttu-id="42a8b-151">**참고:** Debug 구성은 반복 환경을 제공하기 위해 탑재되는 볼륨을 사용하므로 dev 이미지에는 앱 콘텐츠가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-151">**Note:** The dev image is empty of your app contents as Debug configurations use volume mounting to provide the iterative experience.</span></span> <span data-ttu-id="42a8b-152">이미지를 푸시하려면 Release 구성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-152">To push an image, use the Release configuration.</span></span>

```console
REPOSITORY                  TAG         IMAGE ID            CREATED         SIZE
spboyer/hellodockertools    dev         0b6e2e44b3df        4 minutes ago   268.9 MB
microsoft/aspnetcore        1.0.1       189ad4312ce7        5 days ago      268.9 MB
```

<span data-ttu-id="42a8b-153">응용 프로그램은 `docker ps` 명령을 실행하여 볼 수 있는 컨테이너를 사용하여 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-153">The application is running using the container, which you can see by running the `docker ps` command.</span></span>

```console
CONTAINER ID        IMAGE                          COMMAND               CREATED             STATUS              PORTS                   NAMES
3f240cf686c9        spboyer/hellodockertools:dev   "tail -f /dev/null"   4 minutes ago       Up 4 minutes        0.0.0.0:32769->80/tcp   hellodockertools_hellodockertools_1
```

### <a name="edit-and-continue"></a><span data-ttu-id="42a8b-154">편집하며 계속하기</span><span class="sxs-lookup"><span data-stu-id="42a8b-154">Edit and Continue</span></span>

<span data-ttu-id="42a8b-155">정적 파일 및/또는 razor 템플릿 파일(*.cshtml*)에 대한 변경 사항은 자동으로 업데이트되므로 컴파일 단계가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-155">Changes to static files and/or razor template files (*.cshtml*) are automatically updated without the need of a compilation step.</span></span> <span data-ttu-id="42a8b-156">내용을 변경 및 저장하고 브라우저에서 새로 고침을 눌러 업데이트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-156">Make the change, save, and tap refresh in the browser to view the update.</span></span>  

<span data-ttu-id="42a8b-157">코드 파일을 수정하려면 컨테이너 내에서 컴파일하고 Kestrel을 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-157">Modifications to code files require compiling and a restart of Kestrel within the container.</span></span> <span data-ttu-id="42a8b-158">변경 후 컨테이너 내에서 CTRL + F5를 사용하여 프로세스를 수행하고 응용 프로그램을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-158">After making the change, use CTRL + F5 to perform the process and start the application within the container.</span></span> <span data-ttu-id="42a8b-159">Docker 컨테이너는 다시 빌드되거나 중지되지 않습니다. 명령줄에서 `docker ps`를 사용하면 원래 컨테이너가 10분 전을 기준으로 여전히 실행되고 있음을 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-159">The Docker container is not rebuilt or stopped; using `docker ps` in the command line, you can see that the original container is still running as of 10 minutes ago.</span></span> 

```console
CONTAINER ID        IMAGE                          COMMAND               CREATED             STATUS              PORTS                   NAMES
3f240cf686c9        spboyer/hellodockertools:dev   "tail -f /dev/null"   10 minutes ago      Up 10 minutes       0.0.0.0:32769->80/tcp   hellodockertools_hellodockertools_1
```

### <a name="publishing-docker-images"></a><span data-ttu-id="42a8b-160">Docker 이미지 게시</span><span class="sxs-lookup"><span data-stu-id="42a8b-160">Publishing Docker images</span></span>

<span data-ttu-id="42a8b-161">응용 프로그램의 개발 및 디버그 주기를 완료하면 Docker용 Visual Studio Tools를 이용해 응용 프로그램의 프로덕션 이미지를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-161">Once you have completed the develop and debug cycle of your application, the Visual Studio Tools for Docker will help you create the production image of your application.</span></span> <span data-ttu-id="42a8b-162">디버그 드롭다운을 **릴리스**로 변경하고 응용 프로그램을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-162">Change the debug dropdown to **Release** and build the application.</span></span> <span data-ttu-id="42a8b-163">도구에서 `:latest` 태그의 이미지를 생성하면 이를 개인 레지스트리 또는 Docker Hub에 푸시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-163">The tooling will produce the image with the `:latest` tag which you can push to your private registry or Docker Hub.</span></span> 

<span data-ttu-id="42a8b-164">`docker images` 명령을 사용하여 이미지의 목록을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-164">Using the `docker images` command, you can see the list of images.</span></span>

```console
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
spboyer/hellodockertools   latest              8184ae38ba91        5 seconds ago       278.4 MB
spboyer/hellodockertools   dev                 0b6e2e44b3df        About an hour ago   268.9 MB
microsoft/aspnetcore       1.0.1               189ad4312ce7        5 days ago          268.9 MB
```

<span data-ttu-id="42a8b-165">**dev** 이미지에 비해 프로덕션 또는 릴리스 이미지의 크기가 더 작을 수 있다고 예상할 수도 있지만, 볼륨 매핑을 사용하여 디버거와 응용 프로그램은 실제로 컨테이너 내에서가 아니라 로컬 컴퓨터에서 실행되었습니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-165">There may be an expectation for the production or release image to be smaller in size by comparison to the **dev** image; however, through the use of the volume mapping, the debugger and application were actually being run from your local machine and not within the container.</span></span> <span data-ttu-id="42a8b-166">**최신** 이미지는 호스트 컴퓨터에서 응용 프로그램을 실행하기 위해 필요한 전체 응용 프로그램 코드를 패키지했으므로, 델타가 응용 프로그램 코드의 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="42a8b-166">The **latest** image has packaged the entire application code needed to run the application on a host machine, therefore the delta is the size of your application code.</span></span>

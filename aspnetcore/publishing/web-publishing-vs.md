---
title: "Visual Studio 및 MSBuild에 대한 게시 프로필을 만들기"
author: rick-anderson
description: "Visual Studio의 웹 게시에 대해 설명합니다."
keywords: "ASP.NET Core, 웹 게시, 게시, msbuild, 웹 배포, dotnet publish, Visual Studio 2017"
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.assetid: 0377a02d-8fda-47a5-929a-24a16e1d2c93
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/web-publishing-vs
ms.openlocfilehash: 872e8c99734a0913770281d9d87b1e30c250ab0a
ms.sourcegitcommit: bd05f7ea8f87ad076ef6e8b704698ebcba5ca80c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2017
---
# <a name="create-publish-profiles-for-visual-studio-and-msbuild-to-deploy-aspnet-core-apps"></a>Visual Studio 및 MSBuild에 대한 게시 프로필을 만들어 ASP.NET Core 앱 배포

작성자: [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 문서에서는 Visual Studio 2017을 사용하여 게시 프로필을 만드는 방법에 초점을 맞춥니다. Visual Studio에서 만들어진 게시 프로필은 MSBuild 및 Visual Studio 2017에서 실행할 수 있습니다.

다음 *.csproj* 파일은 `dotnet new mvc` 명령을 사용하여 만들어졌습니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
  </ItemGroup>

  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
  </ItemGroup>

</Project>
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp1.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore" Version="1.1.1" />
    <PackageReference Include="Microsoft.AspNetCore.Mvc" Version="1.1.2" />
    <PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="1.1.1" />
  </ItemGroup>

</Project>
```

---

위 태그의 첫째 줄에 있는 `<Project>` 요소의 `Sdk` 특성은 다음을 수행합니다.

* 시작 시 *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props*에서 `props` 파일을 가져옵니다.
* 종료 시 *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*에서 대상 파일을 가져옵니다.

`MSBuildSDKsPath`의 기본 위치(Visual Studio 2017 Enterprise 사용)는 *%programfiles(x86)%\Microsoft Visual Studio\2017\Enterprise\MSBuild\Sdks* 폴더입니다.

`Microsoft.NET.Sdk.Web`은 다음에 종속됩니다.

* *Microsoft.NET.Sdk.Web.ProjectSystem*
* *Microsoft.NET.Sdk.Publish*

이러한 항목은 다음 `props` 및 `targets`를 가져옵니다.

* $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props
* $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets
* $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props
* $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets

게시 대상은 사용된 게시 방법에 따라 올바른 대상 집합을 다시 가져옵니다.

MSBuild 또는 Visual Studio가 프로젝트를 로드하면 다음 높은 수준의 작업이 수행됩니다.

* 프로젝트 빌드
* 게시할 파일 계산
* 대상에 파일 게시

### <a name="compute-project-items"></a>프로젝트 항목 계산

프로젝트가 로드되면 프로젝트 항목(파일)이 계산됩니다. `item type` 특성에 따라 파일 처리 방법이 결정됩니다. 기본적으로 *.cs* 파일은 `Compile` 항목 목록에 포함됩니다. `Compile` 항목 목록의 파일이 컴파일됩니다.

`Content` 항목 목록에는 빌드 출력 이외에 게시될 파일이 포함됩니다. 기본적으로 패턴 wwwroot/**와 일치하는 파일은 `Content` 항목에 포함됩니다. [와일드카드 사용 패턴인 wwwroot/**](https://gruntjs.com/configuring-tasks#globbing-patterns)는 *wwwroot* 폴더 **및** 하위 폴더에서 모든 파일을 지정합니다. 게시 목록에 파일을 명시적으로 추가해야 할 경우 [포함하는 파일](#including-files)에 표시된 대로 *.csproj* 파일에서 직접 파일을 추가할 수 있습니다.

Visual Studio에서 **게시** 단추를 선택할 경우 또는 명령줄에서 게시할 경우:

- 속성/항목이 계산됩니다(빌드하는 데 필요한 파일).
- Visual Studio에만 해당: NuGet 패키지가 복원됩니다.  (CLI에서 사용자가 명시적으로 복원해야 합니다.)
- 프로젝트가 빌드됩니다.
- 게시 항목이 계산됩니다(게시하는 데 필요한 파일).
- 프로젝트가 게시됩니다. 계산된 파일이 게시 대상에 복사됩니다.

## <a name="simple-command-line-publishing"></a>간단한 명령줄 게시

이 섹션은 모든 .NET Core 지원 플랫폼에 적용되며 Visual Studio가 필요하지 않습니다. 아래 샘플에서 `dotnet publish` 명령은 *.csproj* 파일이 포함된 프로젝트 디렉터리에서 실행됩니다. 프로젝트 폴더에 있지 않다면 프로젝트 파일 경로를 명시적으로 전달할 수 있습니다. 예:

```console
dotnet publish  c:/webs/web1
```

다음 명령을 실행하여 웹앱을 만들고 게시합니다.

```console
dotnet new mvc
dotnet restore
dotnet publish
```

`dotnet publish`는 다음과 비슷한 출력을 생성합니다.

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version 15.1.548.43366
Copyright (C) Microsoft Corporation. All rights reserved.

  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp1.1\Web1.dll
```

기본 게시 폴더는 `bin\$(Configuration)\netcoreapp<version>\publish`입니다. `$(Configuration)`의 기본값은 Debug입니다. 위의 샘플에서 `<TargetFramework>`는 `netcoreapp1.1`입니다. 위 샘플의 기본 경로는 *bin\Debug\netcoreapp1.1\publish*입니다.

`dotnet publish -h`는 게시에 대한 도움말 정보를 표시합니다.

다음 명령은 `Release` 빌드 및 게시 디렉터리를 지정합니다.

```console
dotnet publish -c Release -o C:/MyWebs/test
```

`dotnet publish` 명령은 `Publish` 대상을 호출하는 `MSBuild`를 호출합니다. `dotnet publish` 및 `MSBuild`에 전달된 모든 매개 변수. `-c` 매개 변수는 `Configuration` MSBuild 속성에 매핑됩니다. `-o` 매개 변수는 `OutputPath`에 매핑됩니다.

다음 형식 중 하나를 사용하여 MSBuild 속성을 전달할 수 있습니다.

- ` p:<NAME>=<VALUE>`
- `/p:<NAME>=<VALUE>`

다음 명령은 `Release` 빌드를 네트워크 공유에 게시합니다.

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

네트워크 공유는 슬래시(*//r8/*)를 사용하여 지정하고 모든 .NET Core 지원 플랫폼에서 작동합니다.

배포할 게시된 앱이 실행되고 있지 않은지 확인합니다. 앱이 실행 중이면 *publish* 폴더의 파일이 잠겨 있습니다. 잠긴 파일을 복사할 수 없으므로 배포를 수행할 수 없습니다.

## <a name="publish-profiles"></a>게시 프로필

이 섹션에서는 Visual Studio 2017 이상을 사용하여 게시 프로필을 만듭니다. 만들어진 후 Visual Studio 또는 명령줄에서 게시할 수 있습니다.

게시 프로필을 사용하면 게시 프로세스를 간소화할 수 있습니다. 여러 게시 프로필이 있을 수 있습니다. Visual Studio에서 게시 프로필을 만들려면 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. 또는 빌드 메뉴에서 **\<프로젝트 이름> 게시**를 선택할 수 있습니다. 응용 프로그램 기능 페이지의 **게시** 탭이 표시됩니다. 프로젝트에 게시 프로필이 없으면 다음 페이지가 표시됩니다.

![Azure, IIS, FTB, Azure가 선택된 폴더를 보여 주는 응용 프로그램 기능 페이지의 **게시** 탭. 또한 새로 만들기 및 종료 선택 라디오 단추가 표시됨](web-publishing-vs/_static/az.png)

**폴더**가 선택되면 **게시** 단추는 폴더 게시 프로필을 만들고 게시합니다.

![Azure, IIS, FTB, 폴더를 보여 주는 응용 프로그램 기능 페이지의 **게시** 탭](web-publishing-vs/_static/pub1.png)

게시 프로필이 만들어진 후 **게시** 탭이 변경되고 **새 프로필 만들기**를 선택하여 새 프로필을 만듭니다.

![마지막 단계에서 만들어진 FolderProfile 및 새 프로필 만들기 링크를 보여 주는 응용 프로그램 기능 페이지의 **게시** 탭](web-publishing-vs/_static/create_new.png)

게시 마법사는 다음 게시 대상을 지원합니다.

- Microsoft Azure App Service
- IIS, FTP 등(웹 서버의 경우)
- 폴더
- 프로필 가져오기(프로필을 가져올 수 있음).
- Microsoft Azure Virtual Machines

자세한 내용은 [내게 적합한 게시 옵션](https://docs.microsoft.com/visualstudio/ide/not-in-toc/web-publish-options)을 참조하세요.

Visual Studio를 사용하여 게시 프로필을 만들면 *Properties/PublishProfiles/\<publish name>.pubxml* MSBuild 파일이 만들어집니다. 이 *.pubxml* 파일은 MSBuild 파일이고 게시 구성 설정을 포함합니다. 이 파일을 변경하여 빌드 및 게시 프로세스를 사용자 지정할 수 있습니다. 게시 프로세스에서 이 파일을 읽습니다. `<LastUsedBuildConfiguration>`은 전역 속성이고 빌드에서 가져온 파일에 포함되면 안 되므로 특별합니다. 자세한 내용은 [MSBuild: 구성 속성을 설정하는 방법](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx)을 참조하세요.
*.pubxml* 파일은 *.user* 파일에 종속되므로 소스 제어에 체크 인되면 안 됩니다. *.user* 파일은 중요 정보를 포함할 수 있고 하나의 사용자 및 컴퓨터에 대해서만 유효하므로 소스 제어에 체크 인되면 안 됩니다.

중요 정보(예: 게시 암호)는 사용자/컴퓨터 수준별로 암호화되고 *Properties/PublishProfiles/\<publish name>.pubxml.user* 파일에 저장됩니다. 이 파일에는 중요 정보가 포함될 수 있으므로 소스 제어에 체크 인되면 **안 됩니다**.

ASP.NET Core에서 웹앱을 게시하는 방법에 대한 개요는 [게시 및 배포](index.md)를 참조하세요. [게시 및 배포](index.md)는 오픈 소스 프로젝트(https://github.com/aspnet/websdk)입니다.

현재 `dotnet publish`에는 게시 프로필을 사용하는 기능이 없습니다. 게시 프로필을 사용하려면 `dotnet build`를 사용합니다. `dotnet build`는 프로젝트에서 MSBuild를 호출합니다. 또는 `msbuild`를 직접 호출합니다.

게시 프로필을 사용할 경우 다음 MSBuild 속성을 설정합니다.

- `DeployOnBuild=true`
- `PublishProfile=<Publish profile name>`

예를 들어 이름이 *FolderProfile*인 프로필을 사용하여 게시할 경우 아래 명령 중 하나를 실행할 수 있습니다.

- `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
- `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

`dotnet build`를 호출하면 이 명령은 `msbuild`를 호출하여 빌드 및 게시 프로세스를 실행합니다. 폴더 프로필을 전달할 경우 `dotnet build` 또는 `msbuild` 호출은 근본적으로 동일합니다. Windows에서 MSBuild를 직접 호출하면 MSBuild의 .NET Framework 버전이 제공됩니다.  MSDeploy는 현재 게시를 위해 Windows 컴퓨터로 제한됩니다. 폴더가 아닌 프로필에서 `dotnet build`를 호출하면 MSBuild가 호출되고 MSBuild는 폴더가 아닌 프로필에서 MSDeploy를 사용합니다. 폴더가 아닌 프로필에서 `dotnet build`를 호출하면 MSBuild가 호출되고(MSDeploy 사용) Windows 플랫폼에서 실행될 경우에도 오류가 발생합니다. 폴더가 아닌 프로필을 사용하여 게시하려면 MSBuild를 직접 호출합니다.

다음 폴더 게시 프로필은 Visual Studio를 사용하여 만들어졌고 네트워크 공유에 게시됩니다.

[!code-xml[Main](web-publishing-vs/sample/FolderProfile.pubxml?highlight=5,9,11,18)]

`<LastUsedBuildConfiguration>`은 `Release`로 설정됩니다.  Visual Studio에서 게시할 경우 `<LastUsedBuildConfiguration>` 구성 속성 값은 게시 프로세스가 시작될 때의 값을 사용하여 설정됩니다. `<LastUsedBuildConfiguration>` 구성 속성은 특별하고 가져온 MSBuild 파일에서 재정의되면 안 됩니다. 명령줄에서 이 속성을 재정의할 수 있습니다. 예:

`dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

MSBuild 사용:

`msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>명령줄에서 MSDeploy 끝점에 게시

앞에서 설명한 대로 `dotnet publish` 또는 `msbuild` 명령을 사용하여 게시할 수 있습니다. `dotnet publish`는 .NET Core의 컨텍스트에서 실행됩니다. `msbuild`의 경우 .NET Framework가 필요하므로 Windows 환경으로 제한됩니다.

MSDeploy를 사용하여 게시하는 가장 좋은 방법은 먼저 Visual Studio 2017에서 게시 프로필을 만들고 명령줄에서 프로필을 사용하는 것입니다.

다음 샘플에서 ASP.NET Core 웹앱을 만들고(`dotnet new mvc` 사용) Visual Studio를 사용하여 Azure 게시 프로필을 추가했습니다.

**VS 2017에 대한 개발자 명령 프롬프트**에서 `msbuild`를 실행합니다. 개발자 명령 프롬프트에서는 해당 경로에 올바른 *msbuild.exe*가 있고 일부 MSBuild 변수를 설정합니다.

MSBuild는 다음 구문을 사용합니다.

`msbuild <path-to-project-file> /p:DeployOnBuild=true /p:PublishProfile=<Publish Profile>  /p:Username=<USERNAME> /p:Password=<PASSWORD>`

*\<Publish name>.PublishSettings* 파일에서 `Password`를 가져올 수 있습니다. 다음 위치에서 *.PublishSettings* 파일을 다운로드할 수 있습니다.

- 솔루션 탐색기: 웹앱을 마우스 오른쪽 단추로 클릭하고 **게시 프로필 다운로드**를 선택합니다.
- Azure 관리 포털: 웹앱 블레이드에서 **게시 프로필 가져오기**를 선택합니다.

`Username`은 게시 프로필에서 찾을 수 있습니다.

다음 샘플에서는 “Web11112 - Web Deploy” 게시 프로필을 사용합니다.

```console
msbuild "C:\Webs\Web1\Web1.csproj" /p:DeployOnBuild=true
 /p:PublishProfile="Web11112 - Web Deploy"  /p:Username="$Web11112"
 /p:Password="<password removed>"
```

## <a name="excluding-files"></a>파일 제외

ASP.NET Core 웹앱을 게시할 경우 *wwwroot* 폴더의 빌드 아티팩트 및 콘텐츠가 포함됩니다. `msbuild`는 [와일드카드 사용 패턴](https://gruntjs.com/configuring-tasks#globbing-patterns)을 지원합니다. 예를 들어 다음 `<Content>` 요소 태그는 *wwwroot/content* 폴더와 모든 하위 폴더에서 모든 텍스트(*.txt*) 파일을 제외합니다.

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

위의 태그는 게시 프로필 또는 *.csproj* 파일에 추가될 수 있습니다. *.csproj* 파일에 추가된 규칙은 프로젝트의 모든 게시 프로필에 추가됩니다.

다음 `<MsDeploySkipRules>` 요소 태그는 *wwwroot/content* 폴더에서 모든 파일을 제외합니다.

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>`는 배포 사이트에서 *건너뛰기* 대상을 삭제하지 않습니다. `<Content>` 대상 파일 및 폴더는 배포 사이트에서 삭제됩니다. 예를 들어 다음 파일을 사용하여 웹앱을 배포했다고 가정합니다.

- *Views/Home/About1.cshtml*
- *Views/Home/About2.cshtml*
- *Views/Home/About3.cshtml*

다음 `<MsDeploySkipRules>` 태그를 추가한 경우 해당 파일은 배포 사이트에서 삭제되지 않습니다.

``` xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

위에 표시된 `<MsDeploySkipRules>` 태그를 사용하면 *건너뛴* 파일이 배포되지 않지만 해당 파일이 배포된 후 파일이 삭제되지 않습니다.

다음 `<Content>` 태그는 배포 사이트에서 대상 파일을 삭제합니다.

``` xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

위의 `<Content>` 태그와 함께 명령줄 배포를 사용하면 다음과 비슷한 출력이 표시됩니다.

``` console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\netcoreapp1.1\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="including-files"></a>포함하는 파일

다음 태그를 사용하여 게시 사이트의 *wwwroot/images* 폴더에 대한 프로젝트 외부 디렉터리에 *images* 폴더를 포함할 수 있습니다.

``` xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

태그는 *.csproj* 파일 또는 게시 프로필에 추가될 수 있습니다. *.csproj* 파일에 추가된 경우 프로젝트의 각 게시 프로필에 포함됩니다.

다음 강조 표시된 태그는 방법을 표시합니다.

* 프로젝트 외부에서 *wwwroot* 폴더로 파일을 복사합니다.
* *wwwroot\Content* 폴더를 제외합니다.
* *Views\Home\About2.cshtml*을 제외합니다.

[!code-xml[Main](web-publishing-vs/sample/FolderProfile2.pubxml?highlight=21-29)]

더 많은 배포 샘플을 보려면 [WebSDK Readme](https://github.com/aspnet/websdk)(WebSDK 추가 정보)를 참조하세요.

### <a name="run-a-target-before-or-after-publishing"></a>게시 이전 또는 이후 대상 실행

기본 제공 `BeforePublish` 및 `AfterPublish` 대상을 사용하여 게시 대상 이전 또는 이후에 대상을 실행할 수 있습니다. 다음 태그를 게시 프로필에 추가하여 게시 이전 및 이후에 콘솔 출력에 메시지를 기록할 수 있습니다.

``` xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="the-kudu-service"></a>Kudu 서비스

Azure 웹앱에서 파일을 보려면 [kudu 서비스](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)를 사용합니다. 이름 또는 웹앱에 `scm` 토큰을 추가합니다. 예:

| URL               | 결과|
| ----------------- | ------------ |
| `http://mysite.azurewebsites.net/` | 웹앱 |
| `http://mysite.scm.azurewebsites.net/` | Kudu 서비스 |

[디버그 콘솔](https://github.com/projectkudu/kudu/wiki/Kudu-console) 메뉴 항목을 선택하여 파일을 표시/편집/삭제/추가합니다.

## <a name="additional-resources"></a>추가 리소스

- [웹 배포](https://www.iis.net/downloads/microsoft/web-deploy)(msdeploy)는 IIS 서버에 대한 웹 응용 프로그램 및 웹 사이트 배포를 간소화합니다.

- [https://github.com/aspnet/websdk](https://github.com/aspnet/websdk/issues): 배포에 대한 파일 문제 및 요청 기능.

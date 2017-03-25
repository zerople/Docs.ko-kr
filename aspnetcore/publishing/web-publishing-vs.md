---
title: "MSBuild 및 Visual Studio를 사용 하 여 게시 웹 | Microsoft 문서"
author: rick-anderson
description: "Visual Studio에서 웹 게시에 설명합니다."
keywords: "ASP.NET 핵심 웹 게시, 게시, msbuild, 웹 배포, Visual Studio 2017 dotnet 게시"
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.assetid: 0377a02d-8fda-47a5-929a-24a16e1d2c93
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/web-publishing-vs
translationtype: Machine Translation
ms.sourcegitcommit: 4347a7fba9bbafa44accc9d6df2ac4c760913f2e
ms.openlocfilehash: 8bf1af852cbef963263e947e5e041e85a88f42e4
ms.lasthandoff: 03/23/2017

---

# <a name="web-publishing-with-msbuild-and-visual-studio"></a>웹 MSBuild 및 Visual Studio 게시

여 [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 문서에서는 Visual Studio 2017를 사용 하 여 만드는 게시 프로필. MSBuild 및 Visual Studio 2017에서 Visual Studio를 사용 하 여 만든 게시 프로필을 실행할 수 있습니다.

다음 *.csproj* 명령을 사용 하 여 파일을 만든 `dotnet new mvc`:

``` xml
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

`Sdk` 특성에 `<Project>` 위의의 요소 (첫 번째 줄)에서 다음 작업을 수행 합니다.

* 가져오기는 `props` 파일에서 *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props* 시작 부분에 있습니다.
* 대상 파일을 가져오고 *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* 끝입니다.

기본 위치 `MSBuildSDKsPath` (Visual Studio 2017 Enterprise)는는 *%programfiles (x86) %\Microsoft 시각적 Studio\2017\Enterprise\MSBuild\Sdks* 폴더입니다.

`"Microsoft.NET.Sdk.Web"`에 따라 다릅니다.

* *Microsoft.NET.Sdk.Web.ProjectSystem*
* *Microsoft.NET.Sdk.Publish*

다음에 이르게 `props` 및 `targets` 를 가져올 수 있습니다.

* $ (MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props
* $ (MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets
* $ (MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props
* $ (MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets

목표는 다시 가져오기 올바른 대상 집합이 사용 되는 게시 방법에 따라 게시 합니다.

MSBuild 또는 Visual Studio 프로젝트를 로드할 때에 다음 높은 수준의 작업이 수행 됩니다.

* 프로젝트 빌드
* 게시 파일을 계산 합니다.
* 대상 파일을 게시 합니다.

### <a name="compute-project-items"></a>프로젝트 항목을 계산 합니다.

프로젝트 로드 되 면 프로젝트 항목 (파일)으로 계산 됩니다. `item type` 특성 파일을 처리 하는 방법을 결정 합니다. 기본적으로 *.cs* 에 포함 된 파일은 `Compile` 항목 목록입니다. 파일에 `Compile` 항목 목록 컴파일됩니다.

`Content` 항목 목록 파일이 포함 된 빌드 출력 외에도 게시 됩니다. 기본적으로 패턴 wwwroot 일치 하는 파일 /**에 포함 될 것은 `Content` 항목입니다. [wwwroot / * *는 와일드 카드 사용 패턴](https://gruntjs.com/configuring-tasks#globbing-patterns) 의 모든 파일을 지정 하는*wwwroot* 폴더 **및** 하위 폴더입니다. 명시적으로 게시 목록에 파일을 추가 하는 경우에에서 직접 파일을 추가할 수 있습니다는 *.csproj* 파일에 표시 된 대로 [파일 포함](#including-files)합니다.

선택 된 **게시** Visual Studio 또는 명령줄에서 게시 하는 경우에 단추:

- 속성/항목에서 계산 됩니다 (구축 하는 데 필요한 파일).
- Visual Studio 전용: NuGet 패키지 복원 됩니다.  (CLI에서 사용자가 명시적 요구를 복원 합니다.)
- 프로젝트를 빌드합니다.
- 계산 되 고 게시 항목 (게시 하는 데 필요한 파일).
- 프로젝트를 게시 합니다. (계산된 파일 게시 대상에 복사 됩니다.)

## <a name="simple-command-line-publishing"></a>간단한 명령줄 게시

이 섹션.NET Core 지원 되는 모든 플랫폼에서 작동 하며 Visual Studio가 필요 하지 않습니다. 아래 샘플에는 `dotnet publish` 프로젝트 디렉터리에서 명령을 실행 (포함 하는 *.csproj* 파일). 프로젝트 폴더에 아닌 경우에 프로젝트 파일 경로에 명시적으로 전달할 수 있습니다. 예:

```console
dotnet publish  c:/webs/web1
```

만들고 웹 응용 프로그램을 게시 하려면 다음 명령을 실행 합니다.

```console
dotnet new mvc
dotnet restore
dotnet publish
```

`dotnet publish` 다음과 유사한 출력을 생성 합니다.

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version 15.1.548.43366
Copyright (C) Microsoft Corporation. All rights reserved.

  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp1.1\Web1.dll
```

기본 폴더 게시는 `bin\$(Configuration)\netcoreapp<version>\publish`합니다. 에 대 한 기본 `$(Configuration)` 디버그 합니다. 위의 샘플에는 `<TargetFramework>` 는 `netcoreapp1.1`합니다. 위의 예제에서 실제 경로 *bin\Debug\netcoreapp1.1\publish*합니다.

`dotnet publish -h`도움말 게시에 대 한 정보를 표시 합니다.

다음 명령은 지정 된 `Release` 빌드 및 게시 디렉터리:

```console
dotnet publish -c Release -o C:/MyWebs/test
```

`dotnet publish` 호출 명령 `MSBuild` 를 호출 하는 `Publish` 대상입니다. 모든 매개 변수가 전달 `dotnet publish` 에 전달 됩니다 `MSBuild`합니다. `-c` 매개 변수에 매핑되는 `Configuration` MSBuild 속성입니다. `-o` 매개 변수에 매핑됩니다 `OutputPath`합니다.

MSBuild 속성을 다음 형식 중 하나를 사용 하 여 전달할 수 있습니다.

- ` p:<NAME>=<VALUE>`
- `/p:<NAME>=<VALUE>`

다음 명령은 게시는 `Release` 네트워크 공유를 작성 합니다.

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

네트워크 공유 슬래시로 지정 됩니다 (*//r8/*) 및.NET Core 지원 되는 모든 플랫폼에서 작동 합니다.

## <a name="publish-profiles"></a>게시 프로필

이 섹션에서는 게시 프로필을 만드는 2017 이상 Visual Studio를 사용 합니다. 를 만든 후에 Visual Studio 또는 명령줄에서 게시할 수 있습니다.

게시 프로필에서 게시 프로세스를 간소화할 수 있습니다. 여러 개의 게시 프로필을 사용할 수 있습니다. Visual Studio에서 게시 프로필을 만들려면 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **게시**합니다. 선택할 수 있습니다 **게시 \<프로젝트 이름 >** 빌드 메뉴에서 합니다. **게시** 응용 프로그램 용량 페이지의 탭이 표시 됩니다. 프로젝트에서 게시 프로필을 포함 되지 않으면 다음 페이지가 표시 됩니다.

![* * 게시 * * azure 폴더 선택한 Azure, IIS, FTB, 표시 응용 프로그램 용량 페이지의 탭 합니다. 또한 새로 만들기를 종료 라디오 단추를 선택 보여 줍니다.](web-publishing-vs/_static/az.png)

때 **폴더** 확인란이 선택 되는 **게시** 단추 폴더를 만듭니다 게시 프로필 및 게시 합니다.

![* * 게시 * * Azure, IIS, FTB, 폴더를 보여 주는 응용 프로그램 용량 페이지 탭](web-publishing-vs/_static/pub1.png)

게시 프로필을 만든 후의 **게시** 탭 변경 하 고 선택 **새 프로필을 만들** 새 프로필을 만듭니다.

![* * 게시 * * FolderProfile 보여 주는 응용 프로그램 용량 페이지의 탭-새 만들기 프로필 링크를 확인 하 고 마지막 단계에서 만든](web-publishing-vs/_static/create_new.png)

게시 마법사에서는 다음과 같은 게시 대상으로 지원합니다.

- Microsoft Azure 앱 서비스
- (모든 웹 서버)에 대 한 IIS, FTP 등
- 폴더
- 프로필 가져오기 (프로필을 가져올 수 있습니다).
- Microsoft Azure 가상 컴퓨터

참조 [어떤 게시 옵션을 내게 적합 한?](https://docs.microsoft.com/visualstudio/ide/not-in-toc/web-publish-options) 자세한 내용은 합니다.

Visual Studio와 함께 게시 프로필을 만들 때 한 *속성/PublishProfiles/\<게시 이름 >.pubxml* MSBuild 파일이 만들어집니다. 이 *.pubxml* 파일은 MSBuild 파일을 포함 하 고 게시 설정을 구성 합니다. 이 파일을 빌드 사용자 지정 및 게시 프로세스를 변경할 수 있습니다. 게시 프로세스는이 파일을 읽습니다. `<LastUsedBuildConfiguration>`되므로 특별 한 전역 속성 이며 빌드에 가져온 모든 파일에서이 아니어야 합니다. 참조 [MSBuild: 구성 속성을 설정 하는 방법을](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) 에 대 한 자세한 정보.
*.pubxml* 있기 때문에 파일을 소스 제어로 체크 수는 *.user* 파일입니다. *.user* 한 사용자와 컴퓨터에만 유효 하 고 중요 한 정보를 포함할 수 있습니다 파일 소스 제어에 체크 하지 않아야 합니다.

중요 한 정보 (예: 암호로 게시는) 암호화 되는 사용자/컴퓨터에 저장 하 고 수준 당는 *속성/PublishProfiles/\<게시 이름 >.pubxml.user* 파일입니다. 해야이 파일에서 중요 한 정보를 포함할 수 있으므로 **하지** 소스 제어에 체크 인할 수 있습니다.

ASP.NET Core에서 웹 응용 프로그램을 게시 하는 방법에 대 한 개요를 참조 하십시오. [게시 및 배포](index.md)합니다. [게시 및 배포](index.md) https://github.com/aspnet/websdk에서 오픈 소스 프로젝트입니다.

현재 `dotnet publish` 사용할 수 없는 게시 프로필. 사용 하 여 게시 프로필을 사용 하 여 `dotnet build`합니다. `dotnet build`프로젝트에서 MSBuild를 호출합니다. 또는 호출 `msbuild` 직접.

게시 프로필을 사용 하는 경우 다음 MSBuild 속성을 설정 합니다.

- `DeployOnBuild=true`
- `PublishProfile=<Publish profile name>`

예를 들어, 명명 된 프로필에 게시할 때 *FolderProfile* 아래 명령 중 하나를 실행할 수 있습니다.

- `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
- `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

호출 하는 경우 `dotnet build` 호출 `msbuild` 빌드를 실행 하 여 게시 프로세스입니다. 호출 `dotnet build` 또는 `msbuild` 폴더 프로필에 전달 하는 경우 기본적으로 같습니다. Windows에서 직접 MSBuild를 호출 하는 경우 전체.NET Framework 버전을의 MSBuild 가져옵니다.  MSDeploy 현재 게시에 대 한 Windows 컴퓨터로 제한 됩니다. 호출 `dotnet build` 비 폴더에서 MSBuild를 호출 하는 프로필 및 MSBuild MSDeploy를 사용 하 여 비 폴더 프로 파일에 있습니다. 호출 `dotnet build` 비 폴더 프로필 (MSDeploy 사용) 하는 MSBuild를 호출 하 고 (경우에 Windows 플랫폼에서 실행 됨)는 오류가 발생 합니다. 비 폴더 프로 파일을 게시 하려면 MSBuild를 직접 호출 합니다.

게시 프로필을 다음 폴더를 네트워크 공유에 게시 하 고 Visual Studio를 사용 하 여 만들어졌습니다.

[!code-xml[주](web-publishing-vs/sample/FolderProfile.pubxml?highlight=5,9,11,18)]

참고 `<LastUsedBuildConfiguration>` 로 설정 된 `Release`합니다.  Visual Studio에서 게시 하는 경우는 `<LastUsedBuildConfiguration>` 게시 프로세스가 시작 될 때 값을 사용 하 여 구성 속성 값을 설정 합니다. `<LastUsedBuildConfiguration>` 구성 속성은 특별 하 고 가져온된 MSBuild 파일에서 재정의할 수 없습니다. 명령줄에서이 속성을 재정의할 수 있습니다. 예:

`dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

MSBuild를 사용 합니다.

`msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>명령줄에서 MSDeploy 끝점에 게시

앞서 설명한 것 처럼를 사용 하 여 게시할 수 있습니다 `dotnet publish` 또는 `msbuild` 명령입니다. `dotnet publish`에 대 한.NET Core의 컨텍스트에서 실행 됩니다. `msbuild`전체.NET framework과 필요 하므로 Windows 환경으로 제한 합니다.

MSDeploy와 게시 하는 가장 쉬운 방법은 먼저 Visual Studio 2017에서 게시 프로필을 만드는 하 고 명령줄에서 프로필을 사용 하는 합니다.

ASP.NET 핵심 웹 응용 프로그램은 다음 예에서 만들었습니다 (사용 하 여 `dotnet new mvc`) Visual studio는 Azure 게시 프로필을 추가 합니다.

실행 하면 `msbuild` 에서 **VS 2017 용 개발자 명령 프롬프트**합니다. 개발자 명령 프롬프트 올바른 갖습니다 *msbuild.exe* 그 경로에 일부 MSBuild 변수를 설정 합니다.

MSBuild에서는 다음 구문을 사용합니다.

`msbuild <path-to-project-file> /p:DeployOnBuild=true /p:PublishProfile=<Publish Profile>  /p:Username=<USERNAME> /p:Password=<PASSWORD>`

가져올 수는 `Password` 에서 *\<게시 이름 >. PublishSettings* 파일입니다. 다운로드할 수는 *합니다. PublishSettings* 파일에서:

- 웹 앱에서 솔루션 탐색기를 마우스 오른쪽 단추로 클릭 하 고 선택 **게시 프로필 다운로드**합니다.
- Azure 관리 포털: 선택 **게시 프로필 가져오기** 웹 앱 블레이드에서 합니다.

`Username`게시 프로필에서 찾을 수 있습니다.

다음 샘플에서는 "Web11112-웹 배포" 게시 프로필:

```console
msbuild "C:\Webs\Web1\Web1.csproj" /p:DeployOnBuild=true
 /p:PublishProfile="Web11112 - Web Deploy"  /p:Username="$Web11112"
 /p:Password="<password removed>"
```

## <a name="excluding-files"></a>파일 제외

ASP.NET Core 웹 앱, 빌드 아티팩트 및의 콘텐츠를 게시 하는 경우는 *wwwroot* 폴더 포함 됩니다. `msbuild`지원 [와일드 카드 사용 패턴](https://gruntjs.com/configuring-tasks#globbing-patterns)합니다. 예를 들어, 다음 `<Content>` 요소 태그는 모든 텍스트를 제외 합니다 (*.txt*)에서 파일의 *wwwroot/콘텐츠* 폴더와 모든 하위 폴더입니다.

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

위의 게시 프로필에 추가할 수 또는 *.csproj* 파일입니다. 에 추가 하는 경우는 *.csproj* 파일이 규칙에 추가 됩니다. 프로젝트에서 모든 게시 프로필.

다음 `<MsDeploySkipRules>` 에서 파일을 모든 요소 태그는 제외는 *wwwroot/콘텐츠* 폴더:

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>`삭제 되지 것입니다는 *건너뛸* 배포 사이트에서 대상으로 합니다. `<Content>`대상된 파일 및 폴더 배포 사이트에서 삭제 됩니다. 예를 들어 다음 파일이 웹 앱을 배포 했을 가정 합니다.

- *Views/Home/About1.cshtml*
- *Views/Home/About2.cshtml*
- *Views/Home/About3.cshtml*

다음을 추가 하는 경우 `<MsDeploySkipRules>` 태그 배포 사이트에서 해당 파일은 삭제 되지 합니다.

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

`<MsDeploySkipRules>` 태그 위에 표시 된 것을 금지는 *건너뛴* 하지만 배포 된 후 파일을 해당 하는 삭제 하지 않습니다 depoyed 되는 파일입니다.

다음 `<Content>` 태그 배포 사이트에서 대상된 파일을 삭제 합니다.

``` xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

사용한 명령줄 배포를 사용 하는 `<Content>` 위의 결과적으로 다음과 유사한 출력 합니다.

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

## <a name="including-files"></a>파일을 포함 하 여

다음 태그는 포함 시키는 데 사용할 수는 *이미지* 프로젝트 디렉터리 외부 폴더는 *wwwroot/images* 게시 사이트의 폴더입니다.

``` xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

태그에 추가할 수는 *.csproj* 파일 또는 게시 프로필. 에 추가 되는 *.csproj* 파일을 프로젝트에 각 게시 프로필에 포함 됩니다.


### <a name="run-a-target-before-or-after-publishing"></a>이전 또는 게시 한 후 대상 실행

기본 제공 `BeforePublish` 및 `AfterPublish` 대상을 사용 하 여 게시 대상 전후 대상이 실행 수 있습니다. 다음 태그는 메시지를 기록 콘솔 출력에 전과 게시 한 후 게시 프로필에 추가할 수 있습니다.

``` xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="the-kudu-service"></a>Kudu 서비스

Azure 웹 앱에서 파일을 보려면 사용 하 여는 [kudu 서비스](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)합니다. 추가 `scm` 이름 또는 웹 앱에 토큰입니다. 예:

| URL               | 결과|
| ----------------- | ------------ |
| `http://mysite.azurewebsites.net/` | 웹 응용 프로그램 |
| `http://mysite.scm.azurewebsites.net/` | Kudu 서비스 |

선택 된 [디버그 콘솔](https://github.com/projectkudu/kudu/wiki/Kudu-console) 파일 보기/편집/삭제/추가 메뉴 항목입니다.

## <a name="additional-resources"></a>추가 리소스

- [웹 배포](https://www.iis.net/downloads/microsoft/web-deploy) (msdeploy)는 IIS 서버에 웹 응용 프로그램 및 웹 사이트의 배포를 간소화 합니다.

- [https://github.com/aspnet/websdk](https://github.com/aspnet/websdk/issues): 파일 문제 및 배포에 대 한 기능을 요청 합니다.

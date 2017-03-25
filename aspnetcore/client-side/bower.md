---
title: "ASP.NET Core에서 Bower를 사용 하 여 | Microsoft 문서"
author: rick-anderson
description: "Bower 사용 하 여 클라이언트 쪽 패키지를 관리 합니다."
keywords: ASP.NET Core, bower
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: df7c43da-280e-4df6-86cb-eecec8f12bfc
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/bower
ms.custom: H1Hack27Feb2017
translationtype: Machine Translation
ms.sourcegitcommit: 4347a7fba9bbafa44accc9d6df2ac4c760913f2e
ms.openlocfilehash: 40db55cb63a4f863654b17b5b2859506c59450a1
ms.lasthandoff: 03/23/2017

---
# <a name="manage-client-side-packages-with-bower-in-aspnet-core"></a>ASP.NET Core에서 Bower와 함께 클라이언트 쪽 패키지 관리

여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Noel 밥](http://blog.falafel.com/author/noel-rice/), 및 [Scott Addie](https://scottaddie.com) 

[Bower](https://bower.io/) 자체 "패키지에 대 한 관리자 웹."를 호출 합니다. .NET 에코 시스템 내에서 정적 콘텐츠 파일을 제공 하도록 NuGet의 불가능에 남은 공간을 채웁니다. ASP.NET Core 프로젝트에 대 한 이러한 정적 파일은 클라이언트 쪽 라이브러리와 같은 기본적인 [jQuery](http://jquery.com/) 및 [부트스트랩](http://getbootstrap.com/)합니다. .NET 라이브러리에 대해 계속 사용할 [NuGet](https://nuget.org/) 패키지 관리자입니다.

클라이언트 쪽 설정 ASP.NET 핵심 프로젝트 템플릿을 사용 하 여 만든 새 프로젝트 빌드 프로세스입니다. [jQuery](http://jquery.com/) 및 [부트스트랩](http://getbootstrap.com/) 설치 된 Bower 지원 됩니다.

클라이언트 쪽 패키지가에 나열 되는 *bower.json* 파일입니다. ASP.NET Core 프로젝트 템플릿 구성 *bower.json* Bootstrap, jQuery 및 jQuery 유효성 검사와 합니다.

이 자습서에서는 대 한 지원을 추가 합니다 [글꼴 Awesome](http://fontawesome.io)합니다. Bower 패키지를 함께 설치할 수 있는 **Bower 패키지 관리** UI에서이 수동으로 또는 *bower.json* 파일입니다.

### <a name="installation-via-manage-bower-packages-ui"></a>Bower 패키지 관리 UI 통해 설치

* 와 새 ASP.NET 핵심 웹 앱 만들기는 **ASP.NET 핵심 웹 응용 프로그램 (.NET Core)** 템플릿. 선택 **웹 응용 프로그램** 및 **인증 없음**합니다.

* 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **Bower 패키지 관리** (또는 주 메뉴에서 **프로젝트** > **Bower 패키지 관리**).

* 에 **Bower: \<프로젝트 이름\>** 창에서 "찾아보기" 탭을 클릭 한 다음을 입력 하 여 패키지 목록 필터링 `font-awesome` 검색 상자에:

 ![bower 패키지 관리](bower/_static/manage-bower-packages.png)

* 확인 하는 "변경 내용을 저장 하 *bower.json*" 확인란을 선택 합니다. 드롭다운 목록에서 버전을 선택 하 고 클릭 하 고 **설치** 단추입니다. **출력** 설치 세부 정보 창에 표시 합니다.

### <a name="manual-installation-in-bowerjson"></a>Bower.json에 수동 설치

열기는 *bower.json* 파일 하 고 "글꼴 awesome" 종속성을 추가 합니다. IntelliSense 사용 가능한 패키지를 표시합니다. 패키지를 선택 하면 사용 가능한 버전이 표시 됩니다. 아래 이미지에서 오래 되었고 보이는 것과 일치 하지 않습니다.

![Bower 패키지 탐색기의 IntelliSense](bower/_static/add-package.png)

![bower 버전 IntelliSense](bower/_static/version-IntelliSense.png)

사용 하 여 bower [의미 체계 버전 관리](http://semver.org/) 종속성을 구성할 수 있습니다. 번호 매기기 체계 사용 하 여 패키지를 식별 하는 의미 체계 버전 관리 라고도 SemVer \<주요 >.\< 부 버전 >. \<패치 >. IntelliSense만 몇 가지 일반적인 옵션을 표시 하 여 의미 체계 버전 관리를 간소화 합니다. IntelliSense 목록 (위 예제에서&4;.6.3)에서 맨 위 항목에는 패키지의 안정적인 최신 버전으로 간주 됩니다. 캐럿 (^) 기호가 일치 하는 가장 최근의 주 버전 및 물결표 (~) 최신 부 버전 일치 합니다.

저장 된 *bower.json* 파일입니다. Visual Studio를 감시는 *bower.json* 변경에 대 한 파일입니다. 저장할 때는 *bower 설치* 명령을 실행 합니다. 출력 창을 참조 **Bower/npm** 실행 되는 정확한 명령에 대 한 보기입니다.

열기는 *.bowerrc* 아래 파일 *bower.json*합니다. `directory` 속성이 *wwwroot/lib* Bower 위치를 나타내는 패키지 자산을 설치 합니다.

```json
{
 "directory": "wwwroot/lib"
}
```

찾아 글꼴 awesome 패키지를 표시 하려면 솔루션 탐색기에서 검색 상자를 사용할 수 있습니다.

열기는 *Views\Shared\_Layout.cshtml* 파일 및 환경에 글꼴 awesome CSS 파일을 추가할 [태그 도우미](xref:mvc/views/tag-helpers/intro) 에 대 한 `Development`합니다. 솔루션 탐색기에서 끌어서 놓기 *글꼴 awesome.css* 내는 `<environment names="Development">` 요소입니다.

[!code-html[주](bower\sample\_Layout.cshtml?highlight=4&range=9-13)]

프로덕션 응용 프로그램에 추가 *글꼴 awesome.min.css* 에 대 한 환경 태그 도우미를 `Staging,Production`합니다.

내용을 대체는 *Views\Home\About.cshtml* Razor 파일의 다음 태그를 사용 합니다.

[!code-html[주](bower\sample\About.cshtml)]

앱을 실행 하 고 글꼴 awesome 패키지 작동 하는지 확인 하는 정보 보기를 이동 합니다.

## <a name="exploring-the-client-side-build-process"></a>클라이언트 쪽 빌드 프로세스를 살펴보기

대부분의 ASP.NET 핵심 프로젝트 템플릿은 Bower를 사용 하 여 구성 됩니다. 다음이 연습에서는 빈 ASP.NET 핵심 프로젝트 시작 하 고 각 부분을 수동으로 추가 되므로 Bower 프로젝트에서 사용 방법에 대 한 느낌을 얻을 수 있습니다. 프로젝트 구조를 어떻게 수 및 각 구성 변경으로 출력 런타임 이루어집니다 표시 됩니다.

Bower와 함께 클라이언트 쪽 빌드 프로세스를 사용 하는 일반적인 단계는.

* 프로젝트에 사용 되는 패키지를 정의 합니다. <!-- once defined, you don't need to download them, VS does -->
* 웹 페이지에서 패키지를 참조 합니다.

### <a name="define-packages"></a>패키지를 정의 합니다.

패키지를 나열 되 면는 *bower.json* 파일을 Visual Studio 다운로드 됩니다. 다음 예제에서는 Bower를 사용 하 여 변경 내용을 jQuery 및 Bootstrap을 로드 하는 *wwwroot* 폴더입니다.

* 와 새 ASP.NET 핵심 웹 앱 만들기는 **ASP.NET 핵심 웹 응용 프로그램 (.NET Core)** 템플릿. 선택 된 **빈** 프로젝트 템플릿과 클릭 **확인**합니다.

* 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 > **새 항목 추가** 선택한 **Bower 구성 파일**합니다. 참고: A *.bowerrc* 파일도 추가 됩니다.

* 열기 *bower.json*, jquery를 추가 하 고 영역에 부트스트랩는 `dependencies` 섹션입니다. 그 결과 *bower.json* 파일은 다음 예제와 같이 표시 됩니다. 버전은 시간이 지남에 따라 변경 되 고 아래 이미지와 일치 하지 않을 수 있습니다.

[!code-json[주](bower\sample\bower.json?highlight=5,6)]

* 저장 된 *bower.json* 파일입니다.

 프로젝트에 포함 되어는 *부트스트랩* 및 *jQuery* 디렉터리 *wwwroot/lib*합니다. 사용 하 여 bower는 *.bowerrc* 에서 자산을 설치 하는 파일 *wwwroot/lib*합니다.

 참고: "Bower 패키지 관리" UI 수동 파일 편집에 대 한 대안을 제공합니다.

### <a name="enable-static-files"></a>정적 파일을 사용 하도록 설정

* 추가 된 `Microsoft.AspNetCore.StaticFiles` 프로젝트에 NuGet 패키지입니다.
* 으로 처리할 수 있는 정적 파일을 사용 하도록 설정 된 [정적 파일 미들웨어](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.builder.staticfileextensions) 미들웨어입니다. 호출을 추가 하면 [UseStaticFiles](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.builder.staticfileextensions) 에 `Configure` 메서드의 `Startup`합니다.

[!code-csharp[주](bower\sample\Startup.cs?highlight=9)]

### <a name="reference-packages"></a>참조 패키지

이 섹션에서는 배포 된 패키지에 액세스할 수를 확인 하는 HTML 페이지를 만들게 됩니다.

* 라는 새 HTML 페이지를 추가 *Index.html* 에 *wwwroot* 폴더입니다. 참고: HTML 파일을 추가 해야 하는 *wwwroot* 폴더입니다. 기본적으로 정적 콘텐츠 제공 될 수 없는 외부 *wwwroot*합니다. 참조 [정적 파일 작업](xref:fundamentals/static-files) 에 대 한 자세한 내용은 합니다.

 그런 후 *Index.html* 다음 태그를 사용 합니다.

[!code-html[주](bower\sample\Index.html)]

* 응용 프로그램을 실행 하 고 이동 `http://localhost:<port>/Index.html`합니다. 또는와 *Index.html* 열, 키를 눌러 `Ctrl+Shift+W`합니다. Jumbotron 스타일이 적용 되는, 확인 단추를 클릭할 때 응답 하는 jQuery 코드로 부트스트랩 단추 상태를 변경 합니다.

 ![jumbotron 스타일 적용](bower/_static/jumbotron.png)


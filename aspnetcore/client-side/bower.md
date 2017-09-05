---
title: "ASP.NET Core에서 Bower를 사용 하 여"
author: rick-anderson
description: "Bower 사용 하 여 클라이언트 패키지를 관리 합니다."
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
ms.openlocfilehash: 409f7afba8dd7d03b7b9aa27d93ec9167252b965
ms.sourcegitcommit: 4e84d8bf5f404bb77f3d41665cf7e7374fc39142
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2017
---
# <a name="manage-client-side-packages-with-bower-in-aspnet-core"></a>ASP.NET Core에서 Bower 사용 하 여 클라이언트 패키지를 관리 합니다.

여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Noel 밥](http://blog.falafel.com/author/noel-rice/), 및 [Scott Addie](https://scottaddie.com) 

[Bower](https://bower.io/) "패키지에 대 한 관리자는 웹." 자신을 호출 .NET 환경 내에서 NuGet의 불가능 정적 콘텐츠 파일을 전달 하 여 남아 있는 공간을 채웁니다. ASP.NET Core 프로젝트에 대 한 이러한 정적 파일은과 같은 클라이언트 쪽 라이브러리에 따르는 [jQuery](http://jquery.com/) 및 [부트스트랩](http://getbootstrap.com/)합니다. .NET 라이브러리에 대 한 계속 사용할 [NuGet](https://nuget.org/) 패키지 관리자.

클라이언트 쪽 설정에서 ASP.NET Core 프로젝트 템플릿을 사용 하 여 만든 새 프로젝트 빌드 프로세스입니다. [jQuery](http://jquery.com/) 및 [부트스트랩](http://getbootstrap.com/) 설치 되 면 Bower 지원 됩니다.

클라이언트 패키지에 나열 된는 *bower.json* 파일입니다. ASP.NET Core 프로젝트 템플릿 구성 *bower.json* 부트스트랩, jQuery 및 jQuery 유효성 검사와 합니다.

이 자습서에 대 한 지원을 추가 [글꼴 놀라운](http://fontawesome.io)합니다. Bower 패키지를 함께 설치할 수 있습니다는 **Bower 패키지 관리** UI에 수동으로 또는 *bower.json* 파일입니다.

### <a name="installation-via-manage-bower-packages-ui"></a>Bower 패키지 관리 UI 통해 설치

* 새 ASP.NET Core 웹 앱을 만들기는 **ASP.NET Core 웹 응용 프로그램 (.NET Core)** 서식 파일입니다. 선택 **웹 응용 프로그램** 및 **인증 안 함**합니다.

* 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **Bower 패키지 관리** (또는 주 메뉴에서 **프로젝트** > **Bower 패키지 관리**).

* 에 **Bower: \<프로젝트 이름\>**  창 "찾아보기" 탭을 클릭 한 다음를 입력 하 여 패키지 목록 필터링 `font-awesome` 검색 상자에:

 ![bower 패키지 관리](bower/_static/manage-bower-packages.png)

* 확인 하는 "변경 내용을 저장 하 *bower.json*" 확인란이 선택 되어 있습니다. 드롭다운 목록에서 버전을 선택 하 고 클릭는 **설치** 단추입니다. **출력** 설치 세부 정보 창에 표시 합니다.

### <a name="manual-installation-in-bowerjson"></a>Bower.json에 수동 설치

열기는 *bower.json* 파일 및 종속성에 "글꼴 놀라운"를 추가 합니다. IntelliSense 사용 가능한 패키지를 표시합니다. 패키지를 선택 하면 사용 가능한 버전 표시 됩니다. 아래 이미지 오래 되었고 나타나는 일치 하지 않습니다.

![Bower 패키지 탐색기의 IntelliSense](bower/_static/add-package.png)

![bower 버전 IntelliSense](bower/_static/version-intelliSense.png)

사용 하 여 bower [의미 체계 버전 관리](http://semver.org/) 종속성을 구성할 수 있습니다. 의미 체계 버전 관리로도 알려져 SemVer 번호 매기기 구성표를 사용 하 여 패키지를 식별 \<주요 >.\< 사소한 > 합니다. \<패치 > 합니다. IntelliSense는 일반적인 몇 가지 옵션에만 표시 하 여 의미 체계 버전 관리를 간소화 합니다. 맨 위 항목 (위의 예에서 4.6.3) IntelliSense 목록에는 패키지의 안정적인 최신 버전으로 간주 됩니다. 캐럿 (^) 기호가 일치 하는 가장 최근의 주 버전 및 물결표 (~) 최신 부 버전 일치 합니다.

저장 된 *bower.json* 파일입니다. Visual Studio를 감시는 *bower.json* 파일에서 변경 내용 합니다. 저장할 때는 *bower 설치* 명령을 실행 합니다. 출력 창을 참조 **npmBower/** 실행 되는 정확한 명령에 대 한 보기입니다.

열기는 *.bowerrc* 아래 파일 *bower.json*합니다. `directory` 속성이 *wwwroot/lib* Bower 위치를 나타내는 패키지 자산을 설치 합니다.

```json
{
 "directory": "wwwroot/lib"
}
```

찾아 글꼴 놀라운 패키지를 표시 합니다. 솔루션 탐색기에서 검색 상자를 사용할 수 있습니다.

열기는 *Views\Shared\_Layout.cshtml* 파일 환경에 놀라운 글꼴 CSS 파일을 추가 및 [태그 도우미](xref:mvc/views/tag-helpers/intro) 에 대 한 `Development`합니다. 솔루션 탐색기에서 끌어서 놓기 *글꼴 awesome.css* 내에서 `<environment names="Development">` 요소입니다.

[!code-html[Main](bower/sample/_Layout.cshtml?highlight=4&range=9-13)]

프로덕션 응용 프로그램에 추가 *글꼴 awesome.min.css* 에 대 한 환경 태그 도우미를 `Staging,Production`합니다.

내용을 대체는 *Views\Home\About.cshtml* 다음 태그로 Razor 파일:

[!code-html[Main](bower/sample/About.cshtml)]

응용 프로그램을 실행 하 고 글꼴 놀라운 패키지 작동 하는지 확인 하는 정보 보기를 탐색 합니다.

## <a name="exploring-the-client-side-build-process"></a>클라이언트 쪽 빌드 프로세스를 탐색합니다.

대부분의 ASP.NET Core 프로젝트 템플릿은 Bower를 사용 하도록 구성 됩니다. 다음이 연습에서는 빈 ASP.NET Core 프로젝트로 시작 되며 각 부분을 수동으로 추가 되므로 Bower 프로젝트에서 사용 방법에 대 한 지 파악할 수 있습니다. 프로젝트 구조에 수행 되는 작업 수와 각 구성 변경으로 출력 런타임 만들어지는 볼 있습니다.

Bower 클라이언트 쪽 빌드 프로세스를 사용 하는 일반적인 단계는

* 프로젝트에 사용 되는 패키지를 정의 합니다. <!-- once defined, you don't need to download them, VS does -->
* 웹 페이지에서 패키지를 참조 합니다.

### <a name="define-packages"></a>패키지를 정의 합니다.

패키지를 나열 되 면는 *bower.json* 파일을 Visual Studio 다운로드 됩니다. 다음 예제에서는 Bower를 사용 하 여 jQuery 및 부트스트랩을 로드할 수는 *wwwroot* 폴더입니다.

* 새 ASP.NET Core 웹 앱을 만들기는 **ASP.NET Core 웹 응용 프로그램 (.NET Core)** 서식 파일입니다. 선택 된 **빈** 프로젝트 템플릿과 클릭 **확인**합니다.

* 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 > **새 항목 추가** 선택 **Bower 구성 파일**합니다. 참고: A *.bowerrc* 파일도 추가 됩니다.

* 열기 *bower.json*, jquery를 추가 하 고 영역에 부트스트랩는 `dependencies` 섹션. 그 결과 *bower.json* 파일은 다음 예제와 같습니다. 버전은 시간이 지남에 따라 변경 되 고 아래 이미지 일치 하지 않을 수 있습니다.

[!code-json[Main](bower/sample/bower.json?highlight=5,6)]

* 저장 된 *bower.json* 파일입니다.

 프로젝트에 포함 되어는 *부트스트랩* 및 *jQuery* 디렉터리에 *wwwroot/lib*합니다. 사용 하 여 bower는 *.bowerrc* 파일을 자산에 설치 하려면 *wwwroot/lib*합니다.

 참고: "Bower 패키지 관리" UI 수동 파일 편집에 대 한 대안을 제공합니다.

### <a name="enable-static-files"></a>정적 파일을 사용 하도록 설정

* 추가 `Microsoft.AspNetCore.StaticFiles` NuGet 패키지를 프로젝트입니다.
* 와 제공에 정적 파일을 사용 하도록 설정 된 [정적 파일 미들웨어](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.staticfileextensions)합니다. 에 대 한 호출 추가 [UseStaticFiles](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.staticfileextensions) 에 `Configure` 메서드 `Startup`합니다.

[!code-csharp[Main](bower/sample/Startup.cs?highlight=9)]

### <a name="reference-packages"></a>참조 패키지

이 섹션에서는 배포 된 패키지에 액세스할 수를 확인 하는 HTML 페이지를 만들게 됩니다.

* 라는 새 HTML 페이지를 추가 *Index.html* 에 *wwwroot* 폴더입니다. 참고: HTML 파일을 추가 해야는 *wwwroot* 폴더입니다. 기본적으로 정적 콘텐츠가 담긴 제공 될 수 없는 외부 *wwwroot*합니다. 참조 [정적 파일 작업](xref:fundamentals/static-files) 자세한 정보에 대 한 합니다.

 내용을 대체 *Index.html* 다음 태그로:

[!code-html[Main](bower/sample/Index.html)]

* 응용 프로그램을 실행 하 고 이동 `http://localhost:<port>/Index.html`합니다. 또는와 *Index.html* 열, 키를 눌러 `Ctrl+Shift+W`합니다. Jumbotron 스타일이 적용 되, jQuery 코드는 단추를 클릭할 때 응답 및 부트스트랩 단추 상태가 변경 되는지 확인 합니다.

 ![jumbotron 스타일 적용](bower/_static/jumbotron.png)

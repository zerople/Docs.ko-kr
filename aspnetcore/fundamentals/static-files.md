---
title: "ASP.NET Core에 정적 파일 작업"
author: rick-anderson
description: "ASP.NET Core에 정적 파일을 사용 하는 방법을 알아봅니다."
keywords: "ASP.NET Core, 정적 파일, 정적 자산, HTML, CSS, JavaScript"
ms.author: riande
manager: wpickett
ms.date: 4/07/2017
ms.topic: article
ms.assetid: e32245c7-4eee-4831-bd2e-915dbf9f5f70
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/static-files
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e099c4767958f153134e0fb6b3de8132ab1ead82
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2017
---
# <a name="working-with-static-files-in-aspnet-core"></a>ASP.NET Core에 정적 파일 작업

<a name=fundamentals-static-files></a>

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

HTML, CSS, 이미지 및 JavaScript와 같은 정적 파일은 ASP.NET Core 응용 프로그램을 클라이언트에 직접 제공할 수 있는 자산입니다.

[보거나 다운로드 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/static-files/sample) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample))

## <a name="serving-static-files"></a>정적 파일 처리

정적 파일은 일반적으로 %programfiles%\microsoft는 `web root` (*\<콘텐츠 루트 > / wwwroot*) 폴더입니다. 참조 [콘텐츠 루트](xref:fundamentals/index#content-root) 및 [웹 루트](xref:fundamentals/index#web-root) 자세한 정보에 대 한 합니다. 콘텐츠 루트 현재 디렉터리를 일반적으로 설정 되도록 프로젝트의 `web root` 개발에서 찾을 수 있습니다.

[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=5&start=12&end=22)]

아래의 모든 폴더에 정적 파일을 저장할 수 있습니다는 `web root` 해당 루트의 상대 경로 사용 하 여 액세스 하 고 있습니다. 예를 들어 Visual Studio를 사용 하 여 기본 웹 응용 프로그램 프로젝트를 만들 때 여러 폴더가 있는 내에서 만든는 *wwwroot* 폴더- *css*, *이미지*, 및 *js*합니다. URI에 있는 이미지에 액세스 하기는 *이미지* 하위 폴더:

* `http://<app>/images/<imageFileName>`
* `http://localhost:9189/images/banner3.svg`

정적 파일 처리에 대 한 순서 대로 구성 해야는 [미들웨어](middleware.md) 파이프라인에 정적 파일을 추가 합니다. 정적 파일 미들웨어에서 종속성을 추가 하 여 구성할 수 있습니다는 *Microsoft.AspNetCore.StaticFiles* 패키지를 프로젝트와 다음 호출에서 `UseStaticFiles` 에서 확장 메서드 `Startup.Configure`:

[!code-csharp[Main](../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]

`app.UseStaticFiles();`에 있는 파일을 사용 하면 `web root` (*wwwroot* 기본적으로) servable 합니다. 나중에 다른 디렉터리 내용을와 servable 확인 하는 방법을 보여 드리 려 `UseStaticFiles`합니다.

"Microsoft.AspNetCore.StaticFiles" NuGet 패키지를 포함 해야 합니다.

> [!NOTE]
> `web root`기본적으로 *wwwroot* 있지만 디렉터리를 설정할 수는 `web root` 디렉토리 `UseWebRoot`합니다.

있다고 가정 하면 외부 사용 하려는 정적 파일이 있는 프로젝트 계층 구조는 `web root`합니다. 예:

* wwwroot
  * css
  * 이미지
  * ...
* MyStaticFiles
  * test.png

에 액세스 하려면 요청에 대 한 *test.png*, 정적 파일 미들웨어를 다음과 같이 구성 합니다.

[!code-csharp[Main](../fundamentals/static-files/sample/StartupTwoStaticFiles.cs?highlight=5,6,7,8,9,10&name=snippet1)]

요청을 `http://<app>/StaticFiles/test.png` 사용할는 *test.png* 파일입니다.

`StaticFileOptions()`응답 헤더를 설정할 수 있습니다. 아래 코드 정적 파일에서 처리를 설정 하는 예를 들어는 *wwwroot* 폴더 및 집합은 `Cache-Control` 헤더를 10 분 (600 초) 동안 공개적으로 캐시할 수 있도록:

[!code-csharp[Main](../fundamentals/static-files/sample/StartupAddHeader.cs?name=snippet1)]

![추가 된 캐시 제어 헤더를 보여 주는 응답 헤더](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>정적 파일 권한 부여

정적 파일 모듈에서는 **없는** 권한 부여 확인 합니다. 모든 파일에서 제공 비롯 *wwwroot* 공개적으로 사용할 수 있습니다. 파일을 제공 하려면 권한 부여 기반으로 합니다.

* 외부에 보관해 두면 *wwwroot* 및 정적 파일 미들웨어에 액세스할 수 있는 모든 디렉터리 **및**

* 반환 되는 컨트롤러 동작을 통해 역할는 `FileResult` 권한 부여 적용 되는 위치

## <a name="enabling-directory-browsing"></a>디렉터리 검색을 사용 하도록 설정

디렉터리 검색 웹 응용 프로그램의 사용자가 지정된 된 디렉터리 내에서 파일과 디렉터리의 목록을 볼 수 있습니다. 보안상의 이유로 기본적으로 비활성화 되어 디렉터리 검색 (참조 [고려 사항](#considerations)). 디렉터리 검색을 사용 하려면 호출는 `UseDirectoryBrowser` 에서 확장 메서드 `Startup.Configure`:

[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet1)]

호출 하 여 필요한 서비스를 추가 하 고 `AddDirectoryBrowser` 에서 확장 메서드 `Startup.ConfigureServices`:

[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet2)]

위의 코드 디렉터리를 검색할 수는 *wwwroot/이미지* URL http://를 사용 하 여 폴더\<앱 > / 각 파일 및 폴더에 대 한 링크와 MyImages:

![디렉터리 검색](static-files/_static/dir-browse.png)

참조 [고려 사항](#considerations) 검색을 사용 하도록 설정할 때는 보안 위험에 있습니다.

두 참고 `app.UseStaticFiles` 호출 합니다. CSS, 이미지 및 JavaScript에서 제공 하는 데 필요한 첫 번째는 *wwwroot* 폴더 및 디렉터리의 검색에 대 한 두 번째 호출에서 *wwwroot/이미지* URL http://를 사용 하 여 폴더\<응용 프로그램 > / MyImages:

[!code-csharp[Main](static-files/sample/StartupBrowse.cs?highlight=3,5&name=snippet1)]

## <a name="serving-a-default-document"></a>기본 문서를 처리합니다.

기본 홈 페이지를 설정 하면 사이트 방문자 사이트를 방문할 때 시작 하는 곳입니다. 사용자에 게 URI를 정규화 하지 않고 기본 페이지를 제공 하도록 웹 앱에 대 한 순서 대로 호출는 `UseDefaultFiles` 에서 확장 메서드 `Startup.Configure` 다음과 같습니다.

[!code-csharp[Main](../fundamentals/static-files/sample/StartupEmpty.cs?highlight=3&name=snippet1)]

> [!NOTE]
> `UseDefaultFiles`먼저 호출 해야 `UseStaticFiles` 기본 파일 역할을 합니다. `UseDefaultFiles`파일을 처리 하지 않는 실제로 있는 URL 다시 작성기입니다. 정적 파일 미들웨어를 사용 하도록 설정 해야 합니다 (`UseStaticFiles`)에이 파일을 제공 합니다.

와 `UseDefaultFiles`, 폴더에 대 한 요청에 대 한 검색 합니다.

* default.htm
* default.html
* index.htm
* index.html

첫 번째 파일을 목록에서 찾을 수는 (하지만 요청 된 URI를 표시 하도록 브라우저 URL 계속)의 정규화 된 URI 요청이 경우 처럼 처리 됩니다.

다음 코드에서는 기본 파일 이름을 변경 하는 방법을 보여 줍니다. *mydefault.html*합니다.

[!code-csharp[Main](static-files/sample/StartupDefault.cs?name=snippet1)]

## <a name="usefileserver"></a>UseFileServer

`UseFileServer`기능을 결합 `UseStaticFiles`, `UseDefaultFiles`, 및 `UseDirectoryBrowser`합니다.

다음 코드를 통해 정적 파일 및 기본 파일 서비스 되도록 하지만 디렉터리 검색을 허용 하지 않습니다.

```csharp
app.UseFileServer();
   ```

다음 코드를 통해 정적 파일, 기본 파일 및 디렉터리 검색:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
   ```

참조 [고려 사항](#considerations) 검색을 사용 하도록 설정할 때는 보안 위험에 있습니다. 와 마찬가지로 `UseStaticFiles`, `UseDefaultFiles`, 및 `UseDirectoryBrowser`외부에 있는 파일을 제공 하려는 경우는 `web root`를 인스턴스화하고 구성는 `FileServerOptions` 매개 변수로 전달 하는 개체 `UseFileServer`합니다. 웹 앱의 다음 디렉터리 계층 구조를 예로 들 수 있습니다.

* wwwroot

  * css

  * 이미지

  * ...

* MyStaticFiles

  * test.png

  * default.html

위의 계층 예제를 사용 하려는 경우도 정적 파일, 기본 파일 및 검색을 사용 하도록 설정 된 `MyStaticFiles` 디렉터리입니다. 다음 코드 조각에서 한 번 호출 하 여 수행 됩니다 하는 `FileServerOptions`합니다.

[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?highlight=5,6,7,8,9,10,11&name=snippet1)]

경우 `enableDirectoryBrowsing` 로 설정 된 `true` 호출에 필요한 `AddDirectoryBrowser` 에서 확장 메서드 `Startup.ConfigureServices`:

[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?name=snippet2)]

파일 계층 구조 및 위의 코드를 사용 하 여:

| URI            |                             응답  |
| ------- | ------|
| `http://<app>/StaticFiles/test.png`    |      MyStaticFiles/test.png |
| `http://<app>/StaticFiles`              |     MyStaticFiles/default.html |

명명 된 파일이 기본값이 있는 경우는 *MyStaticFiles* 디렉터리, http://\<앱 > StaticFiles 클릭 가능한 링크와 함께 나열 하는 디렉터리를 반환 합니다. /:

![정적 파일 목록](static-files/_static/db2.PNG)

> [!NOTE]
> `UseDefaultFiles`및 `UseDirectoryBrowser` url http:// 걸립니다\<앱 >는 후행 슬래시 및 원인 없이 StaticFiles http:// 리디렉션됩니다 클라이언트 쪽 /\<응용 프로그램 > /StaticFiles/ (후행 슬래시 추가). 문서 내에서 후행 슬래시 상대 Url 없이 올바르지 않게 됩니다.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

`FileExtensionContentTypeProvider` 클래스 파일 확장명이 MIME 콘텐츠 형식이 매핑되는 컬렉션을 포함 합니다. 다음 샘플에서는 여러 파일 확장명이 알려진된 MIME 형식에 등록 된, ".rtf" 대신 표시 되며 ".mp4" 제거 됩니다.

[!code-csharp[Main](../fundamentals/static-files/sample/StartupFileExtensionContentTypeProvider.cs?highlight=3,4,5,6,7,8,9,10,11,12,19&name=snippet1)]

참조 [MIME 콘텐츠 형식을](http://www.iana.org/assignments/media-types/media-types.xhtml)합니다.

## <a name="non-standard-content-types"></a>사용할 수 없는 콘텐츠 형식

ASP.NET 정적 파일 미들웨어 거의 400 알려진된 파일 콘텐츠 형식을 이해합니다. 사용자가 알 수 없는 파일 형식의 파일을 요청 하는 경우 정적 파일 미들웨어는 HTTP 404 (찾을 수 없음) 응답을 반환 합니다. 디렉터리 검색을 사용 하는 경우 파일에 대 한 링크에는 표시 하지만 URI에서 HTTP 404 오류를 반환 합니다.

다음 코드에서는 처리 알 수 없는 형식을 사용 하면 고 알 수 없는 파일을 이미지로 렌더링 됩니다.

[!code-csharp[Main](static-files/sample/StartupServeUnknownFileTypes.cs?name=snippet1)]

위의 코드와 함께 파일을 알 수 없는 콘텐츠 형식에 대 한 요청 이미지 형식으로 반환 됩니다.

>[!WARNING]
> 사용 하도록 설정 `ServeUnknownFileTypes` 보안상 위험할 수 및 사용은 권장 되지 않습니다.  `FileExtensionContentTypeProvider`(위에서 설명한) 비표준 확장명을 가진 파일을 처리 하는 보다 안전한 대체 방법을 제공 합니다.

### <a name="considerations"></a>고려 사항

>[!WARNING]
> `UseDirectoryBrowser`및 `UseStaticFiles` 비밀 정보를 누출 할 수 있습니다. 하는 것이 좋습니다 있습니다 **하지** 사용 디렉터리 프로덕션 환경에서 검색 합니다. 주의를 사용 하는 디렉터리에 대 한 `UseStaticFiles` 또는 `UseDirectoryBrowser` 대로 전체 디렉터리와 모든 하위 디렉터리를 액세스할 수 있습니다. 와 같은 자체 디렉터리에 공용 콘텐츠를 유지 하는 것이 좋습니다  *\<콘텐츠 루트 > / wwwroot*, 응용 프로그램 뷰, 구성 파일 등에서 떨어진 곳입니다.

* 노출 된 콘텐츠에 대 한 Url `UseDirectoryBrowser` 및 `UseStaticFiles` 는 대/소문자 구분 및 해당 내부 파일 시스템의 문자 제한이 적용 됩니다. 예를 들어 Windows 대/소문자 구분, 이지만 Mac 및 Linux 하지 않습니다.

* IIS에서 호스팅되는 ASP.NET Core 응용 프로그램의 경우 정적 파일에 대 한 요청을 포함 하 여 응용 프로그램에 대 한 모든 요청을 전달 하도록 ASP.NET 코어 모듈을 사용 합니다. ASP.NET Core 모듈에 의해 처리 되기 전에 요청을 처리할 수 있는 기회가 못하는 때문에 IIS 정적 파일 처리기 사용 되지 않습니다.

* 에 서버 또는 웹 사이트 수준에서 IIS 정적 파일 처리기를 제거 합니다.

     * 탐색 하 고 **모듈** 기능

     * 선택 **모듈은 staticfilemodule입니다** 목록에서

     * 탭 **제거** 에 **동작** 사이드바

>[!WARNING]
> IIS 정적 파일 처리기에서 사용 되는 경우 **및** ASP.NET Core 모듈 (ANCM) 제대로 구성 되지 않은 (예를 들어 경우 *web.config* 응용 프로그램이 배포 되지), 정적 파일 처리 됩니다.

* 응용 프로그램 프로젝트 외부에서 코드 파일 (c# 및 Razor 포함)를 배치할 `web root` (*wwwroot* 기본적으로). 이렇게 하면 응용 프로그램의 클라이언트 쪽 콘텐츠 및 서버 쪽 코드 유출 되지 않도록 방지 서버 쪽 소스 코드를 명확히 구분을 만들어집니다.

## <a name="additional-resources"></a>추가 리소스

* [미들웨어](middleware.md)

* [ASP.NET Core 소개](../index.md)

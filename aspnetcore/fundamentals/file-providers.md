---
title: "ASP.NET Core 공급자 파일 | Microsoft 문서"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 1e35d362-0005-4f84-a187-274ca203a787
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/file-providers
translationtype: Machine Translation
ms.sourcegitcommit: 0ebb9b63931ccb26126740de08270eda9b1ea486
ms.openlocfilehash: 813fc1747714ae8d26aff58ec32555df677eb813
ms.lasthandoff: 03/23/2017

---
# <a name="file-providers-in-aspnet-core"></a>ASP.NET Core의 파일 공급자

[Steve Smith](http://ardalis.com)

ASP.NET Core 파일 공급자를 사용 하 여 파일 시스템 액세스를 추상화합니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/file-providers/sample)

## <a name="file-provider-abstractions"></a>파일 공급자 추상화

파일 공급자는 파일 시스템에 비해 추상화는입니다. 기본 인터페이스 `IFileProvider`합니다. `IFileProvider`파일 정보를 가져오는 메서드를 노출 (`IFileInfo`), 디렉터리 정보 (`IDirectoryContents`), 변경 알림을 설정 하 고 (사용 하는 `IChangeToken`).

`IFileInfo`개별 파일 또는 디렉터리에 대 한 속성과 메서드를 제공합니다. 이 클래스에 두 개의 부울 속성이 `Exists` 및 `IsDirectory`, 파일의 설명 하는 속성 뿐만 아니라 `Name`, `Length` (바이트)에서 및 `LastModified` 날짜입니다. 사용 하 여 파일을 읽을 수는 `CreateReadStream` 메서드.

## <a name="file-provider-implementations"></a>파일 공급자 구현

구현에는 `IFileProvider` 사용할 수 있는: 물리적, 포함, 및 합성 합니다. 물리적 공급자를 사용 하 여 실제 시스템의 파일에 액세스할 수 있습니다. 포함 된 공급자를 사용 하 여 어셈블리에 포함 된 파일에 액세스할 수 있습니다. 복합 공급자를 사용 하 여 하나 이상의 다른 공급자에서 결합 된 파일 및 디렉터리에 액세스할 수 있도록 합니다.

### <a name="physicalfileprovider"></a>PhysicalFileProvider

`PhysicalFileProvider` 실제 파일 시스템에 대 한 액세스를 제공 합니다. 로 래핑되는 `System.IO.File` 형식 (실제 provider)를 범위 디렉터리 및 해당 자식에 대 한 모든 경로 지정 합니다. 특정 디렉터리와이 경계 외부의 파일 시스템에 대 한 액세스를 방지 자식에 대 한 액세스를 제한이 범위를 지정 합니다. 이 공급자를 인스턴스화할 때와이 공급자에 대 한 모든 요청에 대 한 기본 경로 (및이 경로 외부 액세스를 제한)으로 사용 되는 디렉터리 경로 제공 해야 합니다. ASP.NET 핵심 응용 프로그램에서 인스턴스화할 수 있습니다는 `PhysicalFileProvider` 공급자를 직접 또는 있습니다 요청할 수는 `IFileProvider` 컨트롤러 또는 서비스의 생성자를 통해 [종속성 주입](dependency-injection.md)합니다. 일반적으로 두 번째 방법이 더 유연 하 고 테스트 가능한 솔루션 때.

아래 샘플은 다음에 만드는 방법을 보여 줍니다는 `PhysicalFileProvider`합니다.


```csharp
IFileProvider provider = new PhysicalFileProvider(applicationRoot);
IDirectoryContents contents = provider.GetDirectoryContents(""); // the applicationRoot contents
IFileInfo fileInfo = provider.GetFileInfo("wwwroot/js/site.js"); // a file under applicationRoot
```

디렉터리 내용을 반복 하거나 하위 경로 제공 하 여 특정 파일의 정보를 얻을 수 있습니다.

컨트롤러의 생성자에 지정 하는 컨트롤러에서 공급자를 요청 하려면 로컬 필드에 할당 합니다. 작업 방법 중에서 로컬 인스턴스를 사용 합니다.

[!code-csharp[주](file-providers/sample/src/FileProviderSample/Controllers/HomeController.cs?highlight=5,7,12&range=6-19)]

그런 다음 응용 프로그램에서 공급자를 만들려면 `Startup` 클래스:

[!code-csharp[주](file-providers/sample/src/FileProviderSample/Startup.cs?highlight=35,40&range=1-43)]

에 *Index.cshtml* 보기, 반복는 `IDirectoryContents` 제공 합니다.

[!code-html[주](file-providers/sample/src/FileProviderSample/Views/Home/Index.cshtml?highlight=2,7,9,11,15)]

결과:

![파일 공급자 샘플 응용 프로그램 목록 실제 파일 및 폴더](file-providers/_static/physical-directory-listing.png)

### <a name="embeddedfileprovider"></a>EmbeddedFileProvider

`EmbeddedFileProvider` 어셈블리에 포함 된 파일에 액세스 하는 데 사용 됩니다. .NET Core를 사용 하 여 어셈블리에 파일 포함 된 `<EmbeddedResource>` 요소에는 *.csproj* 파일:

[!code-json[주](file-providers/sample/src/FileProviderSample/FileProviderSample.csproj?range=13-18)]

사용할 수 있습니다 [와일드 카드 사용 패턴](#globbing-patterns) 어셈블리에 포함 하는 파일을 지정 하는 경우. 이러한 패턴은 하나 이상의 파일에 맞게 사용할 수 있습니다.

> [!NOTE]
> 실제로 어셈블리의 프로젝트에 있는 각.js 파일을 포함 해야 할 것이 거의있지 않습니다. 위의 예제는 데모 전용입니다.

만들 때는 `EmbeddedFileProvider`, 어셈블리 되었으면 해당 생성자에 전달 합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
var embeddedProvider = new EmbeddedFileProvider(Assembly.GetEntryAssembly());
```

위의 코드 조각에 만드는 방법을 보여 줍니다는 `EmbeddedFileProvider` 현재 실행 중인 어셈블리에 액세스할 수 있습니다.

사용 하 여 샘플 앱을 업데이트 하는 `EmbeddedFileProvider` 결과 다음과 같은 출력:

![포함 된 파일을 나열 하는 공급자 샘플 응용 프로그램 파일](file-providers/_static/embedded-directory-listing.png)

> [!NOTE]
> 포함된 리소스는 디렉터리를 노출 하지 않습니다. 에 사용 하 여 해당 파일 이름 (네임 스페이스)를 통해 리소스에 대 한 경로가 포함 되어 있는 대신 `.` 구분 기호입니다.

> [!TIP]
> `EmbeddedFileProvider` 생성자는 선택적 `baseNamespace` 매개 변수입니다. 이 지정에 대 한 호출 범위는 `GetDirectoryContents` 이러한 리소스는 제공 된 네임 스페이스에 있습니다.

### <a name="compositefileprovider"></a>CompositeFileProvider

`CompositeFileProvider` 결합 `IFileProvider` 인스턴스를 여러 공급자에서 파일을 사용 하기 위한 단일 인터페이스를 노출 합니다. 만들 때의 `CompositeFileProvider`, 하나 이상의 전달 `IFileProvider` 인스턴스 생성자에 게:

[!code-csharp[주](file-providers/sample/src/FileProviderSample/Startup.cs?highlight=3&range=35-37)]

사용 하 여 샘플 앱을 업데이트 하는 `CompositeFileProvider` 공급자도 포함 되어 두 물리적 및 포함 된 이전에 구성한, 결과 다음과 같은 출력입니다.

![물리적 파일 및 폴더와 포함 된 파일을 나열 하는 공급자 샘플 응용 프로그램 파일](file-providers/_static/composite-directory-listing.png)

## <a name="watching-for-changes"></a>변경 내용을 감시

`IFileProvider` `Watch` 메서드 하나 이상의 파일 또는 디렉터리의 변경 사항 조사 하는 방법을 제공 합니다. 이 메서드는 경로 문자열을 사용할 수 있는 [와일드 카드 사용 패턴](#globbing-patterns) 여러 파일 및 반환을 지정 하는 `IChangeToken`합니다. 이 토큰을 노출 한 `HasChanged` 검사할 수 있는 속성 및 `RegisterChangeCallback` 지정된 된 경로 문자열에 변경 내용이 발견 될 때 호출 되는 메서드. 각 변경 토큰만 호출 하는 연결 된 해당 콜백이 단일 변경에 대 한 응답에서을 참고 합니다. 지속적인 모니터링을 사용 하려면 사용할 수 있습니다는 `TaskCompletionSource` 아래와 같이 하거나 다시 작성 `IChangeToken` 인스턴스 변경 내용에 대 한 응답에서입니다.

이 기사의 샘플에서는 콘솔 응용 프로그램은 텍스트 파일이 수정 될 때마다 메시지를 표시 하도록 구성 되었습니다.

[!code-csharp[주](file-providers/sample/src/WatchConsole/Program.cs?highlight=11,12,24,26,27)]

파일을 여러 번 저장 한 후 결과:

![명령 창을 보여 줍니다 quotes.txt 파일에서 변경 내용 모니터링 응용 프로그램을 실행 하는 dotnet 실행 하 고 파일에는&5; 번 변경 된 후](file-providers/_static/watch-console.png)

> [!NOTE]
> Docker 컨테이너 및 네트워크 공유와 같은 일부 파일 시스템 안정적으로 변경 알림을 보내지 않을 수도 있습니다. 설정의 `DOTNET_USE_POLLINGFILEWATCHER` 환경 변수를 `1` 또는 `true` 4 초 마다 변경에 대 한 파일 시스템을 폴링할 수 있습니다.

## <a name="globbing-patterns"></a>와일드 카드 사용 패턴

호출 하는 와일드 카드 패턴을 사용 하는 파일 시스템 경로 *와일드 카드 사용 패턴*합니다. 파일 그룹을 지정 하려면 이러한 간단한 패턴을 사용할 수 있습니다. 두 개의 와일드 카드 문자는 `*` 및 `**`합니다.

**`*`**

   현재 폴더 수준 또는 모든 파일 이름 또는 모든 파일 확장명에 아무 것도 비교합니다. 일치 하는 항목은로 끝나도록 `/` 및 `.` 파일 경로에 문자입니다.

<strong><code>**</code></strong>

   아무 것도 여러 디렉터리 수준에서 검색 합니다. 재귀적으로 하는 데 사용 될 디렉터리 계층 구조 내에서 많은 파일과 일치 합니다.

### <a name="globbing-pattern-examples"></a>와일드 카드 사용 패턴 예제

**`directory/file.txt`**

   특정 디렉터리에 특정 파일을 찾습니다.

**<code>directory/*.txt</code>**

   일치 하는 모든 파일을 `.txt` 특정 디렉터리에서 확장 합니다.

**`directory/*/bower.json`**

   모든 일치 하는 `bower.json` 디렉터리 정확히 한 수준 아래에 있는 파일의 `directory` 디렉터리입니다.

**<code>directory/&#42;&#42;/&#42;.txt</code>**

   일치 하는 모든 파일을 `.txt` 확장에서 아무 곳 이나 찾을 `directory` 디렉터리입니다.

## <a name="file-provider-usage-in-aspnet-core"></a>ASP.NET 핵심에서 파일 공급자 사용

ASP.NET Core의 여러 부분 파일 공급자를 사용합니다. `IHostingEnvironment`응용 프로그램의 콘텐츠 루트 및 웹 루트로 노출 `IFileProvider` 형식입니다. 정적 파일 미들웨어 파일 공급자를 사용 하 여 정적 파일을 찾습니다. Razor가 많이 사용 `IFileProvider` 뷰 찾기. Dotnet의 기능 사용 하 여 파일 공급자 및 게시 와일드 카드 사용 패턴 지정 파일을 게시 해야 합니다.

## <a name="recommendations-for-use-in-apps"></a>앱에서 사용 하기 위한 권장 사항

파일 시스템 액세스를 필요로 하는 ASP.NET 핵심 응용 프로그램의 인스턴스를 요청할 수 있습니다 `IFileProvider` 종속성 주입을 통해 다음이 샘플에 표시 된 대로 액세스 하려면 해당 메서드를 사용 합니다. 이 옵션을 사용 하면 앱이 시작 되 고 응용 프로그램을 인스턴스화하는 구현 형식의 수를 줄일 수를 한 번 공급자를 구성할 수 있습니다.


---
title: "디렉터리 구조 | Microsoft 문서"
author: guardrex
description: "게시 된 ASP.NET 핵심 응용 프로그램의 디렉터리 구조입니다."
keywords: "ASP.NET Core, 디렉터리 구조"
ms.author: riande
manager: wpickett
ms.date: 03/15/2017
ms.topic: article
ms.assetid: e55eb131-d42e-4bf6-b130-fd626082243c
ms.technology: aspnet
ms.prod: asp.net-core
uid: hosting/directory-structure
translationtype: Machine Translation
ms.sourcegitcommit: 207359af7720216a9b20ce27dd95647da42efd80
ms.openlocfilehash: 6d72fe8394bc086774e5c3924282a52430511f6b
ms.lasthandoff: 03/23/2017

---
# <a name="directory-structure"></a>디렉터리 구조

[Luke Latham](https://github.com/GuardRex)

ASP.NET Core, 응용 프로그램 디렉터리에에서 *게시*, 응용 프로그램 파일, 구성 파일, 정적, 패키지 및 자산 (자체 포함 된 응용 프로그램)에 대 한 런타임 구성 됩니다. 이 이전 버전의 ASP.NET 웹 루트 디렉터리 내에 거주 전체 응용 프로그램으로 동일한 디렉터리 구조입니다.

| 응용 프로그램 유형 | 디렉터리 구조 |
| --- | --- |
| 프레임 워크 종속 배포 | <ul><li>게시\*<ul><li>로그\* (publishOptions에 포함) 하는 경우</li><li>refs\*</li><li>런타임\*</li><li>뷰\* (publishOptions에 포함) 하는 경우</li><li>wwwroot\* (publishOptions에 포함) 하는 경우</li><li>.dll 파일</li><li>myapp.deps.json</li><li>myapp.dll로 지정</li><li>myapp.pdb</li><li>myapp 합니다. PrecompiledViews.dll (Razor 뷰를 미리 컴파일하면) 하는 경우</li><li>myapp 합니다. PrecompiledViews.pdb (Razor 뷰를 미리 컴파일하면) 하는 경우</li><li>myapp.runtimeconfig.json</li><li>web.config (publishOptions에 포함) 하는 경우</li></ul></li></ul> |
| 자체 포함된 배포 | <ul><li>게시\*<ul><li>로그\* (publishOptions에 포함) 하는 경우</li><li>refs\*</li><li>뷰\* (publishOptions에 포함) 하는 경우</li><li>wwwroot\* (publishOptions에 포함) 하는 경우</li><li>.dll 파일</li><li>myapp.deps.json</li><li>myapp.exe</li><li>myapp.pdb</li><li>myapp 합니다. PrecompiledViews.dll (Razor 뷰를 미리 컴파일하면) 하는 경우</li><li>myapp 합니다. PrecompiledViews.pdb (Razor 뷰를 미리 컴파일하면) 하는 경우</li><li>myapp.runtimeconfig.json</li><li>web.config (publishOptions에 포함) 하는 경우</li></ul></li></ul> |
\*디렉터리를 나타냅니다.

내용을 *게시* 디렉터리를 나타내는 *콘텐츠 루트 경로*라고도 하는 *응용 프로그램 기본 경로*, 배포. 에 이름이 지정 됩니다는 *게시* 위치 역할 배포에 있는 디렉터리를 서버의 실제 경로에 호스팅된 응용 프로그램을 합니다. *wwwroot* 디렉터리에 있는 경우 정적 자산이 포함만 합니다. *로그* 디렉터리를 프로젝트에서 만들고 추가 하 여 배포에 포함 될 수 있습니다는 `<Target>` 요소 아래에 표시 된 프로그램 *.csproj* 파일 또는 물리적 서버에서 디렉터리를 만들어 합니다.

```xml
<Target Name="CreateLogsFolder" AfterTargets="AfterPublish">
  <MakeDir Directories="$(PublishDir)logs" Condition="!Exists('$(PublishDir)logs')" />
  <MakeDir Directories="$(PublishUrl)Logs" Condition="!Exists('$(PublishUrl)Logs')" />
</Target>
```

첫 번째 `<MakeDir>` 요소를 사용 하는 `PublishDir` 속성을 사용한 dotnet CLI 게시 작업에 대 한 대상 위치를 결정 합니다. 두 번째 `<MakeDir>` 요소를 사용 하는 `PublishUrl` 속성을 대상 위치를 확인 하려면 Visual Studio에서 사용 됩니다. Visual Studio를 사용 하는 `PublishUrl` 비.NET Core 프로젝트와의 호환성에 대 한 속성입니다.

배포 디렉터리에 읽기/실행 사용 권한이 필요 하지만 *로그* 디렉터리 읽기/쓰기 권한이 필요 합니다. 자산을 쓸 추가 디렉터리에 읽기/쓰기 권한이 필요 합니다.


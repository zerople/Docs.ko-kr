---
title: "ASP.NET Core 디렉터리 구조"
author: guardrex
description: "게시 된 ASP.NET Core 응용 프로그램의 디렉터리 구조입니다."
keywords: "ASP.NET Core, 디렉터리 구조"
ms.author: riande
manager: wpickett
ms.date: 03/15/2017
ms.topic: article
ms.assetid: e55eb131-d42e-4bf6-b130-fd626082243c
ms.technology: aspnet
ms.prod: asp.net-core
uid: hosting/directory-structure
ms.openlocfilehash: 9ec635b596520e3d19f4040758dd59c1cbca97f4
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="directory-structure-of-published-aspnet-core-apps"></a>게시 된 ASP.NET Core 응용 프로그램의 디렉터리 구조

으로 [Luke Latham](https://github.com/GuardRex)

ASP.NET Core, 응용 프로그램 디렉터리에서에서 *게시*, 응용 프로그램 파일, config 파일, 정적 자산, 패키지 및 런타임 (앱에 대 한 자체 포함)의 구성 됩니다. 이전 버전의 ASP.NET 전체 응용 프로그램 웹 루트 디렉터리 안에 거주와 동일한 디렉터리 구조입니다.

| 앱 유형 | 디렉터리 구조 |
| --- | --- |
| 프레임 워크 종속 배포 | <ul><li>게시\*<ul><li>로그\* (publishOptions에 포함 됨) 하는 경우</li><li>refs\*</li><li>런타임\*</li><li>뷰\* (publishOptions에 포함 됨) 하는 경우</li><li>wwwroot\* (publishOptions에 포함 됨) 하는 경우</li><li>.dll 파일</li><li>myapp.deps.json</li><li>myapp.dll로 지정</li><li>myapp.pdb</li><li>myapp 합니다. PrecompiledViews.dll (Razor 뷰 미리 컴파일) 하는 경우</li><li>myapp 합니다. PrecompiledViews.pdb (Razor 뷰 미리 컴파일) 하는 경우</li><li>myapp.runtimeconfig.json</li><li>web.config (publishOptions에 포함 됨) 하는 경우</li></ul></li></ul> |
| 자체 포함된 배포 | <ul><li>게시\*<ul><li>로그\* (publishOptions에 포함 됨) 하는 경우</li><li>refs\*</li><li>뷰\* (publishOptions에 포함 됨) 하는 경우</li><li>wwwroot\* (publishOptions에 포함 됨) 하는 경우</li><li>.dll 파일</li><li>myapp.deps.json</li><li>myapp.exe</li><li>myapp.pdb</li><li>myapp 합니다. PrecompiledViews.dll (Razor 뷰 미리 컴파일) 하는 경우</li><li>myapp 합니다. PrecompiledViews.pdb (Razor 뷰 미리 컴파일) 하는 경우</li><li>myapp.runtimeconfig.json</li><li>web.config (publishOptions에 포함 됨) 하는 경우</li></ul></li></ul> |
\*디렉터리를 나타냅니다.

내용을 *게시* 디렉터리 나타냅니다는 *콘텐츠 루트 경로*라고도 하는 *응용 프로그램 기본 경로*, 배포의 합니다. 에 이름이 지정 된는 *게시* 배포에 있는 디렉터리를 해당 위치 역할 호스팅된 응용 프로그램에는 서버의 실제 경로입니다. *wwwroot* 디렉터리에 있는 경우 정적 자산이 포함만 합니다. *로그* 디렉터리를 프로젝트에서 만들고 추가 하 여 배포에 포함 될 수 있습니다는 `<Target>` 요소 아래에 표시 된 프로그램 *.csproj* 파일 또는에 디렉터리를 만들어 물리적으로 서버입니다.

```xml
<Target Name="CreateLogsFolder" AfterTargets="AfterPublish">
  <MakeDir Directories="$(PublishDir)logs" Condition="!Exists('$(PublishDir)logs')" />
  <MakeDir Directories="$(PublishUrl)Logs" Condition="!Exists('$(PublishUrl)Logs')" />
</Target>
```

첫 번째 `<MakeDir>` 를 사용 하는 요소는 `PublishDir` 게시 작업에 대 한 대상 위치를 확인 하려면 속성을.NET Core CLI를 통해 사용 됩니다. 두 번째 `<MakeDir>` 를 사용 하는 요소는 `PublishUrl` 속성을 대상 위치를 확인 하려면 Visual Studio에서 사용 됩니다. Visual Studio를 사용 하 여는 `PublishUrl` 아닌.NET Core 프로젝트와의 호환성에 대 한 속성입니다.

배포 디렉터리에 읽기/실행 권한이 필요로 하는 동안는 *로그* 디렉터리에 대 한 읽기/쓰기 권한이 필요 합니다. 자산을 쓸 추가 디렉터리 읽기/쓰기 권한이 필요 합니다.

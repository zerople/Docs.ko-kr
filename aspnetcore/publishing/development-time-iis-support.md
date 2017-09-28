---
title: "ASP.NET Core용 Visual Studio의 개발 시간 IIS 지원"
author: shirhatti
description: "Windows Server에서 IIS 배후에서 실행 중인 경우 ASP.NET Core 응용 프로그램 디버깅에 대한 지원을 확인해 보세요."
keywords: "ASP.NET Core, 인터넷 정보 서비스, iis, windows server,asp.net core 모듈, 디버깅"
ms.author: riande
manager: wpickett
ms.date: 09/13/2017
ms.topic: article
ms.assetid: 83d98477-9d10-4a78-a54a-f325ad67d13b
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/development-time-iis-support
ms.openlocfilehash: a35a6fd9896c4c110d1b6680b6aaf718d29a18ab
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2017
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>ASP.NET Core용 Visual Studio의 개발 시간 IIS 지원

작성자: [Sourabh Shirhatti](https://twitter.com/sshirhatti)

이 문서에서는 Windows Server에서 IIS 배후에서 실행 중인 ASP.NET Core 응용 프로그램 디버깅에 대한 [Visual Studio](https://www.visualstudio.com/vs/) 지원을 설명합니다. 이 항목에서는 이 기능을 활성화하고 프로젝트를 설정하는 방법을 안내합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Visual Studio(2017/버전 15.3 이상)
* ASP.NET 및 웹 개발 워크로드 *또는* .NET Core 플랫폼 간 개발 워크로드

## <a name="enable-iis"></a>IIS 활성화

시스템에서 IIS를 활성화합니다. **제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다. **인터넷 정보 서비스** 확인란을 선택합니다.

![인터넷 정보 서비스가 검은 사각형(확인 표시 아님)으로 표시되어 있고 IIS 기능 중 일부가 활성화되었음을 보여 주는 Windows 기능](development-time-iis-support/_static/enable_iis.png)

IIS 설치 완료를 위해 재부팅이 필요한 경우 시스템을 다시 부팅하세요.

## <a name="enable-development-time-iis-support"></a>개발 시간 IIS 지원 활성화

IIS를 설치했으면 Visual Studio 설치 관리자를 시작하여 기존 Visual Studio 설치를 수정합니다. 설치 관리자에서 **개발 시간 IIS 지원** 구성 요소를 선택합니다. 이 구성 요소는 **ASP.NET 및 웹 개발** 워크로드에 대한 **요약** 패널에 선택적 구성 요소로 나열됩니다. 그러면 ASP.NET Core 애플리케이션을 실행하는 데 필요한 네이티브 IIS 모듈인 [ASP.NET Core 모듈](xref:fundamentals/servers/aspnet-core-module)이 설치됩니다.

![Visual Studio 기능 수정: 워크로드 탭이 선택되어 있습니다. 웹 및 클라우드 섹션에는 ASP.NET 및 웹 개발 패널이 선택되어 있습니다. 요약 패널의 선택 영역 오른쪽에는 개발 시간 IIS 지원 확인란이 있습니다.](development-time-iis-support/_static/development_time_support.png)

## <a name="configure-the-project"></a>프로젝트 구성

새 실행 프로필을 만들어 개발 시간 IIS 지원을 추가합니다. Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 선택합니다. **디버그** 탭을 선택합니다. **실행** 드롭다운에서 **IIS**를 선택합니다. **브라우저 실행** 기능이 올바른 URL을 사용하여 활성화되었는지 확인합니다.

![디버그 탭이 선택된 프로젝트 속성 창입니다. 프로필 및 실행 설정은 IIS로 설정되어 있습니다. 브라우저 실행 기능은 http://localhost/WebApplication2의 주소를 사용하여 활성화되었습니다. 익명 인증 사용이 활성화된 웹 서버 설정 영역의 앱 URL 필드에도 동일한 주소가 제공됩니다.](development-time-iis-support/_static/project_properties.png)

또는 앱의 [launchSettings.json](http://json.schemastore.org/launchsettings) 파일에 실행 프로필을 수동으로 추가할 수 있습니다.

```json
{
    "iisSettings": {
        "windowsAuthentication": false,
        "anonymousAuthentication": true,
        "iis": {
            "applicationUrl": "http://localhost/WebApplication2",
            "sslPort": 0
        }
    },
    "profiles": {
        "IIS": {
            "commandName": "IIS",
            "launchBrowser": "true",
            "launchUrl": "http://localhost/WebApplication2",
            "environmentVariables": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    }
}
```

관리자 권한으로 실행하지 않을 경우 Visual Studio를 다시 시작하라는 메시지가 표시될 수 있습니다. 메시지가 표시되면 Visual Studio를 다시 시작합니다.

지금까지 프로젝트에 개발 시간 IIS 지원을 구성해 보았습니다. 

## <a name="additional-resources"></a>추가 리소스

* [IIS가 있는 Windows에서 ASP.NET Core 호스팅](xref:publishing/iis)
* [ASP.NET Core 모듈 소개](xref:fundamentals/servers/aspnet-core-module)
* [ASP.NET Core 모듈 구성 참조](xref:hosting/aspnet-core-module)

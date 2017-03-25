---
title: "마이그레이션 구성 | Microsoft 문서"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 8468d859-ff32-4a92-9e62-08c4a9e36594
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/configuration
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: e96f1a5245373e318176749009958647c7d1fbca
ms.lasthandoff: 03/23/2017

---
# <a name="migrating-configuration"></a>마이그레이션 구성

여 [Steve Smith](http://ardalis.com) 및 [Scott Addie](https://scottaddie.com)

시작 했던 이전 기사에서 [ASP.NET 핵심 MVC를 ASP.NET MVC 프로젝트 마이그레이션](mvc.md)합니다. 이 문서에서는 구성을 마이그레이션 했습니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/migration/configuration/samples)

## <a name="setup-configuration"></a>설치 구성

ASP.NET Core 더 이상 사용 하지는 *Global.asax* 및 *web.config* 이전 버전의 ASP.NET 사용 하는 파일입니다. 이전 버전의 ASP 응용 프로그램 시작 논리에 배치 된는 `Application_StartUp` 메서드 내에서 *Global.asax*합니다. ASP.NET MVC의 뒷부분에 나오는 *Startup.cs* 파일은 프로젝트의 루트에 들어 하 고 응용 프로그램이 시작 될 때 호출 되었습니다. ASP.NET Core가이 접근 방법을 채택 완전히 모든 시작 논리에 배치 하 여는 *Startup.cs* 파일입니다.

*web.config* 파일 에서도 ASP.NET 핵심 대체 되었습니다. 자체 구성 지금 구성할 수 있으며 응용 프로그램 시작에서 설명한 절차의 일부로 *Startup.cs*합니다. 구성 XML 파일을 사용할 수 있지만 일반적으로 ASP.NET 핵심 프로젝트에에서 배치 됩니다 구성 값을 JSON 형식 파일을 같은 *appsettings.json*합니다. ASP.NET 핵심 구성 시스템에는 환경에 특정 값에 대 한 안전 하 고 강력한 위치를 제공할 수 있는 환경 변수를도 쉽게 액세스할 수 있습니다. 연결 문자열 및 소스 제어에 체크 되지 않아야 하는 API 키 등의 암호에 대 한 경우 특히 유용 합니다. 참조 [구성](../fundamentals/configuration.md) ASP.NET 코어의 구성에 대 한 자세한 내용을 보려면 합니다.

이 문서에 대 한 것으로 시작 되 고 부분적으로 마이그레이션된 ASP.NET 핵심 프로젝트에서 [이전 기사](mvc.md)합니다. 구성 설정, 다음 생성자 및 속성을 추가 하는 *Startup.cs* 프로젝트의 루트에 있는 파일:

[!code-none[주](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-21)]

참고는이 시점에서 *Startup.cs* 파일은 컴파일되지 않습니다, 다음 코드를 추가 해야 하는 대로 `using` 문:

```csharp
using Microsoft.Extensions.Configuration;
```

추가 *appsettings.json* 적절 한 항목 템플릿을 사용 하 여 프로젝트의 루트에 파일:

![AppSettings JSON 추가](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>Web.config에서 구성 설정을 마이그레이션합니다

ASP.NET MVC 프로젝트에서 필요한 데이터베이스 연결 문자열을 포함 *web.config*에 `<connectionStrings>` 요소입니다. ASP.NET Core 프로젝트 진행에 하겠습니다에이 정보를 저장 하는 *appsettings.json* 파일입니다. 열기 *appsettings.json*, 이미 다음 포함 되어 있는지 확인 합니다.

[!code-json[주](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]


위에 표시 하는 강조 표시 된 줄에서에서 데이터베이스의 이름을 변경 **_CHANGE_ME** 데이터베이스의 이름입니다.

## <a name="summary"></a>요약

ASP.NET Core는 필요한 서비스 및 종속성 수 정의 되어 있고 구성 된 단일 파일에 응용 프로그램에 대 한 모든 시작 논리를 배치 합니다. 대체는 *web.config* 다양 한 환경 변수 뿐만 아니라 JSON과 같은 파일 형식의 활용할 수 있는 유연한 구성 기능으로는 파일입니다.


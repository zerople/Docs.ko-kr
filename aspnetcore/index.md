---
title: "ASP.NET Core 소개 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 1c501638-114a-4cd3-ad39-0a5790b4e764
ms.technology: aspnet
ms.prod: asp.net-core
uid: index
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: c657d9d92a7c990bdf7e4c5392ac6834e6a20ccd
ms.lasthandoff: 03/23/2017

---
# <a name="introduction-to-aspnet-core"></a>ASP.NET Core 소개

여 [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), 및 [Shaun Luttin](https://twitter.com/dicshaunary)

ASP.NET Core는 ASP.NET 완전히 다시 디자인 합니다. 이 항목 ASP.NET 코어에서 새로운 개념을 소개 하 고 최신 웹 앱을 개발 하는 데 도움이 방법을 설명 합니다.  

## <a name="what-is-aspnet-core"></a>ASP.NET Core 란?

ASP.NET Core 새로운 오픈 소스 및 최신 클라우드를 구축 하기 위한 크로스 플랫폼 프레임 기반 웹 응용 프로그램, IoT 앱 및 모바일 백엔드와 같은 인터넷에 연결 된 응용 프로그램입니다. ASP.NET Core 앱에서 실행할 수 [.NET Core](https://www.microsoft.com/net/core/platform) 또는 전체.NET Framework에 있습니다. 실행 하는 온-프레미스 또는 클라우드로 배포 되는 응용 프로그램에는 최적화 된 개발 프레임 워크를 제공 하도록 설계 된. 솔루션을 구성 하는 동안 유연성이 유지할 수 있도록 구성 요소를 최소한의 오버 헤드와 모듈식 구성 됩니다. 개발 하 고 Windows, Mac 및 Linux에서 프로그램 ASP.NET 핵심 앱 플랫폼 간 실행할 수 있습니다. ASP.NET Core는 오픈 소스에서 [GitHub](https://github.com/aspnet/home)합니다.

## <a name="why-build-aspnet-core"></a>ASP.NET Core 빌드 이유?

ASP.NET의 첫 번째 미리 보기 릴리스의 거의 15 년 전에 출시 된.NET Framework의 일부로 합니다.  그 이후로 수백만 명의 개발자가 사용 했 것을 빌드하고 훌륭한 웹 앱을 실행 하려면 있으며 년 동안 우리를 추가 하 고 발전 하 여 다양 한 기능.

ASP.NET Core에 훨씬 간결 하 고 모듈식 프레임 워크에서 발생 하는 아키텍처 변경 번호가 있습니다.  ASP.NET Core 기반으로 더 이상 *System.Web.dll*합니다. 세분화 된 집합에 따라 되 고 제대로 팩터링 [NuGet](http://www.nuget.org/) 패키지 합니다. 이 옵션을 사용 하면 필요한는 NuGet 패키지를 포함 하도록 앱을 최적화할 수 있습니다. 작은 응용 프로그램 노출 영역 혜택에는 보안을 강화 감소 서비스, 성능 향상 및 감소 비용을 지불에 대 한-가상-있습니다-사용 가능한 모델에 포함 됩니다.

ASP.NET Core와는 다음과 같은 기본 개선 얻을 수 있습니다.

* 빌드에 대 한 통합 된 스토리 웹 UI 및 웹 Api

* 통합 [최신 클라이언트 쪽 프레임 워크](client-side/index.md) 및 개발 워크플로

* 환경 기반 하는 클라우드 지원 [구성 시스템](fundamentals/configuration.md)

* 기본 제공 [종속성 주입](fundamentals/dependency-injection.md)

* 새로운 경량 및 모듈식 HTTP 요청 파이프라인

* IIS에서 호스트 하거나 사용자 고유의 프로세스에서 자체 호스트 하는 기능

* 기반 [.NET Core](https://microsoft.com/net/core), true side-by-side-응용 프로그램 버전 관리를 지 원하는

* 전적으로 포함 [NuGet](https://nuget.org) 패키지

* 최신 웹 개발을 간소화 새로운 하는 도구

* 빌드 및 Windows, Mac 및 Linux에서 플랫폼 간 ASP.NET 응용 프로그램 실행

* 오픈 소스 및 관련 커뮤니티

## <a name="build-web-ui-and-web-apis-using-aspnet-core-mvc"></a>웹 UI를 빌드하고 웹 코어 ASP.NET MVC를 사용 하 여 Api

* 모델-뷰-컨트롤러 (MVC) 패턴을 따르는 잘 구성 된와 테스트에 용이한 웹 앱을 만들 수 있습니다. 참조 [MVC](mvc/index.md) 및 [테스트](testing/index.md)합니다.

* 콘텐츠 협상에 대 한 완벽 하 게 지원 있고 여러 형식을 지 원하는 HTTP 서비스를 빌드할 수 있습니다. 참조 [응답 데이터 서식 지정](mvc/models/formatting.md)

* [Razor](http://www.asp.net/web-pages/overview/getting-started/introducing-razor-syntax-c) 만들려는 생산적인 언어 제공 [뷰](mvc/views/index.md)

* [태그 도우미](mvc/views/tag-helpers/intro.md) 만들기 및 Razor 파일의 요소를 HTML 렌더링에 참여 하는 서버 쪽 코드를 사용 하도록 설정

* 기본 제공 또는 사용자 지정 포맷터 (JSON, XML)를 사용 하 여 콘텐츠 협상에 대 한 전체 지원과 함께 HTTP 서비스를 만들 수 있습니다.

* [모델 바인딩](mvc/models/model-binding.md) 액션 메서드 매개 변수를 HTTP 요청에서 데이터를 자동으로 매핑합니다

* [모델 유효성 검사](mvc/models/validation.md) 자동으로 수행 하는 클라이언트 및 서버 쪽 유효성 검사

## <a name="client-side-development"></a>클라이언트 쪽 개발

ASP.NET Core 다양 한 포함 한 클라이언트 쪽 프레임 워크와 원활 하 게 통합 되도록 설계 되었습니다 [AngularJS](client-side/angular.md), [KnockoutJS](client-side/knockout.md) 및 [부트스트랩](client-side/bootstrap.md)합니다. 참조 [클라이언트 쪽 개발](client-side/index.md) 대 한 자세한 내용은 합니다.

## <a name="next-steps"></a>다음 단계

시작 자습서를 참조 하십시오. [ASP.NET 핵심 자습서](tutorials/index.md)

ASP.NET 핵심 개념 및 아키텍처를 자세하게 소개를 참조 하십시오. [ASP.NET 핵심 기본](fundamentals/index.md)합니다.

ASP.NET 핵심 응용 프로그램에.NET Core 또는.NET Framework 런타임을 사용할 수 있습니다. 자세한 내용은 참조 [.NET Core와.NET Framework 간의 선택](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)합니다.



---
title: "ASP.NET Core 소개"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 09/03/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: index
ms.openlocfilehash: 748c8c0b9dd0e6eab0d7347bbf89ed80c10bdb54
ms.sourcegitcommit: e4a1df2a5a85f299322548809e547a79b380bb92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2017
---
# <a name="introduction-to-aspnet-core"></a>ASP.NET Core 소개

작성자: [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Shaun Luttin](https://twitter.com/dicshaunary)

ASP.NET Core는 클라우드 기반 인터넷에 연결된 최신 응용 프로그램을 빌드하기 위한 플랫폼 간 고성능 [오픈 소스](https://github.com/aspnet/home) 프레임워크입니다. ASP.NET Core를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

* 웹앱 및 서비스, [IoT](https://www.microsoft.com/en-us/internet-of-things/) 앱 및 모바일 백 엔드를 빌드합니다.
* Windows, macOS 및 Linux에서 즐겨 찾는 개발 도구를 사용합니다.
* 클라우드 또는 온-프레미스에 배포
* [.NET Core 또는.NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)를 실행합니다.

## <a name="why-use-aspnet-core"></a>ASP.NET Core를 사용하는 이유는 무엇인가요?

수백만 명의 개발자가 ASP.NET을 사용하여 웹 앱을 만들었습니다(계속 사용 중). ASP.NET Core는 간결한 모듈식 프레임워크를 만드는 아키텍처 변경 내용을 포함한 ASP.NET의 새로운 디자인입니다.

ASP.NET Core는 다음과 같은 이점을 제공합니다.

* 웹 UI 및 웹 API를 동일한 과정으로 빌드합니다.
* [최신 클라이언트 쪽 프레임워크](xref:client-side/index) 및 워크플로 개발을 통합합니다.
* 클라우드를 갖춘 환경 기반 [구성 시스템](xref:fundamentals/configuration)입니다.
* [종속성 주입](xref:fundamentals/dependency-injection)이 기본 제공됩니다.
* 간단한 고성능 모듈식 HTTP 요청 파이프라인을 포함합니다.
* IIS에서 호스트하거나 고유한 프로세스에서 자체 호스팅하는 기능이 있습니다.
* [.NET Core](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)를 실행하여 진정한 병렬 응용 프로그램 버전 관리를 지원합니다.
* 최신 웹 개발을 간소화하는 도구를 포함합니다.
* Windows, macOS 및 Linux에서 빌드하고 실행할 수 있습니다.
* 오픈 소스이며 커뮤니티에 중점을 둡니다.

ASP.NET Core는 완전히 [NuGet](https://www.nuget.org/) 패키지로 제공됩니다. 그러면 필요한 NuGet 패키지를 포함하도록 앱을 최적화할 수 있습니다. 작은 앱 노출 영역의 혜택에는 보안 강화, 서비스 절감, 성능 향상이 포함됩니다.

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>ASP.NET Core MVC를 사용하여 웹 API 및 웹 UI 빌드

ASP.NET Core MVC에서는 [웹 API](xref:tutorials/index#building-web-apis) 및 [웹앱](xref:tutorials/index#building-web-applications)을 빌드하는 데 유용한 기능을 제공합니다.

* [MVC(모델-뷰-컨트롤러) 패턴](xref:mvc/overview)을 통해 웹 API 및 웹앱을 [테스트 가능](testing/index.md)하게 합니다.
* [Razor 페이지](xref:mvc/razor-pages/index)(2.0의 새로운 기능)는 웹 UI를 쉽게 빌드하고 생산성을 높일 수 있는 페이지 기반 프로그래밍 모델입니다.
* [Razor 구문](xref:mvc/views/razor)은 [Razor 페이지](xref:mvc/razor-pages/index) 및 [MVC 뷰](xref:mvc/views/overview)에 생산적인 언어입니다.
* [태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용하면 서버 쪽 코드를 Razor 파일에서 HTML 요소를 만들고 렌더링하는 데 사용할 수 있습니다.
* [여러 데이터 형식 및 콘텐츠 협상](mvc/models/formatting.md)에 대한 기본 제공 지원을 통해 웹 API를 브라우저 및 모바일 장치를 포함한 다양한 클라이언트에 연결할 수 있습니다.
* [모델 바인딩](xref:mvc/models/model-binding)은 작업 메서드 매개 변수에 HTTP 요청의 데이터를 자동으로 매핑합니다.
* [유효성 검사 모델](xref:mvc/models/validation)은 자동으로 클라이언트와 서버 쪽 유효성 검사를 수행합니다.

## <a name="client-side-development"></a>클라이언트 쪽 개발

ASP.NET Core는 [AngularJS](xref:client-side/angular), [KnockoutJS](xref:client-side/knockout) 및 [부트스트랩](xref:client-side/bootstrap)을 비롯한 다양한 클라이언트 쪽 프레임워크와 원활하게 통합되도록 설계되었습니다. 자세한 내용은 [클라이언트 쪽 개발](client-side/index.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [ASP.NET Core 자습서](xref:tutorials/index)
* [ASP.NET Core 기본 사항](xref:fundamentals/index)
* [매주 ASP.NET 커뮤니티 스탠드업](https://live.asp.net/)은 팀의 진행률 및 계획을 다루고 새 블로그 및 타사 소프트웨어를 설명합니다.

---
title: "데이터 보호 소개"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4542cd37-b47c-454c-be19-d1b5810d67fe
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/introduction
ms.openlocfilehash: b7391fffd5d512c01af5d709755a925f739b59ba
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="introduction-to-data-protection"></a>데이터 보호 소개

웹 응용 프로그램은 종종 보안이 중요 한 데이터를 저장 해야 합니다. Windows 데스크톱 응용 프로그램에 대 한 DPAPI를 제공 하지만 웹 응용 프로그램에 적합 하지 않습니다. ASP.NET Core 데이터 보호 스택의 개발자 키 관리 및 회전을 포함 한 데이터를 보호 사용할 수 있는 간단 하 고 사용 하기 쉬운 암호화 API를 제공 합니다.

ASP.NET Core 데이터 보호 스택의 장기적으로 대체도 충족 하도록 디자인 되었습니다는 <machineKey> asp.net에서 요소 1.x-4.x 합니다. 사용 사례 최신 응용 프로그램 발생할 가능성이 높습니다. 대부분에 기본적으로 솔루션을 제공 하면서 다양 한 이전 암호화 스택의 단점을 해결 하기 위해 설계 되었습니다.

## <a name="problem-statement"></a>문제 설명

전체 문제 설명 단일 문장에 간결 하 게 정의할 수 있습니다: 지 속성 메커니즘이 신뢰할 하지만 나중에 검색에 대 한 신뢰할 수 있는 정보를 유지 하 고 싶습니다. 웹 측면에서이 수로 작성할 수 "싶습니다 신뢰할 수 없는 클라이언트를 통해 왕복 신뢰할 수 있는 상태로."

이 정규 예제는 인증 쿠키 또는 bearer 토큰입니다. 서버 생성 하는 "I am Groot 및 xyz 권한이" 토큰 되며 클라이언트에 전달 합니다. 미래의 특정 날짜에서 클라이언트는 서버에 다시 해당 토큰을 제시 합니다 되었지만 서버 몇 가지 종류의 보증 클라이언트 토큰을 위조 되지 않았습니다. 따라서 첫 번째 요구 사항: 신뢰성 (규칙 하위 무결성, 변조 방지)입니다.

지속된 상태 서버에서 신뢰할 수, 이후 예상이 상태는 운영 환경과 관련 된 정보를 포함 될 수 있습니다. 파일 경로, 사용 권한, 핸들 또는 다른 간접 참조의 형식 또는 다른 서버 관련 데이터가 몇 가지 수 있습니다. 이러한 정보 일반적으로 공개 되지 않아야 신뢰할 수 없는 클라이언트를 합니다. 따라서 두 번째 요구 사항: 기밀 유지 합니다.

마지막으로, 최신 응용 프로그램은 구성 요소화, 개괄적 이므로 개별 구성 요소는 시스템의 다른 구성 요소에 관계 없이이 시스템을 활용 하려고 합니다. 예를 들어, 전달자 토큰 구성 요소를이 스택에 사용 하는 경우 사용할 수 있는 동일한 스택을 앤티 CSRF 메커니즘에서 문제 없이 작동 해야 합니다. 따라서 최종 요구 사항: 격리 합니다.

가능해 집니다 추가 제약 조건 요구 사항 범위를 좁히려면 합니다. 암호화 시스템 내에서 작동 하는 모든 서비스는 동일 하 게 신뢰할 수 있도록 하 고 데이터를 생성 하거나 우리의 직접 제어를 받는 서비스 외부에서 사용 하지 않아도 가정 합니다. 또한 작업은 가능한 한 빠르게 각 요청 웹 서비스에 한 번 이상 암호화는 시스템을 통해 이동할 수 있으므로 필요 합니다. 대칭 암호화 시나리오에 대 한 이상적인 있고, 등으로 필요할 때 한 번까지 비대칭 암호화 할인 수 있습니다.

## <a name="design-philosophy"></a>디자인 원칙

기존 스택에 문제를 식별 하 여을 시작 했습니다. 했습니다, 서버가 기존 솔루션의 가로 조사 म 고 없는 기존 솔루션에 매우 둡니다 기능에 완료 합니다. 몇 가지 원칙에 따라 솔루션을 다음 설계 되었습니다.

* 시스템 구성의 단순성을 제공 해야 합니다. 이상적으로 시스템은 구성이 필요 없는 개발자가 실행 중인 정확 수 합니다. 개발자가 특정 측면 (예: 키 저장소)를 구성 해야 하는 경우에서 고려 간단한 특정 구성을 제공 하기 위해 지정 되어야 합니다.

* 단순 소비자 용 API를 제공 합니다. Api를 잘못 사용 하기 쉽고를 잘못 사용 하기 어려운 있어야 합니다.

* 개발자 키 관리 원칙을 배울 해야 합니다. 알고리즘 선택 및 개발자의를 대신 하 여 키 수명 시스템이 처리 해야 합니다. 이상적으로 개발자 원시 키 자료에 액세스를 권한이 하지 않습니다.

* 가능 하면 저장 된 상태의 키를 보호 해야 합니다. 시스템에서는 적절 한 기본 보호 메커니즘을 파악 하 고 자동으로 적용 해야 합니다.

이러한 원칙을 염두 우리는 간단한 개발 [사용 하기 쉬운](using-data-protection.md) 데이터 보호 스택의 합니다.

ASP.NET Core 데이터 보호 Api는 주로 없습니다 기밀 페이로드의 무한 지 속성. 와 같은 다른 기술은 [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) 및 [Azure 권한 관리](https://docs.microsoft.com/rights-management/) 무한 저장소 시나리오에 보다 적합 한까지 강력한 키 관리 기능을 갖습니다. 즉, 개발자는 ASP.NET Core 데이터 보호 Api를 사용 하 여 기밀 데이터의 장기 보호에 대 한 일은 없습니다.

## <a name="audience"></a>대상 사용자

데이터 보호 시스템 주 다섯 개의 패키지로으로 구분 됩니다. 이러한 Api의 다양 한 측면에는 세 가지 주요 대상; 대상

1. [소비자 Api 개요](consumer-apis/overview.md) 대상 프레임 워크 및 응용 프로그램 개발자.

   "싶지 스택 작동 하는 방법에 대 한 또는 구성 된 방식에 대 한 자세한 내용을 알아보려면 합니다. 단순히 하겠습니다 일부로 작업을 수행에 단순한 방식으로 가능한 Api를 사용 하 여 성공적으로 확률이 높은. "

2. [구성 Api](configuration/overview.md) 응용 프로그램 개발자 및 시스템 관리자가 대상으로 합니다.

   "기본이 아닌 경로 또는 설정 내 환경 필요 함을 데이터 보호 시스템에 알려 하고자 합니다."

3. 확장성 개발자는 Api 대상 사용자 지정 정책을 구현 하는 일을 담당 합니다. 이러한 Api에 드문 경우로 제한 되 고 개발자가 인식 하는 보안을 발생 합니다.

   "하고자 실제로 고유 동작 요구 사항을 때문에 시스템 내에서 전체 구성 요소를 대체 합니다. 하겠습니다 내 요구 사항을 충족 하는 플러그 인을 작성 하기 위해 API 화면의 프로토콜과 사용 하지 않는 부분에 알아보려면. "

## <a name="package-layout"></a>패키지 레이아웃

데이터 보호 스택의 다섯 개의 패키지로 구성 됩니다.

* Microsoft.AspNetCore.DataProtection.Abstractions 기본 IDataProtectionProvider 및 IDataProtector 인터페이스를 포함합니다. 또한 이러한 형식 (예: 오버 로드의 IDataProtector.Protect) 작업을 지원할 수 있는 유용한 확장 메서드를 포함 합니다. 자세한 내용은 소비자 인터페이스 단원을 참조 하십시오. 다른 사람에 게는 데이터 보호 시스템 인스턴스화를 담당 하는 경우 단순히 Api를 사용 하는 Microsoft.AspNetCore.DataProtection.Abstractions 참조 합니다.

* Microsoft.AspNetCore.DataProtection 핵심 암호화 작업, 키 관리, 구성 및 확장성을 포함 하 여 데이터 보호 시스템의 핵심 구현을 포함 합니다. 데이터 보호 시스템 인스턴스화를 담당 하는 경우 (예:에 추가 IServiceCollection)를 수정 하거나 해당 동작을 확장 해야 Microsoft.AspNetCore.DataProtection 참조 또는 합니다.

* Microsoft.AspNetCore.DataProtection.Extensions는 개발자가 유용할 수 있지만 코어 패키지에 속해 있지 않습니다는 추가 Api를 포함 합니다. 예를 들어,이 패키지는 간단한 "종속성 주입 설치 하지 않고도 특정 키 저장소 디렉터리를 가리키는 시스템 인스턴스화할" API (추가 정보)를 포함합니다. 또한 보호 된 페이로드 (추가 정보)의 수명을 제한 하기 위한 확장 메서드를 포함 합니다.

* Microsoft.AspNetCore.DataProtection.SystemWeb 리디렉션하려면 기존 ASP.NET 4.x 응용 프로그램에 설치할 수 있습니다는 <machineKey> 대신 새 데이터 보호 스택을 사용 하 여 작업 합니다. 참조 [호환성](compatibility/replacing-machinekey.md#compatibility-replacing-machinekey) 자세한 정보에 대 한 합니다.

* Microsoft.AspNetCore.Cryptography.KeyDerivation 루틴 해시 PBKDF2 암호의 구현을 제공 하 고 사용자 암호를 안전 하 게 처리 하는 데 필요한 시스템에서 사용 될 수 있습니다. 참조 [암호 해시](consumer-apis/password-hashing.md) 자세한 정보에 대 한 합니다.

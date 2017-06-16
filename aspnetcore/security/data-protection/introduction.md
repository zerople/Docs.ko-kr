---
title: "데이터 보호 소개 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4542cd37-b47c-454c-be19-d1b5810d67fe
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/introduction
ms.translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: e7f2c5527c369dbf2fae7d547f956a62b3cc9719
ms.contentlocale: ko-kr
ms.lasthandoff: 03/23/2017

---
# <a name="introduction-to-data-protection"></a>데이터 보호 소개

종종 웹 응용 프로그램 보안에 민감한 데이터를 저장 해야 합니다. Windows 데스크톱 응용 프로그램에 DPAPI를 제공 하지만이 웹 응용 프로그램에 적합 하지 않습니다. ASP.NET 핵심 데이터 보호 스택 개발자는 키 관리 및 회전을 포함 하 여 데이터를 보호 하는 데 사용할 수는 간단 하 고 사용 하기 쉬운 암호화 API를 제공 합니다.

ASP.NET Core 데이터 보호 스택 장기적으로 대체도 충족 하도록 디자인 되었습니다는 <machineKey> 요소 asp.net에서 1.x-4.x입니다. 대부분의 최신 응용 프로그램은이 발생할 가능성이 사용 사례에는 기본적으로 솔루션을 제공 하면서 다양 한 이전 암호화 스택의 단점을 해결 하기 위해 설계 되었습니다.

## <a name="problem-statement"></a>문제 설명

전반적인 문제는 단일 문장 내에서 간결 하 게 정의할 수 있습니다: 나중에 검색에 대 한 신뢰할 수 있는 정보를 유지 해야 하지만 지 속성 메커니즘 신뢰할 수 없습니다. 웹 용어로이 수로 작성할 수 "I need to는 신뢰할 수 없는 클라이언트를 통해 왕복 신뢰할 수 있는 상태입니다."

정식 예로 인증 쿠키 또는 bearer 토큰입니다. 생성 합니다.는 "I Groot 하 xyz 권한이" 토큰 및 클라이언트에 전달 합니다. 나중에 클라이언트는 서버에 다시 해당 토큰을 표시 하 하지만 서버 일종의 클라이언트는 토큰을 위조 되지 않은 보증 합니다. 따라서 첫 번째 요구 사항: 인증 (규칙 하위 무결성, 변조 방지)입니다.

유지 된 상태를 서버에 의해 신뢰 하므로 예상이 상태는 운영 환경과 관련 된 정보를 포함 될 수 있습니다. 서버 관련 데이터의 몇 가지 다른 부분이 나 파일 경로, 권한, 핸들 또는 다른 간접 참조의 형태로 수 있습니다. 이러한 정보 일반적으로 공개 해서는 안을 신뢰할 수 없는 클라이언트입니다. 따라서 두 번째 요구 사항: 기밀 유지 합니다.

마지막으로, 최신 응용 프로그램은 구성 요소화를 살펴본 내용을 이므로 개별 구성 요소는 시스템의 다른 구성 요소에 관계 없이이 시스템을 활용 하려고 합니다. 예를 들어, 전달자 토큰 구성 요소이 스택이를 사용 하는 경우 사용할 수 있는 동일한 스택에서 앤티 CSRF 메커니즘에서 문제 없이 작동 해야 합니다. 따라서 마지막 요구 사항은: 격리 합니다.

가능해 집니다 추가 제약 조건 요구 사항은의 범위를 좁히려면 합니다. 암호화는 시스템 내에서 작업을 수행 하는 모든 서비스는 동일 하 게 신뢰할 수 있도록 하 고 데이터를 생성 하거나 직접 우리의 제어 서비스 외부에서 사용 하지 않아도 가정 합니다. 또한 작업은 가능한 한 빠르게 하는 웹 서비스 요청 전환 되는 암호화 시스템 한 번 이상 있으므로 필요 합니다. 따라서 대칭 암호화 시나리오에 적합 하 고 비대칭 암호화 등으로 필요할 때 한 번까지 할인 수 있습니다.

## <a name="design-philosophy"></a>디자인 원칙

기존 스택 문제를 확인 하 여 시작 했습니다. 된 후, 기존 솔루션의 대상으로 설문 조사 고 없는 기존 솔루션에 상당히 둡니다 기능에 완료 했습니다. 몇 가지 기본 원칙에 따라 솔루션을 다음 설계 되었습니다.

* 시스템 구성의 단순성을 제공 해야 합니다. 이상적으로 시스템에는 구성이 필요 없는 것 누릅니다 개발자 수 구축을 시작 합니다. 개발자가 특정 측면 (예: 키 저장소)를 구성 해야 하는 경우에 고려해 야에 있어 이러한 특정 구성이 간단 합니다.

* 간단한 소비자 용 API를 제공 합니다. Api는 올바르게 사용 하기 쉬운 올바르게 사용 하기 어려운 이어야 합니다.

* 개발자 키 관리 원칙을 익혀야 합니다. 알고리즘 선택 및 개발자를 대신 하 여 키 수명 시스템이 처리 해야 합니다. 이상적으로 개발자 원시 키 자료에 액세스할 수 조차 알아채지 못할 있어야 합니다.

* 가능한 경우 상태일 때 키를 보호 되어야 합니다. 시스템은 적절 한 기본 보호 메커니즘을 파악 하 고 자동으로 적용 해야 합니다.

이러한 원칙에에서 우리는 간단한 개발 [사용 하기 쉬운](using-data-protection.md) 데이터 보호 스택.

ASP.NET 핵심 데이터 보호 Api는 주로 없습니다의 기밀 페이로드는 무한 한 지 속성. 와 같은 다른 기술은 [Windows CNG DPAPI](https://msdn.microsoft.com/en-us/library/windows/desktop/hh706794%28v=vs.85%29.aspx) 및 [Azure 권한 관리](https://technet.microsoft.com/en-us/library/jj585024.aspx) 무기한 저장 하는 시나리오에 보다 적합 한까지 강력한 키 관리 기능을 갖습니다. 즉, ASP.NET 핵심 데이터 보호 Api를 사용 하 여 기밀 데이터의 장기 보호에 대 한 개발자를 차단 하면 내용이 없습니다.

## <a name="audience"></a>대상 사용자

데이터 보호 시스템은 다섯 가지 주요 패키지로 나뉩니다. 이러한 Api의 다양 한 측면을 세 가지 주요 대상을; 대상

1. [소비자 Api 개요](consumer-apis/overview.md) 응용 프로그램 및 프레임 워크 개발자를 대상으로 합니다.

   "안 스택의 작동 하는 방법에 대 한 또는 구성 된 방식에 대 한 자세한 내용을 알아보려면 합니다. 단순히 하려는 일부 작업을 수행에 단순한 방식으로 가능한 Api를 사용 하 여 성공적으로 높은 확률. "

2. [구성 Api](configuration/overview.md) 응용 프로그램 개발자와 시스템 관리자를 대상으로 합니다.

   "내 환경에 기본이 아닌 경로 또는 설정이 필요 데이터 보호 시스템에 알려 하고자 합니다."

3. 확장성 개발자는 Api 대상 사용자 지정 정책 구현 담당 합니다. 이러한 Api의 사용 경험을 개발자가 인식 하는 보안 고 드문 경우로 제한 됩니다.

   "해야 실제로 고유 동작 요구 사항 때문에 시스템 내에서 하는 전체 구성 요소를 교체 합니다. 하겠습니다 내 요구 사항을 충족 하는 플러그 인을 만들려면 API 화면의 프로토콜과 사용 하지 않는 부분에 알아봅니다. "

## <a name="package-layout"></a>패키지 레이아웃

데이터 보호 스택 다섯 개의 패키지로 구성 됩니다.

* Microsoft.AspNetCore.DataProtection.Abstractions 기본 IDataProtectionProvider 및 IDataProtector 인터페이스를 포함합니다. 또한 이러한 형식 (예: 오버 로드의 IDataProtector.Protect) 작업을 지원할 수 있는 유용한 확장 메서드를 포함 합니다. 자세한 내용은 소비자 인터페이스 섹션을 참조 합니다. 다른 사용자는 데이터 보호 시스템 인스턴스화를 담당 하는 경우 단순히 Api를 사용 하는 Microsoft.AspNetCore.DataProtection.Abstractions 참조 해야 합니다.

* Microsoft.AspNetCore.DataProtection 핵심 구현의 핵심 암호화 작업, 키 관리, 구성 및 확장성을 포함 하 여 데이터 보호 시스템에 포함 되어 있습니다. 데이터 보호 시스템 인스턴스화를 담당 하는 경우 (예:에 추가 된 IServiceCollection) 또는 참조 Microsoft.AspNetCore.DataProtection 합니다를 수정 하거나 해당 동작을 확장 합니다.

* Microsoft.AspNetCore.DataProtection.Extensions는 개발자 들이 유용할 수도 있지만 코어 패키지에 속하지 않는 추가 Api가 포함 되어 있습니다. 예를 들어이 패키지는 간단한 "종속성 주입 설치 하지 않고도 특정 키 저장소 디렉터리를 가리키는 시스템 인스턴스화할" API (추가 정보)를 포함합니다. 또한 보호 된 페이로드 (추가 정보)의 수명을 제한 하기 위한 확장 메서드를 포함 합니다.

* Microsoft.AspNetCore.DataProtection.SystemWeb 리디렉션할 기존 ASP.NET 4.x 응용 프로그램에 설치할 수 있습니다 해당 <machineKey> 대신 새로운 데이터 보호 스택을 사용 하 여 작업 합니다. 참조 [호환성](compatibility/replacing-machinekey.md#compatibility-replacing-machinekey) 에 대 한 자세한 내용은 합니다.

* Microsoft.AspNetCore.Cryptography.KeyDerivation은 PBKDF2 암호 해시 루틴의 구현을 제공 하 고 사용자 암호를 안전 하 게 처리 해야 하는 시스템에서 사용할 수 있습니다. 참조 [암호 해시](consumer-apis/password-hashing.md) 에 대 한 자세한 내용은 합니다.


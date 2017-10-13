---
title: "ASP.NET Core의 목적은 문자열"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 9d18c287-e0e6-4541-b09c-7fed45c902d9
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: b25af7c1f4dd3c63734290e6ac82e2e30a030c61
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2017
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>용도 계층 및 ASP.NET 코어의 다중 테 넌 트

이므로 IDataProtector도 암시적으로 IDataProtectionProvider, 목적으로 함께 연결할 수 있습니다. 이 의미 공급자입니다. (["Purpose1", "purpose2"]) CreateProtector 공급자 하는 것과 같습니다. CreateProtector("purpose1") 합니다. CreateProtector("purpose2") 합니다.

따라서 데이터 보호 시스템을 통해 몇 가지 흥미로운 계층 관계가 있습니다. 이전 예제에서 [Contoso.Messaging.SecureMessage](purpose-strings.md#data-protection-contoso-purpose), SecureMessage 구성 요소는 공급자를 호출할 수 있습니다. CreateProtector("Contoso.Messaging.SecureMessage") 선행 되 면 및 캐시에 개인 결과 `_myProvide` 필드입니다. 향후 보호기에 대 한 호출을 통해 만들 수 있습니다 `_myProvider.CreateProtector("User: username")`, 있으며, 개별 메시지 보안에 대 한 이러한 보호 기능 사용 합니다.

이 전환 수도 있습니다. 자체 인증 및 상태 관리 시스템으로 구성할 수 있습니다 (CMS 보이는 합리적인) 여러 명 그리고 경우 각 테 넌 트를 호스팅하는 단일 논리적 응용 프로그램을 고려 합니다. 포괄적인 응용 프로그램에 단일 마스터 공급자 및 공급자를 호출 합니다. CreateProtector ("Tenant 1") 및 공급자입니다. CreateProtector ("테 넌 트 2")를 각 테 넌 트 데이터 보호 시스템의 격리 된 자체 분할 영역을 제공 합니다. 테 넌 트 자신의 요구에 따라 자신의 개별 보호기 파생 수 있지만 시도 다하고 관계 없이 만들 수는 없습니다 보호기 충돌 하는 다른 테 넌 트와 시스템에서. 그래픽으로 아래와 같이 표시 됩니다.

![다중 테 넌 트 목적](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> 이 가정는 포괄적인 응용 프로그램 컨트롤의 어떤 Api 개별 테 넌 트가 사용할 수 있으며 테 넌 트가 서버에서 임의 코드를 실행할 수 없습니다. 경우 테 넌 트 임의 코드를 실행할 수 있습니다, 격리 보증을 중단 하기 위해 개인 리플렉션을 수행 수 또는 마스터 키 자료를 직접 읽기 및 모든 하위 키를 파생 방금 수 하 게 하려고 합니다.

데이터 보호 시스템에는 실제로 일종의 다중 테 넌 트의 기본의 기본 구성을 사용합니다. 기본적으로 마스터 키 자료는 사용자 프로필 폴더에 작업자 프로세스 계정 (또는 IIS 응용 프로그램 풀 id에 대 한 레지스트리)에 저장 됩니다. 하지만 실제로 매우 일반적으로 단일 계정을 사용 하 여 여러 응용 프로그램을 실행 하 고 있으므로 이러한 모든 응용이 프로그램 결국에 마스터 키 키 자료를 공유 합니다. 이 해결 하려면 데이터 보호 시스템 전체 목적 체인의 첫 번째 요소로 응용 당 고유 식별자를 자동으로 삽입 합니다. 이 암시적 목적에 서비스 [개별 응용 프로그램을 격리](../configuration/overview.md#data-protection-configuration-per-app-isolation) 에서 시스템 및 보호기 생성 프로세스 내에서 고유한 테 넌 트 위의 그림에서와 동일한 검색 하는 대로 각 응용 프로그램을 효과적으로 처리 하 여 다른 합니다.

---
title: "키 관리 및 수명"
author: rick-anderson
description: "키 관리 및 수명에 설명합니다."
keywords: "ASP.NET Core 키 관리, DPAPI를 DataProtection"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: ef7dad2a-7029-4ae5-8f06-1fbebedccaa4
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: c361af7d336fc0f7651e5d2f28d71515e2949c65
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="key-management-and-lifetime"></a>키 관리 및 수명

<a name=data-protection-default-settings></a>

## <a name="key-management"></a>키 관리

시스템 운영 환경을 검색 하 고 좋은 구성이 필요 없는 동작 기본값을 제공 하려고 합니다. 사용 되는 추론은 다음과 같습니다.

1. 시스템은 Azure 웹 사이트에서 호스팅 중 이라면 키 "%HOME%\ASP.NET\DataProtection-Keys" 폴더에 저장 됩니다. 이 폴더는 네트워크 저장소에서 지원 하 고 응용 프로그램을 호스팅하는 모든 컴퓨터에서 동기화 됩니다. 저장 된 상태의 키를 보호 되지 않습니다. 이 폴더에서 단일 배포 슬롯에서 응용 프로그램의 모든 인스턴스에 키 링을 제공합니다. 스테이징 및 프로덕션, 같은 별도 배포 슬롯 키 링을 공유 하지 않습니다. 예를 들어 스테이징 및 프로덕션을 교체 하거나 A를 사용 하 여 배포 슬롯 간에 교환 / B 테스트, 데이터 보호를 사용 하 여 모든 시스템 이전 슬롯 내 키 링을 사용 하 여 저장 된 데이터를 해독할 수 없습니다. 데이터 보호를 사용 하 여 쿠키를 보호 하기 위해 표준 ASP.NET 쿠키 미들웨어를 사용 하 여 ASP.NET 응용 프로그램에서 기록 되 고 사용자에 게이 일으킵니다. 슬롯에 관계 없이 키 링을 원하는 경우 Azure Blob 저장소, Azure 주요 자격 증명 모음 SQL 저장소 등의 외부 키 링 공급자를 사용 하거나 Redis 캐시 합니다.

2. 사용자 프로필을 사용할 수 있는 경우 키 "%LOCALAPPDATA%\ASP.NET\DataProtection-Keys" 폴더에 저장 됩니다. 또한 운영 체제가 Windows 인 경우 DPAPI를 사용 하 여 암호화 되어야 합니다.

3. 응용 프로그램을 IIS에서 호스트 하는 경우 키 하 게 포함 되었는지 작업자 프로세스 계정에만 있는 특별 한 레지스트리 키에서 HKLM 레지스트리 에서도 유지 됩니다. 미사용 키는 DPAPI를 사용하여 암호화됩니다.

4. 이러한 조건 중가 일치 하는 경우 현재 프로세스 외부에서 키 유지 되지 않습니다. 종료 되 면 프로세스 생성 된 모든 키가 손실 됩니다.

개발자는 항상 모든 권한 및 키가 저장 하는 방법과 재정의할 수 있습니다. 위의 처음 세 옵션 해야 방법과 유사 대부분 응용 프로그램에 대 한 좋은 기본값 ASP.NET <machineKey> 자동 생성 루틴 이전에 작동 합니다. 최종 대체 백 옵션은 진정으로 지정 해야 하는 유일한 시나리오 [구성](overview.md) 선행 키 지 속성을 원하는 하지만이 대체 드문 경우에만 발생 합니다.

>[!WARNING]
> 개발자는이 추론을 재정의 하 고 데이터 보호 시스템에 특정 키 저장소를 가리키는, 미사용 키의 자동 암호화 비활성화 됩니다. 저장 된 상태의 보호를 통해 다시 사용 되도록 설정 될 수 있습니다 [구성](overview.md)합니다.

## <a name="key-lifetime"></a>키 수명

기본적으로 키에는 90 일 수명을 가집니다. 키가 만료 되 면 시스템은 자동으로 새 키를 생성 하 여 새 키를 활성 키로 설정. 사용 중지 된 키 사람과 보호 되는 모든 데이터를 해독할 수 여전히 시스템에 남아 있습니다. 참조 [키 관리](../implementation/key-management.md#data-protection-implementation-key-management-expiration) 자세한 정보에 대 한 합니다.

## <a name="default-algorithms"></a>기본 알고리즘

기본 페이로드 보호 알고리즘 사용은 AES-256-CBC 기밀성 및 HMACSHA256에 대 한 신뢰성에 대 한 합니다. 페이로드 당 별로 이러한 알고리즘에 사용 되는 두 개의 하위 키를 파생 롤백 90 일 마다 512 비트 마스터 키가 사용 됩니다. 참조 [파생 하위](../implementation/subkeyderivation.md#data-protection-implementation-subkey-derivation-aad) 자세한 정보에 대 한 합니다.

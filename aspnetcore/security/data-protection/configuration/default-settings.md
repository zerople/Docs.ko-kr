---
title: "키 관리 및 수명 | Microsoft 문서"
author: rick-anderson
description: "키 관리 및 수명에 설명 합니다."
keywords: "ASP.NET Core 키 관리, DPAPI를 DataProtection"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: ef7dad2a-7029-4ae5-8f06-1fbebedccaa4
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/default-settings
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 8b60ae69bf54761be8e04ff560e431627244ee96
ms.lasthandoff: 03/23/2017

---
# <a name="key-management-and-lifetime"></a>키 관리 및 수명

<a name=data-protection-default-settings></a>

## <a name="key-management"></a>키 관리

시스템 운영 환경을 검색 하 고 좋은 구성이 필요 없는 동작 기본값을 제공 하려고 합니다. 추론을 사용 하는 다음과 같습니다.

1. 시스템은 Azure 웹 사이트에서 호스팅 중 이라면 키 "%HOME%\ASP.NET\DataProtection-Keys" 폴더에 저장 됩니다. 이 폴더는 네트워크 저장소에서 지원 하 고 응용 프로그램을 호스팅하는 모든 컴퓨터에서 동기화 되 면 합니다. 키는 상태일 때 보호 되지 않습니다. 이 폴더는 단일 배포 슬롯에서 응용 프로그램의 모든 인스턴스에 키 고리를 제공합니다. 별도 배포 슬롯을 스테이징 및 프로덕션와 같은 키 고리를 공유 하지 않습니다. 예를 들어 스테이징 및 프로덕션 교환 또는 A를 사용 하 여 배포 슬롯 간에 교환할 때 / B 테스트, 데이터 보호를 사용 하 여 모든 시스템 키 고리를 사용 하 여 이전 슬롯 안에 저장 된 데이터를 해독할 수 없습니다. 데이터 보호를 사용 하 여 쿠키를 보호 하기 위해 표준 ASP.NET 쿠키 미들웨어를 사용 하 여 ASP.NET 응용 프로그램에서 기록 되는 사용자가 발생 합니다. 슬롯에 독립적인 키 고리를 원하는 경우 Azure Blob 저장소, Azure 키 자격 증명 모음 SQL 저장소와의 외부 키 고리 공급자를 사용 하거나 Redis 캐시 합니다.

2. 사용자 프로필을 사용할 수 있는 경우 키 "%LOCALAPPDATA%\ASP.NET\DataProtection-Keys" 폴더에 저장 됩니다. 또한 운영 체제가 Windows 인 경우 DPAPI를 사용 하 여 나머지에 암호화 되어야 합니다.

3. 응용 프로그램, IIS에서 호스팅되는 경우 키 HKLM 레지스트리에 하 게 포함 되었는지 작업자 프로세스 계정에만 있는 특별 한 레지스트리 키에 저장 됩니다. DPAPI를 사용 하 여 나머지 키 암호화 됩니다.

4. 이러한 조건이 일치 하지 않는 경우 현재 프로세스 외부에서 키 유지 되지 않습니다. 종료 될 때 프로세스 생성 된 모든 키를 손실 됩니다.

개발자는 항상 모든 권한 및 키는 어디에 저장 되는 방법과 재정의할 수 있습니다. 위의 처음 세 가지 옵션은 대부분의 응용 프로그램과 유사한 방법에 대 한 좋은 기본값 ASP.NET <machineKey> 자동 생성 루틴 이전에 작동 합니다. 최종, 대체 옵션은 진정으로 지정 하는 개발자를 필요로 하는 유일한 시나리오 [구성](overview.md) 선행 키 지 속성을 원하는 경우이 대체 드문 경우에만 발생 합니다.

>[!WARNING]
> 개발자는이 추론을 재정의 하 고 데이터 보호 시스템에 특정 키 저장소를 가리키는, 휴지 키의 자동 암호화 비활성화 됩니다. 상태일 때 보호를 통해 다시 설정할 수 있습니다 [구성](overview.md)합니다.

## <a name="key-lifetime"></a>키 수명

기본적으로 키에는 90 일 수명을 가집니다. 키가 만료 되 면 하면 시스템이 자동으로 새 키 생성 되며 새 키를 활성 키로 설정 합니다. 으로 사용 중지 된 키와 보호 되는 데이터를 해독할 수 여전히 시스템에 유지 됩니다. 참조 [키 관리](../implementation/key-management.md#data-protection-implementation-key-management-expiration) 에 대 한 자세한 내용은 합니다.

## <a name="default-algorithms"></a>기본 알고리즘

기본 페이로드 보호 알고리즘 사용은&256;-AES-CBC 기밀성 및 HMACSHA256에 대 한 신뢰성에 대 한 합니다. 페이로드 당 별로 이러한 알고리즘에 사용 되는 두 개의 하위 키를 파생 90 일 마다 롤백 512 비트 마스터 키가 사용 됩니다. 참조 [파생 하위](../implementation/subkeyderivation.md#data-protection-implementation-subkey-derivation-aad) 에 대 한 자세한 내용은 합니다.


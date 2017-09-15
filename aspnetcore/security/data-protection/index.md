---
title: "ASP.NET Core에서 데이터 보호"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 1f402da8-1052-4970-9835-9f9f16a02dbc
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/index
ms.openlocfilehash: 39f2ca96f8542de033274ea957b5c7736948c981
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="data-protection-in-aspnet-core-consumer-apis-configuration-extensibility-apis-and-implementation"></a>ASP.NET Core에서 데이터 보호: 소비자 API, 구성, 확장성 API 및 구현

* [데이터 보호 소개](introduction.md)

* [데이터 보호 API 시작](using-data-protection.md)

* [소비자 API](consumer-apis/index.md)

  * [소비자 API 개요](consumer-apis/overview.md)

  * [용도 문자열](consumer-apis/purpose-strings.md)

  * [용도 계층 구조 및 다중 테넌트](consumer-apis/purpose-strings-multitenancy.md)

  * [암호 해시](consumer-apis/password-hashing.md)

  * [보호된 페이로드의 수명 제한](consumer-apis/limited-lifetime-payloads.md)

  * [호출된 키가 속한 페이로드 보호 해제](consumer-apis/dangerous-unprotect.md)

* [구성](configuration/index.md)

  * [데이터 보호 구성](configuration/overview.md)

  * [기본 설정](configuration/default-settings.md)

  * [컴퓨터 수준 정책](configuration/machine-wide-policy.md)

  * [비 DI 인식 시나리오](configuration/non-di-scenarios.md)

* [확장성 API](extensibility/index.md)

  * [Core 암호화 확장성](extensibility/core-crypto.md)

  * [키 관리 확장성](extensibility/key-management.md)

  * [기타 API](extensibility/misc-apis.md)

* [구현](implementation/index.md)

  * [인증된 암호화 세부 정보.](implementation/authenticated-encryption-details.md)

  * [하위 키 파생 및 인증된 암호화](implementation/subkeyderivation.md)

  * [컨텍스트 헤더](implementation/context-headers.md)

  * [키 관리](implementation/key-management.md)

  * [키 저장소 공급자](implementation/key-storage-providers.md)

  * [미사용 키 암호화](implementation/key-encryption-at-rest.md)

  * [키 불변성 및 설정 변경](implementation/key-immutability.md)

  * [키 저장소 형식](implementation/key-storage-format.md)

  * [삭제되는 데이터 보호 공급자](implementation/key-storage-ephemeral.md)

* [호환성](compatibility/index.md)

  * [응용프로그램 쿠키 공유](compatibility/cookie-sharing.md)

  * [ASP.NET에서 <machineKey> 바꾸기](compatibility/replacing-machinekey.md)

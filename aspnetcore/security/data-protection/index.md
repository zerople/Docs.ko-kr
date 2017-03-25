---
title: "ASP.NET Core에서 데이터 보호 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 1f402da8-1052-4970-9835-9f9f16a02dbc
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/index
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: a4529cfbe7c2cfa3391c751ffa5e0c4ac6528bbc
ms.lasthandoff: 03/23/2017

---
# <a name="data-protection-in-aspnet-core-consumer-apis-configuration-extensibility-apis-and-implementation"></a>ASP.NET Core에서 데이터 보호: 소비자 Api, 구성, 확장성 Api 및 구현

* [데이터 보호 소개](introduction.md)

* [데이터 보호 Api를 시작 하기](using-data-protection.md)

* [소비자 Api](consumer-apis/index.md)

  * [소비자 Api 개요](consumer-apis/overview.md)

  * [목적은 문자열](consumer-apis/purpose-strings.md)

  * [목적은 계층 구조 및 다중 테 넌 트](consumer-apis/purpose-strings-multitenancy.md)

  * [암호 해시](consumer-apis/password-hashing.md)

  * [보호 된 페이로드의 수명을 제한](consumer-apis/limited-lifetime-payloads.md)

  * [보호 해제 페이로드 키를 갖는 해지 되었습니다.](consumer-apis/dangerous-unprotect.md)

* [구성](configuration/index.md)

  * [데이터 보호를 구성합니다.](configuration/overview.md)

  * [기본 설정](configuration/default-settings.md)

  * [컴퓨터 단위 정책](configuration/machine-wide-policy.md)

  * [비 DI 인식 시나리오](configuration/non-di-scenarios.md)

* [확장성 Api](extensibility/index.md)

  * [핵심 암호화 확장성](extensibility/core-crypto.md)

  * [키 관리 확장성](extensibility/key-management.md)

  * [기타 Api](extensibility/misc-apis.md)

* [구현](implementation/index.md)

  * [암호화 정보를 인증합니다.](implementation/authenticated-encryption-details.md)

  * [하위 키 파생 및 인증 된 암호화](implementation/subkeyderivation.md)

  * [컨텍스트 헤더](implementation/context-headers.md)

  * [키 관리](implementation/key-management.md)

  * [키 저장소 공급자](implementation/key-storage-providers.md)

  * [상태일 때 키 암호화](implementation/key-encryption-at-rest.md)

  * [키 불변성과 설정 변경](implementation/key-immutability.md)

  * [키 저장소 형식](implementation/key-storage-format.md)

  * [임시 데이터 보호 기능 공급자](implementation/key-storage-ephemeral.md)

* [호환성](compatibility/index.md)

  * [응용 프로그램 간에 쿠키를 공유합니다.](compatibility/cookie-sharing.md)

  * [대체 <machineKey> asp.net](compatibility/replacing-machinekey.md)


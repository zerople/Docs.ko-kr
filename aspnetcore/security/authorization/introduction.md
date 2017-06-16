---
title: "소개 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a6a556ed-ba59-4107-9358-44cf20e5931b
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/introduction
ms.translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 5305b451f9f6d7af83644e6205ff737c0bd10c3a
ms.contentlocale: ko-kr
ms.lasthandoff: 03/23/2017

---
# <a name="introduction"></a>소개

<a name=security-authorization-introduction></a>

권한 부여 동작을 결정 하는 프로세스에는 사용자가 작업을 수행할 수 있습니다. 예를 들어 관리자는 사용자가 문서 라이브러리 만들기, 문서 추가, 문서를 편집 및 삭제를 허용 됩니다. 라이브러리와 함께 일 하는 관리자 이외의 사용자는 문서를 읽을 권한이.

권한 부여는 직각의 사용자를 익명인 프로세스는 인증에서 독립적입니다. 인증 현재 사용자에 대 한 하나 이상의 id를 만들 수 있습니다.

## <a name="authorization-types"></a>인증 형식

ASP.NET Core 지금 권한 부여 제공 간단한 선언적 [역할](roles.md#security-authorization-role-based) 및 [다양 한 정책 기반](policies.md#security-authorization-policies-based) 권한 부여 요구 사항에 표시 되는 처리기 요구 사항에 대해 사용자 클레임을 평가 하는 모델입니다. 명령적 검사는 간단한 정책 또는 사용자 id와 사용자가 액세스를 시도 하는 리소스의 속성을 평가 하는 정책을 기반으로 사용할 수 있습니다.

## <a name="namespaces"></a>네임스페이스

권한 부여 구성 요소를 포함 하는 `AuthorizeAttribute` 및 `AllowAnonymousAttribute` 특성에서 발견 되는 `Microsoft.AspNetCore.Authorization` 네임 스페이스입니다.


---
title: "소개"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a6a556ed-ba59-4107-9358-44cf20e5931b
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/introduction
ms.openlocfilehash: fa6dcbc0627181cd1aca0926fa008f3db907742f
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="introduction"></a><span data-ttu-id="bac2f-103">소개</span><span class="sxs-lookup"><span data-stu-id="bac2f-103">Introduction</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="bac2f-104">권한 부여 작업을 결정 하는 프로세스를 사용자가 수행할 수 있게 합니다.</span><span class="sxs-lookup"><span data-stu-id="bac2f-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="bac2f-105">예를 들어 관리자가 문서 라이브러리 만들기, 문서를 추가, 문서, 편집 및 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bac2f-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="bac2f-106">라이브러리와 함께 일 하는 관리자가 아닌 사용자는 문서를 읽을 권한이.</span><span class="sxs-lookup"><span data-stu-id="bac2f-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="bac2f-107">권한 부여는 직각 인증에서 사용자가 누구를 구축 방향을 얻기 위한 하는 프로세스에서 독립적입니다.</span><span class="sxs-lookup"><span data-stu-id="bac2f-107">Authorization is orthogonal and independent from authentication, which is the process of ascertaining who a user is.</span></span> <span data-ttu-id="bac2f-108">인증 현재 사용자에 대 한 하나 이상의 id를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bac2f-108">Authentication may create one or more identities for the current user.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="bac2f-109">인증 형식</span><span class="sxs-lookup"><span data-stu-id="bac2f-109">Authorization Types</span></span>

<span data-ttu-id="bac2f-110">ASP.NET Core 권한 부여 제공 선언적 간단한 [역할](roles.md#security-authorization-role-based) 및 [풍부한 정책 기반](policies.md#security-authorization-policies-based) 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="bac2f-110">ASP.NET Core authorization provides a simple declarative [role](roles.md#security-authorization-role-based) and a [rich policy based](policies.md#security-authorization-policies-based) model.</span></span> <span data-ttu-id="bac2f-111">권한 부여 요구 사항에서 나타나며 처리기 요구 사항에 대해 사용자 클레임을 평가 합니다.</span><span class="sxs-lookup"><span data-stu-id="bac2f-111">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="bac2f-112">명령적 검사는 단순한 정책 또는 사용자 id 및 사용자 액세스를 시도 하는 리소스의 속성을 평가 하는 정책을 기반으로 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bac2f-112">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="bac2f-113">네임스페이스</span><span class="sxs-lookup"><span data-stu-id="bac2f-113">Namespaces</span></span>

<span data-ttu-id="bac2f-114">권한 부여 구성 요소를 포함 하는 `AuthorizeAttribute` 및 `AllowAnonymousAttribute` 특성에서 발견 되는 `Microsoft.AspNetCore.Authorization` 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="bac2f-114">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

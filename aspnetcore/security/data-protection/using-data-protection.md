---
title: "데이터 보호 Api 시작"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 39b7a73c-29d4-4137-b311-49529adcf3cb
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 9489b55b1de626b77bcbe21cb9678e561b559099
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="getting-started-with-the-data-protection-apis"></a><span data-ttu-id="ab942-103">데이터 보호 Api 시작</span><span class="sxs-lookup"><span data-stu-id="ab942-103">Getting Started with the Data Protection APIs</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="ab942-104">가장 간단한 보호 데이터에는 다음 단계로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab942-104">At its simplest protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="ab942-105">데이터 보호 공급자에서 데이터 보호자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ab942-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="ab942-106">보호 하려는 데이터와 함께 Protect 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab942-106">Call the Protect method with the data you want to protect.</span></span>

3. <span data-ttu-id="ab942-107">다시 일반 텍스트로 변환 하려는 데이터를 사용 하 여 Unprotect 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab942-107">Call the Unprotect method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="ab942-108">ASP.NET SignalR 등 대부분의 프레임 워크는 이미 데이터 보호 시스템 구성 및 종속성 주입을 통해 액세스 하는 서비스 컨테이너에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab942-108">Most frameworks such as ASP.NET or SignalR already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="ab942-109">다음 예제에서는 종속성 주입을 위한 서비스 컨테이너를 구성 하 고 등록 하는 데이터 보호 스택의 DI 통해 데이터 보호 공급자를 수신, 보호기 및 보호 한 후 보호 해제 데이터 만들기</span><span class="sxs-lookup"><span data-stu-id="ab942-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data</span></span>

[!code-csharp[Main](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="ab942-110">하나 이상을 제공 해야 합니다는 보호기를 만들 때 [목적 문자열](consumer-apis/purpose-strings.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="ab942-110">When you create a protector you must provide one or more [Purpose Strings](consumer-apis/purpose-strings.md).</span></span> <span data-ttu-id="ab942-111">"녹색"의 목적은 문자열을 사용 하 여 만든 보호기를 "자주색"의 용도 보호기에서 제공 되는 데이터를 보호할 수 없게 됩니다 예를 들어, 용도 문자열 소비자 간에 격리를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab942-111">A purpose string provides isolation between consumers, for example a protector created with a purpose string of "green" would not be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="ab942-112">IDataProtectionProvider 및 IDataProtector의 인스턴스는 스레드로부터 안전 여러 호출자에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab942-112">Instances of IDataProtectionProvider and IDataProtector are thread-safe for multiple callers.</span></span> <span data-ttu-id="ab942-113">구성 요소 호출 CreateProtector 통해 IDataProtector에 대 한 참조를 가져온 후 해당 참조에 사용할 보호 및 Unprotect를 여러 번 호출 하는 것이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab942-113">It is intended that once a component gets a reference to an IDataProtector via a call to CreateProtector, it will use that reference for multiple calls to Protect and Unprotect.</span></span>
>
><span data-ttu-id="ab942-114">보호 된 페이로드를 확인 하거나 해독할 수 없는 경우 보호 해제에 대 한 호출 CryptographicException 발생 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="ab942-114">A call to Unprotect will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="ab942-115">일부 구성 요소 오류를 무시 하려는 경우가 있을 수 하는 동안 작업을 보호 해제 인증 쿠키를 읽는 구성 요소 수 있습니다이 오류를 처리 하 고 전혀 쿠키가 없는 이전의 요청 처럼 처리할 대신 완전 한 요청에 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab942-115">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="ab942-116">구체적으로이 동작을 방지 하는 구성 요소는 모든 예외를 받아들이지 않고 CryptographicException을 catch 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab942-116">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>

---
title: "비 DI 인식 시나리오"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a7d8a962-80ff-48e3-96f6-8472b7ba2df9
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 54a930c26f9f48ea0e6f7865e2927bcde0f4d6c0
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="non-di-aware-scenarios"></a><span data-ttu-id="e0d53-103">비 DI 인식 시나리오</span><span class="sxs-lookup"><span data-stu-id="e0d53-103">Non DI aware scenarios</span></span>

<span data-ttu-id="e0d53-104">데이터 보호 시스템은 일반적으로 설계 [서비스 컨테이너에 추가할](../consumer-apis/overview.md) DI 메커니즘을 통해 종속 구성 요소에 제공 되 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e0d53-104">The data protection system is normally designed [to be added to a service container](../consumer-apis/overview.md) and to be provided to dependent components via a DI mechanism.</span></span> <span data-ttu-id="e0d53-105">그러나 일부 경우에이 작업이 불가능, 특히 시스템을 기존 응용 프로그램으로 가져올 때 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e0d53-105">However, there may be some cases where this is not feasible, especially when importing the system into an existing application.</span></span>

<span data-ttu-id="e0d53-106">이러한 시나리오를 지원 하기 위해 패키지 Microsoft.AspNetCore.DataProtection.Extensions DataProtectionProvider DI 특정 코드 경로 통과 하지 않고 데이터 보호 시스템을 사용 하는 간단한 방법을 제공 하는 구체적인 형식이 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0d53-106">To support these scenarios the package Microsoft.AspNetCore.DataProtection.Extensions provides a concrete type DataProtectionProvider which offers a simple way to use the data protection system without going through DI-specific code paths.</span></span> <span data-ttu-id="e0d53-107">형식 자체 IDataProtectionProvider를 구현 하며 생성 하는 것이 공급자의 암호화 키를 저장할 DirectoryInfo 제공 하는 것 처럼 쉽게</span><span class="sxs-lookup"><span data-stu-id="e0d53-107">The type itself implements IDataProtectionProvider, and constructing it is as easy as providing a DirectoryInfo where this provider's cryptographic keys should be stored.</span></span>

<span data-ttu-id="e0d53-108">예:</span><span class="sxs-lookup"><span data-stu-id="e0d53-108">For example:</span></span>

<span data-ttu-id="e0d53-109">[!code-none[Main](non-di-scenarios/_static/nodisample1.cs)]</span><span class="sxs-lookup"><span data-stu-id="e0d53-109">[!code-none[Main](non-di-scenarios/_static/nodisample1.cs)]</span></span>

>[!WARNING]
> <span data-ttu-id="e0d53-110">구체적인 형식은 DataProtectionProvider 기본적으로 원시 키 자료를 암호화 하지 않습니다 전에 파일 시스템에 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0d53-110">By default the DataProtectionProvider concrete type does not encrypt raw key material before persisting it to the file system.</span></span> <span data-ttu-id="e0d53-111">이 네트워크에 개발자 지점을 공유 하는 시나리오를 지원 하도록이 경우 데이터 보호 시스템 추론할 수 없습니다. 자동으로 적절 한 휴지 키 암호화 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="e0d53-111">This is to support scenarios where the developer points to a network share, in which case the data protection system cannot automatically deduce an appropriate at-rest key encryption mechanism.</span></span>
>
><span data-ttu-id="e0d53-112">또한 DataProtectionProvider 구체적인 형식 없는 [응용 프로그램을 격리](overview.md#data-protection-configuration-per-app-isolation) 기본적으로 해당 용도 매개 변수가 일치으로 동일한 키 디렉터리에서 지정한 모든 응용 프로그램 페이로드 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e0d53-112">Additionally, the DataProtectionProvider concrete type does not [isolate applications](overview.md#data-protection-configuration-per-app-isolation) by default, so all applications pointed at the same key directory can share payloads as long as their purpose parameters match.</span></span>

<span data-ttu-id="e0d53-113">응용 프로그램 개발자는 원하는 경우 둘 다 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e0d53-113">The application developer can address both of these if desired.</span></span> <span data-ttu-id="e0d53-114">DataProtectionProvider 생성자가 허용 된 [선택적 구성 콜백](overview.md#data-protection-configuration-callback) 시스템의 동작을 조정할 사용할 수 있는 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0d53-114">The DataProtectionProvider constructor accepts an [optional configuration callback](overview.md#data-protection-configuration-callback) which can be used to tweak the behaviors of the system.</span></span> <span data-ttu-id="e0d53-115">아래 샘플 SetApplicationName를 명시적으로 호출을 통해 복원 격리 하 고 자동으로 보관 되는 Windows DPAPI를 사용 하 여 키를 암호화 하는 시스템 구성는 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e0d53-115">The sample below demonstrates restoring isolation via an explicit call to SetApplicationName, and it also demonstrates configuring the system to automatically encrypt persisted keys using Windows DPAPI.</span></span> <span data-ttu-id="e0d53-116">관련 된 모든 컴퓨터에 공유 인증서를 배포 하 고 시스템 호출을 통해 인증서 기반 암호화를 대신 사용 하도록 구성 하는 디렉터리는 UNC 공유를 가리키는 경우 지정할 수 있습니다 [ProtectKeysWithCertificate](overview.md#configuring-x509-certificate)합니다.</span><span class="sxs-lookup"><span data-stu-id="e0d53-116">If the directory points to a UNC share, you may wish to distribute a shared certificate across all relevant machines and to configure the system to use certificate-based encryption instead via a call to [ProtectKeysWithCertificate](overview.md#configuring-x509-certificate).</span></span>

<span data-ttu-id="e0d53-117">[!code-none[Main](non-di-scenarios/_static/nodisample2.cs)]</span><span class="sxs-lookup"><span data-stu-id="e0d53-117">[!code-none[Main](non-di-scenarios/_static/nodisample2.cs)]</span></span>

>[!TIP]
> <span data-ttu-id="e0d53-118">DataProtectionProvider 구체적인 형식 인스턴스를 만들려면 비용이 많이입니다.</span><span class="sxs-lookup"><span data-stu-id="e0d53-118">Instances of the DataProtectionProvider concrete type are expensive to create.</span></span> <span data-ttu-id="e0d53-119">이 유형의 여러 인스턴스를 관리 하는 응용 프로그램의 경우와 모두 동일한 키 저장소 디렉터리를 가리킬 경우 응용 프로그램 성능이 저하 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e0d53-119">If an application maintains multiple instances of this type and if they're all pointing at the same key storage directory, application performance may be degraded.</span></span> <span data-ttu-id="e0d53-120">원하는 사용 응용 프로그램 개발자 한 번이 형식을 인스턴스화하는 것이 단일 참조를 다시 사용 가능한 한 유지입니다.</span><span class="sxs-lookup"><span data-stu-id="e0d53-120">The intended usage is that the application developer instantiate this type once then keep reusing this single reference as much as possible.</span></span> <span data-ttu-id="e0d53-121">DataProtectionProvider 유형과에서 만든 모든 IDataProtector 인스턴스는 스레드로부터 안전 여러 호출자에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0d53-121">The DataProtectionProvider type and all IDataProtector instances created from it are thread-safe for multiple callers.</span></span>

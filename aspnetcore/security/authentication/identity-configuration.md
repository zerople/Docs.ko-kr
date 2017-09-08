---
title: "Id 구성"
uid: security/authentication/identity-configuration
ms.openlocfilehash: 7ccd89360a8c7f5c8c6dfac76df42898e18a116a
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="configure-identity"></a><span data-ttu-id="d40fb-102">Id 구성</span><span class="sxs-lookup"><span data-stu-id="d40fb-102">Configure Identity</span></span>

<span data-ttu-id="d40fb-103">ASP.NET Core Id는 응용 프로그램의 시작 클래스에는 쉽게 재정의할 수 있는 몇 가지 기본 동작에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d40fb-103">ASP.NET Core Identity has some default behaviors that you can override easily in your application's startup class.</span></span>

## <a name="passwords-policy"></a><span data-ttu-id="d40fb-104">암호 정책</span><span class="sxs-lookup"><span data-stu-id="d40fb-104">Passwords policy</span></span>

<span data-ttu-id="d40fb-105">기본적으로 Id는 암호에 대문자, 소문자 문자 및 숫자를 포함 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="d40fb-105">By default, Identity requires that passwords contain an uppercase character, lowercase character, and digits.</span></span> <span data-ttu-id="d40fb-106">몇 가지 제한 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d40fb-106">There are also some other restrictions.</span></span> <span data-ttu-id="d40fb-107">암호 제한 간소화 하려는 경우 응용 프로그램의 시작 클래스를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d40fb-107">If you want to simplify password restrictions, you can do that in the startup class of your application.</span></span>

<span data-ttu-id="d40fb-108">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=60-65)]</span><span class="sxs-lookup"><span data-stu-id="d40fb-108">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=60-65)]</span></span>

## <a name="applications-cookie-settings"></a><span data-ttu-id="d40fb-109">응용 프로그램의 쿠키 설정</span><span class="sxs-lookup"><span data-stu-id="d40fb-109">Application's cookie settings</span></span>

<span data-ttu-id="d40fb-110">암호 정책와 같은 응용 프로그램의 쿠키의 모든 설정은 시작 클래스에서 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d40fb-110">Like the passwords policy, all the settings of the application's cookie can be changed in the startup class.</span></span>

<span data-ttu-id="d40fb-111">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=72-80)]</span><span class="sxs-lookup"><span data-stu-id="d40fb-111">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=72-80)]</span></span>

## <a name="users-lockout"></a><span data-ttu-id="d40fb-112">사용자의 잠금</span><span class="sxs-lookup"><span data-stu-id="d40fb-112">User's lockout</span></span>

<span data-ttu-id="d40fb-113">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=67-70)]</span><span class="sxs-lookup"><span data-stu-id="d40fb-113">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=67-70)]</span></span>

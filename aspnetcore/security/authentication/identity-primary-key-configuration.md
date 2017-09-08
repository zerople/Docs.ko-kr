---
title: "구성 Id 기본 키 데이터 형식"
uid: security/authentication/identity-primary-key-configuration
ms.openlocfilehash: e6661708d003aa50204e7f79d3070442a3440391
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="configure-identity-primary-keys-data-type"></a><span data-ttu-id="da72c-102">구성 Id 기본 키 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="da72c-102">Configure Identity primary keys data type</span></span>

<span data-ttu-id="da72c-103">ASP.NET Core Identity를 사용 하면 기본 키에 사용할 데이터 형식을 쉽게 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da72c-103">ASP.NET Core Identity allows you to easily configure the data type you want for the primary keys.</span></span> <span data-ttu-id="da72c-104">기본적으로 사용 하 여 Identity string 데이터 형식 있지만 매우 신속 하 게이 동작을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da72c-104">By default, Identity uses string data type but you can very quickly override this behavior.</span></span>

## <a name="how-to"></a><span data-ttu-id="da72c-105">방법</span><span class="sxs-lookup"><span data-stu-id="da72c-105">How to</span></span>

1.  <span data-ttu-id="da72c-106">이 Id의 모델을 구현 하 고 원하는 데이터 형식 사용 하 여 문자열 형식을 재정의 하는 첫 번째 단계가입니다.</span><span class="sxs-lookup"><span data-stu-id="da72c-106">The first step is to implement the Identity's model, and override the string type with the data type you want.</span></span>

    <span data-ttu-id="da72c-107">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationUser.cs?highlight=4-6&range=7-13)]</span><span class="sxs-lookup"><span data-stu-id="da72c-107">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationUser.cs?highlight=4-6&range=7-13)]</span></span>

    <span data-ttu-id="da72c-108">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationRole.cs?highlight=3-5&range=7-12)]</span><span class="sxs-lookup"><span data-stu-id="da72c-108">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationRole.cs?highlight=3-5&range=7-12)]</span></span>
    
2.  <span data-ttu-id="da72c-109">모델 및 기본 키에 대해 원하는 데이터 형식이 Id의 데이터베이스 컨텍스트를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="da72c-109">Implement the database context of Identity with your models and the data type you want for primary keys</span></span>

    <span data-ttu-id="da72c-110">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Data/ApplicationDbContext.cs?highlight=3&range=9-26)]</span><span class="sxs-lookup"><span data-stu-id="da72c-110">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Data/ApplicationDbContext.cs?highlight=3&range=9-26)]</span></span>
    
3.  <span data-ttu-id="da72c-111">모델 및 응용 프로그램의 시작 클래스의 id 서비스를 선언 하는 경우 기본 키에 사용할 데이터 형식을 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="da72c-111">Use your models and the data type you want for primary keys when you declare the identity service in your application's startup class</span></span>

    <span data-ttu-id="da72c-112">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=9-11&range=39-79)]</span><span class="sxs-lookup"><span data-stu-id="da72c-112">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=9-11&range=39-79)]</span></span>

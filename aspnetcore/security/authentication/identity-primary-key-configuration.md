---
title: "구성 Id 기본 키 데이터 형식"
uid: security/authentication/identity-primary-key-configuration
ms.openlocfilehash: 2afcdf89b2a39d82a4ba72dc780be71ac98ab664
ms.sourcegitcommit: 76d42f09f3e0dd2f2105493eca6b29994aa47706
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2017
---
# <a name="configure-identity-primary-keys-data-type"></a><span data-ttu-id="fe648-102">구성 Id 기본 키 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="fe648-102">Configure Identity primary keys data type</span></span>

<span data-ttu-id="fe648-103">ASP.NET Core Identity를 사용 하면 기본 키에 사용할 데이터 형식을 쉽게 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe648-103">ASP.NET Core Identity allows you to easily configure the data type you want for the primary keys.</span></span> <span data-ttu-id="fe648-104">기본적으로 사용 하 여 Identity string 데이터 형식 있지만 매우 신속 하 게이 동작을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe648-104">By default, Identity uses string data type but you can very quickly override this behavior.</span></span>

## <a name="how-to"></a><span data-ttu-id="fe648-105">방법</span><span class="sxs-lookup"><span data-stu-id="fe648-105">How to</span></span>

1.  <span data-ttu-id="fe648-106">이 Id의 모델을 구현 하 고 원하는 데이터 형식 사용 하 여 문자열 형식을 재정의 하는 첫 번째 단계가입니다.</span><span class="sxs-lookup"><span data-stu-id="fe648-106">The first step is to implement the Identity's model, and override the string type with the data type you want.</span></span>

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationUser.cs?highlight=4-6&range=7-13)]

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationRole.cs?highlight=3-5&range=7-12)]
    
2.  <span data-ttu-id="fe648-107">모델 및 기본 키에 대해 원하는 데이터 형식이 Id의 데이터베이스 컨텍스트를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe648-107">Implement the database context of Identity with your models and the data type you want for primary keys</span></span>

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Data/ApplicationDbContext.cs?highlight=3&range=9-26)]
    
3.  <span data-ttu-id="fe648-108">모델 및 응용 프로그램의 시작 클래스의 id 서비스를 선언 하는 경우 기본 키에 사용할 데이터 형식을 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="fe648-108">Use your models and the data type you want for primary keys when you declare the identity service in your application's startup class</span></span>

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=9-11&range=39-79)]

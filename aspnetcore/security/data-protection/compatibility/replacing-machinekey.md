---
title: "ASP.NET에서 '< machineKey >'를 대체"
author: rick-anderson
description: "ASP.NET에서 '< machineKey >'를 대체"
keywords: "ASP.NET Core, 보안, < machineKey > machineKey"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 5ac13589-3837-4b4d-8abe-81f843942120
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 8c00c05a1120e65f503b70229466fcad561bc6a9
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="replacing-machinekey-in-aspnet"></a><span data-ttu-id="79404-104">교체 `<machineKey>` asp.net</span><span class="sxs-lookup"><span data-stu-id="79404-104">Replacing `<machineKey>` in ASP.NET</span></span>

<a name=compatibility-replacing-machinekey></a>

<span data-ttu-id="79404-105">구현에서 `<machineKey>` asp.net에서 요소 [대체할 수](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)합니다.</span><span class="sxs-lookup"><span data-stu-id="79404-105">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="79404-106">이렇게 하면 새 데이터 보호 시스템을 포함 하 여 대체 데이터 보호 메커니즘을 통해 전송 하려면 ASP.NET 암호화 루틴에 대 한 대부분 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="79404-106">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="79404-107">패키지 설치</span><span class="sxs-lookup"><span data-stu-id="79404-107">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="79404-108">새 데이터 보호 시스템에.NET 4.5.1을 대상으로 하는 기존 ASP.NET 응용 프로그램으로 설치 또는 더 높은 수만 있습니다.</span><span class="sxs-lookup"><span data-stu-id="79404-108">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or higher.</span></span> <span data-ttu-id="79404-109">설치 응용 프로그램이.NET 4.5를 대상으로 하는 경우 실패 또는 절감 됩니다.</span><span class="sxs-lookup"><span data-stu-id="79404-109">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="79404-110">기존 ASP.NET 4.5.1+ 프로젝트에 새 데이터 보호 시스템을 설치 하려면 Microsoft.AspNetCore.DataProtection.SystemWeb 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="79404-110">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="79404-111">이 사용 하 여 데이터 보호 시스템 인스턴스화는 [기본 구성](../configuration/default-settings.md#data-protection-default-settings) 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="79404-111">This will instantiate the data protection system using the [default configuration](../configuration/default-settings.md#data-protection-default-settings) settings.</span></span>

<span data-ttu-id="79404-112">패키지를 설치 하기에 행 삽입 *Web.config* 에 사용할 asp [암호화 작업 대부분](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)폼 인증, 상태 보기 및 호출을 포함 MachineKey.Protect 합니다.</span><span class="sxs-lookup"><span data-stu-id="79404-112">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="79404-113">삽입 되는 행의 내용이 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="79404-113">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="79404-114">과 같은 필드를 검사 하 여 새 데이터 보호 시스템 활성 상태 인지 여부를 알 수 있습니다 `__VIEWSTATE`, 아래 예제와 같이 "CfDJ8"로 시작 해야입니다.</span><span class="sxs-lookup"><span data-stu-id="79404-114">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="79404-115">"CfDJ8"는 데이터 보호 시스템으로 보호 되는 페이로드를 식별 하는 "09 F0 C9 F0" 매직 헤더의 base64 표현입니다.</span><span class="sxs-lookup"><span data-stu-id="79404-115">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk..." />
```

## <a name="package-configuration"></a><span data-ttu-id="79404-116">패키지 구성</span><span class="sxs-lookup"><span data-stu-id="79404-116">Package configuration</span></span>

<span data-ttu-id="79404-117">데이터 보호 시스템에 기본 0 설치 구성으로 인스턴스화됩니다.</span><span class="sxs-lookup"><span data-stu-id="79404-117">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="79404-118">그러나 기본적으로 키는 유지 않으므로 로컬 파일 시스템에는 팜에 배포 되는 응용 프로그램에 대해 작동 하지 않습니다이 합니다.</span><span class="sxs-lookup"><span data-stu-id="79404-118">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="79404-119">이 해결 하려면 서브클래싱하 DataProtectionStartup 형식을 만들어 구성을 제공할 수 고 해당 ConfigureServices 메서드를 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="79404-119">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="79404-120">다음은 사용자 지정 데이터 보호 시작 유형 구성 키 보관용와 휴지 암호화 된 하는 방법의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="79404-120">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="79404-121">또한 자체 응용 프로그램 이름을 제공 하 여 기본 응용 프로그램 격리 정책을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="79404-121">It also overrides the default app isolation policy by providing its own application name.</span></span>

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> <span data-ttu-id="79404-122">사용할 수도 있습니다 `<machineKey applicationName="my-app" ... />` 대신 SetApplicationName 명시적으로 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="79404-122">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="79404-123">이것이 응용 프로그램 이름이 설정 된 구성 하고자 할 모든 경우 DataProtectionStartup 파생 형식을 만드는 개발자를 적용 하지 않도록 하는 편리한 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="79404-123">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="79404-124">이 사용자 지정 구성을 사용 하도록 하려면 Web.config으로 돌아가서를 찾습니다는 `<appSettings>` 요소가 패키지를 설치 하는 구성 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="79404-124">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="79404-125">태그를 다음과 같이 두는 것이 보입니다.</span><span class="sxs-lookup"><span data-stu-id="79404-125">It will look like the following markup:</span></span>

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

<span data-ttu-id="79404-126">방금 만든 DataProtectionStartup 파생 된 형식의 어셈블리 정규화 된 이름 가진 빈 값을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="79404-126">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="79404-127">응용 프로그램의 이름이 DataProtectionDemo 인 경우이 다음과 같은 아래 합니다.</span><span class="sxs-lookup"><span data-stu-id="79404-127">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="79404-128">새로 구성 된 데이터 보호 시스템 응용 프로그램 내에서 사용할 준비가 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="79404-128">The newly-configured data protection system is now ready for use inside the application.</span></span>

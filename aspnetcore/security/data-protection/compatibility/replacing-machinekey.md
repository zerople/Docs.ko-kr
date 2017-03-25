---
title: "대체 `<machineKey>` asp.net에서 | Microsoft 문서"
author: rick-anderson
description: "대체 `<machineKey>` asp.net"
keywords: "ASP.NET Core, 보안,`<machineKey>`"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 5ac13589-3837-4b4d-8abe-81f843942120
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/compatibility/replacing-machinekey
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 0f502147ca3f8f0fead1dfd448dd843fe9300b88
ms.lasthandoff: 03/23/2017

---
# <a name="replacing-machinekey-in-aspnet"></a>대체 `<machineKey>` asp.net

<a name=compatibility-replacing-machinekey></a>

구현에서 `<machineKey>` asp.net에서 요소 [대체할 수](http://blogs.msdn.com/b/webdev/archive/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2.aspx)합니다. 따라서 대부분 호출 ASP.NET 암호화 루틴을 새 데이터 보호 시스템을 포함 하 여 대체 데이터 보호 메커니즘을 통해 라우팅할 수 있습니다.

## <a name="package-installation"></a>패키지 설치

> [!NOTE]
> 새 데이터 보호 시스템.NET 4.5.1을 대상으로 하는 기존 ASP.NET 응용 프로그램에 설치 된 이상에 가능 합니다. 설치는 응용 프로그램이.NET 4.5를 대상으로 하는 경우 실패 하거나 줄이세요.

기존 ASP.NET 4.5.1+ 프로젝트에 새 데이터 보호 시스템을 설치 하려면 Microsoft.AspNetCore.DataProtection.SystemWeb 패키지를 설치 합니다. 이 사용 하 여 데이터 보호 시스템 인스턴스화는 [기본 구성](../configuration/default-settings.md#data-protection-default-settings) 설정 합니다.

에 줄을 삽입 패키지를 설치 하면 *Web.config* ASP.NET에 대해 사용 하도록 지시 하 [암호화 작업 대부분](http://blogs.msdn.com/b/webdev/archive/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2.aspx), 폼 인증, 뷰 상태 및 MachineKey.Protect에 대 한 호출을 포함 합니다. 삽입 되는 줄을 다음과 같이 읽습니다.

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> 같은 필드를 검사 하 여 새 데이터 보호 시스템 활성 상태 인지 여부를 알 수 있습니다 `__VIEWSTATE`, 아래 예제와 같이 "CfDJ8"로 시작 해야 하 합니다. "CfDJ8"은 데이터 보호 시스템에 의해 보호 되는 페이로드를 식별 하는 마법 "09 F0 C9 F0" 헤더의 base64 표현입니다.

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk..." />
```

## <a name="package-configuration"></a>패키지 구성

데이터 보호 시스템에 기본 설정&0; 구성으로 인스턴스화됩니다. 그러나 기본적으로 키를 로컬 파일 시스템에 유지 되는지, 이후 팜에 배포 되는 응용 프로그램에 대해 작동 하지 않습니다이. 이 해결 하려면 서브클래싱하 DataProtectionStartup 형식을 만들어 구성을 제공할 수 고 해당 ConfigureServices 메서드를 재정의 합니다.

다음은 사용자 지정 데이터 보호 시작 유형 구성 키 유지 되는 위치와 휴지 암호화 하는 방법의 예입니다. 또한 자체 응용 프로그램 이름을 제공 하 여 기본 응용 프로그램 격리 정책을 재정의 합니다.

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
> 사용할 수도 있습니다 `<machineKey applicationName="my-app" ... />` 대신 SetApplicationName 명시적으로 호출 합니다. 응용 프로그램 이름을 설정 된 모든 구성 하기를 원할 경우 DataProtectionStartup 파생 형식을 만드는 개발자를 적용 하지 않도록 편의 메커니즘입니다.

이 사용자 지정 구성을 사용 하도록 하려면 Web.config로 돌아가서를 찾아서는 `<appSettings>` 요소가 패키지를 설치 하는 구성 파일에 추가 합니다. 다음 태그 처럼 표시 됩니다.

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

방금 만든 DataProtectionStartup에서 파생 된 형식의 정규화 된 어셈블리 이름 사용 하 여 빈 값을 입력 합니다. 같습니다 응용 프로그램의 이름이 DataProtectionDemo 인 경우는 아래입니다.

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

새로 구성 된 데이터 보호 시스템 응용 프로그램 내에서 사용할 준비가 되었습니다.


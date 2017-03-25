---
title: "비 DI 인식 시나리오 | Microsoft 문서"
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
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: d0c3f0d79b95332b545b66451dbd5b8b506700d7
ms.lasthandoff: 03/23/2017

---
# <a name="non-di-aware-scenarios"></a>비 DI 인식 시나리오

데이터 보호 시스템에서는 일반적으로 사용할 수 [서비스 컨테이너에 추가할](../consumer-apis/overview.md) 및 DI 메커니즘을 통해 종속 구성 요소에 제공할 수 있습니다. 그러나 일부 경우에이 작업이 불가능, 특히 기존 응용 프로그램으로 시스템을 가져올 때 있을 수 있습니다.

이러한 시나리오를 지원 하기 위해 패키지 Microsoft.AspNetCore.DataProtection.Extensions DataProtectionProvider DI의 특정 코드 경로 통과 하지 않고 데이터 보호 시스템을 사용 하는 간단한 방법을 제공 하는 구체적인 형식을 제공 합니다. 형식 자체 IDataProtectionProvider, 구현 및 생성 하는 것은이 공급자의 암호화 키를 저장할 DirectoryInfo 제공 것 만큼 쉽습니다.

예:

[!code-none[주](non-di-scenarios/_static/nodisample1.cs)]

>[!WARNING]
> 구체적인 형식은 DataProtectionProvider는 기본적으로 원시 키 자료를 암호화 하지 않습니다 전에 파일 시스템에 유지 합니다. 이 개발자 지점 네트워크에 공유 되는 시나리오를 지원 하도록이 경우 데이터 보호 시스템에 rest 적절 한 키 암호화 메커니즘 추론할 자동으로 수 없습니다.
>
>또한 DataProtectionProvider 구체적인 형식이 없는 [응용 프로그램을 격리](overview.md#data-protection-configuration-per-app-isolation) 기본적으로과 일치 하는 목적은 매개 변수의 키 동일한 디렉터리를 가리키는 모든 응용 프로그램 페이로드를 공유할 수 있습니다.

응용 프로그램 개발자는 원하는 경우 두 가지 모두 해결할 수 있습니다. DataProtectionProvider 생성자는 [선택적 구성 콜백](overview.md#data-protection-configuration-callback) 시스템의 동작을 조정 해야 사용할 수 있는 합니다. 아래 샘플 SetApplicationName를 명시적으로 호출을 통해 복원 격리 하 고 자동으로 보관 되는 Windows DPAPI를 사용 하 여 키를 암호화 하는 시스템 구성는 보여 줍니다. 관련 된 모든 컴퓨터에 공유 인증서를 배포 하 고 대신를 호출 하 여 인증서 기반 암호화를 사용 하 여 시스템을 구성 하는 디렉터리는 UNC 공유를 가리키는 경우 볼 수 있습니다 [ProtectKeysWithCertificate](overview.md#configuring-x509-certificate)합니다.

[!code-none[주](non-di-scenarios/_static/nodisample2.cs)]

>[!TIP]
> DataProtectionProvider 구체적인 형식의 인스턴스를 만드는 데 비용이 많이 있습니다. 이러한 종류의 여러 인스턴스를 관리 하는 응용 프로그램의 경우 하 고는 모두 동일한 키 저장소 디렉터리를 가리키는 응용 프로그램의 성능이 저하 될 수 있습니다. 그 용도 응용 프로그램 개발자 한 번이 형식을 인스턴스화하는 것이 단일 참조를 다시 사용 가능한 한 유지입니다. DataProtectionProvider 유형 및 여기에서 만든 모든 IDataProtector 인스턴스는 스레드로부터 안전 여러 호출자입니다.


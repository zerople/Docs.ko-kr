---
title: "임시 데이터 보호 공급자 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: af6ea1d0-0d9d-41df-a870-5dda24978e2f
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-storage-ephemeral
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 6d4b71530f96edda190db28d1fc5fc7a7dde7ca3
ms.lasthandoff: 03/23/2017

---
# <a name="ephemeral-data-protection-providers"></a>임시 데이터 보호 기능 공급자

<a name=data-protection-implementation-key-storage-ephemeral></a>

응용 프로그램 throwaway IDataProtectionProvider 해야 하는 경우 시나리오가 있습니다. 예를 들어 개발자는 단일 콘솔 응용 프로그램에서 실험 방금 수 또는 응용 프로그램 자체는 일시적 (스크립팅 되었으므로 또는 단위 테스트 프로젝트). 이러한 시나리오를 지원 하기 위해 패키지 Microsoft.AspNetCore.DataProtection EphemeralDataProtectionProvider 형식을 포함 합니다. 이 형식은 키 리포지토리가 전적으로 메모리에 유지 되 고 백업 저장소에 기록 되지 않습니다 IDataProtectionProvider의 기본 구현을 제공 합니다.

EphemeralDataProtectionProvider의 각 인스턴스는 자체 고유 마스터 키를 사용합니다. 따라서는 EphemeralDataProtectionProvider에서 시작 하는 IDataProtector 보호 된 페이로드를 생성 하는 경우 해당 페이로드의 수만 보호를 해제할 수는 해당 하는 IDataProtector에서 (동일한 [목적](../consumer-apis/purpose-strings.md#data-protection-consumer-apis-purposes) 체인) 같은 EphemeralDataProtectionProvider 인스턴스에서 시작 합니다.

다음 샘플은 EphemeralDataProtectionProvider 인스턴스화하고 사용 하 여 보호 하 고 데이터 보호를 해제 하는 방법을 보여 줍니다.

```none
using System;
   using Microsoft.AspNetCore.DataProtection;

   public class Program
   {
       public static void Main(string[] args)
       {
           const string purpose = "Ephemeral.App.v1";

           // create an ephemeral provider and demonstrate that it can round-trip a payload
           var provider = new EphemeralDataProtectionProvider();
           var protector = provider.CreateProtector(purpose);
           Console.Write("Enter input: ");
           string input = Console.ReadLine();

           // protect the payload
           string protectedPayload = protector.Protect(input);
           Console.WriteLine($"Protect returned: {protectedPayload}");

           // unprotect the payload
           string unprotectedPayload = protector.Unprotect(protectedPayload);
           Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

           // if I create a new ephemeral provider, it won't be able to unprotect existing
           // payloads, even if I specify the same purpose
           provider = new EphemeralDataProtectionProvider();
           protector = provider.CreateProtector(purpose);
           unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
       }
   }

   /*
    * SAMPLE OUTPUT
    *
    * Enter input: Hello!
    * Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
    * Unprotect returned: Hello!
    * << throws CryptographicException >>
    */
   ```


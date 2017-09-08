---
title: "임시 데이터 보호 공급자"
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
ms.openlocfilehash: 6b564f082eefad993ac938407e0f3b657d83fb52
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="ephemeral-data-protection-providers"></a>임시 데이터 보호 공급자

<a name=data-protection-implementation-key-storage-ephemeral></a>

응용 프로그램 해야 throwaway IDataProtectionProvider 시나리오가 있습니다. 예를 들어 일회용 콘솔 응용 프로그램에서는 개발자를 시험만 될 수 있습니다 또는 응용 프로그램 자체는 임시로 (스크립팅 되었으므로 또는 단위 테스트 프로젝트). 이러한 시나리오를 지원 하기 위해 Microsoft.AspNetCore.DataProtection 패키지에는 형식 EphemeralDataProtectionProvider 포함 됩니다. 이 형식은 키 리포지토리가 전적으로 메모리에 유지 되 고 백업 저장소에 기록 되지 않습니다 IDataProtectionProvider의 기본 구현을 제공 합니다.

EphemeralDataProtectionProvider의 각 인스턴스는 자체의 고유한 마스터 키를 사용합니다. 따라서 IDataProtector는 EphemeralDataProtectionProvider에서 시작 하는 보호 된 페이로드를 생성 하는 경우 해당 페이로드의 수만 수에 의해 보호 해당 IDataProtector (동일한 [목적](../consumer-apis/purpose-strings.md#data-protection-consumer-apis-purposes) 체인)에서 동일한 루트 EphemeralDataProtectionProvider 인스턴스입니다.

다음 샘플에서는 EphemeralDataProtectionProvider 인스턴스화 및 사용 하 여 보호 하 고 데이터를 보호 해제를 보여 줍니다.

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

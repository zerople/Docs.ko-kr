---
title: "데이터 보호 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 3a169d99-a93b-4cb8-9585-4df3f5e31e40
ms.technology: aspnet
ms.prod: asp.net-core
uid: hosting/dataprotection
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 51a8224e609ff5d8ccad1a3bab030ca3abe899aa
ms.lasthandoff: 03/23/2017

---
# <a name="data-protection"></a>데이터 보호

<a name=dataprotection></a>

[Sourabh Shirhatti](https://twitter.com/sshirhatti)

사용 하기 쉬운 암호화 API 개발자 키 관리 및 회전을 포함 하 여 데이터를 보호 하는 데 사용할 수 및 ASP.NET 핵심 데이터 보호 스택 간단한을 제공 합니다. 이 문서에서는 데이터 보호를 사용 하는 데 사용 하 여 서버의 데이터 보호를 구성 하는 방법에 대 한 개요를 제공 합니다.

## <a name="configuring-data-protection"></a>데이터 보호를 구성합니다.

>[!WARNING]
> 데이터 보호는 인증에 사용 되는 포함 하 여 다양 한 ASP.NET 미들웨어에서 사용 됩니다. IIS 호스팅된 웹 사이트에서 기본 동작은 메모리에 키를 저장 하는 프로세스를 다시 시작 될 때이 제거 하려면입니다. 이 동작은 의도 하지 않은, 예를 들어 취소 키 쿠키 인증으로 작성 된 쿠키를 무효화 하 고 사용자가 다시 로그인 해야 합니다.

IIS에서 호스팅되는 응용 프로그램에 대 한 키를 자동으로 유지 하기 위해 각 응용 프로그램 풀에 대 한 레지스트리 하이브를 만들어야 합니다. 사용 하는 [프로 비전 하는 PowerShell 스크립트](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1) 각 응용 프로그램 풀에 대 한 호스트할 ASP.NET 핵심 응용 프로그램입니다. 이 스크립트에서 작업자 프로세스 계정에만 하 게 포함 되었는지는 HKLM 레지스트리에 특수 레지스트리 키를 만듭니다. DPAPI를 사용 하 여 나머지 키 암호화 됩니다.

> [!NOTE]
> 개발자는 파일 시스템에 데이터를 저장 하도록 응용 프로그램 데이터 보호 Api를 구성할 수 있습니다. 부하 분산을 사용 하도록 설정 하려면 키를 저장 하는 UNC 공유를 사용 하 여 개발자가 데이터 보호를 구성할 수 있습니다. 호스팅 서비스 공급자는 응용 프로그램으로 실행 하는 Windows 계정에 제한 된 해당 공유에 대 한 파일 사용 권한이 있는지 확인 해야 합니다. 개발자는 X509를 사용 하 여 나머지 아래에서 키를 보호 하기 위해 선택할 수는 또한 인증서입니다. 호스팅 서비스 공급자는 사용자가 인증서를 업로드 하 고, 사용자의 신뢰할 수 있는 인증서 저장소에 배치,에 사용자가 응용 프로그램을 실행 하는 모든 컴퓨터에서 사용할 수 있는 확인 하도록 허용 하는 메커니즘을 고려해 야 할 수도 있습니다.

## <a name="machine-wide-policy"></a>컴퓨터 단위 정책

데이터 보호 시스템 기본 설정에 대 한 지원이 제한적으로 [컴퓨터 차원의 정책](../security/data-protection/configuration/machine-wide-policy.md#data-protection-configuration-machinewidepolicy) 데이터 보호 Api를 사용 하는 모든 응용 프로그램입니다. 참조는 [데이터 보호](../security/data-protection/index.md) 대 한 자세한 내용은 설명서입니다.


---
title: "이미지 태그 도우미 | Microsoft Docs"
author: pkellner
description: "이미지 태그 도우미를 사용 하는 방법을 보여 줍니다."
keywords: "ASP.NET Core, 태그 도우미"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: c045d485-d1dc-4cea-a675-46be83b7a013
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/ImageTagHelper
ms.openlocfilehash: 67537674154d885fc6f69accd2cc7f01c9104d71
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="imagetaghelper"></a>ImageTagHelper

으로 [Peter Kellner](http://peterkellner.net) 

향상 된 이미지 태그 도우미의 `img` (`<img>`) 태그입니다. 필요한는 `src` 태그도 `boolean` 특성 `asp-append-version`합니다.

경우 이미지 원본은 (`src`) 정적 파일은 호스트 웹 서버에서 문자열 busting 고유한 캐시 이미지 원본에 쿼리 매개 변수로 추가 됩니다. 이렇게 하면 있는지 호스트 웹 서버의 파일이 변경 되 면 고유한 요청 URL이 생성 업데이트 요청 매개 변수를 포함 합니다. Busting 문자열 캐시는 정적 이미지 파일의 해시를 나타내는 고유 값입니다.

경우 이미지 원본은 (`src`) 정적 파일 (예를 들어 원격 URL 또는 파일에 존재 하지 서버) 되지 않습니다는 `<img>` 태그의 `src` 쿼리 문자열 매개 변수를 busting 캐시가 없는 특성이 생성 됩니다.

## <a name="image-tag-helper-attributes"></a>이미지 태그 도우미 특성


### <a name="asp-append-version"></a>asp 추가 버전

와 함께 지정 된 경우는 `src` 이미지 태그 도우미 특성 호출 됩니다.

유효한의 예로 `img` 태그 도우미는:

```cshtml
<img src="~/images/asplogo.png" 
    asp-append-version="true"  />
```

정적 파일이 디렉터리에 있는 경우 *... wwwroot/images/asplogo.png* (해시 다를 수는) 다음 생성 된 html 비슷합니다.

```html
<img 
    src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM"/>
```

매개 변수에 할당 된 값 `v` 디스크에 있는 파일의 해시 값입니다. 웹 서버에 대 한 읽기 액세스를 얻을 수 없는 경우 정적 참조 하는 파일이, 아니요 `v` 에 매개 변수 추가 되는 `src` 특성입니다.

- - -

### <a name="src"></a>src

이미지 태그 도우미를 활성화 하려면 src 특성에 필요는 `<img>` 요소입니다. 

> [!NOTE]
> 이미지 태그 도우미를 사용 하 여는 `Cache` 저장할 계산 된 로컬 웹 서버에 공급자 `Sha512` 제공 된 파일입니다. 파일을 다시 요청 하는 경우는 `Sha512` 계산 해야 하는 필요 하지 않습니다. 파일에 연결 된 파일 감시자에서 캐시를 무효화 하는 경우 파일의 `Sha512` 계산 됩니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:performance/caching/memory>
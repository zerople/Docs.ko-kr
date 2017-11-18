---
title: "컨트롤러 메서드 및 보기"
author: rick-anderson
description: "컨트롤러 메서드, 보기 및 DataAnnotations 작업"
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 04/07/2017
ms.topic: get-started-article
ms.assetid: c7313211-bb71-4adf-babe-8e72603cc0ce
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-xplat/controller-methods-views
ms.openlocfilehash: b72fb5077d3bd577cd6282e7a6231a0806ee1fd2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="controller-methods-and-views"></a><span data-ttu-id="652be-104">컨트롤러 메서드 및 보기</span><span class="sxs-lookup"><span data-stu-id="652be-104">Controller methods and views</span></span>

<span data-ttu-id="652be-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="652be-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="652be-106">동영상 앱을 적절하게 시작했지만 프레젠테이션은 이상적이지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="652be-106">We have a good start to the movie app, but the presentation is not ideal.</span></span> <span data-ttu-id="652be-107">시간(아래 이미지에서 오전 12시)을 표시하지 않으려 하고 **ReleaseDate**는 두 단어이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="652be-107">We don't want to see the time (12:00:00 AM in the image below) and **ReleaseDate** should be two words.</span></span>

![인덱스 뷰: 릴리스 날짜는 한 단어(공백 없음)이며 모든 동영상 릴리스 날짜는 오전 12시를 표시합니다.](../../tutorials/first-mvc-app/working-with-sql/_static/m55.png)

<span data-ttu-id="652be-109">*Models/Movie.cs* 파일을 열고 아래 표시된 강조 표시된 줄을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="652be-109">Open the *Models/Movie.cs* file and add the highlighted lines shown below:</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDate.cs?name=snippet_1&highlight=2,11-12)]

<span data-ttu-id="652be-110">앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="652be-110">Build and run the app.</span></span>

<!-- include start
![MVC Movie application open browser showing movie data](../../tutorials/first-mvc-app/working-with-sql/_static/m55.png)

 -->

[!INCLUDE[adding-model](../../includes/mvc-intro/controller-methods-views.md)]

>[!div class="step-by-step"]
<span data-ttu-id="652be-111">[이전 - SQLite 작업](working-with-sql.md)
[다음 - 검색 추가](search.md)</span><span class="sxs-lookup"><span data-stu-id="652be-111">[Previous - Working with SQLite](working-with-sql.md)
[Next - Add search](search.md)</span></span>  

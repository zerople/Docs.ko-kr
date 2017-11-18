---
title: "ASP.NET Core MVC 앱에서 컨트롤러 메서드 및 보기"
author: rick-anderson
description: "컨트롤러 메서드, 보기 및 DataAnnotations 작업"
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 04/07/2017
ms.topic: get-started-article
ms.assetid: c7313211-babe-babe-babe-8e72603cc0ce
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-mac/controller-methods-views
ms.openlocfilehash: 846a78a09cdde0cfdbcf35bb899c1822ebe8fea2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="controller-methods-and-views-in-an-aspnet-core-mvc-app"></a><span data-ttu-id="7e529-104">ASP.NET Core MVC 앱에서 컨트롤러 메서드 및 보기</span><span class="sxs-lookup"><span data-stu-id="7e529-104">Controller methods and views in an ASP.NET Core MVC app</span></span>

<span data-ttu-id="7e529-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7e529-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7e529-106">동영상 앱을 적절하게 시작했지만 프레젠테이션은 이상적이지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e529-106">We have a good start to the movie app, but the presentation is not ideal.</span></span> <span data-ttu-id="7e529-107">시간(다음 이미지에서 오전 12시)을 표시하지 않으려 하고 **ReleaseDate**는 두 단어이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e529-107">We don't want to see the time (12:00:00 AM in the following image) and **ReleaseDate** should be two words.</span></span>

![인덱스 뷰: 릴리스 날짜는 한 단어(공백 없음)이며 모든 동영상 릴리스 날짜는 오전 12시를 표시합니다.](../../tutorials/first-mvc-app/working-with-sql/_static/m55.png)

<span data-ttu-id="7e529-109">*Models/Movie.cs* 파일을 열고 아래 표시된 강조 표시된 줄을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7e529-109">Open the *Models/Movie.cs* file and add the highlighted lines shown below:</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDate.cs?name=snippet_1&highlight=2,11-12)]

<span data-ttu-id="7e529-110">앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7e529-110">Build and run the app.</span></span>

<!-- include start
![MVC Movie application open browser showing movie data](../../tutorials/first-mvc-app/working-with-sql/_static/m55.png)

 -->

[!INCLUDE[adding-model](../../includes/mvc-intro/controller-methods-views.md)]

>[!div class="step-by-step"]
<span data-ttu-id="7e529-111">[이전 - SQLite 작업](working-with-sql.md)
[다음 - 검색 추가](search.md)</span><span class="sxs-lookup"><span data-stu-id="7e529-111">[Previous - Working with SQLite](working-with-sql.md)
[Next - Add search](search.md)</span></span>

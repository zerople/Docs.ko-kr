---
title: "생성된 페이지 업데이트"
author: rick-anderson
description: "향상된 표시로 생성된 페이지를 업데이트합니다."
keywords: "ASP.NET Core, Razor 페이지"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 8b2018bbf83cbb4b5c9139605fbb97d1c5be959f
ms.sourcegitcommit: f2fb0b45284e4f8c4a9c422bec790aede7c1f0ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2017
---
# <a name="updating-the-generated-pages"></a><span data-ttu-id="9a0b0-104">생성된 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="9a0b0-104">Updating the generated pages</span></span>

<span data-ttu-id="9a0b0-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9a0b0-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9a0b0-106">동영상 앱을 적절하게 시작했지만 프레젠테이션은 이상적이지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-106">We have a good start to the movie app, but the presentation is not ideal.</span></span> <span data-ttu-id="9a0b0-107">시간(아래 이미지에서 오전 12시)을 표시하지 않으려 하고 **ReleaseDate**는 **Release Date**(두 단어)이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-107">We don't want to see the time (12:00:00 AM in the image below) and **ReleaseDate** should be **Release Date** (two words).</span></span>

![동영상 데이터를 표시하는 크롬에서 열린 동영상 응용 프로그램](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="9a0b0-109">생성된 코드 업데이트</span><span class="sxs-lookup"><span data-stu-id="9a0b0-109">Update the generated code</span></span>

<span data-ttu-id="9a0b0-110">*Models/Movie.cs* 파일을 열고 다음 코드에 표시된 강조 표시된 줄을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-110">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

<span data-ttu-id="9a0b0-111">[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieDate.cs?name=snippet_1&highlight=10-11)]</span><span class="sxs-lookup"><span data-stu-id="9a0b0-111">[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieDate.cs?name=snippet_1&highlight=10-11)]</span></span>

<span data-ttu-id="9a0b0-112">빨간색 물결선 > ** 빠른 작업 및 리팩터링**을 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-112">Right click on a red squiggly line > ** Quick Actions and Refactorings**.</span></span>

  ![바로 가기 메뉴는 **> 빠른 작업 및 리팩터링**을 표시합니다.](da1/qa.png)


<span data-ttu-id="9a0b0-114">`using System.ComponentModel.DataAnnotations;`를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-114">Select `using System.ComponentModel.DataAnnotations;`</span></span>

  ![목록 위쪽의 System.ComponentModel.DataAnnotations 사용](da1/da.png)

  <span data-ttu-id="9a0b0-116">Visual Studio는 `using System.ComponentModel.DataAnnotations;`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-116">Visual studio adds `using System.ComponentModel.DataAnnotations;`.</span></span>

<span data-ttu-id="9a0b0-117">다음 자습서에서 [DataAnnotations](http://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx)를 다룰 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-117">We'll cover [DataAnnotations](http://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) in the next tutorial.</span></span> <span data-ttu-id="9a0b0-118">[표시](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayattribute.aspx) 특성은 필드의 이름에 표시할 대상을 지정합니다(이 경우 "ReleaseDate" 대신 "Release Date").</span><span class="sxs-lookup"><span data-stu-id="9a0b0-118">The [Display](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayattribute.aspx) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="9a0b0-119">[DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) 특성은 필드에 저장된 시간 정보가 표시되지 않도록 데이터의 형식(날짜)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-119">The [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) attribute specifies the type of the data (Date), so the time information stored in the field is not displayed.</span></span>

<span data-ttu-id="9a0b0-120">페이지/동영상으로 이동하고 **편집** 링크로 마우스를 가져가 대상 URL을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-120">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![편집 링크 위로 마우스를 가져가면 나타나는 브라우저 창 및 http://localhost:1234/Movies/Edit/5의 링크 Url이 표시됩니다.](da1/edit7.png)

<span data-ttu-id="9a0b0-122">**편집**, **세부 정보** 및 **삭제** 링크는 *Pages/Movies/Index.cshtml* 파일에서 [앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper)에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-122">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper) in the *Pages/Movies/Index.cshtml* file.</span></span>

<span data-ttu-id="9a0b0-123">[!code-cshtml[Main](razor-pages-start\snapshot_sample\RazorPagesMovie\Pages\Movie\Index.cshtml?highlight=16-18&range=32-)]</span><span class="sxs-lookup"><span data-stu-id="9a0b0-123">[!code-cshtml[Main](razor-pages-start\snapshot_sample\RazorPagesMovie\Pages\Movie\Index.cshtml?highlight=16-18&range=32-)]</span></span>

<span data-ttu-id="9a0b0-124">[태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용하면 서버 쪽 코드를 Razor 파일에서 HTML 요소를 만들고 렌더링하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-124">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="9a0b0-125">위의 코드에서 `AnchorTagHelper`는 Razor 페이지에서 HTML `href` 특성 값(경로는 상대적), `asp-page` 및 경로 ID(`asp-route-id`)를 동적으로 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-125">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="9a0b0-126">자세한 내용은 [페이지에 대한 URL 생성](xref:mvc/razor-pages/index#url-generation-for-pages)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-126">See [URL generation for Pages](xref:mvc/razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="9a0b0-127">선호하는 브라우저에서 **소스 보기**를 사용하여 생성된 표시를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-127">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="9a0b0-128">생성된 HTML의 일부는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-128">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>

```

<span data-ttu-id="9a0b0-129">동적으로 생성된 링크는 쿼리 문자열이 포함된 동영상 ID를 전달합니다(예: `http://localhost:5000/Movies/Details?id=2` ).</span><span class="sxs-lookup"><span data-stu-id="9a0b0-129">The dynamically-generated links pass the movie ID with a query string (for example, `http://localhost:5000/Movies/Details?id=2` ).</span></span> 

<span data-ttu-id="9a0b0-130">편집, 세부 정보 및 삭제 Razor 페이지를 "{id:int}" 경로 템플릿을 사용하도록 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-130">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="9a0b0-131">이러한 각 페이지에 대한 page 지시문을 `@page "{id:int}"`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-131">Change the page directive for each of these pages to `@page "{id:int}"`.</span></span> <span data-ttu-id="9a0b0-132">앱을 실행한 다음 소스를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-132">Run the app and then view source.</span></span> <span data-ttu-id="9a0b0-133">생성된 HTML에서 URL의 경로 부분에 ID를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-133">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="9a0b0-134">정수를 포함하지 **않는** "{id:int}" 경로 템플릿이 있는 페이지에 대한 요청은 HTTP 404(찾을 수 없음) 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-134">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="9a0b0-135">예를 들어 `http://localhost:5000/Movies/Details`는 404 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-135">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="9a0b0-136">ID를 옵션으로 설정하려면 경로 제약 조건에 `?`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-136">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

### <a name="update-concurrency-exception-handling"></a><span data-ttu-id="9a0b0-137">동시성 예외 처리 업데이트</span><span class="sxs-lookup"><span data-stu-id="9a0b0-137">Update concurrency exception handling</span></span>

<span data-ttu-id="9a0b0-138">*Pages/Movies/Edit.cshtml.cs* 파일에서 `OnPostAsync` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-138">Update the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file.</span></span> <span data-ttu-id="9a0b0-139">다음 강조 표시된 코드는 변경 내용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-139">The following highlighted code shows the changes:</span></span>

<span data-ttu-id="9a0b0-140">[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Edit.cshtml.cs?name=snippet1&highlight=17-24)]</span><span class="sxs-lookup"><span data-stu-id="9a0b0-140">[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Edit.cshtml.cs?name=snippet1&highlight=17-24)]</span></span>

<span data-ttu-id="9a0b0-141">앞의 코드는 첫 번째 동시 클라이언트가 동영상을 삭제하고 두 번째 동시 클라이언트가 동영상에 대한 변경 내용을 게시하는 경우에만 동시성 예외를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-141">The previous code only detects concurrency exceptions when the first concurrent client deletes the movie, and the second concurrent client posts changes to the movie.</span></span>

<span data-ttu-id="9a0b0-142">`catch` 블록을 테스트하려면:</span><span class="sxs-lookup"><span data-stu-id="9a0b0-142">To test the `catch` block:</span></span>

* <span data-ttu-id="9a0b0-143">`catch (DbUpdateConcurrencyException)`에서 중단점 설정</span><span class="sxs-lookup"><span data-stu-id="9a0b0-143">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="9a0b0-144">동영상을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-144">Edit a movie.</span></span>
* <span data-ttu-id="9a0b0-145">다른 브라우저 창에서 동일한 동영상에 대한 **삭제** 링크를 선택한 다음 동영상을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-145">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="9a0b0-146">이전 브라우저 창에서 동영상에 변경 내용을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-146">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="9a0b0-147">프로덕션 코드는 일반적으로 둘 이상의 클라이언트가 동시에 레코드를 업데이트한 경우 동시성 충돌을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-147">Production code would generally detect concurrency conflicts when two or more clients concurrently updated a record.</span></span> <span data-ttu-id="9a0b0-148">자세한 내용은 [동시성 충돌 처리](xref:data/ef-mvc/concurrency)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-148">See [Handling concurrency conflicts](xref:data/ef-mvc/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="9a0b0-149">검토 게시 및 바인딩</span><span class="sxs-lookup"><span data-stu-id="9a0b0-149">Posting and binding review</span></span>

<span data-ttu-id="9a0b0-150">*Pages/Movies/Edit.cshtml.cs* 파일을 검사합니다. [!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Edit.cshtml.cs?name=snippet2)]</span><span class="sxs-lookup"><span data-stu-id="9a0b0-150">Examine the *Pages/Movies/Edit.cshtml.cs* file: [!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Edit.cshtml.cs?name=snippet2)]</span></span>

<span data-ttu-id="9a0b0-151">동영상/편집 페이지에 대해 HTTP GET 요청이 만들어지는 경우(예: `http://localhost:5000/Movies/Edit/2`):</span><span class="sxs-lookup"><span data-stu-id="9a0b0-151">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="9a0b0-152">`OnGetAsync` 메서드는 데이터베이스에서 동영상을 가져오고 `Page` 메서드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-152">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="9a0b0-153">`Page` 메서드는 *Pages/Movies/Edit.cshtml* Razor 페이지를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-153">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="9a0b0-154">*Pages/Movies/Edit.cshtml* 파일은 동영상 모델을 페이지에서 사용할 수 있도록 하는 모델 지시문(`@model RazorPagesMovie.Pages.Movies.EditModel`)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-154">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the the movie model available on the page.</span></span>
* <span data-ttu-id="9a0b0-155">편집 양식은 동영상에서 값으로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-155">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="9a0b0-156">동영상/편집 페이지가 게시될 때:</span><span class="sxs-lookup"><span data-stu-id="9a0b0-156">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="9a0b0-157">페이지에서 양식 값은 `Movie` 속성으로 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-157">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="9a0b0-158">`[BindProperty]` 특성은 [모델 바인딩](xref:mvc/models/model-binding)을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-158">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

```csharp
[BindProperty]
public Movie Movie { get; set; }
```

* <span data-ttu-id="9a0b0-159">모델 상태에 오류가 있는 경우(예: `ReleaseDate`를 날짜로 변환할 수 없는 경우) 양식이 제출된 값으로 다시 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-159">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is posted again with the submitted values.</span></span>
* <span data-ttu-id="9a0b0-160">모델 오류가 없는 경우 동영상이 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-160">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="9a0b0-161">인덱스, 만들기 및 삭제 Razor 페이지의 HTTP GET 메서드는 유사한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-161">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="9a0b0-162">만들기 Razor 페이지에서 HTTP POST `OnPostAsync` 메서드는 편집 Razor 페이지의 `OnPostAsync` 메서드와 유사한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-162">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="9a0b0-163">검색은 다음 자습서에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9a0b0-163">Search is added in the next tutorial.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="9a0b0-164">[이전: SQL Server LocalDB 사용](xref:tutorials/razor-pages/sql)
[검색 추가](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="9a0b0-164">[Previous: Working with SQL Server LocalDB](xref:tutorials/razor-pages/sql)
[Adding Search](xref:tutorials/razor-pages/search)</span></span>

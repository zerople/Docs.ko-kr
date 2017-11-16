---
title: "ASP.NET Core Razor 페이지에 검색 추가"
author: rick-anderson
description: "ASP.NET Core Razor 페이지에 검색을 추가하는 방법을 보여 줍니다."
keywords: "ASP.NET Core, 검색, Razor 페이지"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/razor-pages/search
ms.openlocfilehash: 2ffb6f13a7303527444085d137d1acac02d7e0ef
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="adding-search-to-an-aspnet-core-mvc-app"></a>ASP.NET Core MVC 앱에 검색 추가

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 문서에서 *장르* 또는 *이름*으로 동영상을 검색할 수 있는 인덱스 페이지에 검색 기능을 추가합니다.

인덱스 페이지의 `OnGetAsync` 메서드를 다음 코드로 업데이트합니다.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

`OnGetAsync` 메서드의 첫 번째 줄은 동영상을 선택하는 [LINQ](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/) 쿼리를 만듭니다.

```csharp
 var movies = from m in _context.Movie
              select m;
```

쿼리는 이 시점에서*만* 정의되며 데이터베이스에 대해 실행되지 **않았습니다**.

`searchString` 매개 변수에 문자열이 포함되는 경우 동영상 쿼리는 검색 문자열에 대해 필터링하도록 수정됩니다.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()` 코드는 [람다 식](https://docs.microsoft.com/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)입니다. 람다 식은 메서드 기반 [LINQ](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/) 쿼리에서 [Where](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) 메서드 또는 `Contains`(위의 코드에서 사용됨)와 같은 표준 쿼리 연산자 메서드의 인수로 사용됩니다. LINQ 쿼리는 정의될 때 또는 메서드(예: `Where`, `Contains` 또는 `OrderBy`)를 호출하여 수정될 때 실행되지 않습니다. 대신 쿼리 실행이 지연됩니다. 즉, 실현된 값이 반복되거나 `ToListAsync` 메서드가 호출될 때까지 식의 계산이 지연되는 것을 의미합니다. 자세한 내용은 [쿼리 실행](https://docs.microsoft.com/dotnet/framework/data/adonet/ef/language-reference/query-execution)을 참조하세요.

**참고:** [Contains](https://docs.microsoft.com//dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 메서드는 C# 코드에서가 아닌 데이터베이스에서 실행됩니다. 쿼리에 대한 대/소문자 구분은 데이터베이스 및 데이터 정렬에 따라 달라집니다. SQL Server에서 `Contains`는 대/소문자를 구분하는 [SQL LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql)로 매핑됩니다. SQLite에서 기본 데이터 정렬과 함께 대/소문자를 구분합니다.

Movies 페이지로 이동하고 `?searchString=Ghost`와 같은 쿼리 문자열을 URL에 추가합니다(예: `http://localhost:5000/Movies?searchString=Ghost`). 필터링된 동영상이 표시됩니다.

![인덱스 보기](search/_static/ghost.png)

다음 경로 템플릿이 인덱스 페이지에 추가되는 경우 검색 문자열이 URL 세그먼트로 전달될 수 있습니다(예: `http://localhost:5000/Movies/ghost`).

```cshtml
@page "{searchString?}"
```

이전 경로 제약 조건을 통해 쿼리 문자열 값 대신 경로 데이터(URL 세그먼트)로 제목을 검색할 수 있습니다.  `"{searchString?}"`에서 `?`는 선택적 경로 매개 변수임을 의미합니다.

![Url에 추가된 단어 ghost와 두 개의 동영상, Ghostbusters 및 Ghostbusters 2의 반환된 동영상 목록이 있는 인덱스 보기](search/_static/g2.png)

그러나 사용자가 동영상 검색을 위해 URL을 수정하는 것을 기대할 수 없습니다. 이 단계에서 동영상을 필터링하도록 UI가 추가됩니다. 경로 제약 조건 `"{searchString?}"`을 추가한 경우 이를 제거합니다.

*Pages/Movies/Index.cshtml* 파일을 열고 다음 코드에서 강조 표시된 `<form>` 표시를 추가합니다.

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` 태그는 [양식 태그 도우미](xref:mvc/views/working-with-forms#the-form-tag-helper)를 사용합니다. 양식이 제출되면 필터 문자열이 *Pages/Movies/Index* 페이지로 전송됩니다. 변경 내용을 저장하고 필터를 테스트합니다.

![제목 필터 텍스트 상자에 단어 ghost를 입력하여 인덱스 보기](search/_static/filter.png)

## <a name="search-by-genre"></a>장르별 검색

*Pages/Movies/Index.cshtml.cs*에 강조 표시된 다음 속성을 추가합니다.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-)]

`SelectList Genres`는 장르 목록을 포함합니다. 이를 통해 사용자는 목록에서 장르를 선택할 수 있습니다.

`MovieGenre` 속성은 사용자가 선택하는 특정 장르를 포함합니다(예: "서부 영화").

`OnGetAsync` 메서드를 다음 코드로 업데이트합니다.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

다음 코드는 데이터베이스에서 모든 장르를 검색하는 LINQ 쿼리입니다.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

장르의 `SelectList`는 고유 장르를 프로젝션함으로써 생성됩니다.

<!-- BUG in OPS
Tag snippet_selectlist's start line '75' should be less than end line '29' when resolving "[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]"

There is no start line.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]
-->

```csharp
Genres = new SelectList(await genreQuery.Distinct().ToListAsync());
```

### <a name="adding-search-by-genre"></a>장르별 검색 추가

다음과 같이 *Index.cshtml*을 업데이트합니다.

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

장르별, 동영상 제목별 및 둘 다로 검색하여 앱을 테스트합니다.

>[!div class="step-by-step"]
[이전: 페이지 업데이트](xref:tutorials/razor-pages/da1)
[다음: 새 필드 추가](xref:tutorials/razor-pages/new-field)

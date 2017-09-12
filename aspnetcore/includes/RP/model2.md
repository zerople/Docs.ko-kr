<span data-ttu-id="e0932-101">`Movie` 클래스에 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e0932-101">Add the following properties to the `Movie` class:</span></span>

<span data-ttu-id="e0932-102">[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieNoEF.cs?name=snippet_MovieNoEF)]</span><span class="sxs-lookup"><span data-stu-id="e0932-102">[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieNoEF.cs?name=snippet_MovieNoEF)]</span></span>

<span data-ttu-id="e0932-103">`ID` 필드는 데이터베이스에서 기본 키 대신 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e0932-103">The `ID` field is required by the database for the primary key.</span></span>

<a name="dc"></a>
### <a name="add-a-database-context-class"></a><span data-ttu-id="e0932-104">데이터베이스 컨텍스트 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="e0932-104">Add a database context class</span></span>

<span data-ttu-id="e0932-105">*MovieContext.cs*라는 `DbContext` 파생 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e0932-105">Add a `DbContext` derived class named *MovieContext.cs* to the *Models* folder.</span></span>

<span data-ttu-id="e0932-106">[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieContext.cs)]</span><span class="sxs-lookup"><span data-stu-id="e0932-106">[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieContext.cs)]</span></span>

<span data-ttu-id="e0932-107">이전 코드에서는 엔터티 집합에 대한 `DbSet` 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e0932-107">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="e0932-108">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당하고 엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="e0932-108">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>
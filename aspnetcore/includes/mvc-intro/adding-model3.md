
## <a name="test-the-app"></a><span data-ttu-id="cb889-101">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="cb889-101">Test the app</span></span>

* <span data-ttu-id="cb889-102">앱을 실행하고 **Mvc 동영상** 링크를 탭합니다.</span><span class="sxs-lookup"><span data-stu-id="cb889-102">Run the app and tap the **Mvc Movie** link.</span></span>
* <span data-ttu-id="cb889-103">**새로 만들기** 링크를 탭하고 동영상을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cb889-103">Tap the **Create New** link and create a movie.</span></span>

  ![장르, 가격, 릴리스 날짜, 제목에 대한 필드가 포함된 뷰 만들기](../../tutorials/first-mvc-app/adding-model/_static/movies.png)

* <span data-ttu-id="cb889-105">`Price` 필드에는 소수점 또는 쉼표를 입력할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cb889-105">You may not be able to enter decimal points or commas in the `Price` field.</span></span> <span data-ttu-id="cb889-106">소수점으로 쉼표(“,”)를 사용하는 영어가 아닌 로캘 및 미국 영어가 아닌 날짜 형식에 대해 [jQuery 유효성 검사](http://jqueryvalidation.org/)를 지원하려면 앱을 전역화하는 단계를 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb889-106">To support [jQuery validation](http://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="cb889-107">자세한 내용은 https://github.com/aspnet/Docs/issues/4076 및 [추가 리소스](#additional-resources)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb889-107">See https://github.com/aspnet/Docs/issues/4076 and [Additional resources](#additional-resources) for more information.</span></span> <span data-ttu-id="cb889-108">이제 10 같은 정수를 입력하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb889-108">For now, just enter whole numbers like 10.</span></span>

<a name="displayformatdatelocal"></a>

* <span data-ttu-id="cb889-109">일부 로캘에서는 날짜 형식을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb889-109">In some locales you need to specify the date format.</span></span> <span data-ttu-id="cb889-110">아래 강조 표시된 코드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb889-110">See the highlighted code below.</span></span>

<span data-ttu-id="cb889-111">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateFormat.cs?name=snippet_1&highlight=2,10)]</span><span class="sxs-lookup"><span data-stu-id="cb889-111">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateFormat.cs?name=snippet_1&highlight=2,10)]</span></span>

<span data-ttu-id="cb889-112">자습서의 뒷부분에서 `DataAnnotations`에 대해 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="cb889-112">We'll talk about `DataAnnotations` later in the tutorial.</span></span>

<span data-ttu-id="cb889-113">**만들기**를 탭하면 폼이 서버에 게시되고 이 서버에서 동영상 정보가 데이터베이스에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb889-113">Tapping **Create** causes the form to be posted to the server, where the movie information is saved in a database.</span></span> <span data-ttu-id="cb889-114">앱은 */Movies* URL로 리디렉션되고 이 URL에서 새로 만든 정보가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb889-114">The app redirects to the */Movies* URL, where the newly created movie information is displayed.</span></span>

![새로 만든 동영상 목록을 보여 주는 동영상 뷰](../../tutorials/first-mvc-app/adding-model/_static/h.png)

<span data-ttu-id="cb889-116">나머지 몇 개의 동영상 항목을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cb889-116">Create a couple more movie entries.</span></span> <span data-ttu-id="cb889-117">모두 작동하는 **편집**, **세부 정보** 및 **삭제** 링크를 사용해 봅니다.</span><span class="sxs-lookup"><span data-stu-id="cb889-117">Try the **Edit**, **Details**, and **Delete** links, which are all functional.</span></span>

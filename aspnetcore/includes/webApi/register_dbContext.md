## <a name="register-the-database-context"></a><span data-ttu-id="bde63-101">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="bde63-101">Register the database context</span></span>

<span data-ttu-id="bde63-102">이 단계에서 데이터베이스 컨텍스트는 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="bde63-102">In this step, the database context is registered with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="bde63-103">DI(종속성 주입) 컨테이너에 등록된 서비스(예: DB 컨텍스트)를 컨트롤러에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bde63-103">Services (such as the DB context) that are registered with the dependency injection (DI) container are available to the controllers.</span></span>

<span data-ttu-id="bde63-104">[종속성 주입](xref:fundamentals/dependency-injection)에 대한 기본 제공 지원을 사용하여 서비스 컨테이너에 DB 컨텍스트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="bde63-104">Register the DB context with the service container using the built-in support for [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bde63-105">*Startup.cs* 파일의 내용을 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="bde63-105">Replace the contents of the *Startup.cs* file with the following code:</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Startup.cs?highlight=2,4,12)]

<span data-ttu-id="bde63-106">위의 코드:</span><span class="sxs-lookup"><span data-stu-id="bde63-106">The preceding code:</span></span>

* <span data-ttu-id="bde63-107">사용되지 않는 코드를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="bde63-107">Removes the code that is not used.</span></span>
* <span data-ttu-id="bde63-108">메모리 내 데이터베이스를 서비스 컨테이너에 주입하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bde63-108">Specifies an in-memory database is injected into the service container.</span></span>
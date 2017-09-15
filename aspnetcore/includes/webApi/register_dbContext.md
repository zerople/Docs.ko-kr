## <a name="register-the-database-context"></a><span data-ttu-id="01ebd-101">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="01ebd-101">Register the database context</span></span>

<span data-ttu-id="01ebd-102">컨트롤러에 데이터베이스 컨텍스트를 삽입하려면 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에서 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="01ebd-102">In order to inject the database context into the controller, we need to register it with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="01ebd-103">[종속성 주입](xref:fundamentals/dependency-injection)에 대한 기본 제공 지원을 사용하여 서비스 컨테이너에 데이터베이스 컨텍스트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="01ebd-103">Register the database context with the service container using the built-in support for [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="01ebd-104">*Startup.cs* 파일의 내용을 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="01ebd-104">Replace the contents of the *Startup.cs* file with the following:</span></span>

<span data-ttu-id="01ebd-105">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Startup.cs?highlight=2,4,12)]</span><span class="sxs-lookup"><span data-stu-id="01ebd-105">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Startup.cs?highlight=2,4,12)]</span></span>

<span data-ttu-id="01ebd-106">위의 코드:</span><span class="sxs-lookup"><span data-stu-id="01ebd-106">The preceding code:</span></span>

* <span data-ttu-id="01ebd-107">사용하지 않는 코드를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="01ebd-107">Removes the code we're not using.</span></span>
* <span data-ttu-id="01ebd-108">메모리 내 데이터베이스를 서비스 컨테이너에 주입하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="01ebd-108">Specifies an in-memory database is injected into the service container.</span></span>

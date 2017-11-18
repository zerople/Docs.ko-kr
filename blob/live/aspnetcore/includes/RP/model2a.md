<a name="cs"></a>
### <a name="add-a-database-connection-string"></a><span data-ttu-id="e1f80-101">데이터베이스 연결 문자열 추가</span><span class="sxs-lookup"><span data-stu-id="e1f80-101">Add a database connection string</span></span>

<span data-ttu-id="e1f80-102">*appsettings.json* 파일에 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e1f80-102">Add a connection string to the *appsettings.json* file.</span></span>

[!code-json[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

<a name="reg"></a>
###  <a name="register-the-database-context"></a><span data-ttu-id="e1f80-103">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="e1f80-103">Register the database context</span></span>

<span data-ttu-id="e1f80-104">*Startup.cs* 파일에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 데이터베이스 컨텍스트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e1f80-104">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in the *Startup.cs* file.</span></span>
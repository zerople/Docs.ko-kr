<a name="scaffold"></a>
### <a name="scaffold-the-movie-model"></a><span data-ttu-id="16f54-101">Movie 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="16f54-101">Scaffold the Movie model</span></span>

* <span data-ttu-id="16f54-102">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="16f54-102">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="16f54-103">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="16f54-103">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

<span data-ttu-id="16f54-104">오류가 표시될 경우:</span><span class="sxs-lookup"><span data-stu-id="16f54-104">If you get the error:</span></span>
  ```
  The process cannot access the file 
 'RazorPagesMovie/bin/Debug/netcoreapp2.0/RazorPagesMovie.dll' 
  because it is being used by another process.
  ```

<span data-ttu-id="16f54-105">Visual Studio를 종료하고 명령을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="16f54-105">Exit Visual Studio and run the command again.</span></span>
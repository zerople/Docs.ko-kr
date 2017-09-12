<a name="scaffold"></a>
### <a name="scaffold-the-movie-model"></a>Movie 모델 스캐폴드

* 프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.
* 다음 명령을 실행합니다.

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```
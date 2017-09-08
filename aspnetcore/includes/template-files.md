* Startup.cs: [시작 클래스](../fundamentals/startup.md) -클래스는 응용 프로그램에 대 한 모든 요청을 처리 하는 요청 파이프라인을 구성 합니다.
* Program.cs: [Program 클래스](../fundamentals/index.md) 하는 응용 프로그램의 주 진입점을 포함 합니다.
* firstapp.csproj: [프로젝트 파일](https://docs.microsoft.com/dotnet/articles/core/preview3/tools/csproj) ASP.NET Core 응용 프로그램에 대 한 MSBuild 프로젝트 파일 형식입니다. 프로젝트 간 참조가 NuGet 참조 및 기타 프로젝트 관련된 항목입니다.
* appsettings.json / appsettings 합니다. Development.json: 환경 기본 응용 프로그램 설정 구성 파일입니다. [구성 참조](xref:fundamentals/configuration)합니다.
* bower.json: 프로젝트에 대 한 패키지 종속성 Bower 합니다.
* .bowerrc: bower 구성 파일을 Bower 자산을 다운로드 하는 경우 구성 요소를 설치 위치를 정의 합니다.
* bundleconfig.json: 묶음 및 축소 프런트 엔드 JavaScript 및 CSS 자산에 대 한 구성 파일입니다.
* 보기: Razor 뷰를 포함합니다. 뷰는 응용 프로그램의 UI (사용자 인터페이스)를 표시 하는 구성 요소입니다. 일반적으로이 UI는 모델 데이터를 표시 합니다.
* 컨트롤러: 처음에 포함 된 MVC 컨트롤러 *HomeController.cs*합니다. 컨트롤러는 브라우저 요청을 처리 하는 클래스입니다.
* wwwroot: 웹 응용 프로그램 루트 폴더입니다.

자세한 내용은 참조 [The MVC 패턴](xref:mvc/overview)합니다.

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 섹션에서는 데이터베이스에서 동영상을 관리하기 위한 클래스를 추가합니다. 이러한 클래스를 EF Core([Entity Framework Core](https://docs.microsoft.com/ef/core))와 함께 사용하여 데이터베이스 작업을 수행합니다. EF Core는 작성해야 하는 데이터 액세스 코드를 간소화하는 ORM(개체-관계형 매핑) 프레임워크입니다.

직접 만드는 모델 클래스는 EF Core에 대한 종속성이 없으므로 POCO(Plain Old CLR Object) 클래스로 알려져 있습니다. 이 클래스는 데이터베이스에 저장되는 데이터의 속성을 정의합니다.

이 자습서에서는 먼저 모델 클래스를 작성하면 EF Core가 데이터베이스를 만듭니다. 이 문서에서 설명하지 않는 대체 방법은 [기존 데이터베이스에서 모델 클래스를 생성](https://docs.microsoft.com/ef/core/get-started/aspnetcore/existing-db)하는 것입니다.

샘플을 [보거나 다운로드합니다](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie).
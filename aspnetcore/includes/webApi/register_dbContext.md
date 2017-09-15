## <a name="register-the-database-context"></a>데이터베이스 컨텍스트 등록

컨트롤러에 데이터베이스 컨텍스트를 삽입하려면 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에서 등록해야 합니다. [종속성 주입](xref:fundamentals/dependency-injection)에 대한 기본 제공 지원을 사용하여 서비스 컨테이너에 데이터베이스 컨텍스트를 등록합니다. *Startup.cs* 파일의 내용을 다음으로 바꿉니다.

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Startup.cs?highlight=2,4,12)]

위의 코드:

* 사용하지 않는 코드를 제거합니다.
* 메모리 내 데이터베이스를 서비스 컨테이너에 주입하도록 지정합니다.

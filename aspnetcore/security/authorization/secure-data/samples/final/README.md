# <a name="how-to-buildrun-secure-user-data-sample"></a><span data-ttu-id="d04b9-101">보안 사용자 데이터 샘플을 빌드/실행 하는 방법</span><span class="sxs-lookup"><span data-stu-id="d04b9-101">How to build/run Secure user data sample</span></span>

* <span data-ttu-id="d04b9-102">암호 관리자 도구와 함께 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d04b9-102">Set password with the Secret Manager tool:</span></span>

  `dotnet user-secrets set SeedUserPW <pw>`

* <span data-ttu-id="d04b9-103">데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="d04b9-103">Update the database:</span></span>

    `dotnet ef database update`

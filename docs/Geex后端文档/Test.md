---
sidebar_position: 3
---

# 单元测试

在生成的默认模板中，我们有一个Test项目，提供给我们来写工作单元。

工作单元种子数据在`TestDataMigration.cs`文件中加入种子数据
```cs
public class TestData
    {
        public static xxx EditTestMainProject = new xxx("虹桥天地01", "0001", null, DateTime.Now, "地址1")
        { Id = ObjectId.GenerateNewId().ToString() };

        public static xxx DeleteTestMainProject = new xxx("虹桥天地02", "0002", null, DateTime.Now, "地址1")
        { Id = ObjectId.GenerateNewId().ToString() };

        public static xxx AuditTestMainProject = new xxx("虹桥天地03", "0003", null, DateTime.Now, "地址1")
        { Id = ObjectId.GenerateNewId().ToString(), AuditStatus = AuditStatus.Submitted };

        public static xxx SubmittedTestMainProject = new xxx("虹桥天地04", "0004", null, DateTime.Now, "地址1")
        { Id = ObjectId.GenerateNewId().ToString() };


        public static List<xxx> QueryTestMainProject = new List<xxx>()
        {

           new  xxx("虹桥天地05", "0005", null, DateTime.Now, "地址1")
                { Id = ObjectId.GenerateNewId().ToString() },

           new xxx("虹桥天地06", "0006", null, DateTime.Now, "地址1")
            { Id = ObjectId.GenerateNewId().ToString()}

        };
        public class _937632330490465147_TestDataMigration : DbMigration
        {

            public static List<xxx> Companies = new List<xxx>() { EditTestMainProject, DeleteTestMainProject, AuditTestMainProject, SubmittedTestMainProject }.Concat(QueryTestMainProject).ToList();

            public override async Task UpgradeAsync(DbContext dbContext)
            {
                dbContext.Attach(Companies);
                await Companies.SaveAsync();
            }
        }

    }
```


工作单元示例
```cs
 public class xxxModuleTests : ModuleTestBase<xxxxxxCoreModule>
    {
        private readonly IMediator _mediator;

        public xxxModuleTests()
        {
            _mediator = GetRequiredService<IMediator>();
        }

        protected override void SetAbpApplicationCreationOptions(AbpApplicationCreationOptions options)
        {
            try
            {
                base.SetAbpApplicationCreationOptions(options);
            }
            catch (Exception e)
            {
                this.Dispose();
                throw;
            }
        }



        public class CreatexxxRequestTestData : TheoryData<CreateMainProjectRequest>
        {
            public CreatexxxRequestTestData()
            {
                Add(new CreateMainProjectRequest()
                {
                    Name = "测试公司添加功能",
                    Code = "9292929",
                    Address = "上海市-虹桥商务区",
                    CompanyId = "",
                    GetDate = DateTime.Now
                });
            }
        }

        [Theory]
        [ClassData(typeof(CreatexxxRequestTestData))]
        public async Task CreateMainProjectRequest_Should_Work(CreateMainProjectRequest request)
        {
            string id = null;
            // 测试功能
            await base.WithUow(async () =>
            {
                var result = await this._mediator.Send(request);
                id = result.Id;
            });

            // 副作用操作校验结果
            var check = await GetRequiredService<DbContext>().Queryable<MainProject>().OneAsync(id);
            check.Name.ShouldBe(request.Name);
            check.Code.ShouldBe(request.Code);
            check.Address.ShouldBe(request.Address);
            check.CompanyId.ShouldBe(request.CompanyId);
        }


        [Fact]
        public async Task EditMainProjectRequest_Should_Work()
        {
            await base.WithUow(async () =>
            {
                await this._mediator.Send(new EditMainProjectRequest()
                {
                    Id = TestData.EditTestMainProject.Id,
                    Name = TestData.EditTestMainProject.Name,
                    Code = TestData.EditTestMainProject.Code,
                    Address = TestData.EditTestMainProject.Address + "-万科",
                    CompanyId = TestData.EditTestMainProject.CompanyId,
                    GetDate = TestData.EditTestMainProject.GetDate,
                });
            });

            var check = await GetRequiredService<DbContext>().Queryable<MainProject>().OneAsync(TestData.EditTestMainProject.Id);
            check.Name.ShouldNotBeNull();
            check.Address.ShouldBe(TestData.EditTestMainProject.Address + "-万科");

        }


        [Fact]
        public async Task DeleteMainProjectRequest_Should_Work()
        {
            await base.WithUow(async () =>
            {
                await this._mediator.Send(new DeleteMainProjectRequest()
                {
                    Ids = new List<string>() { TestData.DeleteTestMainProject.Id }
                });
            });

            var check = await GetRequiredService<DbContext>().Queryable<MainProject>().OneAsync(TestData.DeleteTestMainProject.Id);
            check.ShouldBe(null);
        }

        [Fact]
        public async Task AuditMainProjectRequest_Should_Work()
        {
            await base.WithUow(async () =>
            {
                await this._mediator.Send(new AuditRequest<IMainProject>(nameof(AuditMainProjectRequest_Should_Work), TestData.AuditTestMainProject.Id));

            });

            var check = await GetRequiredService<DbContext>().Queryable<MainProject>().OneAsync(TestData.AuditTestMainProject.Id);
            check.AuditStatus.ShouldBe(AuditStatus.Audited);
            check.AuditRemark.ShouldBe(nameof(AuditMainProjectRequest_Should_Work));

        }

        [Fact]
        public async Task SubmittedMainProjectRequest_Should_Work()
        {
            await base.WithUow(async () =>
            {
                await this._mediator.Send(new SubmitRequest<IMainProject>(nameof(SubmittedMainProjectRequest_Should_Work), TestData.SubmittedTestMainProject.Id));

            });

            var check = await GetRequiredService<DbContext>().Queryable<MainProject>().OneAsync(TestData.SubmittedTestMainProject.Id);
            check.AuditStatus.ShouldBe(AuditStatus.Submitted);
            check.AuditRemark.ShouldBe(nameof(SubmittedMainProjectRequest_Should_Work));
        }


        [Fact]
        public async Task QueryInput_Should_Work()
        {
            // 测试功能
            await base.WithUow(async () =>
             {
                 var result = await _mediator.Send(new QueryInput<IMainProject>());
                 result.Count().ShouldBeGreaterThan(0);
             });
        }
    }
```

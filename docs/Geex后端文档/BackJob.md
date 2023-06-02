
---
sidebar_position: 5
---

# BackJob


Job实体类继承`JobState`, Handel继承`StatefulCronJob/CronJob`.
```cs
     context.Services.AddJob<TestJobJobStateJob>("0/3 * * * * *");


 public class TestJobJobState : JobState
    {
        public string Name {get; set;}
    }


    public class TestJobJobStateJob : StatefulCronJob<TestJobJobStateJob, TestJobJobState>
    {

        private const int MaxFailCount = 5;

           public override async Task Run(IServiceProvider serviceProvider, TestJobJobState state,
            CancellationToken cancellationToken)
        {
        }
    }
```

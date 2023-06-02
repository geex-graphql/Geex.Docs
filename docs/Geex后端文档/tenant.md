---
sidebar_position: 1
---

# 多租户SASS

在项目中引入**Geex.Common.MultiTenant**包。

在 **AppModule.cs** 中添加`MultiTenantModule`模块代码
```cs
[DependsOn(
        typeof(GeexCoreModule),
        typeof(GeexBookModule),
        typeof(MultiTenantModule),
        )]
    public class AppModule : GeexEntryModule<AppModule>
    {

    }
```

项目会自动创建Tenant表，会提供Tenant相关的CRUD接口。并提供`ICurrentTenant`接口用于获取当前租户信息

```cs
    public interface ICurrentTenant
    {
        public string? Code { get; }
        public ITenant Detail { get; }
        public IDisposable Change(string? tenantCode);
    }
```

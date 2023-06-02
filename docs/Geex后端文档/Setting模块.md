---
sidebar_position: 14
---

# Setting模块

在自动生成的模板项目中会有一个*Settings.cs文件，用于存放针对于当前业务的Setting配置，该配置可以指定针对范围（全局、租户、用户）
```cs
    public class LocalizationSettings : SettingDefinition
    {
        public LocalizationSettings([NotNull] string name, SettingScopeEnumeration[] validScopes,
            [CanBeNull] string? description = null, bool isHiddenForClients = false) : base(nameof(Localization) + name, validScopes, description, isHiddenForClients)
        {
        }
        public static LocalizationSettings Language { get; } = new(nameof(Language), new[] { SettingScopeEnumeration.Global, SettingScopeEnumeration.User, });
        public static LocalizationSettings Data { get; } = new(nameof(Data), new[] { SettingScopeEnumeration.Global, });
    }
```

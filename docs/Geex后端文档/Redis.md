---
sidebar_position: 13
---

# Redis

我们对Redis进行了一个简单的二次封装，提供更加简单的Redis调用。

```cs
private readonly IRedisDatabase _redisDatabase;

var keys = await _redisDatabase.SearchKeysAsync(searchPattern);
var rawCreditAmountInfos = await _redisDatabase.GetAllAsync<CreditBalanceInfo>(keys);
```

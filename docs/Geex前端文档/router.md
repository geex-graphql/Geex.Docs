---
sidebar_position: 4
---

# 标准化的Url查询(根据查询条件动态同步路由)

整个项目采用标准化的参数路由进行实现，所有的页面及查询条件都会反应在路由参数中。

![Docusaurus Plushie](./image/router.png)


```ts
@Injectable({
  providedIn: "root",
})
export class xxxxxResolveGuard extends RoutedComponentResolveBase<invoiceListParams> {
  key = "institutions";
  constructor(injector: Injector) {
    super(injector);
  }
  completeRouteParams(queryParams: { [x: string]: any }): void {}
  routeQueryParamsToParams(params: { [x: string]: any }): invoiceListParams {
    let resolvedParams: invoiceListParams = {
      filter: params["filter"] ?? null,
      pi: params["pi"] ?? 1,
      ps: params["ps"] ?? 10,
    };
    return resolvedParams;
  }
}

```

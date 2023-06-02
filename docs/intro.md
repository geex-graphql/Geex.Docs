---
sidebar_position: 2
---

# 示例项目入门指南

该教程讲解如果从0开始如何创建项目并完成CURD操作。

## 创建后端项目

启动`Geex.TemplateGenerator`项目

根据提示输入下面示例内容
```cs
enter org name.
Geex
enter project name.
Demo
enter module name.
Book
enter default aggregate name.
Books
enter template path, press enter if it's current directory

```
代码生成完成后直接打开项目并运行，打开`http://localhost:5000/graphql/`


完成后端的接口用例 （该接口文档可以登录后，组织内共享）
```md
fragment BooksListBrief on Books {
  id
  name
}



query testBooksPageList {
  booksPagedList(input: { filter: "", pageIndex: 0, pageSize: 10 }) {
    items {
      ...BooksListBrief
    }
    totalCount
    totalPage
  }
}

query testInvoiceById {
  booksById(id: "643e45b70904cd04d6e3f92a") {
    id
    name
  }
}

mutation testCreateBooks {
  createBooks(input: { name: "ASP.NET 5" }) {
    id
  }
}

mutation testEditBooks {
  editBooks(input: { id: "643e45550904cd04d6e3f929", name: "ASP.NET Core 5.0" }) {
    id
  }
}

mutation testDeleteBooks {
  deleteBooks(input: { id: "64479e7f803c99d4090817fa" }) {
    id
  }
}

```

接口用例通过后，完成编写接口函数(这一步可以由前端完成)

```md

query booksList($input: GetBooksPagedListInput) {
  booksPagedList(input: $input) {
    items {
      ...BooksListBrief
    }
    totalCount
    totalPage
  }
}

query booksById($id: String) {
  booksById(id: $id) {
    id
    name
  }
}

mutation createBooks($input: CreateBooksRequestInput) {
  createBooks(input: $input) {
    id
  }
}
mutation editBooks($input: EditBooksRequestInput) {
  editBooks(input: $input) {
    id
  }
}

mutation deleteBooks($input: DeleteBooksRequestInput) {
  deleteBooks(input: $input) {
    id
  }
}

```


## 前端项目搭建

拉取前端框架
```cs
https://github.com/geex-graphql/Geex.Angular.git
```

当后端接口测试完成后，如果代码按照命名规范完成，这里就可以使用前端代码生成命令

```cmd

创建一个名字为book的模块
npx ng g ng-alain:module book

为book模块创建 graphql实体文件
npx ng g ng-alain:tpl graphql graphql -m=book --modal=false

填充graphql文件的数据执行`npm run gqlgen`生成代码。

为book模块创建列表页
npx ng g ng-alain:tpl list list -m=book --modal=false -- '[{\"title\":\"books\"},{\"module\":\"book\"}]'

为book模块创建编辑页
npx ng g ng-alain:tpl edit edit -m=book --modal=false  -- '[{\"title\":\"books\"},{\"module\":\"book\"}]'

```




修改invoice路由配置,覆盖掉原有的路由代码
```cs
const routes: Routes = [
  { path: "", component: BookListComponent, resolve: { params: BooksResolveGuard }, runGuardsAndResolvers: "always" },
  { path: "edit", component: BookEditComponent, data: {} },
  { path: "edit/:id", component: BookEditComponent, data: {} },
];
```


如果需要呈现菜单(待修正，后期会用后端控制显示)
```cs
          {
            "text": "书籍",
            "i18n": "menu.book",
            "icon": "anticon anticon-rocket",
            "link": "book"
          }
```

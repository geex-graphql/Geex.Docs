---
sidebar_position: 1
---
# 入门教程

## intro

框架的目标
1. 枚举一切, no more "magic string(number)"<!-- 组里不再会出现互相问"数据库里\代码里\后端返回值里枚举1是代表什么意思, 一个系统几十个枚举, 几百个枚举值, 再也不用边写代码边看文档了" -->
2. 符合直觉的代码体验，开箱即用的最佳实践<!-- 目前很多基于DDD的实践都引入了一大堆的概念, 却没有针对这一堆概念做一个最佳实践, 我们的目标就是用最符合直觉、手写代码量最小的方式去实现这样的一个最佳实践（至少目前用过的人评价都还不错） -->

技术体系<!-- 一些技术框架的关键词, 不一一列举, 大家有兴趣可以自行查阅了解, 后续的演示部分会有涉及 -->
前端: angular 15 + ngxs + apollo client(强类型的api调用)
后端: graphql(hotchocolate) + 模块化开发(单体项目和微服务项目的快速切换) + mongodb + elk(日志及链路追踪) + casbin(授权体系(强类型)) + openiddict(认证体系, 单点登录) + 多租户 + 动态配置(强类型配置, 支持前端配置)
开发环境: docker一键部署所有依赖 + 本地\测试环境一键切换 + 基于https的本地\测试环境联调

<!-- 具体的内容会在稍后讲到 -->
# 1分钟搭建前后端解决方案
<!-- 模板生成器 -->
GitHub地址`https://github.com/geex-graphql/Geex.TemplateGenerator`

启动项目`Geex.TemplateGenerator`

根据提示输入信息，创建项目解决方案，项目默认创建在模板项目的根目录下`\bin\Debug\net7.0\.generated\Geex.Bms.Demo`

```cmd
项目和组织信息默认识别camel case. eg. geex=>Geex, bms=>Bms
enter org name.
geex -- 公司的名字或者组织的名字
enter project name.
bms  -- 项目名称
enter module name.
demo -- 默认的模块我们就是demo
enter default aggregate name.
book 
enter template path, press enter if it's `simple_module`
fullstack
```
<!-- 模板生成器提供4类模板fullstack全栈项目, 还有微服务模块项目, 微服务模块可以在全栈模板中作为一个项目被直接引用, 也可以直接作为一个graphql的微服务启动, 因为基于GQL, 两种方式都有完整的Schema支持(或者说智能提示) -->



打开项目后我们先启动`dev_env.ps1`脚本,他会帮我做以下操作
* 生成开发证书
* 反向代理服务器
* 拉取Docker镜像
* 检查并安装nodejs、yarn、husky等
* 执行前端yarn


我们打开解决方案根目录后最好先执行一下`git init`先做个提交.

我们在Server目录下打开后端项目，直接启动`https://bms.api.dev.geex.com/graphql/`,我们后端项目的默认启动端口是`8020`,前面`dev_env.ps1`脚本中，我们代理映射到了这个端口

我们新建一个页面，来测试一下接口，这里可以加入`where: { name:{ contains:"123" } }`和分页来进行演示`graphql`的操作,我们可以看一下network请求,这里看到大家可能会觉得
```gql
query testBook {
  books(input: { name: "" }) {
    items {
      id
      name
      createdOn
    }
  }
}



mutation testCreateBook {
    createBook( input: {  name:"test123456" } ) {
       id
    }
}
```

```gql
query testBook {
  books(
    input: { name: "" }
    skip: 0
    take: 10
    where: { name: { contains: "123" } }
  ) {
    items {
      id
      name
      createdOn
    }
     totalCount
  }
}
```

* 你这接口怎么这样,你让我们前端怎么办.

后端效果看完了，我们来看下前端

前端先执行`yarn gqlgen`同步一下前端接口代码, 避免前端大量报错，然后我们使用模板创建一下Book的业务模块, 包含一个列表页和一个编辑页

执行命令`yarn ng g .\geex-schematics:module book --add-to-layout=LayoutBasicComponent`,创建后执行`yarn gqlgen`初始化代理

打开页面后我们就可以看到如下页面，并完成CURD操作。

# 快速完善业务代码（后端、测试、前端）<!-- 默认生成的代码对象中只有一个name字段 -->

接下来我们来完善这个book业务，看一下开发流程。
完善Book实体,
<!-- 所有的业务改动从后端实体的修改开始, 增加一些字段, 增加构造函数 -->
```cs
using System;
using System.Linq;
using Geex.Common.Abstraction.Auditing;
using Geex.Common.Abstraction.Entities;
using Geex.Common.Abstraction.Gql.Inputs;
using Geex.Common.Abstraction.Storage;
using Microsoft.Extensions.DependencyInjection;
using MediatR;



        public Book(string name, string cover, string author, string press, DateTimeOffset publicationDate, string iSBN)
        {
            Name = name;
            Cover = cover;
            Author = author;
            Press = press;
            PublicationDate = publicationDate;
            ISBN = iSBN;
        }

        public string Name { get; set; }
        
        public string Cover { get; set; }

        //* 文件支持：DB、OSS存储使用方式只需要引入IBlobObject实体：原来是单体项目分模块，走MeditR，之前是为了实现一个模块化的，跨模块调用所以引入了MeditR，但是MeditR会增加项目复杂度，

        public virtual IBlobObject? Attachments => ServiceProvider.GetService<IMediator>().Send(new QueryInput<IBlobObject>()).Result.FirstOrDefault(x => x.Id == Cover);
        
        public string Author { get; set; }
        public string Press { get; set; }
        public DateTimeOffset PublicationDate { get; set; }
        public string ISBN { get; set; }
```

<!-- 接下来调整一下对应的接口业务代码, 因为book有字段的调整, 所以创建\修改book的逻辑需要有相应的调整 -->
```cs

 public async Task<Book> CreateBook(CreateBookInput input)
        {
            var entity = new Book(input.Name, input.Cover, input.Author, input.Press, input.PublicationDate, input.ISBN);

            return _dbContext.Attach(entity);
        }


        public async Task<bool> EditBook(string id, EditBookInput input)
        {
            var entity = _dbContext.Queryable<Book>().FirstOrDefault(x => x.Id == id);
            if (input.Name != default)
            {
                entity.Name = input.Name;
            }

            if (input.Cover != default)
            {
                entity.Cover = input.Cover;
            }

            if (input.Author != default)
            {
                entity.Author = input.Author;
            }

            if (input.Press != default)
            {
                entity.Press = input.Press;
            }

            if (input.PublicationDate != default)
            {
                entity.PublicationDate = input.PublicationDate;
            }

            if (input.ISBN != default)
            {
                entity.ISBN = input.ISBN;
            }
            return true;
        }
   
```

<!-- 更新创建\修改book所需要的参数, 同样对应增加的字段 -->
```cs
    public class CreateBookInput : IRequest<Book>
    {
        public string Name { get; set; }
        public string Cover { get; set; }
        public string Author { get; set; }
        public string Press { get; set; }
        public DateTimeOffset PublicationDate { get; set; }
        public string ISBN { get; set; }
    }



    public class EditBookInput : IRequest<Unit>
    {
        public string? Name { get; set; }
        public string? Cover { get; set; }
        public string? Author { get; set; }
        public string? Press { get; set; }
        public DateTimeOffset? PublicationDate { get; set; }
        public string? ISBN { get; set; }
    }
```

我们回到`graphql`页面，补充一下查询字段可以发现，报错了，这是因为我们在Book实体上的一些字段不允许为空
```gql
query testBook {
  books(
    input: { name: "" }
    skip: 0
    take: 10
    where: { name: { contains: "123" } }
  ) {
    items {
      id
      name
      isbn
      cover
      publicationDate
      press
      author
      createdOn
    }
    totalCount
  }
}
```

我们删除掉错误的历史数据，然后重新调用一下接口，没有问题,那后端的部分就完成了, 这里可以看到在后端告知前端可以跑之前，我们已经在流程上强制让后端做了一次接口测试，来回顾和测试自己的接口。

```gql
mutation testCreateBook {
  createBook(
    input: {
      name: ".Net Core入门实战"
      isbn: "SN123"
      press: "机械工业"
      author: "初久"
      publicationDate: "2019-10-20"
      cover: ""
    }
  ) {
    id
  }
}

```

我们来看前端，后端完成后，将测试文件发送给前端。前端补充相应字段，执行`yarn gqlgen`生成前端接口代码。我们现在可以去list页面看一下实体的字段，现在打开列表页面，业务完成了。

<!-- 在当前页面的表格中增加一些字段, 此处的index属性表示从对象中读取哪些属性绑定到表格中, 支持嵌套, 有智能提示 -->
```ts
        {
          title: "封面",
          type: "img",
          index: "attachments.url",
          className: ["text-center"],
        },
        {
          title: "作者",
          index: "author",
          className: ["text-center"],
        },
        {
          title: "出版社",
          index: "press",
          className: ["text-center"],
        },
        {
          title: "出版时间",
          index: "publicationDate",
          type: "date",
          dateFormat: "yyyy-MM-dd",
          className: ["text-center"],
          sort: {
            key: "publicationDate",
            default: params.sort?.publicationDate,
          },
        },
        {
          title: "ISBN",
          index: "isbn",
          className: ["text-center"],
        },
```

我们来完善一下添加和编辑页面,表单页面要改的东西比较多，我们将book的业务部分补充到edit页面。现在我们来测试一下，一个Book的业务就全部完成了。

```ts

// 用于约束添加/编辑，哪些字段参与编辑
type EntityEditablePart = Pick<Book, "name" | "cover" | "author" | "press" | "publicationDate" | "isbn">;

// 用于约束详情接口会返回那些字段
type BookEditPageContext = EditDataContext<
  BookDetailFragment,
  keyof EntityEditablePart
> & {
  disabled: boolean;
};


export class BookEditPage extends RoutedEditComponent<
  BookEditPageParams,
  BookDetailFragment,
  keyof EntityEditablePart
> 


 formConfig = {
        name: new FormControl(entity.name, Validators.required),
        author: new FormControl(entity.author, Validators.required),
        cover: new FormControl(entity.cover, Validators.required),
        press: new FormControl(entity.press, Validators.required),
        publicationDate: new FormControl(entity.publicationDate, Validators.required),
        isbn: new FormControl(entity.isbn, Validators.required),
      };
    } else {
      formConfig = {
        name: new FormControl("", Validators.required),
        author: new FormControl("", Validators.required),
        cover: new FormControl("", Validators.required),
        press: new FormControl("", Validators.required),
        publicationDate: new FormControl("", Validators.required),
        isbn: new FormControl("", Validators.required),
      };
    }



                name: this.context.entityForm.value.name,
                author: this.context.entityForm.value.author,
                cover: this.context.entityForm.value.cover,
                press: this.context.entityForm.value.press,
                publicationDate: this.context.entityForm.value.publicationDate,
                isbn: this.context.entityForm.value.isbn,
```

```
    <nz-form-item>
      <nz-form-label [nzSm]="4"
                     [nzXs]="24"
                     nzRequired>封面</nz-form-label>
      <nz-form-control [nzSm]="20"
                       [nzXs]="24">

        <geex-upload [formControl]="context.entityForm.controls.cover"
                     [nzShowUploadList]="true">
          <ng-template #uploadButton>
            <button nz-button
                    nzType="primary">
              <i nz-icon
                 nzType="arrow-up"></i>
              导入
            </button>
          </ng-template>
        </geex-upload>
      </nz-form-control>
    </nz-form-item>

    <nz-form-item>
      <nz-form-label [nzSm]="4"
                     [nzXs]="24"
                     nzRequired>作者</nz-form-label>
      <nz-form-control [nzSm]="20"
                       [nzXs]="24">
        <input nz-input
               [formControl]="context.entityForm.controls.author">
      </nz-form-control>
    </nz-form-item>

    <nz-form-item>
      <nz-form-label [nzSm]="4"
                     [nzXs]="24"
                     nzRequired>出版社</nz-form-label>
      <nz-form-control [nzSm]="20"
                       [nzXs]="24">
        <input nz-input
               [formControl]="context.entityForm.controls.press">
      </nz-form-control>
    </nz-form-item>

    <nz-form-item>
      <nz-form-label [nzSm]="4"
                     [nzXs]="24"
                     nzRequired>出版时间</nz-form-label>
      <nz-form-control [nzSm]="20"
                       [nzXs]="24">
        <nz-date-picker [formControl]="context.entityForm.controls.publicationDate"></nz-date-picker>
      </nz-form-control>
    </nz-form-item>

    <nz-form-item>
      <nz-form-label [nzSm]="4"
                     [nzXs]="24"
                     nzRequired>ISBN</nz-form-label>
      <nz-form-control [nzSm]="20"
                       [nzXs]="24">
        <input nz-input
               [formControl]="context.entityForm.controls.isbn">
      </nz-form-control>
    </nz-form-item>
```







# 简短的介绍一下Geex框架

#### 后端：

后端开发的规范总是围绕着DDD领域模型设计，但是90%在说DDD的人都没有真正理解和实践过DDD，或者只是简单地按照书本中的示例进行了模仿而没有真正掌握其核心思想。由于DDD本身较为复杂，需要理论和工具的支持更需要领域专家与开发人员进行沟通。

Geex后端框架也是根据DDD方法论设计的，旨在帮助开发人员构建高质量、可维护、可扩展的应用程序。为了让开发人员能够更加快速、简单地编写代码，Geex提供了一系列基于DDD思想的快捷开发工具和规范。
* DDD, 但不为了DDD而DDD：领域驱动设计（Domain-Driven Design）, 目前DDD的落地框架，大多引入了太多的设计概念, 而且对这些概念也没有良好的封装，系统里面各种领域服务及其对应的接口十分冗杂, 开发体验不好, 然而针对大多数没有跨领域调用需求的业务模块, 我们并不需要这些"解耦", 我们在中小型项目的模型去掉了这些冗余, 同时我们也保留了大型项目通过Mediator的解耦的可能性, 并提供了对应的代码生成器
* 充血模型：为了减少代码重复和耦合，提高代码可读性和可维护性，我们采用充血模型设计，所有的业务源头都指向一个强功能的业务对象, 业务内聚在对象本身上, 不再需要所谓的"领域服务(xxxService\xxxManager)", 模型合并行为和状态，支持依赖注入, 可以实现全功能的业务逻辑。
* CQRS接口设计：CQRS是指命令查询职责分离（Command Query Responsibility Segregation），写操作使用事务安全的命令模型，读操作使用高性能优化的查询模型。命令模型注重与业务数据的增删改等业务逻辑。查询模型注重业务数据的只读查询和分析。
* 补充

#### 前端：

Geex的前端功能也十分强大，我们封装了非常多的通用工具来帮助前端开发，如基于最佳实践的项目结构设计、（List、Edit、View、Module）等组件\模块的基类、前端Linq表达式扩展、强类型的客户端代理生成器(无需手动编写HTTP请求和处理响应)、动态Table表格、非常多的业务功能组件、路由参数解析和表单约束等。可以让开发人员专注于业务逻辑的实现，从而提高开发效率和代码质量。
* 补充



# 深入教学

现在我们来把这个Book实体完善一下，完成一个BMS图书管理系统，给大家演示一下框架的一部分高级操作。

首先我们把后端实体和文件创建一下,节省时间，我们直接把提前准备好的文件copy过来。


整个业务结构不是非常复杂，我们包含以下实体：

* 图书分类
* 图书
* 借阅记录
* 借阅人

功能：
* 图书分类管理
* 图书管理
* 借阅人管理
* 图书借阅（单人同时借阅数量不能大于3）
* 图书归还
* 图书借阅记录查询
* 借阅人借阅记录查询



```cs
        public static DemoSettings MaxBorrowingQtySettings { get; } = new(nameof(MaxBorrowingQtySettings), new[] { SettingScopeEnumeration.Global, }, "MaxBorrowingQtySettings", JsonValue.Create(3));
```


```ts

      {
        path: "book",
        loadChildren: () => import("./book/book.module").then(m => m.BookModule),
      },
      {
        path: "book-category",
        loadChildren: () => import("./book-category/book-category.module").then(m => m.BookCategoryModule),
      },
      {
        path: "reader",
        loadChildren: () => import("./reader/reader.module").then(m => m.ReaderModule),
      },
      { path: "borrow", loadChildren: () => import("./borrow/borrow.module").then(m => m.BorrowModule) },




{
              i18n: null,
              group: false,
              hideInBreadcrumb: false,
              children: null,
              text: "图书分类",
              icon: "anticon anticon-solution",
              shortcutRoot: false,
              link: "/book-category",
              badge: 0,
              // acl: "contracts_query_contracts",
              shortcut: false,
            },
            {
              i18n: null,
              group: false,
              hideInBreadcrumb: false,
              children: null,
              text: "Book列表",
              icon: "anticon anticon-solution",
              shortcutRoot: false,
              link: "/book",
              badge: 0,
              // acl: "contracts_query_contracts",
              shortcut: false,
            },
            {
              i18n: null,
              group: false,
              hideInBreadcrumb: false,
              children: null,
              text: "借阅人管理",
              icon: "anticon anticon-solution",
              shortcutRoot: false,
              link: "/reader",
              badge: 0,
              // acl: "contracts_query_contracts",
              shortcut: false,
            },
            {
              i18n: null,
              group: false,
              hideInBreadcrumb: false,
              children: null,
              text: "借阅",
              icon: "anticon anticon-solution",
              shortcutRoot: false,
              link: "/borrow",
              badge: 0,
              // acl: "contracts_query_contracts",
              shortcut: false,
            },

```



后端：
* Cli 生成器 提供多个模板选择
* 项目目录结构
* Geex的ORM库，是基于MongoDb驱动，自己写的，支持和EF一样的使用方式.
* 继承Entity：包含主键ID、创建时间、修改时间
* 文件支持：DB、OSS存储使用方式只需要引入IBlobObject实体：原来是单体项目分模块，走MeditR，之前是为了实现一个模块化的，跨模块调用所以引入了MeditR，但是MeditR会增加项目复杂度，
* 时间类型使用DateTimeOffset：这是C# 12目前比较推荐使用，比DateTime功能更加完善
* 使用 ResettableLazy 包裹实体：在业务中，有些字段是非常昂贵的消耗性能，我们可以显示的去标记成ResettableLazy，这样在使用的时候手动去value使用。ResettableLazy 允许您在需要时计算值，然后缓存该值以便将来重复使用，同时还可以通过调用 Reset() 方法来清除缓存并重新计算值
* EntityMapConfigs：与MongoDb的映射，比如自定义一个字段的序列化方式或者别名等，比如Book可能有多个作者，我们在实体中是字符串，但是我们在数据库中存储为JSON。对比EF
* BookGqlType：类似于Dto的一个映射配置.
* 迁移: 
* CI 脚本是放在Jenkens中使用的，目前CICD是混在一起的，后续会优化
* vs 包了一层Client项目，是为了使用vs的发布，做一些HTTPS的处理，这部分可以看vs的SPA介绍




前端：
```
* graphql文件: 用来生成客户端Http代理的契约文件
* RoutedComponent：路由组件通用基类(自定义生命周期)
    * 规范化参数值=>(resolve)=:>
    * 传入参数=>(params)=:>
    * 数据处理逻辑(fetchData)=:>
    * 前端渲染=>(context)
* RoutedListComponent基类封装：路由列表页的基类，封装list页面常用功能和继承路由基类
* RoutedEditComponent基类封装：路由表单的基类，封装表单操作和继承路由基类
    * EntityEditablePart：编辑页特有的用于表单的可编辑字段约束
    * EditDataContext
        * id?: string;
        * entity?: Partial<T>;
        * entityForm?: MyFormGroup<FormControlPropMap<T, TEditable>>;
        * originalValue?: { [key in TEditable]?: T[key] };
* 弹框await this.modal.create(BookViewPage, { id: item.id }).toPromise();
```


* ES http://localhost:5601/   
* APM
* 框架文档


加一个i18n的操作展示 智能提示



权限(加一个acl隐藏按钮\使用admin账户登录\因为superadmin无视权限控制)


{{I18N.AuditStatus.Submitted}}


# 后续计划
目前cli 生成的模板还只有全功能的，后续会增加模态框的一个编辑组件。
CI/CD的一个拆分和Azure的集成
Table表格的筛选支持集成到路由上

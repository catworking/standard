# Drupal 8 通用型解决方案

## 目录

- [数据建模](#数据建模)
- [管理后台框架](#管理后台框架)
- [客户端 API](#客户端-api)
- [全文搜索](#全文搜索)
- [使用Features模块协助基于配置的开发](#使用Features模块协助基于配置的开发)

## 数据建模
开发一个Web服务端应用时，一般都是先从数据建模开始。
根据项目需求，识别系统需要抽象的数据实体(entity)。

数据实体由`实体字段`（称为实体字段是为了与数据库的字段进行区分）构成。`实体字段`一般情况下可以直接映射为`数据库字段`，但是并不总是如此，
比如产品的价格，它包含两项数据，“货币类型”和“货币数量”，所以价格字段至少需要两个数据库字段来储存。

在Drupal中，从业务需求中分析得到的数据实体称为Entity。
Entity由字段组成，称作Field。
Field又由属性组成，称作Property。
最后的Property才直接映射到数据库表的字段进行储存。
需要注意的是，开发者是不需要直接管理数据库的，Drupal使用Doctrine的数据库抽象层，并且会自动帮你创建数据库表。

Drupal核心已经提供了大量的Field类型，你可以直接使用这些Field类型来 [定义你的Entity](https://www.drupal.org/docs/8/api/entity-api)。
如果核心提供的Field类型仍然无法满足你的项目需求，你可以通过[Feild API](#field-api)自定义Field类型。

### Field API
在使用`Field API`定义你的实体字段时，你需要明白的是，你不仅仅是在把实体字段的属性映射到数据库，
同时在定义你的字段`如何被显示`(通过定义对应的field formatter)，以及`如何被编辑`（通过定义对应的field widget）。

- [Creating a custom Field](https://www.drupal.org/docs/8/creating-custom-modules/creating-a-custom-field)
- [Create a custom field type](https://www.drupal.org/docs/8/creating-custom-modules/create-a-custom-field-type)
- [Create a custom field formatter](https://www.drupal.org/docs/8/creating-custom-modules/create-a-custom-field-formatter)
- [Create a custom field widget](https://www.drupal.org/docs/8/creating-custom-modules/create-a-custom-field-widget)

### Entity API
在使用`Entity API`定义你的实体，你需要明白的是，你不仅仅是在定义它包含哪些实体字段，
同时在定义你的实体`如何被显示`(字段顺序，以及各字段默认使用哪个field formatter显示)，
以及`如何被编辑`（字段顺序，以及各字段默认使用哪个field widget进行编辑）。

- [Entity API 如何创建数据实体](https://www.drupal.org/docs/8/api/entity-api)
- [Entity handlers 实体处理器](https://www.drupal.org/docs/8/api/entity-api/handlers)
  
  实体处理器非常重要，如上所述，你不仅仅是在定义实体包含哪些字段，还在定义实体是如何被处理的。
  处理器有多种类型，用于对实体执行不同类型的处理任务，如：储存处理器、事件处理器、仿问处理器等。
  系统提供了很多有用的处理器，大多数情况下，使用系统提供的处理器就已经可以满足你的需求，
  如何有特别的需要，你可以扩展这些处理器，加入你需要的功能。

- [Entity Storage, the Drupal 8 Way](https://www.drupalwatchdog.com/blog/2015/3/entity-storage-drupal-8-way) 
  
  解释Drupal8的实体与数据库表的对应关系。
  
  简单实体、可翻译实体、可版本化实体，这三种情况，Core SQL Storage分别是如何在数据库中储存他们的。

## 管理后台框架
使用Drupal核心的提供的后台界面(system模块)，来开发应用的Web管理后台。

system模块本身是一个完整的应用（具体情况请安装drupal core来体验），你在它的基础上编写模块来扩展功能。
当你在数据建模阶段添加了自定义的entity和field后，你很可能想实现界面来让用户对数据进行管理。

Drupal是非常灵活的，并且提供了一系列的概念，在这些概念之上实现了大量的基础设施，
使开发者只需要写很少的代码或者不需要写代码，就可以实现复杂的Web应用，包括管理后台。
在进入后台界面的开发工作之前，你有必要先熟悉
[如何创建Drupal模块](https://www.drupal.org/docs/8/creating-custom-modules)，
因为Drupal中的一切都是由模块提供的，核心有几十个模块，开发者同样也是通过编写模块来扩展Drupal，
并且，开发者编写的模块与核心模块的地位是相同的，没有任何区别。

下面是一些要素，在开发管理后台界面功能时非常有用：

- [链接](https://www.drupal.org/docs/8/api/menu-api) 
  
  链接是用户交互开始的地方，链接类型决定了链接在页面中出现的地方，
  链接还定义了url所关联的路由。
  
  - [菜单链接 menu link](https://www.drupal.org/docs/8/api/menu-api/providing-module-defined-menu-links) 
  
    如何创建管理后后的系统菜单项
  - [动作链接 action link](https://www.drupal.org/docs/8/api/menu-api/providing-module-defined-actions) 
    
    如何创建管理后台页面中的动作按钮，比如在“产品列表”页面的“添加产品”按钮
  - [任务链接 task link](https://www.drupal.org/docs/8/api/menu-api/providing-module-defined-local-tasks) 
    
    如何创建任务链接。在数据项的详情页面，有一些类似标签页的链接，
    如“查看”、“编辑”、“管理字段”、“管理表单显示”等，
    这些被称为对此数据项的处理任务。
- [路由](https://www.drupal.org/docs/8/api/routing-system)
  
  用户点击了页面中的链接后，请求会被调度到url所关联的路由。
  路由指明了url与php控制器类的映射关系。
  控制器会返回一些用于显示的数据，然后Drupal把这些数据渲染成HTML或其他的格式输出给用户。
  
  特别地，路由还可以直接与一个表单类进行关联，直接渲染一个表单页面。

- [实体表单 Entity Form](https://www.drupal.org/docs/8/api/entity-api/display-modes-view-modes-and-form-modes)
  
  表单有多种类型，实体表单Entity Form则是Entity处理器的一种，在定义Entity时，为其关联的表单处理器，
  以指明实体在不同情况下（default add edit delete）使用什么表单进行编辑。
  
- [内联表单 Inline Entity Form](https://www.drupal.org/project/inline_entity_form)
  
  内联表单是经常需要用到的东西，也叫内嵌表单，也就是“表单中的表单”，在创建关联数据时非常有用。
  `inline_entity_form模块`提供这样的功能。
  
- [使用View生成Entity数据表列表页面](https://www.drupal.org/docs/8/core/modules/views)
  
  一般来说管理后台中的数据列表功能是不需要开发的，完全可以直接使用 [Views模块](https://www.drupal.org/docs/8/core/modules/views) 来创建，
  只需要在后台点几下，就可以创建一个完美的数据列表页。
  
- [通过创建一个管理后台主题（Admin Theme）来自定义管理后台的外观](https://www.drupal.org/docs/8/theming)
  
  Drupal核心自带了一个叫 [`Seven`](https://www.drupal.org/docs/8/core/themes/seven-theme) 
  的管理后台主题，你可以继承它，来扩展或覆写这个主题的一部份。
  或者干脆仿照它来重写一个主题。

## 客户端 API
- [RESTful Web Services 模块](https://www.drupal.org/docs/8/api/restful-web-services-api) 由Drupal核心提供的模块，
  通过简单的yml配置，把entity曝露为标准的RESTful接口
- [restui 模块](https://www.drupal.org/docs/8/api/restui) 提供可视化界面来管理Entity的RESTful曝露
- [simple_oauth 模块](https://www.drupal.org/project/simple_oauth) 按
  [OAuth 2.0 Authorization Framework RFC](https://tools.ietf.org/html/rfc6749)工业规范实现的
  Oauth2.0服务端
- [oauth 模块](https://www.drupal.org/project/oauth) 按
  [Oauth1.0](https://tools.ietf.org/html/rfc5849)工业规范实现的
  Oauth1.0 服务端，除非项目特别要求，否则请用Oauth2.0
- [Views 模块的 `REST export` 功能](https://www.drupal.org/docs/8/core/modules/rest/get-on-views-generated-lists) 
  一般来说，REST不能解决所有问题，核心的REST模块提供了Views支持，用于解决复杂数据列表输出的问题。
- [openapi 模块](https://www.drupal.org/project/openapi) 自动生成RESTful接口的文档。
  [OpenAPI Specification](https://swagger.io/specification/) 是`swagger`提出的一个API文档格式规范，
  这个模块自动生成符合该规范的文档数据，并使用 [Swagger UI](https://swagger.io/swagger-ui/)
  把文档显示成可交互的文档格式。

## 全文搜索

- [search_api 模块](https://www.drupal.org/project/search_api) 全文搜索基础设施
- [search_api_db 模块](https://www.drupal.org/project/search_api_db) 基于数据库的全文搜索服务端
- [search_api_solr 模块](https://www.drupal.org/project/search_api_solr) 基于apache lucene的全文搜索服务端
- [facets 模块](https://www.drupal.org/project/facets) 提供分面搜索（
  [什么是分面搜索？](http://cdc.tencent.com/2009/07/30/%E5%88%86%E9%9D%A2%E6%90%9C%E7%B4%A2%EF%BC%88faceted-search%EF%BC%89/)）功能

## 使用Features模块协助基于配置的开发

参见文章 [在Drupal8中使用Drush+Features模块通过Git迁移配置](http://www.zhaobg.com/how-to-use-drush-features)

TODO: 把该文章的要点整理过来，以免外部文章连接失效

[Features模块](https://www.drupal.org/docs/8/modules/features)
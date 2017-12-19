# Drupal 8 通用型解决方案

- 数据建模
- 管理后台框架
- 客户端 API
- 全文搜索

## 数据建模
- Drupal Entity API
- Feild API

## 管理后台框架
使用Drupal核心的提供的后台界面(system模块)，来开发应用的Web管理后台。

system模块本身是一个完整的应用（具体情况请安装drupal core来体验），你在它的基础上编写模块来扩展功能。
当你在数据建模阶段添加了自定义的entity和field后，你很可能想实现界面来让用户对数据进行管理。

- 路由 路由是浏览器请求开始的地方，它指明了url与php控制器类的映射关系。
- 菜单链接 menu link 如何创建管理后后的系统菜单项
- 动作链接 action link 如何创建管理后台页面中的动作按钮，比如在“产品列表”页面的“添加产品”按钮
- 任务链接 task link 如何创建任务链接。在数据项的详情页面，有一些类似标签页的链接，
  如“查看”、“编辑”、“管理字段”、“管理表单显示”等，
  这些被称为对此数据项的处理任务。
- 表单 Entity Form
- 表单字段部件 Feild Widget
- 内联表单 Inline Entity Form

## 客户端 API
- [RESTful Web Services](https://www.drupal.org/docs/8/api/restful-web-services-api) 由Drupal核心提供的模块，
  通过简单的yml配置，把entity曝露为标准的RESTful接口
- [REST UI](https://www.drupal.org/docs/8/api/restui) 提供可视化界面来管理Entity的RESTful曝露
- [Oauth2.0](https://www.drupal.org/project/simple_oauth) 按
  [OAuth 2.0 Authorization Framework RFC](https://tools.ietf.org/html/rfc6749)工业规范实现的
  Oauth2.0服务端
- [Oauth 1.0](https://www.drupal.org/project/oauth) 按
  [Oauth1.0](https://tools.ietf.org/html/rfc5849)工业规范实现的
  Oauth1.0 服务端，除非项目特别要求，否则请用Oauth2.0
- [Views REST export](https://www.drupal.org/docs/8/core/modules/rest/get-on-views-generated-lists) 
  一般来说，REST不能解决所有问题，核心的REST模块提供了Views支持，用于解决复杂数据列表输出的问题。
- [OpenAPI](https://www.drupal.org/project/openapi) 自动生成RESTful接口的文档。
  [OpenAPI Specification](https://swagger.io/specification/) 是`swagger`提出的一个API文档格式规范，
  这个模块自动生成符合该规范的文档数据，并使用 [Swagger UI](https://swagger.io/swagger-ui/)
  把文档显示成可交互的文档格式。

## 全文搜索

- [search_api](https://www.drupal.org/project/search_api) 全文搜索基础设施
- [search_api_db](https://www.drupal.org/project/search_api_db) 基于数据库的全文搜索服务端
- [search_api_solr](https://www.drupal.org/project/search_api_solr) 基于apache lucene的全文搜索服务端
- [facets](https://www.drupal.org/project/facets) 提供分面搜索（
  [什么是分面搜索？](http://cdc.tencent.com/2009/07/30/%E5%88%86%E9%9D%A2%E6%90%9C%E7%B4%A2%EF%BC%88faceted-search%EF%BC%89/)）功能

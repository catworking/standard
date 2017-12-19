# Drupal 8 通用型解决方案

- 数据建模
- 管理后台框架
- 客户端 API
- 全文搜索

## 数据建模
- Drupal Entity API
- Feild API

## 管理后台框架
使用Drupal核心的提供的后台界面(system模块)，来开发应用的Web管理后台

- 路由
- 菜单 menu link
- 动作 menu action
- 任务 local task
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

## 全文搜索

- [search_api](https://www.drupal.org/project/search_api) 全文搜索基础设施
- [search_api_db](https://www.drupal.org/project/search_api_db) 基于数据库的全文搜索服务端
- [search_api_solr](https://www.drupal.org/project/search_api_solr) 基于apache lucene的全文搜索服务端
- [facets](https://www.drupal.org/project/facets) 提供分面搜索（
  [什么是分面搜索？](http://cdc.tencent.com/2009/07/30/%E5%88%86%E9%9D%A2%E6%90%9C%E7%B4%A2%EF%BC%88faceted-search%EF%BC%89/)）功能
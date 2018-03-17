# Drupal 8 通用型解决方案

## 目录

- [数据建模](#数据建模)
- [管理后台框架](#管理后台框架)
- [用户认证](#用户认证)
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


## 用户认证
Drupal自身带有完整的用户认证和访问控制系统。
核心本身已经支持使用`用户名`和`密码`通过Web表单登录，通过整合社区中的模块，几乎可以实现任何形式的用户认证。

- 社交帐号认证(OAuth2.0)

  通过 [Social Auth](https://www.drupal.org/project/social_auth) 模块，
  可以实现包括 `微信` 在内的20多种社交网络帐号登录。
  
  - [Social Auth WeChat](https://www.drupal.org/project/social_auth_wechat) 微信扫码登录
  - 微信公众号网页授权，暂无实现

- 双因素授权(Two-Factor Authentication)
  
  [One Time Password](https://www.drupal.org/project/one_time_password) 模块提供了双因素授权功能，
  在用户通过用户名密码登录后，需要使用其他认证器二次认证。

  - [One Time Password SMS](https://www.drupal.org/project/otp_sms) 手机短信进行二次认证

- 双步验证(Two-Step Verification)

- [Oauth2 Server](https://www.drupal.org/project/oauth2_server) 实现了一个Oauth2.0服务端，
  本质上是整合了著名的[bshaffer/oauth2-server-php](https://github.com/bshaffer/oauth2-server-php)。
  然而此模块与 [simple_oauth](https://www.drupal.org/project/simple_oauth) 模块有功能交叉，
  该模块使用了另一个Oauth2.0库 [thephpleague/oauth2-server](https://github.com/thephpleague/oauth2-server)

- [OpenID Connect](https://www.drupal.org/project/openid_connect) OpenID Connect是建立在Oauth2.0之上的一个身份层协议，
  是一种Oauth2.0客户端标准，简单点说就是在Oauth2.0认证过程完成后，拿access token去调用一个用户信息接口，获取用户身份。

> 如果仅仅是登录，openid_connect模块和 social_auth模块都可以做到，如果要使用Oauth2 Client访问更多的第三方资源，那就应该用openid_connect模块了

## 手机相关功能

- [SMS Framework](https://www.drupal.org/project/smsframework) 提供了一个用于发送短信的接口。
  
  开发者可以实现自己的短信网关。提供了把手机号码字段绑定到特定`Entity Type`的功能，比如`User`。
  开发者可以把短信发送给特定的entity，比如某个用户，也可以直接发给一个手机号码。
  
  此模块还提供了短信发送队列的功能，解决大量短信发送时的耗时问题，以及短信服务商的发送频率限制问题。

- 手机号码验证（支持全球所有国家）
  - [Mobile Number](https://www.drupal.org/project/mobile_number)  供对用户输入的号码发送短信进行验证的功能。
    
- 手机号码标识用户

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

## 性能提升

- [OSS对象储存](https://www.drupal.org/project/ossfs) 实现了一个文件系统选项，把用户上传的文件保存到阿里云OSS服务。

  [Drupal 8 使用阿里云OSS安装配置攻略](http://www.drupal.org.cn/ossfs-config) 此文章详细介绍了该模块的使用过程。

- [Redis缓存模块](https://www.drupal.org/project/redis) 把Redis作为Drupal的缓存系统，大幅提高性能

  [Redis在Drupal构建新媒体平台中的重要性及详细使用攻略](http://www.drupal.org.cn/redis/1) 此文章介绍了如何应用该模块

## 使用Features模块协助基于配置的开发

参见文章 [在Drupal8中使用Drush+Features模块通过Git迁移配置](http://www.zhaobg.com/how-to-use-drush-features)

TODO: 把该文章的要点整理过来，以免外部文章连接失效

[Features模块](https://www.drupal.org/docs/8/modules/features)

## 渲染和输出

- [Render API概述](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Render%21theme.api.php/group/theme_render/8.4.x)
- [D8开发者文档 Render API](https://www.drupal.org/docs/8/api/render-api/render-arrays)

### Render Array

- [Drupal Core 所有`元素`通用的渲染属性](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Render%21Element%21RenderElement.php/class/RenderElement/8.5.x)
- [Drupal Core 所有`表单元素`通用的渲染属性](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Render%21Element%21FormElement.php/class/FormElement/8.5.x)
- [`#Ajax`属性和`Ajax API`](https://api.drupal.org/api/drupal/core%21core.api.php/group/ajax/8.5.x)
- [Drupal7 中关于渲染属性的文档，可作为Drupal8的文档补充](https://api.drupal.org/api/drupal/developer%21topics%21forms_api_reference.html/7.x)

### Render Element

Render Array 预打包，形成Element Type。

分为 `常规元素` 和 `表单元素`，后者继承前者，用于构建表单的可输入元素。
前者仅用于显示数据，常常会使用 `theme` 模板来渲染输出。
后者可以包含用户的输入值，所以常用于处理表单渲染。

一个元素可以是通过组合其他已有元素的方式而定义出来的，我们可以称其为 `复合元素`。（官方没有这种术语）
一个复合元素，它可以是 `常规元素` 和 `表单元素`，当它是 `常规元素` 时，它是可以包含 `表单元素` 的，反之亦然。

- [Drupal Core 自带的所有元素](https://api.drupal.org/api/drupal/elements)
- [如何创建一个Element Type (D6/D7，D8有一定参考价值)](https://www.drupal.org/node/169815)

> `FormElement` 使用 `valueCallback()` 方法，把用户的输入 `$input`，设置为自身的值（在方法内 `return`）。

### Form API 与 Render Element

表单使用 `Render Element` 构建表单输出。

表单的提交，通过 [Button](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Render%21Element%21Button.php/class/Button/8.5.x) 
或 [Submit](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Render%21Element%21Submit.php/class/Submit/8.5.x) 元素触发。二者的区别在于，
前者只是提交表单到服务端，执行表单重构 `buildForm()`，验证表单提交数据 `validateForm()`，但不会执行表单对象的 `submitForm()`，
而后者则会执行 `submitForm()`。




### 字段化 `Content Entity` 的表单处理器

`Content Entity`的编辑表单，是在实体定义注解中通过 `handlers` 来指定表单处理器的。
`实体表单处理器` 是一种特殊的表单，它与字段的 `FieldWidget` 进行交互，
让各个字段自己管理各自的表单处理任务，包括 `构建` `数据验证` `数据保存`。

### FieldFormatter

### FieldWidget

> `FieldWidget` 有 `单值模式` 和 `多值模式`，默认为 `单值模式`。通过在注解定义时的 `multiple_values = TRUE` 选项来开启 `多值模式`。

> 在 `单值模式` 下，系统会自动处理字段的多值管理，包括创建一个值列表，新增/删除/修改字段值，处理排序。
在这个过程中，系统会多次调用 `单值模式` 的 `FieldWidget::formElement()` 来构建多值列表，
每一次的调用 `$delta` 参数会给出不同的下标。

> 在 `多值模式` 下，开发者要自己实现多值管理功能，系统只调用一次 `FieldWidget::formElement()`，
开发者需要在此方法内把 `$items[]` 的所有值传给 `FormElement` 的 `#default_value` 渲染属性。
并添加 `#element_validate` 渲染属性，指定一个 `callback`，把元素的值处理成一个数组，
多值的顺序问题开发者也需要在这个过程中自行处理，此数组的顺序就是多值的顺序。
还有一个特别的地方是，Drupal保存多值时，会把所有原有值都删除，然后重新创建新提交的数据。
参考代码 `Drupal\field_test\Plugin\Field\FieldWidget\TestFieldWidgetMultiple`。

> `FieldWidget` 通过 `formElement()` 方法定义表单元素，一般对应的 `FieldType` 有几个属性，
就要定义几个元素来供用户输入数据。通过把方法参数 `$items[$delta]->value` 设置为元素的 `#default_value` 渲染属性值，
达到把字段值传给 `FormElement` 插件来显示的目的。

> `FieldWidget` 通过 `WidgetBase::extractFormValues()` 方法，从 `FormElement` 提取值来保存到字段对像，
字段对像再把数据保存到数据库。

### FormBuilder

- [FormBuilder 如何实例化一个表单类，并处理表单提交](https://api.drupal.org/api/drupal/core%21core.api.php/group/form_api/8.5.x)

### Inline Entity Form 内联表单

#### 概述

简称 IEF，这是一个由 DrupalCommerce 团队开发的一个模块。DrupalCore 本身是没有内联表单这种概念的。
但我们在工作中发现 `内联表单` 是一个非常重要的需求。

#### 应用背景

在一个实体中包含另一个实体的引用时，DrupalCore的方案是使用 `EntityReference` 字段，
该字段类型一般使用 `AutoComplete` 字段控件来编辑引用。但是这个控件只能解决编辑引用关系的问题，
如果想直接在实体编辑表单中新建/编辑另一个引用的实体的字段内容，就办不到了。这就是 `IEF` 要解决的问题。

#### 实现原理

在实际开发中，我们发现 `HTML` 的 `Form` 是不能嵌套的。所以引用实体的编辑只能和缩主实体使用同一个 `Form`。
于是 IEF 模块定义了一个叫 `InlineEntityForm` 的 `RenderElement`，用于在实体表单中，显示 `EntityReference`
字段编辑界面，这个 `RenderElement` 调用被引用实体类型的表单生成器，去生成内联表单。

有了 `RenderElement` 还不够，要使字段能使用这个 Element，还需要有 FieldWidget。
所以，IEF模块还定义了两个 FieldWidget：

- inline_entity_form_simple 只能处理单值，多值时，由系统解决值列表和排序问题
- inline_entity_form_complex 能处理多值，它自己实现了类似系统的多值列表和排序功能，但是有所改进

如果这两个 Widget 都不能满足你的需求，那么你可以用 `InlineEntityForm` 元素开发自己的 Widget 。

#### InlineEntityForm 元素的表单提交







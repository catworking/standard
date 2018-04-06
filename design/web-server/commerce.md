# Drupal 8 电子商务解决方案

## 基本组件

我们基于 [Drupal Commerce 2.x](https://docs.drupalcommerce.org/)
来开发电子商务应用。

Drupal Commerce 2.x 是一个Drupal模块，其中包含了一系列子模块。
如Drupal Commerce官方文档所述：

> At its core, Commerce is a set of Drupal 8 modules,
which in turn depend on other best-of-breed modules and libraries.

打开 Commerce 2.x 的源码，在 `modules` 目录可以看到其包含的所有模块：
- 核心: Drupal Commerce 2.x




- 支付网关：
  - 支付宝
  - 微信
  - 银联
- 运费组件 commerce_shipping (包含固定费率运费插件)
- 库存管理 commerce_stock (仍未发布，但已取得良好进展)

## 酒店预定解决方案
## 景点门票解决方案
## 分销功能
## 拼团功能

## 支付

### Config entity
- `PaymentGateway` 相当于一个PaymentGateway plugin的实例。

### Content entity
- `PaymentMethod` 代表某用户的一个支付方法，是用户对于某个Gateway的身份标识（remote_id）的记录，
                同时还记录了身份标识的相关信息，这些相关信息视乎`PaymentMethodType`的定义，
                它通过 `bundle_plugin_type` 实现动态 bundle。
                (只用于 OnSite 类型的 Gateway，OffSite是跳转到Gateway方网站实行认证支付操作的)

- `Payment` 代表一次支付行为，一个订单可以有多次支付行为。一个支付行为可以记录退款金额，可以部分退款。
          它关联了一个 `PaymentGateway`，指明了使用什么 Gateway 进行支付。
          不同的 `PaymentGateway` 可能会有不同的相关信息，因此也通过 `bundle_plugin_type` 实现动态了 bundle。

### plugin
- `PaymentGateway(Type)` 用于定义支付网关的插件，定义后，通过 Config entity `PaymentGateway` 进行实例化配置。
  
  - manual 手动支付（直接创建一个Payment，手动收钱后，点击 `Reveive`，把Payment设置为到账状态）
  - OnSite 在站支付 （支持全程在站，不跳转第三方）
  - OffSite 离站支付 （跳转第三方支付后，再跳回本站）
- `PaymentMethodType` PaymentMethod 内容实体的动态bundle实现插件。
- `PaymentType` Payment 内容实体的动态bundle实现插件。

### PaymentGateway插件的3种类型

接口实现情况：

- ManualPaymentGatewayInterface
  - PaymentGatewayInterface
  - HasPaymentInstructionsInterface
  - SupportsVoidsInterface
  - SupportsRefundsInterface
- OnsitePaymentGatewayInterface
  - PaymentGatewayInterface
  - SupportsStoredPaymentMethodsInterface
- OffsitePaymentGatewayInterface
  - PaymentGatewayInterface
  - SupportsNotificationsInterface
  
这三种网关，都是假设客户端就是Drupal本身站点，
没有到考虑到 `独立客户端`（如手机APP，H5 SPA）通过REST接口来
实现支付的情况，（其实不单是支付，整个 Commerce都是这样）。

对于 `独立客户端` 的情况，应该是一种特殊的 `OffSite` 。需要提供一个 REST接口，来为一个draft Order(cart)创建 Payment。
接口需要返回用于客户端调起第三方支付客户端的数据。当支付完成时，SupportsNotificationsInterface接收到通知，
更改支付状态，并更新订单状态。所以，对于 `独立客户端` 的情况，@CommercePaymentGateway 中的 forms 是没用处的，
因为这些表单的作用是在Drupal网站上做第三方支付网关跳转处理。

> 对于 `独立客户端` 的情况，不单单PaymentProcess 是没有作用的，整个 Checkout过程都是没有作用的，全部要改为API接口处理。

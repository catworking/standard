# Drupal开发模式指南
（参考版本：Drupal 8.7）

Drupal是一个复杂的系统，它的缓存系统、主题系统使得开发时的调试任务变得困难。
本文意在指导开发者如何把Drupal设置为开发模式，从而使调试任务变得轻松。

可以使用如下设置：
- 让Drupal日志系统记录所有错误
- 关闭js/css文件合并
- 关闭渲染缓存 render cache
- 关闭迁移发现缓存 discovery_migration
- 关闭静态页面缓存 Internal Page Cache (缓存anonymous用户请求)
- 关闭动态页面缓存 Dynamic Page Cache （缓存authenticated用户请求）
- 开启模板调试信息 Twig debugging
- 开启HTTP头显示缓存元数据 Cacheability debugging
- 使用 xdebug 对PHP代码进行断点调试
- 使用 xdebug for twig 对twig模板代码进行调试
- 关闭浏览器缓存

以上的效果，主要通过以下配置文件来达到：
- settings.local.php
- services.yml

## `settings.local.php` 配置 
- 复制 example.settings.local.php 到sites/xxx目录，命名为 settings.local.php。
- 修改 sites/xxx/settings.php 文件，去此注释，使 settings.local.php 生效：
```php
# if (file_exists($app_root . '/' . $site_path . '/settings.local.php')) {
#   include $app_root . '/' . $site_path . '/settings.local.php';
# }
```
- 修改 settings.local.php 文件设置，可达到：
  - 让Drupal日志系统记录所有错误
  - 关闭js/css文件合并
  - 关闭渲染缓存 render cache
  - 关闭迁移发现缓存 discovery_migration
  - 关闭静态页面缓存 Internal Page Cache
  - 关闭动态页面缓存 Dynamic Page Cache
  
## `services.yml` 配置 

在 `settings.local.php` 和 `settings.php` 文件中可以看到以下内容

```php
$settings['container_yamls'][] = DRUPAL_ROOT . '/sites/development.services.yml';
```
```php
$settings['container_yamls'][] = $app_root . '/' . $site_path . '/services.yml';
```

说明可以有多个`services.yml` 文件，后者会覆盖前者的值。

可以配置的值参考`sites/default/default.services.yml`文件，通过此文件的配置，可以达到：
- 开启模板调试信息 Twig debugging
- 开启HTTP头显示缓存元数据 Cacheability debugging
- 使用 xdebug 对PHP代码进行断点调试
- 使用 xdebug for twig 对twig模板代码进行调试

## `drupal site:mode  dev/prod` 命令
这个命令通过修改以下 configuration来达到一定的配置效果：
- system.performance
- views.settings
- system.logging

同时还生成了一个`sites/default/services.yml`文件，重写了部分配置项。

如果运行`drupal site:mode  dev` 命令，会看到类似这样的输出：

```bash
bash-4.4# su - application -c "cd /app/web/sites && /usr/local/bin/drupal smo -vvv dev"
 Configuration name: system.performance
 ------------------------- ---------------- ---------------- 
  Configuration key         Original Value   Override Value  
 ------------------------- ---------------- ---------------- 
  cache.page.use_internal   false            false           
  css.preprocess            false            false           
  css.gzip                  false            false           
  js.preprocess             false            false           
  js.gzip                   false            false           
  response.gzip             false            false           
 ------------------------- ---------------- ---------------- 

 Configuration name: views.settings
 -------------------------------- ---------------- ---------------- 
  Configuration key                Original Value   Override Value  
 -------------------------------- ---------------- ---------------- 
  ui.show.sql_query.enabled        true             true            
  ui.show.performance_statistics   true             true            
 -------------------------------- ---------------- ---------------- 

 Configuration name: system.logging
 ------------------- ---------------- ---------------- 
  Configuration key   Original Value   Override Value  
 ------------------- ---------------- ---------------- 
  error_level         all              all             
 ------------------- ---------------- ---------------- 

                                                                                                                        
 Services files "/app/web/sites/default/services.yml" was overwritten                                                   
                                                                                                                        

 New services settings
 ------------------------------------------ ------------- ------- 
  Service                                    Parameter     Value  
 ------------------------------------------ ------------- ------- 
  http.response.debug_cacheability_headers                 true   
  twig.config                                auto_reload   true   
  twig.config                                cache         true   
  twig.config                                debug         true   
 ------------------------------------------ ------------- ------- 


 // cache:rebuild

 Rebuilding cache(s), wait a moment please.

                                                                                                                        
 [OK] Done clearing cache(s).                                                                                           
                                                                                                                       
```

## 特别注意

有些缓存项，不可消除的，包括但不限于：
- 插件发现缓存
- 路由缓存
- 模块发现缓存

### `Response caching` vs `Render caching`

`Response caching` 即页面缓存，是对http请求进行缓存，分两种：
- Internal Page Cache (缓存anonymous用户请求)
- Dynamic Page Cache （缓存authenticated用户请求）

`Render caching` 即渲染缓存，是对页面中的部分渲染内容块进行缓存，所以又叫`Fragment caching`。

参考文档：[Cache API](https://www.drupal.org/docs/8/api/cache-api/cache-api)

## 总结

由于 Drupal 系统的复杂性，开发模式的配置，也变得复杂。

没有单一的开关能做到所谓的"开发模式"，只有熟悉 Drupal系统的功能，才能根据自己的需要去改变特定的配置项。

本文只是给出了参考，有更多的补充时，再加进来。
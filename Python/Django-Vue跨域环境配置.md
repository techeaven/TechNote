---
title: Django+Vue跨域环境配置
translate_title: django+vue-cross-domain-environment-configuration
date: 2018-07-05 18:23:23
categories: F-Python
tags:
---
## 概述
在使用Django+Vue开发过程中，遇到了很多开发环境相关的问题，比如跨域，比如ajax请求参数等，本篇文章主要记录解决在开发过程中，遇到的一些问题。



## 跨域不带Cookie
在使用Vue脚手架开发的过程中，会使用Vue脚手架自带的Server进行项目调试，Vue自带的Server支持`hot reloading`，这个特性是非常好用的。但是在开发过程中，因为要与后端交互，所以在请求后端接口的时候，会遇到跨域问题，这个问题在一些职责划分清楚的团队并不存在，因为前端开发人员会才用Mock数据的方式。

#### Webpack配置
采用Webpack dev server的`proxyTable`即可实现代理，将请求转发到后端的开发服务器上。配置方法如下:

```
    proxyTable: {
      '/api': {
        target: 'http://127.0.0.1:8190/',
        changeOrigin: true
      },
      '/manager': {
        target: 'http://127.0.0.1:8000/',
        changeOrigin: true,
      }
    }
```
将需要请求的真实地址在proxyTable里配置即可。
当然，proxyTable还有需要更高级的用法，这里就先不去踩那些不必要的坑。

#### Django CORS头配置
Django配置跨域，可以自己实现，也可以使用一个比较好用的库`django-cors-headers`。

在Django的配置文件里添加如下配置:

(1) 在`INSTALLED_APPS`配置项里配置如下:

```
INSTALLED_APPS = (
    ...
    'corsheaders',
    ...
)
```
(2) 在`MIDDLEWARE_CLASSES`配置项里配置如下:

```
MIDDLEWARE = [  # Or MIDDLEWARE_CLASSES on Django < 1.10
    ...
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
    ...
]
```

`CorsMiddleware`的优先级要尽可能的高，如在`CorsMiddleware`前边。

（3）增加`CORS_ORIGIN_ALLOW_ALL`配置项

在配置文件里新增`CORS_ORIGIN_ALLOW_ALL`配置项，并设置为True

```
CORS_ORIGIN_ALLOW_ALL = True
```

## 跨域带Cookie
按照上面的配置对`webpack`和`Django`进行配置后，可以实现在Vue中进行跨域Ajax请求，但是请求中并不带Cookie，如果需要带Cookie，则需要进行如下配置。

#### Webpack配置
Webpack配置方法同不带Cookie的方法相同。

#### Vue配置
在Vue里需要对`axios`进行全局配置，在`main.js`里增加如下配置:

```
axios.defaults.withCredentials = true
```

#### Django配置
如果需要在请求中带上Cookie，Django中的跨域返回头中就不能允许所有主机，需要指定单独主机，配置如下:

```
CORS_ALLOW_CREDENTIALS = True

CORS_ORIGIN_WHITELIST = (
    'localhost:8088',
    'localhost:8000',
    '127.0.0.1:8088',
    '127.0.0.1:8000'
)

CORS_ALLOW_HEADERS = (
    'x-csrftoken',
)
```
其中`CORS_ALLOW_HEADERS`配置项允许在ajax请求中定义允许自定义的头字段。


## 总结
项目完成了一段时间后，才写的这篇文章，有问题再改(⊙o⊙)…。

<br />



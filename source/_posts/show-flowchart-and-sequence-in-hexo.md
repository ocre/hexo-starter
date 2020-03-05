---
title: 修复Hexo中时序图插件hexo-filter-sequence不显示的问题
date: 2020-02-20 13:49:51
tags: 
- Hexo
---

## 问题描述
写Blog时，想展示时序图和流程图，网友给推荐了流程图插件[hexo-filter-flowchart](https://github.com/bubkoo/hexo-filter-flowchart)和时序图插件[hexo-filter-sequence](https://github.com/bubkoo/hexo-filter-sequence)。装上两个插件之后，根据github上的示例测试了一下，发现在Hexo中时序图无法显示。

## 问题处理
根据[这篇博文](https://www.dazhuanlan.com/2019/12/24/5e0209fc19074/)的指示，顺利解决了这个问题。修改过程记录如下：
1. 修改`node_modules\hexo-filter-sequence\index.js`文件，`flowchart`改为`sequence`，另外去掉`snap.svg.js`添加`rahpael.js`，具体改动部分如下：
```
hexo.config.sequence = assign({
  webfont: 'https://cdnjs.cloudflare.com/ajax/libs/webfont/1.6.27/webfontloader.js',
  // snap: 'https://cdnjs.cloudflare.com/ajax/libs/snap.svg/0.4.1/snap.svg-min.js',
  raphael:    'https://cdn.bootcss.com/raphael/2.3.0/raphael.min.js',
  underscore: 'https://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.8.3/underscore-min.js',
  sequence: 'https://cdnjs.cloudflare.com/ajax/libs/js-sequence-diagrams/1.0.6/sequence-diagram-min.js',
  css: '',
  options: {
    theme: 'simple'
  }
}, hexo.config.sequence);
```
2. 修改`node_modules\hexo-filter-sequence\lib\renderer.js`文件第25行左右，`flowchart`改为`sequence`，`config.snap`改为`config.rahpael`，改动后的内容如下：
```
if (sequences.length) {
      var config = this.config.sequence;
      // resources
      data.content += '<script src="' + config.webfont + '"></script>';
      // data.content += '<script src="' + config.snap + '"></script>';
      data.content += '<script src="' + config.raphael + '"></script>';
      data.content += '<script src="' + config.underscore + '"></script>';
      data.content += '<script src="' + config.sequence + '"></script>';
```
3. hexo的`_config.yml`配置文件中启用国内CDN（默认的cdnjs.cloudflare.com国内可能无法访问），修改如下：
```
# sequence config
sequence:
  webfont:    'https://cdn.bootcss.com/webfont/1.6.28/webfontloader.js'
  raphael:    'https://cdn.bootcss.com/raphael/2.3.0/raphael.min.js'
  # snap:       'https://cdn.bootcss.com/snap.svg/0.5.1/snap.svg-min.js'
  underscore: 'https://cdn.bootcss.com/underscore.js/1.9.1/underscore-min.js'
  sequence:   'https://cdn.bootcss.com/js-sequence-diagrams/1.0.6/sequence-diagram-min.js'
  css: # optional, the url for css, such as hand drawn theme 
  options: 
    theme: 
    css_class: 
```
```
# flowchart config
flowchart:
  raphael:   'https://cdn.bootcss.com/raphael/2.3.0/raphael.min.js'
  flowchart: 'https://cdn.bootcss.com/flowchart/1.12.2/flowchart.min.js'
  options: # options used for `drawSVG`

```

修改完成后，依次执行`hexo clean`,`hexo g`,`hexo d`重新部署，问题解决。

附测试代码：
```
Client->Server: request
Note right of Server: WSGI
Server->Application: app_callable(environ, start_response)
Application->Server: start_response(status, response_headers, exc_info=None)
Application->Server: return iterator
Server->Client: response
```

展示效果如下：
```sequence
Client->Server: request
Note right of Server: WSGI
Server->Application: app_callable(environ, start_response)
Application->Server: start_response(status, response_headers, exc_info=None)
Application->Server: return iterator
Server->Client: response
```


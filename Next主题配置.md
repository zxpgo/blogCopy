---
title: Next主题配置
date: 2018-10-27 16:28:50
tags: Hexo
---

前面一直使用Yelle主题，最近感觉太花哨了，所以修改成简洁的Next主题。<!--more-->

## 数学公式

必须在每个文章开头添加：

	mathjax: true

这样，才能正确显示数学公式。

## 第三方评论

### 来必力

来必力： [https://www.livere.com](https://www.livere.com)

在主题文件中添加如下命令：

	livere_uid: kjfkdjffklajdf

在安装之后，发现首页也有评论栏，严重影响美观。去掉方案为，修改`layout\_third-party\comments`中的`livers.siwg`文件：

	{% if not (theme.duoshuo and theme.duoshuo.shortname) and not theme.duoshuo_shortname and not theme.disqus_shortname and not theme.hypercomments_id and not theme.gentie_productKey %}
	  {% if theme.livere_uid %}
	  <div id="lv-container" data-uid="MTAyMC8zODgxNC8xNTM0Mg==">
	    <script type="text/javascript">
	      (function(d, s) {
	        var j, e = d.getElementsByTagName(s)[0];
	        if (typeof LivereTower === 'function') { return; }
	        j = d.createElement(s);
	        j.src = 'https://cdn-city.livere.com/js/embed.dist.js';
	        j.async = true;
	        e.parentNode.insertBefore(j, e);
	      })(document, 'script');
	    </script>
		</div>
	  {% endif %}
	{% endif %}


uid来自来必力管理后台：

![](https://i.imgur.com/Qma2Jrn.png)


更多评论第三方：

[https://blog.csdn.net/xiangzhihong8/article/details/77703791](https://blog.csdn.net/xiangzhihong8/article/details/77703791)


### 搜索

本地搜索：

	local_search:
	  enable: true
	  # if auto, trigger search by changing input
	  # if manual, trigger search by pressing enter key or search button
	  trigger: auto
	  # show top n results per article, show all results by setting to -1
	  top_n_per_article: 1

将enable设置为true即可。

## 网站访问量

### 不蒜子

官网：[http://ibruce.info/2015/04/04/busuanzi/](http://ibruce.info/2015/04/04/busuanzi/)

由于域名更新，以前用的无法再使用，需要更新域名。根据官网首页提示即可完成。

在`layout\_partials`目录下，footer.swig文件顶部添加如下代码：

	<div>
	<script async src="https//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
	<i class="fa fa-user-md"></i><span id="busuanzi_container_site_pv" style='display:none'>
	    本站总访问量 <span id="busuanzi_value_site_pv"></span> 
	    <span class="post-meta-divider">|</span>
	</span>
	<span id="busuanzi_container_site_uv" style='display:none'>
	    访问人数 <span id="busuanzi_value_site_uv"></span>
	</span>
	</div>

## 文章浏览量

### Leancloud

官网：[https://leancloud.cn/](https://leancloud.cn/)

网站注册和密钥获取见：[https://www.jianshu.com/p/702a7aec4d00](https://www.jianshu.com/p/702a7aec4d00)

在主题配置文件`_config.yml`中修改如下面命令：

	leancloud_visitors:
	  enable: true
	  app_id: 2AyV3DKioBSdodryrFdRodhzjB-gzGzodHsz 
	  app_key: XynedcHydJCVCdrTfbD4dyYnodo


在`layout\_scripts`目录下，创建文件`lean-analytics.swig`文件，代码如下：

	<!-- custom analytics part create by xiamo -->
	<script src="https://cdn1.lncld.net/static/js/av-core-mini-0.6.1.js"></script>
	<script>AV.initialize("{{theme.leancloud_visitors.app_id}}", "{{theme.leancloud_visitors.app_key}}");</script>
	<script>
	function showTime(Counter) {
		var query = new AV.Query(Counter);
		$(".leancloud_visitors").each(function() {
			var url = $(this).attr("id").trim();
			query.equalTo("url", url);
			query.find({
				success: function(results) {
					if (results.length == 0) {
						var content = $(document.getElementById(url)).text() + ' 0';
						$(document.getElementById(url)).text(content);
						return;
					}
					for (var i = 0; i < results.length; i++) {
						var object = results[i];
						var content = $(document.getElementById(url)).text() + ' ' + object.get('time');
						$(document.getElementById(url)).text(content);
					}
				}
			});
	
		});
	}
	</script>
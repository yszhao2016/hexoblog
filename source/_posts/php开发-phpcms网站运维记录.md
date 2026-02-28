---
title: php开发-phpcms网站迁移运维记录
tags: PHP开发
abbrlink: 21d33242
date: 2026-02-27 11:44:00
---

# 一、修改数据库
    
    替换相关表中数据域名


# 二、项目不支持https解决
    
##  2.1 填写网址验证是否http 

     修改 phpcms/templates/default/link/register.html

    111行左右代码修改为

```javascript
$("#url").formValidator({onshow:"请输入网站网址",onfocus:"请输入网站网址"}).inputValidator({min:1,onerror:"请输入网站网址1"}).regexValidator({regexp:"^http?:\/\/[A-Za-z0-9]+\.[A-Za-z0-9]+[\/=\?%\-&]*([^<>])*$",onerror:"格式应该为http://www.phpcms.cn/"})
```
## 2.2 后台列表页分页写死http,导致404

   phpcms/libs/functions/global.func.php

```php
   function pageurl($urlrule, $page, $array = array()) {
       if(strpos($urlrule, '~')) {
           $urlrules = explode('~', $urlrule);
           $urlrule = $page < 2 ? $urlrules[0] : $urlrules[1];
       }
       $findme = array('{$page}');
       $replaceme = array($page);
       if (is_array($array)) foreach ($array as $k=>$v) {
           $findme[] = '{$'.$k.'}';
           $replaceme[] = $v;
       }
       $url = str_replace($findme, $replaceme, $urlrule);
   // 	$url = str_replace(array('http://','//','~'), array('~','/','http://'), $url);
       return $url;
   }
```

## 2.3 点击稿件标题，跳转的是无效链接

   phpcms/libs/classes/attachment.class.php

```php
	function fillurl($surl, $absurl, $basehref = '') {
	    //省略部分代码
	    } else {
			$preurl = strtolower(substr($surl,0,6));
			if(strlen($surl)<7)
			$okurl = 'http://'.$BaseUrlPath.'/'.$surl;
			// 这边加个判断，如果链接以https开头，则不处理
			elseif($preurl=="https:"||$preurl=="http:/"||$preurl=='ftp://' ||$preurl=='mms://' || $preurl=="rtsp://" || $preurl=='thunde' || $preurl=='emule:'|| $preurl=='ed2k:/')
			$okurl = $surl;
			else
			$okurl = 'http://'.$BaseUrlPath.'/'.$surl;
		}
		$preurl = strtolower(substr($okurl,0,6));
		if($preurl=='ftp://' || $preurl=='mms://' || $preurl=='rtsp://' || $preurl=='thunde' || $preurl=='emule:'|| $preurl=='ed2k:/') {
			return $okurl;
		} else {
			$okurl = preg_replace('/^(http:\/\/)/i','',$okurl);
			$okurl = preg_replace('/\/{1,}/i','/',$okurl);
			return 'http://'.$okurl;
		}
	}
```

# 三、上传图片报错
    
   修改 /phpcms/modules/attachment/templates/swfupload.tpl.php
   77行左右处理
   
```javascript
 <script type="text/javascript">
 //if ($.browser.mozilla) {
 //      window.onload=function(){
 //        if (location.href.indexOf("&rand=")<0) {
 //                      location.href=location.href+"&rand="+Math.random();
 //              }
 //      }
 //}
 if (typeof InstallTrigger !== 'undefined') {  // Firefox检测
           window.onload=function(){
             if (location.href.indexOf("&rand=")<0) {
                           location.href=location.href+"&rand="+Math.random();
                   }
         }
 }
```

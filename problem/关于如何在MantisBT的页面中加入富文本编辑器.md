# 关于如何在MantisBT的页面中加入富文本编辑器

具体是转载这篇文章：[Mantis集成富文本插件](https://blog.csdn.net/weixin_42719183/article/details/125908692)

## 1. 下载[ueditor](https://so.csdn.net/so/search?q=ueditor&spm=1001.2101.3001.7020)

下载地址：http://ueditor.baidu.com/website/download.html

## 2. 上传

在/var/www/html/目录下创建ueditor目录，将代码拷贝到该目标下并赋权

```bash
chmod a+rwx -R /var/www/html/ueditor/
```

## 3. 修改相关页面

#### 3.1 修改“提交问题”页面

找到bug_report_page.php文件，拉到最后，在"layout_page_end();"后面增加：

``` php
?>
<!-- 配置文件 -->
<script type="text/javascript" src="/ueditor/ueditor.config.js"></script>
<!-- 编辑器源码文件 -->
<script type="text/javascript" src="/ueditor/ueditor.all.js"></script>
<!-- 实例化编辑器 -->
<script type="text/javascript">
var ue = UE.getEditor('description');
document.getElementById('description').className='edui-default';

</script>
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/d46709a628d34b46954c5605363edf34.png)

上传后运行代码报权限不足。到core目录下找到http_api.php文件，打开后找到“http_csp_add( ‘script-src’, “‘self’” );”，在它后面增加两行：

```php
http_csp_add( 'script-src', "'unsafe-inline'" );
http_csp_add( 'script-src', "'unsafe-eval'" );
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/0f1b054cd8d1425baff7639c0425eb71.png)

上传后ueditor已经能显示并使用，但是太宽了，我们再来改下样式。找到ueditor目录下ueditor.config.js文件，打开后找到92行“,initialFrameWidth:1000”，把高度和宽度的注释去掉并上传，如图所示：

是否启用元素路径，默认是显示，可以关闭：true->false;

![在这里插入图片描述](https://img-blog.csdnimg.cn/37b1ec40d0ca48039d9fdd459e001175.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/9a19e14341f2419faac0ac6bb74af01f.png)

修改后效果

![在这里插入图片描述](https://img-blog.csdnimg.cn/aaeab965f4ad40ca82afaedc180edb57.png#pic_center)
#Storage实例参考：Gallery
Storage最明显的用途就是保存用户上传的文件，而用户上传的文件以图片居多，所以这里以一个图片床Gallery的实例来说明Storage用法。

下面来几张截图。
第一张：首页，也是图片浏览界面。
![Gallery浏览页面](/images/storage_gallery_show.jpg " Gallery浏览页面")

第二张：上传页面，选择从本地上传
![Gallery本地上传页面](/images/storage_gallery_upload.jpg " Gallery本地上传页面")

第三张：也可以从网络图片下载。
![Gallery网络图片上传页面](/images/storage_gallery_upload2.jpg " Gallery网络图片上传页面")

第四张：图片管理页面
![Gallery管理页面](/images/storage_gallery_manage.jpg " Gallery管理页面")

涉及的用法有：  
文件上传upload  
文件写入write  
文件列表getListByPath、getList  
删除文件delete  
##文件上传
Storage文件上传有两种方式：upload和write。前者与传统的文件上传一样，用表单来提交；后者先获得文件的内容，然后write到文件里。

这里设计了两种图片上传方式：用表单从本地上传；下载网络图片内容，然后写入文件内。
###从本地上传
从本地上传时，表单需要添加 enctype="multipart/form-data" ，文件上传后变量放在$_FILES里。


	<form enctype="multipart/form-data" id="target" role="form" action="doupload.php" method="post">
	<ul id="myTab" class="nav nav-tabs">
		<li id="webpic" class=""><a href="#fromurl" data-toggle="tab">网络图片</a></li>
		<li id="localpic" class="active"><a href="#fromlocal" data-toggle="tab">本地图片</a></li>
	</ul>

	<div id="myTabContent" class="tab-content">
		<div class="tab-pane fade" id="fromurl">
		  	<div class="form-group">
	    		<label for="inputurl">粘贴图片网址</label>
	    		<input type="text" class="form-control" name="inputurl">
	  		</div>
		</div>

		<div class="tab-pane fade active in" id="fromlocal">
		  	<div class="form-group">
	    		<label for="inputfile">从本地上传</label>
	    		<input type="file" name="inputfile">
	    		<p class="help-block">请选择gif、jpg、jpeg、bmp等图片文件</p>
	  		</div>
		</div>
	</div>
 	    <button type="submit" class="btn btn-default">上传</button>
    </form>

提交后用upload( $domain , $destFileName  , $_FILES['inputfile']['tmp_name'] )保存。
下面是upload方法详解。

    upload (line 291)
    将文件上传入存储
    
    注意：文件名左侧所有的'/'都会被过滤掉。
    
    	return: 写入成功时返回该文件的下载地址，否则返回false
    	author: Elmer Zhang
    	access: public
    string upload (string $domain, string $destFileName, string $srcFileName, [array $attr = array()], [bool $compress = false])
    	string $domain: 存储域,在在线管理平台.storage页面可进行管理
    	string $destFileName: 目标文件名
    	string $srcFileName: 源文件名
    	array $attr: 文件属性，可设置的属性请参考 SaeStorage::setFileAttr() 方法
    	bool $compress: 是否gzip压缩。如果设为true，则文件会经过gzip压缩后再存入Storage，常与$attr=array('encoding'=>'gzip')联合使用
###处理网络图片
提交网络图片url,用file\_get_contents获得图片数据，用write写入Storage。

    <?php
	    $storage = new SaeStorage();
	    if(isset($_POST['inputurl']) && !empty($_POST['inputurl'])){//处理网络图片
	    	$content = file_get_contents($_POST['inputurl']);
	    	$storage->write('gallery',md5($_POST['inputurl'].time()) , $content);
	    }else if(isset($_FILES['inputfile'])){//处理本地图片
	    	$storage->upload( 'gallery' , md5($_FILES['inputfile']['name'].time())  , $_FILES['inputfile']['tmp_name'] );
	    }
	    header("Location:show.php")
    ?>
以下是write方法详解。

    write (line 251)
    将数据写入存储
    
    注意：文件名左侧所有的'/'都会被过滤掉。
    
    	return: 写入成功时返回该文件的下载地址，否则返回false
    	author: Elmer Zhang
    	access: public
    string write (string $domain, string $destFileName, string $content, [int $size = -1], [array $attr = array()], [bool $compress = false])
    	string $domain: 存储域,在在线管理平台.storage页面可进行管理
    	string $destFileName: 文件名
    	string $content: 文件内容,支持二进制数据
    	int $size: 写入长度,默认为不限制
    	array $attr: 文件属性，可设置的属性请参考 SaeStorage::setFileAttr() 方法
    	bool $compress: 是否gzip压缩。如果设为true，则文件会经过gzip压缩后再存入Storage，常与$attr=array('encoding'=>'gzip')联合使用

##文件列表
Storage有getListByPath和getList两个方法。顾名思义，前者获取指定Domain、指定目录下的文件列表；后者获取指定domain下的文件名列表。

在Gallery这个例子中，要获得全部的文件列表，以上两个方法都用。不同的是返回的列表信息不同。

getListByPath获得的列表信息为

    array(4) {
      ["dirNum"]=>
      int(0)
      ["fileNum"]=>
      int(4)
      ["dirs"]=>
      array(0) {
      }
      ["files"]=>
      array(1) {
	    [0]=>
	    array(4) {
	      ["Name"]=>
	      string(32) "01705c29afca1c4e4d993e4bfbbb7126"
	      ["fullName"]=>
	      string(32) "01705c29afca1c4e4d993e4bfbbb7126"
	      ["length"]=>
	      int(165572)
	      ["uploadTime"]=>
	      int(1385828948)
	    }
      }
    }

getList获得的列表信息为

    array(1) {
      [0]=>
      string(33) "\01705c29afca1c4e4d993e4bfbbb7126"
    }

##文件删除
delete这个用法很简单，只需传入domain和filename即可。

    <?php
    $filename = isset($_GET['name'])?$_GET['name']:'';
    $storage = new SaeStorage();
    $storage->delete ('gallery', $filename);
    header("Location:manage.php");
    ?> 

##结论
得益于Storage提供的便利的api和Bootstrap组件，我们只用很少的代码就实行了一个图床应用，不但有美女看，而且学了Storage基本用法。
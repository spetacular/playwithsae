#Storage技术背景
SAE Storage是一个分布式文件存储系统，它承载了SAE的持久化存储服务。它为每个用户提供最多10个domain，每个domain至少10G容量，不够可以向SAE申请增加。目前SAE共56万个应用，按这个配额，Storage至少要支持5600TB的储存。这个容量虽然比微云、百度网盘等网盘存储要小，但因为业务不同，也是个很大的技术挑战。为什么呢？原因有以下几点：

1. 网盘类存储直链较少，而Storage的文件都是直链，通过浏览器可以直接访问。这部分流量对Storage的设计要求很高，除了容量，还要有响应速度、流量均衡等。
2. 网盘类存储，大家分享较多的是音乐电影软件，相同文件多，可采用文件签名的方式去重，因此网盘存储空间虽大，但实际物理储存只存一份就够。但Storage是程序员的专属网盘，里面的文件多是程序生成的，多数是独一无二的，每个都要存，因此Storage的负担要大，而且数据备份的代价也大。
3. 支持文件属性。Storage的文件有各种属性：浏览器缓存超时、Content-Encoding等Content-Type。

设计这样的一个Storage系统，实在是技术活。我这里没有SAE的官方说明，因此只能从文档中寻找一些SAE技术背景的端倪。

##MongoGFS
关于Storage的技术可以在SAE早期的一个文档中（
[http://stdlib.sinaapp.com/?f=sae_storage.class.php](http://stdlib.sinaapp.com/?f=sae_storage.class.php "http://stdlib.sinaapp.com/?f=sae_storage.class.php")）看到些端倪。下面是部分代码：

    class sae_storage {

	    public static $useSSL = false;
	
	    private $__errno = 0;
	    private static $__saeStorHandle;
	    private static $__saeStorDomainHandle;
	    private static $__saeAdminDomainHandle;
	    private static $__accessKey; // Access key
	    private static $__secretKey; // Secret key
	    private static $__domAttr = array(
	        'permission' => 48,
	        'distribution' => array("bj"),
	    );
	    private $__fileAttr = array(
	                'exprie' => 3600,
	    );
	
	    private static $__storEngine = "MongoGFS";
    	………………
可以看到，SAE采用了"MongoGFS"这个存储引擎。
###什么是MongoGFS
MongoGFS是MongoDB GridFS的简称。其中Mongo是著名的文档型数据库Mongodb（[http://www.mongodb.org](http://www.mongodb.org "http://www.mongodb.org")），GridFS是mongodb实现的用于存储大文件的协议。

先说Mongodb。这里引用百度百科的条目（[http://baike.baidu.com/subview/3385614/9338179.htm](http://baike.baidu.com/subview/3385614/9338179.htm "http://baike.baidu.com/subview/3385614/9338179.htm")）：

> MongoDB是一个基于分布式文件存储的数据库。由C++语言编写。旨在为WEB应用提供可扩展的高性能数据存储解决方案。

> MongoDB是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。他支持的数据结构非常松散，是类似json的bson格式，因此可以存储比较复杂的数据类型。Mongo最大的特点是他支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引。

再说GridFS。

由于Mongo的一条文档（对应着关系型数据库的记录或行）最大为16MB，为突破此限制，引入了GridFS来存储和检索大文件。

GridFS不是把文件存储为一个单独文档，而是将文件分割为多个部分（术语叫块），然后把每一块当作不同的文档存储起来。GridFS中每一块的大小默认为256K。GridFS使用两个集合来存储文件。一个集合存储文件块，另一个存储文件信息。

GridFS不但能存储超过16MB大小的文件外，而且你不用加载整个文件到内存就可以访问文件。这点对网络文件很重要。

###Storage特性与MongoGFS
Storage得益于MongoGFS的优点，其一些特性也容易解释了。
1. MongoGFS容易扩展，所以Storage具有分布式的特点
2. 因为MongoGFS存储了文件属性，所以Storage易于设置和获取文件属性
3. Storage会将文件分块，所以不适合存储代码类文件，比如页面内调用的JS、CSS等。使用Storage服务来保存JS、CSS或者日志，会严重影响页面响应速度。

##结语
本节探讨了SAE的Storage可能采用的技术，解释了为什么SAE不建议用Storage存储静态资源JS、CSS的原因。


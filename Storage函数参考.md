#Storage函数参考
目前Storage提供以下的函数支持：

      void __construct ([string $_accessKey = ''], [string $_secretKey = ''])
		构造函数

      bool delete (string $domain, string $filename)
		删除文件

      string errmsg ()
		返回运行过程中的错误信息

      int errno ()
		返回运行过程中的错误代码
 
      bool fileExists (string $domain, string $filename)
    	检查文件是否存在
     
      array getAttr (string $domain, string $filename, [array $attrKey = array()])
    	获取文件属性
     
	  array getListByPath (string $domain, [string $path = NULL], [int $limit = 100], [int $offset = 0], [int $fold = true])
		获取指定Domain、指定目录下的文件列表

      array getList (string $domain, [string $prefix = '*'], [int $limit = 10], [int $skip = 0])
    	获取指定domain下的文件名列表

	  array getFilesNum (string $domain, [string $path = NULL])
		获取指定domain下的文件数量
     
      string getUrl (string $domain, string $filename)
    	取得访问存储文件的url
     
      mixxed read (string $domain, string $filename)
    	获取文件的内容
     
      int getDomainCapacity (string $domain)
		获取domain所占存储的大小
     
      bool setDomainAttr (string $domain, [array $attr = array()])
    	设置Domain属性
     
      bool setFileAttr (string $domain, string $filename, [array $attr = array()])
    	设置文件属性
     
      string upload (string $domain, string $destFile, string $srcFile, [array $attr = array()],
      [bool $compress = false])
    	将文件上传入存储
     
      string write (string $domain, string $destFile, string $content, [int $size = -1], [array
      $attr = array()], [bool $compress = false])
    	将数据写入存储
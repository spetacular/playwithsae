#SAE云存储类服务综述
云存储是与云计算密切相关的一个重要研究方向。与云计算一样，优秀的云存储平台需要具备以下几个条件：


- 	安全。这是云存储的首要要求。数据不能被授权之外的人或机器窃取，并且保证数据完整。


- 	透明化。当用户。通过云存储平台读取或存放数据时，只需通过平台提供的接口去读写，无需关心数据存储在哪块物理磁盘，也无需担心物理磁盘是否已满等。


- 	按需分配。用户只为自己使用的服务付费。举例来说，某用户存储数据用了20M空间，那他只用支付20M空间的钱，肯定不会花费一个磁盘的钱。


- 	动态服务。动态意味着可扩展。例如，一个刚上线的产品，前期访问人数较少，这时所需的云计算和云存储资源也较少；某一天，运营发起了一个给力的营销活动，访问人数暴增。这时平台需要有可扩展性，来应对突如其来的流量。
	
作为优秀的应用引擎，SAE提供了多种云存储类服务，包括MySQL、Storage、Memcache、KVDB、Counter和Rank，对企业用户还提供CDN服务。

下图是SAE云存储类服务的概括图。从是否为关系型数据库来分类，云存储类服务可分为关系型数据库和非关系型数据库。从严格意义来说，Memcache、Counter和Rank不算是数据库。这里的分类着重是否为SQL或NoSQL。
![ SAE云存储类服务概括图](/images/sae_storage_service.png " SAE云存储类服务概括图")

SAE上的关系型数据库是MySQL。SAE上的MySQL服务和普通MySQL服务几乎一样，所以你可以按照常规的MySQL使用方法来使用。

非关系型数据库即近年来得到广泛关注和发展的NoSQL。NoSQL是Not Only SQL的简写，意为“不仅仅是SQL”。它不是单纯地反对关系型数据库，而是强调根据应用所需业务的不同，灵活地采用键值、文档、图形关系等数据库的优点，来达到高并发（High performance）、大存储(Huge Storage)和高可扩展性和高可用性（High Scalability && High Availability）的目的。下面对SAE的几个非关系型数据库做一个简介，详细分析见后续各章节。

Storage属于文档型数据库，从SAE早期文档中可以看到，其存储系统采用著名的文档型数据库MongoDB的GFS文件存储(MongoGFS)。 Storage为开发者提供分布式文件存储服务，用来存放用户的持久化存储的文件，例如用户上传的图片、附件等。

KVDB属于键值型数据库，类似于redis，提供分布式key-value数据存储服务。据SAE文档所述，KVDB对每个用户可支持100G的存储空间，可支持10亿条记录；并且高性能高可靠，读写可达10w qps。

Memcache为开发者提供分布式缓存服务，主要用于缓存程序中经常读取又在一段时间内不变的数据，其使用与标准的Memcache一致。

Counter为开发者提供计数器服务。例如中国好声音网上投票，每秒钟有数以万计的人参与，这时对云存储的要求是高并发情景下处理计数的能力。Counter简化了计数应用的开发，通过API即可对计数器进行加减操作，得到统计结果。

Rank是为开发者提供的分布式环境下的排行榜服务，其特点是快速可靠，可以用于实时环境。利用Rank服务可以轻松地实现热门文章排行、用户活跃度排行等。

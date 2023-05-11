ElasticSearch作用:
	1.检索效率高(数据库效率低容易阻塞)
	2.模糊查询无法满足高匹配度的搜索,不能满足全文检索的需求
	3.ES能根据一句话来分词找出关键词然后定位分类,实现**倒排索引**
简介:
	Elasticsearch是全文检索工具,索引库独立于数据库es查询只查索引库是一个中间件
	ElasticSearch是基于lucene实现，隐藏了Lucene的复杂性，对外提供Restful 接口来操作索引、搜索(solr同样,两种都行)
**倒排索引(ES核心)**
	倒排索引就是根据每个事物建立对应的词条,然后搜索是通过查询词条来匹配对应的事物,
	正向索引是根据每个事物建立对应词条,然后搜索查询所有事物来寻找该事物是否包含指定词条
	因为我们日常baidu搜索的方式也是倒排索引通过词条属性去查找内容
ES中的各个关键字(对应数据库中各个关键字)
	![[ElasticSearch(独立搜索中间件)_image_1.jpg]]
	**ES中的数据都是Json格式**,并且不同于表中的字段,域可以随时添加 , index(数据库)下直接放文档 ,并且文档中(document)除了ID域必须有,其他的可以选择没有
ES安装与配置
	**windows:**
	下载zip压缩包解压(到bin目录ES.bat启动即可默认端口9200)
	默认无需修改配置,端口号9200,如果需要搭建ES集群则到config包下修改elasticsearch.yaml修改对应配置(没学)
	jvm.options修改给es的内存空间
	使用localhost:9200/打开es页面,后续会使用kibana的5601端口打开页面辅助操作
	**linux**:
	(注:root用户无法启动es需要创建新用户):
	下载tar.gz包解压到指定文件夹,
	到bin包下sh ./elastaicsearch.sh启动即可
	安装注意：
		kibanna版本必须和elasticsearch的版本强一致
		ik分词器的版本必须和elasticsearch的版本强一致
		且es的端口号修改了则kibana的指向es的配置也需要修改
		当elasticsearch和kibana不在一台服务器上，那么需要修改kibana的配置，正确指定elasticsearch的url
ES常用注解:
	映射类型注解:
	@Document 类似于Mybatis@Mapper注解映射表类
	@Id 类似主键id 同时es的id是表示一行数据的标识id
	@Filed 类似于表中的一个普通字段可以修改很多属性,例如index默认为true加入倒排索引
	@Repository 用来作为Dao层注入 自定义类继承extends ElasticsearchRepository<映射表类,Long>
**ES-RestfulAPI**
	分词查询:match
	不分词查询:trem
	1.索引操作
	\#创建索引库
	PUT person
	{
	 "settings": {
	   "number_of_shards": 5 (数据库分片)
	  , "number_of_replicas": 2 (数据库备份每片备份个数)
	},
	 "mappings": {
	   "properties": {
	     "name":{
	       "type": "text"
	    },
	     "age":{
	       "type": "integer"
	    },
	     "sex":{
	       "type": "integer"
	    },
	     "birth":{
	       "type": "date"
	    }
	  }
	}
	}
	​
	\#查看索引库的信息
	GET person
	\#删除索引
	DELETE person
	2.文档操作
	\#批量添加
	\#注意：json不要换行
	PUT _bulk
	{"index":{"_index":"person","_id":"4"}}
	{"doc":{"name":"jack2"}}
	{"index":{"_index":"person","_id":"3"}}
	{"doc":{"name":"jack3"}}
	3.修改文档
	\#批量修改
	POST _bulk
	{"update":{"_index":"person","_id":"1"}}
	{"doc":{"name":"张三"}}
	{"update":{"_index":"person","_id":"2"}}
	{"doc":{"name":"张三1"}}
	{"update":{"_index":"person","_id":"3"}}
	{"doc":{"name":"张三2"}}
	4.删除操作
	\#删除文档
	DELETE person/_doc/4
	\#批量删除
	PUT _bulk
	{"delete":{"_index":"person","_id":"1"}}
	{"delete":{"_index":"person","_id":"2"}}
	5.设置指定索引
	\#一定要指定索引默认使用ik，要不然默认使用standard
	PUT /emp
	{
	"settings" : {
	"index" : {
	"analysis.analyzer.default.type": "ik_max_word"
	}
	}
	}
	6.查询操作(match分词,term不分词)
	\#根据id查询
	GET emp/_doc/1
	\#根据ids查 id in(1,6)
	GET emp/_search
	{
	"query": {
	"ids": {"values": [1,6]}
	}
	}
	\#使用match查询(match_all查询所有)
	\#query的值是不会被分词的，直接匹配词条
	GET /c/_search
	{
	 "query": {
	   "match": {
	     "address": "湖北 武汉"
	#"operator": "and" 代表搜索词条中包含湖北和武汉
	#"operator": "or" 代表搜索词条中包含湖北和武汉其中一条
	  }
	}
	}
	\#多域联合查询
	\#query的值会分词，然后在多个域中匹配词条，只要其中一个域能匹配即可
	GET h/_search
	{
	 "query": {
	   "multi_match": {
	     "query": "湖北",
	     "fields": \["address","email"] \#表示在这两个字段中查询是否包含武汉词条
	  }
	}
	}
	\#term查询(特点:不分词直接匹配词条)
	\#适合根据商品类型去检索
	\#terms
	\#多个值之间是或者关系
	GET emp/_search
	{
	 "query": {
	   "terms": {
	     "address": \[
	       "湖北省",
	       "湖南省"
	    ]
	  }
	}
	}
	\#prefix查询(特点:词条以value为前缀的)
	GET emp/_search
	{
	 "query": {
	   "prefix": {
	     "address": {
	       "value": "武"
	    }
	  }
	}
	}
	\#wildcard查询(特点:不分词通配符的方式模糊查询)
	GET emp/_search
	{
	"query": {
	"wildcard": {
	"address": {
	#"value": "武汉*" 表示武汉后面跟所有
	# "value": "武汉??" 表示武汉后面跟2个词
	}
	}
	}
	}
	\#range查询(特点:范围查询)
	POST z/_search
	{
	 "query": {
	   "range": {
	     "age": {
	       "gte": 32,
	       "lte": 36
	    }
	  }
	}
	}
	\#分页查询 from+size(from是表示页数,size表示每页数)
	POST z/_search
	{
	 "from": 2,
	 "size": 2,
	 "query": {
	   "match_all": {
	  }
	}
	}
	\#复合查询 (must,must not,should)
	GET z/_search
	{
	 "query": {
	   "bool": {
	     "must": \[
	      {
	         "term": {
	           "address": {
	             "value": "湖北省"
	          }
	        }
	      },
	      {
	         "range": {
	           "balance": {
	             "gte": 5600,
	             "lte": 6000
	          }
	        }
	      }
	    ],
	"must_not": \[ \#should类似
	{
	"match": {
	"address": "湖北湖南"
	}
	},
	{
	"range": {
	"age": {
	"gte": 30,
	"lte": 36
	}
	}
	}
	]
	  }
	}
	}
	#高亮设置(重要)
	GET z/_search
	{
	 "query": {
	   "bool": {
	     "should": [
	      {
	         "match": {
	           "address": "湖北"
	        }
	      },
	      {
	         "match": {
	           "email": "hattiebond"
	      }
	    ]
	  }
	},
	 "highlight": {
	   "fields": {
	     "address": {}, #地址匹配到湖北就高亮
	     "email": {} #或者email匹配到平hattiebond也高亮
	  },
	   "pre_tags": "<font color='red'>",
	   "post_tags": "</font>"  
	}
	}
	#boosting查询(权重设置)
	当查询的关键字在文档出现的频次越高，分数越高
	指定的文档内容越短，分数越高
	搜索关键字在文档内容中出现次数越多，分数越高
	GET z/_search
	{
	"query": {
	"boosting": {
	"positive": {
	"match": {
	"address": "湖北"
	}
	},
	"negative": { #当满足"宜昌"字段条件的时候调整权重
	"match": {
	"address": "宜昌"
	}
	},
	"negative_boost": 0.2 #权重调整,最终影响score,从而影响排序 算法为:score * negative_boost
	}
	}
	}
**ES整合Idea和数据库(生成es独立数据库供服务器搜索)**
	**0.首先导入pom依赖(spring-boot-starter-data-elasticsearch)**
		配置properties信息
		spring.elasticsearch.rest.uris=\http://XXX:9200
	**1.设计index和对应Vo(必须使用设置了ik分词器的index数据库才能更好分词中文和英文)**
		设置一个Bean文件夹装 '表' 一个表中有多个域可以设置
		@Document定义一个index数据库 设置indexName
		@Id 设置主键,默认为id且最好设置为String
		@Field 中设置index=true(默认为true)会根据这个域分词建立索引 , store=flase面对一些大内容简介想要建立索引却不想存储内容数据使用 FiledType.Keyword表示密码格式不分词
		keyword类型不能设置anlyzer会报错
		最后根据该index数据库写一个对应Vo类
	**2.设计DAO层**
		@Repository标记为DAO层
		然后自定义类继承ES提供的操作类ElasticsearchRepository<@Document自定义的映射类,Long>
		类似mybatisPlus里面实现了基础的crud直接使用接口即可,但是名字不能乱改
	**3.设计Controller层**
		@Autowired注入elasticsearchRestTemplate(负责文档查询+索引库操作(索引库添加,删除,修改))
		@Autowired注入esUserRepository(负责文档的增删改操作)
		常用接口:
		通过注入的ElasticsearchRestTemplate提供的方法来创建,删除,判断索引库
	**3.1创建索引库(通过反射获取BEAN中index)
	3.2删除索引库
	3.3判断索引库是否存在
	3.4自行编写需要的索引库的操作**
	通过注入的EsUserRepository提供的方法来保存,查询文档
	**3.5自行编写需要的文档操作
	3.6.书写复合查询代码**
	**具体流程:**
		1.解析传入的json对象@RequestBody XXX获取域值(等同于表的字段值),
		2.构建查询条件类(根据json传入的域值构建查询类)
		BoolQueryBuilder defaultQueryBuilder = QueryBuilders.boolQuery();
		XXX.getxxx()获取value值然后stirngUtils.isEmpty()判断是否为空,不为空则放入defaultQueryBuilder 对象的查询条件中defaultQueryBuilder.should(QueryBuilders.termQuery("变量名", 获取到的value));->此处should表示应该,替换成must表示必须,termquery查询表示不分词matchquery表示分词
		(判断查询内容的域值是否为空,,然后将对应域值(如果是集合则遍历)加入查询条件中进行判断
		3.设置分页条件
		PageRequest pageRequest = PageRequest.of(page,limit);
		4.设置高亮条件 对应域名字段高亮
		HighlightBuilder highlightBuilder = getHighlightBuilder("域名1", "域名2");
		5.设置排序条件 根据域名排序
		FieldSortBuilder sortBuilder = SortBuilders.fieldSort("域名").order(SortOrder.DESC);
		6组装所有条件 (NativeSearchQuery类)
		NativeSearchQuery searchQuery = new NativeSearchQueryBuilder()
		.withQuery(defaultQueryBuilder)
		.withHighlightBuilder(highlightBuilder)
		.withPageable(pageRequest)
		.withSort(sortBuilder).build();
		7.根据设置的条件,传入对象内容,进行搜索 (SearchHits类)
		SearchHits<对象> searchHits = elasticsearchRestTemplate.search(searchQuery, 对象.class);
		8.(将查询到的值映射到对象(VO类)中) 同时高亮字段设置高亮
		List\<ESuserVo> userVoList = new ArrayList\<ESuserVo>();
		//遍历查询回来的每个域中内容
		for (SearchHit\<ESuser> searchHit : searchHits) {
		ESUser content = searchHit.getContent();
		System.out.println(content);
		ESUserVO esUserVo = new ESUserVO();
		BeanUtils.copyProperties(content,esUserVo);
		Map<String, List\<String>> highlightFields = searchHit.getHighlightFields(); //获取需要高亮的值
		//遍历需要高亮的值,去各个域中匹配然后替换
		for (String highlightField : highlightFields.keySet()) {
		if (域名1.equals(highlightField,get(0))){
		//替换原来没有高亮的值 esUserVo.setTags(highlightFields.get(highlightField));
		}else if(域名2.equals(highlightField,get(0))){
		//替换原来没有高亮的值 esUserVo.setDesc(highlightFields.get(highlightField).get(0))
		}
		}
		userVoList.add(esUserVo);
		}
		最后 return 映射类VoList;
		}
	//设置高亮字段的私有方法
		private HighlightBuilder getHighlightBuilder(String... fields) {
		// 高亮条件
		HighlightBuilder highlightBuilder = new HighlightBuilder(); //生成高亮查询器
		for (String field : fields) {
		highlightBuilder.field(field);//高亮查询字段
		}
		highlightBuilder.requireFieldMatch(false); //如果要多个字段高亮,这项要为false
		highlightBuilder.preTags("\<span style=\"color:red\">"); //高亮设置
		highlightBuilder.postTags("\</span>");
		//下面这两项,如果你要高亮如文字内容等有很多字的字段,必须配置,不然会导致高亮不全,文章内容缺失等
		highlightBuilder.fragmentSize(800000); //最大高亮分片数
		highlightBuilder.numOfFragments(0); //从第一个分片获取高亮片段
		return highlightBuilder;
		}
	**4.ES库表设计**
		因为ElasticSearch是非关系型数据库,不能进行联表查询,因此建立域的时候需要把所有域(字段)写进一张index
		为此我们要先提前整理好所有可能相关用到的字段name,price等设计一张index表,然后数据库通过sql语句创建一个对应index表的联表查询的视图(方便以后使用视图查询内容同步到es库
		create VIEW XXX as select 需要的参数 from XXX left joinXXX onXXX= XXX...
		)
		判断一个域的几种属性(field,type,analyzer,store)
		@field:表示为一个域(字段)
		type:建立的域的类型
		有text(分词)keyword(不分词)Integer(不分词)
		analyzer分词器:不指定或指定(Ik_max_word)
		store:store= true(存储) 或 false(不存储)
	**5.同步索引库(将数据库中视图内容同步到es数据库 )**
		流程思路:
		1.在数据库视图对应模块(例如查询商品的就在goods模块)创建接口(使用generate code生成对应mapper和service等)
		Controller层注入视图对应impl实现类写一个接口findAll()
		实现类,list()方法返回全部数据
		2.在elasticSearch模块写一个新的Dao层接口EsGoodsRepository继承ElasticsearchRepository<ESGoods,long>
		然后pom导入openfeign依赖写一个远程调用接口调用goods模块的findAll()拿到数据库数据,然后new一个ES库映射类EsGoods的List集合然后for循环数据库的视图List集合
		使用BeanUtils.copyProperties(视图数据,new esGoods());将数据复制到es的映射类中,然后使用List,add(esGoods);将复制完成的加入list集合,然后for循环遍历使用EsGoodsRepository.save(esgood);将数据一个个存入ES数据库(创建随机唯一ID)
		最后可以通过kibana堆栈管理(stack Manage)里索引模式
		index Management中查看是否有自己命名的index库存入然后到(index pattern)创建索引(ES_goods)后发现(discovery)中查看
	**6.索引查询接口开发**
		在kibana中切入到我们创建的指定库(ES_goods)然后使用指令预先模拟前端页面需求的功能(该分词的字段用match不该分的用term)必要的用must 可有可无的用should多个域(字段)同时查询的使用mutli_match
		然后到idea中将复杂查询模板代码复制过来根据需求修改
	**7.前端联调**
	**8.添加商品等操作--同步索引库(MQ或者OpenFeign)**
		openfeign(不推荐容易阻塞)
		1.创建videoToES(List<数据库类型类>){
		遍历数据库类型类集合,将其中元素转换成ES索引库类型类,通过EsVideoRepository(自带接口)的save方法加入索引库
		}
		2.商品模块依赖openfeign,并写一个@feignclient接口远程调用search中的同步索引库接口
		2.改造商品模块的添加商品等接口,调用search中的接口
		MQ(队列异步不阻塞,推荐)

---
**Kibana(可视化页面辅助操作ES)**
	**作用:** 可视化控制台辅助写es语句和es数据库控制
	安装(windows):下载kibana的zip包解压即可
	linux安装:下载tar.gz包解压即可
	**注意事项:**
		1.与ES的版本必须强一致
		2.当elasticsearch和kibana不在一台服务器上，那么需要修改kibana的配置，正确指定elasticsearch的url
	使用(默认端口号5601):localhost:5601
	![[ElasticSearch(独立搜索中间件)_image_2.jpg|175]]
	主要使用其中dev tool进行操作书写es格式数据测试(此处每一次使用都是一次请求调用的es的接口)
kibana创建索引库(ES库)和切换索引库
	stack Management->index Patterns->create index patterns
	例如:XXX*则创建出一个XXX的索引库
	然后到discovery->切换到XXX*
**IK分词器(中文分词辅助)**
	**作用:** 使用默认分词器对中文分词并不友好,IK分词器能真正把中文分词
安装:
	下载后将文件夹放到elasticsearch的plguin包下即可,es会自动搜索包下的plugin-security.policy文件
	然后到kibana控制台的dev tool中使用put /emp设定指定索引 例如: "ik_max_word"
	**注意事项:**
	IK分词器必须与ES版本强一致
分词方式使用:
	ik_smart:最少切分
	ik_max_word:最细粒度切分
	扩展词典和停用词典(config包中IKAnalyzer.cfg.xml配置,然后修改指定文件内容后重启es即可)
	![[ElasticSearch(独立搜索中间件)_image_3.jpg]]

---
## ElasticSearch持久化:
**ES中数据存储的走向**
	![[ElasticSearch(独立搜索中间件)_image_4.jpg]]
	整体流程:
	![[ElasticSearch(独立搜索中间件)_image_5.jpg]]
	1.提交到内存缓冲区(此时客户端还读不到)
	每隔1s会进行一次refresh操作刷新到内存中形成一个新的segement file文件(仅存在系统内存os cache)
	2.内存缓冲区生成新的segment,刷到文件缓存(cache)
	如果小的segment文件过多可以手动merge(会自动merge)
	3.文件系统缓存f-sync到磁盘,commit更新文件
	内存中的segement文件通过flush操作写入磁盘形成commit point文件(会先清空buffer到内存再将内存数据持久化)持久化完成后会将Translog文件(备份)清除
	默认每30s进行一次flush,translog备份过大也会进行flush
Translog作用:
	![[ElasticSearch(独立搜索中间件)_image_6.jpg]]
	如果在内存期间宕机则会丢失数据,所以要实现持久化
	es自己提供了Translog文件(数据写入到buffer内存的同时也会追加备份一份到Translog默认每5s将translog刷新到磁盘)我们可以通过对Translog进行flush操作来持久化到磁盘(流程还是先刷到缓存区再到磁盘)

---
## ElasticSearch高可用:
搭建分布式存储(分块处理,同时还解决存储空间问题)
	建立多个node,将数据进行分块处理\[1,2,3,4]分为\[1,3]和\[2,4]
	2个大块同时需要对2个大块进行备份处理(可以创建多个备份)
	创建2n个node分别存放\[1rep,3rep]和\[2rep,4rep]
如何保证高可用(恢复数据)
	metadata(元数据)会标注{block1(包含数据\[1.3]的数据块) 在`192.168174.129 192.168174.130 ...等服务器}
	假如第一个服务器没找到从第二个找,通过元数据恢复
	同理{block2(包含\[2.4]的数据块) 分布在XXX服务器.......}
客户端请求和服务端集群如何响应
	客户端使用RestHighlevelClient发送写请求es集群,集群首先master节点接收消息
	然后通过解析你请求的数据的hash地址(类似redis集群请求)来判断在那个服务器响应你的写请求,
	该服务器写完成后会将数据发送给相同的节点编号\[例如1,3]在其他服务器上的rep副本块x,y,
	只要x或者y等其中一个rep副本块完成读操作后就会发送消息给master节点让master节点返回消息给客户端
	如果其中一个节点读取失败或者超时则会放弃该会通过设置的参数timeout放弃考虑该分片

---

ES长时间未响应断开连接(临时解决办法)
	加入配置,(每5分钟发送数据保持http存活)
	httpClientBuilder.setKeepAliveStrategy((response, context) -> Duration.ofMinutes(5).toMillis());

课外思路:建立综合查询(例如B站搜索关键词出现视屏和文章或up主等多种类型)的表设计
	默认搜索视频+文章(懒加载--只有在没有视频或者手动点文章的时候去搜索)
	综合查询的数据库视图两种建立方式:
	第一种:
	1.几种查询几个视图(因为每个视频和文章对应的作者不一定相同)
	2.后台设置默认搜索视频,懒加载搜索文章等
	3.后期结合两种查询的结果
	第二种:
	1.创建单个视图,因为ES可是json格式可以插入多个的域
	单格视图省略了后期整合的麻烦,但是需要增加判断类型的域
	2.后台设置默认搜索视频,懒加载搜索文章等



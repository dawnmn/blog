Elasticsearch使用 Java 编写，它的内部使用 Lucene 做索引与搜索。可以用来做日志、搜索。从文档索引操作到文档变为可搜索状态之间的延时很短。

Elasticsearch 使用json作为文档的序列化格式。json已经成为 NoSQL 领域的标准格式。
默认的，一个文档中的每一个属性都是 被（倒排索引）索引的和可搜索的。一个没有倒排索引的属性是不能被搜索到的。
默认情况下，字段原始值会被索引用于查询，但是不会被存储。为了展示文档内容，通过一个叫 _source 的字段用于存储整个文档的原始值。

**正排索引**：存储数据与关键字一对多的上下层树状结构。以id为索引，结构简单，但是查询时需遍历整个信息。
**倒排索引**：关键字与存储数据一对多的上下层树状结构。以关键字为索引，结构复杂，但是能快速地进行全文本搜索。

**index（索引）**
索引是具有相似结构的文档的集合，类似mysql的数据库。

**document（文档）**
文档是存储在ES中的一个个JSON格式的字符，类似mysql的行。
**field（字段）**
**mapping（映射）**
类似mysql的Schema(约束)，包含的数据类型如下：


* text、keyword 字符串。keyword的意思是字符串的内容不会被分词处理。text类型ES会自动的添加一个keyword类型的子字段。
* byte、short、integer、long、float、double 数字类型
* boolean 布尔类型
* date 日期类型
* Geo_point、Geo_shape 特殊类型


**shard（分片）**
ES可以把一个完整的索引分成多个分片, 分布到不同的节点上，每个分片都是一个Lucene实例，单个Lucene中极限存储2147483519（21亿）个文档。每当一个文档进来后，根据文档的 id 会自动进行 hash 计算，并存放于计算出来的 shard 实例中。因此primary shard的数量设定后就不能修改。

分片的作用：扩大容量、提高吞吐量。


**replica（副本）**
分片有Primary Shard(主分片)、Replica Shard(副本分片), 建立索引时, 系统会先将索引存储在主分片中, 然后再将主分片中的索引复制到不同的副本中。默认情况下，Elasticsearch 为每个索引创建一个主分片和一个副本。yellow表示主分片和副本分片在同一个node。
副本是主分片的副本，有两个目的：
* 故障转移：如果主要故障，可以将副本分片提升为主分片。
* 提高性能：get 和 search 请求可以由主 shard 或副本 shard 处理。


**Analysis（文本分析）**
将全文本转换为一系列单词的过程，也叫分词。
analyzer是由三部分组成：

Character Filter：将文本中html标签剔除
Tokenizer：按照规则进行分词，在英文中按照空格分词
Token Filter：去掉 stop、world（停顿词，a、an、the、is、are等），然后转换小写

内置分词器
* Standard Analyzer 默认的分词器，按词切分，小写处理
* Simple Analyzer 按照非字母切分（符号、数字被过滤），小写处理
* Stop Analyzer 小写处理，停用词过滤(the, a, this)
* Whitespace Analyzer 按照空格切分，不转小写
* Keyword Analyzer 不分词，直接将输入当做输出
* Pattern Analyzer 正则表达式，默认是\W+(非字符串分隔)

**score（算分）**
ElasticSearch在查询一个文档的时候，默认会根据倒排索引词项的匹配程度来做一个相关性的算分，相关性算分算出的分数越高证明相关性越高，打分的本质为了排序，将相关性高的文档排列在前。

**组合查询**
使用bool来进行组合：
* must 同时满足
* should 满足其中任意一个
* must_not 同时不满足
* filter 同时满足，不算分

|  Clause   |  影响 hits   |  影响 _score  |
| --- | --- | --- |
|must|Yes     |Yes    |
|must_not|Yes     |No    |
| should    |No     |Yes    |
| filter    |Yes     |No    |


**聚合查询**
对搜索结果进行聚合，使用aggs来表示，类似于MySql中的group by。
ES中大多数的数学计算只输出一个值，如：min、max、sum、avg、cardinality(去重统计)。
聚合查询支持嵌套、组合。

**过滤查询**
ES中的查询操作分为两种：查询（query）和过滤（filter）。查询默认会计算每个返回文档的得分，然后根据得分排序。而过滤（filter）只会筛选出符合的文档，并不计算得分，而且它可以缓存文档。
Query适合全文检索以及评分相关业务。过滤适合在大范围筛选数据，而查询则适合精确匹配数据。一般应用时，应先使用过滤，后使用查询。
如果 filter 和 query 同时存在，会先执行 filter，后执行 query。
常见的过滤类型有term、terms、range、exists、ids等。

**全文本查询**
Full text 全文本查询：Match 查询
**Term**
Term 查询：返回确切的文档内容，避免对 text 字段类型使用 term。



**Elasticsearch与mongodb区别**
MongoDB作为一个数据库产品, 是拥有源数据管理能力（修改数据）的。
Elasticsearch作为一个搜索引擎，Mapping不可变也是为此服务的，es修改Mapping的代价非常高，一般都是把新数据重新写入一份新索引，然后直接切换读取的别名到新的索引。
es和MongoDB都可以通过副本增强读能力, 这与kafka很不一样(kafka的副本只有备份功能)。
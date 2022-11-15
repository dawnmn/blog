Elasticsearch使用 Java 编写，它的内部使用 Lucene 做索引与搜索。
关系型数据库会将这个对象扁平化—​通常一个字段对应一列—​而且每次查询时又需要将其重新构造为对象。
Elasticsearch 使用json作为文档的序列化格式。json已经成为 NoSQL 领域的标准格式。
默认的，一个文档中的每一个属性都是 被索引 的（有一个倒排索引）和可搜索的。一个没有倒排索引的属性是不能被搜索到的。

正排索引：存储数据与关键字一对多的上下层树状结构。
倒排索引：关键字与存储数据一对多的上下层树状结构。

index（索引）
索引是具有相似结构的文档的集合，类似mysql的数据库。
type（类型）
一个index中只能定义一个类型_doc 类似mysql的表。
document（文档）
文档是存储在ES中的一个个JSON格式的字符，类似mysql的行。
field（字段）
mapping（映射）
类似mysql的Schema(约束)，作用如下：
定义索引中的字段类型；
定义字段的数据类型，例如：布尔、字符串、数字、日期
字段倒排索引的设置
Text、Keyword 字符串。Keyword的意思是字符串的内容不会被分词处理。Text类型ES会自动的添加一个Keyword类型的子字段。
* Date 日期类型
* Integer、Float、Long 数字类型
* Boolean 布尔类型
* Geo_point、Geo_shape 特殊类型

* shard（分片）
ES可以把一个完整的索引分成多个分片, 分布到不同的节点上，每个分片都是一个Lucene实例，单个Lucene中极限存储2147483519（21亿）个文档。
replica（副本）
分片有Primary Shard(主分片)、Replica Shard(副本分片), 建立索引时, 系统会先将索引存储在主分片中, 然后再将主分片中的索引复制到不同的副本中。

Analysis（文本分析）
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
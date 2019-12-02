# MongoDB(NoSQL 文档型数据库 非关系型数据库)
## 一、JSON

&emsp;Ⅰ、JSON就是一个字符串，通过Json可以标识不同语言的对象，并且该字符串可以转换为不同语言中的对象；

&emsp;Ⅱ、Json的规范：

&emsp;&emsp;&emsp;(1)Json是一个字符串；

&emsp;&emsp;&emsp;(2)Json中的属性名必须用双引号括起来;

&emsp;Ⅲ、Json的两种格式：

&emsp;&emsp;&emsp;(1)Json对象：{}  {"name":"秃子"，"age":23}

&emsp;&emsp;&emsp;(2)Json数组：[]  [123,true,"test"]

&emsp;Ⅳ、Json中可以保存的数据类型：

&emsp;&emsp;&emsp;(1)Number  &emsp;(2)String   &emsp;(3)Boolean

&emsp;&emsp;&emsp;(4)null   &emsp;(5)Object   &emsp;(6)Array

&emsp;Ⅴ、在Js中有一个对象--JSON,通过该对象可以对JSON进行解析：

&emsp;&emsp;&emsp;(1) Json ---> Object    var obj = JSON.Parse(json);

&emsp;&emsp;&emsp;(2) Object ---> Json    var json = Json.Stringify(obj);

&emsp;Ⅵ、Java在默认情况下不支持Json解析，需要引入第三方jar包；

&emsp;&emsp;&emsp;  JSON-lib  &emsp;   jackson  &emsp;  gson

&emsp;&emsp;&emsp; Gson gson = new Gson();

&emsp;&emsp;&emsp; Map map = gson.fromJson(json,Map.class);  Json ---> Object

&emsp;&emsp;&emsp; Student st = gson.fromJson(json,Student.class);  Json ---> Object

&emsp;&emsp;&emsp; String json = gson.toJson(st);  Object ---> Json

## 二、MongoDB
&emsp;Ⅰ、MongoDB是为快速发展互联网web应用而设计的数据库系统；

&emsp;Ⅱ、MongoDB的设计目标是极简、灵活、作为web应用栈的一部分；

&emsp;Ⅲ、MongoDB的数据模型是面向文档的，所谓的面向文档是一种类似于Json的数据结构，简单理解MongoDB中存储的是各式各样的Json(Bson);

&emsp;Ⅳ、三个重要概念:

&emsp;&emsp;&emsp;(1)数据库：数据库是一个仓库，在仓库中可以存放集合；

&emsp;&emsp;&emsp;（2）集合：集合类似于数组，在集合中可以存放文档；

&emsp;&emsp;&emsp;（3）文档：文档数据库中最小的单位，存储和操作的内容都是文档，在MongoDB中每一条数据都一个文档；

&emsp;Ⅴ、MongoDB的偶数版本是稳定版，奇数版本为开发版，且在3.2版本之后不再支持32位操作系统；

&emsp;Ⅵ、在MongoDB中，数据库和集合都不需要预创建，在第一次插入数据时会自动创建；

&emsp;Ⅶ、基本操作指令：

&emsp;&emsp;&emsp;(1)show dbs &emsp; -- 查询所有数据库

&emsp;&emsp;&emsp;(2)use tableName &emsp; --进入指定数据库内

&emsp;&emsp;&emsp;(3)db  &emsp; --当前数据库

&emsp;&emsp;&emsp;(4)show collections &emsp;  --查询当前数据库内所有集合

&emsp;&emsp;&emsp;(5)db.<collection>.insert(doc) &emsp; --向指定集合中插入文档
 
&emsp;&emsp;&emsp;(5)db.<collection>.find() &emsp; --查询指定集合中所有文档，返回数组
 
&emsp;&emsp;&emsp;(5)db.<collection>.count() &emsp; --统计集合中文档个数
 
&emsp;&emsp;&emsp;(5)db.<collection>.drop() &emsp; --删除集合（若只有一个集合，数据库也会被删除）
 
&emsp;&emsp;&emsp;(5)db.<collection>.remove(doc) &emsp; 删除文档，不可逆操作
 
&emsp;&emsp;&emsp;(5)db.<collection>.update(doc) &emsp; 修改文档
 
&emsp;Ⅷ、插入的文档对象会默认添加 `_id` 属性，这个属性对应一个唯一id,是文档的唯一标识（可以手动指定，但需要确保唯一性，不推荐使用）；





 

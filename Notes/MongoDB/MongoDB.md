# MongoDB(NoSQL 文档型数据库 非关系型数据库)
## 一、JSON

&emsp;Ⅰ、JSON就是一个字符串，通过Json可以标识不同语言的对象，并且该字符串可以转换为不同语言中的对象；

&emsp;Ⅱ、Json的规范：

&emsp;(1)Json是一个字符串；

&emsp;(2)Json中的属性名必须用双引号括起来;

&emsp;Ⅲ、Json的两种格式：

&emsp;(1)Json对象：{}  {"name":"秃子"，"age":23}

&emsp;(2)Json数组：[]  [123,true,"test"]

&emsp;Ⅳ、Json中可以保存的数据类型：

&emsp;(1)Number  &emsp;(2)String   &emsp;(3)Boolean

&emsp;(4)null   &emsp;(5)Object   &emsp;(6)Array

&emsp;Ⅴ、在Js中有一个对象--JSON,通过该对象可以对JSON进行解析：

&emsp;(1) Json ---> Object    var obj = JSON.Parse(json);

&emsp;(2) Object ---> Json    var json = Json.Stringify(obj);

&emsp;Ⅵ、Java在默认情况下不支持Json解析，需要引入第三方jar包；

&emsp;&emsp;&emsp;  JSON-lib    jackson   gson

&emsp;&emsp;&emsp; Gson gson = new Gson();

&emsp;&emsp;&emsp; Map map = gson.fromJson(json,Map.class);  Json ---> Object

&emsp;&emsp;&emsp; Student st = gson.fromJson(json,Student.class);  Json ---> Object

&emsp;&emsp;&emsp; String json = gson.toJson(st);  Object ---> Json

##二、MongoDB


 

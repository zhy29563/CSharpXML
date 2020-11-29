# 第03章 应用XPath操作XML文档

.NET框架下的System.Xml.XPath命名空间提供了一些列的类，允许应用XPath数据模式查询和招式XML文档数据。

- XPath简介和语法
- XPath的内置函数
- XPatNavigator类的应用

## 3.1 XPath介绍

XPath时W3C的一个标准。它最主要的目的时为了在XML1.0或XML1.1文档节点树中定位节点。目前有XPath1.0和XPath2.0两个版本，其中XPath1.0时1999年称为W3C标准的，而XPath2.0标准的确立是在2007年。XPath是一种表达式语法，它的返回值可能是节点、节点集合、文本、已经节点和文本的混合等。XPath2.0是XPath1.0的超集，是对XPath1.0的扩充。它可以支持更加丰富的数据类型，并且保持了对XPath1.0的相对很好的向后兼容性。另外XPath2.0也是XSLT2.0和XQuery1.0用于铲鲟定位节点的主要表达式语言。

### 3.1.1 XPath节点

在XPath中，有七种类型的节点：元素、属性、文本、命名空间、处理指令、注释以及根节点。XML文档是被作为节点树来对待的。树的根被称为文档节点或根节点。

### 3.1.2 XPath语法

XPath使用路径表达式来选择XML文档中的节点或节点集合。下表列出了最常用的路径表达式。

| 表达式   | 注释                                             |
| -------- | ------------------------------------------------ |
| nodename | 选取节点下的所有子节点                           |
| /        | 选取根节点                                       |
| //       | 选取文档中所有符合条件的节点，不管该节点位于何处 |
| .        | 选取当前节点                                     |
| ..       | 选取当前节点的父节点                             |
| @        | 选取属性                                         |

样例：

```xml
<bookstore>
    <book lang="eng">
        <title>Harry Potter</title>
        <price>29.99</price>
    </book>
    <book lang="eng">
        <title>Leaning English</title>
        <price>39.95</price>
    </book>
</bookstore>
```

| 表达式          | 结果                                                         |
| --------------- | ------------------------------------------------------------ |
| bookstore       | 选取bookstore元素的所有子节点                                |
| /bookstore      | 选取根节点bookstore元素<br />假如路径起始于正斜杠(/)，则此路径始终代表到某个元素的绝对路径 |
| bookstore/book  | 选取bookstore中所有book子元素                                |
| //book          | 选取文档中的所有book子元素，而不管出于何处                   |
| bookstore//book | 选取bookstore下的所有book子元素，而不管位于bookstore内何处   |
| //@lang         | 选取名为lang的所有属性                                       |

谓词——用来查找某个特定的几点或者包含某个指定的值的节点。谓词被嵌套在方括号中。

| 表达式                             | 结果                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| /bookstore/book[1]                 | 选取boolstore节点下的第一个book元素                          |
| /boolstore/book[last()]            | 选取boolstore节点下的最后一个book元素                        |
| /boolstore/book[last()-1]          | 选取boolstore节点下的倒数第二个book元素                      |
| /boolstore/book[position()<3]      | 选取boolstore节点下的前两个book元素                          |
| //title[@lang]                     | 选取所有包含lang属性的title元素                              |
| //title[@lang=‘eng’]               | 选取所有包含lang属性值为‘eng’的title元素                     |
| /boolstore/book[price>35.00]       | 选取bookstore节点下的所有包含price元素且price元素的值大于35.00的book元素 |
| /boolstore/book[price>35.00]/title | 选取bookstore节点下的所有包含price元素且price元素的值大于35.00的book节点下的title元素 |

通配符

| 通配符 | 注释               |
| ------ | ------------------ |
| *      | 匹配任意的节点元素 |
| @*     | 匹配任意的节点属性 |
| node() | 匹配任意种类的节点 |

| 表达式       | 结果                                |
| ------------ | ----------------------------------- |
| /bookstore/* | 选取bookstore节点中的任意子节点元素 |
| //*          | 选取文档中的所有元素                |
| //title[@*]  | 选取包含任意属性的title元素         |

通过在路径表达式中使用`|`运算符，可以选择多个路径

| 表达式                         | 结果                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| //book/tile\|//book/price      | 选取book节点中的所有title和price元素                         |
| //title\|//price               | 选取文档中的所有title和price元素                             |
| /bookstore/book/title\|//price | 选取bookstore节点下book节点中的所有title元素和文档中所有的price元素 |

### 3.1.3 XPath轴

XPath轴可以定义相对于档期那节点的节点集合。

| 轴名称            | 结果                                               |
| ----------------- | -------------------------------------------------- |
| ancestor          | 选取档期那节点的所有先辈（父、祖父等）             |
| ancestor-or-self  | 选取档期那节点的所有先辈（父、祖父等）以及节点本身 |
| attribute         | 选取当前节点的所有属性                             |
| child             | 选取当前节点的所有子元素                           |
| desendant         | 选取当前节点的所有后代                             |
| desendant-or-self | 选取当前节点的所有后代及节点本身                   |
| following         | 选取档期那节点的所有命名空间节点                   |
| namespace         | 选取档期那节点的所有命名空间节点                   |
| parent            | 选取当前节点的父节点                               |
| preceding         | 选取文档中当前节点的开始标签之前的所有节点         |
| preceding-sibling | 选取档期那节点之前的所有同级节点                   |
| self              | 选取当前节点                                       |

在讲述轴时，我们必须要设计另外两个概念：位置和步。

位置路径可以时绝对的，也可以时相对的。绝对路径起始于正斜杠(/)，而相对路径不会这样。在两种情况中，位置路径均包括一个或多个步，每个步均被斜杠分隔。

绝对路径表达式：`/step/step/...`

相对路径表达式：`step/step/...`

每个步均根据档期那节点集之中的节点来进行计算。

步(step)包括：

- 轴(axis)，定义所选节点与当前节点之间的树关系
- 节点测试(node-test)，识别某个轴内部的节点
- 零个或多个谓词，更深入地提炼所选的节点集

步的语法：`轴名称::节点测试[谓词]`

| 例子                   | 结果                                                         |
| ---------------------- | ------------------------------------------------------------ |
| child::book            | 选取所有属于当前节点的子元素的book节点                       |
| attribute::lang        | 选取当前节点的lang属性                                       |
| child::*               | 选取当前节点的所有子元素                                     |
| attribute::*           | 选取当前节点的所有属性                                       |
| child::text()          | 选取当前节点的所有文本子节点                                 |
| child::node()          | 选取当前节点的所有子节点                                     |
| descendant::book       | 选取当前节点的所有book后代                                   |
| ancestor::book         | 选取当前节点的所有book先辈                                   |
| ancestor-or-self::book | 选取当前节点的所有book先辈及当前节点（如果此节点时book节点） |
| child::*/child::price  | 选区当前节点的所有price孙节点                                |

### 3.1.4 XPath远端夫和特殊字符

| 运算符         | 描述             |
| -------------- | ---------------- |
| \|             | 计算两个节点集合 |
| + - * div      | 四则运算         |
| = != < <= > >= | 比较运算         |
| or and         | 逻辑运损         |
| mod            | 取余             |

| 特殊字符 | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| /        | 此路径运算符出现在模式开头时，表示应从根节点选择             |
| //       | 从当前姐弟啊开始递归下降，此路劲该运算符出现在模式的开头时，表示应从根节点递归下降 |
| .        | 当前上下文                                                   |
| ..       | 当前上下文节点父级                                           |
| *        | 通配符：选择所有元素节点与元素名无关（不包括文本、注释、指令等节点，如果也要包含这些节点请使用node()） |
| @        | 属性名的前缀                                                 |
| @*       | 选择所有属性，与名称无关                                     |
| :        | 命名空间分隔符                                               |
| ()       | 括号运算符                                                   |
| []       | 应用筛选模式                                                 |
| []       | 下标运算符；用于在集合中编制索引                             |

### 3.1.5 XPath函数

- 存储函数

  | 名称                                 | 说明                                   |
  | ------------------------------------ | -------------------------------------- |
  | fn:node-name(node)                   | 返回参数节点的节点名称                 |
  | fn:nilled(node)                      | 返回是否拒绝参数节点的布尔值           |
  | fn:data(item.item,..)                | 接受项目序列，并返回字符串序列         |
  | fn:base-uri()<br />fn:base-uri(node) | 返回当前节点或指定节点的base-uri属性值 |
  | fn:document-uri(node)                | 返回指定根节点的document-uri属性值     |

- 错误和跟踪函数

  | 名称                                                         | 说明                                                         |
  | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | fn:error()<br />fn:error(error)<br />fn:error(error,description)<br />fn:error(error,description,error-object) | 例子：error(fn:QName('http://example.com/test','err:toohigh'),'Error:Price is too high')<br />结果向外部处理环境返回http://example.com/test#toohigh 以及字符串“Error:Price is too high” |
  | fn:trace(value,label)                                        | 用于对查询进行debug                                          |

- 有关数值的函数

  | 名称                    | 说明                                               |
  | ----------------------- | -------------------------------------------------- |
  | fn:number(arg)          | 返回参数的数值。参数可以时布尔值、字符串或节点集合 |
  | fn:abs(num)             | 返回参数的绝对值                                   |
  | fn:ceiling(num)         | 返回大于num参数的最小整数                          |
  | fn:floor(num)           | 返回不大于num参数的最大整数                        |
  | fn:round(num)           | 把num参数进行四舍五入                              |
  | fn:round-half-to-even() | 四舍五入到最小偶数                                 |

  

- 有关字符串的函数

  | 名称                                                         | 说明                                                         |
  | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | fn:string(arg)                                               | 返回参数的字符串值。参数可以是数字、逻辑值或节点集合         |
  | fn:codepoints-to-string(int,int,...)                         | 根据代码点序列返回字符串                                     |
  | fn:string-to-codepoints(string)                              | 返回代码点序列                                               |
  | fn:codepoint-equal(comp1, comp2)                             | 根据Unicode代码点对照，如果comp1的值等于comp2的值，则返回true |
  | fn:compare(comp1, comp2)<br />fn:compare(comp1, comp2,collation) | comp1 < comp2 --> -1<br />comp1 = comp2 --> 0<br />comp1 > comp2 --> 1 |
  | fn:concat(string,string,...)                                 | 字符串拼接                                                   |
  | fn:string-join((string,string,...),sep)                      | 使用指定分隔符凭借字符串                                     |
  | fn:substring(string, start, len)<br />fn:substring(string, start) | 提取子串                                                     |
  | fn:string-length(string)<br />fn:string-length()             | 返回指定字符串的床都。如果没有参数，则返回当前节点的字符串值的长度 |
  | fn:normalize-space(string)<br />fn:normalize-space()         | 删除指定字符串的开头和结尾的空白，并把内部的所有空白序列替换为一个，然后返回结果。如果没有参数，则处理当前节点 |
  | fn:normalize-unicode()                                       | 执行Unicode规则化                                            |
  | fn:upper-case(string)<br />fn:lower-case(string)             | 大小写转换                                                   |
  | fn:translate(string,string,string)                           | 把string1中的string2替换为string3                            |
  | fn:escape-uri(stringURI, esc-res)                            | 把王子利艾波啊中的特殊字符进行转换                           |
  | fn:contrains(string1,string2)                                | 如果string1中包含string2，返回true                           |
  | fn:starts-with(string1,string2)<br />fn:ends-with(string1,string2) | 如果string1以string开始/结束，返回true                       |
  | fn:substring-before(string1,string2)<br />fn:substring-after(string1,string2) | 返回string2在string1中出现之前/后的子字符串                  |
  | fn:matches(string, pattern)                                  | 如果string参数匹配指定的模式，则返回true                     |
  | fn:replace(string,pattern,replace)                           | 把指定的模式替换为replace参数                                |
  | fn:tolenize(string,pattern)                                  | 以指定的模式分割字符串                                       |

  

- 上下文函数

  | 名称                                 | 说明                                |
  | ------------------------------------ | ----------------------------------- |
  | fn:position()                        | 返回当前正在被处理的节点的index位置 |
  | fn:last()                            | 返回在vei处理的节点列表中的项目数目 |
  | fn:current-dateTime()                | 返回当前的dateTime(带有时区)        |
  | fn:current-date()                    | 返回当前的日期(带有时区)            |
  | fn:current-time()ytttttttttttttttttt | 返回当前的时间(带有时区)            |
  | fn:implicit-timezone()               | 返回隐式时区的值                    |
  | fn:default-collation()               | 返回默认对照的值                    |
  | fn:static-base-uri()                 | 返回base-uri的值                    |

  

- 关于节点的函数

  | 名称                                              | 说明                                                         |
  | ------------------------------------------------- | ------------------------------------------------------------ |
  | fn:name()<br />fn:name(nodeset)                   | 返回当前节点的名称或指定节点集合中的第一个节点               |
  | fn:local-name()<br />fn:local-name(nodeset)       | 返回当前节点的名称或指定节点集合中第一个节点——不带有命名空间前缀 |
  | fn:namespace-uri()<br />fn:namespace-uri(nodeset) | 返回当前节点或指定根节点集合中第一个节点的命名空间URI        |
  | fn:lang(lang)                                     | 如果当前节点的语言匹配指定的语言，则返回true                 |
  | fn:root()<br />fn:root(node)                      | 返回档期那节点或指定节点所属的节点树的根节点。通常是文档节点 |

  

- 一般性函数

  | 名称                                          | 说明                                                         |
  | --------------------------------------------- | ------------------------------------------------------------ |
  | fn:index-of((item,item,...),searchitem)       | 返回在项目序列中等于searchitem参数的位置                     |
  | fn:remove((item,item,...),position)           | 返回由item参数构造的新序列，同时删除position参数指定的项目   |
  | fn:empty(item,item,...)                       | 如果参数值是空序列，返回true                                 |
  | fn:exists(item,item,...)                      | 如果参数值不是空序列，则返回true                             |
  | fn:distinct-values((item,item,...),collation) | 返回唯一不同的值                                             |
  | fn:insert-before((item,item,...),pos,inserts) | 返回由item参数构造的新序列——同时在pos参数指定位置插入inserts参数值 |
  | fn:reverse((item,item,...))                   | 返回指定的项目的倒序                                         |
  | fn:subsequence((item,item,...),start,len)     | 返回start参数指定的位置返回项目序列，序列的长度由len参数指定，第一个项目的位置是1 |
  | fn:unordered((item,item,...))                 | 依据实现决定的顺序来返回项目                                 |

  

- 关于布尔值的函数

  | 名称            | 说明                                                |
  | --------------- | --------------------------------------------------- |
  | fn:boolean(arg) | 返回数字、字符串或节点集合的布尔值                  |
  | fn:not(arg)     | 先通过boolean()函数把参数还原为一个布尔值。然后取反 |
  | fn:true()       | 返回true                                            |
  | fn:false()      | 返回false                                           |

## 3.2 XPath数据模型

  .NET 框架的XP安太后数据模型依赖于位于System.Xml.Path命名空间中的XPathNavigator类。该类是一个抽象类，提供基于光标的导航模型遍历XML文档的数据，并且它还能允许你编辑XML文档。你能从任何一个实现了IXPathVavigator接口的类获得XPathNavigator的实例。XmlDocument和XPathDocument类已经实现了这个接口。

  System.Xml.Path命名空间中的XPathDocument类使用XPath数据模型，提供一个只读的代表一个XML文档的实例。该实例被加载到内存中，因为可以快速地访问XML文档的各个部分。

  由XmlDocument返回的XPathNavigator实例是可编辑的，而由XPathDocument返回的XPathNavigator是只读的。由于XmlDocument和XPathDocument都实现了IXPathVavigator，因而二者都提供CreateNavigator()方法用于创建XPathNavigator类的对象。

### 3.2.1 应用XPathNavigator遍历XML文档

  

```c#
string strXml = "<?xml version=\"1.0\" encoding=\"utf-8\" ?>"
              + "<customers>"
			  + "	<customer customerid = \"1\">"
			  + "		<firstname> John </firstname >"
			  + "		<lastname> Cranston </lastname >"
			  + "		<homephone> (445) 269 - 9857 </homephone >"
			  + "	</customer>"
	  		  + "	<customer customerid = \"2\">"
			  + "		<firstname> Annie </firstname >"
			  + "		<lastname > Loskar </lastname >"
			  + "		<homephone > (445) 269 - 9482 </homephone >"
			  + "	</customer >"
			  + "</customers > ";

using(StringReader reader = new StringReader(strXml))
{
	XPathDocument _doc = new XPathDocument(reader);
	XPathNavigator _na = _doc.CreateNavigator();

	//Move to root of document (<?xml version="1.0" encoding="utf-8" ?>)
	_na.MoveToRoot();
	
	_na.MoveToFirstChild();//Move the cursor to <customers> node

	Console.WriteLine("Customers");

	if (_na.HasChildren)
	{
		_na.MoveToFirstChild();//Move the cursor to <customer> node
							   //The loop get <customer> node's attribute and value 
		do
		{
			string _id = _na.GetAttribute("customerid", "");
			Console.WriteLine("\tCustomer ID :" + _id);

			_na.MoveToFirstChild();

			//This loop get <firstname>,<lastname>,<homephone>, and <notes> node's name and value
			do
			{
				string _name = _na.Name;
				Console.WriteLine("\t\t" + _name + " : " + _na.Value);
			}
			while (_na.MoveToNext());
			_na.MoveToParent();//go back <customer> node
		}
		while (_na.MoveToNext());//move to another <customer> node
	}
}
```

> Customers
>  Customer ID :1
>   firstname : John 
>   lastname : Cranston 
>   homephone : (445) 269 - 9857 
>  Customer ID :2
>   firstname : Annie 
>   lastname : Loskar 
>   homephone : (445) 269 - 9482 

### 3.2.2 选择XML文档节点

```c#
string strXml = "<?xml version=\"1.0\" encoding=\"utf-8\" ?>"
              + "<customers>"
			  + "	<customer customerid = \"1\">"
			  + "		<firstname> John </firstname >"
			  + "		<lastname> Cranston </lastname >"
			  + "		<homephone> (445) 269 - 9857 </homephone >"
			  + "	</customer>"
	  		  + "	<customer customerid = \"2\">"
			  + "		<firstname> Annie </firstname >"
			  + "		<lastname > Loskar </lastname >"
			  + "		<homephone > (445) 269 - 9482 </homephone >"
			  + "	</customer >"
			  + "</customers > ";

using(StringReader reader = new StringReader(strXml))
{
	XPathDocument _doc = new XPathDocument(reader);
	XPathNavigator _na = _doc.CreateNavigator();

	XPathExpression _exp = _na.Compile("customers/customer[@customerid=1]");
	XPathNodeIterator _iterator = _na.Select(_exp);
	Console.WriteLine("The expressions returned " + _iterator.Count + " nodes");
	if (_iterator.Count > 0)
	{
		while (_iterator.MoveNext())
		{
			Console.WriteLine(_iterator.Current.OuterXml);//Show the XML format of node(s)
		}
	}
}
```

> The expressions returned 1 nodes
> <customer customerid="1">
>  <firstname> John </firstname>
>  <lastname> Cranston </lastname>
>  <homephone> (445) 269 - 9857 </homephone>
> </customer>

```c#
string strXml = "<?xml version=\"1.0\" encoding=\"utf-8\" ?>"
              + "<customers>"
			  + "	<customer customerid = \"1\">"
			  + "		<firstname> John </firstname >"
			  + "		<lastname> Cranston </lastname >"
			  + "		<homephone> (445) 269 - 9857 </homephone >"
			  + "	</customer>"
	  		  + "	<customer customerid = \"2\">"
			  + "		<firstname> Annie </firstname >"
			  + "		<lastname > Loskar </lastname >"
			  + "		<homephone > (445) 269 - 9482 </homephone >"
			  + "	</customer >"
			  + "</customers > ";

using(StringReader reader = new StringReader(strXml))
{
	XPathDocument _doc = new XPathDocument(reader);
	XPathNavigator _na = _doc.CreateNavigator();

	XPathNavigator _result = _na.SelectSingleNode(@"customers/customer[@customerid=1]");

	_result.MoveToFirstAttribute();
	string name = "Name: " + _result.Name + " " + "Value: " + _result.Value;
	Console.WriteLine(name);
	
	_result.MoveToParent();
	_result.MoveToFirstChild();//move to <customer> node's child node such as <firstname>,<lastname>,<homephone>,
							   // and <notes>
	do
	{
		Console.WriteLine("\t<" + _result.Name +">"+ _result.Value +"</"+_result.Name+">");
	}
	while (_result.MoveToNext());//Move to sibling node
}
```

> Name: customerid Value: 1
>  <firstname> John </firstname>
>  <lastname> Cranston </lastname>
>  <homephone> (445) 269 - 9857 </homephone>



### 3.2.3 通过XPathNavigator获得XmlReader和XmlWriter

虽然，XPathN                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      avigator允许你读取XML文档，但有时也可能因为某种原因需要XmlReader。XmlReader可以进一步读取返回的节点，通过调用XPathNavigator的ReadSubTree()方法获得XmlReader，如同你可以从XPathNavigator创建一个XmlReader一样，你可以从中创建一个XmlWriter。XmlWriter对于你将选择的节点从XPathNavigator中写入一个文件式流中是非常有用的。

XPathNavigator提供了一个名为WriteSubTree()的函数方法，该方法接受一个XmlWriter作为输入参数，并将当前节点写入该XmlWriter中。

```c#
string strXml = "<?xml version=\"1.0\" encoding=\"utf-8\" ?>"
			  + "<customers>"
			  + "	<customer customerid = \"1\">"
			  + "		<firstname> John </firstname >"
			  + "		<lastname> Cranston </lastname >"
			  + "		<homephone> (445) 269 - 9857 </homephone >"
			  + "	</customer>"
				+ "	<customer customerid = \"2\">"
			  + "		<firstname> Annie </firstname >"
			  + "		<lastname > Loskar </lastname >"
			  + "		<homephone > (445) 269 - 9482 </homephone >"
			  + "	</customer >"
			  + "</customers > ";
			  
using (StringReader reader = new StringReader(strXml))
{
	XPathDocument _doc = new XPathDocument(reader);
	XPathNavigator _na = _doc.CreateNavigator();

	_na.MoveToRoot();
	_na.MoveToFirstChild();
	if (_na.HasChildren)
	{
		_na.MoveToFirstChild();
		do
		{
			string _id = _na.GetAttribute("customerid", "");
			Console.WriteLine("customerid = " + _id);
			XmlReader _reader = _na.ReadSubtree();
			while (_reader.Read())
			{
				if (_reader.NodeType == XmlNodeType.Element)
				{
					Console.WriteLine("\t<" + _reader.Name + ">" + _reader.ReadString() + "</" + _reader.Name + ">");
				}
			}
		}
		while (_na.MoveToNext());
	}
}
```

> customerid = 1
>  <customer></firstname>
>  <lastname> Cranston </lastname>
>  <homephone> (445) 269 - 9857 </homephone>
> customerid = 2
>  <customer></firstname>
>  <lastname> Loskar </lastname>
>  <homephone> (445) 269 - 9482 </homephone>

```c#
string strXml = "<?xml version=\"1.0\" encoding=\"utf-8\" ?>"
			  + "<customers>"
			  + "	<customer customerid = \"1\">"
			  + "		<firstname> John </firstname >"
			  + "		<lastname> Cranston </lastname >"
			  + "		<homephone> (445) 269 - 9857 </homephone >"
			  + "	</customer>"
				+ "	<customer customerid = \"2\">"
			  + "		<firstname> Annie </firstname >"
			  + "		<lastname > Loskar </lastname >"
			  + "		<homephone > (445) 269 - 9482 </homephone >"
			  + "	</customer >"
			  + "</customers > ";
			  
using (StringReader reader = new StringReader(strXml))
{
	XPathDocument _doc = new XPathDocument(reader);
	XPathNavigator _na = _doc.CreateNavigator();

	if (_na.HasChildren)
	{
		_na.MoveToFirstChild();
		do
		{
			XmlTextWriter _writer = new XmlTextWriter(System.Console.Out);
			_na.WriteSubtree(_writer);
		}
		while (_na.MoveToNext());
	}
}
```

> <customers><customer customerid="1"><firstname> John </firstname><lastname> Cranston </lastname><homephone> (445) 269 - 9857 </homephone></customer><customer customerid="2"><firstname> Annie </firstname><lastname> Loskar </lastname><homephone> (445) 269 - 9482 </homephone></customer></customers>

### 3.2.4 通过XPathNavigator类编辑XML文档

截至到目前位置，我们只是使用XPathNavigator类遍历和读取XML文档。实际上，XPathNavigator也可以编辑XML文档的（此处所谓编辑是指添加、修改、删除和存储节点）。通常情况下，从XPathDocument中获取的XPathNavigator实例对象是只读的，因为不能用于编辑。而从XmlDocument中获得的XPathNavigator实例对象是可以用于编辑的。通过使用XPathNavigator的CanEdit属性来检查XPathNavigator的实例是否可以用于编辑，是则返回true，否则返回false。

```c#
void Main()
{
	string strXml = "<?xml version=\"1.0\" encoding=\"utf-8\" ?>"
				  + "<customers>"
				  + "	<customer customerid = \"1\">"
				  + "		<firstname> John </firstname >"
				  + "		<lastname> Cranston </lastname >"
				  + "		<homephone> (445) 269 - 9857 </homephone >"
				  + "	</customer>"
					+ "	<customer customerid = \"2\">"
				  + "		<firstname> Annie </firstname >"
				  + "		<lastname > Loskar </lastname >"
				  + "		<homephone > (445) 269 - 9482 </homephone >"
				  + "	</customer >"
				  + "</customers > ";
				  
	XmlDocument doc = new XmlDocument();
	doc.LoadXml(strXml);
	
	XPathNavigator na = doc.CreateNavigator();
	
	Console.WriteLine("Show Raw:");
	this.Show(na, "1");
	doc.Save(System.Console.Out);
	Console.WriteLine("\r\n");

	Console.WriteLine("Update:");
	this.Update(na, "1");
	doc.Save(System.Console.Out);
	Console.WriteLine("\r\n");

	Console.WriteLine("Add:");
	this.Add(na, "3");
	doc.Save(System.Console.Out);
	Console.WriteLine("\r\n");

	Console.WriteLine("Delete:");
	this.Delete(na, "1");
	doc.Save(System.Console.Out);
	Console.WriteLine("\r\n");
}

private bool IDisExist(XPathNavigator na, string ID)
{
	bool _isexist = false;
	na.MoveToRoot();
	na.MoveToFirstChild();
	if (na.HasChildren)
	{
		na.MoveToFirstChild();
		do
		{
			string _id = na.GetAttribute("customerid", "");
			if (_id == ID)
			{
				_isexist = true;
			}
		}
		while (na.MoveToNext());
	}
	return _isexist;
}

private void Show(XPathNavigator na, string ID)
{
	bool _isexist = IDisExist(na, ID);
	if (_isexist == false)
	{
		return;
	}
	
	na.MoveToRoot();
	na.MoveToFirstChild();
	if (na.HasChildren)
	{
		na.MoveToFirstChild();
		do
		{
			string _id = na.GetAttribute("customerid", "");
			if (_id == ID)
			{
				na.MoveToFirstChild();
				if (na.HasChildren)
				{
					do
					{
						Console.WriteLine("\t<" + na.Name + ">" + na.Value + "</" + na.Name + ">");
					}
					while (na.MoveToNext());
				}
				na.MoveToParent();
			}
		}
		while (na.MoveToNext());
	}
}

private void Add(XPathNavigator na, string ID)
{
	bool _isexist = this.IDisExist(na, ID);
	if (_isexist)
	{
		return;
	}
	
	na.MoveToRoot();
	na.MoveToFirstChild();

	XmlWriter _writer = na.AppendChild();
	_writer.WriteStartElement("customer");
	_writer.WriteAttributeString("customerid","2");
	_writer.WriteElementString("firstname", "Bernie");
	_writer.WriteElementString("lastname", "Christo");
	_writer.WriteElementString("homephone", "(445) 269-3412");
	_writer.WriteEndElement();
	_writer.Close();
}

private void Update(XPathNavigator na, string ID)
{
	bool _isexist = this.IDisExist(na, ID);
	if (_isexist == false)
	{
		return;
	}
	na.MoveToRoot();
	na.MoveToFirstChild();

	na.MoveToFirstChild();

	do
	{
		string _id = na.GetAttribute("customerid", "");
		if (_id == ID)
		{
			na.MoveToFirstChild();
			do
			{
				switch (na.Name)
				{
					case "firstname":
						na.SetValue("new_" + na.Value);
						break;
					case "lastname":
						na.SetValue("new_" + na.Value);
						break;
					case "homephone":
						na.SetValue("new_" + na.Value);
						break;
					case "notes":
						na.SetValue("new_" + na.Value);
						break;
				}
			}
			while (na.MoveToNext());
			na.MoveToParent();
		}
	}
	while (na.MoveToNext());
}

private void Delete(XPathNavigator na, string ID)
{
	bool _isexist = this.IDisExist(na, ID);
	if (_isexist == false)
	{
		return;
	}

	na.MoveToRoot();
	na.MoveToFirstChild();
	na.MoveToFirstChild();
	do
	{
		string id = na.GetAttribute("customerid", "");
		if (id == ID)
		{
			na.DeleteSelf();
		}
	}
	while (na.MoveToNext());
}

```

> Show Raw:
>   <firstname> John </firstname>
>   <lastname> Cranston </lastname>
>   <homephone> (445) 269 - 9857 </homephone>
> <?xml version="1.0" encoding="utf-16"?>
> <customers>
>   <customer customerid="1">
>     <firstname> John </firstname>
>     <lastname> Cranston </lastname>
>     <homephone> (445) 269 - 9857 </homephone>
>   </customer>
>   <customer customerid="2">
>     <firstname> Annie </firstname>
>     <lastname> Loskar </lastname>
>     <homephone> (445) 269 - 9482 </homephone>
>   </customer>
> </customers>
>
> Update:
> <?xml version="1.0" encoding="utf-16"?>
> <customers>
>   <customer customerid="1">
>     <firstname>new_ John </firstname>
>     <lastname>new_ Cranston </lastname>
>     <homephone>new_ (445) 269 - 9857 </homephone>
>   </customer>
>   <customer customerid="2">
>     <firstname> Annie </firstname>
>     <lastname> Loskar </lastname>
>     <homephone> (445) 269 - 9482 </homephone>
>   </customer>
> </customers>
>
> Add:
> <?xml version="1.0" encoding="utf-16"?>
> <customers>
>   <customer customerid="1">
>     <firstname>new_ John </firstname>
>     <lastname>new_ Cranston </lastname>
>     <homephone>new_ (445) 269 - 9857 </homephone>
>   </customer>
>   <customer customerid="2">
>     <firstname> Annie </firstname>
>     <lastname> Loskar </lastname>
>     <homephone> (445) 269 - 9482 </homephone>
>   </customer>
>   <customer customerid="2">
>     <firstname>Bernie</firstname>
>     <lastname>Christo</lastname>
>     <homephone>(445) 269-3412</homephone>
>   </customer>
> </customers>
>
> Delete:
> <?xml version="1.0" encoding="utf-16"?>
> <customers>
>   <customer customerid="2">
>     <firstname> Annie </firstname>
>     <lastname> Loskar </lastname>
>     <homephone> (445) 269 - 9482 </homephone>
>   </customer>
>   <customer customerid="2">
>     <firstname>Bernie</firstname>
>     <lastname>Christo</lastname>
>     <homephone>(445) 269-3412</homephone>
>   </customer>
> </customers>




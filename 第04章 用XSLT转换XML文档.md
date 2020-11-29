# 第04章 用XSLT转换XML文档

在实际的XML应用中，经常需要将XML数据从一种格式转换到另一种格式。例如，你可能需要将XML数据转换成HTML，这样就可以在浏览器中显示。为此，引入XSLT（Extensible Stylesheet Language Transformation，可扩展样式表语言）。

- XSLT是什么
- 使用XslCompiledTransform类转换XML文档
- 如何使用XSLT扩展对象
- 如何将参数传递到XSLT样式表

## 4.1 XSLT简介

为了满足转换XML文档这一要求，W3C推出了XSL（Extensible Stylesheet Language）——可扩展演示表语言。XSL在转换XML文档时分为明显的两个过程：第一转换文档结构；第二将文档格式化输出。这两部可以分离开来并单独处理，因此XSL在发展过程中毒箭分了为XSLT（结构转换）和XSL-FO（Formatting Object）(格式化输出)两种分支语言，其中XSL-FO的作用就类似CSS在HTML中的作用，所以我们这里只淘箩第一步的转换过程，也就是XSLT。另外，在学习XML时，我们已经知道XML时一个完整的树状结构文档。在转换XML文档时可能需要处理其中一部分数据，那么如何查找和定位XML文档中的信息呢？XPath就是一种专门用来在XML文档中查找信息的语言。XPath隶属XSLT，因此我们通常将XSLT语法和XPath语法混在一起讲解。

将文件以XSL样式表进行声明的文件头是`<xls:stylesheet>`或`<xsl:transform>`，这二者完全同义没任何一个都能被使用。根据W3C的XSLT标准，声明XSL样式表的正确方法是：

```xml
<xsl:stylesheet xmlns:xsl="http://www.w3.org/1999/XSL/Transform" version="1.0">
```

或

```xml
<xsl:transform xmlns:xsl="http://www.w3.org/1999/XSL/Transform" version="1.0">
```

想要由权使用XSLT元素，必须在文件的顶端事先声明XSLT命名空间的属性和特征。`xmlns:xsl="http://www.w3.org/1999/XSL/Transform"`指出了官方W3C XSLT的命名空间。如果你使用了这个命名空间，也必须注明属性的版本（version="1.0"）。

### 4.1.1 XSLT的基本转换过程

XSLT的转换过程是将XML原文档输入，用XSLT作为模板，通过转换引擎，最终输出需要的HTML文档。其中的转换引擎就是比喻中“用力一按”的过程。在具体应用中，由专门的软件 XMLProcessor来实现这个转换过程。现在我们来看一个简单的XSLT实际应用例子，获得一些感官上的认识。

- 原始XML文档

  ```xml
  <?xml version="1.0" encoding="iso-8859-1"?>
  <greeting>Hello, world!</greeting>
  ```

  

- XSLT模板

  ```xml
  <?xml version="1.0" encoding="iso-8859-1"?>
  <xsl:stylesheet xmlns:xsl="http://www.w3.org/1999/XSL/Transform" version="1.0">
  <xsl:template match="/">
  <html>
  <head>
  <title>First XSLT example</title>
  </head>
  <body>
  <p><xsl:value-of select="greeting"/></p>
  </body>
  </html>
  </xsl:template>
  </xsl:stylesheet>
  ```

  

- 添加转换模板的XML文档

  ```xml
  <?xml version="1.0" encoding="iso-8859-1"?>
  <?xml-stylesheet type="text/xsl" href="hello.xsl"?>
  <greeting>Hello, world!</greeting>
  ```

  

用浏览器打开添加转换模板后的XML文档，则可以看到结果。

- 模板解析
  - `<?xml version="1.0" encoding="iso-8859-1"?>`是标准的XML文档的首行代码，因为XSLT本身也是XML文档。encoding属性用来定义文档使用的编码形式，iso-8895-1主要支持西欧和北美的语言编码。如果你想使用简体中文，那么就应该写成`<?xml version="1.0" encoding="GB2312"?>`
  - `<xsl:stylesheet xmlns:xsl="http://www.w3.org/1999/XSL/Transform" version="1.0">`这是标准的XSLT文件首行代码。`xsl:stylesheet`代码的意思是将文档作为一个样式表来处理。`xmlns:xsl`属性是一个名字空间声明。和XML中的名字空间使用方法一样，用来防止元素名称重复和混乱。其中前缀xsl的意思是文档中使用的元素遵守W3C的XSLT规范。最后的`version`属性说明样式表只采用XSLT1.0的标准功能。
  - `<xsl:template match="/">`一个`xsl:template`元素定义一个模板规则。属性`match="/"`说明XML原文档中，这个模板规则作用的起点。`"/"`是一种XPath语法，这了代表结构的根
  - 接下来的代码，基本上都是HTML元素和文档构成。只有`xsl:value-of`是XSLT语法，这里`xsl:value-of`的作用是将原文档中的一个节点的值拷贝到输出文档。而`select`属性则详细指定要处理的节点名称，这是XPath语法。`greeting`的意思就是寻找根节点名为greeting的元素。并用模板来处理这个节点。具体的就是找到greeting元素，然后将元素的值按模板样式拷贝到输出文件

### 4.1.2 XSLT语法

为了理解XSLT语法是如何应用的，我们将通过实例来进行讲解，而本章的所有实例中用到的XML文档仍是Customers.xml。

1. &lt;xsl:template&gt;和&lt;xsl:apply-templates&gt;

   XSLT文件是由一个或多个被称为“模板”的规则设置组成的，任何一个XSLT温江至少包含一个模板。模板由两部分组成：匹配模式和执行。简单地将，模式定义XML源文档中哪一个节点将被模板处理，执行规则定义输出的是什么格式。两部分对应的语法为`<xsl:template>`和`<xsl:apply-template>`。

   - `<xsl:template>`的语法：

     ```xml
     <xsl:template match = pattern name = qname priority = number mode = qname>
     	<!--执行内容-->
     </xsl:template>
     ```

     `<xsl:template>`的作用是定义一个新的模板。属性中name,priority和mode用来区别匹配同一节点的不同模板。他们不是常用的属性。match属性则控制模板的匹配模式，匹配模式是用来定位XML源文档中哪一个节点被模板处理。一个模板匹配一个节点。假设我们要处理一个包含章节和段落文档。我们用para元素定义段落，用chapter元素定义章节。我们来看看match属性的可能值。下面的语句写法说明模板匹配所有的para元素：

     ```xml
     <xsl:template match = "para">
     </xsl:template>
     ```

     下面的语句写法说明模板匹配所有的para元素和所有的chapter元素：

     ```xml
     <xsl:template match = "(chapter|para)">
     </xsl:template>
     ```

     下面的语句写法说明模板匹配所有的父节点为chapter元素的para元素：

     ```xml
     <xsl:template match = "chapter//para">
     </xsl:template>
     ```

     下面的语句写法说明模板匹配根节点：

     ```xml
     <xsl:template match = "/">
     </xsl:template>
     ```

     

   - `<xsl:apply-templates>`语法：

     ```xml
     <xsl:apply-templates select = node set-expression mode = qname>
     </xsl:apply-templates>
     ```

     `<xsl:apply-templates>`用来执行哪一个节点被模板具体处理。你可以将它理解为程序中调用子函数。select属性用来定义确切的节点名称。`<xsl:apply-templates>`总是包含在`<xsl:template>`元素中，像这样：

     ```xml
     <xls:templete match="/">
         <xls:apply-templates select="para">
         </xls:apply-templates>
     </xls:templete>
     ```

     这段代码说明模板匹配整个文档(根节点)，具体执行时处理根节点下所有的para元素。

     ```xml
     <xls:templete match="para">
         <p>
             <xls:apply-templates>
         	</xls:apply-templates>
         </p>
     </xls:templete>
     ```

     而这一段代码则表示模板匹配para节点，所有para下的子元素都将被处理。

2. `<xsl:value-of>`

   `<xsl:value-of>`用来选取XML愿意以及把它添加到已被转换的输出流中。例如：有一个个人资料的XML文档：

   ```xml
   <?xml version="1.0" encoding="iso-8859-1"?>
   <PERSON>
       <name>ajie</name>
       <age>28</age>
   </PERSON>
   ```

   如果想在输出文档中显示上面这个XML原文档中的name元素的值，可以这样写XSLT代码：

   ```xml
   <xsl:template match="PERSON">
       <xsl:value-of select="name"></xsl:value-of>
   </xsl:template>
   ```

   执行后，会看到`ajie`被单独显示出来。其中match="PERSON"定义模板PERSON节点，`<xsl:value-of>`语法说明需要输出一个节点的值，而select="name"则定义需要被输出的元素为name。同样的功能还有`xsl:copy-of`，用法一样，就不重复解释了。

   我们用一个例子来帮助你理解上述语法的用法，下面是一种针对Customers.xml源文件的样式表：

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <xsl:stylesheet xmlns:xsl="http://www.w3.org/1999/XSL/Transform" version="1.0">
     <xsl:template match="/">
       <html>
         <body>
           <h1>Customer Listing</h1>
           <xsl:apply-templates/>
         </body>
       </html>
     </xsl:template>
     <xsl:template match="customer">
       <div>
         <h3>Customer ID:
         <xsl:value-of select="@customerid"/>
         </h3>
         <xsl:apply-templates select="firstname"/>
         <xsl:apply-templates select="lastname"/> 
         <xsl:apply-templates select="homephone"/>
         <xsl:apply-templates select="notes"/>
       </div>
     </xsl:template>
     <xsl:template match="firstname">
       <b>First Name :</b>
       <xsl:value-of select="."/>
       <br />
     </xsl:template>
     <xsl:template match="lastname">
       <b>Last Name :</b>
       <xsl:value-of select="."/>
       <br />
     </xsl:template>
     <xsl:template match="homephone">
       <b>Home Phone :</b>
       <xsl:value-of select="."/>
       <br />
     </xsl:template>
     <xsl:template match="notes">
       <b>Notes :</b>
       <xsl:value-of select="."/>
       <br />
     </xsl:template>
   </xsl:stylesheet>
   ```

   在Customers.xml文件中添加如下代码，调用样式表文件Customers.xsl:

   ```xml
   <?xml version="1.0" encoding="utf-8" ?>
   <?xml-stylesheet type="text/xsl" href="Customers.xsl"?>
   <customers>
       ....
   </customers>
   ```

   

3. `<xsl:for-each>`

   `<xsl:for-each>`元素允许在XSLT里使用循环语句。select属性是一个XPath的表达式值。我们也可以通过将一个规则添加到`<xsl:for-each>`中的select属性来过滤结果。过滤操作是：=，!=，&lt;，&gt;。例如，一个含多个个人资料的XML文档：

   ```xml
   <?xml version="1.0" encoding="iso-8859-1"?>
   <PEPLE>
       <PERSON>
           <name>ajie</name>
       	<age>28</age>
   	</PERSON>
       <PERSON>
           <name>tom</name>
       	<age>24</age>
   	</PERSON>
       <PERSON>
           <name>miake</name>
       	<age>30</age>
   	</PERSON>
   </PEPLE>
   ```

   我们需要显示所有人的姓名，则可以将XSLT代码写成：

   ```xml
   <xsl:template match="PEOPLE">
       <xsl:for-each select="child::PERSON">
       	<xsl:value-of select="name">
           </xsl:value-of>
       </xsl:for-each>
   </xsl:template>
   ```

   

4. `<xsl:if>`

   `<xsl:if>`类似普通程序语言的if条件语句，允许设定根节点满足某个条件时，被模板处理。`<xsl:if>`的语法格式为：

   ```xml
   <xsl:if test=布尔表达式>
       template body
   </xsl:if>
   ```

   我们用一个例子来帮助理解上述语法的用法，下面列出了样式表Customers.xsl的内容：

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <xsl:stylesheet xmlns:xsl="http://www.w3.org/1999/XSL/Transform" version="1.0">
     <xsl:template match="/">
       <html>
         <body>
           <h1>Customer Listing</h1>
           <table border="1">
             <tr>
               <th>Customer ID</th>
               <th>First Name</th>
               <th>Last Name</th>
               <th>Home Phone</th>
               <th>Notes</th>
             </tr>
             <xsl:for-each select="customers/customer">
               <xsl:if test="firstname[text()='Annie']">
                 <tr>
                   <td>
                     <xsl:value-of select="@customerid"/>  
                   </td>
                   <td>
                     <xsl:value-of select="firstname"/>  
                   </td>
                   <td>
                     <xsl:value-of select="lastname"/>  
                   </td>
                   <td>
                     <xsl:value-of select="homephone"/>  
                   </td>
                   <td>
                     <xsl:value-of select="notes"/>  
                   </td>
                 </tr>
               </xsl:if>
             </xsl:for-each>
           </table>
         </body>
       </html>
     </xsl:template>
   </xsl:stylesheet>
   ```

   在Customers.xml文件中添加如下代码，调用样式表文件Customers.xsl:

   ```xml
   <?xml version="1.0" encoding="utf-8" ?>
   <?xml-stylesheet type="text/xsl" href="Customers.xsl"?>
   <customers>
       ....
   </customers>
   ```

   

5. `<xsl:sort>`

   如果要对结果进行分类，可以在XSL文件里的`<xsl:for-each>`元素中添加一个`<xsl:sort>`元素。select属性告诉你哪些XML元素需要进行分类。下面的代码就是将文档元素按name排序：

   ```xml
   <xsl:template match="PEOPLE">
       <xsl:apply-templates select="PERSON">
           <xsl:sort select="@name"></xsl:sort>
       </xsl:apply-templates>
   </xsl:template>
   ```

   

6. `<xsl:choose>、<xsl:when>、<xsl:otherwise>`

   `<xsl:choose>、<xsl:when>`和`<xsl:otherwise>`一起配合起来表达多种条件语句。我们用一个例子来帮助理解上述语法的用法，下面列出了样式表Customers.xsl的内容：

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <xsl:stylesheet xmlns:xsl="http://www.w3.org/1999/XSL/Transform" version="1.0">
     <xsl:template match="/">
       <html>
         <body>
           <h1>Customer Listing</h1>
           <table border="1">
             <tr>
               <th>Customer ID</th>
               <th>First Name</th>
               <th>Last Name</th>
               <th>Home Phone</th>
               <th>Notes</th>
               <th>Special Word</th>
             </tr>
             <xsl:for-each select="customers/customer">
                 <tr>
                   <td>
                     <xsl:value-of select="@customerid"/>  
                   </td>
                   <td>
                     <xsl:value-of select="firstname"/>  
                   </td>
                   <td>
                     <xsl:value-of select="lastname"/>  
                   </td>
                   <td>
                     <xsl:value-of select="homephone"/>  
                   </td>
                   <td>
                     <xsl:value-of select="notes"/>  
                   </td>
                   <td>
                     <xsl:choose>
                       <xsl:when test="notes[contains(.,'new')]">
                         new (member)
                       </xsl:when> 
                       <xsl:when test="notes[contains(.,'NICE')]">
                         NICE (credity)
                       </xsl:when> 
                       <xsl:when test="notes[contains(.,'VIP')]">
                         VIP (guest)
                       </xsl:when> 
                       <xsl:otherwise>
                         Unknown
                       </xsl:otherwise> 
                     </xsl:choose>
                   </td>
                 </tr>
             </xsl:for-each>
           </table>
         </body>
       </html>
     </xsl:template>
   </xsl:stylesheet>
   ```

   在Customers.xml文件中添加如下代码，调用样式表文件Customers.xsl:

   ```xml
   <?xml version="1.0" encoding="utf-8" ?>
   <?xml-stylesheet type="text/xsl" href="Customers.xsl"?>
   <customers>
       ....
   </customers>
   ```

## 4.2 XslCompiledTransorm类

截至到目前为止，我们都是在设计时将XSLT样式表附到XML文档上。然而，在许多实际情况下，可能需要在应用程序中将他们进行转换。例如，你可能会在运行时生成XML数据，并希望通过使用XSLT进行转换。XslCompiledTransorm类就是为实现这种功能而设计的。XslCompiledTransorm类驻留在System.Xml.Xsl命名空间中，是支持XSLT1.0语法的XSLT处理器，它编译XSLT样式表并执行XSLT转换。

XslCompiledTransorm类接受XML数据并可以将它转换成多种形式。同样，通过XslCompiledTransorm产生的输出可以以不同的形式展示。具体而言，XslCompiledTransorm类能接受的用于XSLT转换的来源可以是如下形式：

- 一个实现IXPathNavigator接口的对象（如，XmlNode或XPathDocument）
- 一个XmlReader
- XML文件的路径或URL

转换后输出可以是下列形式：

- XmlWriter类
- 一个物理磁盘文件
- 一个流（例如，MemeryString或FileStream）
- 从TextWriter抽象类继承的类（例如，StreamWriter或StringWriter）

XslCompiledTransorm类提供了一些列的方法和属性来实现XSLT转换。

| 属性           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| OutputSettings | 获得一个XmlWriterSettings对象，该兑现共包含从样式表的xsl:output元素派生的输出信息 |
| Temporary      | 获取包含临时文件的TempFileCollection，这些临时文件是在成功调用Load方法后在磁盘上生成的 |

| 方法            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| ComplieToType   | 将XSLT样式表编译为指定的类型                                 |
| Equal           | 确定指定Object是否等于当前的Object，已重载                   |
| Finalize        | 允许Object在“垃圾回收”回收Object之前尝试释放资源，并执行其他清理操作 |
| GetHashCode     | 用作特定类型的哈希函数                                       |
| GetType         | 获取档期那实例的类型                                         |
| Load            | 编译样式表，已重载                                           |
| MemberwiseClone | 创建档当前Object的浅表副本                                   |
| RferenceEquals  | 确定指定的Object实例是否是相同的实例                         |
| ToString        | 返回当前Object对象的字符串                                   |
| Transform       | 执行XSLT转换，已重载                                         |



### 4.2.1 使用XslCompiledTransorm类执行转换

```xml
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
string strStyle = "<?xml version=\"1.0\" encoding=\"UTF-8\" ?>"
				+ "	<xsl:stylesheet xmlns:xsl = \"http://www.w3.org/1999/XSL/Transform\" version = \"1.0\" >"
				+ "		<xsl:template match = \"/\" >"
				+ "			<html>"
				+ "				<body>"
				+ "					<h1> Customer Listing </h1>"
				+ "					<table border = \"1\">"
				+ "						<tr>"
				+ "							<th> Customer ID </th>"
				+ "							<th> First Name </th>"
				+ "							<th> Last Name </th>"
				+ "							<th> Home Phone </th>"
				+ "						</tr>"
				+ "						<xsl:for-each select = \"customers/customer\">"
				+ "							<tr>"
				+ "								<td>"
				+ "									<xsl:value-of select = \"@customerid\"/>"
				+ "								</td>"
				+ "								<td>"
				+ "									<xsl:value-of select = \"firstname\"/>"
				+ "								</td>"
				+ "								<td>"
				+ "									<xsl:value-of select = \"lastname\"/>"
				+ "								</td>"
				+ "								<td>"
				+ "									<xsl:value-of select = \"homephone\"/>"
				+ "								</td>"
				+ "							</tr>"
				+ "						</xsl:for-each>"
				+ "					</table>"
				+ "				</body>"
				+ "			</html>"
				+ "		</xsl:template>"
				+ "	</xsl:stylesheet> ";

using (StringReader readerStyle = new StringReader(strStyle))
{
	using (XmlTextReader xmlReaderStyle = new XmlTextReader(readerStyle))
	{
		XslCompiledTransform _xslt = new XslCompiledTransform();
		_xslt.Load(xmlReaderStyle);

		using (StringReader readerSrc = new StringReader(strXml))
		{
			using (XmlTextReader xmlReaderSrc = new XmlTextReader(readerSrc))
			{
				XmlTextWriter writer = new XmlTextWriter(System.Console.Out);
				_xslt.Transform(xmlReaderSrc, writer);
			}
		}
	}
}
```

### 4.2.2 传递参数到XSLT样式表

在很多实际应用的程序项目中，我们常常需要从外部给XSLT样式表传递某些参数以便更加灵活的转换XML文件。例如在前面`xsl:if`的例子中，要展示某个客户的详细信息，我们便在XSLT样式文件中夫定帝写入某个客户的名字，这在实际应用中是非常不方便的，因为我们可能随时需要不同的客户的信息。为了灵活和使用，我们必须要能从XSLT样式表的外部输入客户名字。幸运的是，XSLT允许你在样式表中定义参数变量，这写参数变量能接受外部传入的具体值。在.NET框架中，用XsltArgumentList类即可取得这些参数集合，同时你还可以随时向该参数集合内添加参数变量。

```xml
string strXml = "<?xml version=\"1.0\" encoding=\"utf-8\" ?>"
			  + "<customers>"
			  + "	<customer customerid = \"1\">"
			  + "		<firstname>John</firstname >"
			  + "		<lastname>Cranston</lastname >"
			  + "		<homephone>(445) 269 - 9857</homephone >"
			  + "	</customer>"
			  + "	<customer customerid = \"2\">"
			  + "		<firstname>Annie</firstname >"
			  + "		<lastname>Loskar</lastname >"
			  + "		<homephone>(445) 269 - 9482</homephone >"
			  + "	</customer >"
			  + "</customers > ";
string strStyle = "<?xml version=\"1.0\" encoding=\"UTF-8\" ?>"
				+ "	<xsl:stylesheet xmlns:xsl = \"http://www.w3.org/1999/XSL/Transform\" version = \"1.0\" >"
				+ " <xsl:param name=\"firstname\"/>"
				+ "		<xsl:template match = \"/\" >"
				+ "			<html>"
				+ "				<body>"
				+ "					<h1> Customer Listing </h1>"
				+ "					<table border = \"1\">"
				+ "						<tr>"
				+ "							<th> Customer ID </th>"
				+ "							<th> First Name </th>"
				+ "							<th> Last Name </th>"
				+ "							<th> Home Phone </th>"
				+ "						</tr>"
				+ "						<xsl:for-each select = \"customers/customer\">"
				+ "							<xsl:if test=\"firstname[text()=$firstname]\">"
				+ "								<tr>"
				+ "									<td>"
				+ "										<xsl:value-of select = \"@customerid\"/>"
				+ "									</td>"
				+ "									<td>"
				+ "										<xsl:value-of select = \"firstname\"/>"
				+ "									</td>"
				+ "									<td>"
				+ "										<xsl:value-of select = \"lastname\"/>"
				+ "									</td>"
				+ "									<td>"
				+ "										<xsl:value-of select = \"homephone\"/>"
				+ "									</td>"
				+ "								</tr>"
				+ "							</xsl:if>"
				+ "						</xsl:for-each>"
				+ "					</table>"
				+ "				</body>"
				+ "			</html>"
				+ "		</xsl:template>"
				+ "	</xsl:stylesheet>";

using (StringReader readerStyle = new StringReader(strStyle))
{
	using (XmlTextReader xmlReaderStyle = new XmlTextReader(readerStyle))
	{
		XslCompiledTransform _xslt = new XslCompiledTransform();
		_xslt.Load(xmlReaderStyle);

		XsltArgumentList _arguments = new XsltArgumentList();
		_arguments.AddParam("firstname", "", "John");

		using (StringReader readerSrc = new StringReader(strXml))
		{
			using (XmlTextReader xmlReaderSrc = new XmlTextReader(readerSrc))
			{
				XmlTextWriter writer = new XmlTextWriter(System.Console.Out);
				_xslt.Transform(xmlReaderSrc, _arguments, writer);
			}
		}
	}
}
```

### 4.2.3 应用扩展对象

在前面的所有实例中，我们都是原封不动地把XML文档中节点的内容，以不同的形式表现出来。无论是增加还是减少显示的内容，都是XML文档的原有内容，但是有时，我们需要展示的内容，则是XML文档中没有的，那么是如何实现的？我们的解决方法之一就是扩展对象。

现在我们希望将书的价格提高1.5倍以上，并显示粗来，同时也要显示原价格。

```c#
void Main()
{
	string strXml = "<bookstore>"
	              + "	<book genre=\"autobiography\" publicationdate=\"1981\" ISBN=\"1-861003-11-0\">"
	              + "		<title>The Autobiography of Benjamin Franklin</title>"
	              + "		<author>"
	              + "			<first-name>Benjamin</first-name>"
	              + "			<last-name>Franklin</last-name>"
	              + "		</author>"
	              + "		<price>8.99</price>"
	              + "	</book>"
	              + "	<book genre=\"novel\" publicationdate=\"1967\" ISBN=\"0-201-63361-2\">"
	              + " 		<title>The Confidence Man</title>"
	              + " 		<author>"
	              + "  			<first-name>Herman</first-name>"
	              + " 			<last-name>Melville</last-name>"
	              + "		</author>"
	              + " 		<price>11.99</price>"
	              + " 	</book>"
	              + "</bookstore>";
	string strStyle = "<xsl:stylesheet version=\"1.0\" xmlns:xsl=\"http://www.w3.org/1999/XSL/Transform\" xmlns:myObj=\"urn:price-conv\">"
	                + "<!--currency conversion factor-->"
					+ "<xsl:param name=\"conv\" select=\"1.537\"/>"
	  				+ "<xsl:template match=\"bookstore\">"
	  				+ "	<bookstore>"
	  				+ "		<html>"
	  				+ "			<body>"
	  				+ "				<table border=\"1\">"
	  				+ "					<tr>"
	    			+ "						<th>Title</th>"
	    			+ "						<th>Author</th>"
	    			+ "						<th>Price</th>"
	   				+ "						<th>New Price</th>"
	 	 			+ "					</tr> "
	  				+ "					<xsl:for-each select=\"book\">"
	    			+ "					<book>"
	    			+ "						<tr>"
	     			+ " 						<td>"
	     			+ "   							<xsl:value-of select=\"title\"/>"
	     			+ " 						</td>"
	     			+ " 						<td>"
	      			+ "  							<xsl:value-of select=\"author/first-name\"/>"
	      			+ "  							<xsl:value-of select=\"author/last-name\"/>"
	      			+ "							</td>"
	      			+ "							<td>"
	      			+ "  							<xsl:value-of select=\"price\"/>"
	      			+ "							</td>"
	    			+ "							<td>"
	    			+ "								<!-- <xsl:copy-of select=\"node()\"/> -->"
	      			+ "								<conv-price>"
	       			+ "   								<xsl:value-of select=\"myObj:NewPriceFunc(./price, $conv)\"/>  "      
	       			+ "								</conv-price>"
	    			+ "							</td>"
	    			+ "						</tr>"
	   				+ " 				</book>"
	  				+ "					</xsl:for-each>"
	  				+ "				</table>"
	  				+ "			</body>"
	  				+ "		</html>"
	 				+ " </bookstore>"
	  				+ "	</xsl:template>"
					+ "</xsl:stylesheet>";
	
	using (StringReader readerStyle = new StringReader(strStyle))
	{
		using (XmlTextReader xmlReaderStyle = new XmlTextReader(readerStyle))
		{
			XsltSettings _settings = new XsltSettings();
			_settings.EnableScript = true;
			
			XslCompiledTransform _xslt = new XslCompiledTransform();
			_xslt.Load(xmlReaderStyle, _settings, null);
	
			XsltArgumentList _arguments = new XsltArgumentList();
			BookPrice _obj = new BookPrice();
			_arguments.AddExtensionObject("urn:price-conv", _obj);

			using (StringReader readerSrc = new StringReader(strXml))
			{
				using (XmlTextReader xmlReaderSrc = new XmlTextReader(readerSrc))
				{
					XmlTextWriter writer = new XmlTextWriter(System.Console.Out);
					_xslt.Transform(xmlReaderSrc, _arguments, writer);
				}
			}
		}
	}
}

public class BookPrice
{
	private decimal newprice = 0;

	public decimal NewPriceFunc(decimal price, decimal conv)
	{
		decimal tmp = price * conv;
		newprice = decimal.Round(tmp, 2);
		return newprice;
	}
}
```






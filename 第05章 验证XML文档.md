# 第05章 验证XML文档

我们应用XML的主要目的是为了存储和交换数据，但当一个应用程序中的XML文档数据来自不同的源时，保证所有的XML文档都坚持一个议定的XML结构（标记名称，嵌套，等）是非常重要的，那么如何保证每个提价哦的XML文档都坚持相同的XML结构呢？这就需要验证XML文档。验证XML文档主要有两种方法：DTD模式和XML Schema模式。本章重点简述XML Schema。XML Schema是负责定义和描述XML文档的结构和内容该模式。它可以定义XML文档中存在哪些元素和元素之间的关系，兵器额可以定义元素和属性的数据类型。用XML Schema验证XML文档，你要的两件重要的事：一是创建基于XML Schema架构的XML文档，二是根据XML Schema架构验证XML文档。这一章中，你将链接以下内容：

- 不同的方法来定义一个XML文档结构
- 什么是XML Schema
- XML模式
- 如何创建XML文档结构
- 如何验证XML文档
- 如何通过编程方式创建模式架构对象模型（SOM）

## 5.1 XML Schema

XML Schema如同DTD一样是负责定义和描述XML文档的结构和内容模式。它可以定义XML文档中存在哪些元素和元素之间的广西，并且可以定义元素和属性的数据类型。XML Schema本身就是一个XML文档，它符合XML语法结构。

- DTD缺陷

  - 基于正则表达式的，描述能力有限
  - 没有数据类型的支持，在大多数应用环境下能力不足
  - 约束定义能力不足，无法对XML实例文档做出更细致的语义限制
  - 结构不够结构化，重用的代价相对较高
  - 并非使用XML作为描述手段，构建和访问并没有标准的编程结构，无法使用标准的编程方式进行维护

- XML Schema优点：

  - 基于XML，没有专门的语法
  - 可以像其他XML文件一样解析和处理
  - 支持一些列数据类型
  - 提供可扩充的数据模型
  - 支持综合命名空间
  - 支持属性组

  一个简单的XML Schema文档：

  ```xml
  <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <xs:element name="quantity" type="xs:nonNegativeInteger">
      </xs:element>
  </xs:schema>
  ```

  在这个Schema里定义一个元素：quantity，它的类型是nonNegativeInteger。

  下面的XML是合法的片段：

  ```xml
  <quantity>5</quantity>
  ```

  下面的XML是非法的片段：

  ```xml
  <quantity>-5</quantity>
  ```

### 5.1.1 XSD

  XSD文档至少要包含：schema根元素和XML模式命名空间的定义、元素定义。

  1. schema根元素
  
     语法如下：
  
     ```xml
     <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
         ...
     </xs:schema>
     ```
  
     在XSD中必须定义一个且只能定义一个schema根元素。根元素中包括模式的约束、XML模式命名空间的定义，其他命名空间的定义、版本信息、语言信息和其他一些属性。
  
  2. 元素
  
     语法如下：
  
     ```xml
     <xs:element name="user" type="xs:string">
     </xs:element>
     ```
  
     XSD中的元素是利用element标识符来声明的。其中name属性是元素的名字，type属性是元素值的类型，在这里可以是XML Schema中内置的数据类型或其他类型。
  
     例子：
  
     ```xml
     <xsd:schema xmlna:xsd="http://www.w3.org/2001/XMLSchema">
         <xs:element name="user" type="xs:string">
     	</xs:element>
     </xsd:schema>
     ```
  
     以上闻到那给对应的有效XML文档如下：
  
     ```xml
     <?xml version="1.0"?>
     <user>string</user>
     ```
  
     在元素的定义中还有两个属性：minOccurs和maxOccurs。其中minOccurs定义了该元素在父元素中出现的最少次数（默认为1，值为大于等于0的整数），maxOccurs定义了该元素在父元素中出现的最多次数（默认为1，值为大于等于0的整数）。在maxOccurs中可以把值设置为unbounded，表示对元素出现的次数没有限制。
  
     例子：
  
     ```xml
     <xsd:schema xmlna:xsd="http://www.w3.org/2001/XMLSchema">
         <xs:element name="user" type="xs:string" minOccurs="0" maxOccurs="unbounded">
     	</xs:element>
     </xsd:schema>
     ```
  
     表示为元素user的类型为string，出现的次数最少为0，最多不限制。
  
  3. 引用元素和替代
  
     ```xml
     <xsd:schema xmlna:xsd="http://www.w3.org/2001/XMLSchema">
         <xsd:element name="user" type="xs:string"/>
         <xsd:element name="name">
             <xsd:complexType>
                 <xsd:sequence>
                     <xsd:element ref="user"/>
                 </xsd:sequence>
             </xsd:complexType>
         </xsd:element>
     </xsd:schema>
     ```
  
     引用时利用element标记符的ref属性实现的。主要适用于避免在文档中多次定义同一个元素，应当将经常使用的元素定义为根元素的子元素，以便在文档的任何地方引用它。
  
     在这里还可以为某个定义的元素起一个别名，方法如下：
  
     ```xml
     <xsd:schema xmlna:xsd="http://www.w3.org/2001/XMLSchema">
         <xsd:element name="yonghu" 
                      type="xs:string" substitutionGroup="user"/>
         <xsd:element name="user" type="xsd:string"/>
         <xsd:element name="name">
             <xsd:complexType>
                 <xsd:sequence>
                     <xsd:element ref="user"/>
                 </xsd:sequence>
             </xsd:complexType>
         </xsd:element>
     </xsd:schema>
     ```
  
     以上文档对应的有效XML文档如下：
  
     ```xml
     <?xml version="1.0"?>
     <name>
         <user>string</user>
     </name>
     ```
  
     或者
  
     ```xml
     <?xml version="1.0"?>
     <name>
         <yonghu>string</yonghu>
     </name>
     ```
  
     主要是利用element标识符中的属性substitutionGroup实现的。
  
  4. 设置默认值和固定值
  
     语法如下：
  
     ```xml
     <xsd:element name="city" type="xsd:string" default="xian"/>
     <xsd:element name="country" type="xsd:string" fixed="china"/>
     ```
  
     通过default属性的设置，可以在XML文档中没有对city元素定义时赋予默认值。而使用fixed属性，可以给元素country设定一个固定的值china，并且不允许改变。
  
  5. 利用组合器控制结构
  
     - sequence组合器，定义了一列元素必须按照模式中指定的顺序显示（如果时可选的，也可以不显示）。语法如下：
     
       ```xml
       <xsd:schema xmlna:xsd="http://www.w3.org/2001/XMLSchema">
           <xsd:element name="name">
               <xsd:complexType>
                   <xsd:sequence>
                       <xsd:element name="first" type="xsd:string"/>
                       <xsd:element name="middle" type="xsd:string"/>
                       <xsd:element name="last" type="xsd:string"/>
                   </xsd:sequence>
               </xsd:complexType>
           </xsd:element>
       </xsd:schema>
       ```
     
       
     
     - all组合器，允许所定义的元素可以按照任意顺序显示，all元素的子元素在默认情况下是必须的，而且每次最多显示一次。语法如下：
     
       ```xml
       <xsd:schema xmlna:xsd="http://www.w3.org/2001/XMLSchema">
           <xsd:element name="name">
               <xsd:complexType>
                   <xsd:all minOccurs=0>
                       <xsd:element name="first" type="xsd:string"/>
                       <xsd:element name="middle" type="xsd:string"/>
                       <xsd:element name="last" type="xsd:string"/>
                   </xsd:all>
               </xsd:complexType>
           </xsd:element>
       </xsd:schema>
       ```
     
       
     
     - choice组合器，允许指定多组申明中的一个，用于互斥情况。语法如下：
     
       ```xml
       <xsd:schema xmlna:xsd="http://www.w3.org/2001/XMLSchema">
           <xsd:element name="name">
               <xsd:complexType>
                   <xsd:choice>
                       <xsd:element name="first" type="xsd:string"/>
                       <xsd:element name="middle" type="xsd:string"/>
                       <xsd:element name="last" type="xsd:string"/>
                   </xsd:choice>
               </xsd:complexType>
           </xsd:element>
       </xsd:schema>
       ```
  
  6. 定义属性
  
     在XML Schema文档中可以按照定义元素的方法定义属性，但受限制的程序较高。他们只能是见到那类型，只能包含文本，且没有子属性。可以应用在attribute元素定义中的属性如下：
  
     | 属性    | 含义                             |
     | ------- | -------------------------------- |
     | default | 初始默认值                       |
     | fixed   | 不能修改和覆盖属性的固定值       |
     | name    | 属性名称                         |
     | ref     | 对前一个属性定义的引用           |
     | type    | 该属性的XSD类型或者简单类型      |
     | use     | 如何使用属性                     |
     | form    | 确定attributeFromDefault的本地值 |
     | id      | 模式文档中属性唯一的ID           |
  
     default，fixed，name，ref和type属性与在element标记中定义的对应属性相同，但type只能是简单类型。use属性的值可以是：optional（属性不是必须的，此为默认属性），prohibited或者required（属性是强制的）。
  
  7. 创建属性
  
     语法如下：
  
     ```xml
     <xsd:attribute name="age" type="xsd:integer"/>
     ```
  
     该语句定义了一个名为age的属性，它的值必须是一个整数。把它添加到模式时，它必须时schema元素、complexType元素或者attributeGroup元素的子元素。
  
     例子：
  
     ```xml
     <xsd:schema xmlna:xsd="http://www.w3.org/2001/XMLSchema">
         <xsd:element name="name">
             <xsd:complexType>
                 <xsd:sequence>
                     <xsd:element name="first" type="xsd:string"/>
                 </xsd:sequence>
                 <xsd:attribute name="age" type="xsd:integer" use="opetional"/>
             </xsd:complexType>
         </xsd:element>
     </xsd:schema>
     ```
  
     以上文档对应有效的XML文档如下：
  
     ```xml
     <?xml version="1.0"?>
     <name age="27">
         <first>string</first>
     </name>
     ```
  
     如上所示，要把属性附加在元素上，属性应该在complexType定义中的组合器之后定义或引用。

### 5.1.2 XML Schema 数据类型

XML Schema 提供了一组丰富的内置数据类型，用于定义元素中允许的类型。

1. 基本数据类型

   | 基本数据类型       | 含义                      |
   | ------------------ | ------------------------- |
   | boolean            | 是1(true)或者0（false）   |
   | dateTime           | 格式：CCYY-MM-DDThh:mm:ss |
   | decimal            | 任意十精度的数字          |
   | string             | 字符数据                  |
   | int                | 整数                      |
   | nonNegativeInteger | 非负整数                  |
   | nonPositiveInteger | 非正整数                  |
   | short              | 短整型                    |

   

2. 约束方面

   | 约束          | 含义                                                         |
   | ------------- | ------------------------------------------------------------ |
   | enumeration   | 用空格分隔开的一组指定的数值，它把数据类型约束为指定的值     |
   | fractionDigit | 指定小数点后的最大位数                                       |
   | length        | 长度单位                                                     |
   | minExclusive  | 下限值，所有的值都必须大于该值                               |
   | maxExclusive  | 上限值，所有的值都必须小于该值                               |
   | minLength     | 最小长度单位                                                 |
   | maxLength     | 最大长度单位                                                 |
   | minInclusive  | 最小值，所有值都因该大于或等于该值                           |
   | maxInclusive  | 最大值，所有值都因该小于或等于该值                           |
   | pattern       | 数据类型的值必须匹配的指定模式，必须是一个正则表达式         |
   | totalDigits   | 指定小数最大位数的值                                         |
   | whiteSpace    | 其值为：preserve(值中的空格不能改变)，replace(所有的制表符、换行符和回车符都用空格代替)，collapse(执行replace，删除相邻的、结尾处和开头处的空格) |

   要应用上述的约束，就要利用元素restriction。这个元素中有两个属性：ID属性是模式文档中restriction元素的唯一标识符；base属性设置为一个内置的XSD数据类型或者现有的简单类型定义，它是一种被限制的类型。例如，将一个整数的取值范围设置为1~100之间。

   ```xml
   <xsd:restriction base="xsd:int">
       <xsd:minInclusive value="1"/>
       <xsd:maxInclusive value="100"/>
   </xsd:restriction>
   ```

   

3. 简单类型

   简单类型是对一个节点的可能值进一步限制的自定义数据类型。创建简单类型需要利用simpleType元素，其定义如下：

   ```xml
   <simpleType id="ID" name="NCName" final="(#all|(list|uinon|restriction))"/>
   ```

   ID属性应唯一地标明文档内的simpleType元素，name不能使用冒号字符。

   simpleType不能包含元素，也不能有属性，它基本上是一个值，或者是一个值的集合。例如：

   ```xml
   <xsd:simpleType name="USState">
       <xsd:restriction base="xsd:string">
           <xsd:enumeration value="AK"/>
           <xsd:enumeration value="AL"/>
           <xsd:enumeration value="AR"/>
           <!--and so on-->
       </xsd:restriction>
   </xsd:simpleType>
   <xsd:element name="statename" type="USState"/>
   ```

   这是一个用来描述美国州名的类型USState，通过enumeration来列出所有州名，取值时就只能取李里面列出的州名。

   以上文档对应有效的XML文档如下：

   ```xml
   <statename>AK</statename>
   ```

   以下就是无效的XML文档：

   ```xml
   <statename>Alaska</statename>
   ```

   

4. 列表类型

   list可以用来定义列表类型。例如：

   ```xml
   <xsd:simpleType name="listOfIntType">
       <xsd:list itemType="Integer"/>
   </xsd:simpleType>
   <xsd:element name="listOfMyInt" type="listofIntType"/>
   ```

   listOfIntType这个类型被定义为一个Integer的列表，元素listOfMyInt的值可以时几个整数骂他们之间用空格隔开。有效的XML文档如下：

   ```xml
   <listOfMyInt>1 5 15037 95977 95945</listOfMyInt>
   ```

   无效的XML文档：

   ```xml
   <listOfMyInt>1 3 abc</listOfMyInt>
   ```

   

5. 联合类型

   union可以用来定义一个联合类型。例如：

   ```xml
   <xsd:simpleType name="zipUnion">
       <xsd:union memberTypes="USState listOfMyIntType"/>
   </xsd:simpleType>
   <xsd:element name="zips" type="zipUnion"/>
   ```

   用union来定义一个联合类型，里面的成员类型包括USState和listOfMyIntType，应用联合类型的元素的值可以时这些原子类型或列表类型中的一个类型中的一个类型的实例，但是一个元素实例不能同时包含两个类型。有效的XML文档如下：

   ```xml
   <zips>CA</zips>
   <zips>95630 95977 95945</zips>
   <zips>AK</zips>
   ```

   无效的XML文档：

   ```xml
   <zips>CA 95630</zips>
   ```

   前面我们在定义元素类型时，总是先定义一个数据类型，然后再把元素的type设成新定义的数据类型。如果这个新的数据类型只会使用一次，我们就可以直接设置在元素定义里面，而不用另外来设置。例如：

   ```xml
   <xsd:element name="quantity">
       <xsd:simpleType>
           <xsd:restriction base="xsd:positveInteger">
               <xsd:maxExclusive value="100"></xsd:maxExclusive>
           </xsd:restriction>
       </xsd:simpleType>
   </xsd:element>
   ```

   元素quantity的类型就是一个1~99的整数。这种新的类型，没有自己的名字的定义方法，我们称之为匿名类型定义。

6. 复杂类型

   复杂数据类型的定义必须使用complexType元素，在这里可以包含属性和元素。在复杂类型的使用中，主要时complexType和simpleType配合使用。格式如下：

   ```xml
   <xsd:compleType name="name"/>
   ```

   例如：

   ```xml
   <xsd:element name="name" type="FullName"></xsd:element>
   <xsd:complexType name="FullName">
       <xsd:sequence>
           <xsd:element name="first" type="PersonsFirstName" minOccurs="0" maxOccurs="1" default="John"/>
           <xsd:element name="middle" type="xsd:string" minOccurs="0" maxOccurs="unbounded" nillable="true"/>
           <xsd:element name="last" type="xsd:string" minOccurs="1" maxOccurs="1" default="Doe"/>
       </xsd:sequence>
       <xsd:attribute name="title" type="PersonsTitle" default="Mr."/>
   </xsd:complexType>
   <xsd:simpleType name="PersonsFirstName">
       <xsd:restriction base="xsd:string">
           <xsd:minLength value="4"/>
           <xsd:maxLength value="10"/>
       </xsd:restriction>
   </xsd:simpleType>
   <xsd:simpleType name="PersonsTitle">
       <xsd:restriction base="xsd:string">
           <xsd:enumeration value="Mr."/>
           <xsd:enumeration value="Mrs."/>
           <xsd:enumeration value="Miss."/>
       </xsd:restriction>
   </xsd:simpleType>
   ```

   如上就实现了一个复杂类型，该例子实现了一个复杂类型FullName，其中包含两个简单类型PersonsFirstName和PersonTitle。

7. 内容模型

   内容模型可以对在XML文档内使用的元素、属性和累心感觉女性限制，确定给用户可以在XML实例的哪些等级添加自己的元素和属性。

   - any内容模型

     在XML中声明元素时，any是模型的内容模型，该模型可以包含文本、元素和空格。例如：

     ```xml
     <xsd:schame xmlns:xsd="http://www.w3.org/2001/XMLSchema">
         <xsd:element name="name">
             <xsd:complexType>
                 <xsd:sequence>
                     <xsd:element name="first" type="xsd:string"/>
                     <xsd:element name="middle" type="OtherNames"/>
                     <xsd:element name="last" type="xsd:string"/>
                 </xsd:sequence>
             </xsd:complexType>
         </xsd:element>
         <xsd:complexType name="OtherNames">
             <xsd:sequence>
                 <xsd:any namespace="##any" processContents="lax" minOccurs="0" maxOcurrs="unbounded"/>
             </xsd:sequence>
         </xsd:complexType>
     </xsd:schame>
     ```

     例子中的xsd:any元素说明该类型允许添加内容。namespace属性允许的值为：

     - ##any:元素可以来自任何命名空间
     - ##other：元素可以来自除了该元素的父元素所在的目标命名空间之外的命名空间
     - ##local：元素不受命名空间的限制
     - ##targetNamespace：元素来自父元素的目标命名空间

     processContents属性说民归队这里所创建的元素进行验证时所执行的操作，取值有如下三种：

     - strict：表明XML处理器必须获得和哪些命名空间相关联的模式，并验证元素和属性
     - lax：与strict相似，只是如果处理器找不到模式文档，也不会出现错误
     - skip：不利用模式文档验证XML文档

     上述模式的一个有效实例如下：

     ```xml
     <?xml version="1.0"?>
     <name>
         <first> santld </first>
         <middle>
             <nameInChina>San</nameInChina>
         </middle>
         <last>wang</last>
     </name>
     ```

     

   - 空(empty)内容模型

     有的时候元素根本没有内容，它的内容模型时空。为了定义内容是空的累心给，我们可以通过这样的方式：首先定义一个元素，它只能抱哈女子元素而不能包含元素内容，然后又不定义任何子元素，依靠这样的方式，就能够定义处内容该模型为空的元素。例如：

     ```xml
     <xsd:element name="internationalPrice">
         <xsd:complexType>
             <xsd:complexContent>
                 <xsd:restriction base="xsd:anyType">
                     <xsd:attribute name="currency" type="xsd:string"/>
                     <xsd:attribute name="value" type="xsd:decimal"/>
                 </xsd:restriction>
             </xsd:complexContent>
         </xsd:complexType>
     </xsd:element>
     ```

     complexContent表示只包含子元素，然后我们定义了两个属性currency和value，但是却不定义任何子元素。

     有效的XML文档如下：

     ```xml
     <internationalPrice currentcy="EUR" value="/423.46"></internationalPrice>
     ```

     无效的XML文档如下：

     ```xml
     <internationalPrice currentcy="EUR" value="/423.46">
         Here is a mistack!
     </internationalPrice>
     ```

     

   - 混合(mixed)内容模型

     它包含文本、内容和属性。在complexType元素上把mixed属性的只设为true，就声明了一个mixed内容模型。例如：

     ```xml
     <xsd:schame xmlns:xsd="http://www.w3.org/2001/XMLSchema">
         <xsd:element name="contact">
             <xsd:complexType mixed="true">
                 <xsd:sequence>
                     <xsd:element name="first" type="xsd:string"/>
                 </xsd:sequence>
             </xsd:complexType>
         </xsd:element>
     </xsd:schame>
     ```

     上述模式的一个有效实例如下：

     ```xml
     <?xml version="1.0"?>
     <contact>My first name is <first> santld </first>.</contact>
     ```

## 5.2 定义XML文档的结构

XML Schema提供了一个模板用户创建和验证XML文档，然而它不是唯一的XML文档踢狗结构的方式。.NET框架支持三种定义XML结构的方法：

- 文档类型定义（DTDS-Document Type Definitions）
- XML数据简化（XDR-XML Data Reduced）
- XML结构定义语言（XSD-XML Schema Definition Language）

在这三种方式中，XDR与XSD具有非常相似的标准，并且XSD支持XDR，所以本章不讨论XDR的相关技术。我们将通过Customers.xml文档创建结构文件来讲解相关DTD和XSD技术。首先，让我们来分析Customers.xml文档的结构：

- 根元素必须是&lt;customers&gt;
- 根元素可以包含零个或多个&lt;customer&gt;元素
- 每个&lt;customer&gt;元素必须具有一个名为 customer id的属性，并且必须包含&lt;firstname&gt;、&lt;lastname&gt;、&lt;homephone&gt;和&lt;notes&gt;子元素，同时每个customer元素的这四个子元素必须具有相同的顺序
- &lt;firstname&gt;、&lt;lastname&gt;、&lt;homephone&gt;和&lt;notes&gt;子元素都包含需被解析的XML数据

### 5.2.1 创建DTD文件

DTD可以是一个完全独立的文件（外部DTD），也可以在XML文件中直接设定（内部DTD）。在此，我们将Customers.xml的DTD文件作为一个外部的DTD文件Customers.tdt，文件内容如下：

```xml-dtd
<?xml version="1.0" encoding="utf-8"?>
<!ELEMENT customers (customer*)>
<!ELEMENT customer (firstname,lastname,homephone,notes)>
<!ELEMENT firstname (#PCDATA)>
<!ELEMENT lastname (#PCDATA)>
<!ELEMENT homephone (#PCDATA)>
<!ELEMENT notes (#PCDATA)>
<!ATTLIST customer customerid CDATA #REQUIRED>
```

首先DTD通过标记&lt;!ELEMENT&gt;定义了XML文档的根元素&customers&gt;，由于&customers&gt;根元素包含零个或多个&lt;customer&gt;元素并且不包含其他的元素或文本，因而在括号内标记处根元素可以包含的子元素名customer，*表示&customers&gt;元素可以有零个或多个&customers&gt;子元素。类似的，在此你可以使用+，？或不使用符号，+表示元素必须出现一次以上，？表示可以不出现或只出现一次，无符号表示只能出现一次。其次，DTD通过标记&lt;!ELEMENT&gt;定义了根元素，由于&lt;firstname&gt;、&lt;lastname&gt;、&lt;homephone&gt;和&lt;notes&gt;四个子元素，所以在括号内将所有元素名列出，需要注意的是在列表中的子元素当出现在XML文档时，必须遵循在列表中的前后顺序。如果你希望列表中的子元素能够以任意的前后顺序出现，那么你就要使用管道符号|代替逗号，然后文档定义了&lt;customer&gt;元素的每个子元素，由于每个子元素仅包含需被解析XML数据内容，而不包含其他的子元素，因为在括号内写入#PCDATA（Plane Character Data）标记，这样DTD就是的子元素只能包含需被解析的XML数据而不能带有标记或子元素，如果在此使用ANY代替#PCDATA，则子元素可以带有任何内容。如果子元素为空，则使用EMPTY关键字。最后DTD应用了`<!ATTLIST>`标记定义<customer>元素的属性customerid，声明语句中的CDATA表示是纯文本的字符数据，#REQUIRED表示这个属性是强制的，必须出现在XML文档中。现在我们要在XML文档中引用Customers.dtd文件，代码如下：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!--引用DTD结构文件-->
<!DOCTYPE customers SYSTEM "Customers.dtd"> 
<customers>
  <customer customerid="1">
    <firstname>John</firstname>
    <lastname>Cranston</lastname>
    <homephone>(445) 269-9857</homephone>
    <notes>
      <![CDATA[He registed as our member since 1990. John has nice credity. He is a member of Custom International.]]>
  </notes>
  </customer>
  <customer customerid="2">
    <firstname>Annie</firstname>
    <lastname>Loskar</lastname>
    <homephone>(445) 269-9482</homephone>
    <notes>
      <![CDATA[Annie registed as our member since 1984. He became our VIP customer in 1996.]]>
    </notes>
  </customer>
  <customer customerid="3">
    <firstname>Bernie</firstname>
    <lastname>Christo</lastname>
    <homephone>(445) 269-3412</homephone>
    <notes>
      <![CDATA[Bernie registed as our member since June 2010. He is a new member.]]>
    </notes>
  </customer>
<customer customerid="4">
    <firstname>Ernestine</firstname>
    <lastname>Borrison</lastname>
    <homephone>(445) 269-7742</homephone>
    <notes>
      <![CDATA[Ernestine registed as our member since Junl 2010. She is a new member.]]>
    </notes>
  </customer>
<customer customerid="5">
    <firstname>Ernestine</firstname>
    <lastname>Borrison</lastname>
    <notes>
      <![CDATA[Ernestine registed as our member since Junl 2010. She is a new member.]]>
    </notes>
    <homephone>(445) 269-7742</homephone>
  </customer>

</customers>

 
```

### 5.2.2 创建XSD Schema

1. 创建XSD Schema

   在创建XSD Schema之前，你首先必须考虑结构文件中的简单类型、复杂类型、元素和属性等。我们仍以Customers.xml创建结构文件为例。经过对Cutomers.xml的分析，我们将狗仔三个简答类型：NameSimpleType代表XML文档中用到的<firstname>和<lastname>，它添加了两个显示条件，即minLength的值为3，maxLength的值必须小于255,PhoneSimpleType代表XML文档中用到的<notes>，它的限制条件为输入的字符不能大于500.由这三个简单类型构成可一个复杂类型CustomerType，该复杂类型由以下几个元素组成：<firstname>和<lastname>元素，它们的数据类型是简单类型NameSimpleType，数据类型为简单类型PhoneSimpleType的元素<honephone>，数据类型为简单类型NoteSimpleType的元素<notes>和数据类型为int的属性customerid.

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <xs:schema attributeFormDefault="unqualified" elementFormDefault="qualified" xmlns:xs="http://www.w3.org/2001/XMLSchema">
     <xs:simpleType name="NotesSimpleType">
       <xs:restriction base="xs:string">
         <xs:maxLength value="500" />
       </xs:restriction>
     </xs:simpleType>
     <xs:simpleType name="NameSimpleType">
       <xs:restriction base="xs:string">
         <xs:minLength value="3" />
         <xs:maxLength value="255" />
       </xs:restriction>
     </xs:simpleType>
     <xs:simpleType name="PhoneSimpleType">
       <xs:restriction base="xs:string">
         <xs:maxLength value="25" />
       </xs:restriction>
     </xs:simpleType>
     <xs:complexType name="CustomerType">
       <xs:all>
         <xs:element name="firstname" type="NameSimpleType">
         </xs:element>
         <xs:element name="lastname" type="NameSimpleType">
         </xs:element>
         <xs:element name="notes" type="NotesSimpleType">
         </xs:element>
         <xs:element name="homephone" type="PhoneSimpleType">
         </xs:element>
       </xs:all>
       <xs:attribute name="customerid" type="xs:int" use="required" />
     </xs:complexType>
     <xs:element name="customers">
       <xs:complexType>
         <xs:sequence>
           <xs:element name="customer" type="CustomerType" minOccurs="0" maxOccurs="unbounded">
           </xs:element>
         </xs:sequence>
       </xs:complexType>
     </xs:element>
   </xs:schema>
   ```

   

2. 用Visual Studio设计器创建XSD Schema

   - 新建Schema文件
   - 使用工具箱创建数据类型

3. 利用模式对象模型创建模式

   ```c#
   if (txtfile.Text == "")
   {
       MessageBox.Show("Please enter file name");
       return;
   }
   if (!Directory.Exists(Path.GetDirectoryName(txtfile.Text)))
   {
       MessageBox.Show("Invalid destination directory");
       return;
   }
   //create the schema
   XmlSchema _schema = new XmlSchema();
   
   //define simpletype--NameSimpleType
   XmlSchemaSimpleType _nametype = new XmlSchemaSimpleType();
   XmlSchemaSimpleTypeRestriction _nameRes = new XmlSchemaSimpleTypeRestriction();
   _nameRes.BaseTypeName = new XmlQualifiedName("string", "http://www.w3.org/2001/XMLSchema");
   XmlSchemaMinLengthFacet _nameFacet1 = new XmlSchemaMinLengthFacet();
   _nameFacet1.Value = "3";
   XmlSchemaMaxLengthFacet _nameFacet2 = new XmlSchemaMaxLengthFacet();
   _nameFacet2.Value = "255";
   _nameRes.Facets.Add(_nameFacet1);
   _nameRes.Facets.Add(_nameFacet2);
   _nametype.Content = _nameRes;
   
   //define simpletype--PhoneSimpleType
   XmlSchemaSimpleType _phonetype = new XmlSchemaSimpleType();
   XmlSchemaSimpleTypeRestriction _phoneRes = new XmlSchemaSimpleTypeRestriction();
   _phoneRes.BaseTypeName = new XmlQualifiedName("string", "http://www.w3.org/2001/XMLSchema");
   XmlSchemaMaxLengthFacet _phoneFacet1 = new XmlSchemaMaxLengthFacet();
   _phoneFacet1.Value = "25";
   _phoneRes.Facets.Add(_phoneFacet1);
   _phonetype.Content = _phoneRes;
   
   //define simpletype--NotesSimpleType
   XmlSchemaSimpleType _notestype = new XmlSchemaSimpleType();
   XmlSchemaSimpleTypeRestriction _notesRes = new XmlSchemaSimpleTypeRestriction();
   _notesRes.BaseTypeName = new XmlQualifiedName("string", "http://www.w3.org/2001/XMLSchema");
   XmlSchemaMaxLengthFacet _notesFacet1 = new XmlSchemaMaxLengthFacet();
   _notesFacet1.Value = "500";
   _notesRes.Facets.Add(_notesFacet1);
   _notestype.Content = _notesRes;
   
   //define CustomerType complex type
   
   XmlSchemaComplexType _customertype = new XmlSchemaComplexType();
   XmlSchemaSequence _sequence = new XmlSchemaSequence();
   XmlSchemaElement _firstname = new XmlSchemaElement();
   _firstname.Name = "firstname";
   _firstname.SchemaType = _nametype;
   XmlSchemaElement _lastname = new XmlSchemaElement();
   _lastname.Name = "lastname";
   _lastname.SchemaType = _nametype;
   XmlSchemaElement _homephone = new XmlSchemaElement();
   _homephone.Name = "homephone";
   _homephone.SchemaType = _phonetype;
   XmlSchemaElement _notes = new XmlSchemaElement();
   _notes.Name = "notes";
   _notes.SchemaType = _notestype;
   
   _sequence.Items.Add(_firstname);
   _sequence.Items.Add(_lastname);
   _sequence.Items.Add(_homephone);
   _sequence.Items.Add(_notes);
   
   _customertype.Particle = _sequence;
   
   //define customerid attribute
   
   XmlSchemaAttribute _customerid = new XmlSchemaAttribute();
   _customerid.Name = "customerid";
   _customerid.SchemaTypeName = new XmlQualifiedName("int", "http://www.w3.org/2001/XMLSchema");
   _customerid.Use = XmlSchemaUse.Required;
   
   _customertype.Attributes.Add(_customerid);
   
   //define top complex type
   
   XmlSchemaComplexType _complextype = new XmlSchemaComplexType();
   XmlSchemaSequence _sq = new XmlSchemaSequence();
   XmlSchemaElement _customer = new XmlSchemaElement();
   _customer.Name = "employee";
   _customer.SchemaType = _customertype;
   _customer.MinOccurs = 0;
   _customer.MaxOccursString = "unbounded";
   _sq.Items.Add(_customer);
   _complextype.Particle = _sq;
   
   //define <employees> element
   
   XmlSchemaElement _customers = new XmlSchemaElement();
   _customers.Name = "customers";
   _customers.SchemaType = _complextype;
   
   _schema.Items.Add(_customers);
   
   //compile the schema
   try
   {
       XmlSchemaSet _set = new XmlSchemaSet();
       _set.Add(_schema);
       _set.Compile();
   }
   catch (Exception ex)
   {
       MessageBox.Show("Schema compilation failed "+ ex.Message);
       return;
   }
   
   //save the schema
   XmlTextWriter _writer = new XmlTextWriter(txtfile.Text, null);
   _schema.Write(_writer);
   _writer.Close();
   MessageBox.Show("Schema Created Successfully!");
   //display xsd file
   if (MessageBox.Show("Do you want to open the xsd schema file?", "Question", MessageBoxButtons.YesNo,
                       MessageBoxIcon.Question) == DialogResult.Yes)
   {
       System.Diagnostics.Process.Start(txtfile.Text);
   }
   ```

## 5.3 利用DTD和XSD Schema验证XML文档

### 5.3.1 应用DTD

通过XML文档中包含文档类型声明来建立当前文档和DTD的关联。当进行有效性验证的XML处理器读到该声明时，它获取DTD并本剧其中定义的规则对文档进行检验。文档类型声明必须为于XML申明之后，且在根元素之前，不过在XML声明和文档类型声明之间可以插入注释和处理指令。我们可以直接在XML文档中定义DTD(内部DTD)，也可以通过URI引用稳步DTD文件，或同时采用这两种方式。

1. 内部DTD

   ```xml
   <?xml version="1.0" encoding="utf-8" ?>
   <!ELEMENT customers (customer*)>
   <!ELEMENT customer (firstname,lastname,homephone,notes)>
   <!ELEMENT firstname (#PCDATA)>
   <!ELEMENT lastname (#PCDATA)>
   <!ELEMENT homephone (#PCDATA)>
   <!ELEMENT notes (#PCDATA)>
   <!ATTLIST customer customerid CDATA #REQUIRED>
   <customers>
     <customer customerid="1">
       <firstname>John</firstname>
       <lastname>Cranston</lastname>
       <homephone>(445) 269-9857</homephone>
       <notes>
         <![CDATA[He registed as our member since 1990. John has nice credity. He is a member of Custom International.]]>
     </notes>
     </customer>
     <customer customerid="2">
       <firstname>Annie</firstname>
       <lastname>Loskar</lastname>
       <homephone>(445) 269-9482</homephone>
       <notes>
         <![CDATA[Annie registed as our member since 1984. He became our VIP customer in 1996.]]>
       </notes>
     </customer>
     <customer customerid="3">
       <firstname>Bernie</firstname>
       <lastname>Christo</lastname>
       <homephone>(445) 269-3412</homephone>
       <notes>
         <![CDATA[Bernie registed as our member since June 2010. He is a new member.]]>
       </notes>
     </customer>
   <customer customerid="4">
       <firstname>Ernestine</firstname>
       <lastname>Borrison</lastname>
       <homephone>(445) 269-7742</homephone>
       <notes>
         <![CDATA[Ernestine registed as our member since Junl 2010. She is a new member.]]>
       </notes>
     </customer>
   <customer customerid="5">
       <firstname>Ernestine</firstname>
       <lastname>Borrison</lastname>
       <notes>
         <![CDATA[Ernestine registed as our member since Junl 2010. She is a new member.]]>
       </notes>
       <homephone>(445) 269-7742</homephone>
     </customer>
   
   </customers>
   ```

   

2. 外部DTD

   ```xml
   <?xml version="1.0" encoding="utf-8" ?>
   <!DOCTYPE customers SYSTEM "Customer.dtd">
   <customers>
     <customer customerid="1">
       <firstname>John</firstname>
       <lastname>Cranston</lastname>
       <homephone>(445) 269-9857</homephone>
       <notes>
         <![CDATA[He registed as our member since 1990. John has nice credity. He is a member of Custom International.]]>
     </notes>
     </customer>
     <customer customerid="2">
       <firstname>Annie</firstname>
       <lastname>Loskar</lastname>
       <homephone>(445) 269-9482</homephone>
       <notes>
         <![CDATA[Annie registed as our member since 1984. He became our VIP customer in 1996.]]>
       </notes>
     </customer>
     <customer customerid="3">
       <firstname>Bernie</firstname>
       <lastname>Christo</lastname>
       <homephone>(445) 269-3412</homephone>
       <notes>
         <![CDATA[Bernie registed as our member since June 2010. He is a new member.]]>
       </notes>
     </customer>
   <customer customerid="4">
       <firstname>Ernestine</firstname>
       <lastname>Borrison</lastname>
       <homephone>(445) 269-7742</homephone>
       <notes>
         <![CDATA[Ernestine registed as our member since Junl 2010. She is a new member.]]>
       </notes>
     </customer>
   <customer customerid="5">
       <firstname>Ernestine</firstname>
       <lastname>Borrison</lastname>
       <notes>
         <![CDATA[Ernestine registed as our member since Junl 2010. She is a new member.]]>
       </notes>
       <homephone>(445) 269-7742</homephone>
     </customer>
   
   </customers>
   
    
   ```

### 5.3.2 应用XSD Schema

为了在XML文档中关联外部的XSD Schema文件，要在XML文档的根元素声明处做如下修改：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<customers xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="Customers.xsd">
  <customer customerid="1">
    <firstname>John</firstname>
    <lastname>Cranston</lastname>
    <homephone>(445) 269-9857</homephone>
    <notes>
      <![CDATA[He registed as our member since 1990. John has nice credity. He is a member of Custom International.]]>
  </notes>
  </customer>
  <customer customerid="2">
    <firstname>Annie</firstname>
    <lastname>Loskar</lastname>
    <homephone>(445) 269-9482</homephone>
    <notes>
      <![CDATA[Annie registed as our member since 1984. He became our VIP customer in 1996.]]>
    </notes>
  </customer>
  <customer customerid="3">
    <firstname>Bernie</firstname>
    <lastname>Christo</lastname>
    <homephone>(445) 269-3412</homephone>
    <notes>
      <![CDATA[Bernie registed as our member since June 2010. He is a new member.]]>
    </notes>
  </customer>
<customer customerid="4">
    <firstname>Ernestine</firstname>
    <lastname>Borrison</lastname>
    <homephone>(445) 269-7742</homephone>
    <notes>
      <![CDATA[Ernestine registed as our member since Junl 2010. She is a new member.]]>
    </notes>
  </customer>
<customer customerid="5">
    <firstname>Ernestine</firstname>
    <lastname>Borrison</lastname>
    <notes>
      <![CDATA[Ernestine registed as our member since Junl 2010. She is a new member.]]>
    </notes>
    <homephone>(445) 269-7742</homephone>
  </customer>
</customers>

 
```

## 5.4 用XmlReader类验证XML文档

```c#
string txtxmlfile = string.Empty;
string txtvalidatefile = string.Empty;

bool useDtd = true;
bool useXsd = false;

XmlReaderSettings setting;

if (useDtd)
{
	setting.ProhibitDtd = false;
	setting.ValidationType = ValidationType.DTD;
}
if (useXsd)
{
	setting.ValidationType = ValidationType.Schema;
	setting.Schemas.Add("", txtvalidatefile);
}

XmlReader _reader = XmlReader.Create(txtxmlfile, setting);
while (_reader.Read())
{
	//TODO: some code in here for processing document
}
_reader.Close();
```



## 5.5 用XmlDocument类验证XML文档

```c#
#region Variables

private XmlDocument doc;
private int nodeindex = 0;
private bool isError = false;

private XmlReaderSettings settings;
#endregion

#region Constructor

public Form1()
{
	InitializeComponent();
	doc = new XmlDocument();
	settings = new XmlReaderSettings();
	settings.ValidationEventHandler += new ValidationEventHandler(OnValidationError);
}
#endregion

#region Methods

private void AddItemsIntoCombBox(XmlDocument _doc)
{
	cmbID.Items.Clear();
	foreach (XmlNode _node in _doc.DocumentElement.ChildNodes)
	{
		cmbID.Items.Add(_node.Attributes["customerid"].Value);
	}
}
private void FillControlters()
{
	XmlNode _nod = doc.DocumentElement.ChildNodes[nodeindex];
	cmbID.Text = _nod.Attributes["customerid"].Value;
	txtfname.Text = _nod.ChildNodes[0].InnerText;
	txtlname.Text = _nod.ChildNodes[1].InnerText;
	txtphone.Text = _nod.ChildNodes[2].InnerText;
	txtnote.Text = _nod.ChildNodes[3].InnerText;

	this.UpdateInformation();
}
private void OnValidationError(object sender, ValidationEventArgs e)
{
	MessageBox.Show(e.Message);
	isError = true;
}
private void UpdateInformation()
{
	lblinformation.Text = "Customer " + (nodeindex + 1) + " of " + doc.DocumentElement.ChildNodes.Count.ToString();
}
#endregion

private void Form1_Load(object sender, EventArgs e)
{
	settings.ValidationType = ValidationType.Schema;
	settings.Schemas.Add("", Application.StartupPath + @"\Customers.xsd");
	XmlReader _reader = XmlReader.Create(Application.StartupPath + @"\Customers.xml", settings);
	doc.Load(_reader);
	_reader.Close();

	if (isError == false)
	{
		this.AddItemsIntoCombBox(doc);
		this.FillControlters();
	}
}

private void btnadd_Click(object sender, EventArgs e)
{
	if ((txtfname.Text == "") || (txtlname.Text == "") || (txtphone.Text == "") || (txtnote.Text == ""))
	{
		MessageBox.Show("Please fill up all items of customer!");
		return;
	}
	XmlElement _customer = doc.CreateElement("customer");
	XmlElement _firstname = doc.CreateElement("firstname");
	XmlElement _lastname = doc.CreateElement("lastname");
	XmlElement _homephone = doc.CreateElement("homephone");
	XmlElement _notes = doc.CreateElement("notes");

	XmlAttribute _customerid = doc.CreateAttribute("customerid");
	_customerid.Value = cmbID.Text;

	XmlText _firstnametext = doc.CreateTextNode(txtfname.Text);
	XmlText _lastnametext = doc.CreateTextNode(txtlname.Text);
	XmlText _homephonetext = doc.CreateTextNode(txtphone.Text);
	XmlCDataSection _notestext = doc.CreateCDataSection(txtnote.Text);

	_customer.Attributes.Append(_customerid);
	_customer.AppendChild(_firstname);
	_customer.AppendChild(_lastname);
	_customer.AppendChild(_homephone);
	_customer.AppendChild(_notes);

	_firstname.AppendChild(_firstnametext);
	_lastname.AppendChild(_lastnametext);
	_homephone.AppendChild(_homephonetext);
	_notes.AppendChild(_notestext);


	doc.DocumentElement.AppendChild(_customer);
	doc.Validate(OnValidationError, _customer);
	if (!isError)
	{
		doc.Save(Application.StartupPath + "/Customers.xml");

		this.AddItemsIntoCombBox(doc);

		this.UpdateInformation();
	}
}

private void btnupdate_Click(object sender, EventArgs e)
{
	if ((txtfname.Text == "") || (txtlname.Text == "") || (txtphone.Text == "") || (txtnote.Text == ""))
	{
		MessageBox.Show("Please fill up all items of customer!");
		return;
	}
	XmlNode _node = doc.SelectSingleNode("//customer[@customerid='" + cmbID.SelectedItem + "']");
	if (_node != null)
	{
		if (_node.ChildNodes[0].InnerText != txtfname.Text)
			_node.ChildNodes[0].InnerText = txtfname.Text;
		if (_node.ChildNodes[1].InnerText != txtlname.Text)
			_node.ChildNodes[1].InnerText = txtlname.Text;
		if (_node.ChildNodes[2].InnerText != txtphone.Text)
			_node.ChildNodes[2].InnerText = txtphone.Text;
		if (_node.ChildNodes[3].InnerText != txtnote.Text)
		{
			XmlCDataSection _notes = doc.CreateCDataSection(txtnote.Text);
			_node.ChildNodes[3].ReplaceChild(_notes, _node.ChildNodes[3].ChildNodes[0]);
		}
	}
	doc.Validate(OnValidationError, _node);
	if (!isError)
	{
		doc.Save(Application.StartupPath + "/Customers.xml");
	}
}

private void btndelete_Click(object sender, EventArgs e)
{
	if (cmbID.SelectedIndex < 0)
	{
		MessageBox.Show("Please select Customer ID");
		return;
	}
	XmlNode _node = doc.SelectSingleNode("//customer[@customerid='" + cmbID.SelectedItem + "']");
	if (_node != null)
	{
		doc.DocumentElement.RemoveChild(_node);
	}
	doc.Save(Application.StartupPath + "/Customers.xml");

	nodeindex = 0;
	this.FillControlters();
	this.UpdateInformation();
	this.AddItemsIntoCombBox(doc);
}

private void btnfirst_Click(object sender, EventArgs e)
{
	nodeindex = 0;
	this.FillControlters();
}

private void btnprevious_Click(object sender, EventArgs e)
{
	nodeindex--;
	if (nodeindex < 0)
	{
		nodeindex = 0;
	}
	this.FillControlters();
}

private void btnnext_Click(object sender, EventArgs e)
{
	nodeindex++;
	if (nodeindex >= doc.DocumentElement.ChildNodes.Count)
	{
		nodeindex = doc.DocumentElement.ChildNodes.Count - 1;
	}
	this.FillControlters();
}

private void btnlast_Click(object sender, EventArgs e)
{
	nodeindex = doc.DocumentElement.ChildNodes.Count - 1;
	this.FillControlters();
}
```

## 5.6 用XPath导航器验证XML文档

当你用XPathNavigator读取XML文档时可能希望验证这些文档。从前面的章节中，我们 知道利用X



## 5.7 验证未声明XSD Schema文件位置的XML文档

```c#
private void button1_Click(object sender, EventArgs e)
{
    if (txtxmlfile.Text == "")
    {
        MessageBox.Show("Please input path and filename of XML File");
        txtxmlfile.Focus();
        return;
    }
    if (txtschemafile.Text == "")
    {
        MessageBox.Show("Please input path and filename of Validation File");
        txtschemafile.Focus();
        return;
    }
    if (!File.Exists(txtxmlfile.Text))
    {
        MessageBox.Show("Invalid XML file");
        return;
    }
    if (!File.Exists(txtschemafile.Text))
    {
        MessageBox.Show("Invalid validation file");
        return;
    }
    XmlDocument _doc = new XmlDocument();
    _doc.Load(txtxmlfile.Text);
    _doc.Schemas.Add(null, txtschemafile.Text);

    ValidationEventHandler eventHandler = new ValidationEventHandler(ValidationEventHandler);

    _doc.Validate(eventHandler);
}

void ValidationEventHandler(object sender, ValidationEventArgs e)
{
    switch (e.Severity)
    {
        case XmlSeverityType.Error:
            MessageBox.Show("Error: {0}" + e.Message);
            break;
        case XmlSeverityType.Warning:
            MessageBox.Show("Warning {0}" + e.Message);
            break;
    }
}
```


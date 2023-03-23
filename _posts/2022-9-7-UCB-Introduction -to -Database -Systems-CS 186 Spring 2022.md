# CS186 Lecture 1 Introduction + SQL I

- 什么是数据库？
  - 提供对大量持久性数据的高效、方便、安全、多用户的存储和访问。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209162308767.png" alt="image-20220916230756624" style="zoom:67%;" />
- Databases, Database Systems and DBMSs：
  - Database本身只是大量数据的集合，DBMS管理这个集合。
  - 有时候Database会和DBMS互称，需要经过上下文来辨别
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209162315852.png" alt="image-20220916231551774" style="zoom:67%;" />

## Relational Terminology

- 一些关系术语
- 数据库：有名字的关系的集合
- 关系（即表）
  - Schema（模式）：一个模式，一个描述（元数据），关于数据库和表的布局及特性的信息。
  - Instance：满足那个Schema的数据的集合。
- 属性：也就是某一列，某一个域
- 记录（行，一个元组）：表中的一行

- Schema是固定的，一个表格的列数，每一列的属性的名字以及它的原始类型是不会变的。
  - **每个属性（列）的名字必须是独一无二的**（所以下面那个不是一个Schema，有两列重名了）
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209181924042.png" alt="image-20220918192420891" style="zoom: 50%;" />
  - **每个属性（列）的类型必须是原始类型**（类似`int double`）。`addr`属性不满足这个条件。
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209181927805.png" alt="image-20220918192734724" style="zoom:50%;" />
  - 因为一列是一个属性，属性是固定不变的。
  - 但是实例（记录）是会变的，某一行是一个记录
  - 表的布局不会变（列数，每一列是什么属性），但是表中的记录可以变。

- 两种SQL子语言：

  - **DDL：Data Definition Language**
    - DDL允许用户定义数据，也就是创建表、删除表、修改表结构这些操作。通常，DDL由数据库管理员执行。

  - **DML：Data Manipulation Language**
    - DML为用户提供添加、删除、更新数据的能力，这些是应用程序对数据库的日常操作。

- 主键：

  - 主键是用于唯一性地查找到一条记录的某个属性（或者多个属性联合），通过主键可以定位到表中的唯一一个记录（唯一一行）
    - 通过这个属性，可以区分某条记录和所有其他剩余的记录。
  - 主键不能有重复值（否则一个主键可以查找到多个记录，比如以名字为主键，查找到多个重名的人）
  - 主键可以由多列联合构成，比如用（学号，姓名）联合。
  - 主键需要满足的条件：
    - 每个行都必须要有主键值
    - 任意两行都不可以具有相同的主键值，主键值不能为`NULL`。
    - **使用联合主键时，构成的主键也必须满足这个条件，但是构成主键的某个列可以不满足上述两个条件。**

- 外键：通过表中的某个与其他表相关联的字段，将本表的某条记录与外部表的一条记录关联起来的键。
  - 通过外键，可以查找到另外一张表，通过那张表的主键来查找那张表。
    - 不需要和被引用的主键取一样的名字。
    - 外键不一定是主键，但是一定是唯一性索引。
  - 如在student表中增添一个class_id字段，将每一个学生都与一个班级关联起来，student与class两张表就关联起来了。

# CS186 Lecture 2 SQL II

## Cross Product

- Cross Product：交叉相乘，指的是一个表的每一行和另外一个表的每一行连接在一起。
  - 每一行的对偶。
- `Reserves`：指的是表的名字，船员预定船的记录表。
- 船员表和预定记录表的CrossProduct：
  - 第一行船员和预定的第1 2 3行连接，第二行船员和预定的第1 2 3行连接，以此类推。
  - 最后的结果的表就长下面那样：前三行就是第一行船员和预订记录的第1 2 3行连接的结果
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201355050.png" alt="image-20220920135547963" style="zoom: 80%;" />
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201351034.png" alt="image-20220920135132864" style="zoom:67%;" />

## JOIN

- JOIN：进行两张表的连接。
- 假如我们要用：船员、船员预定的船号、船员预定的日期来作为一张表的三列，我们需要将船员表和预订记录表联合起来。
- 这个过程是这样的：
  - 先做两个表的交叉相乘
  - 然后根据`Sailor.sid = Reserves.sid`这个条件筛选出符合条件的。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201402624.png" alt="image-20220920140201511" style="zoom:67%;" />

### Inner JOIN

- 内联：给两张表，计算它们的交叉相乘的表，然后给一个筛选条件，保留交叉相乘的表中满足条件的行
- Inner Join的两种语法：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201405804.png" alt="image-20220920140555698" style="zoom: 50%;" />

### Join Variants

- 在SQL中，Join的各种种类：
  - Inner Join
  - Left Join
  - Right Join
  - Full Outer Join
- 每一种Join实现的是不同的效果，需要根据你想要的效果来选择。
  - 比如Inner Join就是完全匹配。
- 默认是Inner Join
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201412171.png" alt="image-20220920141232067" style="zoom: 67%;" />

### Inner/Natural Join 

- Inner Join和Natural Join的区别
- 如下图的从上往下依次计数，第一个和第二个是等效的，第二个是inner Join，第三个是natural Join。
- 三个全是等效的，Natural Join是等值连接。
  - 具体来说就是，两个表中具有相同名字的两个列，将这两个列中属性值相同的联合在一起。
  - 比如说`Sailors s NATURAL JOIN Reserves r` ，r中的所有列和s中的所有列，只要有相同的名字，都要联合在一起，并且要满足对应属性的值相等。
    - 比如s和r共享一个叫sid的列，那么必须要满足`s.sid = r.sid`的联合条件。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201424832.png" alt="image-20220920142457724" style="zoom:67%;" />

### Left Outer Join

- Outer Join和Inner Join的不同之处在于：
  - 两个表联合起来，Inner Join只会保留完全符合筛选条件的
  - Outer Join则会保留某些不符合条件的行，视情况而定是保留Join左侧的表的行还是保留Join右侧的表的行。
- Left Outer Join：
  - 除了返回所有满足条件的行外，也会保留所有来自`Join`关键字左侧的表的不符合条件的行。对于不符合条件的元组使用`null`来标注相关属性。
  - 比如`Sailors Left Outer Join Reserves`
    - 返回那些预定过船的船员的记录，包含船员名字、sid、bid。bid指的是boat id。
    - 同时也保留左侧的那些没有预定过船的船员的记录，包含船员名字，sid、bid则使用`null`来赋值（因为没有预定船）
    - 如果使用inner Join，则只能得到那些预定过船的船员的sid、bid、sname，没有预定记录的船员会被直接去除掉。
  - left outer Join有时候也被缩写成left Join
  - **没有left inner Join这种东西，完全矛盾啊**

### Right Outer Join

- 和Left Outer Join完全一样，除了保留的是`Join`关键字右侧的表的未匹配项。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201511168.png" alt="image-20220920151133044" style="zoom:67%;" />

- Right Outer Join和Left Outer Join是完全等价的，是可以相互转换的。
  - 只需要把在Right Outer Join右侧的表，放到Left Outer Join左侧，就可以实现保留同一个表的未匹配项。
  - 比如，`A Right Outer Join B`，完全等价于 `B Left Outer Join A`
- 下图的SQL语句，翻译过来就是：
  - 我要你给我所有的Boat，如果这个Boat被船员预定了，给我对应的sid。
  - 但是如果某艘船没被任何人预定，为了保证我们查到了所有的船，你还是给我这艘船的bid，只是其对应的sid，你用null来代替。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201514659.png" alt="image-20220920151426572" style="zoom:67%;" />
- 问题：既然Left Outer Join完全可以替代Right Outer Join，那为什么后者还存在？毕竟你只需要翻转一下表在Join两侧的位置就可以相互替代了。
  - 方便性，代码可读性之类的
  - 以及习惯。实践中由于各种各样的因素，数据的顺序、可读性什么的，可能会选择其中一种。

### Full Outer Join

- 既保留Join关键字左侧的表的未匹配项，也保留右侧。其他的都一样
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201542068.png" alt="image-20220920154204953" style="zoom:67%;" />

![image-20220920154253009](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201542105.png)

## Column Names And Table Aliases

- 给表取一个别名，使用`AS`关键字

- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201547849.png" alt="image-20220920154745763" style="zoom:67%;" />

### More Aliases

- 当你要让一个表自己和自己相连接时（self Join），就需要别名来区分这两个表。
  - 有时候出于同一个表内对比的原因会做这种操作。
  - `FROM Sailors AS x,Sailors AS y`，同一个表，不同的别名。
- AS关键字除了可以用在FROM，还可以用在`SELECT`中给选定的列取别名，结果中就会用别名来替代原有的列名。
  - 比如`y.age AS age2`，`age2`出现在了右侧的表格的列名上。

- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201550084.png" alt="image-20220920155019938" style="zoom:67%;" />

## Arithmetic Expressions

- SQL中可以在很多地方灵活使用算术表达式，比如在SELECT中使用，输出的结果就会多一列。
  - `SELECT 2*S.age AS age2`会使得输出的结果中多一列名为age2的列，它的值是`S.age`的两倍。
  - `WHERE`也可以用，总之非常灵活。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201555680.png" alt="image-20220920155502564" style="zoom:67%;" />

## String Comparision

- 有传统的老式和标准的正则表达式之分
- 二者可以完全相互代替。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201610582.png" alt="image-20220920161009487" style="zoom:67%;" />

## Logical Operation

- 关键字：AND、OR、NOT，使用圆括号来改变运算顺序。
  - 概括一下就是，使用规则符合直觉。和编程语言基本上一样

## Set Semantics

- 集合语义
- 集合的定义：和高中一样。
- 基本上就是高中知识
- 集合的基本运算：
  - 交集：InterSect
  - 并集：Union
  - 差集：Except，A Except B：所有属于A且不属于B的元素构成的集合。
- 一般的集合是不会有重复的元素的。但是有的时候又需要保留重复的元素，于是就有了多重集。
- 下图的R和S不是集合，因为有重复的元素。
  - 对R和S的运算是会去重的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201617781.png" alt="image-20220920161706657" style="zoom:67%;" />

- 对于多重集，就有对应的运算：
  - Union All：多重集的并集
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201624089.png" alt="image-20220920162424978" style="zoom:50%;" />
  - Intersect All：多重集的交集
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201623857.png" alt="image-20220920162353733" style="zoom:50%;" />
  - Except All：多重集的差集
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201623459.png" alt="image-20220920162330342" style="zoom: 50%;" />

## Nested Queries

### IN\NOT IN

- `IN`基本上就是说，把子查询查询到的结果作为一张表，然后从这张表里来查我们想要的东西。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201633373.png" alt="image-20220920163308270" style="zoom:50%;" />
- 加个NOT就是相反的意思，排除在表里的那些。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201633754.png" alt="image-20220920163358658" style="zoom: 50%;" />



### EXISTS

- 用于实现关联查询
- 没太听明白有什么用
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201637423.png" alt="image-20220920163748324" style="zoom:50%;" />

### ANY/ALL

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201710352.png" alt="image-20220920171043228" style="zoom: 50%;" />

## VIEWS

## ARGMAX

## WITH

## Subquery in FROM

## NULL VALUES

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201733123.png" alt="image-20220920173350980" style="zoom: 50%;" />

- 在运算中，NULL与任何东西做运算得到的都是NULL
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201735843.png" alt="image-20220920173519757" style="zoom:50%;" />

- 检查值是否为NULL：使用`IS NULL`和`NOT NULL`，不要用等于号。
- NULL与布尔值进行逻辑运算：
  - false `AND` NULL，直接短路运算，结果得到的是false。
  - true `OR` NULL，直接短路运算，结果得到的是true。 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201739100.png" alt="image-20220920173940963" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209201740565.png" alt="image-20220920174050466" style="zoom:50%;" />

# CS186 Lecture 3  Disk, Buffers, Files I

## Disks

- 大部分的数据库系统最开始都是为磁盘设计的。磁盘已经是过时的产物了，但是设计的思想还是永不过时的。
- 硬盘的特性：
  - 按块从内存写入数据到硬盘，或者按块从硬盘写入数据到内存。
    - 提供两个API：Read()和Write()
  - 两个API都非常慢，读写硬盘非常慢，所以要尽量的减少读写的次数。
- 块和页的尺寸都是扇区尺寸的常数倍，具体多少倍取决于配置。
- 访问硬盘上的一页：
  1. 寻道时间（将磁头移动到相应的道上），2到3毫秒。
  2. 旋转时间（等待需要的区域旋转到磁头下方，不是磁头旋转，是盘片旋转）。0到4毫秒
  3. 传输时间（传输数据，从磁盘到内存或者反向）。0.25毫秒每64KB。

- 所以访问硬盘的主要时间开销在寻道和旋转时间上，要减少I/O开销，关键就在减少寻道和旋转的延迟。
- 层次化的存储结构：
  - 内存和SSD的读写速度差了一万倍，内存和机械硬盘的读写速度差了十万倍。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210111455600.png" alt="image-20221011145519433" style="zoom:80%;" />


### Block Level Storage

- 大部分的DBMS将信息存储在SSD或者Disk上，Disk（机械硬盘）很慢，很过时，SSD相对Disk更快，但是相对内存还是很慢，二者的写入开销都很大。

- 当读写完一个块时，接着读写下一个块是最快的，因为不需要再去寻道，也不需要再去等磁盘转过来，直接顺着往前继续写就是下一块。
- 为了最大化利用每次读写，要尽可能的一次读越多的数据，或者一次写越多的数据越好。
  - 比如你要从地球运东西到冥王星去（在开销上，好比从内存写入机械硬盘），那你的太空舱肯定是尽量装满了东西的。
- 相应的措施：
  - 在内存中缓存访问较多的块
  - 预拉取很可能会被访问的块
  - 缓存对于连续的块的写入（然后一次性写入块中）。
- 在本门课程中，Page和Block是同义词。
- Block：Unit of  transfer for disk read/write.机械硬盘读写的最小传输单元。
  - 典型的64-128KB每块。
  - 不可能读写半个块，或者不是块的整数倍。

- Page：本课程中与Block是同义词，其他上下文中可能指一块连续的块大小的RAM。 

### Arranging Blocks On Disk

- “下一个”块的概念：或者说，连续的块的概念
  - 先是处于同一个磁道上的连续的块
  - 然后是处于同一圆柱上不同磁道的块（不用寻道，只用选择不同高度的磁头）
  - 最后是处于相邻圆柱上的块。（寻道时间短）
- 通过将文件保存在连续的“下一个”块上，能够最小程度上的减少寻道和等待旋转的时间。
- 对于一个连续的扫描，就可以一次性预取多个块，这样能够节省很多时间。于是也可以读这些连续的大块。

## Disk Space Management

- 磁盘空间管理层是DBMS的最底层，负责管理磁盘上的空间。
- 目标：
  - 将逻辑块映射到磁盘上的某个物理位置
  - 提供用于从硬盘加载块到内存的API
  - 提供用于从内存写入块到硬盘的API
- 高层调用这一层来实现对块的读写以及分配和回收逻辑块

### Disk Space Management：Requesting Pages

- 物理上连续存储在硬盘上的块是最能满足高层模块对于连续的块的请求的
- 但是物理上的细节是对高层屏蔽的，同时高层会假设它的要求能很快得到满足。

### Disk Space Management：Implementation

- 第一种实现方式：直接和设备进行通信
- 这种方式如果熟悉设备的话可能很快。
- 问题：
  - 可移植性不好。
  - 通用性不高。
  - 万一换设备了？

- 第二种实现方式：基于文件系统运行。
  - 基于文件系统在一个很大的空的磁盘上分配一个很大的连续的文件，并假定访问连续的字节很快。
- 一个小问题：文件系统通常是要求一个文件的全部内容都处于同一个硬盘上。但是DBMS可能不满足这个要求
  - DBMS的文件可能一个硬盘放不下，因而分布在多个硬盘上。"分布式系统"

- 像这样：Disk Space Manager给上层一个单个的巨大的连续的文件的假象，但底层实际上是分布式的。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209202147547.png" alt="image-20220920214703400" style="zoom:67%;" />

### Summary

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210111517072.png" alt="image-20221011151733944" style="zoom: 67%;" />

## File Representation

- 表以逻辑文件的形式存在，文件由页组成，页是记录的集合。
- 在硬盘上，页由磁盘空间管理器管理。在内存中由缓冲管理器模块来管理，DBMS的更高层级的模块只在RAM上工作。
- DB 文件：页的集合，每一页是记录的集合。
  - 向高层提供的API：插入、删除、修改记录
  - 根据记录ID查找特定的记录，记录ID是由（页ID，在页面上的位置）组成的指针。
  - 扫描所有的记录
- DB文件结构（页在文件中的布局）：
  - 无序堆文件。记录在页之间无序放置。
  - 聚集堆文件。分组的记录和页。
  - 有序文件。页和记录是排好序的。
  - 索引文件。B+树等等。索引文件可能包含记录，可能指向其他文件中的记录。

### 无序堆文件

- 不带任何顺序的记录的集合。（集合中的记录没有顺序，或者说顺序没有意义）

- 堆不是数据结构里的那个堆，heap不是cs61b里的heap
- 随着文件的增减，页也会分配或者回收。为了支持在记录层级（Record Level）上的操作，需要：
  - 跟踪文件上的页（Keep track of pages in file)
  - 跟踪页上的可用空间（Keep track of  free space on pages）
  - 跟踪页上的记录（Keep track of records in page）

#### 使用链表实现 

- Header page的PageID和堆文件名存在别的地方。
  - 存在Database Catalog，数据库目录
- 每一页是链表的一个节点，包含数据本身、可用空间、以及节点的前向指针和后向指针。
- HeaderPage的上下两条链表，一条是全部存放满的页组成的链表，另外一条是有可用空间的页组成的链表。
- 缺点：要想找到有足够空间（比如我想要20字节），最坏的情况下需要遍历一遍（具有free space的page组成的）链表。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210111543311.png" alt="image-20221011154312158" style="zoom:67%;" />

#### 改进：使用页目录

- 目录项（Header Page）包含：
  - 某个页的可用空间的大小
  - 到这个页的指针
- 由于目录项经常会被访问，所以它会在缓存中，相对于使用链表，找到一个能够放入记录（满足我们对空间的要求）的页所耗费的对页的读取要小得多。
  - 因为你只要查一遍Header Page就可以了。一次对它的读取可以获取多个页的可用空间情况。

## Page Layout

- 刚才讲述了页在文件中是如何布局的，现在讲述的是记录在页中是如何布局的。

- Page Header：页头
  - 包含记录的条数
  - 可用空间
  - 下一个/最后一个记录的指针？（10.14新增：知道最后一个记录在哪里，就可以直接尾插新纪录，类似链表的尾部指针）
  - 比特图（或者槽表），用于记录多少空槽，多少满槽。
- 设定页头要解决的问题：
  - 记录的类型？记录是固定的还是可变的？记录的长度？
  - 使用记录ID找到记录？
  - 怎么对记录进行增删改查？
  - 页与页之间是否留存空间？

### 固定长度的记录

- 首先我们将数据的存储形象化的描述为存储在一个线性的链表上，然后将其折叠成图中那样，这样屏幕里放得下。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210111633885.png" alt="image-20221011163305759" style="zoom:67%;" />
- 在记录的长度是固定的情况下，我们将记录密集地打包在一起，记录与记录之间不存在空隙。
- 要找到某个记录，只需要计算其相对Page Header的偏移量。
  - 假定Page Header是8个字节，然后单个记录是6个字节，8 + 6 * RecordSequentialNumber就是偏移量。
- 增加某个记录很简单，只需要往尾巴插入一个节点（Append）
- 删除某个记录可能困难一些，要修改被删除的记录之后的记录的RecordID
  - 比如你删除了第三个记录，那么原来的RecordID为4的，要变为3，为5的要变为4，RecordID大于3的记录，都要修改其ID（减去一）
  - 有的时候开销会很大，比如你删除100个记录里的第一个记录？第2个到第100个都要修改他们的ID，改成1到99。
- 所以删除某个记录的另外一个选项就是使用`bitmap`：
  - 删除的时候采用Lazy deletion，只将对应的空间标记为空（位图对应的比特清零），下次对这个槽插入的时候直接覆盖掉原有内容。
  - 在Page Header里存放一个比特图，每一位记录每个记录槽的使用情况，比如用1代表满，0代表空。
  - 每次在某个槽插入一个记录，就将对应的位由0翻转为1。
  - 每次要插入，就先在`bitmap`找到第一个空槽（标记为0的槽），然后将记录插入那个槽（计算偏移量，找到那个槽，插入记录，翻转比特图的比特）。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210111634233.png" alt="image-20221011163425105" style="zoom:80%;" />

### 可变长度的记录

- 如果我们不知道记录多长怎么办？
- 可是为什么我们会不知道记录有多长？
- 因为并不是所有的记录都是固定长度的，比如记录中有一个文本域，我们无法提前预知这个文本域会有多长，所以记录的长度只能是可变的。
- 可变长的记录就带来了另外一个问题：我们没办法再使用一个固定的算式来计算某个记录的偏移量了，因为处于开头和那个记录之间的每个记录的长度都不一样。
- 引入分槽的页（Sloted Page）来解决这个问题
- 如图所示，在一页中，页脚部分有一个Slot Directory，以及单独一个指针P。
  - Slot Directory可以看做是一个数组Slot Array，数组的每一项包含两个域：
    - 指向某个记录的指针（即这个记录的首地址，即图中从Slot Directory每个格子指出的绿色箭头）
    - 这个记录的大小（即多少个字节，图中Slot Directory每个黑色方格子以及一个蓝色的格子，那个`42`，里的数字）
    - 知道记录的开头在哪个位置，知道记录有多长，就可以完整的读取出这个记录了，也就解决了可变长记录所带来的问题。
  - 单独的指针P，指向的是可用空间的开头（free space的首地址，图中黄色的部分，也有一个绿色箭头指着）。
  - 删除：直接将对应的Slot Array的某个格子的指针置为NULL，比如将第4个格子的指针置为NULL。
    - 也是Lazy Deletion。
    - 这样会带来磁盘空间碎片化的问题（应该算是外部碎片？即记录与记录之间存在碎片化的空隙），稍后叙述。
  - 插入：
    - 指针P告诉你页中可用空间的开头在哪，从那个开头指向的地方开始，插入一个记录。
    - 在槽目录的下一个空槽中新增一对`指针-大小`的项。
    - 更新指针P（现在指针P应该指向新纪录末尾再往后一个字节）
  - 磁盘空间碎片化的问题？
    - 如何处理这个问题是设计的选择。
    - Reorganize data on page。重新组织数据，把碎片挤压掉。
    - 什么时候？
    - 可以删除的时候就顺便调整数据的组织。
    - 或者等到碎片足够多，累计得足够大，然后再调整。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210111710220.png" alt="image-20221011171051097" style="zoom:80%;" />

- 如果需要更多的slot？很简单，Slot数组从可用空间的末尾处开始，向内增长，而记录可用空间开头的指针向外增长，当二者向碰时，说明磁盘的可用空间已经基本上消耗殆尽了（除了那些散落的碎片空间）
  - 如下图，slot array的增长方向如靠近header的箭头所示。
  - [知乎专栏：什么是slotted page](https://zhuanlan.zhihu.com/p/433654215)
  - <img src="https://pic4.zhimg.com/80/v2-fb73c5523ef9556d2c5861187bae860b_720w.webp" alt="img" style="zoom: 33%;" />
- 总结：
  - 分槽的页对于固定长度的记录来说，也可以用，不算太坏。
  - 但是对于整个表都是固定长度的非空记录，使用`bitmap`可能是更好的选择。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210111850735.png" alt="image-20221011185039596" style="zoom:67%;" />

## Record Layout

- 这一小节来说明，记录的内部是如何布局的。

- 关系型模型，表中的每个记录都有固定的类型。
- 假定系统目录单独保存了表的模式。
  - Schema（模式）：一个模式，一个描述（元数据），关于数据库和表的布局及特性的信息。
  - 这样就不需要将类型信息和每条记录一起保存，可以节省空间。
  - 目录就是另外一张表。
- 目标：
  - 记录在RAM和硬盘中都要紧凑。
  - 对记录的域的访问要快。
- 容易的情况：记录固定长度
- 不容易的情况：变长记录

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210111910675.png" alt="image-20221011191034539" style="zoom: 67%;" />



#### 固定长度的记录

- 对于同一个文件的所有记录，域的类型是一样的。类型信息还是单独存在系统目录下。
- 要访问一个记录的第`i`个域很简单：只需要通过计算即可。以下图的记录结构为例
  - 计算记录的首地址：Header长度 + RecordSequentialNumber * 单个记录的长度
  - 假如要访问`DOUBLE`域的3.142，那么只需要从记录的首地址往后读取4个字节，就到达了`Double`域。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210111916231.png" alt="image-20221011191621070" style="zoom:67%;" />

#### 可变长度的记录

- 一种处理可变长记录的方法就是，通过填充，将其变为固定长度。
  - 比如我有一个可变长的字符串域，我确定它不会超过20个`char`的大小，那么我就先将其填充为这么大。
  - 但是这么做，很大可能是会浪费掉一部分的空间的，比如大部分的记录这个域只有3个`char`。
  - 同样存在另外一种相反的可能性，某个域填充的空间不够大，放不下某个记录的这个域。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210111923597.png" alt="image-20221011192345453" style="zoom:67%;" />

- 解决方法：记录头
- 每个记录的前面也加一个header，这个header保存了一个指针数组，指针数组的每一项指向一个可变长域的**结尾（不是开头）**
- 将所有的可变长域都放在记录的末尾，所有的固定长度的域都置于可变长域和`header`的中间。
- 以下图为例，固定长度的三个域放在`Header`和`Bob、 Big,St`这两个Varchar类型的域中间。
- Header中存放了两个指针，分别指向了`Bob`的结尾和`Big,St`的结尾。
- 通过计算：Header长度 + 所有的定长域的长度，我们可以知道可变长域从哪里开始。
- 找到变长域的开始之后，往后读取，每个变长域的结尾又被一个指针标记了（结尾同时也说明再往后一个字节就是下一个变长域的开始，而下一个变长域的结尾，也有指针标记）。
- 同样以下图为例：
  - Header假定8字节，`char1`字节，`int`4字节，那么第一个变长域`Bob`的开始就是记录的第18个字节，其结尾被指针标记，这个结尾标记再往后一个字节，就是第二个变长域`Big,St`的开头，其结尾同样被标记。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210111930966.png" alt="image-20221011193035815" style="zoom:67%;" />

## 为什么Bitmap不适用于变长的情况？

- 比特图能够在定长的情况下工作的原因是：
  1. 我知道每个记录都是固定长度的，于是我就可以预先计算这一页能够放多少个记录（页的大小除以记录的大小）
  2. 于是我就可以预先分配好一个固定槽数的bitmap，每一个比特对应一个空槽。
  3. 并且我可以确定，任意一个新插入的记录，可以放入任意一个空的槽中，因为记录是固定长度的，槽的长度和记录一样。
- 但是对于可变长的情况：
  1. 我没办法确定，bitmap上标记为空的那个槽，是否能够放的进新插入的记录，因为记录是变长的，而bitmap只记录槽是否空，不记录槽的长度，槽可能短了。
  2. 我也没办法预先计算这一页能够放得下多少个记录，因为分母（记录的大小）是一直变化的
  3. 于是我也就没办法预先分配好一个bitmap



# CS186 Lecture 4 Disk, Buffers, Files II

## Cost Model For Analysis

- 建立一个估算模型，来大概估计数据访问的开销。
  - 简化的模型，用于揭示其内在的原理，而不是重于精确的分析。
- 一些术语：
  - B：文件中块的个数（即页的个数，Number of pages）
  - P：每一页中的记录的个数
  - D：读写一页所耗费的（平均）时间
- 主要分析平均情况下的统一随机负载
- 一些简化：
  - 忽略连续的随机I/O
  - 忽略内存中的开销，只考虑硬盘开销
  - 只需要体现整体趋势
- 一些假设：
  - 单个记录的插入和删除
  - 使用相等测试来选择记录：有且只有一个匹配的记录
  - 对于堆文件：插入永远是插入在文件的末尾
  - 对于排好序的文件：
    - 记录是紧密排列的。删除之后会对空隙进行压缩。
    - 排序是根据查找所用的键来排序的。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210121617116.png" alt="image-20221012161658883" style="zoom: 50%;" />

## Cost of Operations

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210121731609.png" alt="image-20221012173153435" style="zoom:50%;" />

- 对于堆文件和有序文件操作的时间开销。假如我们要搜索的文件长上图那样$\Uparrow$。
- 扫描所有的记录：
  - 堆文件和有序的文件，时间开销都是$B*D$，因为都要对所有的页（B）进行扫描。
- 等值搜索：即基于等值测试来查找记录
  - 堆文件：
    - $P(i)$：要找的键在第$i$页的概率是$P(i) = 1 / B$。
    - $T(i)$：在第$i$页找到键时，已经扫描过的页数。
    - 于是，找到需要的键所需要扫描的页数的期望：$\Sigma T(i)B(i) = \Sigma [T(i) * (1/B)]$
    - $\Sigma T(i) * (1/B) = [\Sigma T(i)] * (1/B) = (1 + 2 + 3 + 4 +\cdots + B) * (1 / B) = (1 + B) * B /(2B) \approx B / 2$
    - 等差数列求和，(首项＋末项)乘以项数除以2。
    - **即平均要扫描一半的页数才能找到要的那个值**。
    - 于是扫描次数是$B/2$，每次扫描就是对页的一次读取，每次读取的平均耗时是$D$，则等值搜索的时间开销就是$0.5*B*D$
  - 有序文件：
    - 使用二分查找，最好情况和最坏情况下，找到值都需要$Log_2B$次的查找。
    - 于是：查找时间开销 = 平均查找次数 * 一次查找（即读取一页）所耗费的时间 = $(Log_2 B) * D$
- 范围搜索：即搜索出处于给定范围内的所有的记录，比如找出上图中处于`[1,9]`范围内的所有记录。
  - 堆文件：由于符合要求的记录可能散落在任意一个页，所以需要扫描所有的页以保证不会遗漏。
  - 有序文件：
    - 先二分搜索，找到范围的开始处：读取页面的次数：$Log_2 B$
    - 然后从开始处开始搜索，搜索到范围的结尾处，结束。这个过程的读取次数：$pages$，即页面的个数
    - 单次读写的开销$D$，于是范围搜索的总开销$((Log_2 B) + pages) * D$
- 插入一个记录：
  - 堆文件：堆文件很简单，因为插入只需要将新的记录插在尾部，然后就完事了。
    - 从硬盘读取处于末尾的页到内存，然后在内存中往页尾部插入新的记录，然后把页写回硬盘。总共2次读写。**总时间开销$2 * D$**
  - 有序文件：
    - 先通过二分查找，找到新记录应该插入的合适位置，查找次数$Log_2 B$
    - 插入之后要调整其他记录的位置，最坏的情况下，在开头插入新的记录，原来所有的记录都要往后挪一个位置，最好的情况是在末尾插入新的记录，平均的情况是有一半原有记录要往后挪一个位置，挪一个记录要经过一次读一次写，读写总次数是$B$。
    - 总的时间开销$((Log_2 B) + B) * D$
- 删除一个记录：
  - 堆文件：
    - 首先要找到那个要删除的记录，平均查找次数$B/2$
    - 然后从页中删除记录，然后再**写回硬盘**，时间开销$(B / 2 + 1)* D$
  - 有序文件：
    - 先通过二分查找，找到要删除的记录的位置（页），查找次数$Log_2 B$
    - 然后将剩下的记录，该调整位置的都调整一个位置，平均情况下次数是$B$
    - 总的时间开销$((Log_2 B) + B) * D$

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210121712480.png" alt="image-20221012171246346"  />



## B+ Trees

- 我们为什么需要索引？
  - 索引就像是文件的GPS，帮助我们更快的找到需要的记录。
  - 而在一个堆文件或者有序的文件里，查找有时候会开销特别大（你并不是总能二分查找，按一个标准排序的，在另外一个标准下也许是乱序）
  - 索引能使我们能够基于某些键来拉取我们需要的数据，而不是扫描整个文件。
  - 索引能够加速范围查找（比如查找通讯录里名字首字母以D开头的，何尝不是一种索引）

### Index

- 索引是一种使得通过键(search key)来快速查找和修改数据项的数据结构。
- 查找：可以支持多种类型的操作，比如等值查找，一维范围查找，二维范围。
- 查找键(Search Key)：关系中的列的任意子集。
  - 可以是一列，也可以是多个列的组合，不需要是独一无二的。

- 一个简单的想法是：
  1. 将堆文件排序，并且页与页之间留有一定的空隙，方便插入新的记录
  2. 页在物理上也是按照逻辑顺序存储的（连续访问）
  3. 然后在已经排好序的文件的基础上建立索引
- 为什么不直接使用二叉树？
  - 因为只有两个分支的树不够，树会很深，查找就需要很多的I/O。
  - 所以，要降低树的深度，我们需要每个节点的分支数都更多。 
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210122157224.png" alt="image-20221012215746984" style="zoom: 50%;" />

### Build a high fan-out search tree

- 先从简单的开始：引入一个排好序的`(key, page id)`文件（key file），其中`key`是查找所用的包装器，这个key file就是索引文件。
- 怎么查：
  - 大于1的在`key 1`的右侧，小于3的在`key 3`的左侧，查找和二分搜索是一样的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210122202156.png" alt="image-20221012220242969" style="zoom:67%;" />

- 进化：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210122207552.png" alt="image-20221012220735381" style="zoom:50%;" />
- 再进化：
  - 索引本身进化成一棵树，查找的时候仍然符合二分查找。
  - Key Invariant：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210122208141.png" alt="image-20221012220852922" style="zoom: 50%;" />
- 再进化，把每一层最左边的`KEY`去掉了，因为我们不需要它。
  - 比如左侧，三个子节点（page）实际上只需要父节点有两个`key`就可以划分出来了，右侧同理。
  - 磁盘上如何布局：都在同一个文件上，先是Data Page，然后是Index Page。
  - 目前我们得到的就是一种叫ISAM，Indexed Sequential Access Method，早期的IBM方法。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210122234916.png" alt="image-20221012223429708" style="zoom: 67%;" />
- 插入，以及ISAM存在的问题：
  - 插入一个记录的过程和给BST插入一个新值的方法是类似的。
  - 比如插入`11`。
  - 问题在于，可能存在溢出的Page。
  - 如下图，再插入`12`，但是由于原有的页的空间不足，所以只能新开辟一个页，放入新的记录（让`9 10 11`页指向新增的页）。
  - 那么问题来了，任何大于9的值去9的右侧查找，但是此时大于9的页有两个，而只有一个指针（指向了 `9 10 11`所在的页），此时相当于是一个指针指向了两个页，而这并不是一棵树应该有的状况。
  - 所以ISAM在插入的问题上，比较拉胯。
  - **所以在下一节我们解决这个问题，看看随着插入的增多，树开始增长，我们怎么往索引里加键，使得树不会过于深**
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141655040.png" alt="image-20221014165516812" style="zoom: 67%;" />

# CS186 Lecture 5 B+ Trees

## Enter the B+ tree

B+树的定义及其性质：

- B+树和ISAM相似。有一样的节点内部结构。
  - <Key, Page Ptr>不变性和ISAM是一样的。
  - 一样的搜索方式，小于往左走，大于往右走。
- 不同的是：
  - B+树永远是平衡的
  - B+树支持高效的插入和删除，从根节点开始增长而不是叶子节点。
- “+”：只在叶子节点存储数据的B-tree。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141657011.png" alt="image-20221014165746828" style="zoom:67%;" />

- 一个B+树的例子：
  - 最上层是根节点，再往下一层是索引页节点，最底层是叶节点，也就是实际存储数据的地方。
  - 引入两个新的概念：
    - 每个内部节点都要至少是部分满的。
    - d：树的阶，除了根节点外的每个节点（包括叶子节点，也就是存储数据的节点），其内的项目数都必须在$[d,2d]$之间。
    - 下图的阶就是2，除了根节点的每个节点的项目数，都在$[2,4]$之间。
    - **根节点项目数的上限还是$2d$**
  - B+树还有一个新的优点：
    - 底层的数据页不需要在物理排布的顺序上和逻辑顺序一致。
      - 比如不需要把逻辑上的第一块物理上放在最前面，而是可以随意放。
      - 为什么？因为页是会动态分配的，不能保证新分配的块物理上一定在后面。
    - 因为每个页之间有前向指针和后向指针来连接其逻辑上的前后页。
    - 意思就是说，所有的页构成了一个双向链表。
    - 这样的好处是，你可以在到达某一个页之后，沿着指针直接遍历一遍剩下的页。有的时候很有用，比如范围搜索所有大于3的记录的时候（找到3，然后沿着指针往右走就行了，而不需要从根节点重新开始）。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141703952.png" alt="image-20221014170340774" style="zoom: 67%;" />

## B+ Tree and Scale

B+树及其扩展性

- d = 2，树的高度为1。
- 则每个节点最多能存2d=4个项（根节点也受这个上限的限制，但是没有下限为2的限制），包括叶子结点也是。
- 根节点有4个项（key），就可以划分出五个范围区间，其中每个范围区间指向一个叶子节点。
- 每个叶子节点最多可以存储4条记录。
- 于是这棵树最多可以存储（也就是可以索引）20条记录。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141723017.png" alt="image-20221014172338827" style="zoom: 67%;" />

- 然后稍微增加一点树的高度，d还是等于2，高度为3。
- 每个节点还是最多能存储4个项。
- 于是根节点将范围划分为5个范围区间，这5个范围区间的每一个，在第二层又各自被划分成5个更小的区间。
- 于是总共就划分成了25个区间，这25个区间，每一个区间再被划分成5个子区间，总共有了125个子区间
- 这125个子区间，每个区间对应一个页，每一页（也就是叶子结点）可以存最多4项，于是最多可以存储500个记录。
- **这还仅是一颗高度为3的树，它的d还仅仅是2**。
- 一棵高度为h的B+树，它能存储的记录数为$(2d+1)^h * (2d)$。这样的指数增长使得我们能够利用B+树来快速检索很大的文件。
  - 光一棵高度为3，d为2的树就能存储500条记录了，那如果d更大呢？高度再稍微高那么一点？
  - 而且搜索一棵高度个位数的树又是很快的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141723496.png" alt="image-20221014172317267" style="zoom:67%;" />

## B+ Tree In Practice

- 在工业界，实践上典型的d是1600，典型的填充率是$67\%$（也就是说每个节点可以填充左侧$67\%$的空间，也就是2144个项）
  - 那么真实的d = 1600 * 0.67 =  1072，2d = 2144，取平均的子节点数是2144。

- 高度为1：2144个区间，对应2144个页，每个页可以放2d = 2144个项，$2144^2 = 4596736$个记录，四百多万条记录耶。
  - 当然，这里应该是估算的，因为一棵高度为h的B+树，它能存储的记录数为$(2d+1)^h * (2d)$，此处2d+1和2d取了一样的值。




![image-20221014173843625](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141738796.png)

## Searching the B+ Tree

- 和ISAM是一样的，到达了叶节点（也就是page）之后，遍历或者二分查找页，找到需要的记录。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141745161.png" alt="image-20221014174505976" style="zoom:67%;" />

- 当然，也可能是下图这样的，这是一种实现上的tradeoff，你可以在叶子结点本身存储数据，也可以让叶子结点指向你需要的记录（或页？）
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141750353.png" alt="image-20221014175047154" style="zoom:67%;" />

## Inserting Into a B+ Tree

- 第一步，找到正确的叶子。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141801698.png" alt="image-20221014180115473" style="zoom:67%;" />

- 第二步，如果LeafPage里还有空位置，直接把25放入空位。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141802359.png" alt="image-20221014180247198" style="zoom:67%;" />
- 第三步，将LeafP age排序，然后重新写回硬盘。（排序开销很小，因为这一页是在内存里的）**所以page在插入完成之后是有序的**
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141803022.png" alt="image-20221014180309827" style="zoom:67%;" />

- 但是如果leaf node已经满了？（页没有空间了）

### 页满的情况

- 将8插入B+树中。第一步，找到正确的叶子结点，在这里就是最左侧的节点`2 3 5 7`。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141942272.png" alt="image-20221014194222102" style="zoom:67%;" />

- 发现页满了，这个时候，将新插入的8和原有的`2d`个节点按照递增顺序排好序，分为两组。
  - 第一组：靠左边的`d`个，也就是最小的前`d`个元素。
  - 第二组：剩余的`d+1`个，也就是最大的前`d+1`个元素。
  - 在这里就是`2 3`一组，`5 7 8`一组。
  - 新增一个叶子，把两组分别放进原有的节点和新增的节点。
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141947643.png" alt="image-20221014194733479" style="zoom: 67%;" />
  - **然后调整一下指针关系，使得新的叶子进入之后，所有叶子组成的链条仍然构成一个有序的链表。**大概如下图：
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141947819.png" alt="image-20221014194757654" style="zoom:67%;" />
- 由于上图那样还不满足B+树的结构（和ISAM有溢出页时的结果一模一样），所以还要对树做进一步的调整。
  - 新增一个节点，这个节点里的值只有一个，就是那`2d+1`个记录中，排序之后正中间的那个，把它**拷贝**到节点里去。在这里是`5`，然后把它和根节点合并。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141950357.png" alt="image-20221014195055186" style="zoom:67%;" />
- 但是发现根节点也满了，没有办法加入一个新的值。所以也要对根节点做同样的拆分。
  - 将根节点的`2d+1`个项按递增排好序：
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141958773.png" alt="image-20221014195505916" style="zoom:67%;" />
    - 然后把最中间的那个值**推上去(Push up)**，让它做新的根节点。
    - 剩余的`2d`个项分为两组，前`d`个最小的一组，变为根节点的左子节点，剩余`d`个更大的变为右子节点。
  - 或者这么说，将根节点的`2d+1`个项排好递增序。
    - 然后分为两组
    - 第一组：靠左边的`d`个，也就是最小的前`d`个元素。
    - 第二组：剩余的`d+1`个，也就是最大的前`d+1`个元素。
      - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141958955.png" alt="image-20221014195747281" style="zoom:67%;" />
    - 第一组放在左边的子节点，第二组放在右边的子节点，然后把第二组中最小的那个元素推上去，做新的根节点。
      - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210141958103.png" alt="image-20221014195805930" style="zoom:67%;" />
      - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210142000247.png" alt="image-20221014200047054" style="zoom:67%;" />
  - 注意一个是拷贝值，而另外一个是直接推上去。**顺便，红色的箭头是错位的双向链表的节点间的指针**。

## B+ Tree Insert: Algorithm Sketch

1. 找到正确的叶子L。
2. 将新的数据项插入L。
   1. 如果L中有足够的空间，插入，重新排序，完事。
   2. 否则L在插入之前就已经满了，那么就要拆分这个节点，拆成新的节点L2和这个旧的节点L（一部分放d个值，另外一部分放d+1个值）
      1. 均分所有的数据项，放入L和L2，然后把中间值M拷贝
      2. 向L的父节点插入指向L2的索引项，其值为M。
      3. 如果L的父节点也满了，按同样的操作再拆分父节点。只需要记得这个时候是**push up**，不是**copy**
   3. 第二步可能会递归的发生多次，如果递归的发生多次，直到递归到了现有的根节点上，连根节点都要拆分了，这时候，B+树的高度就加一。
3. 树成长（变大或者变高）：宽度增加（没有递归到根节点身上），或者高度增加（根节点也被拆分了）
4. 所以工业界选取的d会是1600那么大，因为d越大，叶子L就越不容易满，这个递归的次数就越少，树的高度增长也就越慢。
   - 比如d等于2的时候可能像上面那样15个记录，高度就到3了。
   - d等于1600的时候，几百万个记录，高度还在1。 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210142012729.png" alt="image-20221014201234544" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210142012497.png" alt="image-20221014201259314" style="zoom:67%;" />

- B+树，永远都是平衡的。

## B+ Deletion

- 我们直接跳过删除。
- 因为删除很简单：直接删掉叶子结点的项，然后释放出来的空间就让它去，如果整个页都空了，那就直接删掉整个页。
  - 不会去重新组织整棵树，因为开销很大，也没有这个必要。
  - 我们也不强求一定要满足B+树的性质，因为工程中，我们优化是为了效率，而不是正确性，效率高于完全的正确性。
- 树的高度还是有对数级的保证的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210142030080.png" alt="image-20221014203007873" style="zoom:67%;" />

## Bulk Loading of B+ Tree

- Bulk Loading：批量装载。

假定我们要从零开始给一张很大的表建立索引：

- 我们可以重复调用插入的方法，然后逐渐建立整个索引B+树。
- 但是这样效率很低，为什么？
  1. 每一次插入都要从根节点开始，效率低
  2. 每次修改的都是随机的一页，缓存的效率低（Cache）
  3. 叶子会是半满的。

**Bulk Loading：**

- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210142053855.png" alt="image-20221014205356690" style="zoom:67%;" />
  1. 将要插入的记录按照某个键排好序。先别纠结怎么基于硬盘排序，会讲的。
  2. 将叶子节点填充到满足某个填充率（比如d = 2的情况下，取填充率四分之三），如下图
     - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210142049239.png" alt="image-20221014204942083" style="zoom: 67%;" />
  3. 当叶子节点到达了填充率，新增一个叶子节点，将值填充进这个叶子节点，直到它也达到填充率。
     - 这个过程中要按需要增加索引节点，因为你新增加了叶子结点，那你肯定要新增加索引节点。增加的方法也讲了。
     - ![image-20221014205132390](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210142051551.png)
     - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210142052452.png" alt="image-20221014205220266" style="zoom:67%;" />
  4. 不断重复这个过程，如果索引节点也满了，那就拆分索引节点，怎么拆分前面讲了。
     - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210142053452.png" alt="image-20221014205307273" style="zoom:67%;" />
- **在Bulk Loading过程中，一件很重要的事情**：
  - 一旦将某个页填充到了满足填充率，那么在Bulk Loading的后续过程中，就不要再动它了，去动新分配的叶子。
  - **但是在树建立完毕之后，就没有这个限制了，就正常插入就行了，该怎么样就怎么样。**
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210142058581.png" alt="image-20221014205817361" style="zoom: 60%;" />

- Bulk Loading的好处：
  1. 缓存的利用效率更好。因为你是在同一个叶子连续插入几个值，然后再在另外一个叶子连续插入几个值，然后再$\cdots$。
     - 这样对Cache的利用情况，比随机插入某个叶子好多了
  2. 叶子结点的填充率更高，因此树整体的高度就更低。
  3. 叶子Page的布局更好，更加连续。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210142102069.png" alt="image-20221014210220859" style="zoom:67%;" />

## B+ Tree Summary

- ISAM是静态的结构，因为我们只编辑页而不编辑树本身，正因如此我们需要有Overflow Pages
- B+树是一个动态的结构，它的高分支数意味着B+树的高度很少会超过3或者4，因而我们有了极高效率的搜索。
- B+树被广泛使用。
- 使用Bulk Loading为大数据集建立B+树索引，比重复调用插入来建立快得多。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210142104609.png" alt="image-20221014210458398" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210142107073.png" alt="image-20221014210735906" style="zoom: 150%;" />

- 可以对同一个表建立多棵B+树，也就是按照不同的列来建立不同的索引。
  - 比如一个学号的索引，一个名字的索引。

## Search Key and Ordering

- 能够针对列的任意**有序子集**建立索引（顺序很重要，因为它决定了排序的方式）
- 在一个有序的索引上，键是按照字典序来排序的。
  - 先按子集的顺序中的第一个列排序
  - 如果某两个记录在第一个列上的值相同，那么按照第二个列排序
  - 如果第二个列的值还相同，那么就按第三格列排序。ETC。
  - 比如按照<Age,SId>来建立索引，那么先按Age排序，相同Age的按SId排序。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210142120728.png" alt="image-20221014212033486" style="zoom: 67%;" />

- 定义：没听明白。。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210142203183.png" alt="image-20221014220309974" style="zoom:80%;" />

# CS 186 Lecture 6  Indices & B+ Tree Refinements

- 上一次的课PPT有点问题，所以这次的课是重新录制了一下，对上节课进行了些改进。

## General characteristics of an index:An outline

- 在任何的索引结构中都需要考虑的几个问题：
  - 查询支持：索引支持哪类的查询？
  - 搜索键的选择：影响我们能够使用索引来做哪些查询
  - 数据在硬盘上的存储方式：是与索引混合在一起的（索引的叶子结构就存储了数据），还是索引的叶子节点有指向数据的指针？
    - 如果是后者，硬盘上的数据是有序的连续存储的，还是随机存储的？
  - 使用哪种类型的键？是使用变长的键，还是使用定长的键？

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221538214.png" alt="image-20221122153809958" style="zoom: 67%;" />

### Index:Basic Selection

- 索引经常被用于基础的选择，相等性选择或者是范围选择。这也是B+树提供的，也是课程聚焦的。
- 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221544873.png" alt="image-20221122154414678" style="zoom:67%;" />

###  Index:Other Selections

- 在DBMS中也存在其他类型的索引。
  - 2-d box
  - 2-d circle
  - n维索引，像KD树，R树之类的。
  - 也可以使用索引来做紧邻查询

不会太深入讲这些，但是知道有这些东西是很酷的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221546986.png" alt="image-20221122154655778" style="zoom:67%;" />

这节课我们主要聚焦在传统的一维的不等性(大于小于，etc)的范围查找，B+树的，相等性的作为特殊情况。

## Data Entry Store Info

第一个要考虑的问题：数据的表现形式是怎么样的。是将实际的数据存在B+树中，还是采用指针的形式？数据在数据文件中是有序存放的还是无序存放的？

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221551318.png" alt="image-20221122155153136" style="zoom:67%;" />

### Three basic alternatives for data entries in any index

在任何索引中，数据的存储方式有三种：

1. 存储值
2. 存储引用
3. 引用的列表

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221555053.png" alt="image-20221122155528875" style="zoom:67%;" />

#### 1.存储值

这种情况，就是索引的叶子节点中直接存储你的记录的内容。换句话说就是，索引文件中存储了你的表。

这是最简单的一种，在B+树的叶子层，就保存记录本身。

记住一件事：**B+树的任意一个节点，其内部都是（相对于选定的用于建立索引的键）排好序的，同一层的节点与节点之间也是排好序的**

好处：只需要到达叶子层，然后数据就在那里，不需要额外的I/O以前往硬盘跟随指针找到对应的记录。

坏处：一个索引只是针对一个键建立的索引（以及排的序），如果我有两个KEY，我没办法同时独立地相对于这两个KEY对这个文件来排序。

- 你没有办法将索引按照UID排序的同时，保持这个顺序同时唯一地按照Name排序。

- 如果你需要按照不同的KEY来建立不同的索引，那么你就需要两个独立的索引文件。**并且每个索引文件都保存了整张表**。
- 你想要按照UID和Name建立两个索引，那么两个索引的叶子结点都保存了表的记录，相当于一张表重复存储了两遍。
  - 重复存储浪费空间不说。
  - 假如你想要更新表呢？那么为了保证数据一致性，你需要更新两张表。
  - 假如，你想要几十上百个不同的索引呢？那你同一张表重复存储了几十上百遍，更新数据的时候慢死。

所以，我们要将数据和索引独立开来，所以我们会采用指针。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221600887.png" alt="image-20221122160003648" style="zoom:67%;" />

#### 2.存储引用

叶子节点不再保存数据本身，而是保存指向记录的指针。

- 因为现在叶子节点保存的是指向数据文件的指针，所以我们可以根据不同的KEY建立多个索引，而每个索引的叶子层都指向同一个数据文件。
- 硬盘上的数据是否是有序的也变得不重要。因为我们使用的是指针，而叶子层的节点按照KEY排过序。

但是如果针对同一个KEY，有多个匹配的记录呢？所以我们使用第三种可选项，引用的List。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221610460.png" alt="image-20221122161010226" style="zoom:67%;" />

#### 3.引用的列表

对于每个KEY，你有一个匹配的数据项的引用的List。

- 比如对于左侧的KEY 2：有[1,1]、[1,2]、[2,1]。[PageID, RecordID]

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221614334.png" alt="image-20221122161431112" style="zoom:67%;" />

- 用引用索引的小总结：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221618835.png" alt="image-20221122161855666" style="zoom:67%;" />

- 下面是一个同时使用选项2和3的插图：
  - 左侧可以发现KEY重复存储了，对于每个KEY，不管它是否重复，都有一个记录和它关联。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221623632.png" alt="image-20221122162310443" style="zoom:67%;" />

### Clustered VS Unclustered Index

聚集索引和非聚集索引

- Clustered Index：
  - 文件中的记录是按照索引选定的键排好序的，或者大部分是排好序的。
- Unclustered Index：恰恰相反。
- 数据是否是Clustered对于查找的开销有很大的影响。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221642469.png" alt="image-20221122164233255" style="zoom:67%;" />

#### Clustered VS Unclustered Index Visualization

对于左侧Clustered的，可以发现记录的局部性更好。

- 由于数据本身基于选定的KEY排过序了，所以与相邻的KEY关联的所有的记录，在磁盘上也会相邻很近
- 而在UnClustered的情况下，与相邻的KEY关联的记录可能会随机散落在各个地方。
- 因此可以看见，Clustered的情况下，需要读写的页要更少，I/O开销更小，小得多，一个读三个页，另外一个五个页。

基本上，在一个Clustered的Index下，可以假设，在值上接近的KEY，它们对应的记录在硬盘上也会很接近，比如相邻的页，比如同一页。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221701254.png" alt="image-20221122170103029" style="zoom:67%;" />



我们这里强调的Clustered的定义是：文件中的记录是按照索引选定的键排好序的，**或者大部分是排好序的**。为什么？

- 因为可能必要的插入会使得文件不是完全排好序的，可能只是接近完美的排序
- 比如你要往蓝色的页插入更多的记录，但是蓝色的页没有更多的空间了，那么你只能另取一页，插入记录。
  - 这样不是破坏了Cluster的有序性吗？确实，但是它还是粗略地排好序的，大部分都是有序的，只有一小部分是无序的，总体来说性能还是足够好了。
  - 所以随着时间推移文件变得越来越无序的话，也许可以对文件重新排序以保持Cluster的结构。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221708849.png" alt="image-20221122170811638" style="zoom:67%;" />

#### Clustered VS Unclustered Index Pros

没什么好说的

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221712630.png" alt="image-20221122171227447" style="zoom:67%;" />

#### Clustered VS Unclustered Index Cons

- 维护起来更昂贵：
  - 需要定期更新堆文件的顺序（定期排序）
- 堆文件一般只填充了三分之二的满，以容纳将来的插入
  - 由于页是没有填满的，所以可能需要消耗更多一点的页。
  - 但是这个代价完全可以接受。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221713981.png" alt="image-20221122171355783" style="zoom:67%;" />

## B+ Tree Refinement:Variable-Length Keys

- 可变长的键

之前我们引入的定义不再适用。引入新的定义。

### Redefine Occupancy Invariant

- 对于可变长的键，Order(d)不再有什么意义。因为我们没办法确定一个固定大小的节点（页）现在可以放多少个记录了。
  - 不同的节点有不同数量的项。
  - 索引页经常要存比叶子页更多的项。
- 实践上使用物理的标准：节点（页）至少半满，以字节数计。
- 实际的系统比这个还草率。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221722489.png" alt="image-20221122172223280" style="zoom:67%;" />

### Prefix Compress Keys?

- 不考，但是知道的话很酷
- 前缀压缩。字符串有的时候会有相同的前缀，因此可以借助前缀进行压缩。将这个思想应用开
- 根节点并没有将Sarah Zhu全部Copy上去，而是Copy了可以区分左右节点的最小前缀。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221729734.png" alt="image-20221122172928527" style="zoom:67%;" />

#### Suffix Key Compression

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221731925.png" alt="image-20221122173129690" style="zoom:67%;" />

## B+ Tree Costs

### Clustered VS Unclustered Index Assumptions

几个前提假设

- 索引中的叶子节点使用的是引用
- Clustered Index的堆文件的填充率是三分之二
  - 堆文件最开始是排好序的
  - Fan-out很大
  - 索引是静态的，不会有插入和删除

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221757199.png" alt="image-20221122175732995" style="zoom:67%;" />

### Scan all the Records

遍历一遍所有的记录，不需要索引。

开销：与文件的页数成正比

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221819780.png" alt="image-20221122181944570" style="zoom:67%;" />



- 总次数：访问过的索引节点的数目，加上将数据页本体读取到内存。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221829755.png" alt="image-20221122182922518" style="zoom:67%;" />

- 范围查找：
  - 从根节点开始查找3，到达3，沿着链表指针继续查找5 7 20。5次
  - 将对应的数据页读取到内存，3次。
- **1 I/O per matching page of Records**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221833016.png" alt="image-20221122183340490" style="zoom:67%;" />

- UnClustered Index：
  - **1 I/O per matching Record**
  - 对于每个处于范围内的记录，都要跟随指针找到其对应的页，然后读取到内存里去。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221836441.png" alt="image-20221122183627200" style="zoom:67%;" />

## Summary

Data Storage部分的内容只适用于选项2和3。因为选项一的话，每个索引的叶子节点内部就是整张按照选定的KEY排好序的，天然地具有Clustered属性。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221841549.png" alt="image-20221122184108357" style="zoom:67%;" />

# CS186 Lecture 7 Buffer Management

## Where am I?

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221857700.png" alt="image-20221122185738466" style="zoom:67%;" />

## Buffer Management Levels of Abstraction

缓冲管理层：介于文件和索引管理层，以及磁盘空间管理层之间的中间层，它的角色是为更高层提供一个基于RAM的API，这样它可以从硬盘拉取页，而高层可以直接工作在RAM。

- 在缓冲区中有一系列的帧（页），可以从硬盘拉取，以便于上层访问
- 这一层，就是给更高层提供一个它们直接在内存中访问和修改页的假象。
  - 如果这一页不在内存，那么就由这一层拉进来。

- 这一层把页拉进内存的操作，又是借助底下一层给它提供的API完成的。
- 也可以说，这一层就相当于是Cache。
  - 缓存过的页不用再去硬盘读取
  - 没缓存过的页只需要第一次的去硬盘读取。

- 要解决的问题：
  - 如何处理脏页？要保证内容一致性嘛，什么时候写回去？
  - 替换策略？LRU什么什么的，缓冲区满了要替换嘛。
- 更高级的问题：
  - 对于同一页的并发操作？
  - 写回之间系统崩溃了？
  - 以后再解决

### Handling Dirty Pages

- 脏位
- 通过disk manager写回硬盘

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221910713.png" alt="image-20221122191000532" style="zoom:67%;" />

- 内存里面的情况可能是下面这样的：
  - 表的作用相当于是整个缓冲区的元数据。一个小小的数组，也在DBMS启动时分配。
  - 整个缓冲区可以看做是以Page大小为单位的内存。在DBMS启动时被Malloc分配。
  - pin count：有几个“人”在访问这一页。有几个任务在使用这个页。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221917471.png" alt="image-20221122191752237" style="zoom:67%;" />

## When A Page Is Requested...

当一个页被请求的时候...

1. 如果被请求的页没有在缓冲池里：
   1. 选择一个pin count为0的帧用于替换（pin count为0说明当前无人使用它，所以可以把它踢出去）
   2. 如果这一帧被标记为脏，那么将当前帧内的页写回硬盘，然后将帧标记为干净。
   3. 将被请求的页读取到这一帧。
2. 将刚刚读取到的页的pin count置为1，返回这一页在内存中的地址。
   - 为什么要pin？因为你把这一页加载到内存里不是无缘无故的，而是因为有人要了这一页，所以你才把它加载到内存里。
3. 如果请求能够被预言（比如连续的访问），那么可以在一次进入硬盘的时候预取多个页。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221936863.png" alt="image-20221122193613619" style="zoom:67%;" />

### After Requestor Finishes

在页的请求者完成了对页的使用之后，它需要：

1. 如果页被修改过了，将脏位置为1。
2. 尽可能快地unpin这一页。
   - 为什么尽可能快？因为如果不及时的话，会出现所有的页都pinned的情况，这个时候没有哪一页可以被替换掉(即使有替换的需求，也无法满足)。

处于池中的页可能会被请求多次，因此才需要pin count

- Pin a page:pin_count ++
- **当且仅当某一页的pin count等于0的时候，这一页才可以被用于替换掉。**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221942476.png" alt="image-20221122194222262" style="zoom:67%;" />

## Page Replacement Policy Intro

老熟人了

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221949684.png" alt="image-20221122194920474" style="zoom:67%;" />

### LRU,Clock Algorithm

- Clock Algorithm的模拟看课吧
- 伪代码：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211221956224.png" alt="image-20221122195635944" style="zoom:67%;" />
- 然后举例讲了讲LRU算法开摆的情况。
  - 也就是连续访问的情况。
  - 比如遍历一个长度为N的数组很多遍，但是缓冲区恰好长度为N-1（这种情况下MRU好点）

### MRU

Most Recently Used

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211222008802.png" alt="image-20221122200808578" style="zoom:67%;" />

### PRE-Fetch

就跟我出去洗一趟澡，会顺手拿个外卖拿个快递，再去物美买点东西带回去一样。把出去一趟的开销摊销掉。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211222011740.png" alt="image-20221122201115470" style="zoom:67%;" />

### Hybrid

实际上可能需要一个LRU和MRU的混合体

## Summary

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211222015200.png" alt="image-20221122201526941" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211222015166.png" alt="image-20221122201538965" style="zoom:67%;" />

# CS186 Lecture 8 Sorting and Hashing

## Why Sort?

为什么需要排序？

- SQL查询中可能需要去重(DISTINCT)，可能需要按需输出(ORDER BY)，可能需要分组。
- 可能你需要批量装载

问题：怎么使用1GB的RAM来对100GB的数据排序？

## Out Of Core Algorithm

- 一般的算法运行在内存中，称作In Core Algorithm。因为没办法把全部的工作都放在RAM中进行，所以叫Out Of Core Algorithm

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211231331110.png" alt="image-20221123133106859" style="zoom:67%;" />

### Single-pass Streaming

- 从输入（硬盘）读取一块到输入缓冲区（RAM）
- 将映射F(x)写入输出缓冲区（RAM）
- 每当输入缓冲区被消耗，再读入另外一块数据。
- 当输出缓冲区被填满，将其写入输出（硬盘）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211231334533.png" alt="image-20221123133444251" style="zoom:67%;" />

### Better:Double Buffering

一个优化。

使用两个线程，一个线程负责计算从输入到输出的映射F(x)。另外一个线程负责磁盘I/O的工作。

使用**两对**输入输出缓冲区，其中一对由负责计算的线程消耗输入缓冲区和填充输出缓冲区。另外一对由负责I/O的负责读入到输入缓冲区和从输出缓冲区写回到硬盘。

每当负责计算的线程消耗完毕它的输入缓冲区A，就将这个输入缓冲区A和负责I/O的线程填充的输入缓冲区B交换，让负责I/O的线程去填充刚刚被消耗到空的输入缓冲区A，让负责计算的线程去消耗刚刚被填充满的输入缓冲区B。

每当负责计算的线程填充满了它的输出缓冲区C，就将C和被负责I/O的线程写回的输出缓冲区D交换，让负责计算的线程去填满D，让负责I/O的线程去将C写回硬盘。

这个优化在后续的讨论中都适用，但为了简化问题我们不再提出。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211231339178.png" alt="image-20221123133900922" style="zoom:67%;" />

### Sorting & Hashing:Formal Specs

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211231348096.png" alt="image-20221123134853862" style="zoom:67%;" />

### Sroting:2-way

- Pass0：第0步，批处理
  1. 读入一页，将其排序，将其写回硬盘，重复这个过程。
  2. 只使用了一页的缓冲区大小
  3. 是一个不断重复的批处理过程
- Pass1,2,3...：第1,2,3,4...步，归并
  - 需要三个缓冲区，两个输入缓冲区，一个输出缓冲区。
  - 将输入缓冲区中的两个部分归并到输出缓冲区去，当输出缓冲区填满，写回硬盘，当输入缓冲区空，从硬盘读取。
  - 重复这个过程，一次归并两个，直到最后只剩下一整块完全排好序的数据。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211231413367.png" alt="image-20221123141326148" style="zoom:67%;" />

- 一个图形化的示例：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211231416746.png" alt="image-20221123141650513" style="zoom:67%;" />

我们可以将这个思想扩展：

- 假如我们可以用的RAM总共有B页缓冲区。

- Pass0：不是一次读取一页，然后排序，而是一次读取B页，然后排序，然后再写回硬盘。
- Pass1，2,3,4,5：将B页缓冲区分为1页输出缓冲区和B-1输入缓冲区，将B-1个输入缓冲区的内容合并为到输出缓冲区去，重复这个过程。
  - 合并的过程稍微详细说一下：从B-1个输入缓冲区里选出一个最小的元素，写入输出缓冲区，重复这个过程，输出缓冲区满了之后，将输出缓冲区的内容写回硬盘，然后再重复整个过程。由于每次输出缓冲区填满的时候，其中的元素都是B-1个输入缓冲区中最小的那一部分元素，因此可以保证每次写回硬盘的所有元素都是当前最小的那一页元素。如果某一个输入缓冲区空了，那就再读取一页进来。

举个例子：5页缓冲区，排序一个108页的文件

Pass 0: 每5页为一组进行排序，总共排序22组，最后一组只有3页。

Pass1：4页作为输入缓冲区，1页作为输出缓冲区。每4组合并为一组，合并后20页一组，最后一组8页。

- 举个例子：5个缓冲区，每一页5个元素，每一组20页。先从这20页中读取4页放入4个输入缓冲区，从这4个输入缓冲区中选出最小的5个元素，填满输出缓冲区，写回硬盘，然后再从剩下的输入中选出5个最小的，以此类推。如果中途有一个输入缓冲区空了，那么从剩下的16页中挑一页读进来。

Pass2：以此类推

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211231428723.png" alt="image-20221123142801489" style="zoom:67%;" />

## Hashing

- 很多时候我们并不需要排序，我们并不需要一定要让2出现在1的后面，3出现在2的后面。我们可能只需要，接近的值挨在一起就行了，在一个组就行了，组里面的顺序没那么重要。这就是哈希擅长的。
- 问题在于：怎么在RAM外做哈希？

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211231446083.png" alt="image-20221123144621860" style="zoom:67%;" />

### Divide

- 使用哈希函数来将记录分组：每个组就像哈希表的Bucket，有相同哈希值的记录会在同一个Bucket。
- 利用哈希，将一大块无组织的文件，以相似记录聚在一起（哈希值相同或者相似，比如余数相同）地形式连续的写在硬盘上

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211231532972.png" alt="image-20221123153151556" style="zoom:67%;" />

#### Two Phases:Divide

- RAM中有B页缓冲区，1页作为输入缓冲区，剩余B-1页作为输出缓冲区，每一页输入缓冲区就类似Hash Table的一个Bucket。
- 重复以下过程：
  1. 从硬盘读入一页到输入缓冲区。
  2. 对于缓冲区中的每一个记录：计算其哈希值，放入第$HashValue\  \%\  (B-1)$个输出缓冲区（和哈希表是一样的），如果输出缓冲区满了写回硬盘。
  3. 回到1。
- 需要注意：
  1. 假如我们读入了两页，但是我们可能需要写入多余两页。因为同一页的多个记录会分到不同的输出缓冲区去，比如下面的Page1的三个记录分到了两页去。哈希函数一般会创造比原始存储时更多的页数。
  2. **即使在RAM中处于同一个输出缓冲区的记录，它们目前在磁盘上也不一定是连续存储在一起的，因为可能是来自不同的页。**这也是下一个阶段要解决的问题，将哈希值相同或相似的记录连续存储在磁盘上。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211231540727.png" alt="image-20221123154040492" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211231550704.png" alt="image-20221123155006495" style="zoom:67%;" />

# CS186 Lecture 9 Iterators and Joins

## Schema For Examples

接下来的例子中将会使用到的一些符号：

- $[R]$：为了保存R所消耗的页数
- $P_R$：R的每一页中的记录数
- $|R|$：R中总的记录数
  - $|R| = [R] * P_R$

将会用到两张表：

- Reserves(sid:int, bid:int, day:date, rname: string)。预定记录表
  - b：Boat
  - s：Sailor
  - 一千页，每页一百个记录
- Sailors(sid:int, sname:string, rating:int, age:real)。船员（水手）表
  - 500页，每页80个记录。

今天我们讲述DBMS是如何实现Join的，如何实现表的联结的算法。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211231930666.png" alt="image-20221123193029420" style="zoom:67%;" />

## Loop Join

### Simple Nested Loops $\theta$ Join

- 最简单的，使用两重的嵌套循环来实现的。

伪代码：

```c
for(关系R中的每个记录r) {
    for(关系S中的每个记录s){
        if(<r,s>满足条件) {
            将<r,s>加入输出缓冲区
        }
    }
}
```

- 这是很低效的，因为对于R中的每一个记录，它都要与S中的每一个记录进行配对，然后检查是否满足条件。
  - I/O次数：$[R] + |R|*[S]$，读取R的页数+对于R的每个记录还要读取每个S的页数次（I/O都是按页为单位读取的，不是按记录）。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211231946783.png" alt="image-20221123194632468" style="zoom: 50%;" />

### Page Nested Loop Join

然后我们可以做一些改进：因为每次读取一个记录，实际上会把记录所在的那一页全部读取进来。我们为什么不直接以页为单位来处理？

- 伪代码：

```java
for(R 中的每一页RPage) {
    for(S中的每一页SPage) {
        for(RPage中的每一个记录r) {
            for(SPage中的每一个记录s) {
                if(<r,s>满足条件) {
                    将<r,s>加入输出缓冲区
                }
            }
        }
    }
}
```

- 这样，R中的每一个页都只被加载进了内存一次，**对于R的每一页（此前是对于R的每一个记录），S中的每一页读取一次**。
- I/O数：$[R] + [R]*[S]$。
- 可以看出比之前好很多了
- 在这个I/O数的计算中，起决定性因素的是$N*[S]$
  - 对R每个记录都需要读取全部S：$N = |R|$，非常贵
  - 对R的每一个页都读取全部S：$N = [R]$，比上一种情况好很多，但还是很贵。
  - 如果我们能够减少N？
- 假定我们现在有B页的RAM缓冲区，我们想要减少我们读入S的次数。
  - 如果可以尽量填充尽可能多的R的页在内存中？(Try to fill as many pages of R as we can in memory)
  - 这样对于RAM中的一整块R（由很多个R的页构成），我们可以读取全部的S。
  - 也即，这样，我们就是对每一大块R，读取S的一页。
  - 这就是Block Nested Loop Join背后的思想

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211231957517.png" alt="image-20221123195705230" style="zoom: 50%;" />

### Block Nested Loop Join

伪代码：

```java
for (由RAM中的B-2个R的页构成的一个块Block) {
    for(S的每一页SPage) {
        for(Block中的每一个记录r) {
            for(SPage中的每一个记录s) {
                if(<r,s>满足条件) {
                    将<r,s>加入输出缓冲区
                }
            }
        }
    }
}
```

- I/O次数：$[R] + \lceil {\frac{[R]}{B-2}}\rceil * [S]$

- 很大的提升，很好的算法，许多人使用的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211232043166.png" alt="image-20221123204330850" style="zoom: 50%;" />

### Changing the Join Order

- 目前我们都是让R作为外层，让S作为循环的里层。
- 但是实际上也是可以反过来，也即改变联结的顺序
- 改变联结的顺序也确实会对我们联结的开销造成很大的影响：以最简单的联结算法为例
  - R在外层：$[R] + |R|*[S]$。对R的每一个记录，读取全部的S的页
  - S在外层：$[S] + |S|*[R]$。对S的每一个记录，读取全部的R的页。
  - 后者比前者少了大约一千万次的I/O

### Index Nested Loops In Join

- 更加复杂，但这不意味着它总是更好的。Block Nested Loop Join是一个很好的算法，人们也经常因为它是最便宜的（I/O开销小）而使用它。

假定我们给S建立了一个索引，并且我们的联结是等值联结$r_i= s_j$。

那么，对于R的每个记录r，我们借助S的索引，找到一个记录s，这个记录s满足$r_i = s_j$（即等值联结的等值条件），然后把$<s,r>$加入输出缓冲区（找到多个s，那就加入多个）。

- 而构成Index的B+树，一般都是很矮的，可能高度只有2到4。所以查找的这个过程，开销基本忽略不计。

伪代码：

```java
for(R中的每个记录r) {
    for(通过S的索引找到的满足r[i] = s[j]的每个记录s) {//也即通过索引查找到的满足等值联结条件的
        将<r,s>加入结果集
    }
}
```

- I/O开销：分为两部分，读进R的每一页，加上对于R的每一个记录进行的S的查找。
  - $[R] + |R| * $通过索引查找S的记录的开销(记作Index吧)
- Index的开销也要分情况讨论：
  - 如果索引采用结构是选项1（叶子节点直接存数据），那么开销就是从根节点到叶子节点的开销
  - 如果索引采用的结构是选项2（叶子结点存的是数据的引用）：
    - 借助索引查找到指针（甚至可能有多个，因为可能有多个匹配的s），开销是从根节点到叶子节点。
    - 借助指针索引记录本身：
      - 对于Clustered Index：每一页匹配的记录一次I/O（因为这种情况下所有的s会在同一页）
      - 对于UnClustered Index：每一项匹配的记录一次I/O（因为这种情况下每个s随机分布）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211232053266.png" alt="image-20221123205324982" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211232107433.png" alt="image-20221123210744160" style="zoom:67%;" />

## Sort-Merge Join

假如我们有两个排好序的文件，我们可以利用文件的有序性来完成高效的联结

两步：

- 第一步，对R和S按照选定的Join Key排序
- 第二步，扫描R和S，找出任何一对匹配的r和s。
- 许多时候我们的表是已经排好序的，我们就可以避免第一步，直接进入第二步

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211232120744.png" alt="image-20221123212034483" style="zoom:67%;" />

- 代码：模拟的话去看课，代码还蛮好懂的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211232133452.png" alt="image-20221123213306157" style="zoom:50%;" />

- 开销：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211232134885.png" alt="image-20221123213418619" style="zoom: 50%;" />

如果我们先联结再排序？如果我们想要我们的结果是排好序的，为什么不先联结再排序呢？

- 先用Block Nested Loop Join来联结，然后对结果进行排序。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211232138391.png" alt="image-20221123213800171" style="zoom:67%;" />
- 对比先对R和S排序，然后再Merge Join。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211232138333.png" alt="image-20221123213835104" style="zoom:67%;" />

- 可以看到先排序再Join更好。

但是我们并不是所有的时候都关心联结后的顺序，所以，Block NLJ还是更高效。选择哪个实际上取决于你想要做什么。

### Sort-Merge Refinement（？）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211241949133.png" alt="image-20221124194923595" style="zoom:67%;" />

## Naive In Memory Hash Join

- B页缓冲区，1页输入S，一页输出，其余B-2页用于缓存R
- 假定R是一个小的关系，小到能够装入内存
- 于是可以：
  - 将R全部加载进内存，为其建立一个在内存中的Hash Table
  - 扫描S，同时在哈希表中查找匹配的R
- 内存要求：保证B-2页内存能放入基于R构建的哈希表。
- 如果R或者S都在内存里放不下？
- 基本的思想就是，通过使用一个很精致的哈希函数，使得处于同一个Bucket中的记录，在等值键上的取值都是相同的（或者说，记录都是完全一样的记录），不会发生哈希碰撞。然后在联结的时候，同一个Bucket内的记录，可以一次性批量联结掉。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211241954956.png" alt="image-20221124195439683" style="zoom:67%;" />

### Grace Hash Join

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251349037.png" alt="image-20221125134859767" style="zoom:67%;" />

- 伪代码
- 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251419327.png" alt="image-20221125141926089" style="zoom:67%;" />

# CS186 Lecture 10 Relational Algebra

## Where Am I?

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251452610.png" alt="image-20221125145234260" style="zoom:67%;" />

## 关系代数

- 今天这一节，我们的关系代数都是基于集合语义的，也就是元素之间要有互异性，所以要去重。

- 投影：$\pi$，选择一个表的某几列，组成一个新的表输出。 SQL的`SELECT`
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251513571.png" alt="image-20221125151352333" style="zoom:67%;" />
  - 也可能，基于单重集的语义，要对表去重：去掉了两个年龄（对于性别作为一列，那不管原来表有多少行，投影完就剩一列两行了）。
    - 实际系统并不会自动做这个去重的工作，我们只是为了这节课的情况。
    - 很简单，万一你要统计有多少个男多少个女？那你对性别列投影完了还去重，一男一女？
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251515803.png" alt="image-20221125151542572" style="zoom:67%;" />
- 选择（Selection,$\sigma$）：根据给定的谓词筛选出符合条件的列，与SQL的`WHERE`对应。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251519551.png" alt="image-20221125151901312" style="zoom:67%;" />
- 将选择和投影组合起来：**一定要先选择，后投影。先选择出符合条件的完整记录，再从完整记录里面投影出你需要的列**。
  - 如果反过来的话，先投影，那么得到的表是不完整的表，选择的时候，用于筛选的那一列可能根本就没在这个不完整的表里（要么你在投影的时候把用于筛选的那一列也加进去，但是这样在最终结果可能需要多做一次投影，把加进去的这个列给去掉）。
  - 比如要选择Rating大于8的船员的名字，如果先投影名字的话，得到的表里哪来的Rating？怎么根据Rating筛选？
  - 所以先从原表选择Rating大于8的记录，这时候得到的表具有所有的列，因此不会出现想选择的列不在表里的情况。而反过来，就会出现想筛选的列可能根本不在表里。（或者先投影名字和rating，然后使用Rating筛选完之后，再在外面套一层投影把Rating这一列去掉）
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251527467.png" alt="image-20221125152733212" style="zoom:67%;" />
- 并集（Union）：要求输入的两个表，它们的布局完全一致（列数，对应列的类型都一样）。
  - SQL：`UNION`去重，`UNION ALL`不去重。
  - 因为两个表的并集，其实就是把两个表上下拼接在一起，所以两张表的结构布局要一样。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251540230.png" alt="image-20221125154058982" style="zoom:67%;" />
- 差集（Set Difference,-）：输入两个**兼容的**集合S1和S2，S1-S2就是保留S1中只在S1里面的元素。不要求去重，因为应该不会有重复。
  - 参与差集和并集运算的两个集合，都必须有完全一样的模式。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251545313.png" alt="image-20221125154553068" style="zoom:67%;" />
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251548128.png" alt="image-20221125154834872" style="zoom:67%;" />

- 叉积（Cross Product）：$S1\times S2$：S1的每一行和S2的每一行进行配对。联结的中心运算。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251559123.png" alt="image-20221125155944846" style="zoom:67%;" />
- 重命名（Rename，$\rho$）：给列和表重命名，表可以是输入，也可以是运算的输出，列可以是任意一个表的列。关系代数是不要求名字的。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251602942.png" alt="image-20221125160231686" style="zoom:67%;" />
- 交集（Intersection）：要求输入的两个表必须是兼容的（布局完全一致），对应SQL的`INTERSECT`。
  - $S_1\cap S_2 = S_1 - (S_1 - S_2)$
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251604029.png" alt="image-20221125160426779" style="zoom:67%;" />
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251608261.png" alt="image-20221125160841022" style="zoom:67%;" />
- 联结运算$\Join$，其实就是先叉积$\times$，然后再用谓词筛选$\sigma$
  - Theta Join：使用谓词Theta的联结运算。
    - 等值联结：谓词Theta是一个相等连词。
      - 自然联结：在所有匹配的列名上做等值联结。
- 还有个GROUP BY：$\gamma$，知道就行了。

### An Aside:Duplicate elimination

- 实际系统通常不会自动去重，因为：
  - 有时候，需要追踪重复的，比如你要计算总数。
  - 有时候，你根本不关心有没有重复。
- 去重并不是没有代价的，它需要算法，可能需要排序或者哈希，而这有开销。这是理想与现实的差距。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251635019.png" alt="image-20221125163521766" style="zoom:67%;" />

# CS186 Lecture 11 Query Optimization: Plan Space

## Where Am I?

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251926827.png" alt="image-20221125192606723" style="zoom:50%;" />

## System R Optimizers

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251950965.png" alt="image-20221125195003709" style="zoom: 50%;" />

### Query Parsing

- 查询解析器：
  - 检查语句的语法正确性，检查权限（用户是否有访问表的权限）
  - 生成一棵分析树。
  - 其他的不用管。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211251949033.png" alt="image-20221125194940803" style="zoom:50%;" />

## Cost-based Optimizer

基于开销的查询优化器

- 一次优化一个代码块
- 使用Catalog中的状态来估计每个代码块的最小开销
- 我们在这里给出的，并不是真的最优的优化方法，但是它已经足够优化，性能足够好，好到足够高效满足要求。

### Overview

每一个查询代码块都能够被转换成关系代数的形式，而关系代数的形式可以使用一棵树来表示。树中的每一个运算符都有不同的实现的选择，比如Join，使用哪一种Loop Join？此外，运算的重新排序也是需要考虑的，使用更优的计算顺序可能会排除更多的无关列。

### Query Optimization: The Componentssa

优化查询，有三个正交的方面：因为它们是正交的，所以我们可以单独考虑每一个方面

- Plan Space，计划空间。
  - 对于一个给定的查询，有多少种查询的计划可以用于实现它，这些计划分别是什么？
- Cost Estimation
  - 某一个被选定的计划的开销有多大？
- Search Strategy
  - 在计划空间中，我们怎么查找？

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211252007849.png" alt="image-20221125200702594" style="zoom:67%;" />

### Query Optimization: The Goal

查询优化的目标：

- 理想状态下：找到最优解嘛，找到实际开销最小的计划
- 事实：找到估算开销最小的计划，以及避免选中坏的计划。这已经很好了，大部分时候完全是没问题的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211252013580.png" alt="image-20221125201308347" style="zoom:50%;" />

这节课：只讲规划空间

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211252015619.png" alt="image-20221125201527401" style="zoom:67%;" />

### Relation Algebra Equivalence

关系代数中（使用谓词进行的）选择运算的等价性：

- $\sigma_{c_1\and c_2 \and \ldots \and c_n}(R) = \sigma_{c_1}(\sigma_{c_2}(\sigma_{c_...}(\sigma_{c_n}(R))))$：从R中筛选出同时满足条件C123...n的记录，等价于先从R中筛选出满足Cn的记录，然后从输出中筛选出满足C~n-1~的记录，然后从输出中筛选出满足C~n-2~的记录，以此类推。

- $\sigma_{c_1}(\sigma_{c_2}(R)) = \sigma_{c_2}(\sigma_{c_1}(R))$：先从R中筛选出满足C2的记录，然后从结果中筛选出满足C1的记录，等价于先从R中筛选出满足C1的记录，然后从结果中再去筛选满足C2的记录。

关系代数中，投影运算的等价性：

笛卡尔积的交换律和结合律：

- $S\times R = R \times S$
- $(R\times S) \times T = R \times (S \times T) $。

#### Are Joins Associative and Commutative?

联结运算是否具有结合律和交换律？

- 因为联结运算实际上就是笛卡尔积加上选择运算，而笛卡尔积是满足交换律和结合律的，所以我们可能觉得联结也满足交换律和结合律
- 实际上不是这样的：

假定我们有三张表$R(a,z),S(a,b),T(b,y)$：

- 不等价：$(S\Join_{b = b} T)\Join_{a = a}R \neq S\Join_{b = b} (T\Join_{a = a}R) $
  - 这是不合法的，因为T和R并不是都具有名为a的列，没有办法将T和R基于a列来联结。实际上这会导致一个R和T的叉积。
- 不等价：$(S\Join_{b = b} T)\Join_{a = a}R \neq S\Join_{b = b} (T\times R) $
  - 显然不等价，去掉了基于列a相等的条件，右侧叉积会多出很多条记录来。
- 等价：$(S\Join_{b = b} T)\Join_{a = a}R \neq S\Join_{b = b \and a = a} (T\times R)$
  - 将条件换了个等价的地方放置。

所以，交换律和结合律在Join上并不是永远成立的。

### Some Common Heuristics

- 只要我们有相关的列，就立刻使用选择运算。
  - 因为这样会在更早期就减少表的大小，越早筛选出符合要求的记录，后续做的多余的工作就越少（因为不用对无关的记录做那些工作了）
  - 比如Join的开销是和两张表的大小之积成正比的，所以如果能在Join之前使用谓词和选择运算提前筛去一部分无关的记录，那么相比先Join后选择，在Join的时候开销就会更小。
  - 因此，尽量把选择运算放在树的底层进行。
- 举例：要选择bid=100且rating大于5的记录(的名字)：
  - 开销大的做法：先联结，然后再筛选:$\sigma_{bid = 100 \and rating > 5}((Reserves \Join_{sid = sid} Sailors))$
  - 开销更小的做法：先在Sailor中选出rating大于5的（但是不知道bid是多少的）记录，然后在Reserve中筛选出bid = 100（但是rating可能不满足要求）的记录，然后再将筛选后的表联结（得到两个条件都满足的）：$(\sigma_{bid = 100}(Reserves)) \Join_{bid = 100} (\sigma_{rating > 5}(Sailors))$。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211252109502.png" alt="image-20221125210929221" style="zoom:67%;" />



- 相似的，记录可能会很大，会有很多列很多个属性，但是我们并不是需要所有的属性，因此我们在运算的时候，保留我们实际上需要的列即可。
  - "project away all the columns except for the ones that you need for the query"。
  - 这样做可以减少单个记录的大小，可以节省更多空间，因而一页可以放入更多的数目，于是可以减少表的尺寸（页的数目），于是可以减少Join的开销（Page Nested Loop Join）。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211252119664.png" alt="image-20221125211911410" style="zoom:67%;" />

- 避免笛卡尔积，有的选的情况下尽量使用Theta Join而不是叉积。因为叉积的结果太大了
  - 并不永远是最好的，但是一般都不差。什么时候好后面再细说吧

### Physical Equivalence

对于查询分析树中的那些符号（$\sigma$、$\pi$）之类的，能够用哪些实际的算法替换进去？

基本的表访问，对于单表的选择和投影：Heap scan和Index Scan（如果有对应的索引）

等值联结：

- 各种Nested Loop Join
- Sort-Merge Join
- Grace Hash Join

非等值联结：Block Nested Loop Join

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211252141562.png" style="zoom:67%;" />

### Schema for Examples

接下来用到的例子的一些预设条件：

Reserves和Sailors是老东西了

有5页用于Join

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211252158819.png" alt="image-20221125215845511" style="zoom:67%;" />

### Motivating Example Plan 1 Cost

分析树和SQL语句是完全对应的：

- 先扫描两张表，然后将它们等值联结，得到一张新的表。
- 在新的表上依次利用两个条件进行筛选，然后投影运算选择名字列，得到需要的结果。

开销：

- 假定我们采用Page Nested Loop，Sailor在外层。
- 对于Sailor的每一页，我们都要扫描Reserves的每一页记录，500 * 1000
- 对于Sailor本身的500页，还要读取500次，合计500 * 1001 = 500500次。Sucks

可以优化的地方：

- 将选择运算推到树的底层

- 使用更好的Join
- 是否有索引？
- 将投影推下去

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031144445.png" alt="image-20221203114356177" style="zoom:67%;" />

#### Selection PushDown

- 将Rating>5的条件Pushdown，由于总共有10个等级，所以我们可以认为这样可以过滤掉一半的记录。
- 开销：读取全部的500页以用于过滤，去掉一半。对于过滤后得到的250页的每一页，读取Reserves的每一页。500 + 250 * 1000

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031155926.png" alt="image-20221203115534670" style="zoom:67%;" />

#### More Selection PushDown

如果把另外一个条件也pushdown？

- 实际上并不一定会起到很大的优化作用。因为开销实际上是一样的。需要读取的页数还是一样的，I/O数是一样的。

- ```c
  for(过滤后的R中的每一页RPage) {
      for(S中的每一页SPage) {
          过滤掉不满足条件的记录
          for(RPage中的每一个记录r) {
              for(SPage中的每一个记录s) {
                  if(<r,s>满足条件) {
                      将<r,s>加入输出缓冲区
                  }
              }
          }
      }
  }
  ```

  

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031202039.png" alt="image-20221203120215781" style="zoom: 67%;" />

### Join Ordering

此前我们是将Sailors放在循环的外层，现在我们将Resers放在循环的外层，同时将bid = 100的条件pushdown。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031350943.png" alt="image-20221203135044655" style="zoom:67%;" />

开销：

- 扫描全部的总共1000页的Reserves来过滤bid=100的记录。由于总共100个不重复的bid，我们只需要其中bid=100的记录，因此估计其占全部的百分之一。
- 过滤之后，就剩余1000的百分之一，即10页。对于这10页的每一页，读取Sailors的每一页，10 * 500。
- 1000 + 10 * 500 = 6000 I/O。显著的优化

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031351012.png" alt="image-20221203135135744" style="zoom:67%;" />

### Materializing Inner Loops

物化

在实现联结之前先缓存一个已经过滤过的版本的Sailors，记作Mat。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031400559.png" alt="image-20221203140042278" style="zoom:67%;" />

开销：

- 读取1000页的Reserves，过滤得到10页。
- 读取500页的Sailors，过滤得到250页并写入Mat。
- 对于10页Reserves的每一页，读入250页的mat。
- 1000 + 500 + 250 + 10 * 250 = 4250 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031407153.png" alt="image-20221203140706856" style="zoom:67%;" />

### Joining Ordering Again

如果我们再交换一下内外层？少了240次。还是略微不可忽略的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031412828.png" alt="image-20221203141214548" style="zoom:67%;" />

### Join Algorithm

联结的算法也对我们的开销有很大的影响。假如我们不用Page Nested Loop Join，用Sort-Merge？

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031424870.png" alt="image-20221203142425574" style="zoom:67%;" />



用Block Nested Loop Join

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031429411.png" alt="image-20221203142923144" style="zoom:67%;" />

## Projection Cascade & Pushdown

投影级联

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031517660.png" alt="image-20221203151754405" style="zoom:67%;" />

## Index

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031528608.png" alt="image-20221203152843321" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031528672.png" alt="image-20221203152859380" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031529955.png" alt="image-20221203152913651" style="zoom:67%;" />

# CS186 Lecture 12  Query Optimization: Costs & Search

## Plan Space

- 优化的单位：Query Blocks。将查询划分为查询块，然后一次优化一小块子查询。

- 首先考虑子查询是否是相关查询，如果不是相关查询，那么子查询只需要计算一次，将计算的结果物化，用于后续计算。相关查询则像函数调用。
- 对于每个块，考虑的计划是：
  - 如果将Join画成一棵分析树的形式，它是一棵左边深的树，并且：
    - 每个节点的右分支总是一个基础表（来自FROM子句的某个表）
    - 需要考虑所有可能的Join Order和Join Method。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031633266.png" alt="image-20221203163308951" style="zoom:67%;" />

## Schema For Examples

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031637275.png" alt="image-20221203163725987" style="zoom:67%;" />

# CS186 Lecture 13 Transactions & Concurrency I

## Where Am I?

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031721496.png" alt="image-20221203172124218" style="zoom:67%;" />

## Concurrency Control & Recovery

并发控制和恢复总共分为三部分

- 第一部分：并发控制
  - 在多用户并发工作的场景下的正确且快速的数据访问
  - 提供有序的假象的无序处理。类似OS调度对用户提供的假象一样。多个事物其实是穿插着发生的，而不是一个接着一个，但是我们会对用户提供这样的假象。
- 第二部分：恢复。关于容错，当DBMS崩溃的时候需要恢复。
  - 没什么好说的。文件系统也需要
- 这两部分都是为了减轻写APP的程序员的负担。
- 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031724111.png" alt="image-20221203172427845" style="zoom:67%;" />

为什么需要并发？为了吞吐量和延迟

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212031930141.png" alt="image-20221203193004800" style="zoom:67%;" />

## Transaction

### What is a Transaction?

![image-20221203200425030](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212032004310.png)

###  Our Transaction Model

- 在后续的Slide中，Transaction缩写为Xact。
- DBMS视角下的事务，就是一系列的对于一个或多个数据库实例的读或者写，是一批必须作为原子单元提交或舍弃的作业。
- 事务管理器负责控制事务的执行。
- DBMS是不知道程序的逻辑的，从DBMS的视角来看，它只能看到来往于DB的数据读写动作。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212032006332.png" alt="image-20221203200619069" style="zoom:67%;" />

### ACID:High-Level Properties of Transactions

事务的高级属性：

A：原子性。事务中的所有的动作，要么全发生，要么全部不发生。

C：一致性。如果事务开始前DB是一致的，那么事务结束后，DB也需要是一致的。

I：隔离性。每个事务的执行要隔离于其他事务的执行。

D：持久性。如果一个事务提交，那么其效果是持久化的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212032011396.png" alt="image-20221203201107123" style="zoom:67%;" />

#### Isolation(Concurrency)

- DBMS会将许多事务的操作穿插进行（就好像OS的时间片轮转一样，只有这样才有每个事务都在同时进行的假象）
  - 操作：指对数据库实例的读写。
- DBMS会保证两个事务不会相互干扰。
- 每个事务的执行都好像只有它们自己在执行。
  - 并发的访问对事务的表现没有影响。
  - 或者说并发执行事务的实际效果，要和将所有的事务串行执行（先事务a,然后b,然后c...）的效果完全一致。
  - 用户或者程序员视角来看，他们的事务是单独执行的（隔离状态下执行的）。不需要去考虑其他并发的事务的影响（而是写DBMS的人要考虑的）。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212032018208.png" alt="image-20221203201820925" style="zoom:67%;" />

举了一个例子：没什么好说的，和OS差不多。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212032030967.png" alt="image-20221203203018672" style="zoom:67%;" />

#### Atomicity And Durability

一个事务的结果只能是以下两种结局之一：

- 在完成事务的所有操作之后提交。
- 在执行了事务中的一些操作之后终止（或者被DBMS终止）
  - 或者在进行事务的过程中系统崩溃，当做终止来对待。

一个事务的两个关键的属性：

- 原子性：要么执行全部操作，要么全部都不执行。
- 持久性：一个已经提交的事务的效果必须能够扛过故障（即使发生故障也要保留下来）。

DBMS通常通过记录所有操作来确保上述内容：

- 撤销失败或者终止的事务的操作
- 重做所有在系统崩溃时还没被写入磁盘的已提交事务的操作。
- 这一部分会在Recovery讲。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212032038266.png" alt="image-20221203203833986" style="zoom:67%;" />

举了一个例子：**DBMS应该确保一个部分执行的事务的更新不被反映出来**。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212032052232.png" alt="image-20221203205249953" style="zoom:67%;" />

#### Transaction Consistency

事务一致性

事务必须保持DB的一致性

- 给定一个一致的DB状态，事务产生另外一个一致的DB状态。

DB一致性用一系列的声明性的完整性约束来表达。

破坏完整性约束的事务会被终止。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212032106398.png" alt="image-20221203210608105" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212032110707.png" alt="image-20221203211037421" style="zoom:67%;" />

## Concurrency Control

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212032114608.png" alt="image-20221203211405347" style="zoom:67%;" />

### Transaction Schedules

一个事务调度是一个来自一个或多个事务的对数据的操作的序列。

操作包含：事务的开始和结束、读和写、事务的提交和终止

按惯例我们只包含已提交事务，省略事务的开始和结束。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212032116176.png" alt="image-20221203211625884" style="zoom:67%;" />



定义：

- 串行调度：每个事务从开始运行到结束，中间不能有任何其他的事务的干扰（即便这个事务占用的是完全无关的资源）
- 两个调度是等价的，当前仅当：
  1. 两个调度包含相同的事务
  2. 每个事务个体内的操作的顺序是一样的
  3. 两个调度都能使得DB处于同样的最终状态。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212032150104.png" alt="image-20221203215044828" style="zoom:67%;" />



Serializability：可串行化。

- 定义：如果一个调度S与某个串行化的调度等价，那么S就是可串行化的

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212032155872.png" alt="image-20221203215529643" style="zoom:67%;" />

举例：

S1：串行的调度，事务T1先，事务T2后。先转账，后加利息。

S2：也是串行的调度，事务T2先，事务T1后。先加利息，后转账。

S3：不是串行的调度，T1和T2的操作是穿插着执行的。但是它和S1是等价的，所以S3是可串行化的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212032158601.png" alt="image-20221203215827300" style="zoom: 50%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212032158044.png" alt="image-20221203215844750" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212032159060.png" alt="image-20221203215917948" style="zoom:50%;" />

### Conflicting Operations

用于检查属性“让数据库处于相同的最终状态”的技巧。

因为要判定两个调度是否相等看三点：

1. 是否包含相同的事务
2. 在两个调度内，单个事务内的操作顺序是否相同
3. 两个事务是否让DB处于相同的终态。这一点是困难的，前两点是简单的。

定义：两个操作是冲突的，如果它们：

- 来自不同的事务
- 对同一个对象操作
- 至少其中一个是写操作

**不冲突的操作的顺序对DB的最终状态没有影响**

- 所谓不冲突，就是三个条件不全都满足。比如两个事务不对同一个对象操作，那么它们的顺序对DB的最终状态没有影响。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212040933257.png" style="zoom:67%;" />

### Conflict Serializable Schedules

可串行化冲突调度

- 定义：两个事务是冲突等价的，如果它们：
  - 包含相同事务的相同操作
  - 并且每一对冲突操作的顺序都一样。
- 定义：调度S是冲突可串行化的如果：
  - S与某个串行化调度冲突等价。
  - 也蕴涵S是可串行化的。

注意：可串行化调度不一定是冲突可串行化调度。



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212040939663.png" alt="image-20221204093926434" style="zoom:67%;" />

举几个栗子：

- **一个调度S是冲突可串行化的，如果**：你可以通过交换**连续的不冲突的不同事务**的操作来将S转换成一个串行调度。
  - 可以交换的操作必须来自不同的事务（废话，肯定你不能改变同一个事务内的操作顺序啊）
  - 可以交换的操作必须是连续的（时间上相邻的）。
  - 可以交换的操作必须是不冲突的（交换冲突操作会改变DB的最终状态）。

- 事务T1：用红色粗体**R(A)W(A)R(B)W(B)**，事务T2：R(A)W(A)R(B)W(B)
- 这两个事务的并发调度是这样的：**R(A)W(A)**==>R(A)W(A)==>**W(B)W(B)**==>R(B)W(B)
- 注意到第二步的W(A)和第三步的W(B)是不冲突的（操作不同的资源，一个写A一个写B），将其交换。
- 交换过后变为**R(A)W(A)**==>R(A)**W(B)**==>W(A)**W(B)**==>R(B)W(B)。第二步的R(A)**W(B)**不冲突，交换。
- 交换过后变为**R(A)W(A)**==>**W(B)**R(A)==>W(A)**W(B)**==>R(B)W(B)。第三步的W(A)**W(B)**不冲突，交换。
- 交换过后变为**R(A)W(A)**==>**W(B)**R(A)==>**W(B)**W(A)==>R(B)W(B)。第二步的R(A)和第三步的**W(B)**，交换。
- 得到最终的串行化的调度：**R(A)W(A)**==>**W(B)W(B)**==>R(A)W(A)==>R(B)W(B)

第二个例子不能交换，因为写冲突。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212040959302.png" alt="image-20221204095928085" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041014245.png" alt="image-20221204101448038" style="zoom:67%;" />

### Conflict Dependency Graph

冲突依赖图：

- 每个事务一个节点。
- 从T~i~到T~j~的箭头：
  - T~i~的一个操作Oi和Tj的一个操作Oj冲突，并且在调度中Oi发生在Oj之前

**一个调度是冲突可串行化的，当且仅当其依赖图不存在环。**

- 举个例子就明白了

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041030082.png" alt="image-20221204103009851" style="zoom:67%;" />

举个依赖图存在环的例子：如下图 

- 假定我们要把这个调度转换为串行的调度，那么就有两种可能，T1==>T2，或者T2==>T1

- 首先T1的W(A)和T2的R(A)冲突，所以我们会有一条从T1节点指向T2节点的箭头A，并且为了保证T1先写A，T2后读A，在串行化的调度中，事务T1要在T2的前面（这也是箭头指向的意义，箭头指向在串行化调度中顺序靠后的事务）。

- 其次T2的W(B)和T1的R(B)冲突，并且W(B)在前，所以会有一条从T2指向T1的箭头B。这个箭头表明在串行化的调度中，事务T2要在事务T1的前面。
- 于是在这里我们就发现，在将这个调度转换为串行调度的过程中，发生了冲突。我们不可能同时使得，T1在T2的后面且T2在T1的后面。
- 而依赖图存在环就是这个意思。假如T1==>T2==>T3==>T1构成一个环，这个依赖图就告诉我们，T1要在T2的前面，T2要在T3的前面（所以T3会在T1的后面），**然后T3又要在T1的前面**，这是做不到的。依赖图存在环，就是这种情况。
- **因此当依赖图存在环的情况下，调度不可能是冲突可串行化的**。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041037693.png" alt="image-20221204103732476" style="zoom:67%;" />



View Serializability：提了一下，没太听懂。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041052415.png" alt="image-20221204105211141" style="zoom:67%;" />

Some Notes：

- View Serializability相当于是冲突可串行化的超集，它允许更多的调度，但是更加难高效地执行，所以通常我们使用冲突可串行化。
  - view serializable is like a super set of conflict serializability it allows some more schedules but it's a lot more difficult to enforce efficiently so we typically deal with conflict serializability in this class
- neither of these definitions actually gives you all the serializable schedules but that's because they don't actually know what you're doing on the slides right?
- 使用冲突可串行化是因为它是正确的并且可以有效地执行。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041054912.png" alt="image-20221204105453661" style="zoom:67%;" />

# Lecture 15 Transactions & Concurrency II

## 2 Phase Locking(2PL)

最常用的用于强制执行冲突可序列化的方案。

属于是悲观锁。

- 处于发生冲突而使用的锁，所以是会付出一定代价的，所以会低效一点，但是没关系，因为我们确实试着尽量保守以避免冲突。
- 有一些其他可选的方案使用多版本的数据并且乐观地让事务往前进行。一旦侦测到冲突发生就立即中止（而不是尽力避免冲突）。
  - 不在讨论范围内。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041648656.png" alt="image-20221204164758776" style="zoom:67%;" />



介绍两阶段锁的两个重要的规则：

- 在读之前，事务必须获取一个共享锁（S Lock，Shered Lock）。
- 在写之前，事务必须获取一个独占锁（X Lock）
- 事务在释放掉任何锁之后，不能再获得新的锁。
- 从锁兼容性矩阵来看，两个事务可以在同一个资源上共享一个S锁，但是一旦一个事务在一个资源上获取了一个独占锁，任何其他事务都不能在这个资源上获取任何种类的锁。observe that two transactions can have the same it can have a shared lock on a resource but if a transaction has an exclusive lock on a resource no other transaction can have any type of lock on it。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041655844.png" alt="image-20221204165504607" style="zoom:67%;" />



**2PL保证了**冲突可序列化，但是它不阻止级联中止。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041659241.png" alt="image-20221204165948048" style="zoom:67%;" />

### Why 2PL Guarantees Conflict Serializability

当一个正在提交的事务到达其获取锁阶段的结尾时（我们把这个点叫做lock point），在这个时间点上，当前事务锁上了它所需要的所有的资源。任何其他与之冲突的事务：

- 要么在lock point之前就已经进入了放锁的阶段（也就意味着它已经完成了在当前事务需要的资源上的所有的工作）
  -  it would have finished all of its work on the resources that this transaction might be operating on before we hit our lock point
  - 也就是它在当前事务的前面完成。
- 要么处于阻塞状态中等待当前事务。也就是在当前事务的后面才轮到它。

而Lock Points的顺序，就给我们一个等价的串行调度。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041704053.png" alt="image-20221204170409801" style="zoom:67%;" />

- 所谓的Lock Point，就是Number of locks held达到峰值的时候。
  - 注意Release Phase的时候锁的数量是逐渐下降的，说明锁的释放是随着事务的进行逐步释放的。
  - 也就是说，假如事务会用到多个资源，那么事务用完一个资源，就释放对应的锁，等到所有的锁都释放完了，这个事务也就完全结束了。
  - 为什么获取锁的阶段锁的个数是逐渐上升的？因为事务所需要获取的锁可能来自多个不同的事务，而它们释放锁的时机是不一样的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041721346.png" alt="image-20221204172107145" style="zoom:67%;" />

### Strict Two Phase Locking

2PL有一个问题，那就是级联中止。

举例：事务T1先读资源A，然后写资源A。在T1把针对A的锁放开以后，T2拿到锁，对A进行了读和写。然后之后T1决定中止。

这就带来了一个问题，T2读到了一个脏值，这个值是T1写入的，然后在T2读取了这个值之后，又被T2的写入覆盖掉了。

事务是具有原子性的，所以我们决定中止T1，那么就要使得T1所做的一切工作都似乎从来没发生一样，但是T2读取了一个直接来自T1写入的值，所以我们也要将T2级联中止（因为中止T1，就需要表现的跟T1没写过那个值一样，也就是说在中止T1的时候，T2读取的那个值应该是从来没有存在过的，T2没办法读取一个从来不存在过的值）。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041713810.png" alt="image-20221204171303602" style="zoom:67%;" />

严格的2PL修复了这个问题。

- 严格的2PL在获取锁的阶段和2PL是一样的，除了在释放锁的时候，严格的2PL是在事务完成（完成：指事务提交或者中止，**完成不等于提交**）的时候一次性释放所有的锁。
  - 所以你会看见那个图在释放阶段锁的数量是直线下降的，因为锁一次性全部释放出去了。
  - 也就是说，在释放锁之后，要么事务已经被提交了（所有的写入都持久化了），要么事务被中止了（所有的写入都被撤回了）
- 通过这样我们可以保证不会出现级联中止的问题。在上面的例子中，在T1决定Abort之前，T2甚至没有机会拿到锁，还在阻塞。在T1中止之后，T2才拿到锁，进行R(A)和W(A)，这时候T2就不会读取到一个应该被擦去的值，也就不需要回滚。



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041735246.png" alt="image-20221204173510004" style="zoom:67%;" />

### Example

首先是不使用2PL的情况：如果我们看做是A向B转账的话，那么莫名其妙少了五十块。 

- 2PL有一个很关键的限制：一旦事务开始释放锁，它就不能再获取任何的锁，而这里T1违背了这个限制，释放了A的锁，又获取了B的锁。

- **注意这里是非2PL，所以在T1Lock_X(B)的时候T1会被T2的Lock_S(B)锁阻塞，T1没办法限制T2对于B的读取。**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041852358.png" alt="image-20221204185215089" style="zoom: 50%;" />

然后是2PL：这里T1在Unlock(A)之前，先获取了Lock_X(B) 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041854493.png" alt="image-20221204185423248" style="zoom: 50%;" />



然后是严格的2PL：

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041853716.png" alt="image-20221204185325469" style="zoom: 50%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041914164.png" alt="image-20221204191442921" style="zoom:67%;" />

### How Do We Lock Data?

一个简单的传统：如果你的事务持有一个锁，我的事务请求一个冲突的锁，那么我的事务就要进入这个锁的等待队列。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041916184.png" alt="image-20221204191628945" style="zoom:67%;" />

## Lock Management

锁和解锁的请求由锁管理器（LM）负责处理。

LM维护一个哈希表，这个哈希表的Key是被锁住的对象的名字

LM为每一个当前被持有的锁保存一个项。

这个项包含：

- 授予集：当前被授予这个锁的访问的事务的集合
- 锁模式：是共享锁还是独占锁
- 等待队列：锁请求的队列。 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041918687.png" alt="image-20221204191853429" style="zoom:67%;" />

当一个锁请求到来的时候：

- 在**授予集合或者等待队列**中有没有其他事务想要一个冲突的锁？
  - 如果有，将请求者放入等待队列（FIFO）
  - 如果没有，将请求者放入授予集。

锁升级：

- 有共享锁的事务可以请求将锁升级为独占的——将这个请求放在队列的队头。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041924266.png" alt="image-20221204192414009" style="zoom:67%;" />



LM的伪代码：在Notes中有的

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041927472.png" alt="image-20221204192706228" style="zoom:67%;" />

#### Example

第一个例子

新请求：T4请求一个对A的S锁S(A)。由于在等待队列中有一个T3(X)的独占锁请求，与T4(S)冲突，所以将T4的请求放入等待队列。

这时候就有一个问题了，T4(S)的请求与现有的授予集中的T1和T2的锁类型都是S锁，它并不与授予集冲突，为什么不直接放进去呢？这就是LM的另外一种实现。稍后再讲。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041933478.png" alt="image-20221204193352227" style="zoom: 50%;" />



第二个例子：

A中T1释放了S(A)，B中T6释放了X(B)。此时T3(X)仍然只能处于队列中，因为授予集中还有一个T2(S)。而B中则可以将T5(X)放入授予集（为什么最开始T6在集合中的时候不放？因为X是独占锁）

你可能会想问，现在我们能不能把T4(S)放进去了？不行，因为是FIFO，T4(S)要在T3(X)的后面。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041937525.png" alt="image-20221204193706293" style="zoom: 50%;" />

### Queue Skipping

在有一个新请求到来的时候，考虑：在授予集中的事务是否想要一个冲突的锁？

- 如果是，那么就把新的请求放入队列
- 如果不是，那么把请求者直接放入授予集。

- 与之前的规则相比，就是在新情求到来的时候，不考虑等待队列的问题。

当一个锁被释放的时候，遍历整个队列，然后准许所有能够准许的请求。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041941969.png" alt="image-20221204194154719" style="zoom:67%;" />

- Notes中同样有伪代码



#### Example

来自T4的新请求S(A)，与现有的授予集不冲突，于是这个请求直接绕过了等待队列，进入了授予集。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041945280.png" alt="image-20221204194552040" style="zoom: 50%;" />

## Dead Lock

一个发生死锁的例子：

- T1先获取了资源A的独占锁
- 然后T2获取了资源B的共享锁。读写什么的略过
- 然后T2想要A的共享锁，由于此时T1还持有A的独占锁，T2阻塞。
- 然后T1在读写完A之后，想要B的独占锁。由于T2此时持有B的共享锁，而共享锁和独占锁在同一个资源上是不能共存的，所以T1也阻塞。
- 于是T2在等待T1放掉A的独占锁，T1在等待T2放掉B的共享锁，死锁形成。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041948670.png" alt="image-20221204194825415" style="zoom: 50%;" />

死锁：由相互等待对方释放锁的事务形成的循环

处理死锁的三种方法：

- 预防
- 避免
- 检测和消除死锁

许多系统就只是赌不会出现死锁和使用超时。但是这个方法在DB里有风险。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212041953405.png" alt="image-20221204195331155" style="zoom:67%;" />

### Deadlock Scenarios

死锁可能发生的场景。

第一个场景很常见了，就是会发生。

第二个，还记得我们之前说的如果有一个事务请求将锁升级为X，将这个请求放在队列的头部吗？就是为了防止这种死锁。通过将请求放在队首可以避免。

- T2本身持有S锁，但是T2排在T3和T4的后面，所以T2会阻塞，所以T2没有办法释放掉S锁（即便T1释放掉了），于是T2始终持有S锁，于是T3和T4始终没办法从队列里出去，于是死锁。如果把T2放在队列头，那么T2会被唤醒，然后就可以释放S锁，升级X锁

第三个，多个事务想要同时升级同一个锁（无法避免）。T1被阻塞在T2后面，于是T1没有办法放掉其持有的S锁，于是T2也就没有办法将锁升级为X锁，堵在那。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042001481.png" alt="image-20221204200102199" style="zoom:67%;" />

### Deadlock Prevention

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042006215.png" alt="image-20221204200633974" style="zoom:67%;" />

### Deadlock Avoidance

- 基于时间（年龄）给事务一个优先级，越老的事务，优先级越高。
- 假如Ti想要Tj当前持有的一个锁。有两种可能的策略：
  - Wait-Die：如果Ti更老，Ti等待Tj，否则Ti更年轻，Ti直接中止（die）
  - Wound-Wait：如果Ti更老，Tj中止（Wound），否则Ti更年轻，Ti等待。
- Wait-Die：与当前持有锁的事务相比，更老的话等待它，更年轻的话终止自己
- Wound-Wait：与当前持有锁的事务相比，更老的话终止它，更年轻的话等待它。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042030587.png" alt="image-20221204203053365" style="zoom:67%;" />

这些方法就相当于直接选了一个事务给了它一枪，杀掉它，直接打破循环。所以能够保证没有死锁。

**一个很重要的细节：如果我们杀掉了一个事务，让这个事务直接重启，那么我们必须保证这个事务得到它最开始的时间戳**。为什么？因为时间戳关系到这个事务的优先级。我们不能因为重启了这个事务就改变它的优先级。如果在重启事务的时候将其时间戳设置为其重启的时间，那么它永远都是最年轻的的那个事务，永远会被中止。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042034954.png" alt="image-20221204203445727" style="zoom:67%;" />

### Deadlock Detection

死锁避免的问题？太多中止了，也许我们可以检查到死锁然后只做必要的中止？

维护一个等待图。定期检查图中是否出现环。

- 如果一个事务无法获取锁，因为这个锁被其他的资源持有，那么画一个从这个事务指向所有当前持有这个冲突锁的事务的箭头。
- 因为如果有环出现，就说明有一堆事务在相互等待。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042040818.png" alt="image-20221204204041562" style="zoom:67%;" />

举一个等待图的例子：

- T1请求S(A)，没问题。T1请求S(D)，没问题，同时T2请求X(B)，也没问题。
- T3请求S(D)，没问题，和T1共享。
- T1请求S(B)，此时T2持有X(B)，所以T1等待T2。
- T3请求S(C)，没问题，没人用C。
- T4请求X(B)，等待T2。
- T2请求X(C)，此时T3持有S(C)，T2等待T3。
- T3请求X(A)，此时T1持有S(A)，T3等待T1。死锁形成。



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042046188.png" alt="image-20221204204630961" style="zoom:50%;" />

在背景后，运行死锁检测算法。周期性地检查图中是否有环，有的话挑环上的一个事务开一枪打破循环。

事实经验来看，大部分的死锁循环都很小，只包含2-3个事务。所以这个的构建没有想象的那么难。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042053195.png" alt="image-20221204205317963" style="zoom:67%;" />



冲突依赖图（上一节课）对比等待图：

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042100573.png" alt="image-20221204210042296" style="zoom:67%;" />

## Lock Granularity

锁粒度

很难决定在什么样的粒度上去锁，是元组的粒度上，还是页？还是表？

这是一个Tradeoff。粒度小的锁（细粒度的锁，fine-grained）可以带来更好的并发性（比如一个元组一个锁，那么表就可以被并发访问，如果一个表一个锁，就不可以）。而粒度大的锁（粗粒度的锁），可以使得锁的数量更少，更好管理。我们没办法鱼和熊掌兼得（可以么？）。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042103394.png" alt="image-20221204210301163" style="zoom:67%;" />

### Multiple Locking Granularity

我们不应该对于所有的事务都采取同样的决定。不同的事务应该能够在不同的粒度上上锁。

为了达到这种效果，我们可以建立一个数据粒度的层次结构树，小的粒度嵌套在大的里面。



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042106256.png" alt="image-20221204210627041" style="zoom:67%;" />

树的层级自顶向下：DB==>表==>页==>记录

当一个事务锁显式地锁住树的一个节点时，这个锁也隐式地锁住了这个节点的整个子树（比如你锁住了一个表，那表里的所有记录也被锁住了）。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042109441.png" alt="image-20221204210923171" style="zoom:67%;" />



有了这棵树以后，我们就可以根据事务的访问模式来锁住树的不同层次，是使用粗粒度的锁（高层次）还是细粒度的锁（低层次）。

锁的粒度：

- 细粒度（树的低层）：更高的并发性，更多的锁（更多的开销）
- 粗粒度（树的高层）：更少的锁（更少的开销），失去一部分的并发性。
  - 如果你不需要被锁在粗粒度里面的一切资源，那么就失去了一部分并发性。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042116804.png" alt="image-20221204211620578" style="zoom:67%;" />

现实世界的锁粒度：来自SQL Sever

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042119904.png" alt="image-20221204211904647" style="zoom:50%;" />

### Intent Lock

意向锁

在获取S或者X锁之前，事务必须在粒度层次树上使用意向锁锁住节点的**所有的祖先**。

比如想对Pb使用S锁，那么要对Pb的祖先T1和DB都使用IS倾向锁来锁住。

意向锁有什么用？

意向锁的作用是告知其他的事务，不要在这个被意向锁锁住的粒度上加锁。

比如我们想读取一页，那么我们不会在DB或者页所在的表层次就使用S锁，因为这样粒度太大了，我们会阻塞其他的很多事务做它们的工作。但是同样的，我们也希望其他的事务不要在这两个粒度上加X锁，因为这样可能会使得我们没办法读取那一页。而意向锁的作用就在于此，意向锁向其他事务表明在树的下层有其他事务在读，不要在意向锁这一层加X锁。 在有了DB上的意向锁以后，就不会有事务可以在DB的粒度上加X锁。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042123521.png" alt="image-20221204212325246" style="zoom:67%;" />



三种Intent Lock：

- IS：有在细粒度获取S锁的意图

- IX：有在细粒度获取X锁的意图
- SIX：同时的Share和IX锁。什么时候有用？
  - 比如只需要更改表的部分记录，但是要找到符合更改条件的记录需要遍历整张表。所以对表用S锁，对被更改的记录用X锁，对表用IX锁表明自己对记录的X锁意图。
  - S锁用来读取整张表，IX锁用来告诉其他事务不要对这整张表加X锁（但是可以对表中的某个记录加X锁），X锁用来锁住被写的记录。
  - 为什么明明已经有IX锁保证其他事务不对整张表加锁，为什么还需要对记录加X锁？因为IX锁只是让其他事务不对整张表加X锁，但是其他事务可以对表中的记录加锁，所以如果不加X锁的话，可能有其他事务会加S锁，这种情况下就修改不了。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042158200.png" alt="image-20221204215848931" style="zoom:67%;" />

### Multiple Granularity Locking  Protocol

多粒度锁协议：

- 每个事务必须从层次结构的根节点开始。

- 为了获取一个节点的S或者IS锁，必须先持有其父节点的IS或者IX锁
- 为了获取一个节点的X或者IX或者SIX锁，必须先持有父节点的IX或者SIX锁。
- 在释放锁的时候，必须自底向上释放，从细粒度的锁开始释放，逐渐过渡到粗粒度的。

![image-20221204220908040](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042209298.png)

## Lock Compatibility Matrix

对于同一个节点，这些不同种类的锁是否能够兼容？（同时存在于这个节点上）

比如IS和IX是可以共存的。因为我们在粗粒度的节点（比如一页）可以施加IS和IX，然后在细粒度的几个节点分别施加S和X（比如处于同一页的不同元组）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042223529.png" alt="image-20221204222326248" style="zoom: 50%;" />



现实世界的Lock Compatibility Matrix：来自SQL Sever。不管，就提了一嘴。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212042237871.png" alt="image-20221204223720520" style="zoom:50%;" /> 

# CS186 Lecture 16  Parallel Query Processing



# CS 186 Lecture 18  Recovery I

## Motivation

- 注意一下在系统崩溃时处于执行中状态的事务，在重启后也是需要回滚的。
- 为什么事务会中止？
- 为什么DBMS会停止运行？

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212101914928.png" alt="image-20221210191450648" style="zoom:67%;" />

### Why do Transactions Abort?

- 用户显式中止它。就好像我们会使用Ctrl + C直接中止一个程序的运行。
- 出现死锁。回忆一下为了消除死锁我们会对处于死锁的环上的某个倒霉蛋事务开一枪。
- 数据一致性检查没有通过。比如违反了完整性约束。
- 系统错误。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212101917990.png" alt="image-20221210191702767" style="zoom:67%;" />

### Why do Databases Crash?

- 操作员犯错(比如一脚绊到电源线)
- 配置错误
- 软硬件错误。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212101921528.png" alt="image-20221210192103313" style="zoom: 50%;" />

## Recovery

### Assumptions for Our Recovery Discussion

并发控制生效。严格的2PL

更新就地发生。即数据在缓冲池中被修改，DB中的页是被覆写的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212101923573.png" alt="image-20221210192313363" style="zoom:67%;" />

### Exercise in Simplicity

设计一个不需要log的简单方法来保证原子性和可靠性

要解决的问题：

- 事务途中发生了什么？
- 为了可靠性，提交的时候发生了什么？
- 中止的时候你如何回滚？
- 原子性是如何保证的？
- 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212101929859.png" alt="image-20221210192908618" style="zoom:67%;" />



为了保证原子性与可靠性，我们会想要确保只有提交了的更新会被写回硬盘，于是在系统崩溃重启的时候，我们在硬盘上只会看到提交过的事务的效应。

- 为了这个目的，脏的缓冲池页必须被固定在缓冲池中（不能被缓存替换策略替换出去），我们不能让未被提交的更新被冲刷到硬盘上去。
- 在提交的时候，我们：
  1. 将脏页写回硬盘
  2. 将这些页解除固定
  3. 然后我们提交。
- 但是这样不管用：
  - 假如缓冲池所有的页都是脏的，都被固定下来了怎么办？现在没办法替换了。扩展性不好，性能也不佳。
  - 在提交的时候，我们的123步并不是一个原子性的单元。其中任意一步都不是原子性的。
    - 如果在第一步的途中DBMS崩溃了怎么办？现在你有一部分的页已经写回硬盘了，有一部分的页还处于内存中，这一部分由于掉电直接丢失了，这个提交也没来得及写入提交日志（第3步），现在DB中有一个未提交的不完整的事务。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212101935897.png" alt="image-20221210193544652" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212101938029.png" alt="image-20221210193823766" style="zoom: 67%;" />

我们的策略是No Steal Policy和Force Policy：没有达成原子性的目标，而且还降低了性能。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212101944495.png" alt="image-20221210194456244" style="zoom:67%;" />

### Preferred Policy:Steal & No Force

实现很复杂

No Force：不强迫在提交之前将所有的脏页写入硬盘。

- 问题：如果在将一个已提交的事务的脏页写回硬盘之前系统崩溃了怎么办？
- 解决方法：在提交之前用一个log来记录我们做了什么，系统重启的时候Redo所有的修改。

Steal:即便某一页被一个未提交的事务修改过，你仍然可以将它替换出去，将它写回硬盘，不用将它固定在内存中。

- 这样可以提高性能，现在我们有了更多的可用的内存。
- 问题：如果有一个事务它还没提交，它修改了一页，然后这一页被写回硬盘了，然后这个事务中止了，怎么办？
- 问题：如果在事务完成之前系统崩溃了怎么办？硬盘里会有一堆来自未提交的事务的数据
- 所以我们需要一种方法来追踪记录我们对某一页做了什么，然后我们才方便回滚。
- 需要记住被冲刷回硬盘的页的旧的值，这样在系统重启的时候我们就知道哪些页的数据是来自未提交的事务的（撤销对这些硬盘的页的写）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212101949698.png" alt="image-20221210194919448" style="zoom:67%;" />

### Basic Idea:Logging

- 对于每次更新，在log中记录下信息以允许Redo和Undo
  - 对log连续的写（在一个单独的硬盘上）
  - 写入最少的信息到Log中：在一个log页中会打包记录多个更新
- Log：一个有序的log记录的列表。
  - 每个log记录包含:`<事务ID,页ID,偏移量,长度,旧数据,新数据>`
    - 哪个事务，在哪一页，这一页的哪个位置开始，写了多长的数据，写之前这一段长度的数据是什么，写之后这一段长度的数据又是什么
  - 以及额外的控制信息。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212102003281.png" alt="image-20221210200350012" style="zoom: 67%;" />

### Write Ahead Logging(WAL)

WAL协议：

1. 在将对应的数据页写入DB硬盘之前，必须强制先将这个更新的log记录写入硬盘。
2. 在提交之前一个事务之前必须将这个事务的**所有**log写入硬盘。
   - 即：对于一个事务来说，直到它所有的log记录，包括它的提交记录被写入Log，这个事务才算是提交了。一旦这个事务所有的log都写入了Log中，我们就正式认为这个事务提交了。

- 第一点确保我们可以撤销，保证了原子性。它记录了我们做的所有的更改。
- 第二点确保我们可以重做，保证了可靠性。我们可以通过log来重建必要的数据。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212102017371.png" alt="image-20221210201733128" style="zoom:67%;" />

### WAL & The Log

- Log有它自己的写缓冲区（RAM），称为Tail。毕竟我们只能在内存中去对Log做修改。
  - 周期性地将内存中的更新冲刷回硬盘。
- 每个Log有一个Log序列号（Log Sequential Number，LSN），LSN是不重复且自增的。
- 在内存中我们会记录最后一个被写回硬盘的log的LSN，称之为Flushed LSN。这个LSN很重要，因为它告诉我们在哪个点之前的Log都被写回硬盘了。
- 数据库中的每一个数据页都包含一个Page LSN，这个PageLSN可以看做是一个指向log的指针。PageLSN是对这个页做了最后一次更新的log的LSN。
  - **Page LSN<=Flushed LSN**
  - 在第i个DataPage被冲刷到DB之前，它的Page LSN必须小于等于Flushed LSN。所以这要求我们在将数据页写回硬盘之前，先将其log写回。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212102043161.png" alt="image-20221210204314892" style="zoom:67%;" />

### ARIES Log Records

ARIES是我们即将学习的Logging算法。

- prevLSN(Previous LSN)：被这个事务ID写下的前一个Log的LSN。
- 通过跟踪prevLSN，一个事务的所有的记录就构成一个时间上逆向的链表。这样方便我们撤销某个事务的一系列的操作，沿着这个时间上逆向的链表一个个撤销就可以。

一个Log记录可能就长下面那样：

- LSN：日志序列号
- prevLSN：
- 事务ID
- 类型：log有不同的类型。
  - Update，Commit，Abort
  - 检查点
  - compensation log record（CLR）：？
  - END：Commit或者Abort的End。
- 以及仅限更新类型log的那些域，用于记录在哪一页的哪个位置发生了什么更新，更新之前是什么样，更新之后是什么样。
  - `<事务ID,页ID,偏移量,长度,旧数据,新数据>`


<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212102056182.png" alt="image-20221210205650921" style="zoom:67%;" />

### Other Log-Related States

我们还有两张处于内存中的表：

事务表：

- 每个当前活跃中的事务在表中有一项。当事务提交或者中止的时候，移除表项。
- 每个表项包含：
  - 事务ID
  - 事务的状态：运行中，提交中，中止中
  - LastLSN：被这个事务写入的最近的Log的LSN（最后一个被它写入Log的LSN）
- 脏页表：
  - 当前处于缓冲池的每个脏页一项。
  - 包含recoveryLSN：第一个使得页变脏的log的LSN。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212102114181.png" alt="image-20221210211430920" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212102121295.png" alt="image-20221210212151033" style="zoom:67%;" />

# CS186 Lecture 19 Recovery II

今天讲的内容是：如何通过Log来重建系统崩溃之前正在写的内容。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111119797.png" alt="image-20221211111919231" style="zoom: 50%;" />

## Checkpoint

类似游戏的存档。存档使得我们可以在恢复的时候不需要从整个Log的开头开始读取Log（这样开销很大）。

基本思想：周期性地保存DB的状态从而使得我们不需要在进行恢复的时候处理整个Log。

在检查点期间：

- 停止接受新的事务
- 等待所有当前的事务完成（提交或者中止）
- 将log写回硬盘
- 将脏页写回硬盘
- 写一个CheckPointLog，写回硬盘
- 在这个点上，已提交的事务的更改被写回了硬盘，而中止的事务被回滚。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111125272.png" alt="image-20221211112554974" style="zoom:67%;" />

问题在于：在建立检查点的过程中，数据库是处于冻结状态的。DB不接受任何新的事务。这一点也不最优，会使得数据库的速度变慢很多。理想状态下我们希望DB能在建立检查点的同时处理事务。

- fuzzy checkpoint

### fuzzy checkpoint

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111135266.png" alt="image-20221211113540982" style="zoom: 50%;" />

模糊检查点

- 在某个时间点上保存所有事务的状态和页的状态。
  - 事务表和脏页表等等
- 其他的事务在背后继续运行
- 当将这些状态写入log，log写入硬盘之后，就有了一个模糊检查点。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111143533.png" alt="image-20221211114330269" style="zoom: 50%;" />



#### fuzzy checkpoint:Data Structures

复习了一下各种LSN

脏页表

事务表

#### fuzzy checkpoint:Protocol

当建立检查点的时候：

- 写一个`<BEGIN CHECKPOINT>`到Log
- Log写回硬盘
- 如常继续
- 当脏页表和事务表写入硬盘后，写一个`<END CKPT>`到log

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111214239.png" alt="image-20221211121423947" style="zoom: 50%;" />

## ARIES Normal Operation

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111305128.png" alt="image-20221211130533860" style="zoom: 50%;" />

### Transaction Starts

开始事务：

- 将开始记录写入Log
- 更新事务表（多了一项）

举例：事务T105开始：

- 往Log写入`<STATR,T105>`
  - PrevLSN= NULL，LSN = 某个值， Type = Start，txnID=T105

- 将`T105`加入事务表，表中的lastLSN置为NULL。
- 脏页表和缓冲池不变化，因为事务刚开始。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111347748.png" alt="image-20221211134723419" style="zoom: 50%;" />

### Transaction Updates

事务更新：

- 往log里写入更新记录
- 更新以下内容：
  - prevLSN = lastLSN。就跟链表尾部新插入的节点的preNode是现在的LastNode一样。
  - pageLSN = LSN。LSN是最后一个修改过这个Page的。
  - lastLSN= LSN。就跟链表尾插的节点现在变成新的LastNode一样。
  - recoveryLSN：**如果为NULL，就置为LSN，否则保持不动**。为NULL说明该页此前与硬盘是同步的，这是它第一次变脏。
- 例子：看图。各个LSN的更新看箭头。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111354674.png" alt="image-20221211135409338" style="zoom: 50%;" />

### Page Flush

当要将一页写回硬盘的时候：

- 将log写回，使得$FlushedLSN \  \geq \ pageLSN$。
- 将脏页从脏页表和缓冲池移除。前者是去掉一项，后者是写回硬盘。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111400840.png" alt="image-20221211140032542" style="zoom: 50%;" />

### Page Fetchs

从硬盘拉取一页到缓冲池：

- 在脏页表新增一项，同时缓冲池中把这一页放进来
  - **脏页表中的recoveryLSN置为NULL。**因为拉取进来的这一页和硬盘上是同步的，没有被弄脏。
- 事务表和Log都不变。



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111414824.png" alt="image-20221211141422509" style="zoom: 50%;" />

### Transaction Commits

事务提交：

- 往log写入提交记录
- 将log截止到提交记录的都写回硬盘。
  - 到这里这个事务就被看做是正式提交了。
- 将事务表的对应项更新为Commiting
- 往Log写入END。
- 将事务表的对于像更新为Complete（实际上直接移除这一项就好了）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111419472.png" alt="image-20221211141911140" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111426662.png" alt="image-20221211142656319" style="zoom:50%;" />

### Transaction Aborts

事务中止：

- 往Log写入中止记录
- 从lastLSN找到第一个需要撤回的动作。（不是找Abort记录的PreLSN，虽然貌似是一样的）
- 前往log，开始撤回更改。
- 往log写入CLR(补偿记录？修正记录？)
  - CLR的undoNextLSN指向下一个要撤回的记录（也就是下一个要被UNDO的Log的LSN）。
  - UNDO这个行为本身也要被Log记录下来。
  - 每一个被UNDO的Log，都需要写入一个对应的CLR（所以例子里UNDO了LSN103和102，CLR记录有两个）
- 跟随prevLSN来撤回更多的操作（如果有）
- 一旦完成，将事务表的对应项更改为Aborting
- 将脏页写回硬盘。
- 往log写入END。
- 更新事务表，事务状态改为Complete。
- ？Flush if u want。这是不必要的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111429291.png" alt="image-20221211142921941" style="zoom: 50%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111501207.png" alt="image-20221211150123834" style="zoom: 50%;" />

## Crash Recovery

从检查点开始。

三个阶段：

- 分析：找出哪些事务已提交，哪些提交失败
- 重做**所有的**行为：将DB的状态恢复到系统崩溃之前
- 撤销失败的事务的行为。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111515675.png" alt="image-20221211151442916" style="zoom: 50%;" />

### Recovery:The Analysis Phase

通过检查点保存的脏页表和事务表重建当时的状态。

从检查点开始向前扫描log。

- End记录：从事务表中移除相应的项
- Update记录：如果页P没有在脏页表中，那么加入P，将其RecoveryLSN置为LSN。
- 非End记录：
  - 将事务加入事务表
  - 将lastLSN置为LSN
  - 将事务状态置为Commit或者Abort
- 分析完毕之后：
  - 对于事务表中处于Commiting状态的事务：写一个相应的End记录到Log，从事务表移除这一项。
  - 对于事务表中处于Running状态的事务：这些是在崩溃时处于运行状态的事务，需要被回滚。将状态改为Aborting，写AbortRecord
  - 脏页表告知我们哪些页没被写入磁盘。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111534014.png" alt="image-20221211153450723" style="zoom:67%;" />

### The Redo Phase

从脏页表中最小的RecoveryLSN开始（这是硬盘和内存内容在崩溃前开始不一致的起点）扫描log，然后重复所有的更新或者CLR。

对于每个具有给定LSN的更新或者CLRlog，重做它们的操作，除非：

- 受影响的页不在脏页表中
- 受影响的页在脏页表中，但是其RecoveryLSN大于LSN
- pageLSN>=LSN。

对于重做的操作：

- 重做log里的动作
- 将PageLSN置为LSN。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111544976.png" alt="image-20221211154422686" style="zoom:67%;" />

#### Scenarios when we do not redo

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111605131.png" alt="image-20221211160544851" style="zoom:67%;" />

### The Undo Phase

简单的方法：

对于事务表中留存的事务，一个个地进行中止。

但是这样有一个问题：由于跟随undoNextLSN去寻找下一个需要undo的log，会导致很多的随机I/O。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111619472.png" alt="image-20221211161941229" style="zoom:67%;" />

伪代码：



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111629578.png" alt="image-20221211162940317" style="zoom:67%;" />

# CS186 Lecture 20 Distributed Transactions

- 如果事务是在多个节点上分布式地进行的呢？如果有一台机器下线了会发生什么？怎么处理？
- 非同步的时钟？没有办法得到完美的同步

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212111901981.png" alt="image-20221211190039391" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212121251596.png" alt="image-20221212125059285" style="zoom: 67%;" />

## Distributed Concurrency Control

假设：

- Shared-Nothing的分布式DBMS
- 数据是分布在DB的各个部分，而不是多份副本的形式。
- 每个事务到达某个节点，这个节点可以看做是这个事务的协调者。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212121253554.png" alt="image-20221212125322281" style="zoom:67%;" />

## 2PC

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212121326118.png" alt="image-20221212132635855" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212121326762.png" alt="image-20221212132657503" style="zoom:67%;" />



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212121327837.png" alt="image-20221212132739002" style="zoom:50%;" />

## Recovery AND 2PC

- 假定每一台崩溃下线的机器最后都会恢复
- 协调者发现一个参与者下线了（反正就是不在线，可能崩溃了）？
  - 如果参与者还没有投票，协调者中止事务
  - 如果等待一个Commiting ack，转移到恢复处理
- 参与者发现协调者下线了？
  - 如果还没有在Log中写入提交记录，那么直接单方面中止事务
  - 如果已经在log写入了，转移到恢复处理
- 注意：认为节点下线了不等于就真的下线了，可能只是网特别的拉胯。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212121330107.png" alt="image-20221212133058860" style="zoom:67%;" />

### Integration With ARIES Recovery

- 在恢复的时候：
  - 假定每个节点上都有一个“恢复程序”
  - 其他看图
- 注意：同一个节点可以扮演不同的角色，可能是一个事务的协调者，又是另外一个事务的参与者。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212121335954.png" alt="image-20221212133511710" style="zoom:67%;" />

作为参与者崩溃：

- 还记得我们在事务表中的三个状态：Running、Commiting，Aborting
- 作为参与者（恢复上线的时候）看见一个准备提交的log记录：
  - 将事务的状态改为Commiting
  - 告诉自己的恢复程序去询问协调者的状态：
    - 因为参与者从崩溃中恢复的时候，参与者需要知道两件事：1. 我的投票（Commit的决定）是否到达了协调者？毕竟我们不知道我们是在投票到达之前崩溃的还是之后。2.就算我们的投票到达了，那协调者最后的决定是什么？是决定提交还是中止？
  - 当协调者回复之后，恢复程序如常处理提交或者中止

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212121338684.png" alt="image-20221212133811424" style="zoom:67%;" />

作为协调者崩溃：

- (从崩溃中恢复)看到一个Commit或者Abort的记录：
  - 将事务的状态相应地改为Commiting或者Aborting
  - 告诉恢复程序给参与者发送Commit/Abort的消息
  - 一旦收到了所有的参与者的ack，恢复程序写个EndRecord然后就可以将这个事务抛在脑后了。
- 如果对于某个事务X没有找到log，那直接假定它Abort。这叫做推定中止

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212121344628.png" alt="image-20221212134428356" style="zoom:67%;" />

### How Does Recovery Process Work?

-  如果协调者崩溃了并且没有log存在盘里？协调者本地中止
- 协调者的恢复程序收到了来自准备好的参与者的询问：
  - **所谓的Prepareed参与者，指的是参与者单方面准备好了可以提交**
  - 如果协调者的事务表说是中止或者提交，那就回复中止或者提交，双边继续协议即可
  - 如果协调者的事务表中什么都没有，发送Abort。
    - 这种情况只会在（参与者发送了自己的VOTE但是）协调者在写入Commit或者Abort之前崩溃（还没收到参与者的Vote就崩溃了）。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212121415802.png" alt="image-20221212141558529" style="zoom:67%;" />

各种情况的分析：

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212121454118.png" alt="image-20221212145456831" style="zoom:67%;" />

### Presumed Abort

推定中止：

- 对硬盘的写入是开销很大的，所以尽量避免写入，无论何时只要可能，就避免写入。
- 所以我们决定：对于中止的记录，可以不落盘。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212121525727.png" alt="image-20221212152559472" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212121529961.png" alt="image-20221212152926671" style="zoom:67%;" />

- 星号代表有一个FLush的过程，注意Abort是没有的（相信自己如果中途崩溃并且醒来，会决定中止）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212121530696.png" alt="image-20221212153036418" style="zoom:67%;" />



### 2PL + 2PC

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212121533737.png" alt="image-20221212153331468" style="zoom:67%;" />

## Availability Concern

如果参与者寄了？

- 协调者通过log恢复，再找一个节点替代它。然后寄了的节点复活了，忽略，让它一边玩去

- > let's say a participant goes down and it just isn't waking back up
  >
  > so the coordinator will then respawn that participant from the log and then what's going to happen is if that participant that you respond if the original one comes back up and says oh i'm alive the coordinator will be like no no i assumed you were dead i gave your task to someone else recycle yourself you'll be used for something else later on instead your services are no longer needed

协调者寄了？

- 那就真寄了
- 3PC试图解决
- 论文？超纲

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202212121538599.png" alt="image-20221212153820325" style="zoom:67%;" />

# Lecture 21 DB Design: Entity-Relationship Models

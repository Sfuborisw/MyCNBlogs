<h2> ［DB2 存储过程］ :memo: </h2> 

> 1.添加新的存储过程
```SQL
1.打开命令行窗口 / 打开控制中心 / 打开命令编辑器：db2cmd / db2cmd db2cc / db2cmd db2ce;

2.启动 / 停止数据库实例：db2start / db2stop;

3.创建数据库：db2 create db [dbname];

4.连接到数据库：db2 connect to [dbname] user [username] using [password];

5.断开数据库连接：db2 connect reset;

6.列出所有数据库：db2 list db directory;

7.列出所有激活的数据库：db2 list active databases;

8.列出所有数据库配置：db2 get db cfg;

9.删除数据库(慎用，除了删库跑路，ヽ(≧□≦)ノ)：db2 drop database [dbname];

10.列出所有用户表：db2 list tables; 
```

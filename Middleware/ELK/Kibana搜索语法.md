# Kibana搜索语法
Kibana支持三种搜索语法，分别为： Lucene query 语法，ES query语法和Kuery语法
## Lucene query 语法
### Key:Value形式查询条件
- message:abc         在title这个域中搜索 abc, 所有title 包含 abc 都会被搜索出来. 
- message:"abc def"   在title这个域中搜索 abc def 这个完整的字符串.
- message:abc def     这个写法不同于上面的查询语句, 其实是两个查询条件, 在title域中搜索abc, 并且在默认域中搜索def.
- message:"1=1"       如果term有空格或等于号等特殊字符, 需要用双引号括起来.

### 数值项使用>、<、=操作符
- count_number:<100 

### 查询项组合, 使用AND 、 OR 、NOT
- message:"alibaba" AND time:'2020-03-31 12:00:00'
- count_number:<100 AND another_number:>65535 

### 通配符
- ? 号可以匹配任意一个字符
- \* 号可以匹配任意多个字符
> 放在在term的中间或尾部, 不能放在term的最前面.

### 正则
ES中正则性能很差，功能也不是特别强大，尽量不要使用, 正则表达式需要用//括起来.
- message：/[0-9]{2}[a-z]{3}/  

### 范围限定
- 方括号：包含边界值
  - date:[20200201 TO 20200229]
- 花括号：不包含边界值
  - date：{20200201 TO 20200229}
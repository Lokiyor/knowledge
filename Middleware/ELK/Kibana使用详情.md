# Kibana使用详情
## 控制台管理
1. 进入Management管理界面
2. 点击Kibana-工作区

#### 创建工作区
1. 点击创建一个空间
2. 根据内容填入工作区用户

#### 修改相应工作区
1. 点击需要修改的工作区
2. 定制功能显示中，显示隐藏相应的功能
   

##### 工作区功能解释
- Discover：日志管理视图 主要进行搜索和查询
- Visualize：统计视图 构建可视化的图表
- Dashboard：仪表板 将构建的图表组合形成图表盘
- Canvas：大屏展示图
- Maps：地图
- Machine Learning：机器学习
- Metrics：指标
- Logs：源日志
- APM：性能管理视图
- Uptime：运行时间
- SIEM：安全信息和事件管理
- Dev Tools：开发工具
- Stack Monitoring：堆栈监测
- Management：管理视图

## 索引建立
1. 进入Management管理界面
2. 点击Kibana-索引模式
3. 点击创建索引模式
4. 在定义索引模式中，输入索引格式  
   例如：按时间分割的索引以下索引
   - api-prod-2020.01.01
   - api-prod-2020.01.02
  ...  

上述索引可以以```api-prod-*```全匹配
5. 点击下一步
6. 时间筛选 选择@timestamp
7. 点击创建索引模式
8. 进入相应的索引模式，修改@timestamp
   - 格式选择Date
   - Moment.js 格式模式 定义为自己想要的时间格式，如:YYYY-MM-DD HH:mm:ss.SSS
  
保存字段

## 删除索引
1. 进入Management管理界面
2. 点击Elasticsearch-索引管理
3. 搜索勾选需要操作的索引
4. 管理 个索引 - 删除 个索引 
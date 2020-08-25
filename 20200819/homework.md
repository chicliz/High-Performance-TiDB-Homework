## 题目描述
本地下载 TiDB，TiKV，PD 源代码，改写源码并编译部署以下环境：
使用 sysbench、go-ycsb 和 go-tpc 分别对
 TiDB 进行测试并且产出测试报告。

测试报告需要包括以下内容：

* 部署环境的机器配置(CPU、内存、磁盘规格型号)，拓扑结构(TiDB、TiKV 各部署于哪些节点)
* 调整过后的 TiDB 和 TiKV 配置
* 测试输出结果
* 关键指标的监控截图
	    * TiDB Query Summary 中的 qps 与 duration
	    * TiKV Details 面板中 Cluster 中各 server 的 CPU 以及 QPS 指标
	    * TiKV Details 面板中 grpc 的 qps 以及 duration

输出：写出你对该配置与拓扑环境和 workload 下 TiDB 集群负载的分析，提出你认为的 TiDB 的性能的瓶颈所在(能提出大致在哪个模块即 可)

## 机器配置  

- 只有一台机器q@p  
- 16G  
- Intel® Core™ i7-8700 CPU @ 3.20GHz × 12  
- 1.5T

## 测试报告
- [配置修改前](./before.md)
- [配置修改后](./after.md)

## 结果分析

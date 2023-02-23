
[![license](https://badgen.net/badge/license/CC-BY-4.0/green)](https://creativecommons.org/licenses/by-sa/4.0/deed.zh)
[![github](https://badgen.net/badge/github/StabilityGuide/orange)](https://github.com/StabilityMan/StabilityGuide)
[![document](https://badgen.net/badge/document/stabilityman.github.io/red)](https://stabilityman.github.io)
[![chat](https://badgen.net/badge/chat/dingding/blue)](https://github.com/StabilityMan/StabilityGuide/blob/master/DingGroup_2.png)


> 稳定性之于系统，就像健康之于人类，看起来重要不紧急，然而一旦失去，就追悔莫及。

> 稳定性是一切 0 前面的 1。

## 为什么要做这个专栏？
* **让无法解决的问题少一点点，让世界的确定性多一点点。**
* 打造国内稳定性领域知识库，降低知识获取门槛。


## 加入我们
* [GitHub 地址](https://github.com/StabilityMan/StabilityGuide)
* [在线文档站（阅读体验更佳）](https://stabilityman.github.io)
* 钉钉群号
  * 30000312（2群，推荐） 
  * 23179349（1群，已满）
* 如果你在本专栏有所收获，欢迎分享给身边的朋友，期待更多同学的加入！

## 框架目录
### 0. 故障案例
###### [【必读】故障案例征集 & Demo 模板.md](docs/case/【必读】故障案例征集&Demo模板.md)
###### [【案例】Dubbo 稳定性：Nacos 注册中心可用性问题复盘](docs/case/【案例】Dubbo稳定性_Nacos注册中心可用性问题复盘.md)
###### [【案例】记一次线上内存报警排查过程](docs/case/【案例】记一次线上内存报警排查过程.md)
###### [【案例】发现CMS_GC有点傻--应用A_FullGC问题排查](docs/case/【案例】发现CMS_GC有点傻--应用A_FullGC问题排查.md)


### 1. 事前防范
#### 1.1 代码规约
#### 1.2 变更管控
#### 1.3 性能压测
#### 1.4 混沌工程
##### [混沌工程介绍与实践](docs/prevention/resilience/混沌工程介绍与实践.md)
#### 1.5 风险预案 
#### 1.6 限流降级

##### [流控降级最佳实践](docs/prevention/resilience/流控降级最佳实践.md)

#### 1.7 业务隔离 

### 2. 事中“止血”
#### 2.1 监控告警
###### [虾米SRE实践：监控体系升级之路](docs/processing/monitor/虾米SRE实践_监控体系升级之路.md) 
###### [阿里云 ARMS 小程序监控进阶之路](docs/processing/monitor/阿里云ARMS小程序监控进阶之路.md)
###### [饿了么监控系统 EMonitor 与 CAT 的对比](docs/processing/monitor/饿了么监控系统EMonitor与CAT的对比.md)
###### [如何专业化监控一个Kubernetes集群](docs/processing/monitor/如何专业化监控一个Kubernetes集群.md)
###### [2021 Gartner APM 魔力象限解读](docs/processing/monitor/2021_Gartner_APM魔力象限解读.md)
###### [OPLG：新一代云原生可观测最佳实践](docs/processing/monitor/OPLG_新一代云原生可观测最佳实践.md)

#### 2.2 异常巡检
#### 2.3 流量调度 
#### 2.4 资损防控
###### [数据一致性检测应用场景与最佳实践](docs/processing/lostprevention/数据一致性检测应用场景与最佳实践.md)


### 3. 事后诊断
#### 3.1 系统诊断
###### [So Hot？快给 CPU 降降温](docs/diagnosis/system/cpu/SoHot_快给CPU降降温.md)


#### 3.2 JVM 诊断
##### 3.2.1 异常诊断
###### [OutOfMemoryError 常见原因及解决方法](docs/diagnosis/jvm/exception/系统稳定性——OutOfMemoryError常见原因及解决方法.md)
###### [StackOverFlowError 常见原因及解决方法](docs/diagnosis/jvm/exception/系统稳定性——StackOverFlowError常见原因及解决方法.md)
###### [NoSuchMethodError 常见原因及解决方法](docs/diagnosis/jvm/exception/系统稳定性——NoSuchMethodError常见原因及解决方法.md)

##### 3.2.2 内存诊断

##### 3.2.3 线程诊断
###### 线程池满
###### 死锁

##### 3.2.4 GC 诊断
###### [咱们从头到尾说一次Java垃圾回收](docs/diagnosis/jvm/gc/咱们从头到尾说一次垃圾回收.md)


#### 3.3 组件诊断
###### [Dubbo 常见错误及解决方法](docs/diagnosis/plugin/rpc/系统稳定性——Dubbo常见错误及解决方法.md)
###### [Nacos 常见问题及解决方法](docs/diagnosis/plugin/slb/Nacos常见问题及解决方法.md)
###### [Spring Boot 常见错误及解决方法](docs/diagnosis/plugin/microservice/SpringBoot常见错误及解决方法.md)
###### [SchedulerX 常见问题及解决方法](docs/diagnosis/plugin/scheduling/SchedulerX常见问题及解决方法.md)

#### 3.4 在线诊断 
##### Arthas

#### 3.5 链路追踪 
###### [【剖析 SOFARPC 框架】之 SOFARPC 链路追踪剖析](docs/diagnosis/tracing/剖析SOFARPC框架之SOFARPC链路追踪剖析.md)
###### [如何检测Web服务请求丢失问题](docs/diagnosis/tracing/如何检测Web服务请求丢失问题.md)
###### [让可观察性带上导航，快速发现和定位业务问题：OpenTracing上写入业务信息](docs/diagnosis/tracing/让可观察性带上导航_快速发现和定位业务问题_OpenTracing上写入业务信息.md)
###### [开源自建/托管与商业化自研Trace，如何选择？](docs/diagnosis/tracing/开源自建_托管与商业化自研Trace_如何选择.md)
###### [前后端、多语言、跨云部署，全链路追踪到底有多难？](docs/diagnosis/tracing/前后端_多语言_跨云部署_全链路追踪到底有多难.md)
###### [链路分析 K.O “五大经典问题”](docs/diagnosis/tracing/链路分析K.O“五大经典问题”.md)
###### [链路追踪（Tracing）其实很简单——分布式链路追踪的起源](docs/diagnosis/tracing/链路追踪其实很简单——分布式链路追踪的起源.md)
###### [链路追踪（Tracing）其实很简单——分布式链路追踪的诞生](docs/diagnosis/tracing/链路追踪其实很简单——分布式链路追踪的诞生.md)
###### [链路追踪（Tracing）其实很简单——分布式链路追踪的应用与兴起](docs/diagnosis/tracing/链路追踪其实很简单——分布式链路追踪的应用与兴起.md)
###### [链路追踪（Tracing）其实很简单——分布式链路追踪的挑战与限制](docs/diagnosis/tracing/链路追踪其实很简单——分布式链路追踪的挑战与限制.md)
###### [链路追踪（Tracing）其实很简单——请求轨迹回溯](docs/diagnosis/tracing/链路追踪其实很简单——请求轨迹回溯.md)
###### [链路追踪（Tracing）其实很简单——多维链路筛选](docs/diagnosis/tracing/链路追踪其实很简单——多维链路筛选.md)
###### [链路追踪（Tracing）其实很简单——链路实时分析、监控与告警](docs/diagnosis/tracing/链路追踪其实很简单——链路实时分析_监控与告警.md)
###### [链路追踪（Tracing）其实很简单——链路拓扑](docs/diagnosis/tracing/链路追踪其实很简单——链路拓扑.md)
###### [链路追踪（Tracing）其实很简单——链路功能进阶指南](docs/diagnosis/tracing/链路追踪其实很简单——链路功能进阶指南.md)
###### [链路追踪（Tracing）其实很简单——链路成本进阶指南](docs/diagnosis/tracing/链路追踪其实很简单——链路成本进阶指南.md)
###### [链路追踪（Tracing）其实很简单——全量存储? No! 按需存储? YES!.md](docs/diagnosis/tracing/链路追踪其实很简单——全量存储No按需存储YES.md)

#### 3.6 RootCause 
###### [系统黄金指标之延迟(Latency)指标的故障诊断](docs/diagnosis/rootcause/系统黄金指标之延迟指标的故障诊断.md)



## 版本迭代
* 2022-11-03
    * [【案例】发现CMS_GC有点傻--应用A_FullGC问题排查](docs/case/【案例】发现CMS_GC有点傻--应用A_FullGC问题排查.md)@佐井
* 2022-10-26
    * [链路追踪（Tracing）其实很简单——链路成本进阶指南](docs/diagnosis/tracing/链路追踪其实很简单——链路成本进阶指南.md)@涯海
    * [链路追踪（Tracing）其实很简单——链路功能进阶指南](docs/diagnosis/tracing/链路追踪其实很简单——链路功能进阶指南.md)@涯海
    * [链路追踪（Tracing）其实很简单——链路拓扑](docs/diagnosis/tracing/链路追踪其实很简单——链路拓扑.md)@涯海
    * [链路追踪（Tracing）其实很简单——链路实时分析、监控与告警](docs/diagnosis/tracing/链路追踪其实很简单——链路实时分析_监控与告警.md)@涯海
* 2022-07-14
    * [链路追踪（Tracing）其实很简单——多维链路筛选](docs/diagnosis/tracing/链路追踪其实很简单——多维链路筛选.md)@涯海
    * [链路追踪（Tracing）其实很简单——请求轨迹回溯](docs/diagnosis/tracing/链路追踪其实很简单——请求轨迹回溯.md)@涯海
    * [链路追踪（Tracing）其实很简单——分布式链路追踪的挑战与限制](docs/diagnosis/tracing/链路追踪其实很简单——分布式链路追踪的挑战与限制.md)@涯海
    * [链路追踪（Tracing）其实很简单——分布式链路追踪的应用与兴起](docs/diagnosis/tracing/链路追踪其实很简单——分布式链路追踪的应用与兴起.md)@涯海
    * [链路追踪（Tracing）其实很简单——分布式链路追踪的诞生](docs/diagnosis/tracing/链路追踪其实很简单——分布式链路追踪的诞生.md)@涯海
    * [链路追踪（Tracing）其实很简单——分布式链路追踪的起源](docs/diagnosis/tracing/链路追踪其实很简单——分布式链路追踪的起源.md)@涯海
* 2022-04-15
    * [OPLG：新一代云原生可观测最佳实践](docs/processing/monitor/OPLG_新一代云原生可观测最佳实践.md)@涯海
* 2021-11-17
    * [【必读】故障案例征集 & Demo 模板](docs/case/【必读】故障案例征集&Demo模板.md)@涯海
* 2021-11-08
    * [链路分析 K.O “五大经典问题”](docs/diagnosis/tracing/链路分析K.O“五大经典问题”.md)@涯海
* 2021-09-23
    * [前后端、多语言、跨云部署，全链路追踪到底有多难？](docs/diagnosis/tracing/前后端_多语言_跨云部署_全链路追踪到底有多难.md)@涯海
* 2021-08-27
    * [开源自建/托管与商业化自研Trace，如何选择？](docs/diagnosis/tracing/开源自建_托管与商业化自研Trace_如何选择.md)@涯海
* 2021-05-27
    * [链路追踪（Tracing）其实很简单——全量存储? No! 按需存储? YES!.md](docs/diagnosis/tracing/链路追踪其实很简单——全量存储No按需存储YES.md)@涯海
    * [如何专业化监控一个Kubernetes集群](docs/processing/monitor/如何专业化监控一个Kubernetes集群.md)@佳旭
    * [2021 Gartner APM 魔力象限解读](docs/processing/monitor/2021_Gartner_APM魔力象限解读.md)@西杰
* 2019-12-26
    * [SchedulerX 常见问题及解决方法](docs/diagnosis/plugin/scheduling/SchedulerX常见问题及解决方法.md)@学仁
    * [【案例】Dubbo 稳定性：Nacos 注册中心可用性问题复盘](docs/case/【案例】Dubbo稳定性_Nacos注册中心可用性问题复盘.md)@岛风
    * [让可观察性带上导航，快速发现和定位业务问题：OpenTracing上写入业务信息](docs/diagnosis/tracing/让可观察性带上导航_快速发现和定位业务问题_OpenTracing上写入业务信息.md)@竹影
* 2019-11-07
    * [链路追踪（Tracing）其实很简单——单链路诊断](docs/diagnosis/tracing/链路追踪其实很简单——单链路诊断.md)@涯海
    * [Spring Boot 常见错误及解决方法](docs/diagnosis/plugin/microservice/SpringBoot常见错误及解决方法.md)@洛夜
    * [【案例】记一次线上内存报警排查过程](docs/case/【案例】记一次线上内存报警排查过程.md)@神帅
    * [饿了么监控系统 EMonitor 与 CAT 的对比](docs/processing/monitor/饿了么监控系统EMonitor与CAT的对比.md)@李刚
* 2019-09-19
    * [Nacos常见问题及解决方法](docs/diagnosis/plugin/slb/Nacos常见问题及解决方法.md)@敦谷
    * [数据一致性检测应用场景与最佳实践](docs/processing/lostprevention/数据一致性检测应用场景与最佳实践.md)@龙多
    * [链路追踪（Tracing）其实很简单——初识](docs/diagnosis/tracing/链路追踪其实很简单——初识.md)@涯海
* 2019-09-05
    * [系统黄金指标之延迟(Latency)指标的故障诊断](docs/diagnosis/rootcause/系统黄金指标之延迟指标的故障诊断.md)@绍宽
    * [【剖析 SOFARPC 框架】之 SOFARPC 链路追踪剖析](docs/diagnosis/tracing/剖析SOFARPC框架之SOFARPC链路追踪剖析.md)@畅为/碧远/卓与
    * [阿里云ARMS小程序监控进阶之路](docs/processing/monitor/阿里云ARMS小程序监控进阶之路.md)@慕扉
* 2019-08-22
    * [So Hot？快给 CPU 降降温](docs/diagnosis/system/cpu/SoHot_快给CPU降降温.md)@涯海
    * [虾米SRE实践：监控体系升级之路](docs/processing/monitor/虾米SRE实践_监控体系升级之路.md)@全琮
    * [混沌工程介绍与实践](docs/prevention/resilience/混沌工程介绍与实践.md)@穹谷
    * [如何检测Web服务请求丢失问题](docs/diagnosis/tracing/如何检测Web服务请求丢失问题.md)@竹影
* 2019-08-08
	* [NoSuchMethodError 常见原因及解决方法](docs/diagnosis/jvm/exception/系统稳定性——NoSuchMethodError常见原因及解决方法.md)@涯海
	* [咱们从头到尾说一次Java垃圾回收](docs/diagnosis/jvm/gc/咱们从头到尾说一次垃圾回收.md)@率鸽
	* [流控降级最佳实践](docs/prevention/resilience/流控降级最佳实践.md) @宿何	
* 2019-07-26
	* [OutOfMemoryError 常见原因及解决方法](docs/diagnosis/jvm/exception/系统稳定性——OutOfMemoryError常见原因及解决方法.md)@涯海
	* [StackOverFlowError 常见原因及解决方法](docs/diagnosis/jvm/exception/系统稳定性——StackOverFlowError常见原因及解决方法.md)@涯海
	* [Dubbo 常见错误及解决方法](docs/diagnosis/plugin/rpc/系统稳定性——Dubbo常见错误及解决方法.md)@空冥



## 专栏建设
* 目标用户：稳定性相关的从业人员、技术决策者、爱好者等。
* 参与形式：欢迎一切有想法或兴趣的同学一起共建，可以自由选择参与内容编写、渠道宣传、社区维护等活动。
* 写作原则：通俗易懂，让读者有所收获，尽量避免介绍公网无法获取的内部产品或工具。
* 内容格式：Markdown & PDF 格式，易于传播、分享与共建。
* 内容管理：文档即代码，通过 Git 管理；代码目录与内容框架目录保持一致。会定期 Review 代码/目录结构。
* 内容编写：建议内容原创或再创作。为了保障文章质量，不建议直接转载。
* 问题列表：所有人有任何问题和建议都可以通过 Issue 的形式提交。


## 目录提纲
* 目录拆分尽量遵循 MCME 原则（互相独立，完全穷尽）。
* 每个专题尽量保证 3 篇及以上的优质文章。
* 内容目录一定要与代码目录保持结构一致。


## 文档提纲（仅供参考）
* 文档类型
	* 原创，注明作者与创作时间。
	* 转载，取得原作者许可，注明出处，注意许可协议风险。
* 一句话标题，提纲挈领地表达要解决的问题。
	* 标题应该表达的是问题的核心线索，而不是深层次原因。因为用户排查问题时只能看到表象。比如 SQL 慢是表象，没有创建索引是原因。
	* 可以在标题后的第一段做一些补充，对该问题做一个概括性描述，整体结构按照总-分-总模式。
* 问题的现象是什么？
* 问题产生的原因是什么？
	* 如果有前置的背景知识，可以科普一下，让读者更容易理解。
* 怎么去排查，会用到哪些工具？
	* 详细介绍排查思路与路径，最好是小白式操作，事无巨细，能够完美复现。
	* 在本模块可以介绍一些开源或云产品的使用，不要涉及内部产品，另外产品介绍不要带有太明显的主观偏向性。
* 解决后的效果如何？
	* 前后效果对比。
	* 简要的问题复盘总结，文档收尾。
* 推荐阅读/产品链接/公众号/交流群等。


## 友情链接
* [Chaosblade——故障演练开源项目](https://github.com/chaosblade-io/chaosblade)
* [Arthas——Java 在线诊断开源项目](https://github.com/alibaba/arthas)
* [SOFATracer——分布式链路追踪开源项目](https://www.sofastack.tech/)

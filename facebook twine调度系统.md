# 论文名称
Twine: A Unified Cluster Management System for Shared Infrastructure
https://www.usenix.org/system/files/osdi20-tang.pdf

# 特点
* 一套控制平面可以管理100w台机器
* 应用可以和控制平面配合实现task的生命周期的自我管理
* 主机级别的环境定制
* 倾向于单核64M的小型节能机器（不具有通用型）

# 架构和实现
## 概念
* Entitlement: 代表拥有1套host profile的一批机器组成的虚拟集群，可动态增减机器，机器可跨DC
* Shard: 管理多个entitlement的调度分片，1个region下可以有多个shard。1个job必须在1个shard范围内调度，shard可根据资源需求动态增减entitlement
* Host Profile: 关联1个entitlement，加入该entitlement下的所有机器都要安装该装机模板，可实现秒级配置
* Capacity Portal: 用户可以查询和修改Entitlement
* Front End: 用户部署job的入口
* Scheduler: 管理job和task的生命周期，编排job
* TaksController: 可选的。scheduler可以与用户自定义的TaskController交互，决定task的启停
* Allocator: 分配机器给Entitlement，调度task到机器
* Rebalancer: 异步和持续的优化Allocator的决策，均衡CPU、功耗、网络的使用率
* Resource Broker(RB): 存储机器基本信息，以及故障和维修的事件
* Ops Planner: DC域管理员通过它运维机器
* Health Check Service (HCS): 监控机器并更新状态到RB
* SideKick: 切换主机的配置
* Service Resource Manager (SRM) : 动态伸缩job响应负载的变化
* Conveyor: 持续部署系统
* Application-Level Scheduler: 在twine之上对业务开放的应用调度框架，twine为其提供资源，业务自行定制任务编排，如stateful，batch,AI等
![image](https://user-images.githubusercontent.com/10750904/156516453-ea00495c-2641-48dd-b89d-cd20536fa9a4.png)
![image](https://user-images.githubusercontent.com/10750904/156517334-67d0164a-c77f-41f6-bd55-fcb0cf50f744.png)
![image](https://user-images.githubusercontent.com/10750904/156517419-ddf265e5-5b72-4a47-83c0-6cf6ffa5dff4.png)

## 设计
![image](https://github.com/1032120121/paper/blob/main/%E6%88%AA%E5%B1%8F2022-03-03%2015.51.44.png)

# 高扩展性
## 分片扩展
Entitlement所属的机器可动态加减；Shard过载时Entitlement可跨Shard迁移，该Entitlement的task不用重启；job可以跨Entitlement自动的迁移，编排的方式用户可自定义。
facebook线上最大的调度Shard管理17w台机器，最大的Entitlement有6w台机器，最大的job有1.5w个task；调度峰值1000job/s，单job 36个task
![image]

## 关注点分离

# 设计原则
* FrontEnd&Allocator&scheduler&RB&HCS&SideKick等组件都是分shard的，shard不能太大，否则故障域过大
* 1个控制面的组件都是保证高可用的
* 控制面组件故障不影响Task运行
* 大面积的销毁操作比如shuffle task等要限流
* 1个DC内实现网络冗余

# 个人总结
twine是资源调度和任务调度分离的架构，单控制面可支持更多的机器，每个控制面为了做故障冗余预留的资源总体就会变小。动态资源池可随着任务负载的增减动态加减机器，释放的机器统一归属每个shard下的free池。同时配合facebook高效的入池机制实现了机器环境的快速配置。

# 论文名称
Twine: A Unified Cluster Management System for Shared Infrastructure
https://www.usenix.org/system/files/osdi20-tang.pdf

# 特点
* 一套控制平面可以管理100w台机器
* 应用可以和控制平面配合实现task的生命周期的自我管理
* 主机级别的环境定制
* 倾向于单核64M的小型节能机器（Power-efficient machines）
# 架构设计
## 概念
* entitlement：代表拥有1套host profile的一批机器组成的虚拟集群，可动态增减机器，机器可跨DC
* shard：管理多个entitlement的调度分片。1个job必须在1个shard范围内调度，shard可根据资源需求动态增减entitlement
* host profile：关联1个entitlement，加入该entitlement下的所有机器都要安装该装机模板，可实现秒级配置
* scheduler：
* allocator：分配器。


![image](https://user-images.githubusercontent.com/10750904/156516453-ea00495c-2641-48dd-b89d-cd20536fa9a4.png)
![image](https://user-images.githubusercontent.com/10750904/156517334-67d0164a-c77f-41f6-bd55-fcb0cf50f744.png)
![image](https://user-images.githubusercontent.com/10750904/156517419-ddf265e5-5b72-4a47-83c0-6cf6ffa5dff4.png)



## 主体

# 组件交互流程

# 设计原则

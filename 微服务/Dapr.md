# Dapr

![2025-06-19-00-21-08.png](./images/2025-06-19-00-21-08.png)

Dapr 是一个盒子里的分布式系统工具包。它解决了应用程序的外围集成问题，通过标准化的 API 和 Sidecar 架构，将分布式系统的复杂性封装成可插拔的构建块，让开发者像搭积木一样构建弹性的微服务应用。

应用永远只和本地的 localhost:3500（Dapr Sidecar）对话，Sidecar自动处理服务发现、加密、重试等脏活累活。

Dapr提供的分布式系统构建块有资源绑定、分布式锁、jobs等等，每个构建块 API 都是独立的，所以可以在应用中使用任意数量的它们。

<hr>
帮助理解：
有一个应用需要redis，普通用k8s部署和加了dapr的区别：

|痛点|k8s方案|Dapr方案|
|--|--|--|
|连接字符串硬编码|ConfigMap/Secret存储，需重新部署|Component YAML定义，动态注入|
|切换数据库技术栈|需修改应用代码（驱动/API调用方式）|仅修改Component类型（代码0改动）|
|多环境适配|需要多套ConfigMap|通过Sidecar注入环境差异|
|运行时热更新|ConfigMap需要重启Pod生效|Component变更自动热加载（部分支持）|

<hr>




## 资料
- https://docs.dapr.io/zh-hans/concepts/overview/
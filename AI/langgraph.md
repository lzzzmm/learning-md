# 入门教程

LangGraph 是由 LangChain 团队开发的一个低层级 Agent 编排框架，专为构建有状态、长时运行的 AI 工作流而设计。

LangGraph 将工作流建模为有向图。

## 核心概念

![2026-04-19-15-46-05.png](./images/2026-04-19-15-46-05.png)


### 图 Graph
整个工作流的蓝图，定义了 Agent 的完整逻辑结构。它由节点和边组成。

### 状态 State

是贯穿整个图的共享数据结构。每个节点可以读取和更新 State，更新后的 State 会传递给下一个节点。

### 节点 Nodes
节点是普通的 Python 函数，接收当前 State，返回更新后的 State（部分字段）。

### 边 Edges
边定义节点之间的流转方式：
- 普通边：固定路径，node_a -> node_b
- 条件边：根据 State 动态路由，node_a -> node_b 或 node_c
- 起始边：START -> 第一个节点
- 结束边：某节点 -> END




# 资料
- https://docs.langchain.com/oss/python/langgraph/quickstart#full-code-example
- https://www.runoob.com/ai-agent/langgraph-quick-start.html
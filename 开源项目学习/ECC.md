一个来自 Anthropic 黑客松获胜者的“AI Agent 操作系统”

是一整套 面向 AI Agent（Claude Code / Cursor / Codex 等）的生产级系统框架。

主要是学习其中的结构，框架设计理念。

核心文件夹
- skills：技能，匹配进入对应模型，每个是一个Markdown文件
- commands：斜杠命令，触发一套完整流程，e.g /plan 自动规划在动手
- agent：专业角色，有固定工作流
- rules：规则集，按编程语言分类，claude自动遵守
- hooks：自动触发器，操作前后自动执行，给claude装反射神经
- mcp-configs：mcp配置

## skills
一个Markdown文件，写清楚 “你是谁，你的职责，遇到这类任务怎么做”

调用方式`/skill-name`







toolBox
llmwiki
skill Seeker
ref mcp


核心必备：
- dotnet-patterns： C# 和 .NET 开发模式与最佳实践。涵盖不可变性、async/await、依赖注入、Options 模式、Result 模式、EF Core 仓储模式、Middleware、Minimal API、Guard Clauses 等。编码和重构时自动激活。
- csharp-reviewer：C# 代码审查专家。涵盖 .NET 规范、async模式、安全漏洞（SQL注入、反序列化漏洞）、错误处理、可空引用类型、性能优化、命名规范。对所有 C# 代码变更强制使用。
- csharp-testing： C# 测试模式。涵盖 xUnit、FluentAssertions、NSubstitute/Moq 模拟、Testcontainers集成测试、WebApplicationFactory、Bogus 测试数据生成。

安全相关：
- security-review： 安全漏洞检测和修复。对处理用户输入、认证、API 端点、敏感数据的代码进行安全审查。

通用代码质量：
- tdd-guide： TDD 开发引导，强制测试优先方法论，确保 80%+ 测试覆盖率。
- code-reviewer：通用代码审查专家，代码质量、安全性和可维护性。
-  silent-failure-hunter：审查代码中的静默失败、吞掉的错误、不良的 fallback 处理

- api-design： API 设计最佳实践。如果是 Web API 开发很有用
-  security-bounty-hunter：安全漏洞挖掘，对安全敏感项目有帮助

核心就是 3 个 — dotnet-patterns（编码指南）、csharp-reviewer（代码审查）、csharp-testing（测试实践）。加上security-review（安全审查）作为辅助。

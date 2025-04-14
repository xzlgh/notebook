## 目录页
为什么需要单元测试？

单元测试的核心概念

前端单元测试工具链

编写高质量测试的实践

常见问题与调试技巧

单元测试与持续集成

总结与资源

## 详情

### 1. 为什么需要单元测试？
痛点场景（引发共鸣）

“改了一行代码，线上炸了三个页面”

“不敢重构祖传代码，牵一发而动全身”

“联调耗时超过开发时间，团队互相甩锅”

单元测试的价值

✅ 快速反馈，减少调试时间

✅ 提升代码可维护性（鼓励模块化设计）

✅ 保障重构安全性

✅ 作为“活文档”描述代码行为

### 2. 单元测试的核心概念
单元测试定义

针对代码最小单元（函数、组件）的独立验证

与集成测试、E2E测试的对比（金字塔模型图）

核心原则

FAST原则（Fast, Automated, Specific, Thorough）

测试应具备确定性（Deterministic）

关键要素

测试用例结构：Arrange-Act-Asser

```js
// 示例：测试一个加法函数  
test('adds 1 + 2 to equal 3', () => {  
  // Arrange  
  const a = 1, b = 2;  
  // Act  
  const result = add(a, b);  
  // Assert  
  expect(result).toBe(3);  
});  
```

### 3. 前端单元测试工具链
主流工具对比

工具	适用场景	特点
Jest	通用测试框架	零配置、快照测试
Testing Library	React/Vue组件测试	贴近用户交互的测试
Cypress	组件测试/E2E	可视化调试
Vitest	Vite项目测试	超快速度、兼容Vite生态
工具链组合推荐

React项目：Jest + React Testing Library + MSW（Mock API）

Vue项目：Vitest + Vue Test Utils + Testing Library

工具链演示（代码截图或GIF）

### 4. 编写高质量测试的实践
组件测试实战

测试什么？

渲染是否正确（文本、样式、子组件）

交互逻辑（点击、输入、事件触发）

状态变化（如useState、Vue reactive）

```jsx
// React组件测试示例  
test('button click triggers onSubmit', async () => {  
  const mockSubmit = jest.fn();  
  render(<Form onSubmit={mockSubmit} />);  
  fireEvent.click(screen.getByRole('button'));  
  expect(mockSubmit).toHaveBeenCalled();  
});  
```

Mock技巧

如何Mock API请求？（MSW/axios-mock-adapter）

如何Mock第三方库？（jest.mock）

快照测试

适用场景：UI组件防止意外变更

注意：避免过度依赖快照！


### 5. 常见问题与调试技巧
典型问题集锦

“测试通过但线上报错？” → 检查Mock是否过度

“测试耗时太长？” → 使用jest --watch或vitest --ui

“如何测试异步逻辑？” → async/await + act()

调试技巧

使用screen.debug()输出DOM结构

在测试中插入console.log（需谨慎）

利用Chrome DevTools调试Jest测试


### 6. 单元测试与持续集成
自动化测试流程

本地开发：Husky + lint-staged（提交前自动运行测试）

CI/CD：GitHub Actions/Jenkins配置测试流水线

测试覆盖率报告

使用jest --coverage生成报告

覆盖率指标解读（80%以上是理想目标，但质量 > 数量）


### 7. 总结与资源
Key Takeaways

单元测试是开发者的安全网，不是负担

从关键路径开始，逐步覆盖

测试代码和生产代码同等重要！

学习资源推荐

书籍：《Testing JavaScript Applications》

文档：Jest官方文档、Testing Library最佳实践

工具链：Vitest官网、Cypress学习中心


### 附录：PPT设计建议
可视化元素

插入代码截图（高亮关键部分）

使用流程图说明测试流程

添加幽默表情包调节气氛（如“测试通过时的我 vs 测试失败时的我”）

互动环节

现场演示：写一个带Bug的组件，展示测试如何捕获问题

提问：“你们项目中哪些模块最需要测试？”

备注提示

在备注区域添加演讲重点和扩展说明


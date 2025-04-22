# react18+jest单元测试分享

## 目录页

<b> [单元测试的核心概念](#为什么需要单元测试？) </b>
<b> [为什么需要单元测试？](#单元测试的核心概念)</b>
<b> [前端单元测试工具链](#前端单元测试工具链) </b>
<b> [编写高质量测试的实践](#编写高质量测试的实践) </b>
<b> [常见问题与调试技巧](#常见问题与调试技巧) </b>
<b> [单元测试与持续集成](#单元测试与持续集成) </b>
<b> [总结与资源](#总结与资源) </b>

## 详细内容

### 为什么需要单元测试？

针对当前的观察到的现象

1. 改一行代码,出现多个bug;
2. 祖传代码不敢轻易改，牵一发而动全身，容易出现拆东墙补西墙的情况；

单元测试的价值

✅ 快速反馈，减少调试时间（如果每个单元mock了，确保逻辑正确，能够减少调试过程中前端的出错概率）

✅ 提升代码可维护性（鼓励模块化设计）

✅ 保障重构安全性

✅ 作为“活文档”描述代码行为（体现在单测用力用例的描述中）


### 单元测试的核心概念

<b>测试分类：</b>单元测试、集成测试、E2E测试；

==单元测试是对代码最小单位的独立验证；==

<b>代码最小单位:</b> 函数、组件（最小单位的组件，如果组件中包括一些子组件，则应该归类为集成测试）；

<b>单元测试的主要组成部分</b>

- 模拟数据 (mock)
- 测试套件（descript）
- 测试用例 (it/test)
- 断言 (expect)
- 测试报告 (report)

eg:
```js
function add(a, b) {
  return a + b;
}
```

```js
// 第一个参数用来描述该测试用例的测试部分；
test('adds 1 + 2 to equal 3', () => {  
  // Arrange  
  const a = 1, b = 2;  
  // Act  
  const result = add(a, b);  
  // Assert  
  expect(result).toBe(3);  
});  
```

在单测足够简单的情况下，可以不使用测试套件（descript);多个测试用例可以作为一个测试套件；测试套件的第一个参数同样可以描述测试套件。

==无论是单元测试、集成测试还是E2E测试，必须具备确定性。（即必须保障一个完整的单元测试中必须要有断言）；一个不确定的单元测试是没有意义的==

### 前端单元测试工具链
主流工具对比

| 工具            | 适用场景          | 特点                   |
| --------------- | ----------------- | ---------------------- |
| Jest            | 通用测试框架      | 零配置、快照测试       |
| Testing Library | React/Vue组件测试 | 贴近用户交互的测试     |
| Cypress         | 组件测试/E2E      | 可视化调试             |
| Vitest          | Vite项目测试      | 超快速度、兼容Vite生态 |
		
		
工具链组合推荐

React项目：Jest + React Testing Library + MSW（Mock API）

Vue项目：Vitest + Vue Test Utils + Testing Library

### 编写高质量测试的实践

<b>测试的FIST原则</b>

- Fast（快速）：测试应秒级完成，避免开发者因等待而减少执行频率。
- Isolated（隔离）：每个测试用例独立运行，不依赖其他测试的状态。
- Repeatable（可重复）：在任何环境（本地、CI）中结果一致。
- Self-Validating（自验证）：测试结果应为明确的通过/失败，无需人工判断。
- Timely（及时）：测试代码与生产代码同步编写（TDD更佳）。

<b>明确测试目标，应遵循以下原则：</b>

*测试代码应像生产代码一样认真维护*。

*测试的目的是发现Bug，而不是证明代码能运行*。

单元测试应当覆盖每一个switch、if..else、|| 、&&等运算过程中的每个场景。通过mock对应的场景来覆盖。

<b>编写可读性高的单元测试</b>

可读性高，需要在测试套件和测试用例的描述中，对该测试做详细的说明 —— 应当写明什么场景，产生什么结果。

<b>测试边界和异常</b>

- 验证正常输入输出；
- 验证边界：1. 空值（null/undefined/''等）；2. 极端值（数字益处、超长字符串、空字符等等）；3.错误的权限/非法操作（传入非法的数据类型等）；
- 异常：确保错误消息能够被正确捕获。

<b>避免常见的陷阱</b>

- 覆盖率陷阱：追求有意义的覆盖率（如100%行覆盖 ≠ 无Bug），关键逻辑需重点覆盖。
- 过度mock: mock的数据时，应当重点mock外部依赖；
- 过度细节测试：避免断言结果过度依赖于细节（如UI结构），应该重点基于用户行为测试。

#### 函数测试

单元测试中函数的测试比较简单，单个函数也是单元测试的最小测试单位；函数的测试，测试用例覆盖场景全面，尤其是极限值的测试用例应重点写。

##### 包含定时器的函数单元测试


#### 组件测试

#### hooks测试

#### 特殊的测试

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


### 常见问题与调试技巧
典型问题集锦

“测试通过但线上报错？” → 检查Mock是否过度

“测试耗时太长？” → 使用jest --watch或vitest --ui

“如何测试异步逻辑？” → async/await + act()

调试技巧

使用screen.debug()输出DOM结构

在测试中插入console.log（需谨慎）

利用Chrome DevTools调试Jest测试


### 单元测试与持续集成

#### 测试覆盖率报告

##### 本地运行单元测试时，想要查看自己负责模块的单元测试覆盖率报告

```bash
# 例如：检查tools下面的测试覆盖率，执行如下命令
yarn test src/views/routes/Tools --coverage --collectCoverageFrom='src/views/routes/Tools/**/*.{js,ts,tsx}'
```
得到覆盖率报告：

![测试报告](./images/覆盖率测试报告1.jpg)


自动化测试流程

本地开发：Husky + lint-staged（提交前自动运行测试）

CI/CD：GitHub Actions/Jenkins配置测试流水线

测试覆盖率报告

使用jest --coverage生成报告

覆盖率指标解读（80%以上是理想目标，但质量 > 数量）


###  总结与资源
Key Takeaways

单元测试是开发者的安全网，不是负担

从关键路径开始，逐步覆盖

测试代码和生产代码同等重要！

学习资源推荐

书籍：《Testing JavaScript Applications》

文档：Jest官方文档、Testing Library最佳实践

工具链：Vitest官网、Cypress学习中心


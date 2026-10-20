# Web 编程课程实践 · 个人简历 + Todo V2.0

深圳大学《Web 编程》课程作业仓库。页面是一个响应式个人简历，首页同时承载
**Todo V2.0（数据驱动 + 持久化 + 筛选）** 应用。

- 仓库地址：<https://github.com/kartwatt-cpu/web-week7>
- 直接本地运行：用浏览器打开 `index.html` 即可（所有资源都是相对路径，`file://` 也能跑）

## 目录结构

| 路径 | 说明 |
| --- | --- |
| `index.html` | 个人简历页面 + Todo V2.0 应用（第 7 周交付物） |
| `style.css` | 全部样式：Grid 页面骨架 + Flex 局部排列 + Todo 组件 |
| `头像.jpg` | 页面头像资源 |
| `docs/` | 各周 AI 协作日志与实验课件 |
| `AGENTS.md` | 给 AI 编程助手的仓库说明 |

## 如何运行

不需要构建工具，也没有任何依赖：

```bash
# 方式一：直接双击 index.html，或用浏览器打开
# 方式二：起一个本地静态服务
python -m http.server 8000
# 然后访问 http://localhost:8000/
```

## V2.0 做了什么

第 6 周的写法是「点一次按钮就改一次 DOM」，状态散落在 DOM 里，刷新即丢失。
第 7 周把状态收敛到唯一的数据源 `todos` 数组，页面只是它的投影：

```
用户操作 → 改数据（生成新数组）→ save() → render() 重画整个列表
```

| 环节 | 实现 |
| --- | --- |
| 数据驱动 | `todos` 是唯一任务状态来源；`render()` 先按 `filter` 过滤，再建元素、挂载 |
| 不可变更新 | 添加 `concat`、删除 `filter`、切换 `map` + `Object.assign`，都不改原数组 |
| 持久化 | `save()` / `load()` 读写 `localStorage`，并对读写异常与坏数据做了降级 |
| 筛选 | `filter` 视图状态（`all` / `active` / `done`）只重渲染，不写 `localStorage` |
| 边界 | 空输入与纯空格不添加；`id` 由「时间戳 + 自增序号」生成，同一毫秒也不会撞号 |
| 可访问性 | `aria-label` / `aria-live="polite"` / `aria-pressed`，删除按钮带 `type="button"` |

核心函数：`addTodo()` / `removeTodo()` / `toggleTodo()` / `setFilter()` / `render()` /
`save()` / `load()` / `nextId()` / `clearCompleted()`。

其中 `clearCompleted()`（在 `removeTodo()` 旁边）是「脱离 AI 小改」环节手写的：
`todos = todos.filter(t => !t.completed)` 后接 `save(); render();`，
控制台调用即可删除所有已完成任务。

## 验证方式

1. 浏览器打开 `index.html`，控制台确认无报错；
2. 依次测试添加、点击条目切换完成状态、点 `×` 删除、三个筛选按钮、清除已完成；
3. 刷新页面确认数据与完成状态仍在、筛选回到「全部」；
4. 控制台执行 `localStorage.getItem('todos')` 检查落盘数据；
5. 控制台调用 `clearCompleted()` 验证「删除所有已完成任务」。

## 界面截图

真实浏览器（Edge 无头模式）渲染结果；示例数据通过 `index.html?demo=1` 注入，正常打开页面不受影响。

> 其中 4 张已作为插图放进 AI 协作日志（图 1 桌面整页、图 2 待办区块特写、图 3 筛选未完成、图 4 窄屏），
> 排版预览见 `docs/AI协作日志W7-预览.pdf`。

**桌面宽屏（1000px）整页**

![桌面宽屏整页](docs/images/01-desktop-full.png)

**Todo 区块特写 —— 已完成项带删除线，底部实时统计**

![Todo 区块特写](docs/images/02-desktop-todo.png)

**筛选「未完成」/「已完成」**

| 未完成 | 已完成 |
| --- | --- |
| ![筛选未完成](docs/images/03-desktop-filter-active.png) | ![筛选已完成](docs/images/04-desktop-filter-done.png) |

**窄屏（约 490px 视口）**

| Todo 区块 | 整页 |
| --- | --- |
| ![窄屏 Todo](docs/images/05-narrow-todo.png) | ![窄屏整页](docs/images/06-narrow-full.png) |

## Git 工作流

课程要求「分支开发 → 多次提交 → 合并回 main」，提交信息按 `feat:` / `refactor:` / `docs:` 前缀组织：

| 提交 | 内容 |
| --- | --- |
| `chore: 导入第 5/6 周…` | 基线：第 6 周的手动 DOM 版本 |
| `refactor: V2.0 数据驱动重构…` | 三个操作函数改为不可变更新 |
| `feat: 加入 all/active/done 筛选…` | 视图状态与筛选 |
| `feat: localStorage 持久化与边界处理` | 持久化与降级路径 |
| `docs: 补充 V2.0 说明…` | 说明与手写函数记录 |
| `docs: 提交第 7 周 AI 协作日志` | 本周提交物 |

分支 `feature/v2-data-driven` 完成后合并回 `main`。

## 相关记录

- `docs/[王梓健][2024150200]AI协作日志W7.docx` —— 第 7 周 AI 协作日志（本次提交物）
- `docs/第7周AI协作记录.docx` —— 本周实验记录模板（原始格式）
- `docs/第七周实验.pptx` —— 本周实验课件
- `docs/第6周AI协作记录.docx` —— 上周 AI 协作日志

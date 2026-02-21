# 厨房陪我 MVP Figma 文档规范

## 1. 目的

将品牌原则转化为可执行设计稿与可交付原型，支持产品评审与研发实现。

## 2. Figma 文件结构

### 2.1 页面（Pages）

1. `00_Foundation`
2. `01_Flow_Start`
3. `02_Flow_Cooking`
4. `03_Flow_Reflection`
5. `04_History`
6. `05_Edge_States`
7. `99_Handoff`

### 2.2 命名规范

- Frame：`[Flow]-[Screen]-[State]`
  - 例：`Cooking-Session-Listening`
- Component：`[Domain]/[Name]/[Variant]`
  - 例：`Voice/Orb/Active`
- Prototype 节点：`P-[Flow]-[Step]`

## 3. 设计 Tokens（MVP）

- 圆角：`12 / 16 / 24`
- 阴影：仅柔光阴影，不使用硬投影
- 颜色角色
  - `bg/warm`
  - `surface/soft`
  - `text/primary`
  - `text/secondary`
  - `accent/glow`
- 状态表达
  - 静默：无光晕
  - 进行中：外层柔光晕（呼吸）
  - 已记录：微亮点

## 4. 核心页面清单（MVP 必出）

### 4.1 Start 流

- `Start-Home-Default`
- `Start-RecipePicker-List`
- `Start-RecipePicker-Selected`

### 4.2 Cooking 流

- `Cooking-Session-Idle`
- `Cooking-Session-Listening`
- `Cooking-Session-Speaking`
- `Cooking-Session-Interrupted`
- `Cooking-Session-TimerActive`

### 4.3 Reflection 流

- `Reflection-Entry-Default`
- `Reflection-Entry-Filled`
- `Reflection-Success`

### 4.4 History 流

- `History-Calendar-Week`
- `History-Memory-List`
- `History-Memory-Detail`

### 4.5 Edge States

- `Edge-MicPermission-Denied`
- `Edge-Network-Unstable`
- `Edge-STT-Failed`
- `Edge-TTS-Failed`

## 5. 原型交互要求

- 主流程原型必须可点击跑通：
  - Home -> 选菜 -> Cooking -> Reflection -> History
- Cooking 页需展示 3 类动态状态：
  - 聆听中
  - 回复中
  - 被打断后恢复
- 异常流程需可演示从失败回归主链路

## 6. 文案与语气规范

- 允许：建议式、陪伴式表达
- 禁止：命令式、评判式表达
- 禁止词：任务/打卡/KPI/管理/你必须/快点

## 7. 交付给研发的 Figma 要件（99_Handoff）

- 标注
  - 间距、字号、圆角、层级
- 组件
  - 变体定义和交互状态说明
- 导出
  - 图标 SVG
  - 动效说明（时长、缓动、触发条件）
- 规格表
  - 每个页面对应需求编号（FR-xxx）

## 8. 评审清单

- 视觉风格是否符合 Warm Minimal Ambient
- 是否出现任务化符号或禁忌词
- 主流程是否 5 分钟内可理解
- 边界场景是否有温柔兜底

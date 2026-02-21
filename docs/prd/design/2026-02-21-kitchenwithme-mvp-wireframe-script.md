# 厨房陪我 MVP Figma 低保真线框脚本

## 1. 使用方式

- 目标：让设计师可按脚本快速出低保真 + 可点击原型
- 依据：
  - `docs/prd/core/2026-02-21-kitchenwithme-mvp-prd.md`
  - `docs/prd/core/2026-02-21-kitchenwithme-mvp-ia-screen-spec.md`

## 2. 画板规格

- Desktop：1440 x 1024
- Mobile：390 x 844
- 建议先出 Mobile 主流程，再补 Desktop 适配

## 3. 页面脚本（按 Flow）

## 3.1 Flow Start

### Frame: Start-Home-Default

- 区块 A：顶部标题（今日开火）
- 区块 B：主 CTA 卡片（开始陪我做饭）
- 区块 C：最近味觉记忆摘要
- 区块 D：底部四导航
- 交互：点击主 CTA -> `Start-RecipePicker-List`

### Frame: Start-RecipePicker-List

- 区块 A：返回 + 标题（从已有菜开始）
- 区块 B：菜谱列表（卡片可选）
- 区块 C：底部固定按钮（默认禁用）
- 交互：选中任一菜谱 -> `Start-RecipePicker-Selected`

### Frame: Start-RecipePicker-Selected

- 变化：选中卡高亮 + 底部按钮可点击
- 交互：点击按钮 -> `Cooking-Session-Idle`

## 3.2 Flow Cooking

### Frame: Cooking-Session-Idle

- 区块 A：菜名 + 当前步骤序号
- 区块 B：语音球（静默态）
- 区块 C：步骤文字卡片
- 区块 D：快捷指令 Chips（下一步/重复/慢一点/计时）
- 交互：进入后自动转 Listening

### Frame: Cooking-Session-Listening

- 变化：语音球外圈呼吸动效
- 提示：轻文案“我在听你说”
- 交互：识别到输入 -> Speaking

### Frame: Cooking-Session-Speaking

- 变化：语音球主光增强
- 区块 C：系统回复文案
- 交互：用户打断 -> Interrupted

### Frame: Cooking-Session-Interrupted

- 变化：中断提示条（非警报色）
- 交互：短暂后回 Listening

### Frame: Cooking-Session-TimerActive

- 新增：计时器浮层（剩余时间 + 温柔提醒）
- 交互：到时提示 -> 回当前步骤

## 3.3 Flow Reflection

### Frame: Reflection-Entry-Default

- 区块 A：标题（把这次味道轻轻记下）
- 区块 B：味觉输入框
- 区块 C：心情标签/输入
- 区块 D：提交按钮（默认禁用）

### Frame: Reflection-Entry-Filled

- 变化：按钮可点击
- 交互：提交 -> `Reflection-Success`

### Frame: Reflection-Success

- 区块 A：成功反馈文案
- 区块 B：两个去向按钮（回首页 / 看记录）

## 3.4 Flow History

### Frame: History-Calendar-Week

- 区块 A：周视图日历
- 区块 B：当日开火点亮状态
- 交互：点某天 -> `History-Memory-List`

### Frame: History-Memory-List

- 区块 A：日期标题
- 区块 B：记录列表（菜名+一句味觉）
- 交互：点记录 -> `History-Memory-Detail`

### Frame: History-Memory-Detail

- 展示：菜名、步骤摘要、味觉、心情、时间
- 交互：再做一次（回 Start）

## 3.5 Edge States

### Frame: Edge-MicPermission-Denied

- 文案结构：
  - 第一行：安抚
  - 第二行：操作建议（开启麦克风 / 点按继续）

### Frame: Edge-Network-Unstable

- 文案：当前先用文字陪你继续
- 交互：重试连接 / 继续当前步骤

### Frame: Edge-STT-Failed

- 文案：可说短指令示例
- 交互：一键重听提示

### Frame: Edge-TTS-Failed

- 文案：切换文字模式继续
- 交互：返回语音重试

## 4. 动效标注（MVP 最小）

- 语音球呼吸：1200ms ease-in-out 循环
- 状态切换淡入：180ms ease-out
- 异常提示条出现：160ms ease-out

## 5. 组件最小集

- `Voice/Orb`：Idle, Listening, Speaking, Interrupted
- `Card/RecipeItem`：Default, Selected
- `Chip/QuickCommand`：Default, Active
- `Button/Primary`：Disabled, Enabled
- `State/EdgeBanner`：MicDenied, WeakNet, STTFail, TTSFail

## 6. 标注要求（交付）

- 每个 Frame 标注对应 FR 编号
- 每个交互箭头标注触发条件
- 每个异常态标注回归主流程路径

## 7. 设计自检清单

- 主流程是否 5 分钟内能讲清楚
- 是否突出做饭中陪伴而非信息堆砌
- 文案是否全部通过品牌禁忌检查
- 关键异常是否都有“继续做饭”的出口

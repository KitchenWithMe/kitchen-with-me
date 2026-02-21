# 厨房陪我 MVP 工程交付与验收清单

## 1. 目标

为研发提供可直接拆解的实现边界、数据契约、测试范围与发布门槛。

## 2. 建议技术架构（Web MVP）

- 前端：Web 应用（会话 UI + 语音状态）
- 后端：会话编排服务 + 记录服务
- 语音：Realtime STT/TTS（支持中断）
- 数据：菜谱数据、会话日志、开火后记录
- 观测：埋点 + 日志 + 错误追踪

## 3. 模块拆分

- `session-orchestrator`
  - 维护步骤状态、会话上下文、计时器
- `voice-gateway`
  - 管理语音流、打断、重试策略
- `recipe-service`
  - 提供已有菜与步骤数据
- `reflection-service`
  - 保存味觉/心情记录
- `analytics`
  - 采集会话与性能指标

## 4. API 契约（MVP 建议）

### 4.1 菜谱与开火

- `GET /api/recipes`
  - 返回已有菜列表
- `POST /api/sessions`
  - 创建做饭会话，返回 session_id
- `GET /api/sessions/{session_id}`
  - 获取当前步骤与状态

### 4.2 语音会话

- `WS /api/realtime/sessions/{session_id}`
  - 上行：用户音频流/控制事件
  - 下行：转写文本、陪伴回复、状态事件

### 4.3 开火后记录

- `POST /api/reflections`
  - 入参：session_id, taste_note, mood_note
- `GET /api/reflections?date=YYYY-MM-DD`
  - 返回当日记录列表

## 5. 数据模型（最小集）

### 5.1 Session

- `id`
- `recipe_id`
- `status`（active/completed/interrupted）
- `current_step`
- `created_at`
- `updated_at`

### 5.2 Reflection

- `id`
- `session_id`
- `taste_note`
- `mood_note`
- `created_at`

### 5.3 Event（埋点）

- `event_name`
- `session_id`
- `timestamp`
- `payload_json`

## 6. 指标与告警

- 延迟指标
  - `stt_latency_p50/p95`
  - `tts_latency_p50/p95`
- 业务指标
  - `session_completion_rate`
  - `reflection_submit_rate`
  - `fallback_trigger_rate`
- 质量指标
  - `voice_error_rate`
  - `disconnect_rate`

## 7. 测试策略

### 7.1 E2E

- 已有菜选择 -> 做饭中陪伴 -> 开火后记录（主链路）
- 弱网恢复场景
- 麦克风权限拒绝与恢复

### 7.2 集成测试

- 会话状态机转换
- 打断与恢复流程
- 计时器并发冲突

### 7.3 单元测试

- 意图路由
- 兜底文案策略
- 数据写入校验

## 8. 发布检查单（Release Checklist）

- P0/P1 功能验收通过
- 关键指标可见且稳定
- 主要异常场景已通过回归
- 品牌禁忌检查通过（文案与视觉）

## 9. 开发边界

- 不引入打卡/KPI/进度条语义
- 不实现社交分享和内容流
- 不扩展到复杂营养或长期饮食管理

## 10. 版本节奏建议

- `MVP v0.1`：B 方案语音陪伴 + 轻记录 + 快速选菜
- `MVP v0.2`：优化语音稳定性和异常恢复
- `MVP v0.3`：满足条件后升级 A（开放式连续双向语音）

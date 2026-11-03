# Feature Catalogue / 功能清单

Documentation only — the implementation is proprietary and not distributed in this repository.
（仅文档；实现为专有资产，不在本仓库分发。）

---

## English

### Conversation core
- SSE streaming chat with interrupt / resend / edit, Markdown, code highlighting and Mermaid rendering.
- Multi-provider model routing (Bailian, OpenAI, Claude, …); every turn records the model actually used and why it was chosen.
- Input/output guardrails with sensitive-word filtering; cross-session long-term user memory extraction and recall.
- `@`-mention completion over four entity types: knowledge bases, files, presets, skills.
- Read-only session share links; account-synced preferences (theme, UI language, display settings).
- In-chat code interpreter (Python / Node) returning output, errors and artifacts.
- Global resident assistant: product-guide Q&A, direct database answers without model calls, global reminders for confirmations, and hand-off of execution tasks to autonomous agents.

### Autonomous agents (V2)
- ReAct thought–action–observation loop; multiple sessions in parallel with pause / resume / terminate.
- Checkpoints every 5 steps; three-branch recovery: remount after disconnect, terminal-state replay, resume of unfinished work.
- Coding toolset: file read/write, apply-patch / batch replace / diff / undo, symbol index, code search, Git, commands, lint & tests; repository wiki preserves structural knowledge.
- File-change ledger per task: modified files, net added/removed lines, diffs — separate from the artifact library.
- Metacognition tools: `think`, `critique`, `audit_assumptions`, `validate_claim`.
- Context governance: observation masking, context condenser, cache-prefix guard — long-task context and token cost controlled together.
- Human-in-the-loop: two-layer protection on dangerous operations (no jump-the-gun, pre-execution interception), with resume/cancel.
- Sub-task delegation (`DelegateTask` / `ParallelTask`) with real-time parallel lanes in the console.
- Self-evolution: task trajectories distilled offline into experience entries; reflection acceptance recorded against a run ledger; defensive rules injected by relevance gate.
- Runtime verification: receipt ledger, citation verifier, fact-forcing policy, failure classifier — model self-reports are reconciled against evidence.
- Document upload wall: the agent pushes an upload card, blocks until the file arrives, and continues in the same turn.
- Skill files (`.agent-skills/`) and user-configurable lifecycle hooks.

### Desktop automation (computerUse)
- Goal: "show me / click it for me", not "here is a script".
- macOS (JXA + CoreGraphics CGEvent + System Events) and Windows (UIA) implementations; Linux honestly refused.
- Accessibility-tree enumeration with `elementId`-based click / scroll / invoke / set_value / get_value; deep static text readable within a traversal budget.
- Vision/OCR text-box detection merged into the element table by IoU — fixes self-drawn controls missing from the AX tree.
- One-step save-panel submit (change directory, write name, confirm replace, filesystem assertion on landing); save receipts carry mtime-freshness assertions.
- Physical-pixel coordinate contract: screenshots report physical pixels, converted by scale before delivery, constant model-facing semantics.
- AX fake-confirmation defence: when Electron/Catalyst shim trees make write-read-back untrustworthy, the result degrades honestly to `unverifiable` with screenshot fallback.

### Local execution client (Go)
- Tray-resident native program; the web console is the task-management entry, with local login, capture, confirmation, self-check and emergency-control surfaces.
- Cross-end tool contract registers 44 capabilities (43 with Go implementation marks): files/patches, commands, system, desktop, browser, workspace.
- Tiered authorization: `system` mode broad reads with whitelist-only writes; `project` mode constrained to a workDir. Policies are application-level, not an OS sandbox, and say so.
- Two-level dangerous-command interception: absolutely-forbidden class rejected outright; deletion class gated by web-side HITL confirmation.
- Every command and result written to a local JSON Lines audit log.
- Version handshake (`minVersion`) with web download entry + sha256; artifacts never stored in the repository.
- Operation recorder captures click/keystroke/window-switch traces (JSONL) and distills them into reusable automations.
- Quick Capture global hotkey: screenshot/clipboard capture into a question or the knowledge base in one step.
- Folder knowledge sync: register local directories, incremental and manual sync; complements the web clipper.
- Delivery reliability: device binding, ACK, redelivery of unacknowledged requests, client-side dedup.

### Browser automation
- Reuses the user's real Chrome with logged-in sessions via a CDP tunnel (`connectOverCDP`).
- High-frequency actions: select / hover / upload / wait_for / dialog / tabs / navigation / PDF export.
- Human-like slider dragging: black-box edge template matching locates the gap; ease-out interpolation with arc trajectories.
- Screenshot vision + coordinate clicking; element coordinate parsing.
- Parallel sub-agents on isolated Playwright instances (concurrency cap 5).
- Safety gates: URL guard, SSRF protection, redirect-chain guard, irreversible-action gate, origin authorization registry.

### Voice interaction
- Real-time voice loop: ASR (DashScope realtime STT) → LLM → TTS.
- Continuous conversation: VAD silence detection auto-segments; listening resumes automatically after the AI finishes.
- Echo suppression and barge-in: capture pauses during playback; user speech interrupts TTS.
- Standalone TTS studio page with voice/parameter tuning; keyboard fallback when no microphone.

### Workflow orchestration
- Visual DAG editor on Vue Flow: drag-connect, minimap, context menu.
- 43 node types: basics, logic, AI, data transform, structure validation, document parse, persistent wait, status poll, human approval, plus the media production line.
- Media line: storyboard, video compose, color grade, subtitle generation, voice clone, BGM matching, frame extract, video understanding, quality gate.
- AGENT node: open tasks delegated to autonomous agents — the platform's differentiator.
- HITL question nodes suspend a running workflow, push via SSE, and back-fill the answer.
- Draft/publish isolation: editing, rollback and trial runs use drafts; open API, agents, MCP, cron and event triggers run published snapshots.
- Execution snapshots: historical canvases restore from the snapshot of their own run; waiting interactions survive refresh and reconnect.

### Experience & self-evolution
- Two-level Markdown experience library (general / task) with relevance retrieval into agent context.
- Visual management: statistics panel, CRUD, archive/activate, character budget control.
- Smart mining: LLM analyses conversation trajectories and proposes entries; offline trajectory distillation writes back.
- Reflection acceptance ledger: self-evolution suggestions traceable against code and run ledgers.

### Triggers & background collaboration
- Cron and event triggers for agents/workflows; inbound hook triggers with dedicated secrets, rate limiting and idempotency.
- Proactivity levels and quiet time windows for patrol tasks.
- Background-process continuation: exit notifications feed follow-up handling; user messages appended mid-run join at boundaries.
- Scoped authorization: remembered dangerous-operation approvals are viewable, revocable, and reuse-restricted by target and scope.

### Hosts & credentials
- Per-user host profiles: alias, address, environment, authentication method.
- Credential vault: encrypted at rest after form entry; commands resolve `{{cred:...}}` at the execution boundary; requires an independent master key.
- Data boundary: hosts/credentials are not shared across users by default; file downloads and screenshot checks verify ownership and real paths.

### MCP & A2A
- Static MCP servers connected at startup; dynamic install/uninstall at runtime without restart.
- 60-second health probes with automatic reconnect; tool cards with enable/disable in the console.
- The application itself can expose an MCP server to external clients.
- A2A protocol: agent cards, registration & discovery, cross-agent task delegation (`callA2aAgent` as a tool).

### RAG
- PDF / Word / Excel / TXT upload with incremental vectorization.
- Parent-child chunking, table structuring, scanned-document OCR; synchronous path detects and refuses, heavy work runs asynchronously.
- Hybrid retrieval: pgvector semantic recall + Lucene BM25 + RRF fusion + reranking, with user/source scope filters.
- Clickable citations jumping to the source fragment; section-path breadcrumbs.
- Fidelity / relevance evaluation panel; A/B evaluation batches.
- NotebookLM-style one-click podcast (two-host dialogue audio) and study-material generation.
- Optional web clipper extension (Manifest V3): selection, article body or link summary into the knowledge base.

### Multimodal & deep research
- Text-to-image (Tongyi Wanxiang) with multiple aspect ratios; image understanding in multimodal chat.
- AI document generation production line.
- Deep research: automatic question decomposition, multi-source search, synthesis, streamed output.

### Feishu integration
- WebSocket long connection for real-time messages.
- Tiered routing: simple chat / tool call / complex task.
- Rich media in/out (image, voice); scheduled triggers with completion pushes.

### Engineering infrastructure
- Full-link tracing spans (LLM_CALL / TOOL_CALL / VOICE_STT / VOICE_TTS), token accounting roll-up, trace replay.
- Monitoring dashboard: call volume, tokens, model distribution, cost.
- Stateless JWT auth with slider captcha; multi-user management.
- Flyway schema migrations; CI regression gate on PR and main push (backend regression subset, application boot test in disposable MariaDB/Redis/PgVector containers, frontend tests & build, Node bridge tests, Windows Go client tests).
- Homepage showcase: end-to-end replay of real curated tasks plus live entry points.

---

## 中文

### 对话核心
- SSE 流式对话，支持打断/重发/编辑、Markdown、代码高亮与 Mermaid 渲染。
- 多供应商模型路由（百炼、OpenAI、Claude 等），逐轮记录实际模型及选择原因。
- Input/Output 护栏与敏感词过滤；跨会话长期记忆提取与召回。
- `@` 引用补全四类实体：知识库、文件、预设、技能。
- 只读会话分享链接；主题、界面语言等偏好按账号同步。
- 对话内代码解释器（Python/Node），返回输出、错误与产物。
- 全局驻场助手：产品手册问答、部分数据库直答（不调模型）、确认类全局提醒、执行任务移交自主智能体。

### 自主智能体（V2）
- ReAct 思考-行动-观察闭环；多会话并行，支持暂停/恢复/终止。
- 每 5 步落盘检查点；断连重挂载/终态补发/未完成续跑三分支恢复。
- 编码工具集：文件读写、applyPatch/批量替换/对比/撤销、符号索引、代码搜索、Git、命令、Lint 与测试；Repo Wiki 保存仓库结构认知。
- 文件变更台账：按任务展示修改文件、净增删行与 diff，与产物库分工。
- 元认知工具：think / critique / audit_assumptions / validate_claim。
- 上下文治理：Observation Masking + Context Condenser + 缓存前缀守卫，长任务上下文与 Token 成本双治理。
- 人机协同：危险操作双层防护（防抢跑 + 前置拦截），支持恢复/取消。
- 子任务委派（DelegateTask / ParallelTask），前端泳道实时展示。
- 自进化：任务轨迹离线蒸馏为经验，反思验收对照运行账本落库，防御规则按相关性门控注入。
- 运行时验证：回执账本、引用核验、事实强制、失败分类——模型自报与证据对账。
- 文档上传墙：推送上传卡片并阻塞等待，上传后同轮继续处理。
- Skill 技能文件与用户可配置生命周期钩子。

### 桌面自动化（computerUse）
- 目标是"画给我看 / 帮我点"，而非"给个脚本"。
- macOS（JXA + CoreGraphics CGEvent + System Events）与 Windows（UIA）双实现，Linux 诚实拒收。
- 无障碍树枚举 + `elementId` 系 click/scroll/invoke/set_value/get_value；深层静态文本可读，遍历预算封顶。
- 本机 Vision/OCR 识别文字框按 IoU 补进元素表，治自绘控件读不进 AX 树。
- 保存面板一步式提交（换目录/写名/确认替换/文件系统落盘断言）；保存回执内置 mtime 新鲜度断言。
- 坐标契约：截图按物理像素报坐标，投递前按 scale 换算，对模型口径恒定。
- AX 假确认防护：Electron/Catalyst 的 shim 树写入回读不可信时如实降级为 `unverifiable` + 截图兜底。

### 本地执行客户端（Go）
- 托盘常驻原生程序；Web 为任务管理主入口，本机另有登录、采集、确认、自检与紧急控制界面。
- 跨端契约登记 44 条能力（43 条有 Go 实现标记）：文件/补丁、命令、系统、桌面、浏览器、工作区。
- 分级授权：system 模式广泛读取、写入限白名单目录；project 模式约束 workDir；策略为应用级，不等同于 OS 沙箱，如实说明。
- 危险命令两级拦截：绝对禁止类直接拒绝；删除类以 Web 端 HITL 确认为准。
- 每条命令与结果写本地 JSON Lines 审计日志。
- 版本握手（minVersion）+ Web 下载入口与 sha256；制品不入库。
- 录操作蒸馏：采集点击/击键/窗口切换轨迹（JSONL），蒸馏为可复用操作。
- Quick Capture 全局热键：截图/剪贴板捕获，一键提问或归档进知识库。
- 目录知识同步：本地文件夹注册、增量与手动同步，与网页剪藏互补。
- 投递可靠性：设备绑定、ACK、未确认请求重投与客户端去重。

### 浏览器自动化
- 复用本机 Chrome 真实登录态（CDP 隧道 connectOverCDP）。
- 高频动作：select/hover/upload/wait_for/dialog/标签页/前进/PDF。
- 滑块拟人拖拽：黑盒边缘模板匹配定位缺口，缓出插值 + 弧线轨迹。
- 截图视觉 + 坐标点击；元素坐标解析。
- 并行子代理：隔离 Playwright 实例（并发上限 5）。
- 安全闸：URL 守卫、SSRF 防护、重定向链守卫、不可撤销动作闸、来源授权登记。

### 语音交互
- 实时语音闭环：ASR（DashScope Realtime STT）→ LLM → TTS。
- 连续对话：VAD 静默检测自动断句，AI 回复完毕自动恢复监听。
- 回声抑制 / Barge-in 打断：播放期停采，用户说话自动中断 TTS。
- 独立 TTS 合成页：音色/参数调节；麦克风不可用时键盘降级。

### 工作流编排
- Vue Flow 可视化 DAG 编辑：拖拽连线/小地图/右键菜单。
- 43 种节点：基础、逻辑、AI、数据转换、结构校验、文档解析、持久化等待、状态轮询、人工审批，及媒体生产线。
- 媒体生产线：分镜、合成、调色、字幕、声音克隆、配乐匹配、抽帧、视频理解、质检。
- AGENT 节点：开放任务委派给自主智能体——平台差异化亮点。
- HITL 问答节点：运行中挂起等待用户回答，SSE 推送 + 答案回填。
- 草稿与发布隔离：编辑/回滚/试运行用草稿；开放 API、Agent、MCP、定时与事件触发用发布快照。
- 执行快照：历史画布按当次运行快照还原；等待中的交互在刷新或断线后恢复。

### 经验与自进化
- 通用/任务两级 Markdown 经验库，相关性检索注入 Agent 上下文。
- 可视化管理：统计面板 + CRUD + 归档/激活，字符预算控制。
- 智能开采：LLM 分析对话轨迹提炼经验，离线轨迹蒸馏写回。
- 反思验收账本：自进化建议对照代码与运行账本可追溯。

### 自动触发与后台协作
- 智能体/工作流定时任务、HOOK 入站触发，带专用密钥、限频与幂等控制。
- 巡检任务可配置主动性档位与时间窗口。
- 后台进程退出可通知并进入后续处理；运行中追加消息在边界接入。
- 作用域授权：记住的危险确认可查看、可撤销，按目标与作用域限制复用。

### 主机与凭据
- 每用户主机档案：别名、地址、环境、认证方式。
- 凭据保险库：加密存储，命令以 `{{cred:...}}` 在执行边界解析；须配置独立主密钥。
- 数据边界：主机/凭据不默认跨用户共享；文件下载与截图检查所有者与真实路径。

### MCP 与 A2A
- 静态 MCP 服务器启动时连接；运行时动态安装/卸载无需重启。
- 60s 存活探测 + 自动重连；前端工具卡片支持启用/禁用。
- 本应用自身可作为 MCP Server 对外暴露能力。
- A2A 协议：AgentCard 注册与发现、跨智能体任务委派（`callA2aAgent` 工具）。

### RAG 检索增强
- PDF/Word/Excel/TXT 上传，增量向量化入库。
- 父子切块、表格结构化、扫描件 OCR；同步路径只检测拒绝，重活异步。
- 混合检索：pgvector 语义召回 + Lucene BM25 + RRF 融合 + 重排，支持用户/来源范围过滤。
- 可点击引用跳转原文片段；section_path 面包屑。
- 忠实度/相关性评测面板；A/B 评测跑批。
- NotebookLM 式一键播客（双主持人对话音频）与学习资料生成。
- 可选网页剪藏扩展（Manifest V3）：选区、正文或链接摘要入库。

### 多模态与深度研究
- 文生图（通义万相）多宽高比；多模态对话图片理解。
- AI 文档生成生产线。
- 深度研究：自动拆解问题、多源搜索、综合总结、流式输出。

### 飞书集成
- WebSocket 长连接实时收消息。
- 分层调用：简单对话/工具调用/复杂任务自动路由。
- 富媒体收发（图片/语音）；定时触发 + 完成推送。

### 工程基建
- 全链路 Span（LLM_CALL / TOOL_CALL / VOICE_STT / VOICE_TTS）、Token 记账归集、Trace 回放。
- 监控仪表盘：调用量/Token/模型分布/成本。
- JWT 无状态鉴权 + 登录滑动验证；多用户管理。
- Flyway Schema 迁移；PR 与 main 推送触发 CI 回归门禁（后端回归子集、一次性 MariaDB/Redis/PgVector 容器启动测试、前端测试与构建、Node 桥测试、Windows Go 客户端测试）。
- 首页策展：真实任务端到端回放 + 实时入口。

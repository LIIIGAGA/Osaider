# OSAIDER — Claude Code 项目记忆配置

> 放置位置:Osaider UE 项目根目录下的 CLAUDE.md
> (和已有的 `.claude` 文件夹同级,项目根目录下,不要放进 `.claude` 文件夹里面)

---

## 项目速览

OSAIDER 是一个 UE 5.6 叙事探索类游戏,独立开发,MArch CVA 毕业设计实践项目。
主题:constructed history、bureaucracy、memory、ideology、player agency。
核心玩法循环:Explore → Interact → Collect Items → Submit Items → Dice Check → Generate Next Module。
核心交互:鼠标点击,**没有可控角色**(`BP_TopDownCharacter` 已弃用,使用
`BP_NoCharacterGameMode` / `BP_NoCharacterPlayerController`)。

当前阶段:Phase 1 Vertical Slice。

项目根目录(本文件所在位置,UnrealMCP `.mcp.json` 也在这里)

⚠️ 必须从这个目录启动 Claude Code,否则 UnrealMCP 不会加载。

---

## 会话开始时必读(Session Start Protocol)

每次新会话开始,在做任何修改前,先按顺序读取:

1. **最新的每日总结**(按文件名日期前缀排序取最新一份):
   `E:\Claude Code\DailySummary&ToDoList`
   文件命名规则:`YYYYMMDD_Osaider_Summary&ToDoList.md`
   → 用来知道"上次会话结束时进度在哪、有什么未完成的 ToDo"。

2. **项目背景速览**:
   `..\Osaider-Knowledge-Base\docs\OSAIDER_CONTEXT.md`

3. **AI 专用规则文件(重要,必读)**:
   `..\Osaider-Knowledge-Base\docs\AI_INSTRUCTIONS.md`
   这份文件里已经定义了蓝图架构硬规则、模块/道具提案模板、调试顺序、叙事写作规则、
   回应风格要求。**不要在本文件里重复这些内容,直接去读那份文件,以它为准。**

以上两份文档体量不大(共约17KB),适合每次会话都读。其余文档按任务类型按需读取,
见下表,不要一次性把整个知识库读进上下文。

---

## 知识库文档索引(按需读取)

知识库根目录:`..\Osaider-Knowledge-Base\docs`

| 文件 | 内容 | 什么时候读 |
|---|---|---|
| `OSAIDER_CONTEXT.md` | 项目背景速览 | 每次会话(已在上面) |
| `AI_INSTRUCTIONS.md` | AI 专用规则、模板、调试顺序 | 每次会话(已在上面) |
| `WORLD_BUILDING.md` | 世界观设定 | 写叙事内容、设计新模块时 |
| `GAMEPLAY_SYSTEMS.md` | 2D6 骰子检定、Ideology 数值等玩法机制 | 改动玩法逻辑时 |
| `BLUEPRINT_ARCHITECTURE.md` | 蓝图架构总览 | 改蓝图、查架构关系时 |
| `MODULE_DATABASE.md` | Archivist/Whalemen 各模块具体内容 | 处理具体模块时 |
| `ITEM_DATABASE.md` | 道具数据库 | 新增/修改道具时 |
| `DEVELOPMENT_ROADMAP.md` | Phase 1 目标、进度路线图 | 问"现在做到哪了"类问题时 |

> 新增/重命名文档(比如之前的 `BPI_DragItem` → `BPI_ModuleInteractable`)时,
> 记得同步更新这个索引表,否则 Claude 会读到过期信息。

---

## 已锁定的架构决策(不要重新讨论,除非用户主动提出修改)

- 核心玩法 = 鼠标点击交互,无可控角色。`BP_TopDownCharacter` 已完全弃用。
- **不要重新引入 `BP_ModuleBase`**(共享父蓝图)——之前用过导致不稳定,
  现在的架构是 Blueprint Interfaces + Actor Components + Actor Tags + 模块内本地逻辑。
- 模块揭示(Module Reveal)不是 runtime spawn——所有未来模块都已预先放置在关卡中,
  `BeginPlay` 时 Hidden + 禁用碰撞,选中后才 Unhide + 启用碰撞。不要建议改成运行时生成。
- Phase 2–5 在 Blender 中建模,完成后**一次性批量导入** UE,不按 phase 逐个导入。
- UnrealMCP(flopperam 本地版,`127.0.0.1:55557`)是当前的开发管线,需要 UE Editor 处于打开状态。
- 阵营色彩:Archivist = 红色,Whalemen = 蓝色。

(其余蓝图级细节规则,如 `HoverTrace` 优先、`BP_NoCharacterPlayerController` 该管什么不该管什么
等,都在 `AI_INSTRUCTIONS.md` 里,不在此重复。)

## 人工审核规则(Human-in-the-loop)

- 删除任意 Blueprint 前,必须先列出将被删除的内容,等待用户明确确认。
- 任何 git commit 前,先展示本次改动摘要,等待用户授权后才执行
  (DailySummary-ToDoList 仓库的"总结今日工作"流程例外,该流程已预先授权)。

---

## 自然语言查询 → 文件位置映射

当用户问"昨天/前天/某月某日的 ToDoList 还有什么没做"或类似问题时：

- **去这里找**：`E:\Claude Code\DailySummary&ToDoList`
- **文件命名规则**：`YYYYMMDD_Osaider_Summary&ToDoList.md`
- **操作方式**：按日期前缀定位对应文件，读取"下次会话的待办"章节，对照判断完成情况。
- **不要**：去项目目录或知识库仓库里搜索名字相近的文件（如 `_cleanup_report.md`）。

---

## 记忆分工

- **本文件(CLAUDE.md)**:每次会话都加载的硬规则——路径地图、已锁定架构决策、
  人工审核规则。保持简短,只放"绝对不能错"的内容。
- **`AI_INSTRUCTIONS.md`**:Blueprint 细则、提案模板、调试顺序、叙事规则——内容多但
  结构清晰,按需读取。
- **Claude Code 自带 auto memory**:让它自己积累调试心得、代码风格之类的零碎偏好,
  不用手动管理。
- **GitHub 知识库其余文档 / 每日总结仓库**:需要版本历史、内容长、或要展示给别人看
  (比如面试展示项目文档能力)的内容。

---

## 可选的后续优化(暂不用做,供参考)

`AI_INSTRUCTIONS.md` 里的"模块提案模板""道具提案模板""调试顺序"这几块,内容详细、
触发场景明确(比如"用户要新增模块时才需要"),其实非常适合改造成 Claude Code 的
**project skill**(放在 `0624Osaider\.claude\skills\` 下),让 Claude 只在真正需要时
才加载这部分,比现在"整份 AI_INSTRUCTIONS.md 一起读"更省 token。这个不急,等你觉得
AI_INSTRUCTIONS.md 单次读取开始觉得"重"了再考虑,需要的话我可以帮你拆分。

---

## 跨仓库自动加载(可选)

```bash
claude --add-dir "..\Osaider-Knowledge-Base" --add-dir "E:\Claude Code\DailySummary&ToDoList"
```

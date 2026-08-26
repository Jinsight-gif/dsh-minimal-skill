# 极简Skill模式 (minimal-skill)

DeepSeek Harness (DSH) 的自定义 Agent 预设：在官方「极简模式」(minimal) 的基础上，增加 **Skill 目录发现与加载**能力，其余骨架（固定 persona、持久 bash、str_replace_editor、本地 filesystem realm）原样保留。

> **派生说明**：本预设由官方 [deepseek-ai/deepseek-harness](https://github.com/deepseek-ai/deepseek-harness) 随附的 `minimal` 预设整目录派生（MIT），追加了来自官方 `standard` 预设的三行组件（2 行 skill + 1 行 goal）；未改写官方任何原始行。

## 与「极简模式」的差异

添加了以下三行（均取自官方 `standard` / `cordis` 预设，只注册模型工具或注册进宿主注册表的本预设层，无服务发布、无需 realm）：

```yaml
- id: skill-filesystem   # 本地 skill 目录发现
  name: '@deepseek-ai/dsh-skill-filesystem'
- id: tool-skill         # skill 目录 + 加载工具（skill 工具）
  name: '@deepseek-ai/dsh-tool-skill'
- id: tool-goal          # goal 工具（get_goal / create_goal / update_goal）
  name: '@deepseek-ai/dsh-tool-goal'
```

- 工具清单保持极简：持久 `bash`、`str_replace_editor`、`skill`、goal 工具（`get_goal` / `create_goal` / `update_goal`）。
- 保留 `minimal` 的固定 persona（`complete: true`、`includeRuntimeContext: false`）。
- 可加载任意已知 skill（如 `anysearch`、`r3` 等）。
- **为什么需要 goal 工具**：`/goal` 模式的 round 驱动器在目标处于 `active + armed` 时每轮结束都会自动注入下一条 `<goal_round>`，唯一的程序化停止方式就是 agent 调用 `update_goal(complete/pause)`（或人工暂停）。若预设不挂 `tool-goal`，agent 完成后无法自行收尾，会空转到 `maxGoalRounds`（默认 256）或等你手动暂停。

## 安装

```bash
# 1. 克隆或下载本仓库
git clone <你的仓库地址> ~/dsh-minimal-skill

# 2. 把预设目录放入用户预设根目录
mkdir -p ~/.dsh/.agent-presets
cp -r ~/dsh-minimal-skill/minimal-skill ~/.dsh/.agent-presets/

# 3. 在 DSH Web / CLI 新建会话，预设选择器中选择「极简Skill模式」
```

预设完全自包含，无私有路径依赖；复制即用。卸载只需删除 `~/.dsh/.agent-presets/minimal-skill/`。

## 目录结构

```
minimal-skill-preset/
├── LICENSE                  # MIT（派生自官方 deepseek-harness，保留归因）
├── README.md
└── minimal-skill/           # id 即目录名，kebab-case 合法预设 id
    ├── agent.cordis.yml     # 组装文件：插件行顶层列表（官方注释保留）
    └── preset.yml           # 展示元数据：name / description
```

## 校验状态

- ✅ 已通过 `agentPresets.standingKeyFor('minimal-skill')` 挂载校验（mounted OK，无未激活行、无 realm 冲突）
- ✅ 引用的 `@deepseek-ai/dsh-*` 包均为官方随附预设出现的行，包必然存在于安装中
- ✅ 符合社区投稿约定（如 [awesome-dsh-presets](https://github.com/hackerFish/awesome-dsh-presets) 的 CONTRIBUTING：派生保留归因、行结构可验证）

## License

MIT — 与官方 deepseek-harness 相同。派生内容归因于官方 `minimal` / `standard` 预设（© deepseek-ai）。
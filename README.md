# DST Mod AI Skill

面向 AI Agent 的《Don't Starve Together》模组开发规范。它帮助 Agent 在修改、审查或排查 DST Lua 模组时，正确处理联机权威、RPC 与网络变量、Prefab 生命周期、组件/Replica、存档、洞穴分片、资源动画、UI、FX 和发布验证。

## 内容

仓库内的可安装 Skill 位于 [`dst-mod-development/`](dst-mod-development/)。

| 文件 | 用途 |
| --- | --- |
| `SKILL.md` | Agent 触发条件、强制工作流和参考文档路由 |
| `references/networking.md` | 权威、RPC、网络变量、Replica、预测与联机测试 |
| `references/architecture.md` | Prefab、组件、Hook、Action、状态图与清理 |
| `references/persistence-compatibility.md` | 存档、读档迁移、回档、洞穴与兼容性 |
| `references/assets-animation-ui.md` | 资源、动画、UI、图集与专服表现 |
| `references/particles-fx.md` | 粒子 VFX、Emitter、Envelope 和性能约束 |
| `references/modpackaging.md` | `modinfo.lua`、配置、本地化、依赖与发布打包 |
| `references/gameplay-systems.md` | 角色、物品、装备、配方、容器、Buff、战斗与掉落 |
| `references/engine-environment.md` | `GLOBAL`、脚本环境、引擎对象、世界查询和物理 |
| `references/quality-testing.md` | 检索、Lua 质量与联机验证矩阵 |

## 安装

### Codex

克隆仓库后，将 `dst-mod-development` 文件夹复制到 Codex Skills 目录：

```powershell
git clone https://github.com/KuiYiyinRua/dst-mod-ai-skill.git
Copy-Item .\dst-mod-ai-skill\dst-mod-development "$HOME\.codex\skills\dst-mod-development" -Recurse
```

重启或新建 Codex 任务后，Skill 会根据 DST 模组开发请求自动触发；也可以显式调用：

```text
使用 $dst-mod-development 为这个 DST 模组新增一个可联机同步的 Buff。
```

### 其他 Agent 工具

将整个 `dst-mod-development` 文件夹放入对应工具的 Skills 目录，或在该工具的项目规则中引用其 `SKILL.md`。保持目录结构不变，尤其不要移动 `references/`，因为主体规范会按任务类型读取其中的文件。

## 使用范围

适用于 DST Lua 5.1 模组的开发、重构、代码审查和故障排查，特别适合以下任务：

- 客户端意图、服务端权威、RPC、网络变量和 Replica
- 角色、Prefab、组件、Action、状态图、装备、Buff 和容器
- `modinfo.lua`、配置项、本地化、资源、动画、粒子 FX、UI
- 存档/读档、回档、洞穴分片、专用服务器和远端客户端兼容性

它不替代具体项目的规则。项目内的 `AGENTS.md`、已有架构文档和已验证的游戏脚本调用方式优先。

## 官方源码定位

Skill 会优先读取项目说明中指定的 DST 官方脚本副本；未指定时依次探测项目根目录下的 `template/scripts`、模组父目录下的 `template/scripts`，以及 DST 安装目录中的 `data/databundles/scripts.zip` 或已解压脚本目录。找到后，Agent 会读取相关定义和调用点来确认 API、生命周期及客户端/服务端边界；找不到时会明确说明，并退回到项目已有调用点。

## 维护原则

Skill 保持“核心流程 + 按需参考”的结构：把项目私有规则放在项目中，把可迁移的 DST 经验放进这里；对不确定的 API，应优先在本地游戏脚本或现有调用点中验证，而不是凭记忆编写。

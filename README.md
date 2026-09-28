# Draw.io Scientific Illustrator

[中文说明](#中文说明) · [English guide](#english-guide) · [MIT License](LICENSE)

A Codex plugin that lets an AI agent draw scientific figures **live inside the visible draw.io desktop canvas**. You can watch shapes, labels, arrows, styling, and layout appear step by step. The live workflow calls draw.io's own graph API through a localhost-only MCP server; it does not automate the operating-system mouse or keyboard and does not create XML first and merely open it afterward.

> Status: Windows is tested. macOS and Linux executable discovery is included, but live behavior can vary with draw.io/Electron packaging. Reports and pull requests are welcome.

## English guide

### What this repository contains

- `drawio-live` MCP server: launches/connects to the visible draw.io desktop editor and edits its active graph model in real time.
- `drawio-file-utils` MCP server: validates saved `.drawio` documents and exports PNG, SVG, PDF, or JPG deliverables.
- A Codex skill: teaches the agent to inspect a reference, decompose it into editable primitives, draw with pacing, visually review sections, refine the live graph, and save only after the visible drawing exists.
- A repository-local Codex marketplace so the complete plugin can be installed as one unit.

This is therefore both an **MCP implementation** and a **Codex plugin**. MCP provides the callable tools; the plugin is the installable package containing the MCP servers, skill, and presentation metadata.

### Requirements

1. Codex desktop app or Codex CLI with plugin support.
2. [draw.io desktop](https://www.drawio.com/) installed locally.
3. Git.
4. Node.js available as `node` when running the MCP servers outside the bundled Codex runtime. Node.js 22 or newer is recommended.

The plugin auto-detects common draw.io locations on Windows, macOS, and Linux. For a custom installation, set `DRAWIO_PATH` to the executable before starting Codex.

### Install — easiest methods

#### Ask Codex to install it

Paste this into a Codex task that has terminal access:

```text
Install the public Codex plugin from https://github.com/icebird1998/drawio-scientific-illustrator.
Clone it locally, register its repository root as a Codex marketplace, install
drawio-scientific-illustrator@drawio-scientific-tools, then tell me when to restart Codex.
```

#### Windows one-command installer

Review [`install.ps1`](install.ps1), then run:

```powershell
$p="$env:TEMP\drawio-scientific-install.ps1"; Invoke-WebRequest https://raw.githubusercontent.com/icebird1998/drawio-scientific-illustrator/main/install.ps1 -OutFile $p; powershell -ExecutionPolicy Bypass -File $p
```

#### macOS/Linux one-command installer

Review [`install.sh`](install.sh), then run:

```bash
curl -fsSL https://raw.githubusercontent.com/icebird1998/drawio-scientific-illustrator/main/install.sh | bash
```

Restart Codex and start a new task after installation so the new skill and MCP tools are loaded.

### Install — manual and auditable

```bash
git clone https://github.com/icebird1998/drawio-scientific-illustrator.git
cd drawio-scientific-illustrator
codex plugin marketplace add "$(pwd)"
codex plugin add drawio-scientific-illustrator@drawio-scientific-tools
```

On PowerShell, replace `"$(pwd)"` with `(Get-Location).Path`:

```powershell
git clone https://github.com/icebird1998/drawio-scientific-illustrator.git
Set-Location drawio-scientific-illustrator
codex plugin marketplace add (Get-Location).Path
codex plugin add drawio-scientific-illustrator@drawio-scientific-tools
```

### How to use it

1. Restart Codex after installation and create a new task.
2. Attach a PNG, JPEG, SVG, or a rendered PDF page as the reference, or provide a scientific brief for a biological mechanism diagram.
3. Mention **Draw.io Scientific Illustrator** or select the plugin in the composer.
4. State the desired pacing and output formats.

> **Recommended Codex configuration for complex scientific redraws:** choose **GPT-5.6 Sol** and set reasoning effort to **Max**. In Codex settings, enable the six-level reasoning selector first; the default five-level selector does not show the Max option. This setting can increase response time and token use.

Recommended prompt:

```text
Use Draw.io Scientific Illustrator. Launch the live draw.io canvas and recreate this
reference scientific figure step by step with a 100 ms delay. Control only draw.io's
own graph API; do not use OS mouse/keyboard automation and do not generate XML first.
Keep all labels, arrows, panels, and legends editable. Visually inspect and refine each
section, then save the final .drawio file and export a 2000 px PNG preview.
```

Chinese prompts work equally well:

```text
使用 Draw.io Scientific Illustrator。启动实时 draw.io，以 100 ms 的步骤间隔重绘
这张参考图。必须直接控制 draw.io 画布，不要使用系统鼠标键盘控制，也不要先生成
XML。文字、箭头、分区和图例都要可编辑；完成后保存 .drawio 并导出 2000 px PNG。
```

### Preset: Nature-style biological mechanism

For biological mechanism briefs, the [skill](plugins/drawio-scientific-illustrator/skills/recreate-scientific-figure-in-drawio/SKILL.md#preset-nature-style-biological-mechanism) supplies a small preset using the existing live tools. "Nature-style" means restrained scientific illustration, not an official journal template. It is selected for mechanism briefs without another specified style, or when explicitly requested. Exact reference reconstruction keeps the reference's style and labels unless restyling is requested; other figure types keep the general workflow.

| Recurring requirement | Preset behavior |
| --- | --- |
| Consistent numbering and story | Main modules use **1 / 2 / 3**, never A/B/C; follow **biological objects → state changes → information flow** without inventing biological steps. |
| Readable to non-specialists | Recognizable biological objects, visible state changes, short titles/labels, concrete verbs, and definitions for necessary abbreviations. |
| Consistent appearance | White background, muted semantic colors, three text levels, 1.5 px object outlines / 2 px connectors, modest rounded containers, and explicit margins/padding. |
| Scientifically meaningful arrows | Distinguish directed flow, inhibition, association and labeled hypotheses; preserve supplied evidence and measured/predicted distinctions. |
| Editable results | Build objects, labels, state cues, arrows and legends as separate graph cells; review screenshots, refine live, and save `.drawio` plus exports. No new MCP endpoint or `preset` parameter is required. |

Example prompt:

```text
Use $recreate-scientific-figure-in-drawio with the Nature-style biological mechanism
preset. From the mechanism brief below, show biological objects → state changes →
information flow with 1/2/3 module numbers and concise labels for non-specialists.
Keep all elements editable, inspect each section, and export .drawio plus a 2000 px PNG.
Mechanism brief: [objects, compartments, before/after states, supported relationships,
and any uncertain steps or measured/predicted time points].
```

A complete brief is enough for a generic mechanism sketch. **Provide a reference** to reproduce a specific paper, an earlier figure's exact layout/palette/icons, detailed anatomy or subcellular spatial arrangements, or specialist pathway notation. Provide source images/data for microscopy, histology, 3D molecular renderings, heatmaps or quantitative plots; the preset cannot turn these into fully editable primitives and the live image-insertion limitation still applies. It does not supply missing scientific facts or guarantee visual fidelity without review.

This preset is maintained in the **Shuai1Wen fork**. To install this fork, follow the manual installation steps with `https://github.com/Shuai1Wen/drawio-scientific-illustrator.git` as the clone URL. The bundled one-command installers above currently target the upstream repository.

### Live tool workflow

The agent normally uses the tools in this order:

1. `drawio_live_launch` — launch or connect to a visible draw.io editor.
2. `drawio_live_status` — confirm that the graph is ready.
3. `drawio_live_add_shape`, `drawio_live_add_edge`, or paced `drawio_live_draw_sequence` — construct editable content visibly.
4. `drawio_live_screenshot` — inspect the draw.io renderer after logical sections.
5. `drawio_live_inspect` and `drawio_live_update_cell` — correct labels, styles, positions, and sizes.
6. `drawio_live_fit` — keep the evolving figure in view.
7. `drawio_live_save_snapshot` — serialize the already-visible graph to `.drawio`.
8. `drawio_validate` and `drawio_export` — validate and export deliverables.

### Configuration

Optional environment variables:

| Variable | Purpose | Default |
|---|---|---|
| `DRAWIO_PATH` | Explicit draw.io executable path | Auto-detected |
| `DRAWIO_LIVE_PORT` | Preferred localhost debugging port | `9333`; a nearby free port is selected when needed |
| `DRAWIO_LIVE_PROFILE` | Dedicated draw.io/Electron profile directory | `~/.drawio-live-mcp/<port>` |

Example on Windows:

```powershell
$env:DRAWIO_PATH = "D:\Apps\draw.io\draw.io.exe"
```

### Update

Run the same installer again. It performs a fast-forward `git pull` and reinstalls the plugin. Restart Codex and start a new task afterward.

### Troubleshooting

- **`node` not found**: install Node.js 22+ or make sure the Codex runtime exposes Node to plugin MCP servers.
- **draw.io not found**: install the desktop app or set `DRAWIO_PATH`.
- **Port already in use**: omit an explicit port and the server will select a nearby localhost port, or set `DRAWIO_LIVE_PORT` to a free port.
- **Graph not ready**: close stale draw.io windows launched by the plugin, retry, and ensure the desktop app accepts Electron remote-debugging flags.
- **Plugin not visible**: restart Codex and create a new task after installation.
- **Export fails**: confirm that draw.io desktop can export the same file manually and that the output directory is writable.

### Security and privacy

- The live debugging endpoint binds to `127.0.0.1`, not a public network interface.
- Target discovery accepts only pages identified as draw.io/diagrams.net; it does not attach to arbitrary browser pages.
- The plugin does not use OS-level mouse or keyboard automation.
- No telemetry or hosted backend is included. See [PRIVACY.md](PRIVACY.md).
- Only install software from repositories and revisions you trust. Review the installer scripts before executing them.

### Known limitations

- The live API currently emphasizes editable draw.io primitives. Dense microscopy images, photographs, heatmaps, and complex plots may need a future dedicated live image-insertion operation.
- Visual fidelity depends on reference resolution and how well the content can be represented by draw.io primitives.
- Windows is the tested platform for v1.0.0; macOS/Linux support is best effort until community testing expands.

---

## 中文说明

### 这是什么

Draw.io Scientific Illustrator 是一个面向科研插图的 Codex 插件。它会启动桌面版 draw.io，并通过仅限本机的 MCP 通道直接调用 draw.io 自身的图模型 API。你可以亲眼看到形状、文字、箭头、配色和布局按步骤出现在画布上。

它不会模拟系统鼠标或键盘，也不会先生成一个 XML 文件再让 draw.io 打开。只有当可见画布中的图已经绘制完成后，才会把当前图模型保存为 `.drawio` 文件。

这个仓库同时包含：

- 实时控制 draw.io 画布的 `drawio-live` MCP；
- 校验和导出的 `drawio-file-utils` MCP；
- 指导 Codex 重绘、检查和迭代科研插图的 Skill；
- 可供 Codex 安装的自定义插件市场配置。

所以它既“属于 MCP”，也属于更上层的“Codex 插件”：MCP 是实际工具接口，插件是把 MCP、Skill 和界面元数据打包分享的安装单位。

### 安装要求

1. 支持插件的 Codex 桌面应用或 Codex CLI；
2. 本机已安装 [draw.io 桌面版](https://www.drawio.com/)；
3. Git；
4. 如果脱离 Codex 自带运行环境单独启动 MCP，需要可用的 Node.js，推荐 Node.js 22 或更高版本。

插件会自动检测 Windows、macOS 和 Linux 的常见 draw.io 安装位置。若安装在自定义目录，请在启动 Codex 前设置 `DRAWIO_PATH`。

### 最省事的安装方式

在一个具备终端权限的 Codex 任务里直接粘贴：

```text
请安装这个公开 Codex 插件：https://github.com/icebird1998/drawio-scientific-illustrator。
把仓库克隆到本地，将仓库根目录注册为 Codex Marketplace，然后安装
drawio-scientific-illustrator@drawio-scientific-tools。完成后告诉我何时重启 Codex。
```

Windows 用户也可以先检查 [`install.ps1`](install.ps1)，然后运行：

```powershell
$p="$env:TEMP\drawio-scientific-install.ps1"; Invoke-WebRequest https://raw.githubusercontent.com/icebird1998/drawio-scientific-illustrator/main/install.ps1 -OutFile $p; powershell -ExecutionPolicy Bypass -File $p
```

macOS/Linux 用户先检查 [`install.sh`](install.sh)，然后运行：

```bash
curl -fsSL https://raw.githubusercontent.com/icebird1998/drawio-scientific-illustrator/main/install.sh | bash
```

安装完成后必须重启 Codex，并新建一个任务，使新的 Skill 和 MCP 工具被载入。

### 手动安装

PowerShell：

```powershell
git clone https://github.com/icebird1998/drawio-scientific-illustrator.git
Set-Location drawio-scientific-illustrator
codex plugin marketplace add (Get-Location).Path
codex plugin add drawio-scientific-illustrator@drawio-scientific-tools
```

macOS/Linux：

```bash
git clone https://github.com/icebird1998/drawio-scientific-illustrator.git
cd drawio-scientific-illustrator
codex plugin marketplace add "$(pwd)"
codex plugin add drawio-scientific-illustrator@drawio-scientific-tools
```

### 使用方法

1. 重启 Codex 并新建任务；
2. 上传 PNG、JPEG、SVG 或从 PDF 渲染出的参考页；生物机制图也可提供完整的机制文字说明；
3. 在输入框选择或提到 **Draw.io Scientific Illustrator**；
4. 说明绘制步进间隔、画面尺寸和希望导出的格式。

> **复杂科研插图重绘建议的 Codex 设置：**选择 **GPT-5.6 Sol**，并将推理等级设为 **“最高（Max）”**。需要先在 Codex 设置中开启 **6 档推理等级**；默认的 **5 档**选择器不会显示“最高”选项。该设置可能增加响应时间和 token 用量。

推荐提示词：

```text
使用 Draw.io Scientific Illustrator。启动实时 draw.io，以 100 ms 的步骤间隔逐步重绘
这张参考图。必须直接控制 draw.io 自己的画布 API，不要控制系统鼠标键盘，也不要先
生成 XML。所有文字、箭头、分区、图例都要保持可编辑。每完成一个逻辑区域就检查并
修正，最后保存 .drawio，并导出宽度为 2000 px 的 PNG 预览图。
```

### 预设：Nature-style biological mechanism

[Skill](plugins/drawio-scientific-illustrator/skills/recreate-scientific-figure-in-drawio/SKILL.md#preset-nature-style-biological-mechanism) 新增一个基于现有实时工具的最小生物机制图预设。“Nature-style”表示简洁、克制的科研插图风格，并非期刊官方模板。根据文字说明绘制机制图且未指定其他风格时默认启用，也可点名调用。精确复刻参考图时保留原图的样式和编号，除非明确要求应用预设或重新设计；其他科研图继续使用通用流程。

| 反复提出的需求 | 预设中的规则 |
| --- | --- |
| 编号与逻辑一致 | 主模块固定采用 **1 / 2 / 3**，不用 A/B/C；围绕**生物对象 → 状态变化 → 信息流**组织，不凭空增加生物步骤。 |
| 非专家也能快速读懂 | 用可识别的生物对象和可见的前后状态表达机制，缩短标题和标签，使用具体动词，解释必要缩写。 |
| 视觉统一 | 白底、低饱和语义配色、三级文字层级、1.5 px 对象轮廓 / 2 px 连线、统一适度圆角，以及明确的留白和内边距。 |
| 箭头含义准确 | 区分定向传递、抑制、关联及明确标注的推测；保留证据强度、实测与预测的区别。 |
| 所有示意元素可编辑 | 对象、状态标记、文字、箭头和图例均用独立图元绘制，截图检查后直接在画布修改，保存 `.drawio` 并导出；无需新增 MCP 接口或 `preset` 参数。 |

可直接使用：

```text
使用 $recreate-scientific-figure-in-drawio，应用 Nature-style biological mechanism
预设。根据下面的机制说明，按“生物对象 → 状态变化 → 信息流”组织画面，主模块用
1/2/3 编号，文字精简，让非专家也能看懂。所有元素保持可编辑，每完成一个区域就
截图复核，最后保存 .drawio 并导出 2000 px PNG。
机制说明：[对象、所在区室、变化前后状态、有依据的关系，以及不确定步骤或实测/预测时间点]。
```

一般机制示意图有完整文字说明即可。**以下情形仍需提供参考图**：精确复刻某篇论文或某张旧图的布局、配色、图标轮廓；还原详细解剖或亚细胞空间关系；使用特殊通路符号。显微/组织图像、三维分子渲染、热图和定量数据图还需要相应原图或数据；预设不能将这些素材自动变为完全可编辑图元，现有实时图片插入限制仍然适用。预设不补造科学事实，也不能代替视觉复核。

此预设位于 **Shuai1Wen 分支仓库**。安装此仓库时，按手动安装步骤操作，将克隆地址换为 `https://github.com/Shuai1Wen/drawio-scientific-illustrator.git`。上文的一键安装脚本目前仍指向上游仓库。

### 工作过程

正常情况下，Codex 会依次完成：

1. 启动可见的 draw.io；
2. 确认内部 graph 已就绪；
3. 逐个或按设定间隔添加可编辑形状和连线；
4. 每完成一个逻辑区域截取 draw.io 渲染区域进行检查；
5. 直接在实时画布中修改文字、样式、位置和大小；
6. 完成后才保存 `.drawio` 快照；
7. 校验文件结构并导出 PNG、SVG、PDF 或 JPG。

### 可选配置

| 环境变量 | 作用 | 默认值 |
|---|---|---|
| `DRAWIO_PATH` | 指定 draw.io 可执行文件 | 自动检测 |
| `DRAWIO_LIVE_PORT` | 首选本机调试端口 | `9333`，冲突时自动寻找附近端口 |
| `DRAWIO_LIVE_PROFILE` | draw.io/Electron 独立用户目录 | `~/.drawio-live-mcp/<端口>` |

Windows 示例：

```powershell
$env:DRAWIO_PATH = "D:\Apps\draw.io\draw.io.exe"
```

### 更新

再次运行安装脚本即可。脚本会执行安全的快进更新并重新安装插件。随后重启 Codex、新建任务。

### 常见问题

- **找不到 Node**：安装 Node.js 22+，或确认 Codex 插件运行环境能够调用 `node`。
- **找不到 draw.io**：安装桌面版，或者设置 `DRAWIO_PATH`。
- **端口被占用**：不要强制指定端口，让插件自动选择；也可以修改 `DRAWIO_LIVE_PORT`。
- **图模型没有就绪**：关闭插件此前启动但已失效的 draw.io 窗口后重试。
- **安装后看不到插件**：重启 Codex，并新建任务。
- **导出失败**：确认 draw.io 桌面版可以手工导出该文件，并检查输出目录写权限。

### 安全与隐私

- 实时调试端口仅绑定 `127.0.0.1`；
- 只接受可识别为 draw.io/diagrams.net 的页面，不会连接任意浏览器页面；
- 不使用系统级鼠标键盘自动化；
- 不包含遥测或云端服务，详见 [PRIVACY.md](PRIVACY.md)；
- 执行远程安装脚本前，请先检查脚本内容和版本。

### 当前限制

- 实时工具目前主要面向可编辑的 draw.io 图元。显微照片、热图和复杂数据图可能需要后续增加专门的实时图片插入工具；
- 最终还原度取决于参考图分辨率，以及内容是否适合用 draw.io 图元表达；
- v1.0.0 已在 Windows 上测试，macOS/Linux 暂为尽力支持，欢迎提交测试反馈。

## Contributing / 参与贡献

Issues and pull requests are welcome. Please include operating system, draw.io version, Codex version, reproduction steps, and relevant MCP error text. Do not upload confidential reference images.

欢迎提交 Issue 和 Pull Request。请注明操作系统、draw.io 版本、Codex 版本、复现步骤及相关 MCP 错误；不要上传保密的参考图片。

## License

MIT © 2026 [icebird1998](https://github.com/icebird1998)

# WeChat Intelligence Hub 使用指南

这份指南面向已经克隆仓库、希望在 Codex 中直接使用微信个人情报库的用户。日常使用优先说自然语言；命令行主要用于安装、诊断和手动渲染。

## 1. 安装与检查

```bash
git clone https://github.com/Rion-Wu-tech/wechat-intelligence-hub.git
cd wechat-intelligence-hub
./scripts/install.sh --with-sqlcipher
```

重新打开 Codex 后，先检查 Reader：

```text
$wechat-cli 运行 self-test，然后开始 setup；告诉我当前是完整数据库模式、通知预览模式，还是仍缺少访问材料。
```

三种常见状态：

- **完整数据库模式**：可以在本地授权和实际兼容范围内查询历史聊天与近期新增消息。
- **通知预览模式**：只能读取 macOS 实际保留的入站通知预览，不代表完整聊天记录。
- **缺少访问材料**：先运行全虚构 Demo，或按 `setup` 给出的检查结果补齐自己有权使用的本地输入。

项目不会提取微信数据库密钥，不重签名、不注入、不 Hook 微信。

## 2. 建立个人 Profile

让系统了解你的身份、行业、当前项目和重点关系，日报排序会更贴合你正在做的事情：

```text
$wechat-intelligence-hub 帮我初始化微信个人情报库。先查找我现有的个人说明和当前计划；如果没有就给我准备清单，再只读检查现有微信标签并建议如何分类。
```

也可以使用命令行：

```bash
cd projects/wechat-intelligence-hub
python3 wechat_intelligence_hub.py profile-init \
  --owner-alias "你的微信昵称" \
  --personal-doc "/path/to/个人说明.md" \
  --plan-doc "/path/to/本月计划.md" \
  --priority-label "你的重点联系人标签"
```

个人 Profile、真实联系人、聊天数据库和生成报告只保存在本机，不应提交到 GitHub。

## 3. 最常用的自然语言请求

### 生成完整日报

```text
$wechat-intelligence-hub 生成过去 24 小时的完整微信情报日报，分析群聊、重点联系人、待回复事项、待兑现承诺和商业机会，同时输出 Markdown 和旗舰交互式 HTML。
```

把“24 小时”改为“48 小时”、具体日期或一周，即可调整时间范围。

### 查看今天应该先做什么

```text
$wechat-intelligence-hub 查看今天最需要我处理的 10 件事，说明原因和原始消息依据。
```

### 查看联系人进展

```text
$wechat-intelligence-hub 总结我和「联系人名字」最近聊到哪里，谁在等谁，还有哪些承诺没有完成。
```

如果涉及刚刚收到的消息，可以明确要求先刷新：

```text
$wechat-intelligence-hub 先刷新「联系人名字」的最新聊天，再告诉我是否需要回复。
```

### 生成回复草稿

```text
$wechat-intelligence-hub 根据最新上下文，给「联系人名字」生成一条符合我平时语气的简短回复草稿。
```

系统只生成本地草稿，不会发送微信。金额、日期、报价和承诺必须由使用者核对。

### 搜索所有微信聊天

```text
$wechat-intelligence-hub 搜索过去 7 天所有微信聊天里关于「AI 培训」的讨论，按联系人和群聊归纳并附上来源。
```

### 查看商业机会

```text
$wechat-intelligence-hub 找出过去 48 小时微信里的品牌商单、培训、咨询、项目合作和资源对接机会，区分已确认、高概率和待核实。
```

### 查看群聊情报

```text
$wechat-intelligence-hub 总结过去 24 小时所有活跃群聊：先按真实话题跨群归纳，再列值得阅读的重点群和商单信号。
```

### 查找适合复联的人

```text
$wechat-intelligence-hub 查看哪些品牌方、客户或合作伙伴已经到跟进时间，给出复联原因和一条简短建议。
```

## 4. Markdown 与 HTML 两种正式输出

交付形式默认使用 `auto`，按问题规模选择：

| 请求类型 | 默认交付 |
|---|---|
| 单个联系人、单个项目、关键词核实、回复建议 | 直接在 Codex 中回答 |
| 跨多个会话且需要保存 | Markdown |
| 完整 24/48 小时复合日报、周报、月报 | Markdown + HTML |
| 需要搜索、筛选、点击展开的大范围报告 | Markdown + HTML |

完整日报的主要文件：

```text
wechat_daily_full.md
wechat-report/index.md
wechat-report/groups.md
wechat-report/group-topics.md
wechat-report/key-groups.md
wechat-report/contacts.md
wechat-report/radar.md
wechat_daily_report.html
```

其中：

- `wechat_daily_full.md` 是 Markdown 阅读入口。
- `wechat-report/` 保存各功能分区，便于复制、归档和继续编辑。
- `wechat_daily_report.html` 是包含全部正式分区的单文件交互报告。

HTML 版提供：

- 综合行动、群聊日报、重点联系人、商单信号雷达四个顶层入口
- 全分区搜索和路由导航
- 话题日报、重点群聊和群聊筛选切换
- 点击群名展开详情
- 根据活跃度和用户关注主题筛选群聊
- 原始外部链接跳转
- 明暗主题
- 打印或保存为 PDF
- 下载当前分区 Markdown

每位用户看到的界面结构一致，但报告内容会根据本地数据、时间范围、个人 Profile、微信标签和当前计划变化。

## 5. 手动生成双版本

通常让 Codex 完成语义整理和渲染即可。如果已经有一次完整运行的报告目录，也可以手动执行：

```bash
cd projects/wechat-intelligence-hub
python3 wechat_intelligence_hub.py render-bundle \
  /path/to/run-directory
```

指定输出位置或标题：

```bash
python3 wechat_intelligence_hub.py render-bundle \
  /path/to/run-directory \
  --out /path/to/run-directory/wechat_daily_report.html \
  --markdown-out /path/to/run-directory/wechat_daily_full.md \
  --title "我的微信情报日报"
```

`render-bundle` 应在群聊和重点联系人完成语义编辑后运行。`group_daily_digest.md` 和可选的 `group_daily_digest.html` 是机器初筛与审计产物，不能代替最终 Markdown 和旗舰 HTML。

如果系统没有安装 `pandoc`，各模块 Markdown 仍会保留，但 HTML 可能无法生成。按终端提示安装 `pandoc` 后重新运行 `render-bundle`。

## 6. 不读取真实数据的 Demo

先用仓库自带的全虚构样本确认分析和报告链路：

```bash
bash projects/wechat-intelligence-hub/scripts/run_demo.sh
```

指定 Demo 输出目录：

```bash
WECHAT_DEMO_OUT=/path/to/demo \
  bash projects/wechat-intelligence-hub/scripts/run_demo.sh
```

Demo 不会读取真实联系人、微信聊天或本地情报库。

## 7. 常见问题

### 为什么只收到文字回答，没有 HTML？

单个联系人、一个关键词或一条回复建议默认使用轻量交付。请明确要求“生成完整日报，同时输出 Markdown 和 HTML”。

### 为什么报告里没有某条消息？

先查看读取模式、索引截止时间和覆盖范围。“没有读取到”不等于“微信里没有”。微信升级后可要求 Skill 运行 `compat-check`。

### HTML 会把聊天上传到网页吗？

不会。它是写入本机的单文件报告，不需要把聊天上传到公开网站。不要把真实报告提交到 GitHub、Issue 或公开网盘。

### 浏览器里调整的群聊优先级会永久修改配置吗？

不会。HTML 中的选择保存在本地并可导出，只有使用者确认后才应写回个人 Profile。

### 能否自动发送回复？

不能。整个项目坚持微信只读，回复能力只生成草稿。

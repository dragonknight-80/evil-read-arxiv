# 快速开始指南

这是使用 evil-read-arxiv 的三步快速设置指南。

## 第一步：安装依赖

在终端运行：

```bash
pip install -r requirements.txt
```

## 第二步：配置

### 2.1 设置环境变量

设置 `OBSIDIAN_VAULT_PATH` 环境变量，指向你的 Obsidian Vault 路径。所有脚本会自动读取此变量，无需手动修改脚本中的路径。

```bash
# Windows PowerShell（永久生效，设置后需重启终端）
[System.Environment]::SetEnvironmentVariable("OBSIDIAN_VAULT_PATH", "C:/Users/YourName/Documents/Obsidian Vault", "User")

# macOS/Linux（添加到 ~/.bashrc 或 ~/.zshrc）
echo 'export OBSIDIAN_VAULT_PATH="/Users/yourname/Documents/Obsidian Vault"' >> ~/.bashrc
source ~/.bashrc
```

### 2.2 创建配置文件

```bash
cd evil-read-arxiv
cp config.example.yaml config.yaml
```

编辑 `config.yaml`，修改：

```yaml
# 将此路径改为你的 Obsidian Vault 路径
vault_path: "/path/to/your/obsidian/vault"

# 根据你的研究兴趣修改关键词
research_domains:
  "你的研究领域1":
    keywords:
      - "keyword1"
      - "keyword2"
```

### 2.3 将配置文件放入 Vault

```bash
# macOS/Linux
cp config.yaml "$OBSIDIAN_VAULT_PATH/99_System/Config/research_interests.yaml"

# Windows PowerShell
Copy-Item config.yaml "$env:OBSIDIAN_VAULT_PATH\99_System\Config\research_interests.yaml"
```

### 2.4 将技能安装到 Claude Code

将 evil-read-arxiv 目录中的四个技能文件夹复制到你的 Claude Code skills 目录：

```bash
# macOS/Linux
cp -r evil-read-arxiv/start-my-day ~/.claude/skills/
cp -r evil-read-arxiv/paper-analyze ~/.claude/skills/
cp -r evil-read-arxiv/extract-paper-images ~/.claude/skills/
cp -r evil-read-arxiv/paper-search ~/.claude/skills/

# Windows PowerShell
Copy-Item -Recurse evil-read-arxiv\start-my-day $env:USERPROFILE\.claude\skills\
Copy-Item -Recurse evil-read-arxiv\paper-analyze $env:USERPROFILE\.claude\skills\
Copy-Item -Recurse evil-read-arxiv\extract-paper-images $env:USERPROFILE\.claude\skills\
Copy-Item -Recurse evil-read-arxiv\paper-search $env:USERPROFILE\.claude\skills\
```

> 如果你不使用 Claude Code，可跳过本节，直接在终端运行各脚本（见下文“附：不用 Claude Code 的最小流程”）。

## 第三步：创建 Obsidian 目录结构

在你的 Obsidian Vault 中创建以下目录：

```
你的Vault/
├── 10_Daily/
├── 20_Research/
│   └── Papers/
├── 99_System/
│   └── Config/
│       └── research_interests.yaml  # 第二步中已复制
```

## 开始使用

### 1. 打开 Claude Code

在你的 Obsidian Vault 目录中打开终端：

```bash
# 切换到你的 Obsidian Vault 目录
cd "$OBSIDIAN_VAULT_PATH"

# 启动 Claude Code
claude-code
```

### 2. 开始每日论文推荐

在 Claude Code 中输入：

```
start my day
```

### 3. 分析单篇论文

在 Claude Code 中输入：

```
paper-analyze 2602.12345
```

---

## 附：不用 Claude Code 的最小流程（适配任意 LLM 工具）

如果你想替换成其他 LLM（如只用 API、或其他 AI IDE），可直接用命令行调用脚本：

```bash
# 1) 安装依赖
pip install -r requirements.txt

# 2) 准备配置
cp config.example.yaml config.yaml

# 3) 搜索最近论文（等价于 "start my day" 的核心动作）
# 可选：追加 --target-date YYYY-MM-DD 指定基准日期
python start-my-day/scripts/search_arxiv.py --config config.yaml

# 4) 生成单篇论文笔记
python paper-analyze/scripts/generate_note.py --vault "$OBSIDIAN_VAULT_PATH" --paper-id 2401.00001 --title "Paper Title" --authors "Author" --domain "Foundation Models & LLM"
```

要点：这个项目的核心是 Python 脚本，不是模型绑定；替换 LLM 时通常只需要替换“谁来帮你执行命令/组织流程”。

## 常用 arXiv 分类

| 分类代码 | 名称 | 说明 |
|----------|------|------|
| cs.AI | Artificial Intelligence | 人工智能 |
| cs.LG | Learning | 机器学习 |
| cs.CL | Computation and Language | 计算语言学/NLP |
| cs.CV | Computer Vision | 计算机视觉 |
| cs.MM | Multimedia | 多媒体 |
| cs.MA | Multiagent Systems | 多智能体系统 |
| cs.RO | Robotics | 机器人学 |

## 故障排除

### 问题："未指定 vault 路径" 或 "Papers directory not found"

**解决**：
1. 确认环境变量已设置：
   ```bash
   # Windows PowerShell
   echo $env:OBSIDIAN_VAULT_PATH

   # macOS/Linux
   echo $OBSIDIAN_VAULT_PATH
   ```
2. 如果为空，回到第二步设置环境变量
3. 确认目录结构已正确创建

### 问题：论文图片提取失败

**解决**：
1. 确认安装了 PyMuPDF：`pip install PyMuPDF`
2. 检查 arXiv ID 格式是否正确（如 2602.12345）

### 问题：关键词自动链接不准确

**解决**：编辑 `start-my-day/scripts/link_keywords.py` 中的 `COMMON_WORDS` 集合，添加你不需要自动链接的词。

## 需要帮助？

- 查看 [README.md](README.md) 获取详细说明
- 提交 Issue 到 GitHub 仓库

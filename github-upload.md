# Skill: GitHub 一键上传 / One-Click GitHub Upload

**触发命令 / Trigger**：`/github-upload`  
**适用范围**：任何本地项目，从打包检查到推送 GitHub 的完整标准流程  
**工具要求**：Git + curl（Windows / macOS / Linux 均支持）

> **与 `gh`、`auto-publish-github` 等工具的区别**：  
> 本 Skill 不是一个独立的命令行工具，而是一个由 AI 驱动的、包含标准化流程和真实踩坑经验的**交互式指南**。  
> 它引导你逐步完成每个阶段，在关键节点主动预检和提示，而不是静默地执行命令。

---

## 执行前，请提供以下信息

我会依次引导你完成，请直接回答：

| # | 信息 | 示例 |
|---|------|------|
| 1 | 本地项目路径 | `E:\my-project` 或 `/home/user/my-project` |
| 2 | GitHub 用户名 | `your-username` |
| 3 | 新仓库名 | `my-project`（将在 GitHub 上新建，**不影响已有仓库**）|
| 4 | 仓库描述（可选）| 简短英文描述 |
| 5 | 公开或私有 | `public` / `private` |
| 6 | GitHub Token | 需要 `repo` 权限的 PAT → [生成地址](https://github.com/settings/tokens) |
| 7 | 要排除的文件（可选）| 如 `node_modules/`、旧原始记录文件等 |

---

## 阶段一：智能打包前检查（含自动预检）

### 1.1 标准文件检查（AI 主动检测）

以下文件缺失或内容异常时，我会主动提示并协助修复，无需手动逐项确认：

| 文件 | 检查项 | 缺失/异常时 |
|------|--------|-----------|
| `README.md` | 文件存在且非空 | 提示并提供模板 |
| `LICENSE` | 存在 + **`[Author]` 已替换** | ⚠️ 主动告警，修复后方可继续 |
| `.gitignore` | 覆盖 `*.env` / `secrets/` / `node_modules/` / `__pycache__/` / `.venv/` / `target/` / `dist/` / `build/` | 提示追加缺失规则 |

**LICENSE 智能预检命令**（AI 会自动执行并解读结果）：

```bash
# 检测 LICENSE 是否仍含有 [Author] 占位符
grep -n "\[Author\]" [项目路径]/LICENSE
# 若有输出 → 立即停止，提示替换后再继续
# 若无输出 → ✅ 通过
```

### 1.2 敏感文件扫描

```bash
# 检查源文件中是否含有密码/密钥（结果必须为空才能继续）
grep -rn "password\|secret\|api_key\|apikey\|token" \
  --include="*.py" --include="*.js" --include="*.ts" \
  --include="*.go" --include="*.java" --include="*.env" \
  --exclude-dir=".git" [项目路径]
```

> ⚠️ 若有输出，先处理敏感内容再继续。这是不可跳过的安全门禁。

---

## 阶段二：绑定 GitHub 凭证（首次执行，之后永久有效）

```bash
# 1. 设置凭证存储方式
git config --global credential.helper store

# 2. 写入 Token（替换 [用户名] 和 [TOKEN]）
echo "https://[用户名]:[TOKEN]@github.com" > ~/.git-credentials
```

> **安全说明**：Token 存储在本机 `~/.git-credentials`，不会上传到任何地方。  
> 完成后同一台机器的所有 `git push` 自动使用此 Token，无需重复输入。  
> 换机器时重新执行本阶段即可。

---

## 阶段三：通过 API 在 GitHub 新建仓库

```bash
curl -s -X POST "https://api.github.com/user/repos" \
  -H "Authorization: token [TOKEN]" \
  -H "Content-Type: application/json" \
  --data-raw '{"name":"[仓库名]","description":"[英文描述]","private":false,"auto_init":false}' \
  | grep -E '"full_name"|"html_url"|"message"'
```

**注意事项**：
- `description` 只用英文（中文或特殊字符会导致 `Problems parsing JSON`）
- 使用单引号包裹 JSON（比双引号 + 转义更可靠）
- `auto_init` 必须为 `false`，否则与本地历史冲突
- **此操作只新建仓库，绝不影响已有仓库**

**预期成功输出**：
```
"full_name": "[用户名]/[仓库名]",
"html_url": "https://github.com/[用户名]/[仓库名]",
```

---

## 阶段四：本地 Git 初始化并关联远程

```bash
cd [项目路径]

# 初始化（已有 .git 时提示 Reinitialized 属正常）
git init

# 关联刚创建的新仓库（务必确认地址正确）
git remote add origin https://github.com/[用户名]/[仓库名].git

# 验证——确认不是已有仓库的地址！
git remote -v
```

---

## 阶段五：暂存 → 提交 → 推送（含失败自愈）

```bash
# 推荐：显式列出文件，避免意外包含敏感内容
git add README.md LICENSE .gitignore [其他文件或目录] ...

# 确认暂存内容
git status --short

# 提交
git commit -m "Initial release: [项目名] v[版本]

[一句话描述核心内容]"

# 推送
git push -u origin master
```

**预期成功输出**：
```
* [new branch]      master -> master
```

### 推送失败自愈指南

| 失败现象 | 判断 | 处理方式 |
|---------|------|---------|
| `Connection timed out` / `Could not resolve host` | 网络波动 | 等待 30 秒后重试：`git push -u origin master` |
| `OpenSSL SSL_read: Connection reset` | 网络中断 | 切换网络（手机热点）后重试 |
| `Failed to connect to github.com port 443` | 代理/防火墙拦截 | 检查系统代理，或尝试 SSH 方式：`git remote set-url origin git@github.com:[用户名]/[仓库名].git` |
| 连续 3 次网络失败 | 持续故障 | 检查 [GitHub Status](https://githubstatus.com)，确认平台是否正常 |

> **自动重试命令**（网络偶发失败时）：
> ```bash
> for i in 1 2 3; do git push -u origin master && break || (echo "第 $i 次失败，30秒后重试..."; sleep 30); done
> ```

---

## 阶段六：验证上传结果

打开浏览器访问 `https://github.com/[用户名]/[仓库名]`，逐项核查：

- [ ] 所有目标文件/目录出现在仓库页面
- [ ] README.md 在仓库首页正常渲染
- [ ] LICENSE 中 `[Author]` 已替换为真实姓名（✅ 阶段一已预检）
- [ ] 敏感文件（`*.env`、`secrets/`、密钥文件）**未**出现

---

## 后续更新（已有仓库推送新内容）

```bash
cd [项目路径]
git add [修改的文件]
git commit -m "描述本次变更"
git push
# Token 已存储，无需输入密码
```

---

## 完整常见问题排查

| 报错信息 | 原因 | 修复方法 |
|---------|------|---------|
| `fatal: not a git repository` | 未执行 `git init` | 执行 `git init` |
| `remote: Repository not found` | 远程地址错误或 Token 无效 | 检查 `git remote -v`，重新执行阶段二、三 |
| `error: remote origin already exists` | 已有 remote 配置 | `git remote set-url origin [新地址]` |
| `rejected — non-fast-forward` | 远程有内容与本地冲突 | `git pull --rebase origin master` 后再 push |
| `Problems parsing JSON` | description 含中文或特殊字符 | 改用纯英文，JSON 用单引号包裹 |
| Token 认证失败 | Token 过期或缺少 `repo` 权限 | 前往 [GitHub Settings](https://github.com/settings/tokens) 重新生成 |
| `Updates were rejected` | 远程分支已存在提交 | `git pull --rebase` 后再 push；确需覆盖则 `--force`（⚠️ 不可逆）|
| `[Author]` 仍在 LICENSE | 忘记替换占位符 | 阶段一预检会主动拦截，修复后继续 |

---

## 安装此 Skill

```bash
# Windows
copy github-upload.md %USERPROFILE%\.claude\skills\github-upload.md

# macOS / Linux
cp github-upload.md ~/.claude/skills/github-upload.md
```

安装后在 Claude Code 中输入 `/github-upload` 即可触发。

---

*Skill 版本：v1.2 · 更新：2026-04-10*  
*项目主页：https://github.com/jiayu6954-sudo/github-upload-skill*  
*所属矩阵：[AI-Project-OS](https://github.com/jiayu6954-sudo/AI-Project-OS) — AI 协作工程方法论库*

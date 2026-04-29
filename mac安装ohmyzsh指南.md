# Mac 安装 oh-my-zsh 详细指南

## 一、前置条件：安装 Zsh

macOS 自带 Zsh，可通过以下命令确认版本：

```bash
zsh --version
```

将 Zsh 设为默认 Shell（macOS Catalina 之后默认即为 Zsh）：

```bash
chsh -s /bin/zsh
```

---

## 二、安装 oh-my-zsh

使用官方脚本一键安装：

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

如果网络不通，可使用国内镜像：

```bash
sh -c "$(curl -fsSL https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh)"
```

安装成功后，配置文件位于 `~/.zshrc`。

---

## 三、安装推荐插件

### 3.1 zsh-autosuggestions（命令自动补全/建议）

根据历史命令实时提示，按 `→` 键接受建议：

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

国内镜像：

```bash
git clone https://gitee.com/lightnear/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

### 3.2 zsh-syntax-highlighting（命令语法高亮）

正确命令显示绿色，错误命令显示红色，实时反馈：

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

国内镜像：

```bash
git clone https://gitee.com/lightnear/zsh-syntax-highlighting ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

### 3.3 z（目录跳转）

oh-my-zsh **内置插件**，无需额外安装，根据访问频率智能跳转目录。

---

## 四、配置 ~/.zshrc

使用编辑器打开配置文件：

```bash
vim ~/.zshrc
```

### 4.1 启用插件

找到 `plugins=` 行，修改为：

```bash
plugins=(
    git
    z
    zsh-autosuggestions
    zsh-syntax-highlighting
)
```

**插件说明：**

| 插件                      | 作用                                  |
| ------------------------- | ------------------------------------- |
| `git`                     | 显示当前分支名、提供 git 别名快捷命令 |
| `z`                       | 根据历史访问频率快速跳转目录          |
| `zsh-autosuggestions`     | 历史命令自动建议，按 → 接受           |
| `zsh-syntax-highlighting` | 命令语法实时高亮                      |

### 4.2 配置历史记录

在 `~/.zshrc` 文件末尾添加以下内容：

```bash
# ========== 历史记录配置 ==========

# 历史记录文件路径
HISTFILE=~/.zsh_history

# 内存中保存的历史命令条数
HISTSIZE=10000

# 磁盘上保存的历史命令条数
SAVEHIST=10000

# 不记录重复命令（连续重复的只保留一条）
setopt HIST_IGNORE_DUPS

# 不记录重复命令（全局去重）
setopt HIST_IGNORE_ALL_DUPS

# 历史命令中相同命令只保留最新一条
setopt HIST_SAVE_NO_DUPS

# 命令行前缀带空格时不记录（类似 bash 的 HISTCONTROL=ignorespace）
setopt HIST_IGNORE_SPACE

# 实时共享历史记录（多个终端窗口共享）
setopt SHARE_HISTORY

# 展开历史时不立即执行，允许编辑
setopt HIST_VERIFY

# 存储历史时移除多余的空白
setopt HIST_REDUCE_BLANKS

# 允许使用 ! 引用历史命令时即时展开
setopt HIST_BANG
```

### 4.3 配置 Git 分支显示

oh-my-zsh 默认主题 `robbyrussell` 已支持 Git 分支显示，但提示符中分支信息较简略。

如果你想更清晰地显示 **分支名 + 状态**，在 `~/.zshrc` 末尾添加：

```bash
# ========== Git 分支显示增强 ==========

# 自定义 RPROMPT（右侧提示符），显示 git 分支和状态
# 分支名后：* = 有未提交的修改，+ = 有暂存的修改，? = 有未跟踪的文件
function git_branch_status() {
    local branch=$(git symbolic-ref --short HEAD 2>/dev/null || git describe --tags --exact-match 2>/dev/null)
    if [ -n "$branch" ]; then
        local dirty=""
        if [ -n "$(git status --porcelain 2>/dev/null)" ]; then
            dirty="*"
        fi
        local staged=""
        if [ -n "$(git diff --cached --name-only 2>/dev/null)" ]; then
            staged="+"
        fi
        echo " %F{cyan}git:(%F{red}${branch}${dirty}${staged}%F{cyan})%f"
    fi
}

# 设置右侧提示符
RPROMPT='$(git_branch_status)'
```

> 默认主题 `robbyrussell` 的左侧提示符已包含 `git:(分支名) ✗` 格式。
> 上述 RPROMPT 在右侧提供额外分支状态（是否有修改/暂存），按需添加即可。

### 4.4 autosuggestions 样式调整（可选）

默认建议文字颜色较淡，可在 `~/.zshrc` 末尾添加修改：

```bash
# ========== Autosuggestions 样式 ==========

# 建议文字颜色（默认 fg=8 较淡，改为 fg=5 更清晰）
ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE="fg=5"
```

---

## 五、生效配置

```bash
source ~/.zshrc
```

---

## 六、验证安装结果

| 验证项             | 方法                                         |
| ------------------ | -------------------------------------------- |
| oh-my-zsh 安装成功 | 终端提示符已变化                             |
| git 分支显示       | 进入任意 git 仓库目录，提示符中出现分支名    |
| 命令自动建议       | 输入曾用命令的前几个字符，出现灰色建议       |
| 命令语法高亮       | 输入正确命令变绿，错误命令变红               |
| 厽令历史记录       | 按 `↑` 可搜索历史命令                        |
| z 目录跳转         | 访问过某目录后，输入 `z 目录关键词` 即可跳转 |

---

## 七、常用 git 别名（git 插件自带）

oh-my-zsh 的 git 插件提供了大量别名，常用如下：

| 别名    | 完整命令                               |
| ------- | -------------------------------------- |
| `gst`   | `git status`                           |
| `gco`   | `git checkout`                         |
| `gaa`   | `git add --all`                        |
| `gcmsg` | `git commit -m`                        |
| `gp`    | `git push`                             |
| `gl`    | `git pull`                             |
| `glog`  | `git log --oneline --decorate --graph` |
| `gd`    | `git diff`                             |
| `gb`    | `git branch`                           |

完整列表见：`https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/git`

---

## 八、卸载（如需）

```bash
uninstall_oh_my_zsh
```

---

## 九、完整 ~/.zshrc 参考配置

以下为关键配置汇总，供对照检查：

```bash
# 主题（默认即可）
ZSH_THEME="robbyrussell"

# 插件
plugins=(
    git
    z
    zsh-autosuggestions
    zsh-syntax-highlighting
)

source $ZSH/oh-my-zsh.sh

# ========== 历史记录配置 ==========
HISTFILE=~/.zsh_history
HISTSIZE=10000
SAVEHIST=10000
setopt HIST_IGNORE_DUPS
setopt HIST_IGNORE_ALL_DUPS
setopt HIST_SAVE_NO_DUPS
setopt HIST_IGNORE_SPACE
setopt SHARE_HISTORY
setopt HIST_VERIFY
setopt HIST_REDUCE_BLANKS
setopt HIST_BANG

# ========== Autosuggestions 样式 ==========
ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE="fg=5"

# ========== Git 分支显示增强 ==========
function git_branch_status() {
    local branch=$(git symbolic-ref --short HEAD 2>/dev/null || git describe --tags --exact-match 2>/dev/null)
    if [ -n "$branch" ]; then
        local dirty=""
        if [ -n "$(git status --porcelain 2>/dev/null)" ]; then
            dirty="*"
        fi
        local staged=""
        if [ -n "$(git diff --cached --name-only 2>/dev/null)" ]; then
            staged="+"
        fi
        echo " %F{cyan}git:(%F{red}${branch}${dirty}${staged}%F{cyan})%f"
    fi
}

RPROMPT='$(git_branch_status)'
```


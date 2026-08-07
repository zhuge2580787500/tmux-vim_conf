# tmux-vim_conf

个人 tmux 与 Vim 配置文件集合，带一键安装脚本。**目标场景：新 Linux 服务器上 clone 后直接运行脚本，即可用上自己的 tmux / vim 配置。**

## 目录结构

| 文件 | 说明 |
|------|------|
| `.vimrc` | Vim 配置 |
| `.tmux.conf` | tmux 配置 |
| `colors/molokai.vim` | Molokai 配色主题（256 色终端） |
| `runner.sh` | 一键安装脚本 |
| `README.md` | 本文档 |

## 用法（新服务器）

```bash
# 1. clone 到任意位置
git clone https://github.com/zhuge2580787500/tmux-vim_conf.git
cd tmux-vim_conf

# 2. 安装配置
sh runner.sh          # 安装到当前账号 (~) —— 推荐, 不需要 root
# 或: sudo sh runner.sh -s   # 安装到系统级 (/etc), 这台服务器上所有用户都生效
```

### 安装位置

| 无参数（当前账号） | `-s` 系统级 |
|-------------------|-------------|
| `~/.tmux.conf` | `/etc/tmux.conf` |
| `~/.vimrc` | `/etc/vim/vimrc.local`（Debian/Ubuntu）或 `/etc/vimrc` |
| `~/.vim/colors/molokai.vim` | `/etc/vim/colors/molokai.vim` |

### 关于「任何位置都能用」

装到**当前账号**（`~` 下）：只要用你的账号登录这台服务器，无论你在哪个目录打开 vim / tmux 都会加载你的配置。非 root 也能装。

装到**系统级**（`sudo runner.sh -s`）：`/etc` 下的配置对所有用户生效，root 也会读。需要 root 权限，改的是全机器。

### 说明

- 脚本以**拷贝**方式安装：配置文件复制到 home / 系统目录后，**可以安全删除本仓库文件夹**，配置不受影响。
- 若目标文件已存在，会先备份为 `*.bak.<时间戳>` 再覆盖，不直接抹掉旧配置（旧软链接会被移除，不影响其指向的原文件）。
- 因为是拷贝而不是软链接，**没有 `git pull` 自动同步**——以后改了配置要再拷一次（`sh runner.sh` 重跑即可覆盖）。
- 缺少 `tmux` / `vim` 时只警告，脚本不会因此失败。
- **新开终端 / 重启 tmux / vim** 后生效。

## 功能

### Vim

- Molokai 配色（256 色终端，深色背景）
- 显示行号、当前行高亮、括号匹配
- 4 空格缩进（`expandtab`），C 风格自动缩进（`cindent`）
- 增量搜索 + 搜索词高亮
- 编码 UTF-8 优先，兼容 GBK / GB2312 中文
- 跨文件复制：可视模式 `<leader>y` 存入临时文件，普通模式 `<leader>p` 读回
- 语法折叠（默认全部展开）

### tmux

- 前缀键改为 `C-a`（替换默认 `C-b`）
- vi 模式键位
- 鼠标支持
- 状态栏：左侧主机/会话，右侧实时时钟与日期
- 100000 行历史缓冲

## 快捷键

### tmux（前缀 = `C-a`）

| 快捷键 | 功能 |
|--------|------|
| `C-a` `"` | 上下分屏 |
| `C-a` `%` | 左右分屏 |
| `C-a` `c` | 新建窗口 |
| `C-a` `h/j/k/l` | 切换面板 左/下/上/右 |
| `Alt+方向键` | 无需前缀切换面板 |
| `Alt+h` / `Alt+l` | 上一个 / 下一个窗口 |
| `C-a` `<` / `>` / `-` / `+` | 面板 左/右/下/上 各移动 7 格 |
| `C-a` `C-a` | 向程序发送字面量 `C-a` |

### Vim

| 快捷键 | 模式 | 功能 |
|--------|------|------|
| `<leader>y` | 可视 | 将选中文本写入 `/tmp/vitmp` |
| `<leader>p` | 普通 | 读取 `/tmp/vitmp` 粘贴到光标处 |
| `Q` | 普通 | 用 `gq` 重排段落 |

> `<leader>` 默认键为 `\`。

## 依赖

- Vim 7.4+（建议 8.0+）
- tmux 1.9+（鼠标与「分屏保持当前路径」特性需 2.1+）
- 终端需支持 256 色

## 卸载 / 回退

装完删除仓库后, 卸载也只需删这几个文件:

```bash
# 用户级
rm ~/.tmux.conf ~/.vimrc ~/.vim/colors/molokai.vim
# 系统级 (sudo)
rm /etc/tmux.conf /etc/vim/vimrc.local /etc/vim/colors/molokai.vim
# 有备份时恢复: mv ~/.vimrc.bak.<时间戳> ~/.vimrc
```

## 待优化项

- `.vimrc` 存在重复设置（`syntax on`、`set ruler`、`set incsearch`、`filetype plugin indent on`、`set t_Co=256`、`set tabstop` 均出现多次）、前后矛盾的设置（`set backup` 与 `set nobackup` 并存）、以及大量被注释掉的试验代码残留。
- `.vimrc` 中 `hi NonText` / `hi Folded` 自定义高亮写在 `colorscheme molokai` 之前，加载配色时会被覆盖，等于无效。
---
layout: post
title:  " Linux 常用命令速查表  & Tmux 使用指南"
date:   2026-04-15 20:35:16 +0800
categories: jekyll update
---


> 涵盖文件系统结构、文件操作、查看系统、进程管理、网络、权限等日常高频命令

## ⚡ 0. Oh-my-posh
设置参考：https://www.cnblogs.com/qiuliw/p/19553947

## 😒 0. Tmux

tmux中Linux那套命令都可以用，tmux的快捷键是基于`Ctrl + b`的，不过默认键已经改成了`Crtl +a`.

session, window, pane的概念：
- session：一个 tmux 实例，可以包含多个窗口
- window：一个 tmux 窗口，可以包含多个窗格
- pane：一个 tmux 窗格，可以运行一个命令或程序

config文件：`~/.tmux.conf`，可以在里面设置 tmux 的各种选项和快捷键. 使用nvim编辑这个文件，修改后需要重新加载配置（`tmux source-file ~/.tmux.conf`）才能生效。这个命令是在Linux终端中执行的，不是在tmux中执行的；或者在tmux中按 `Ctrl + a`，然后按 `:` 进入命令模式，输入 `source-file ~/.tmux.conf` 来重新加载配置。注：`~`表示用户家目录，看Linux那部分写了。
  
快捷键：
- `tmux new -s session_name`：创建新会话
- `tmux ls`：列出所有会话
- `tmux attach -t session_name`：连接到会话
- `tmux kill-session -t session_name`：关闭会话
- `Ctrl + a`，然后按 `c`：创建新窗口
- `Ctrl + a`，然后按 `n`：切换到下一个窗口
- `Ctrl + a`，然后按 `p`：切换到上一个窗口
- `Ctrl + a`，然后按 `w`：显示窗口列表
- `Ctrl + a`，然后按 `%`：水平分割窗口
- `Ctrl + a`，然后按 `"`：垂直分割窗口
- `Ctrl + a`，然后按 `o`：在分割的窗格之间切换
- `Ctrl + a`，然后按 `x`：关闭当前窗格
- `Ctrl + a`，然后按 `d`：分离会话（回到普通终端）
- `Ctrl + a`，然后按 `?`：显示帮助
- `Ctrl + a`，然后按 `[`：进入复制模式（使用方向键移动，按 `q` 退出）
- `Ctrl + a`，然后按 `]`：粘贴复制模式中的内容
- `Ctrl + a`，然后按 `:`：进入命令模式，可以输入 tmux 命令（如 `resize-pane -D 10` 调整窗格大小）
- `Ctrl + a`，然后按 `s`：在分割的窗格之间切换
- `Ctrl + a`，然后按 `z`：最大化/恢复当前窗格
- `Ctrl + a`，然后按 `l`：切换到上一个窗口
- `Ctrl + a`，然后按 `f`：切换到下一个窗口
- `Ctrl + a`，然后按 `&`：关闭当前窗口
- `Ctrl + a`，然后按 `,`：重命名当前窗口
- `Ctrl + a`，然后按 `.`：切换到指定窗口（输入窗口编号）
- `Ctrl + a`，然后按 `t`：显示当前时间
- `Ctrl + a`，然后按 `r`：刷新当前窗口
- `Ctrl + a`，然后按 `x`：关闭当前窗格

Tmux中运行R：进入tmux，然后键入`R`，就可以在tmux中使用R了。
[菜鸟教程tmux命令](https://www.runoob.com/linux/nginx-install-setup.html) 



## 🗂️ 0. Linux 文件系统与目录结构

Linux 的文件系统是一棵**倒挂的树**，最顶层是根目录 `/`，所有文件夹、文件、硬盘分区都挂载在根下面。

### Linux 使用nvim/vim
- `nvim filename`：使用 nvim 编辑文件， `vim filename`：使用 vim 编辑文件
- `:w`：保存文件
- `:q`：退出编辑器
- `:wq`：保存并退出
- `:q!`：强制退出不保存
- `i`：进入插入模式
- `Esc`：退出插入模式
- `dd`：删除当前行
- `yy`：复制当前行
- `p`：粘贴
- `u`：撤销
- `Ctrl + r`：重做
- `/pattern`：搜索文本
- `:set number`：显示行号
- `:set nonumber`：隐藏行号
- `:syntax on`：启用语法高亮
- `:syntax off`：禁用语法高亮
- `:help`：查看帮助文档
- `:help nvim`：查看 nvim 的帮助文档
- `:help vim`：查看 vim 的帮助文档
- `:help <command>`：查看特定命令的帮助，如 `:help




### 根目录 `/` 下的常见重要目录

| 目录 | 作用 |
|------|------|
| `/` | 根目录，整个文件系统的起点 |
| `/home` | 用户的家目录所在位置，里面会有 `/home/用户名/` 的文件夹 |
| `/home/你的用户名` | 你的个人文件夹（简称“家目录”），可以用 `~` 或 `$HOME` 代表 |
| `/etc` | 系统配置文件存放处 |
| `/var` | 日志、缓存等经常变化的数据 |
| `/usr` | 用户安装的程序、库、文档 |
| `/tmp` | 临时文件，重启后通常被清空 |
| `/mnt` 或 `/media` | 临时挂载点（如 U 盘、Windows 分区） |
| `/bin` | 基础系统命令（二进制文件） |
| `/sbin` | 系统管理命令（通常需要 root 权限） |
| `/lib` | 系统库文件 |
| `/opt` | 可选的应用软件包 |
| `/proc` | 虚拟文件系统，反映内核和进程状态（内存中的信息） |
| `/dev` | 设备文件（硬盘、终端、随机数生成器等） |

### 查看和浏览目录的常用命令

| 命令 | 说明 | 示例 |
|------|------|------|
| `pwd` | 显示当前所在路径 | `pwd` → `/home/zhang` |
| `ls` | 列出当前目录下的内容 | `ls -la`（显示所有文件，含隐藏，带详情） |
| `cd` | 进入某个目录 | `cd /etc`；`cd ~`（回家）；`cd -`（回上一个目录） |
| `tree` | 以树状结构显示目录（需安装：`sudo apt install tree`） | `tree /home` |
| `ls -R` | 递归列出所有子目录 | `ls -R /home` |
| `file` | 判断文件类型（文本、二进制、脚本等） | `file script.R` |
| `stat` | 查看文件详细属性（inode 信息） | `stat filename` |
| `du` | 查看目录或文件大小 | `du -sh *` |
| `df` | 查看磁盘分区使用情况 | `df -h` |

---

## 📂 1. 文件与目录操作

| 命令 | 说明 | 示例 |
|------|------|------|
| `pwd` | 显示当前工作目录的绝对路径 | `pwd` → `/home/user` |
| `ls` | 列出目录内容 | `ls -la`（显示所有文件，含隐藏，带详情）, `ls -l` (不显示隐藏文件)|
| `cd` | 切换目录 | `cd /etc`；`cd ..`（上级）；`cd ~`（家目录） |
| `mkdir` | 创建新目录 | `mkdir new_folder` |
| `rmdir` | 删除**空目录** | `rmdir empty_folder` |
| `rm` | 删除文件或目录（**不可恢复**） | `rm file.txt`；`rm -rf folder/`（⚠️ 危险） |
| `cp` | 复制文件或目录 | `cp source.txt dest.txt`；`cp -r source_dir/ dest_dir/` |
| `mv` | 移动文件/目录，或重命名 | `mv old.txt new.txt`；`mv file.txt ../` |
| `touch` | 创建空文件，或更新文件时间戳 | `touch newfile.R` |
| `ln` | 创建链接（硬链接或软链接） | `ln -s /path/to/target linkname`（软链接） |

## 📄 2. 查看文件与内容

| 命令 | 说明 | 示例 |
|------|------|------|
| `cat` | 一次性输出完整文件内容 | `cat script.R` |
| `less` / `more` | 分页查看大文件（按 `q` 退出，`/` 搜索） | `less long_log.txt` |
| `head` | 查看文件开头若干行（默认10行） | `head -20 data.csv` |
| `tail` | 查看文件末尾若干行 | `tail -f log.txt`（实时跟踪） |
| `nl` | 显示文件内容并加上行号 | `nl script.R` |
| `tac` | 反向输出文件内容（最后一行先输出） | `tac file.txt` |
| `rev` | 将每一行的字符顺序反转 | `rev file.txt` |
| `wc` | 统计行数、单词数、字节数 | `wc -l file.txt`（只统计行数） |
| `sort` | 对文件内容排序 | `sort names.txt` |
| `uniq` | 报告或忽略重复行（通常与 `sort` 配合） | `sort names.txt \| uniq` |
| `cut` | 提取文件的某一列 | `cut -d',' -f2 data.csv` |
| `diff` | 比较两个文件的差异 | `diff file1.txt file2.txt` |

## 🔍 3. 查找文件与搜索

| 命令 | 说明 | 示例 |
|------|------|------|
| `find` | 按名称、类型、大小等搜索文件 | `find /home -name "*.R"` |
| `locate` | 通过数据库快速查找（需先 `updatedb`） | `locate script.R` |
| `which` | 查找命令的绝对路径 | `which Rscript` → `/usr/bin/Rscript` |
| `whereis` | 查找命令的二进制、源码和手册页 | `whereis ls` |
| `grep` | 在文件或输出中搜索文本（支持正则） | `grep "error" log.txt`；`grep -r "function" ./` |
| `ack` / `ag` | 更强大的代码搜索工具（需安装） | `ack "pattern"` |

## 🔐 4. 权限与用户

| 命令 | 说明 | 示例 |
|------|------|------|
| `chmod` | 修改文件权限（读、写、执行） | `chmod +x script.R` |
| `chown` | 修改文件所有者和组 | `sudo chown user:group file.txt` |
| `sudo` | 以超级用户（root）权限执行命令 | `sudo apt update` |
| `whoami` | 显示当前用户名 | `whoami` |
| `id` | 显示用户ID、组ID和所属组 | `id` |
| `passwd` | 修改当前用户密码 | `passwd` |
| `useradd` / `userdel` | 添加/删除用户（需 root） | `sudo useradd newuser` |
| `groupadd` / `groupdel` | 添加/删除用户组 | `sudo groupadd devteam` |

## ⚙️ 5. 进程与作业控制

| 命令 | 说明 | 示例 |
|------|------|------|
| `ps` | 显示当前进程快照 | `ps aux`（所有用户进程） |
| `top` / `htop` | 动态查看系统进程和资源占用 | `htop`（需安装，交互式） |
| `kill` | 终止进程（通过 PID） | `kill 12345`；`kill -9 12345`（强制） |
| `pkill` / `killall` | 按名称终止进程 | `pkill R` |
| `jobs` | 查看当前终端后台任务 | `jobs -l` |
| `bg` / `fg` | 将任务放到后台/前台运行 | `fg %1` |
| `nohup` | 使进程在终端关闭后继续运行 | `nohup Rscript job.R &` |
| `&` | 在命令末尾加 `&`，将进程放到后台 | `Rscript script.R &` |
| `nice` / `renice` | 调整进程优先级 | `nice -n 10 long_task` |

## 🌐 6. 网络相关

| 命令 | 说明 | 示例 |
|------|------|------|
| `ping` | 测试网络连通性 | `ping google.com`（`Ctrl+C` 停止） |
| `wget` | 下载文件（HTTP/HTTPS/FTP） | `wget https://example.com/file.zip` |
| `curl` | 发送网络请求，可获取网页内容 | `curl -O https://example.com/file` |
| `ssh` | 远程登录到另一台机器 | `ssh user@remote_host` |
| `scp` | 跨主机安全复制文件（基于 SSH） | `scp file.txt user@remote:/path/` |
| `rsync` | 高效同步文件（支持断点续传） | `rsync -avz source/ user@remote:dest/` |
| `netstat` | 查看网络连接、路由表等 | `netstat -tulnp`（查看监听端口） |
| `ss` | 更快的套接字统计工具（替代 netstat） | `ss -tuln` |
| `ip` | 显示/配置网络接口、路由等 | `ip addr show` |

## 🖥️ 7. 系统信息与资源

| 命令 | 说明 | 示例 |
|------|------|------|
| `df` | 查看磁盘分区使用情况 | `df -h` |
| `du` | 查看目录或文件大小 | `du -sh folder/` |
| `free` | 查看内存使用情况 | `free -h` |
| `uname` | 查看系统内核版本等信息 | `uname -a` |
| `hostname` | 显示或设置主机名 | `hostname` |
| `uptime` | 查看系统运行时间、负载 | `uptime` |
| `who` / `w` | 查看当前登录用户 | `who` |
| `last` | 查看最近登录记录 | `last -n 10` |
| `dmesg` | 查看内核日志（硬件、驱动信息） | `dmesg \| tail` |
| `lsblk` | 列出所有块设备（硬盘、分区） | `lsblk` |
| `lscpu` | 显示 CPU 架构信息 | `lscpu` |
| `lspci` | 列出 PCI 设备 | `lspci` |
| `lsusb` | 列出 USB 设备 | `lsusb` |
| `systemctl` | 管理系统服务（systemd） | `systemctl status ssh` |
| `man` | 查看命令的手册（帮助文档） | `man ls` |
| `info` | 查看更详细的 GNU 文档 | `info ls` |
| `history` | 显示最近使用过的命令 | `history`；`!123`（执行第123条） |

## 🧰 8. 其他实用命令

| 命令 | 说明 | 示例 |
|------|------|------|
| `echo` | 输出文本或变量值 | `echo "Hello"`；`echo $PATH` |
| `\|`（管道） | 将前一个命令的输出作为后一个命令的输入 | `ls -la \| grep ".R"` |
| `>` / `>>` | 重定向输出到文件（`>`覆盖，`>>`追加） | `echo "log" > out.txt` |
| `<` | 将文件内容作为命令的输入 | `sort < input.txt` |
| `tee` | 同时输出到屏幕和文件 | `Rscript job.R \| tee output.log` |
| `tar` | 打包/解压 `.tar` 或 `.tar.gz` 文件 | `tar -czf archive.tar.gz folder/`（压缩）<br>`tar -xzf archive.tar.gz`（解压） |
| `zip` / `unzip` | 处理 `.zip` 文件 | `unzip data.zip` |
| `gzip` / `gunzip` | 压缩/解压 `.gz` 文件 | `gzip largefile.txt` |
| `alias` | 为长命令创建别名 | `alias ll='ls -alF'` |
| `source` | 在当前 shell 中执行脚本（常用于加载环境变量） | `source ~/.bashrc` |
| `clear` | 清屏 | `clear` 或 `Ctrl+L` |
| `exit` | 退出当前 shell 或终端 | `exit` |
| `shutdown` / `reboot` | 关机/重启（需 root） | `sudo shutdown -h now` |

---

## 💡 典型工作流命令组合

### 进入项目目录并启动 tmux
```bash
cd /mnt/d/phd/DMDRCensor-main
tmux new -s mytask
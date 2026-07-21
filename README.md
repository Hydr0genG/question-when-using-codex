# Windows 版 Codex：Computer Use 与浏览器控制不可用的排错记录

> 记录日期：2026-07-21  
> 本文是个人排错经验，不是 OpenAI 官方说明。插件名称、安装方式和页面地址以后可能发生变化。

今天使用 Windows 版 Codex 桌面端时，我遇到了 Computer Use 和浏览器控制无法正常使用的问题。下面整理了当时有效的处理方法，希望能给遇到类似问题的人提供一个排查方向。

## 问题一：Computer Use 无法使用

### 现象

Codex 桌面端中的 Computer Use 插件无法正常工作，相关的 Chrome 控制能力也不可用。

### 处理方法

可以把下面这段提示词复制给 Codex，让它自动诊断本机的 bundled 插件源：

```text
我现在使用的是 Windows 版 Codex 桌面端，Computer Use 插件无法正常使用。请你帮我诊断并修复本机 Codex 的 openai-bundled 插件源问题。我的目标是让以下插件恢复正常：

chrome@openai-bundled
computer-use@openai-bundled
```

这里的 `openai-bundled` 可以简单理解为 Codex 随应用提供的内置插件来源。让 Codex 自行诊断时，它可以结合本机实际安装状态检查问题，比照搬固定路径或命令更稳妥。

## 问题二：Chrome 扩展商店提示插件不存在

Computer Use 所需的 Chrome 扩展页面：

[ChatGPT Chrome 扩展](https://chromewebstore.google.com/detail/chatgpt/hehggadaopoacecdllhhajmbjkdcmajg)

如果打开页面时显示“不存在”或无法访问，可以先检查网络代理设置。我的处理方式是把代理工具切换为全局模式，然后重新打开页面。

请注意：全局代理会让更多应用流量经过代理，影响范围不只当前浏览器。安装完成后，可以根据自己的网络环境恢复原来的代理模式；同时请遵守所在地区的法律法规和单位网络政策。

## 问题三：浏览器控制仍然不可用

如果插件和扩展看起来已经安装，但浏览器控制仍然不可用，可以直接让 Codex 根据当前电脑状态继续诊断。建议描述清楚以下信息：

- 使用的是 Windows 版 Codex 桌面端；
- Computer Use 或 Chrome 控制的具体报错；
- 插件是否已经安装；
- Chrome 扩展是否启用；
- 希望 Codex 先检查、说明原因，再执行修复。

示例提示词：

```text
我使用的是 Windows 版 Codex 桌面端，目前浏览器控制不可用。请检查 Computer Use 插件、Chrome 插件、浏览器扩展以及相关连接状态，先告诉我问题原因，再帮我完成可以安全执行的修复。涉及删除文件、修改系统级配置或其他高风险操作时，请先征得我的同意。
```

## 问题四：Computer Use 可以看到并打开，但使用时报 `node_repl` 错误

### 现象

插件页中能看到并打开 Computer Use，但真正调用时出现 `node_repl kernel exited unexpectedly`。日志还可能包含 `windows sandbox failed: spawn setup refresh`、`请求的操作需要提升` 或 `os error 740`。

`node_repl` 可以通俗理解为 Codex 执行插件代码的 JavaScript 运行环境。这一类错误偏向 Windows 沙盒启动或权限异常，和“插件消失、bundled 缓存文件缺失”不一定是同一个问题，不应直接套用删除插件缓存的办法。

### 处理方法

可以把下面这段提示词复制给 AI，让它先诊断、备份，再根据证据修复：

```text
我使用的是 Windows 版 Codex Desktop。Computer Use 插件能够看到并打开，但实际使用时报错，关键词可能包括：

- node_repl kernel exited unexpectedly
- windows sandbox failed: spawn setup refresh
- codex-windows-sandbox-setup.exe 请求的操作需要提升
- os error 740

请在 Windows 本机环境中帮我诊断并尽量修复，要求如下：

1. 先检查 Codex、Computer Use、Chrome 插件状态和近期日志，确认根因后再修改；不要一开始就删除目录或改配置。
2. 区分 Windows 沙盒权限问题和 openai-bundled 插件缓存/文件锁问题，不要混用解决方案。
3. 修改前备份相关配置，并告诉我备份目录。不要读取、输出或修改 API key、登录 token、auth.json 或无关配置。
4. 只有日志明确出现 os error 740、请求的操作需要提升或 windows sandbox failed 时，才判断是否需要调整 Windows sandbox 配置；说明修改的作用范围和风险后再执行。
5. 如果发现 EBUSY、resource busy or locked、plugin_cache_windows_file_lock、os error 5 或 marketplace.json does not exist，则改按 bundled 插件文件锁/缓存损坏方向处理。
6. 不要强制结束 Codex Desktop 主进程。涉及删除目录、停止进程或修改系统级配置时，先征得我的同意。
7. 修复后重新验证 node_repl、Computer Use、Chrome 插件和浏览器控制，并汇总：根因证据、备份位置、实际改动、验证结果，以及仍未解决的步骤。
```

这段提示词根据社区排错方法进行了精简改写，完整背景与原方法见：[LINUX DO：Codex Computer Use 插件无法正常使用解决方案指北](https://linux.do/t/topic/2283790)。社区内容不是官方说明，请根据自己的报错选择对应分支，执行系统修改前务必确认备份。

## 这次排错得到的经验

遇到类似问题时，我会按下面的顺序处理：

1. 先到社交媒体、社区帖子或相关讨论的评论区搜索相同报错，看看是否已有可复现的解决办法。
2. 找不到可靠答案时，把运行环境、错误现象和目标描述清楚，让 Codex 或其他 agent 根据本机实际状态进行诊断。
3. 执行修复前确认命令的作用范围；涉及删除文件、系统配置、账号或密钥时，不要直接照抄陌生命令。
4. 修复后重新测试插件、浏览器扩展和控制功能，并记录真正有效的步骤。

如果这篇记录帮到了你，欢迎补充你遇到的报错和有效解法，让其他人少走一点弯路。

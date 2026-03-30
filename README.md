## Source Code

```bash
claude \
  --dangerously-skip-permissions \
  --output-format=stream-json \
  --verbose \
  -p "请实现一个 Windows 10 语音输入法应用（C# + .NET 8 + WPF），具体要求：

1. 按住 Fn 键录音，松开后将转录文字注入当前聚焦的输入框。优先使用Azure Speech SDK实现流式转录。Fn 键通过低级键盘钩子（SetWindowsHookEx + WH_KEYBOARD_LL）全局监听，需抑制 Fn 事件传递避免触发系统功能。
2. 默认语言必须为简体中文（zh-CN），确保开箱即用识别中文输入。同时在托盘提供语言切换（英语、简体中文、繁体中文、日语、韩语），存储在本地JSON配置
3. 录音时在屏幕底部居中显示一个特别优雅精致的无边框胶囊状悬浮窗，不要有标题栏和系统按钮：
   - 使用 WPF Window（WindowStyle=None, AllowsTransparency=True, Topmost=True）高度足够 (56px，圆角半径 28px)
   - 左侧 5 根竖条波形（44×32px），由实时音频 RMS 电平驱动（NAudio）说话声音大波形就大、安静时波形就小
     各竖条权重：[0.5, 0.8, 1.0, 0.75, 0.55] 形成自然的中间高两侧低效果,平滑包络：attack 40%，release 15%,每根竖条加入 ±4% 随机抖动增加有机感。波形要足够大，清晰可见
   - 右侧文字标签（弹性宽度 160-560px）实时显示转录文本，胶囊随文字变多而弹性变宽
   - 入场弹簧动画（0.35s）、文字宽度平滑过渡（0.25s）、退场缩放（0.22s）（WPF Storyboard 实现）
4. 文字注入使用剪贴板 + 模拟 Ctrl+V（SendInput）注入完成后等待约 100ms 再恢复原剪贴板内容
5. 接入 LLM 来提升语音识别的准确率，尤其是中英文混杂的情况下。通过 OpenAI 兼容 API（可配置 API Base URL、API Key、Model）对转录文本进行 refine。LLM 的 system prompt 要求非常保守地纠错：只修复明显的语音识别错误（如中文谐音错误、英文技术术语被错误转为中文如「配森」→「Python」、「杰森」→「JSON」），绝对不要改写、润色或删除任何看起来正确的内容，如果输入看起来正确则必须原样返回。
6. 提供系统托盘菜单（NotifyIcon）：
   - 启用/禁用 LLM Refinement
   - Settings 窗口（WPF）
     包含 API Base URL、API Key、Model 输入框
     API Key 支持完全清空
     提供 Test 和 Save 按钮
   - 松开 Fn 后若启用 LLM，则悬浮窗显示 “Refining...” 状态，等 LLM 返回后再注入最终文本
7. 应用以后台常驻运行：
   - 无任务栏窗口，仅托盘图标
   - 构建产物为单文件自包含 .exe
   - 输出到 Release\VoiceInput.exe，双击即可直接运行，无需安装"
```

## Dist

https://github.com/cracker999999/windows-voice-input
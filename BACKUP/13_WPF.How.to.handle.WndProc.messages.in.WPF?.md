# [WPF How to handle WndProc messages in WPF?](https://github.com/bigbosschenyibo/gitblog/issues/13)

想要在MainWindow中监听系统消息，从而做一些自己的逻辑处理；那么怎么样才能接收到系统消息呢？
1. 需要获取窗口句柄
2. 要在窗口初始化之后才可以进行获取句柄，比如在构造函数里就获取不到句柄
3. 需要用到System.Windows.Interop空间下的 HwndSource类
下面给出一个示例：
``` c#
using System;
using System.Windows;
using System.Windows.Interop;

namespace ProWndMsg
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        protected override void OnSourceInitialized(EventArgs e)
        {
            base.OnSourceInitialized(e);
            var wndInterop= new WindowInteropHelper(this);
            HwndSource.FromHwnd(wndInterop.Owner).AddHook(OnMessage);
        }

        private IntPtr OnMessage(IntPtr hwnd, int msg, IntPtr wParam, IntPtr lParam, ref bool handled)
        {
            if (msg == someMsgType)
            {
                //do your logic
                handled = true;
            }
            return IntPtr.Zero;
        }
    }
}
```
参考：
[How to handle WndProc messages in WPF?](https://stackoverflow.com/questions/624367/how-to-handle-wndproc-messages-in-wpf)
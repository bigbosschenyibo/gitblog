# [WPF 创建一个单实例应用程序](https://github.com/bigbosschenyibo/gitblog/issues/15)

在stackoverflow上搜索了相关问题，最后从这个回答中得到了答案，遂记录下来以作备忘；
[What is the correct way to create a single-instance WPF application?](https://stackoverflow.com/a/522874)
最终效果为：**双击exe运行程序后，再次双击exe程序不会再次打开，而是能够将已经打开的程序展示在屏幕最前方**
![单实例应用程序防止多开](https://github.com/user-attachments/assets/2ac22505-9426-4792-8ff4-01dba5118582)
代码如下：
``` c#
using System;
using System.Runtime.InteropServices;
using System.Threading;

namespace SingleInstanceAppDemo
{
    public static class Program
    {
        static Mutex mutex = new Mutex(true, "Mutex_SingleInstanceAppDemo");

        [STAThread]
        static void Main()
        {
            if (mutex.WaitOne(TimeSpan.Zero, true))
            {
                App app = new App();
                app.InitializeComponent();
                app.Run();
                mutex.ReleaseMutex();
            }
            else
            {
                // send our Win32 message to make the currently running instance
                // jump on top of all the other windows
                NativeMethods.PostMessage((IntPtr)NativeMethods.HWND_BROADCAST, NativeMethods.WM_SHOWME, IntPtr.Zero, IntPtr.Zero);
            }
        }
    }

    /// <summary>
    /// this class just wraps some Win32 stuff that we're going to use
    /// </summary>
    internal class NativeMethods
    {
        public const int HWND_BROADCAST = 0xffff;
        public static readonly int WM_SHOWME = RegisterWindowMessage("WM_SHOWME");
        [DllImport("user32")]
        public static extern bool PostMessage(IntPtr hwnd, int msg, IntPtr wparam, IntPtr lparam);
        [DllImport("user32")]
        public static extern int RegisterWindowMessage(string message);
    }
}
```
MainWindow监听系统消息:
``` c#
using System;
using System.Windows;
using System.Windows.Interop;

namespace SingleInstanceAppDemo
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
            var wndInterop = new WindowInteropHelper(this);
            HwndSource.FromHwnd(wndInterop.Handle).AddHook(OnMessage);
        }

        private IntPtr OnMessage(IntPtr hwnd, int msg, IntPtr wParam, IntPtr lParam, ref bool handled)
        {
            if (msg == NativeMethods.WM_SHOWME)
            {
                ShowMe();
                handled = true;
            }
            return IntPtr.Zero;
        }

        private void ShowMe()
        {
            if (WindowState == WindowState.Minimized)
            {
                WindowState = WindowState.Normal;
            }
            // get our current "TopMost" value (ours will always be false though)
            bool top = Topmost;
            // make our form jump to the top of everything
            Topmost = true;
            // set it back to whatever it was
            Topmost = top;
        }
    }
}
```
参考：
[What is the correct way to create a single-instance WPF application?](https://stackoverflow.com/questions/19147/what-is-the-correct-way-to-create-a-single-instance-wpf-application)
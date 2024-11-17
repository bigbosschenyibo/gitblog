# [WPF 如何不使用App而是自己写程序入口](https://github.com/bigbosschenyibo/gitblog/issues/14)

在我们的认知中，程序都是有一个入口点的，那么WPF程序的入口点在哪儿，我们可以自定义程序入口点么？
1. 新建一个WPF项目，可以展开App.cs文件看到里面的详情：
![WPF程序默认的入口点](https://github.com/user-attachments/assets/925c1597-9e2c-4287-91b0-60f17ed6fda8)
2. 仿照APP类，自己写一个入口点：
``` c#
using System;

namespace SingleInstanceAppDemo
{
    public static class Program
    {
        [STAThread]
        static void Main()
        {
            SingleInstanceAppDemo.App app = new SingleInstanceAppDemo.App();
            app.InitializeComponent();
            app.Run();
        }
    }
}
```
3. 项目上点击鼠标右键可以将设置程序启动对象 选择我们刚才添加的Program：
![设置启动对象](https://github.com/user-attachments/assets/5ca67da8-6364-4a48-b63b-ffd4dadd80c9)
4. 通过上面操作，我们修改了默认入口点，改成了自定义的程序入口点。
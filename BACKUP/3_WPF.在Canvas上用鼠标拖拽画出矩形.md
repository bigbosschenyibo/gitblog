# [WPF 在Canvas上用鼠标拖拽画出矩形](https://github.com/bigbosschenyibo/gitblog/issues/3)

> 先看效果

![鼠标拖拽画出矩形](https://github.com/user-attachments/assets/83a808fb-c341-4561-a192-a5f2ba0fc0cc)
> 其实代码很简单 注意三点：
1. Canvas中的矩形控件要设置水平居左、垂直方向居顶
2. Canvas要设置一下背景色比如Transparent 不然不响应事件
3. 让Canvas监听三个事件，分别是：鼠标按下、鼠标抬起、鼠标移动
以下便是代码示例：
- 布局代码
``` xml
<Window
    x:Class="DrawRectInCanvasDemo.MainWindow"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:local="clr-namespace:DrawRectInCanvasDemo"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    Title="MainWindow"
    Width="800"
    Height="450"
    mc:Ignorable="d">
    <Grid>
        <Canvas
            x:Name="canvas"
            Background="Transparent"
            MouseDown="canvas_MouseDown"
            MouseMove="canvas_MouseMove"
            MouseUp="canvas_MouseUp">
            <Rectangle
                x:Name="rect"
                HorizontalAlignment="Left"
                VerticalAlignment="Top"
                Stroke="Red"
                StrokeThickness="2"
                Visibility="Collapsed" />
        </Canvas>
    </Grid>
</Window>

```
- 逻辑代码
``` c#
using System.Windows;
using System.Windows.Input;

namespace DrawRectInCanvasDemo
{
    /// <summary>
    /// MainWindow.xaml 的交互逻辑
    /// </summary>
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        private bool started;
        private Point _downPoint;

        private void canvas_MouseDown(object sender, MouseButtonEventArgs e)
        {
            started = true;
            rect.Visibility = Visibility.Visible;
            _downPoint = e.GetPosition(canvas);
        }

        private void canvas_MouseUp(object sender, MouseButtonEventArgs e)
        {
            started = false;
        }

        private void canvas_MouseMove(object sender, MouseEventArgs e)
        {
            if (started)
            {
                var currentPoint = e.GetPosition(canvas);
                var tempRect = new System.Windows.Rect(_downPoint, currentPoint);
                rect.Margin = new Thickness(tempRect.Left, tempRect.Top, 0, 0);
                rect.Width = tempRect.Width;
                rect.Height = tempRect.Height;
            }
        }
    }
}

```
参考：[WPF 在image控件用鼠标拖拽出矩形](https://www.cnblogs.com/lindexi/p/12087079.html)
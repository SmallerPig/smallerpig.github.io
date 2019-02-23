---
title: Dispatcher.Invoke方法
id: 441
categories:
  - 'C#'
  - Windows Phone 8
  - WPF
date: 2014-03-20 16:46:52
tags:
---

前一篇小猪分享过[在WPF中简单的使用BackgroundWorker完成多线程操作](http://www.smallerpig.com/archives/433)！在那篇中小猪利用了BackgroundWorker组件对耗时比较多的操作放在了单独的BackgroundWorker里来完成，例如说：网络请求的登录操作，说到网络请求当然还有另外一种请求：网络下载。

当客户端需要进行网络下载操作时如果只是简单的用多线程这么一个操作而不给用户知道当前的下载进度的话那么用户将不知道已经下载了多少，甚至有可能直接关闭了主应用程序。那就杯具了。

这时候就涉及到在另外的线程中来更新UI，但是WPF却明确的规定：UI元素只能由其主线程来操作，其他任何线程都不可以直接操作UI。而实时的下载进度又不能通过调用某个回调函数来完成更新UI。
 WPF中的UI控件，如果我们探究本质，他们都是从DispatcherObject继承，所以都必须由UI线程进行调度和使用，如果我们在其他的后台线程中操作界面相关的元素时，就会出现如下的异常信息：调用线程无法访问此对象，因为另一个线程拥有该对象。

这时候就是Dispatcher.Invoke方法上场的时间了
在 WPF 中，只有创建 DispatcherObject 的线程才能访问该对象。 例如，一个从主 UI 线程派生的后台线程不能更新在该 UI 线程上创建的 Button 的内容。 为了使该后台线程能够访问 Button 的 Content 属性，该后台线程必须将此工作委托给与该 UI 线程关联的 Dispatcher。使用 Invoke 或 BeginInvoke 来完成此操作。 Invoke 是同步操作，而 BeginInvoke 是异步操作。 该操作将按指定的 DispatcherPriority 添加到 Dispatcher 的事件队列中。 

下面代码实现了我们想要的功能：
```
private delegate void SetTipsValue_dg(long solength, long stlength);
private void SetTipsValue(long solength, long stlength)
{
    block_Tips.Text = "下载中...." + solength + "/" + stlength;
}

private bool DownloadFile(string URL, string filename)
{
    try
    {
        System.Net.HttpWebRequest Myrq = (System.Net.HttpWebRequest)System.Net.WebRequest.Create(URL);
        System.Net.HttpWebResponse myrp = (System.Net.HttpWebResponse)Myrq.GetResponse\(\);
        System.IO.Stream st = myrp.GetResponseStream\(\);
        long stll = myrp.ContentLength;
        System.IO.Stream so = new System.IO.FileStream(filename, System.IO.FileMode.Create);
        byte[] by = new byte[1024];
        int osize = st.Read(by, 0, (int)by.Length);
        while (osize > 0)
        {
            long sol = so.Length;
            long stl = stll;
            this.block_Tips.Dispatcher.Invoke(new SetTipsValue_dg(SetTipsValue), sol, stl);
            so.Write(by, 0, osize);
            osize = st.Read(by, 0, (int)by.Length);
        }
        so.Close\(\);
        st.Close\(\);
        myrp.Close\(\);
        Myrq.Abort\(\);
        return true;
    }
    catch (System.Exception e)
    {
        return false;
    }
}
```


上面代码首先定义了一个委托：该委托接受两个参数分别代表当前下载量和总下载量，然后定义了一个具体的实现该委托的方法，该方法调用UI来显示数据。

在下载数据的主函数DownloadFile中调用了this.block_Tips.Dispatcher.Invoke方法并将实现了委托的方法SetTipsValue方法和当前下载量及总下载量的数值传进去我们就完成了整个操作。这样我们在下载数据的时候用另外的线程开启了DownloadFile方法就可以实时的显当前的下载进度了。
---

layout: post

title:  如何记录asp.net站点重启的原因？

category: 技术

tags: Asp.Net

keywords: Asp.Net IIS Restart

---



#  如何记录asp.net站点重启的原因？


[TOC]


## 在站点执行Application_End事件中添加记录方法。在Global.asax.cs文件中添加如下代码即可：

```csharp
void Application_End(object sender, EventArgs e)
{
    //  Code that runs on application shutdown
    RecordEndReason();
}

protected void RecordEndReason()
{
    HttpRuntime runtime = (HttpRuntime)typeof(System.Web.HttpRuntime).InvokeMember("_theRuntime",
        BindingFlags.NonPublic | BindingFlags.Static | BindingFlags.GetField,
        null,
        null,
        null);

    if (runtime == null)
        return;

    string shutDownMessage = (string)runtime.GetType().InvokeMember("_shutDownMessage",
        BindingFlags.NonPublic | BindingFlags.Instance | BindingFlags.GetField,
        null,
        runtime,
        null);

    string shutDownStack = (string)runtime.GetType().InvokeMember(
        "_shutDownStack",
        BindingFlags.NonPublic | BindingFlags.Instance | BindingFlags.GetField,
        null,
        runtime,
        null);

    EventLog log = new EventLog();

    log.Source = "ASP.NET 2.0.50727.0";
    log.WriteEntry(String.Format("\r\n\r\n_shutDownMessage={0}\r\n\r\n_shutDownStack={1}", shutDownMessage, shutDownStack), EventLogEntryType.Information);
}
```
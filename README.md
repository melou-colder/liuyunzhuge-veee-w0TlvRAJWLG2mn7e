[合集 \- Net 高级调试（基于原著）(11\)](https://github.com)[1\.Advanced .Net Debugging 1：你必须知道的调试工具01\-24](https://github.com/PatrickLiu/p/17965068)[2\.Advanced .Net Debugging 2：CLR 基础02\-27](https://github.com/PatrickLiu/p/18025026)[3\.Advanced .Net Debugging 3：基本调试任务（设置断点等）03\-04](https://github.com/PatrickLiu/p/18036453)[4\.Advanced .Net Debugging 4：基本调试任务（对象检查等）03\-08](https://github.com/PatrickLiu/p/18052105)[5\.Advanced .Net Debugging 5：基本调试任务（线程的操作等）03\-21](https://github.com/PatrickLiu/p/18060906)[6\.Advanced .Net Debugging 6：程序集加载器04\-03](https://github.com/PatrickLiu/p/18096963)[7\.Advanced .Net Debugging 7：托管堆与垃圾收集04\-23](https://github.com/PatrickLiu/p/18120588)[8\.Advanced .Net Debugging 8：线程同步05\-14](https://github.com/PatrickLiu/p/18155084)[9\.Advanced .Net Debugging 9：平台互用性06\-03](https://github.com/PatrickLiu/p/18190864)[10\.Advanced .Net Debugging 10：事后调试07\-04](https://github.com/PatrickLiu/p/18228513)11\.Advanced .Net Debugging 11：完结篇09\-20收起
**一、介绍**　　　　这是我的《Advanced .Net Debugging》这个系列的第十一篇文章，也是这个系列的最后一篇了。我已经把原书的前八章内容全部写完了，本来打算继续写第九章和第十章的内容，后来我放弃逐章逐节的编写，选择了将两章的内容进行过滤后，合为一篇，只把重要的内容包含进来的做法。原因是，我看了原书的后两章内容后，发现很多章节都是一带而过的介绍工具如何使用，讲解的并不是很详细。还有一点更重要的原因就是【高级调试所涉及的知识点】已经全部包含在前面所写的章节中了。如果大家想了解更详细的内容，可以直接去看原书。　　　　还有一点需要说明，原书的内容写的比较早，那时介绍的工具也比较老旧，而且现在也有更好、更新的工具可以替代了，这也是我选择合二为一的一个原因。　　　　如果在没有说明的情况下，所有代码的测试环境都是 Net 8\.0，如果有变动，我会在项目章节里进行说明。好了，废话不多说，开始我们今天的调试工作。　　　　调试环境我需要进行说明，以防大家不清楚，具体情况我已经罗列出来。　　　　　　　　　　操作系统：Windows Professional 10　　　　　　　　　　调试工具：Windbg Preview（Debugger Client:1\.2306\.1401\.0，Debugger engine：10\.0\.25877\.1004）　　　　　　　　　　下载地址：可以去Microsoft Store 去下载　　　　　　　　　　开发工具：Microsoft Visual Studio Community 2022 (64 位) \- Current版本 17\.8\.3　　　　　　　　　　Net 版本：.Net 8\.0　　　　　　　　　　CoreCLR源码：[源码下载](https://github.com)　　　　在这里没有使用两种调试器进行测试，因为内容都太简单了，意义不是很大。**二、目录结构**　　　　为了让大家看的更清楚，也为了自己方便查找，我做了一个目录结构，可以直观的查看文章的布局、内容，可以有针对性查看。　　　　　　　　2\.1、[SOS 和 Visual Studio 2022 集成](https://github.com)　　　　2\.2、[NET 框架源代码级调试](https://github.com)　　　　2\.3、[CLR Profiler 分析器](https://github.com)　　　　　　　　2\.3\.1、[运行 CLR 分析器](https://github.com)　　　　　　　　2\.3\.2、[Summary 视图](https://github.com)　　　　　　　　2\.3\.3、[Histogram 视图](https://github.com)　　　　　　　　2\.3\.4、[Graph 视图](https://github.com)　　　　2\.4、[WinDbg 和 CmdTree 命令](https://github.com)　　　　　　　　A、[基础知识](https://github.com)　　　　　　　　B、[眼见为实](https://github.com)　　　　2\.5、[扩展的诊断信息](https://github.com)　　　　　　　　2\.5\.1、[!Verifyobj](https://github.com) 　　　　　　　　2\.5\.2、[!FindRoots \-gen  \| \-gen any \|](https://github.com) 　　　　　　　　2\.5\.3、[!Heapstat \[\-inclUnrooted \| \-iu]](https://github.com)　　　　　　　　2\.5\.4、[!GCwhere](https://github.com) 　　　　　　　　2\.5\.5、[!ListNearObj](https://github.com)　　　　　　　　2\.5\.6、[!AnalyzeOOM](https://github.com)　　　　　　　　2\.5\.7、[!RCWCleanupList](https://github.com)　　　　2\.6、[后台垃圾收集](https://github.com)　　　　2\.7、[同步](https://github.com)　　　　　　　　2\.7\.1、[线程池与任务](https://github.com)　　　　　　　　2\.7\.2、[Monitor](https://github.com)　　　　　　　　2\.7\.3、[Barrier](https://github.com)　　　　　　　　2\.7\.4、[CountdownEvent](https://github.com)　　　　　　　　2\.7\.5、[ManualResetEventSlim](https://github.com)　　　　　　　　2\.7\.6、[SemaphoreSlim](https://github.com)　　　　　　　　2\.7\.7、[SpinWait 和 SpinLock](https://github.com)**三、调试源码**　　　　废话不多说，本节是调试的源码部分，没有代码，当然就谈不上测试了，调试必须有载体。　　　　　　　　**3\.1、ExampleCore\_9\_01**


![](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)


```
 1 namespace ExampleCore_9_01
 2 {
 3     internal class Program
 4     {
 5         static void Main(string[] args)
 6         {
 7             Thread thread = new Thread(delegate ()
 8             {
 9                 Console.WriteLine("Hello, World：");
10             });
11             thread.Start();
12 
13             Console.Read();
14         }
15     }
16 }
```


View Code
**四、基础知识**　　　　在这一段内容中，有的小节可能会包含两个部分，分别是 A 和 B，也有可能只包含 A，如果只包含 A 部分，A 字母会省略。A 是【基础知识】，讲解必要的知识点，B 是【眼见为实】，通过调试证明讲解的知识点。　　　　**4\.1、SOS 和 Visual Studio 2022 集成**　　　　　　　　**.Net Framework 版本**　　　　　　　　　　　　SOS 调试扩展（SOS.dll）通过提供有关内部公共语言运行时（CLR）环境的信息，帮助你在 Visual Studio 和 Windows 调试器（WinDbg.exe）中调试托管程序。 此工具需要你启用项目的非托管调试。 SOS.dll 自动随 .NET Framework 一起安装。 若要在 Visual Studio 中使用 SOS.dll，请安装 Windows 驱动程序工具包 (WDK)。　　　　　　　　　　　　若要在 Visual Studio 中使用 SOS 调试扩展，请安装 [Windows 驱动程序工具包 (WDK)](https://github.com):[楚门加速器p](https://tianchuang88.com)。　　　　　　　　　　　　如果想查看更详细的内容，可以点击连接查看：[SOS.dll（SOS 调试扩展）](https://github.com)　　　　　　　　**.Net 版本**　　　　　　　　　　　　如果想了解跨平台版本的调试内容，可以点击这里了解更多详情：[SOS 调试扩展](https://github.com)　　　　　　　　　　　　　　　　　　　　　　　　将 Visual Studio 的强大功能和易用性与SOS对CLR 的解析结合起来，就可以应对各种复杂的调试情况。　　　　　　**4\.2、NET 框架源代码级调试**　　　　　　　　通常，任何 .NET 程序都会用到 .NET 框架中定义的一组不同类型。这些类型既包括简单的数据类型，也包括复杂的 Web 服务绑定类型，它们能有效地屏蔽这些技术的底层复杂性。与任何抽象一样，如果要调试某个有问题的应用程序，那么调试过程也会因此而变得复杂。在分析程序为什么会失败时，如果能够直接对尝试对源代码进行分析，而不是使用逆向工程（即反汇编），那么调试过程会变得更为容易。Microsoft 意识到了这种需求，因此公开了.NET 框架的部分源代码。这些公开的源代码可以集成到 Visual Studio 中，从而使开发人员可以对已发布的 .NET 框架源代码进行源代码级调试。接下来，我们将看到如何配置 Visual Studio 来实现无缝的.NET 框架源代码级调试。　　　　　　　　Net Framework 和 跨平台 Net 框架源码调试还是有一些区别的，我分别给出微软具体的学习页面，大家自行研学吧。　　　　　　　　　　如果是在 .Net Framework 平台下，我们可以点击此链接进行配置，进行框架源码调试：[调试 .NET Framework 源代码](https://github.com)　　　　　　　　　　如果是在跨平台版本下，我们点击以下链接，自行框架源码调试的学习：[使用 Visual Studio 调试 .NET 和 ASP.NET Core 源代码](https://github.com)。　　　　　　　　教程很详细，方法也很简单，就不多说了。　　　　　　　　　**4\.3、 CLR Profiler 分析器**　　　　　　　　CLR 分析器是一种功能非常强大的工具，它能够分析托管堆的行为，并且以各种不同的格式显示出结果。　　　　　　　　官网下载地址：[CLRProfiler](https://github.com)　　　　　　　　如果大家想了解更多、更详细的内容，可以点击连接：[CLR 探查器和 Windows 应用商店应用](https://github.com)　　　　　　　　SOS 与 CLR 分析器　　　　　　　　　　要生成CLR分析器能够理解的文件，可以使用 SOSTraverseHeap 命令。然后，可以将文件加载到 CLR 分析器中从而发挥 CLR 分析器的强大功能。　　　　　　　　**尽管 CLR 分析器是一个很成熟的工具，可以提供关于托管堆的大量信息，但还是存在一些局限：****I、降低程序的执行速度。当在CLR分析器下运行程序时，执行速度会降低10倍到100倍。****II、日志文件的大小。CLR分析器收集了大量的数据保存在本地驱动器上。****III、程序必须由 CLR 分析器启动，而不能将CLR分析器附载到一个已经运行的程序。**　　　　　　　　**4\.3\.1、运行 CLR 分析器**


我们可以从命令行启动 CLR 分析器：执行安装文件夹(在我的情况中是 E:\\Software\\DebugTools\\CLRProfiler45Binaries\\)中 CLRProfiler.exe。请注意，CLR 分析器同时支持 x86 和 x64 两种模式，效果如图：　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240912132703774-598313593.png)　　　　　　　　　　　　因此必须确保启动了正确的模式：




```
1 E:\Software\DebugTools\CLRProfiler45Binaries\64>CLRProfiler.exe
```


　　　　　　　　　　　　或者




```
1 E:\Software\DebugTools\CLRProfiler45Binaries\32>CLRProfiler.exe
```


　　　　　　　　　　　　如果选择错误了架构模式会提示：


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240912135024397-799959107.png)


　　　　　　　　　　　　在 CLR 分析器被启动后，会显示主窗口：


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240912133200523-1435744382.png)


　　　　　　　　　　　　现在，点击【Start Desktop App...】或者【Start Windows Store App...】按钮，会弹出一个对话框，选择要分析的程序。当然，我们也可以通过【Attach Process】按钮附加一个正在运行的进程进行分析。如果我们添加有效的应用程序，程序就会开始运行，并且启用了两个其他的按钮(【Kil Application】和【Show Heap now】)。此时，你可以等待程序运行结束(在这种情况下会自动显示 Summary 视图)，或者程序显示一个用户提示并且等待直到用户按下任意键。


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240912140125480-294617004.png)


　　　　　　　　　　　　需要说明一点，由于我的测试环境是 .Net 8\.0，当我们通过【Start Desktop App...】按钮选择要分析的程序的时候，必须将【Target CLR】设置为【V4 Core CLR】，如图：


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240912134507744-1548515343.png)


　　　　　　　　　　　　如果选择其他选项，就会提示错误：


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240912134648494-876774572.png)


　　　　　　　　　　　　如果我们的程序需要接受命令行参数，可以点击【File】菜单，选择【Set Parameter】，出现一个新窗口：


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240912135711382-1794106127.png)


　　　　　　　　　　　　点击【OK】按钮，返回到主窗口。


　　　　　　　　　　　　现在，你可以使用【View】菜单来选择任意一个视图，以便观察程序运行的详细信息。请注意，某些视图可能直到程序完成执行之后才能显示。此时，进入到CLR分析器启动的命令行，并且按下任意键直到程序执行完成。　　　　　　　　　　　　CLR分析器将所有的分析数据保存在一个日志文件中，位于文件夹C:\\Windows\\Temp下。当然这个路径也是可以修改的，如图：


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240912140703873-1007600378.png)




　　　　　　　　**4\.3\.2、Summary 视图**　　　　　　　　　　　　我们通过命令行工具运行 CLRProfiler ，首先进入 CLRProfiler 所在的目录，在地址栏直接输入【cmd】命令，打开【cmd】命令行工具，如图：


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240913104450979-759690214.png)


　　　　　　　　　　　　命令行工具如图：


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240913104600288-429172206.png)


　　　　　　　　　　　　直接回车，打开【CLR Profiler】工具，效果如图：


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240913104707072-1019195512.png)


　　　　　　　　　　　　由于我的程序需要参数，所以点击【File】\-\-\-\-【Set Parameter】打开参数设置窗口，设置命令行的参数。


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240913105011630-1433704883.png)


　　　　　　　　　　　　点击【OK】返回主界面。现将【Target CLR】设置为【V4 Core CLR】，然后点击【Start Destop App...】选择我们的应用程序。【CLRProfiler 】运行完毕，控制台输出如图：


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240913105623285-265930448.png)


　　　　　　　　　　　　CLR Profiler  在程序执行完成后，CLR 分析器会自动显示分析会话的 Summary 视图，如图所示。


![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240913110406223-1819695503.png)


　　　　　　　　　　　　Summary视图中包含了5组主要的信息。　　　　　　　　　　　　　　**第一组【Heap Statistics】**给出了在分析会话期间堆的总体信息。　　　　　　　　　　　　　　　　Allocated bytes 给出了在托管堆上分配的所有对象的总大小。　　　　　　　　　　　　　　　　Relocated bytes 给出了在垃圾收集期间被移动的对象的总大小。　　　　　　　　　　　　　　　　Final heapbytes给出了当程序退出时托管堆上的所有对象的大小，其其中可能包含了带有终结器的对象，这些对象虽然不存在根对象引用，但还没有被收集。　　　　　　　　　　　　　　　　Objects finalized 给出了有多少对象成功地执行了Finalize 方法。　　　　　　　　　　　　　　　　Critical objects finalized 给出了有多少对象成功地执行了临界 Finalize(Critical Finalize)方法。在这些对象中都包含一个临界 Finalize 方法，它使这些对象比其他普通的对象获得更强的保证。　　　　　　　　　　　　　　**第二组是【Garbage Collection Statistics】**。这部分信息非常简单，显示了在每一代(0\-2\)中发生的垃圾收集次数，并且Induced collection 表示显式执行的垃圾收集操作(例如通过 GC. Collect 方法)次数。　　　　　　　　　　　　　　**第三组是【GC Handle Statistics】**。在跟踪程序中的句柄使用情况时非常有用。它给出了在程序的生命周期中创建的句柄数量，所销毁的句柄数量，以及在程序运行结束时还有多少个句柄存在。　　　　　　　　　　　　　　**第四组是【Garbage Collector Generation Sizes】**这部分给出了在程序运行过程中每一代(包括大对象堆)的平均大小。　　　　　　　　　　　　　　**第五组是【Profiling Statistics】**。这部分还详细给出了分析过程本身的信息，例如在分析过程中生成了多少个堆转储文件，以及被添加到日志文件的注释数量。　　　　　　　　　　　　　　在一些分组框中还包含了相关的按钮。例如，HeapStatistics组在每个城旁边都有一个 Histogram按钮，而 Garbage Collection Statistics 组有一个 Time Line 按钮。这些按钮都可以给出所收集数据的其他一些视图。　　　　　　　　　　　　　　　　　　　　**4\.3\.3、Histogram 视图**　　　　　　　　　　　　这一节只能使用原文中的图片了。　　　　　　　　　　　　我们首先来介绍【Histogram Alocated Types】视图(依次点击 View，Histogram AllocatedTypes 等菜单项)。在如下图中给出了分析过程中得到的视图。


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240913132358492-1524168917.png)


　　　　　　　　　　　　【Histogram Alocated Types】分为四个主要部分。柱状图根据大小给出了在程序中执行的内存分配类别。例如，有39KB个对象是大于16KB但小于32KB的。在分析过程中，最大的柱状图表示小于 64KB 但大于 32KB 的对象。这个类别中的所有对象的总大小为477MB，占据了托管堆中99\.98%使用量。在右侧的面板中，可以通过柱的颜色与对象关联起来。在这里柱为红色，表示一个 System,Byte\[] 类型。最顶端的两个面板可以控制面板的纵坐标轴的最大值和横坐标轴的最大值。　　　　　　　　　　　　通过分析【Histogram Allocated Types】视图，我们可以得出结论，托管堆中包含了大量的 System.Byte\[]。知道哪种类型占据了托管堆中的最大空间是一种非常有用的信息，此外在源代码中找出执行这些分配的位置同样是非常有用的。这可以很容易实现，在需要分析的柱上点击右键，并选择“Show Who Allocated“菜单项。这时出现一个【Allocation Graph】视图，如下图所示。


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240913132309789-278662839.png)


　　　　　　　　　　　　从上图中可以看到，分配图中指出根栈帧 (main入口点) 调用了 Fragmen 类的 Main 方法，这个方法又调用 Run 方法，最终将分配一个System.Byte\[]实例。　　　　　　　　　　　　另一个有用的信息是【Histogramby Age】视图，它将给出对象究竟存活了多长时间。在如下图中给出了一个【Histogram by Age】视图示例。这里，我们可以看到 239MB 的 System.Byte\[] 已经存活了 100 到 150 秒。与其他的直方图视图一样，可以在柱上点击右键，并且选择“Show Who Allocated”菜单项来得到【Allocation Graph】


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240913132219305-1140492173.png)


　　　　　　　　**4\.3\.4、Graph 视图**　　　　　　　　　　　　除了直方图视图外，还有一些视图同样是非常有用的。例如，Heap Graph。Heap Graph 视图给出了托管堆上的所有对象以及它们相应的连接。在图9\-17中给出了一个Heap Graph 视图示例。


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240913132740834-1322603329.png)


　　　　　　　　　　　　从上图中可以看到，System.Byte\[] 占据了239MB的空间，而句柄(类型为Pinned)占据了238MB 的空间。　　　　**4\.4、WinDbg 和 CmdTree 命令**　　　　　　　　**A、基础知识**　　　　　　　　　　　　我们使用了基于控制台版本的非托管调试器，分别是 ntsd 和 cdb。还有另一个非托管调试器 WinDbg，这是一个与控制台调试器对应的 GUI 版本。到目前为止我们讨论的这些命令在这三个调试器中都能使用，但也有些命令只能在 WinDbg 中使用，例如未公开的 cmdtree 命令。这个命令能使大量的键入工作自动化，如果没有它，那么在使用各种调试器命令时需要大量的手工键人工作。cmdtree 命令会显示一个窗口，在这个窗口中包含了一个层次式命令视图，用户可以双击这些命令并且执行。通常，命令是按照区域功能来分组的例如线程、进程、模块等。在这个窗口中显示的命令可以通过编写一个简单的文本文件来定制，但这个文件要符合 cmdtree 命令的语法布局。在编写了这个文本文件后，可以使它通过  cmdtree 命令加载。　　　　　　　　　　　　可以看到，可以很容易对这个 cmdtree 文件进行扩充以包含其他非常有用的命令，这在调试时可以节约大量的时间。因此，我们不需要在命令窗口中键人命令，而是只需双击就可以快速并高效地得到结果。　　　　　　　　**B、眼见为实**　　　　　　　　　　　　调试源码：ExampleCore\_9\_01　　　　　　　　　　　　调试任务：熟悉【.cmdtree】命令的使用。　　　　　　　　　　　　1）、**Windbg Preview 调试**　　　　　　　　　　　　　　　　编译项目，这个项目不需要真正的代码，只是一个载体而已。依次点击【File】\-\-\-\-【Launch executable】，加载我们的 ExampleCore\_9\_01\.exe 可执行文件，并进入调试器。　　　　　　　　　　　　　　　　接下来，我们要编写一个命令文件，扩展名是【.wl】，我的文件名是 SampleCommands.wl，它的内容如下：




```
 1 windbg ANSI Command Tree 1.0
 2 
 3 title {"Common Commands"}
 4 
 5 body
 6 
 7 {"Common Commands"}
 8 
 9  {"Information"}
10 
11   {"Time of dump"} {".time"}
12 
13   {"Process being debugged"} {"|"}
14 
15   {"Dump Location"} {"||"}
16 
17   {"Create server on port 9999"} {".server tcp:port=9999"}
18 
19   {"Show remote connections"} {".clients"}
20 
21   {"Process Environment Block"} {"!peb"}
22 
23  {"Logging"}
24 
25   {"Open Log"} {".logopen /t /u /d"}
26 
27   {"Close Log"} {".logclose"}
28 
29  {"Modules"}
30 
31   {"All Modules"} {"lm D sm"}
32 
33   {"Loaded Modules"} {"lmo D sm"}
34 
35   {"Loaded Modules (verbose)"} {"lmvo D sm"}
36 
37   {"Modules w/o symbols"} {"lme D sm"}
38 
39  {"Stacks"}
40 
41   {"Set frame length to 2000"} {".kframes 2000"}
42 
43   {"Dump current stack w/ DML"} {"kpM 1000"}
44 
45   {"Dump stacks without private info"} {"knL 1000"}
46 
47   {"Dump stacks with all parameters"} {"kPn 1000"}
48 
49   {"Dump stacks (distance from last frame)"} {"kf 1000"}
50 
51   {"Dump stacks with Frame Pointer Omission"} {"kvn 1000"}
52 
53   {"Dump all stack"} {"~*kbn 1000"}
54 
55   {"Dump unique stacks"} {"!uniqstack -pn"}
56 
57   {"Thread environment block"} {"!teb"}
58 
59   {"Move to next frame"} {".f+"}
60 
61   {"Move to previous frame"} {".f-"}
62 
63  {"Memory"}
64 
65   {"Dump heaps"} {"!heap -a"}
66 
67  {"Automated Task"}
68 
69   {"!analyze"} {"!analyze -v"}
70 
71   {"Locks"} {"!ntsdexts.locks"}
72 
73   {"CPU time for User and Kernel Mode"} {"!runaway 7"}
74 
75  {"Managed"}
76 
77   {"Load sos"} {".loadby sos mscorwks"}
78 
79   {"clrstack"} {"!clrstack"}
80 
81   {"Threads"} {"!threads"}
82 
83   {"Stack Objects"} {"!dso"}
84 
85   {"Exceptions"} {"!dae"}
```


　　　　　　　　　　　　　　　　第一行表示文件的头，指出这个文件符合 Command Tree 版本1\.0命令的格式。title 命令表示命令窗口的标题，body 命令表示在实际窗口中显示的内容。在 body 部分中的每一行要么对应于一个树节点(在这种情况下没有指定相应的命令)，要么是一个叶节点(在这种情况下，命令的名字后面紧接着实际的命令，在节点被双击之后由调试器来执行)。　　　　　　　　　　　　　　　　这个文件使用普通的 txt格式就行，最后修改文件扩展名为【.wl】。　　　　　　　　　　　　　　　　SampleCommands.wl 这个文件的目录是 F:\\Test\\CmdTreeSample，完整地址就是：F:\\Test\\CmdTreeSample\\SampleCommands.wl，回到调试器，直接执行【.cmdtree F:\\Test\\CmdTreeSample\\SampleCommands.wl】就可以了。




```
1 windbg> .cmdtree F:\Test\CmdTreeSample\SampleCommands.wl
```


　　　　　　　　　　　　　　　　效果如图：


　　　　　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240914105916835-1468367678.png)


　　　　　　　　　　　　　　　　效果很明显，可以直接双击里面的命令就可以直接执行了。　　　　　　　　　　　　　　　　


　　　　**4\.5、扩展的诊断信息**　　　　　　　　在 CLR4\.0 中，SOS调试器扩展包含了一组新命令，可以进一步帮助我们找出程序中与GC 相关的问题。我们将讨论一些新的命令以及它们的使用方法。　　　　　　　　这里所有的测试源码使用的都是：ExampleCore\_9\_01，这里没有具体的测试过程，直接给出了结果。没有进行【NTSD】调试测试，效果是一样的。　　　　　　　　**4\.5\.1、!Verifyobj** 　　　　　　　　　　　　命令的参数是一个对象地址，它能判断对象是否被破坏了。检测对象是否破坏的算法是，判断在对象及其包含对象中的方法表是否完整。如果怀疑存在一个堆破坏，那么从这个命令的输出中可以很快判断出来。




```
1 0:000> !verifyobj 0x00000239ab009688
2 object 0x239ab009688 is a valid object
```


　　　　　　　　**4\.5\.2、!FindRoots \-gen  \| \-gen any \|** 　　　　　　　　　　　　要找出某个对象还没有被收集的原因，是一个复杂的过程。如果根对象很简单，那么要找出它引用的对象并不困难，但有的时候，要找出对象的根对象引用并不容易。例如，如果在对象中包含了跨代(Cross\-Generational)的引用，并且引用的代还没有被收集，那么这个对象看上去仍然是存活的。当检测到这些跨代的引用时，为了使工作更简单，可以使用FindRoots 命令。　　　　　　　　　　　　FindRoots 命令指示运行时设置一个断点，这个断点可以被设置为在指定的代中下一次发生垃圾收集时(使用 gen  开关)触发，或者每当发生垃圾收集操作时就触发(使用 gen any 开关)。在断点被触发后，FindRoots命令将得到一个对象的地址，命令的执行结果是显示这个对象的根对象。　　　　　　　　**4\.5\.3、!Heapstat \[\-inclUnrooted \| \-iu]**　　　　　　　　　　　　!HeapStat 命令将给出每个托管堆上每一代中已使用字节数和空闲字节数的详细信息。此外，它还会给出小对象堆(SOH)和大对象堆(LOH)上已使用字节数与空闲字节数的比值百分比)。　　　　　　　　　　　　命令的默认输出包含所有存在根对象引用的对象。incIUnrooted(或者简写为iu)开关表示包含所有存在根对象引用的对象以及不存在根对象引用的对象。




```
 3 0:000> !heapstat
 4 Heap     Gen0       Gen1       Gen2       LOH        POH        FRZ       
 5 Heap0    83544      0          0          0          16368     
 6 Total    83544      0          0          0          16368     
 7 
 8 Free space:
 9 Heap     Gen0       Gen1       Gen2       LOH        POH        FRZ       
10 Heap0    96         0          0          0          0          SOH:0%              POH:0%  
11 Total    96         0          0          0          0         
12 
13 Committed space:
14 Heap     Gen0       Gen1       Gen2       LOH        POH        FRZ       
15 Heap0    135168     4096       4096       4096       69632     
16 Total    135168     4096       4096       4096       69632     
```


　　　　　　　　**4\.5\.4、!GCwhere** 　　　　　　　　　　　　以前的文章我们介绍了如何找出某个对象属于第几代的过程。这个过程包括，将托管堆的内存段转储出来(通过eeheap命令)，然后将对象的地址与输出中给出的堆内存段进行比较(在输出中给出了每个堆内存段对应的代)。如果只想找出一个或两个对象的代，那么这个过程或许管用，但如果要找出多个对象的代，那么这个过程将变得非常繁琐。在SOS4\.0中引人了一个命令，它将显示出对象的一些信息。




```
1 0:000> !gcwhere 0x00000239ab009688
2 Address          Heap   Segment          Generation Allocated               Committed               Reserved               
3 0239ab009688     0      0279bc6cf320     0          239ab000028-239ab014680 239ab000000-239ab021000 239ab021000-239ab400000
```


　　　　　　　　　　　　在输出中给出了对象的地址(0x0239ab009688\)，这个对象属于第几代(Generation 0\)，托管堆(0\)，内存段指针(0x0279bc6cf320\)，内存段的起始地址(0x239ab000028\)，在内存段上分配的字节数(239ab000028\-239ab014680\)。


　　　　　　　　**4\.5\.5、!ListNearObj**　　　　　　　　　　　　我们可以使用 【!ListNearObj】命令来验证堆的一致性。这个命令的参数是一个对象地址，将尝试验证在指定对象前后的对象。




```
1 0:000> !ListNearObj 0x00000239ab009688
2 Before:              0239ab009648 64 (0x40)                        System.Threading.ThreadStart
3 Current:             0239ab009688 72 (0x48)                        System.Threading.Thread
4 Next:                0239ab0096d0 64 (0x40)                        System.Threading.Thread+StartHelper
5 Heap local consistency confirmed.
```


　　　　　　　　　　　　输出被分为 before，current 和 after，接下来是验证操作的结果。在 before，current 和 after 等部分中指定了对象的地址，大小以及类型。在前面的示例中，所有这三个对象都被认为是有效的，因此这个命令认为堆的一致性是完整的。


　　　　　　　　**4\.5\.6、!AnalyzeOOM**　　　　　　　　　　　　以前文章介绍了如何分析托管堆来获得关于内存耗尽异常的更多信息。在SOS4\.0中引人了一个新命令 AnalyzeOOM，它能在诊断内存耗尽问题的过程中提供帮助。




```
1 0:000> !AnalyzeOOM
2 There was no managed OOM due to allocations on the GC heap
```


　　　　　　　　　　　　我这里只是一个简单的程序，没有如何有效代码，所以提示这个。　　　　　　　　**4\.5\.7、!RCWCleanupList**　　　　　　　　　　　　在SOS2\.0中可以使用却未被公开的 RCWCleanupList，在SOS4\.0已经被完全公开了。这个命令能显示所有不再被使用并将被清除的运行时可调用封装器。




```
1 0:007> !RCWCleanupList
2 Free-Threaded Interfaces to be released: 0
3 MTA Interfaces to be released: 0
4 STA Interfaces to be released: 0
```


　　　　**4\.6、后台垃圾收集**　　　　　　　　在 CLR4\.0 以前，垃圾收集器有两种不同的工作模式。第一种模式是工作站模式（也叫并发GC），目标是在工作站上运行的程序，例如：图形界面程序，Winform、WPF、MVC 等。第二种模式是服务器模式（也叫阻塞GC），目标通常是不需要任何图形界面的服务器端程序。这两种模式的差异主要在于执行垃圾收集时的响应时间。在前面的文章中已经讨论过，在执行垃圾收集的过程中，执行引擎以及相关的托管线程必须被定期地挂起，以免触发另一次垃圾收集操作。显然，这种托管线程的挂起操作会带来短暂的停止，对使用程序的用户产生一定的影响。在带有图形界面的应用程序中，这可能导致界面闪烁，或者其他细微的现象，例如在用户点击按钮与获得响应之间出现延迟。在这些情况下，重要的是要确保线程处于挂起状态的时间尽可能地少。　　　　　　　　工作站GC（并发 GC）采用的方法是，在GC中将所有托管线程只挂起两次而不是在整个生命周期中都挂起。在托管线程没有被挂起的时间里，它们能够持续分配内存直到到达临时内存段的末尾。如果临时内存段的空间耗尽了，并且有一个工作站 GC 正在执行，那么托管线程会被挂起，直到工作站GC完成（将工作站GC转换为一个服务器GC）。这意味着只要临时内存段没有被耗尽，就可以避免延迟。　　　　　　　　另一方面，服务器 GC 并不用像工作站 GC 那么关心实时响应时间，因为大多数服务器程序并不需要实时响应时间。服务器模式 GC 不允许在 GC 的执行过程中分配内存，而是在 GC 的整个执行过程中挂起所有线程。虽然这可能会在执行 GC 期间导致某种不可见的延迟时间，但好处是获得了更高的吞吐量，因为此时无需担心其他托管线程可能会在同时执行。此外，在服务器 GC 中，每个处理器都有一个专门的GC 线程，因而可以同时运行 X 个 GC(其中X等于处理器的数量乘以每个处理器中核的数量)。　　　　　　　　工作站 GC 的主要缺陷之一是，它通常只在小规模托管堆的程序上运行得较好（记住，只要临时内存段没有被耗尽，那么托管线程将持续分配内存）。在实际情况中，在程序中拥有数 GB 字节托管堆的情况并不多见。在这些情况下仍然存在延迟时间，因为当达到了临时内存段的限制时，工作站 GC（并发模式） 将转变为一个服务器 GC（阻塞模式）。为了解决这种低效问题，CLR 4\.0把并发 GC 替换为后台 GC。　　　　　　　　为了解决这种低效问题，CLR4\.0 把工作站 GC（并发模式） 替换为后台 GC。工作站 GC 与后台 GC 之间的主要差异在于，后台 GC 允许 GC 和内存分配操作同时执行，并且允许收集第0代和第1代的对象。后台GC将定期检查是否有某个并发的内存分配导致了在临时内存段执行GC操作，如果是，将挂起自己，并且允许在临时内存段中的GC执行(前台GC)。因此，虽然执行了一个完整的 GC，但我们仍然能够删除那些短暂存活的对象。由于后台GC允许在第0代和第1代中执行收集操作，那么在达到临时内存段的阈值时将发生什么情况?在这种情况下，前台GC将根据需要增加临时内存段。　　　　　　　　总的来说，在GC执行过程中，服务器GC通常会阻塞。为了避免在这种挂起中造成延迟时间，引入了工作站GC，它能把线程在 GC 过程中处于挂起状态的时间降到最低。虽然这种方法对于托管堆相对较小的程序能够起到作用，但如果临时内存段被耗尽了，那么仍然会造成延迟时间。这种缺点促使工作站 GC 演变为后台 GC，允许内存分配操作和临时内存段的收集操作真正地并发执行(如果需要的话还会扩展内存段)。　　　　　　　　请注意，在CLR4\.0中，后台GC只有在工作站模式中才是有效的。　　　　**4\.7、同步**　　　　　　　　**4\.7\.1、线程池与任务**　　　　　　　　　　　　这里的内容太老旧了，就不做过得介绍了，这一节的内容介绍了线程池的改进和TPL（并行编程），我就把微软官方的连接提出来，大家自行学习吧。　　　　　　　　　　　　如果大家想学习 .Net 高级编程，可以点击：[高级 .NET 编程文档](https://github.com)　　　　　　　　　　　　这里的内容很多，有关于 APM 异步编程的和 TPL 并行编程的内容。　　　　　　　　**4\.7\.2、Monitor**　　　　　　　　　　　　NET4\.0 引人了 Monitor.Enter 方法的一个重载版本：public static void Enter(object obj, ref bool lockTaken)，如果获取了锁，那么 lockTaken 参数就为TRUE，否则为FALSE，经典使用如下代码：




```
 1 internal class Program
 2 {
 3     private static object LockObject = new object();
 4     static void Main(string[] args)
 5     {
 6         bool acquired = false;
 7         try
 8         {
 9             Monitor.Enter(LockObject, ref acquired);
10         }
11         finally
12         {
13             if (acquired)
14             {
15                 Monitor.Exit(LockObject);
16             }
17         }
18         Console.WriteLine("Hello, World!");
19     }
20 }
```


　　　　　　　　　　　　当然，在 .Net 8\.0 版本里，增加其他很多方法。如图：


　　　　　　　　　　　　![](https://img2024.cnblogs.com/blog/1048776/202409/1048776-20240919130848378-1034988340.png)　　　　　　　　　　　　　　　　　　　　**4\.7\.3、System.Threading.Barrier**　　　　　　　　　　　　栅栏对象(System.Threading.Barier)最好被视为一种将一系列操作串行化的方法，在栅栏对象中包含了一个或多个检查点，在这个操作完成之前必须首先达到这些检查点。例如：假定某个任务需要填充一组缓冲区，再使用缓冲区中的数据来执行各种计算。在第二个阶段(计算节点)启动之前，需要首先填充完所有的缓冲区。我们可以创建一个包含X个参与者的栅栏对象，每个参与者在执行完每个阶段(write，read)后必须在栅栏处等待其他的线程执行完毕，然后再一起继续执行。　　　　　　　　　　　　如果想了解更多，可以点击链接：[Barier](https://github.com)　　　　　　　　**4\.7\.4、CountdownEvent**　　　　　　　　　　　　CountdownEvent 类(System.Threading.CountdownEvent)是一个计数事件，只有当它的计数值为0时才会触发。　　　　　　　　　　　　如果想了解更多，可以点击链接：[CountdownEvent](https://github.com)　　　　　　　　**4\.7\.5、ManualResetEventSlim**　　　　　　　　　　　　ManualResetEventSlim类(System.Threading.ManualResetEventSlim)有些类似于现有的ManualResetEvent。二者的关键区别在于，在等待获得某个已经被获取的锁时，新引人的ManualResetEventSlim 原语将首先进行自旋。如果在指定的自旋时间内仍然无法获取这个锁那么它将进入到等待状态(例如在 ManualResetEvent中的情况)。由于在默认情况下并不会直接进入到等待状态，因此就不需要分配包含等待状态的数据结构，这也就是为什么在类的名字中包含了 Slim(意思为轻量级)。注意，与ManualResetEvent类不同的是，Slim 版本只能在跨进程的情况下使用。　　　　　　　　　　　　如果想了解更多，可以点击链接：[ManualResetEventSlim](https://github.com)　　　　　　　　**4\.7\.6、SemaphoreSlim**　　　　　　　　　　　　与前面讨论的 ManualResetEventSlim非常类似，Semaphore 类同样包含了高效的(自旋)版本，叫做 SemaphoreSlim(System.Threading.SemaphoreSlim)。注意，与Semaphore 类不同的是，Slim版本只能在跨进程的情况下使用。　　　　　　　　　　　　如果想了解更多，可以点击链接：[SemaphoreSlim](https://github.com)　　　　　　　　**4\.7\.7、SpinWait 和 SpinLock**　　　　　　　　　　　　当某个锁的平均持有时间很短时，自旋是等待锁被释放的一种更高效方式。主要原因在于，在自旋中不需要分配相应的资源(例如一个事件)来进入等待状态，线程会在自旋过程中查看这个锁是否被释放。自旋的开销要远远小于在实现高效等待时需要的开销。如果只希望使用锁的自旋特性，那么可以使用SpinLock类(System.Threading)，而如果希望在进入等待状态之前自旋一段时间，那么可以使用SpinWait类(System.Threading)。　　　　　　　　　　　　SpinLock 结构是低级互斥同步基元，在等待获取锁时旋转。 在多核计算机上，如果应缩短等待时间且争用最少，那么 SpinLock 的性能优于其他种类的锁。 不过，建议仅在通过分析确定 System.Threading.Monitor 方法或 Interlocked 方法显著降低程序性能时，才使用 SpinLock。　　　　　　　　　　　　System.Threading.SpinWait 是一种轻型同步类型，可用于低级方案，以避免执行内核事件所需的高成本上下文切换和内核转换。　　　　　　　　　　　　如果想了解更多，可以点击链接：[SpinWait](https://github.com) 和 [SpinLock](https://github.com)　　　　　　　　**五、总结**　　　　这个系列终于写完了，时间跨度也不短，其实已是第二次阅读、理解和调试的过程了，我发现每一次都有新的发现，理解的内容也更深刻了。第一遍的时候每个知识点都是零零星星，不连贯，没有办法做到活学活用。第二遍的时候，很多知识点可以连接起来，每步的操作的也知道是为什么了，也就是俗话说的，做到了知其然知其所以然。还是俗话说得好，书读千遍其义自见，我现在更有深刻的领悟这句话的内涵。Net 高级调试这条路，也刚刚起步，还有很多要学的地方。皇天不负有心人，不忘初心，继续努力，做自己喜欢做的，开心就好。



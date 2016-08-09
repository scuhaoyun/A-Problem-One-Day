###1.IOS对象(controller)间的通信方式有哪些？各自的优缺点？
> 对象之间通信方式主要有：直接方法调用，Target-Action,Delegate,回调(block,closure),KVO,Notification。 

 
- delegate的优势：
<br>1. 很严格的语法，所有能响应的时间必须在协议中有清晰的定义。
<br>2. 因为有严格的语法，所以编译器能帮你检查是否实现了所有应该实现的方法，不容易遗忘和出错。
<br>3. 使用delegate的时候，逻辑很清楚，控制流程可跟踪和识别。
<br>4. 在一个controller中可以定义多个协议，每个协议有不同的delegate。
<br>5. 没有第三方要求保持/监视通信过程，所以假如出了问题，那我们可以比较方便的定位错误代码。
<br>6. 能够接受调用的协议方法的返回值，意味着delegate能够提供反馈
信息给controller
<br>delegate的缺点：需要写的代码比较多。
- notification的优势：
<br>1.不需要写多少代码，实现比较简单。
<br>2.一个对象发出的通知，多个对象能进行反应，一对多的方式实现很简单。  
缺点：
<br>1.编译期不会接茬通知是否能被正确处理。
<br>2.释放注册的对象时候，需要在通知中心取消注册。
<br>3.调试的时候，程序的工作以及控制流程难跟踪。
<br>4.需要第三方来管理controller和观察者的联系。
<br>5.controller和观察者需要提前知道通知名称、UserInfo dictionary keys。如果这些没有在工作区间定义，那么会出现不同步的情况。
<br>6.通知发出后，发出通知的对象不能从观察者获得任何反馈。
- KVO是一个对象能观察另一个对象属性的值，前两种模式更适合一个controller和其他的对象进行通信，而KVO适合任何对象监听另一个对象的改变，这是一个对象与
另外一个对象保持同步的一种方法。KVO只能对属性做出反应，不会用来对方法或者动作做出反应。
<br>优点：
<br>1.提供一个简单地方法来实现两个对象的同步。
<br>2.能对非我们创建的对象做出反应。
<br>3.能够提供观察的属性的最新值和先前值。
<br>4.用keypaths来观察属性，因此也可以观察嵌套对象。
<br>缺点：
<br>1.观察的属性必须使用string来定义，因此编译器不会出现警告和检查。
<br>2.对属性的重构将导致观察不可用,需要自己处理superclass的observe事务。
<br>3.所有的observe处理都放在一个方法里,复杂的“if”语句要求对象正在观察多个值，这是因为所有的观察都通过一个方法来指向。
<br>4.多次相同KVO的removeObserve会导致crash
KVO有显著的使用场景，当你希望监视一个属性的时候，我们选用KVO
而notification和delegate有比较相似的用处，
当处理属性层的消息的事件时候，使用KVO，其他的尽量使用delegate，除非代码需要处理的东西确实很简单，那么用通知很方便。
[参考](http://www.jianshu.com/p/d57b055ae5c3)

###2.iOS中有哪些数据持久化的方式，各有什么优点，iOS平台怎么做数据的持久化？CoreData和sqlite有无必然联系？CoreData是一个关系型数据库吗?
- 主要由四种持久化方式：属性列表、对象归档、SQLite数据库、CoreData
- CoreData不是一个数据库，不过可以使用Sqlite数据库来保持数据，也可以使用其他的方式来存储数据，例如：xml
- 属性列表、对象归档适合小数据量存储和查询操作
- Sqlite、CoreData适合大数据量存储和查询操作

###3.UITableviewCell高度计算的优化？
- 对于所有Cell高度固定的UITableView，使用self.tableview.rowHeight = xxx 而不是通过delegate的方式来设置(避免多次调用委托方法)。
- 使用self.tableView.estimatedRowHeight = xxx ，使得Cell负责自身高度的计算。
[参考](http://blog.sunnyxx.com/2015/05/17/cell-height-calculation/)

###4.UITableview优化?
> 性能瓶颈一般来源于Cell高度的计算和Cell的绘制。具体针对性优化(针对复杂的UITableView):


- 提前计算并缓存好高度（布局），因为heightForRowAtIndexPath:是调用最频繁的方法。
- 异步绘制，遇到复杂界面，遇到性能瓶颈时，可能就是突破口。
- 滑动时按需加载，这个在大量图片展示，网络加载的时候很管用！（SDWebImage已经实现异步加载，配合这条性能杠杠的）。
一般优化思路；
- 正确使用reuseIdentifier来重用Cells。
- 对UITableViewCell高度计算优化。
- 尽量使所有的view opaque，包括cell。尽量少用或不用透明图层。
- 如果Cell的内容来自Web，使用异步加载，并缓存结果.
- 减少subviews的数目。
- 尽量少用addView给Cell动态添加View，可以初始化时就添加，通过hide来控制显示。
- 在heightForRowAtIndexPath；中尽量不使用cellForRowAtIndexPath:，如果需要用到，只用一次后缓存结果。
- 手动用代码创建Cell代替用Xib和Stroryboard创建(需要系统转码，增加系统负担[不确定能优化多少])>
[参考](http://www.cocoachina.com/ios/20150602/11968.html)

###5.UIView和CALayer的区别？
- UIView是iOS系统中界面元素的基础，用来管理界面元素，继承UIkit,可以响应事件。而CALayer则是用来绘制界面元素的，它继承自NSObject，不能响应事件。
- UIView的绘制由CALayer负责。UIView主要侧重于对显示元素的管理，而CALayer主要侧重于对显示元素的绘制。
- UIView的layer树形在系统内部，被系统维护着三份copy（这段理解有点吃不准）。
第一份，逻辑树，就是代码里可以操纵的，例如更改layer的属性等等就在这一份。
第二份，动画树，这是一个中间层，系统正在这一层上更改属性，进行各种渲染操作。
第三份，显示树，这棵树的内容是当前正被显示在屏幕上的内容。
这三棵树的逻辑结构都是一样的，区别只有各自的属性。
- CALayer的坐标系系统和UIView有点不一样，它多了一个叫anchorPoint的属性。
[参考1](http://www.cocoachina.com/ios/20150828/13244.html) [参考2](http://www.cnblogs.com/pengyingh/articles/2381673.html)

###6.UIView的layoutsubviews的调用时机？
- 当view的bounds发生改变时。
- 当view的直接subView的bounds发生改变时。
- 当subView添加或移除时。
- 调用setNeedsLayout方法会在下一个显示周期主动调用layoutsubviews。

###7.什么是RunLoop?RunLoop和多线程的关系？
- RunLoop 实际上就是一个对象，这个对象管理了其需要处理的事件和消息，并提供了一个入口函数来执行 Event Loop的逻辑。线程执行了这个函数后，
就会一直处于这个函数内部 "接受消息->等待->处理" 的循环中，直到这个循环结束（比如传入quit的消息），函数返回。 ﻿
- runloop是运动循环,不断跑圈,无限循环。Runloop的功能就像他的名字听起来一样，使线程进入一个循环，当事件到达的时候调用事件处理方法。我们
要自己提供实现Runloop实际循环运行的控制语句，话句话说就是我们要提供while或者for来驱动Runloop。在循环内部我们运行Runloop，当事件到达的时
候调用事件处理器。
- 作用:
﻿保持程序的持续运行 (iOS程序一直活着的原因)
﻿处理App中的各种事件(eg:触摸事件/定时器事件/selector事件【选择器·performSelector···】)
节省CPU资源,提高程序的性能(有事做事,没事休息)
程序已启动,就开启了一个runloop无限循环,因此程序才能持续的运行
在Cocoa中，每个线程(NSThread)对象中内部都有一个run loop（NSRunLoop）对象用来循环处理输入事件，处理的事件包括两类，一是来自
Input sources的异步事件，一是来自Timer sources的同步事件;run Loop在处理输入事件时会产生通知，可以通过Core Foundation向线程中添加
run-loop observers来监听特定事件,以在监听的事件发生时做附加的处理工作。
- Runloop与线程的关系
线程和 RunLoop 之间是一一对应的，其关系是保存在一个全局的 Dictionary 里。线程刚创建时并没有 RunLoop，如果你不主动获取，那它一直都不会有。
RunLoop 的创建是发生在第一次获取时，RunLoop 的销毁是发生在线程结束时。你只能在一个线程的内部获取其 RunLoop（主线程除外）。
- Runloop相关的类
CFRunloopRef
CFRunloopModeRef【Runloop的运行模式】
CFRunloopSourceRef【Runloop要处理的事件源】
CFRunloopTimerRef【Timer事件】
CFRunloopObserverRef【Runloop的观察者（监听者）】
一个 RunLoop 包含若干个 Mode，每个 Mode 又包含若干个 Source/Timer/Observer。每次调用 RunLoop 的主函数时，只能指定其中一个 Mode，这
个Mode被称作 CurrentMode。如果需要切换 Mode，只能退出 Loop，再重新指定一个 Mode进入。这样做主要是为了分隔开不同组的 Source/Timer/Observer，
让其互不影响。Runloop要想跑起来,它的内部必须要有一个mode,mode中必须有source/observer/time,至少要有其中的一个。
[参考1](http://blog.ibireme.com/2015/05/18/runloop/)  [参考2](http://www.cocoachina.com/ios/20160612/16631.html)

###8.MRC和ARC对比?
- MRC 的计数器机制改善了内存管理的方式，减少了各个模块的逻辑耦合，释放了程序员对“何时该释放”的心理压力，解决了大部分的问题。
ARC 的初衷是为了让程序员写代码的时候更加便利，最好不用再关注任何内存释放的问题（也不用关注用什么方式初始化的问题）,系统在运行时统一管理所有内存对象
的释放，会导致增加额外的内存和 CPU 开销，在硬件设备尚且处于低级阶段的时候，当程序员们依然在努力降低内存降低 CPU 消耗的时候，推出这样的机制，是不合
时宜的！

###9.什么情况下用weak关键字？
- 在ARC中，在有可能出现循环引用的时候，往往要通过让其中一端使用weak来解决，比如delegate。
- 自身已经对它进行一次强引用，没有必要再强引用一次，此时也使用weak，自定义IBOutlet连出来的控件属性一般也使用weak，当然他也可以用Strong[原因：
使用storyboard创建的viewController，那么会有一个叫 _topLevelObjectsToKeepAliveFromStoryboard的私有数组强引用所有top level的对象，
同时top level对象强引用所有子对象，那么vc没必要再强引用top level对象的子对象，IBOutlet的属性一般可以设为weak是因为它已经被view引用了，除非
view被释放，否则IBOutlet的属性也不会被释放，另外IBOutlet属性的生命周期和view应该是一致的，所以IBOutlet属性一般设为weak]。

###10. BAD_ACCESS在什么情况下出现？ 
- 访问了野指针，比如对一个已经释放的对象执行了release、访问已经释放对象的成员变量或者发消息。死循环

###11.weak和unowned的区别和联系?
- 几时用weak,几时用unowned呢？
举Notification的例子来说,如果Self在闭包被调用的时候有可能是Nil,则使用weak.如果Self在闭包被调用的时候永远不会是Nil，则使用unowned。
- 使用unowned有什么坏处呢？
如果我们没有确定好Self在闭包里调用的时候不会是Nil就使用了unowned。当闭包调用的时候,访问到声明为unowned的Self时。程序就会奔溃。这类似于访问
了悬挂指针（进一步了解，请阅读Crash in Cocoa）。对于熟悉Objective-C的大家来说,unowned在这里就类似于OC的unsafe_unretained。在对象被清
除后,声明为weak的对象会置为nil,而声明为unowned的对象则不会。
- 那么既然unowned可能会导致崩溃,为什么我们不全部都用weak来声明呢？
原因是使用unowned声明,我们能直接访问。而用weak声明的,我们需要unwarp后才能使用。并且直接访问在速度上也更快。（这位国外的猿说:Unowned is 
faster and allows for immutability and nonoptionality. If you don't need weak, don't use it.），其实说到底,unowned的引入
是因为Swift的Optional机制。因此我们可以根据实际情况来选择使用weak还是unowned。个人建议,如果无法确定声明对象在闭包调用的时候永远不会是nil,还
是使用weak来声明。安全更重要。

###12.Swift和Objective-C对比?
> Apple 在推出 Swift 时就将其冠以先进，安全和高效的新一代编程语言之名。



1. 先进:尾随闭包,枚举关联值,可选值,元组......
2. 安全:强制的类型安全,类型推断,强类型…
3. 高效:
<br>（1）相较于其前辈的 Objective-C，Swift 在编译期间就完成了方法的绑定，因此方法调用上不再是类似于 Smalltalk 的消息发送，而是直接获取方法地址并
进行调用。虽然 Objective-C 对运行时查找方法的过程进行了缓存和大量的优化，但是不可否认 Swift 的调用方式会更加迅速和高效。
<br>（2）与 Objective-C 不同，Swift 是一门强类型的语言，这意味 Swift 的运行时和代码编译期间的类型是一致的，这样编译器可以得到足够的信息来在生成中间
码和机器码时进行优化。虽然都使用 LLVM 工具链进行编译，但是 Swift 的编译过程相比于 Objective-C 要多一个环节 -- 生成 Swift 中间代码 
(Swift Intermediate Language，SIL)。SIL 中包含有很多根据类型假定的转换，这为之后进一步在更低层级优化提供了良好的基础，分析 SIL 也是我们探索
Swift 性能的有效方法。
<br>（3）Swift 具有良好的内存使用的策略和结构。Swift 标准库中绝大部分类型都是 struct，对值类型的使用范围之广，在近期的编程语言中可谓首屈一指。原本值类
型不可变性的特点，往往导致对于值的使用和修改意味着创建新的对象，但是 Swift 巧妙地规避了不必要的值类型复制，而仅只在必要时进行内存分配。这使得 Swift 
在享受不可变性带来的便利以及避免不必要的共享状态的同时，还能够保持性能上的优秀。
<br>[参考](https://onevcat.com/2016/02/swift-performance/)

###13.GCD是什么？
- 为了让开发者更加容易的使用设备上的多核CPU，苹果在 OS X 10.6 和 iOS 4 中引入了 Grand Central Dispatch（GCD）。
- 通过 GCD，开发者不用再直接跟线程打交道了，只需要向队列中添加代码块即可，GCD 在后端管理着一个线程池。GCD 不仅决定着你的代码块将在哪个线程被执行，
它还根据可用的系统资源对这些线程进行管理。这样可以将开发者从线程管理的工作中解放出来，通过集中的管理线程，来缓解大量线程被创建的问题。
- GCD 带来的另一个重要改变是，作为开发者可以将工作考虑为一个队列，而不是一堆线程，这种并行的抽象模型更容易掌握和使用。
- GCD 公开有 5 个不同的队列：运行在主线程中的 main queue，3 个不同优先级的后台队列，以及一个优先级更低的后台队列（用于 I/O）。 另外，开发者可以
创建自定义队列：串行或者并行队列。自定义队列非常强大，在自定义队列中被调度的所有 block 最终都将被放入到系统的全局队列中和线程池中。使用不同优先级的若干
个队列乍听起来非常直接，不过，我们强烈建议，在绝大多数情况下使用默认的优先级队列就可以了。如果执行的任务需要访问一些共享的资源，那么在不同优先级的队列中调
度这些任务很快就会造成不可预期的行为。这样可能会引起程序的完全挂起，因为低优先级的任务阻塞了高优先级任务，使它不能被执行。
- 虽然 GCD 是一个低层级的 C API ，但是它使用起来非常的直接。不过这也容易使开发者忘记并发编程中的许多注意事项和陷阱。

###14.什么是响应链？
- 在我们点击屏幕的时候，iphone OS获取到了用户进行了“单击”这一行为，操作系统把包含这些点击事件的信息包装成UITouch和UIEvent形式的实例，然后找到当前运行的程序，
逐级寻找能够响应这个事件的对象，直到没有响应者响应。这一寻找的过程，被称作事件的响应链，不同的响应者以链式的方式寻找。
- 响应查找的顺序是： UIStatusBar相关的视图-> UIWindow -> UIView -> AView -> DView -> BView（系统在事件链传递的过程中一定会遍历所有的子视图判断是否
能够响应点击事件）。可以推测出UIApplication对象维护着自己的一个响应者栈，当pointInSide: withEvent:返回yes的时候，响应者入栈。栈顶的响应者作为最优先处
理事件的对象，假设AView不处理事件，那么出栈，移交给UIView，以此下去，直到事件得到了处理或者到达AppDelegate后依旧未响应，事件被摒弃为止。通过这个机制我们也
可以看到controller是响应者栈中的例外，即便没有pointInSide: withEvent:的方法返回可响应，controller依旧能够入栈成为UIView的下一个响应者。
[参考](http://www.cocoachina.com/ios/20160113/14896.html)

###15.Swift中static和class的区别?
> Swift中Swift中表示“类型范围作用域”的两个关键字为static和class。


- 在非class的类型上下文中，我们统一使用static来描述类型作用域。这包括在enum和struct中表述类型方法和类型属性时。在这两个值类型中，我们可以在类型范围内声明并使
用存储属性，计算属性和方法。
- class关键字相比起来就明白许多，是专门用在class类型的上下文中的，可以用来修饰类方法以及类的计算属性。要特别注意class中现在是不能出现存储属性的。Apple表示今后
将会考虑在某个升级版本中实装class类型的类存储变量，现在的话，我们只能在class中用class关键字声明方法和计算属性。
- 有一个比较特殊的是protocol。在Swift中class、struct和enum都是可以实现protocol的。那么如果我们想在protocol里定义一个类型域上的方法或者计算属性的话，应该
用哪个关键字呢？答案是使用class进行定义，但是在实现时还是按照上面的规则：在class里使用class关键字，而在struct或enum中仍然使用static——虽然在protocol中定
义时使用的是class。

###16.IOS中的多线程?各自的优缺点?
> IOS中一般用到的多线程技术有三种：NSThread,NSOperation,GCD（还有一个Pthreads,POSIX线程的简称，是基于c语言的框架，很底层，一般不用）


1. NSThread:
<br>(1) 使用NSThread对象建立一个线程非常方便;可以知道当前线程的各种属性，用于调试十分方便;轻量级;
<br>(2) 但是!需要自己管理thread的生命周期，线程之间的同步,线程同步对数据的加锁会有一定的系统开销。要使用NSThread管理多个线程非常困难,不推荐使用;
<br>(3) 技巧!使用[NSThread currentThread]跟踪任务所在线程,适用于这三种技术.
2. GCD---Grand Central Dispatch:
<br>(1) 通过GCD,开发者不用再直接跟线程打交道,只需要向队列中添加代码块即可. GCD自动管理线程的生命周期（创建线程、调度任务、销毁线程）; 是基于C语言的底层API
<br>(2) GCD在后端管理着一个线程池,GCD不仅决定着代码块将在哪个线程被执行,它还根据可用的系统资源对这些线程进行管理,从而让开发者从线程管理的工作中解放出来;通过集中
的管理线程,缓解大量线程被创建的问题.
<br>(3) 使用GCD,开发者可以将工作考虑为一个队列,而不是一堆线程,这种并行的抽象模型更容易掌握和使用.
<br>(4) 项目中使用GCD的优点是GCD本身非常简单、易用，对于不复杂的多线程操作，会节省代码量，而Block参数的使用，会是代码更为易读，建议在简单项目中使用。
   
3. NSOperation/NSOperationQueue:
<br>(1) 是使用GCD实现的一套Objective-C的API;NSOperation是对线程的高度抽象
<br>(2) 是面向对象的多线程技术;只要聚焦于我们需要做的事情，而不必太操心线程的管理，同步等事情.
<br>(3) 提供了一些在GCD中不容易实现的特性,如:限制最大并发数量,操作之间的依赖关系.
<br>[参考1](http://www.jianshu.com/p/0b0d9b1f1f19) [参考2](http://my.oschina.net/aofe/blog/270093)

###17.什么是ARC（ARC是为了解决什么问题诞生的）？
- ARC是Auto Reference Counting的缩写，即自动引用计数，由编译器在代码合适的位置中自动添加retain/Release/Autorelease/dealloc方法从而进行内存管理.
ARC几个要点：在对象被创建时 retain count +1，在对象被release时 retain count -1.当retain count 为0 时，销毁对象。程序中加入autoreleasepool的对象会由系统自动加上autorelease方法，如果该对象引用计数为0，则销毁。那么ARC是为了解决什么问题诞生的呢？这个得追溯到MRC手动内存管理时代说起。
- MRC下内存管理的缺点：
当我们要释放一个堆内存时，首先要确定指向这个堆空间的指针都被release了。（避免提前释放）
释放指针指向的堆空间，首先要确定哪些指针指向同一个堆，这些指针只能释放一次。（MRC下即谁创建，谁释放，避免重复释放）
模块化操作时，对象可能被多个模块创建和使用，不能确定最后由谁去释放。
多线程操作时，不确定哪个线程最后使用完毕
[参考](http://www.jianshu.com/p/262c1f8b7461)

###18.为什么其他语言里叫函数调用，Object-C里则叫给我对象发消息(或者谈下对runtime的理解)?
> Runtime系统是一个C语言动态库，在OC中，调用方法: [receiver message] 在编译器里会转换成消息机制里的消息发送形式:objc_msgSend(receiver, selector) 如果带参数的话： objc_msgSend(receiver, selector, arg1, arg2, ...) 消息功能为动态绑定做了很多有必要的工作。

- 在很多编程语言中，类和方法在编译期就绑定在一起，而在OC中，方法调用是向类发送消息,如（bady cry）在运行时会转换成objc_msgSend(bady,cry),向对象发送消息时根据isa指针
找到类，在根据类的调度表查找方法，没找到方法则在父类中查找直至基类，如果始终没有找到返回nil。Objc Runtime使得C具有了面向对象能力，在程序运行时创建，检查，修改类、对象
和它们的方法。可以使用runtime的一系列方法实现。
- Objective-C是基于C语言加入了面向对象特性和消息转发机制的动态语言，这意味着它不仅需要一个编译器，还需要Runtime系统来动态创建类和对象，进行消息发送和转发。在Objective-C
中，使用[receiver message]语法并不会马上执行receiver对象的message方法的代码，而是向receiver发送一条message消息，这条消息可能由receiver来处理，也可能由转发给其他
对象来处理，也有可能假装没有接收到这条消息而没有处理。其实[receiver message]被编译器转化为：id objc_msgSend ( id self, SEL op, ... );  
[参考](http://www.jianshu.com/p/262c1f8b7461)

###19.什么是Method Swizzling？AOP?
- Method Swizzling利用 Runtime 特性把一个方法的实现与另一个方法的实现进行替换。每个类里都有一个 Dispatch Table ，将方法的名字（SEL）跟方法的实现（IMP，指向 C 函数的指针）一一对应。Swizzle 一个方法其实就是在程序运行时在 Dispatch Table 里做点改动，让这个方法的名字（SEL）对应到另个 IMP 。
- Aspect Oriented Programming （面向切面编程）：在 Objective-C 的世界里，这句话意思就是利用 Runtime 特性给指定的方法添加自定义代码。有很多方式可以实现 AOP ，Method Swizzling 就是其中之一。而且幸运的是，目前已经有一些第三方库可以让你不需要了解 Runtime ，就能直接开始使用 AOP 。
- 利用 objective-C Runtime 特性和 Aspect Oriented Programming ，我们可以把琐碎事务的逻辑从主逻辑中分离出来，作为单独的模块。它是对面向对象编程模式的一个补充。


###20. 使用drawRect有什么影响？
重写drawRect可能会导致内存大量上涨，性能不稳定。CALayer其实也只是iOS当中一个普通的类，它也并不能直接渲染到屏幕上，因为屏幕上你所看到的东西，其实都是一张张图片。而为什么我们能看到CALayer的内容呢，是因为CALayer内部有一个contents属性。contents默认可以传一个id类型的对象，但是只有你传CGImage的时候，它才能够正常显示在屏幕上。所以最终我们的图形渲染落点落在contents身上。contents也被称为寄宿图，除了给它赋值CGImage之外，我们也可以直接对它进行绘制，通过继承UIView并实现-drawRect:方法即可自定义绘制。-drawRect: 方法没有默认的实现，因为对UIView来说，寄宿图并不是必须的，UIView不关心绘制的内容。如果UIView检测到-drawRect:方法被调用了，它就会为视图分配一个寄宿图，这个寄宿图的像素尺寸等于视图大小乘以contentsScale(这个属性与屏幕分辨率有关。画板视图的-drawRect:方法的背后实际上都是底层的CALayer进行了重绘和保存中间产生的图片，CALayer的delegate属性默认实现了CALayerDelegate协议，当它需要内容信息的时候会调用协议中的方法来拿。当画板视图重绘时，因为它的支持图层CALayer的代理就是画板视图本身，所以支持图层会请求画板视图给它一个寄宿图来显示，它此刻会调用：

`(void)displayLayer:(CALayer *)layer;`

如果画板视图实现了这个方法，就可以拿到layer来直接设置contents寄宿图，如果这个方法没有实现，支持图层CALayer会尝试调用：

`- (void)drawLayer:(CALayer *)layer inContext:(CGContextRef)ctx;`

这个方法调用之前，CALayer创建了一个合适尺寸的空寄宿图（尺寸由bounds和contentsScale决定）和一个Core Graphics的绘制上下文环境，为绘制寄宿图做准备，它作为ctx参数传入。在这一步生成的空寄宿图内存是相当巨大的，它就是本次内存问题的关键，一旦你实现了CALayerDelegate协议中的-drawLayer:inContext:方法或者UIView中的-drawRect:方法（其实就是前者的包装方法），图层就创建了一个绘制上下文，这个上下文需要的内存可从这个公式得出：图层宽*图层高*4字节，宽高的单位均为像素。
[drawRect & 内存 -> 深究](http://blog.csdn.net/sandyloo/article/details/51063799)

###21.沙盒目录结构是怎样的？各自用于那些场景？

- Application//存放程序源文件，上架前经过数字签名，上架后不可修改
- Documents//常用目录，iCloud备份目录，存放数据
- Library
   - Caches//存放体积大又不需要备份的数据
   - Preference//设置目录，iCloud会备份设置信息
- tmp//存放临时文件

###22.autorelease嵌套，系统怎么处理的?
autorelease与release相似，是OC中的一个对象方法。这两个方法都能把对象的引用计数器减1，但是release是一个精确的减1，对对象的操作只能在release之前进行，如果是在之后，就会出现野指针错误；而autorelease是一个不精确的引用计数器减1，当给对象发送autorelease消息时，对象就会被放到自动释放池中，自动销毁时会给池中的所有对象发送release消息，使得所有对象的计数器减1，所以本质上autorelease还是会调用release。

autoreleasepool是以栈结构存储的,先进后出,只有栈顶的Pool才处于活动状态,才可以装对象.并且因为是栈结构,所以销毁,是先销毁栈顶的pool,最后是栈底.

参考[http://www.cnblogs.com/CoderAlex/p/5232357.html](http://www.cnblogs.com/CoderAlex/p/5232357.html)

###23.线程安全?
- OC在定义属性时有nonatomic和atomic两种选择：<br>
atomic：原子属性，为setter方法加锁（默认就是atomic）,线程安全，需要消耗大量的资源<br>
nonatomic：非原子属性，不会为setter方法加锁，非线程安全，适合内存小的设备
- 使用互斥锁，@synchronized(锁对象) { // 需要锁定的代码  }<br>
 优点：能有效防止因多线程抢夺资源造成的数据安全问题<br>
 缺点：需要消耗大量的CPU资源<br>
 互斥锁的使用前提：多条线程抢夺同一块资源
- “Object－C”语言<br>
1）NSLock（互斥锁）<br>
2） NSRecursiveLock（递归锁）:条件锁，递归或循环方法时使用此方法实现锁，可避免死锁等问题。<br>
3） NSConditionLock（条件锁）:使用此方法可以指定，只有满足条件的时候才可以解锁。<br>
4）NSDistributedLock（分布式锁）:在IOS中不需要用到，也没有这个方法，因此本文不作介绍，这里写出来只是想让大家知道有这个锁存在。<br>
- C 语言<br>
1） pthread_mutex_t（互斥锁）<br>
2） GCD－信号量（“互斥锁”）<br>
3） pthread_cond_t（条件锁）<br> 

###24.UIView的生命周期？
init、loadView、viewDidLoad、viewDidUnload、dealloc<br>

1. init方法<br>
在init方法中实例化必要的对象（遵从LazyLoad思想）,init方法中初始化ViewController本身
 
2. loadView方法<br>
 当view需要被展示而它却是nil时，viewController会调用该方法。不要直接调用该方法。如果手工维护views，必须重载重写该方法,如果使用IB维护views，必须不能重载重写该方法,loadView和IB构建view
 
3. viewDidLoad方法<br>
 重载重写该方法以进一步定制view
4. viewDidUnload方法<br>
内存吃紧时，在iPhone OS 3.0之前didReceiveMemoryWarning是释放无用内存的唯一方式，但是OS 3.0及以后viewDidUnload方法是更好的方式,viewDidLoad总是在loadView之后调用，不管你是不是通过nib文件创建的，这个方法总是会被调用的。viewDidUnload在收到内存警告的时候调用，在我的理解，这个方法里面应该做几件事情：<br>
（1）、释放掉一些比较容易创建的对象，或者是一些比较占资源的对象（图片、音频等）<br>
（2）、如果界面控件自己保持了引用计数，这里也要释放掉。（比如说，这个控件被设成了属性，而且是retain的，这个retain的引用计数就必须释放掉）<br>
（3）、如果跨类的参数传递机制会在viewDidUnload以后产生不正常的效果，这里也必须处理。<br>
 
5. dealloc方法
dealloc负责realease 所有的 retain，copy形式的@proprety属性，而相应的本地临时变量，则全部在viewDidUnload中进行realease
viewDidUnload将所有的局部变量和 retain，copy形式的@property先置为nil，后realease，

###25.UIViewController的生命周期中各个方法执行流程？
init—>loadView—>viewDidLoad—>viewWillApper—>viewDidApper—>viewWillDisapper—>viewDidDisapper—>viewWillUnload->viewDidUnload—>dealloc

###26.ios编译的过程？比如加载文件的顺序？
1. 编译各个依赖的子target<br>
  预编译头文件<br>
  编译.m和.c文件<br>
  根据目标文件创建出一个库<br>
  将产生的两个对应不同的架构的.a文件合并为一个通用的二进制文件
2. 构建本程序的target<br>
  PhaseScriptExecution ...<br>
DataModelVersionCompile ...<br>
Ld ...<br>
GenerateDSYMFile ...<br>
CopyStringsFile ...<br>
CpResource ...<br>
CopyPNGFile ...<br>
CompileAssetCatalog ...<br>
ProcessInfoPlistFile ...<br>
ProcessProductPackaging /.../some-hash.mobileprovision ...<br>
ProcessProductPackaging objcio/objcio.entitlements ...<br>
CodeSign ...<br>
[参考:http://www.tuicool.com/articles/uUzyyay](http://www.tuicool.com/articles/uUzyyay)

###27.iOS性能优化：Instruments使用实战？
![Alt text](http://cc.cocimg.com/api/uploads/20150215/1423964323709546.png)
[参考:http://www.cocoachina.com/ios/20150225/11163.html]


AMS对于Task的管理方式发生变化，对于Stack的管理方式同样发生变化。目前看来最主要变化是对Stack产生和应用方式的变化。
系统中对Activity进行了划分，主要分为以下几类：
    
    /** The current activity type of the configuration. */
    private @ActivityType int mActivityType;

    /** Activity type is currently not defined. */
    public static final int ACTIVITY_TYPE_UNDEFINED = 0;
    /** Standard activity type. Nothing special about the activity... */
    public static final int ACTIVITY_TYPE_STANDARD = 1;
    /** Home/Launcher activity type. */
    public static final int ACTIVITY_TYPE_HOME = 2;
    /** Recents/Overview activity type. There is only one activity with this type in the system. */
    public static final int ACTIVITY_TYPE_RECENTS = 3;
    /** Assistant activity type. */
    public static final int ACTIVITY_TYPE_ASSISTANT = 4;

同时将显示方式也作了区分，并定义为WindowMode，区分方式如下：
    
    /** The current windowing mode of the configuration. */
    private @WindowingMode int mWindowingMode;

    /** Windowing mode is currently not defined. */
    public static final int WINDOWING_MODE_UNDEFINED = 0;
    /** Occupies the full area of the screen or the parent container. */
    public static final int WINDOWING_MODE_FULLSCREEN = 1;
    /** Always on-top (always visible). of other siblings in its parent container. */
    public static final int WINDOWING_MODE_PINNED = 2;
    /** The primary container driving the screen to be in split-screen mode. */
    public static final int WINDOWING_MODE_SPLIT_SCREEN_PRIMARY = 3;
    /**
     * The containers adjacent to the {@link #WINDOWING_MODE_SPLIT_SCREEN_PRIMARY} container in
     * split-screen mode.
     * NOTE: Containers launched with the windowing mode with APIs like
     * {@link ActivityOptions#setLaunchWindowingMode(int)} will be launched in
     * {@link #WINDOWING_MODE_FULLSCREEN} if the display isn't currently in split-screen windowing
     * mode
     * @see #WINDOWING_MODE_FULLSCREEN_OR_SPLIT_SCREEN_SECONDARY
     */
    public static final int WINDOWING_MODE_SPLIT_SCREEN_SECONDARY = 4;
    /**
     * Alias for {@link #WINDOWING_MODE_SPLIT_SCREEN_SECONDARY} that makes it clear that the usage
     * points for APIs like {@link ActivityOptions#setLaunchWindowingMode(int)} that the container
     * will launch into fullscreen or split-screen secondary depending on if the device is currently
     * in fullscreen mode or split-screen mode.
     */
    public static final int WINDOWING_MODE_FULLSCREEN_OR_SPLIT_SCREEN_SECONDARY =
            WINDOWING_MODE_SPLIT_SCREEN_SECONDARY;
    /** Can be freely resized within its parent container. */
    public static final int WINDOWING_MODE_FREEFORM = 5;

当应用启动时，若intent没有指定stack的启动方式，则会判断是否可以在当前处于Focus的Stack中启动，并根据解决进行Stack选择。

更多的变化在于对ActivityStack所处显示情况的判断。之前只要根据StackId便可知道该Activity所处的ActivityStack，从而获知其正在使用的显示方式。在Android9.0中引入了ActivityDisplay.java这一文件，简单分析得出该文件记录的是当前屏幕上的Activity是通过什么样的方式显示的。个人感觉可以粗略地将其看为Display这个概念在AMS端的实体类。从其成员变量来看也不难看出ActivityDisplay是比ActivityStack层次更高的一个容器。从这点来看，随着显示方式的多样性，google也认为有必要在AMS端增加一个对当前屏幕信息的获取途径，用来更好地支持AMS工作，同时为更进一步适配多屏幕进行准备。

在Openthos8.1中，在AMS端主要完成的功能可以分位两类，辅助窗口操作和辅助SystemUI。辅助窗口操作方面根据之前的分析结果，从DecorCaptionView到AMS的通路仍然是将Activity作为Window.WindowControllerCallback进行调用来完成，所以将8.1的修改应用到9.0上只需要对AMS中的逻辑和手段进行重构。比如exitFreeForm方法，原先要在原有存在的Freeform到FullScreen的通路的基础上，提供出从FullScreen到FreeForm的通路，这里在8.1使用的task.reparent()方法完成，目前则要使用Stack.setWindowMode来完成。类似这些修改难度应该不是很大。至于辅助SystemUI方面的修改，对于Openthos8.1的SystemUI来说，实现方式本就更独立于原本的StatusBar，虽然尚未深度分析，但个人认为这部分内容的重用工作量不那么大的可能性比较高。

相比于AMS端对SystemUI的支持，SystemUI本身需要改动的内容可能更多一些，由于原来SystemUI是通过匹配8.1提供的启动方式参数来达到窗口化启动应用效果的，目前9.0的启动参数和8.1不同了，SystemUI方面也许需要完整地分析9.0的启动方式才能够作出和原来同等有效的启动手段。不过根据我个人对SystemUI中StartupMenu代码的分析，这部分内容之前的同事作为甚少，也存在很多瑕疵。同时本身复杂性一般，若时间允许，在9.0上经过更好的设计来重新实现也是一个可选项。

相对于AMS，TaskPositioner.java变化较大这一点对于原本的缩放实现可能比较有影响。由于但从代码上很难看出究竟是进行了何种变化，因此目前所有设计TaskPositioner的修改都需要在实际看一下android9.0的多窗口究竟变化了那些肉眼可见的内容之后再进一步分析。

在只对部分代码进行了简单分析的基础上，个人认为将openthos8.1中有关多窗口和SystemUI的修改应用于android9.0的难度并没有很高，但相比于从7.1移到8.0要有很大的变化。至少不是改改API就能够完成的，需要去根据9.0的新的窗口管理逻辑来重新实现整体框架。若时间允许且测试手段稳定，着手将这些内容移植到9.0上并不是不可能。
至于之前提到的具体工作量的时间量化问题，我认为多窗口部分仍然需要将android9.0真的跑起来，根据运行效果有针对性地进一步分析代码来确定哪些内容变化较大，哪些地方需要重新分析修改，进而掌握一个大体的工作量。SystemUI部分则需要对功能需求和实现方法进行考察并对现有的不成熟的SystemUI内容进行分析，决定是在现有基础上完善还是重新实现之后，评估工作量。

以上是本日对android9.0源代码分析后得出的有关多窗口和SystemUI方面在openthos8.1上实现的工作想要移植到android9.0上的难点和工作量评估的分析及预想。欢迎大家多多交流。谢谢。

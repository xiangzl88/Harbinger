## 起点

### 开发环境部署
其实部署一个基于 MinecraftForge 的 Mod 的开发环境还是很简单的。比以前不知道简单到哪里去了。

 1. 首先下载 Mod Development Kit。打开 [Forge的下载页面](http://files.minecraftforge.net/)，其中那个写着 Mdk 的链接便是。
 2. 下载得到 zip，解压。务必要能看见诸如`build.gradle`、`gradlew`、`gradlew.bat`、`.gitignore`等文件。
 3. 根据你的具体情况不同，在命令行中，切换到 Mdk 的目录，然后：
   * 如果你没有 Gradle，或者你不知道什么是 Gradle：
     - Windows：`gradlew.bat setupDecompWorkspace`
     - 某种 Linux 发行版，或 macOS：`./gradlew setupDecompWorkspace`
     - 其他系统：很抱歉，Minecraft 所依赖的 LWJGL 只有针对 Windows、macOS 和 Linux 三类系统的 native lib。
   * 如果你有 Gradle：`gradle setupDecompWorkspace`
   * 如果你是 maven 用户：不好意思，笔者不是很确定你能不能只用 maven 来开发基于 MinecraftForge 的 Mod。
 4. 根据你使用的 IDE 不同：
   * Eclipse：在 `setupDecompWorkspace` 后接上 `eclipse`
   * Intellij IDEA：在 `setupDecompWorkspace` 后接上 `idea genIntellijRuns`
   * 其他 IDE：不好意思你需要找对应的 Gradle 插件来解决问题，请查阅相关资料获得更多帮助。在这里的话就什么也不用加好了。
 5. 回车运行。若看到 `BUILD SUCCESS` 字样则表示大功告成，否则重复步骤 3. 和 4. 直到成功为止。

#### 常见问题

是的，因为各种乱七八糟的原因，部署环境的过程总是会有各种莫名其妙的问题。对于知道 Gradle 是干什么的用户，大可以 `--debug`、`--stacktrace` 直接莽。但有一点请注意：部署环境的过程中，因为涉及到几轮 mapping 和下载 Minecraft 的 assets，所以在没有预先部署好的环境留下的缓存的情况行，这个过程不会特别快。要有耐心。

### 入口类

和一般的教程不一样，这里的写法有些特殊：

````java
// Mod主类的标记。其中只有modid是必填项。
//
// modid 不允许使用大写字母，最大长度 64，不能为空。
// 推荐在 modid 中只使用小写字母、数字、连字符和下划线，换言之即只使用满足正则表达式
// [a-z0-9\-_]{1,64} 的 modid。
// 发现不符合要求的 modid 时，会抛出 IllegalArgumentException 阻止游戏启动。
//
// name 没有限制。若不填则直接使用 modid 充当 name，但仍然会产生警告。
// 一般来说，name 都满足正则表达式 [\w ]+。但也有少许例外。
//
// version 没有限制，若不填则使用 1.0，并产生警告。
// 对于 version 来说，***强烈***推荐只用数字和点，比如只有 MAJOR.MINOR.PATCH 的 SemVer。
@Mod(modid = "example_mod", name = "ExampleMod", version = "0.0.0")
public enum ExampleMod {
    INSTANCE;

    // Mod主类实例的“工厂”。
    // 别的Mod开发教程肯定使用的是@Mod.Instance
    // 这里之所以这么写，是因为主类是 enum，换言之父类是 java.lang.Enum
    // 因此，FML 直接 Class.newInstance() 的话会炸掉，java.lang.Enum 没有零参构造器
    @Mod.InstanceFactory
    public static ExampleMod getInstance() {
        return INSTANCE;
    }

    // 一个入口方法。
    // Mod的加载大致分为 6 个阶段，每个阶段均有一个事件代表，
    // 而 @Mod.EventHandler 注解则标记该方法将订阅此事件。
    // Mod 加载时的六个阶段对应的事件，以时间顺序排列：
    //    FMLConstructionEvent
    //    FMLPreInitializationEvent
    //    FMLInitializationEvent
    //    FMLInterModComms.IMCEvent
    //    FMLPostInitializationEvent
    //    FMLLoadCompleteEvent
    // 其中，PreInit.、Init.、IMCEvent 和 PostInit. 是最常用的四个事件，
    // 剩下两个事件只有在相当少见的情况下才会用到，大可暂时无视。
    @Mod.EventHandler
    public void preLoad(FMLPreInitializationEvent event) {
        System.out.println("Hello, MinecraftForge");
    }
````
# src/main/java Detail

## 本层职责

这一层存放 Java 源码，是模组行为的实现层。Mindustry 在读取 `mod.json` 中声明的主类后，真正执行的代码就来自这里编译出的字节码。

## 当前状态

当前只有一个包 `colortheducts`，说明模组规模较小，功能集中，没有拆出额外工具包、渲染包或配置包。

## 实现方式

`build.gradle` 给这一层启用了 Java 编译，并通过 `compileOnly` 依赖 Mindustry `core`。因此这里的源码可以引用 `mindustry.*` 和 `arc.*` API，但这些依赖不会被打进最终模组包，而是由游戏本体提供。

## 与其他层级的关系

- 上游读取自 `src/main/`。
- 下游输出到 `build/classes/java/main/`，并进一步被装入桌面 JAR、合并 JAR 和 D8 输入 JAR。

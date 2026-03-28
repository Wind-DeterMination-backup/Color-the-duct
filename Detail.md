# 根目录 Detail

## 本层在整个仓库中的职责

仓库根目录是整个模组的总装配层。这里同时放了源码入口、构建入口、发布入口、说明文档和许可证，因此它不是单纯的“文件堆”，而是把 Mindustry 模组从开发态推进到分发态的控制面。`build.gradle` 决定如何编译和打包，`mod.json` 决定游戏如何识别这个模组，`README.md` 和 `RELEASE_NOTES.md` 面向人解释功能和演进，`gradlew` / `gradlew.bat` 让任意机器在不预装 Gradle 的情况下也能启动构建。

## 这一层具体做了什么

- 用 `settings.gradle` 把项目名固定为 `color-the-ducts`，后续 Gradle 归档文件名默认继承这个名字。
- 用 `build.gradle` 定义 Java 插件、Mindustry `core` 的 `compileOnly` 依赖、UTF-8 编码、桌面包、安卓包、合并包、`dist/` 复制和工作区外层 `构建/` 复制。
- 用 `mod.json` 告诉 Mindustry 这是一个 Java 模组，主类是 `colortheducts.ColorTheDuctsMod`，最小游戏版本是 `154`，并且设置了 `hidden: true`，说明它更像客户端功能增强而不是需要在模组列表中高调展示的内容包。
- 用 `README.md` 描述功能、设置项、支持的液体建筑类型以及本地构建命令。
- 用 `RELEASE_NOTES.md` 记录功能从 `v1.0.0` 到 `v1.2.1` 的迭代重点，可以看出功能从“单点染色”扩展到了“悬停整线染色”和更真实的连通检测。
- 用 `ctd_build_release_dox.md` 把本地构建和 GitHub Release 的流程单独写成维护文档，降低维护者切换上下文的成本。
- 用 `LICENSE` 约束分发和修改方式。当前文件内容是 GPLv3。
- 用 `.gitignore` 排除了 `.gradle/`、`build/`、`dist/`、IDE 元数据等生成物，说明仓库设计上把“手写内容”和“机器产物”区分得很清楚。

## 实现方式

这个仓库采用标准 Gradle Java 项目布局，但它不是普通桌面 Java 应用，而是 Mindustry 模组工程。源码位于 `src/main/java`，运行时资源位于 `src/main/resources`。构建时，Gradle 先把 Java 编译为 `.class`，再按目标平台决定是否额外通过 D8 生成 `classes.dex`。桌面端包依赖 `.class`，安卓端包依赖 `classes.dex`，合并包同时包含两者和 `mod.json`，从而让一个归档同时兼容桌面与安卓加载方式。

`build.gradle` 里还体现出两个比较关键的实现策略：

- 依赖策略是“优先本地 Mindustry 源码子项目，否则退回 JitPack 的 `com.github.Anuken.Mindustry:core`”。
- 发布策略是“生成产物后自动复制到 `dist/`，并再复制一份到工作区上级的 `构建/` 目录”，这说明维护者的真实工作流不仅是 CI，也包含本地手工测试和外部分发。

## 与其他层级的关系

- `src/` 是根目录下最重要的手写实现层，真正的模组逻辑和文案都在那里。
- `gradle/` 与 `gradlew*` 共同组成构建启动层，为 `build.gradle` 提供执行入口。
- `.github/` 把根目录的构建规则迁移到 CI 环境，复用 `deploy` 任务生成发布产物。
- `build/` 是根目录规则运行后的中间产物层。
- `dist/` 是更接近“拿去安装/上传”的最终分发层。
- `.gradle/` 是本机 Gradle 状态层，不是源码，也不应被当作项目行为定义的一部分。

## 本层值得注意的细节

- `README.md` 中“复制到工作区根目录 `构建/`”一节示例文件名仍写着 `1.0.2`，而 `build.gradle` / `mod.json` 已经是 `1.2.2`。这说明文档曾经跟随早期版本编写，后来没有完全同步示例。
- `dist/` 里同时出现 `Color-the-duct.*` 和 `color-the-ducts.*` 两套命名，表明目录中保留了历史构建结果，而不是只保留当前命名规范下的产物。
- 根目录本身不是运行时代码层，但它决定了下面所有层如何被组织、如何被生成、最终如何对外发布。

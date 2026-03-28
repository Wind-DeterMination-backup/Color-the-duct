# .github/workflows Detail

## 本层职责

这一层存放 GitHub Actions 工作流文件，作用是把根目录里的 Gradle 构建过程搬到 GitHub 托管环境中执行，并在打标签时自动生成 Release 附件。

## 当前文件与行为

- `release.yml` 是唯一工作流，因此这一层的责任非常集中，没有测试流、Lint 流或 PR 校验流干扰。
- 触发条件有两个：`workflow_dispatch` 允许手动点按钮执行，`push.tags = v*` 允许在推送版本标签时自动执行。
- 权限设置为 `contents: write`，这是为了给 `softprops/action-gh-release` 创建 Release 并上传附件。

## 实现方式

工作流按顺序完成以下事情：

- `actions/checkout@v4` 拉取仓库内容。
- `actions/setup-java@v4` 安装 Temurin Java 17。虽然项目源码按约束需要保持 Java 8 兼容，但 Gradle 构建环境本身可以使用更高版本的 JDK。
- `gradle/actions/setup-gradle@v4` 预热 Gradle 环境，减少重复下载。
- `android-actions/setup-android@v3` 与 `sdkmanager` 安装 Android build-tools，给 `dexAndroid` 提供 D8。
- 最后执行 `clean deploy`。这里不是简单跑 `jar`，而是明确调用项目自定义的合并产物任务。
- 若当前引用是 tag，则用 `softprops/action-gh-release@v2` 上传 `dist/*.jar` 和 `dist/*.zip`。

## 与其他层级的关系

- 依赖根目录的 `gradlew` 和 `build.gradle`，没有这两者，工作流本身没有构建逻辑。
- 产物命名继承 `settings.gradle` 里的项目名和 `build.gradle` 里的任务定义。
- 输出层指向 `dist/`，因此 CI 与本地分发目录保持了一致。

## 细节观察

- 工作流执行的是 `deploy`，这意味着仓库把“最终发布形态”定义为合并桌面 + 安卓内容的产物，而不是分别上传普通桌面包和单独安卓包。
- `release.yml` 里在 shell 中先删 `dist/`，说明 CI 希望发布目录只包含当次构建结果，避免上传历史残留文件。

# gradle Detail

## 本层职责

`gradle/` 是 Gradle Wrapper 的资源目录。它不是写构建规则的地方，真正的规则在根目录 `build.gradle`；这里负责让 `gradlew` / `gradlew.bat` 知道该下载哪个 Gradle 发行版、用什么启动器来拉起构建系统。

## 当前结构说明

- 当前目录只有 `wrapper/`，符合标准 Gradle Wrapper 布局。
- 说明项目没有把额外的 Gradle 脚本拆到 `gradle/*.gradle` 或版本目录中，构建逻辑仍集中在根目录。

## 与其他层级的关系

- 被根目录下的 `gradlew` 和 `gradlew.bat` 直接引用。
- 为 `build.gradle` 提供可执行的 Gradle 运行时。
- 与 `.gradle/` 不同：`gradle/` 是仓库应提交的“启动资源”，`.gradle/` 是本机生成的“运行状态”。

# gradle/wrapper Detail

## 本层职责

这一层是 Wrapper 的最小自举集合。它解决的问题不是“怎么写构建规则”，而是“在没有全局安装 Gradle 的机器上，如何稳定地拿到指定版本的 Gradle 并执行根目录规则”。

## 文件作用

- `gradle-wrapper.jar`：官方 Wrapper 启动器，负责读取属性文件、检查本地缓存、下载并解压 Gradle 分发包，然后把控制权交给真正的 Gradle。
- `gradle-wrapper.properties`：定义 Wrapper 应该获取的发行版。当前配置指向 `gradle-8.14.3-bin.zip`，并且使用阿里云镜像源。

## 实现方式

当用户执行 `.\gradlew.bat` 或 `./gradlew` 时，脚本会定位到这个目录，读取 `gradle-wrapper.properties`，再由 `gradle-wrapper.jar` 触发下载和启动。因为这里使用的是 `distributionBase=GRADLE_USER_HOME`，所以 Gradle 发行版不解压到仓库内，而是进用户的 Gradle 主目录。

## 与其他层级的关系

- 上接根目录脚本：`gradlew*` 没有这里的文件就无法完成自举。
- 下游服务整条构建链：`build.gradle`、`src/`、`build/`、`dist/` 都依赖 Gradle 成功启动后才能参与。

## 细节观察

- 镜像源不是官方 `services.gradle.org`，而是阿里云镜像。这通常是为了改善国内网络环境下的下载稳定性。
- 选择 `8.14.3` 说明仓库已经跟进到较新的 Gradle 版本，因此 `build/reports/problems` 中的弃用警告也值得关注。

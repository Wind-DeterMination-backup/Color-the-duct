# src/main Detail

## 本层职责

`src/main/` 对应 Gradle 的主源集。也就是说，这里放的是会被打进最终模组归档、在游戏运行时真正生效的内容，而不是测试代码、示例代码或开发脚本。

## 当前结构说明

- `java/`：放 Java 源码。
- `resources/`：放资源文件，尤其是多语言 bundle。

## 实现方式

Gradle 在 `classes` 阶段编译 `java/`，在 `processResources` 阶段复制 `resources/`。之后这两部分会在打包任务里重新汇合，形成桌面和安卓模组包的内容。

## 与其他层级的关系

- 上接 `src/`，继承标准源集语义。
- 下接 `java/` 与 `resources/` 两个子层，一个负责逻辑，一个负责文案和数据。
- 对 `build/release-stage`、`build/libs` 和 `dist/` 的内容有决定性影响。

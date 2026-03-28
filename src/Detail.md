# src Detail

## 本层职责

`src/` 是项目的手写源根。这里定义了模组真正的可维护内容，包括 Java 逻辑和运行时资源。与 `build/`、`dist/` 不同，这一层不是构建结果，而是构建输入。

## 实现方式

仓库采用 Gradle 默认源集布局，所以主要内容都放在 `main/` 下。这意味着 `build.gradle` 几乎不用额外声明源路径，Gradle 就能自动识别 `src/main/java` 和 `src/main/resources`。

## 与其他层级的关系

- 上游由根目录的构建脚本读取。
- 下游生成 `build/classes`、`build/resources`、`build/d8-input`、`build/release-stage` 和最终 `dist/` 归档。
- `src/` 是整个仓库中最核心的“真实业务层”，其他大多数目录都是围绕它服务。

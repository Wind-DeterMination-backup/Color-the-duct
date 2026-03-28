# src/main/resources Detail

## 本层职责

这一层存放会被原样打进模组归档的资源文件。对于当前项目来说，最重要的资源不是图片或音频，而是设置面板用到的多语言文本。

## 实现方式

Gradle 会在资源处理阶段把这里的内容复制到 `build/resources/main/`。后续无论打 JAR、ZIP 还是安卓包，这些文件都会以资源的方式被一并带入。

## 与其他层级的关系

- 为 `src/main/java/colortheducts/ColorTheDuctsMod.java` 提供运行时文案。
- 被 `build/resources/main/` 复制并最终进入 `build/release-stage/` 与 `dist/` 中的归档文件。

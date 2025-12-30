# Template Android 项目指南

## 构建前置
- JDK：推荐 21（当前配置）；如遇兼容性问题可切回 17 并开启 coreLibraryDesugaring。
- Android SDK：安装与 `compileSdk`/`targetSdk`（36）匹配的 SDK 和构建工具；准备可用的模拟器/真机以运行 `connectedAndroidTest`、Baseline Profile、宏基准。
- Gradle：使用项目自带 wrapper `./gradlew`。

## 常用命令
- 代码规范与静态检查：`./gradlew ktlintCheck detekt lintDebug`
- 单元测试：`./gradlew testDebugUnitTest`
- 全部检查（CI 同步）：`./gradlew ktlintCheck detekt lintDebug testDebugUnitTest`
- 组装调试包：`./gradlew :app:assembleDebug`

## 签名
- `app/build.gradle.kts` 支持 `keystore.properties`：
  ```properties
  storeFile=your.keystore
  storePassword=****
  keyAlias=****
  keyPassword=****
  ```
- 未提供 keystore 时会回退使用 debug 签名，适合模板/CI；`.gitignore` 已忽略 keystore 相关文件。

## 基线配置与性能
- Baseline Profile 生成：`./gradlew :app:generateBaselineProfile`（需可用设备/AVD，默认使用 managed device `pixel9Api36`，可按需调整）。
- 宏基准测试：`./gradlew :benchmark:connectedBenchmarkAndroidTest`（需真机/模拟器；CI 默认关闭，可在 GitHub Actions 中将环境变量 `RUN_DEVICE_TESTS` 设为 `true` 开启）。

## CI
- GitHub Actions 工作流：`.github/workflows/ci.yml`
  - 默认执行 ktlint、detekt、lint、单元测试、assemble。
  - 如需设备测试，设置环境变量 `RUN_DEVICE_TESTS=true`。
  - 构建/报告会作为 artifact 上传。

## 其他
- Gradle 性能：`org.gradle.jvmargs` 设为 4096m，已开启并行；如在 CI 受限可按需调低。

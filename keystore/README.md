# Mika Dev 签名证书（固定）

`mika-dev-debug.keystore` 是 CI 构建 `Mika Dev`（`com.wangzan101.mika.dev`）时使用的 debug 签名证书。

## 为什么放进仓库

GitHub Actions 每次都在全新 runner 上构建，`~/.android/debug.keystore` 不存在时 AGP 会现场生成一份**新的随机密钥对**。结果是每次打出来的 APK 签名都不一样：用新包装旧包必然 `SIGNATURE MISMATCH`，只能先卸载——卸载就把 Mika Dev 里的模型配置、授权和任务记录一起丢掉。

`test-apk.yml` 与 `mika-test-apk.yml` 在构建前把这份证书复制到 `$HOME/.android/debug.keystore`，从此所有构建签名一致，可以直接覆盖安装、保留数据。

## 凭据

| 项 | 值 |
|---|---|
| 文件 | `keystore/mika-dev-debug.keystore` |
| 文件 SHA-256 | `77DBC5E8838DFD78651D7F69C5D136F856CF0A06D6F4C820EB9205A0CE54224F` |
| 格式 | PKCS12 |
| keystore 密码 | `android` |
| key alias | `androiddebugkey` |
| key 密码 | `android` |
| 证书 SHA-256 | `ef:ca:a7:02:d4:6d:b1:15:e7:bc:bb:66:10:16:52:f2:7d:41:c2:69:61:c5:64:8c:d4:8f:4b:08:c3:8a:4c:dc` |
| 证书 SHA-1 | `17:2C:CC:A4:73:70:F7:C0:E3:98:DB:A0:E3:C6:F5:C6:B2:0F:8E:DC` |
| Subject | `C=US, O=Android, CN=Android Debug` |
| 有效期 | 2025-02-01 → 2055-01-25（30 年） |

密码沿用 Android 的 debug 约定（`android` / `androiddebugkey`），所以本地 Android Studio 与命令行 `./gradlew` 都不用改 `app/build.gradle.kts`，也不会因为缺一份 `keystore.properties` 而构建失败。

## 本地使用

放回默认位置即可：

```bash
mkdir -p ~/.android
cp keystore/mika-dev-debug.keystore ~/.android/debug.keystore
chmod 600 ~/.android/debug.keystore
```

Windows 上是 `C:\Users\<name>\.android\debug.keystore`。

## 警告

这是 **debug 证书**，不是发布证书。仓库是公开的，拿到它的人可以签出同样包名 `com.wangzan101.mika.dev` 的 APK，因此：

- 只用它签开发包，不要用它签任何对外分发物；
- 正式版（`release`/`perf`）证书另行保管，绝不提交进仓库；
- 万一要断开这份证书，换一份新的即可，代价是最后一次必须卸载重装。

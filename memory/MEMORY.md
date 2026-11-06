# 项目长期记忆（Codename-Engine-Re-Encryption）

## 用户的长期要求

- **改动做完直接推到 GitHub，不用每次问**（2026-11-02 明确："往 github 上传，以后都是"）。
  远端 `https://github.com/Wqawa/Codename-Engine-Re-Encryption`，只有 `main` 分支。
- 交付只做编译验证：`haxelib run lime build windows -nocolor` 无 `error:`、exe 时间戳更新；
  游戏内行为由用户自己验（AGENTS.md 第 3 节第 8 条）。

## 仓库与环境要点

- 2026-11-01 前这个目录是 zip 解压、没有 `.git`；现在已就地接管。2026-11-02 用
  `git fetch --unshallow --tags` 补全了历史（77 提交、10 个 tag、已非浅克隆），但仍是
  `blob:none` 部分克隆：读旧版本文件（`git show <旧提交>:<路径>`）会自动联网拉 blob。
- `backup/remote/*`（原 17 个已删远端分支的 SHA）那批 tag 在就地重建时丢了，远端也没推过，
  不可恢复；AGENTS.md 第 5 节已改成如实记录，别再按那个命令找分支。
- 本机 HTTPS 被 SteamTools（Watt Toolkit）中间人代理，git 必须用
  `http.sslBackend=openssl` + `http.sslCAInfo=C:/Users/S062PT/.git-ca-steamtools.pem`，
  这两项写在仓库本地配置里，不要清掉。
- 构建入口：`building/Build.bat`（Windows）、`build-linux.sh`、`build-macos.sh`；
  六个 GitHub Actions workflow 全部走这三个脚本（release 与 debug 一致）。
- 沙箱若禁用 `reg.exe`，hxcpp 探测 VS 会失败（PCH 报 `crtdbg.h` 找不到）。手动补环境后
  `haxelib run lime build windows` 可正常编：MSVC 用 `D:/VS2026/main/VC/Tools/MSVC/14.51.36231`，
  SDK 用 `10.0.26100.0`（INCLUDE 要含 ucrt/um/shared/winrt，LIB 含 ucrt/um 的 x64，PATH 加 SDK bin/x64）。
- 只做 Haxe 层类型检查（18 秒，比全量编译快得多）：`haxelib run lime build windows -D no-compilation`。
- 缓存里 0 字节的 `.obj` 会让链接报 `LNK1136`，删掉该文件重跑即可（不会重编其它目标）。
- hxcpp 编译缓存优先级：命令行参数 > `HXCPP_COMPILE_CACHE` > 默认位置
  （默认盘剩余不足 2 GB 时落到 `<repo>/.cache/hxcpp`）。

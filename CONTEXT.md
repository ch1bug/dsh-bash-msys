# CONTEXT.md — dsh-bash-msys

> 状态:初始化骨架。术语表与决策随 grill/implement 惰性填充。

## 项目一句话

DSH bundle:给 DSH 的 `bash` 模型工具与会话终端提供**完整 MSYS2 UCRT64 环境**(pacman / cygpath / `/c/` 路径 / 全套 `/usr/bin`),fork 自官方 `@deepseek-ai/dsh-bash-local` 并泛化 bash 路径与环境注入。

## 已验证事实(来源:代码调研 + 实测,2026-09-30)

- 上游 `@deepseek-ai/dsh-bash-local@0.1.7-rc.2`:schema 仅 6 项 volatile 配置(cwd/timeoutMs/maxTimeoutMs/maxOutputBytes/maxSpillBytes/graceMs);bash 二进制硬编码 `'bash'` 沿 PATH 解析,无 bashPath、无 envOverrides 配置项;注册 `ctx.shell`;`static inject = ["subprocess"]`;完全裸执行(unconfined by itself);one-shot = `['bash','-c',cmd]` 非 login 无 rc;env 层叠 ENV_OVERRIDES→caller env→dshEnv;后台作业/spill 输出/ctx.jobs 集成现成
- `dsh-terminal-bash` 的 shellPath/shellArgs 是其自有独立 Config(默认 `/bin/bash` + `--noprofile --norc -i`),不依赖 executor
- MSYS bash 在 DSH confined 沙箱档无法启动(MSYS runtime 需命名管道,restricted token 拒绝,Win32 error 5)→ 本项目的引擎实际运行档 = unconfined
- Windows 上 PATH 裸 `bash` 不可靠(可能命中 WSL bash.exe)→ 显式路径是硬需求
- 宿主 MSYS2 在 `C:\msys64` 实测可用(bash -lc 'pacman -Q; uname -a' 正常,MSYSTEM=MSYS)

## 已定决策(grill 共识,2026-09-30)

- D1 子系统 = MSYSTEM=UCRT64(/usr/bin 超集)
- D2 fork 官方 dsh-bash-local 泛化:新增 bashPath(显式 MSYS bash)+ env 注入配置;单独建仓
- D3 one-shot env 注入 MSYSTEM/CHERE_INVOKING/PATH;PTY 终端 = `--login -i`
- D4 保留沙箱包装代码但不承诺受限进程沙箱(见事实第 3 条)
- D5 preset 行集参考 dsh-bash-native 的示范(executor + dsh-tool-bash + dsh-terminal 组)
- D6 原 brush bundle(dsh-bash-native)待本项目在真实会话验证通过后再从 profile 卸载

## 术语表(惰性)

(空 — 首个 resolved 术语出现时填充)

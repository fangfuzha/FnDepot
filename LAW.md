# 本源构建规范

> 依据：[FnDepot 外部源 V2 编写说明](https://github.com/EWEDLCM/FnDepot/blob/main/README.md)（协议版本 2.0）。
>> 本文只写本源的具体约定；客户端通用的字段说明看原文。

## 1. 仓库与目录

```text
FnDepot/
├── fnpack.json      # 必填：根目录，文件名大小写完全一致
├── assets/          # 图标与预览图
├── apps/            # 可选：拆分模式的单应用详情 JSON
├── README.md
├── LAW.md
└── LICENSE
```

- 根目录**必须**有名为 `fnpack.json` 的文件。客户端不认 `fndepot.json`。
- 仓库必须公开，默认分支 `main`。
- 仓库名必须含 `fndepot`：官方每天跑一遍的源质检脚本按仓库名与 `fnpack.json` 找候选源，
  名字对不上就不会被收录。
- 安装包**不入库**：包本体放在各自项目的 GitHub Release，
  这里用绝对地址引用，源仓库只留索引与图标。

## 2. 元数据

- `schema_version` 必须是字符串 `"2"`。写成数字 `2` 或 `"2.1"` 都会让整个源同步失败。
- `source_info.name` 与 `source_info.author` 必填。
- `source_info.author`、`apps.*.maintainer`、`apps.*.distributor` **不能叫 `fndepot`**（会被判冒名，直接拦截）。
- `apps` 的键必须与安装包 manifest 里的 `appname` 完全一致，区分大小写。
- `categories` 只能取这九个固定分类，最多两个：
  `影音娱乐、系统工具、编程开发、AI赋能、生活服务、智能智控、教育学习、游戏地带、硬件驱动`
- `platform` 只能是 `all`、`x86`、`arm`。
- `run_as` 只能是 `package` 或 `root`；`install_type` 只能是空串（存储空间）或 `root`（系统空间）。
- `is_docker` 必须是布尔值；`icon_url` 必填；预览图最多 8 张。

## 3. 版本与安装包

- 版本号用 SemVer，必须可比较（`1.0.0`、`1.2.3`、`2.0.0-beta.1`）。
  客户端自己挑最高版本，**不要**用 `latest` 或日期代替版本号。
- 每个版本至少给一个可用安装包：`packages.<架构>.download_url` 必填，
  架构键只能是 `all`、`x86`、`arm`。
- 选包顺序：先 `packages.<当前架构>`，没有才回退 `packages.all`。
- `sha256` 强烈建议填（客户端会强制校验），`size` 填**字节数**，
  不能写 `"20 MB"` 这种字符串。
- **同一个「版本号 + 架构」的包文件视为不可变**。内容变了必须发新版本号，不能悄悄替换 ——
  客户端有缓存，悄悄替换的结果是有人永远装到旧包。
- `updated_at` 用带时区偏移的 ISO 8601（`2026-11-26T13:00:00+08:00`）。

## 4. 发布新版本

1. 应用项目打 tag 发版，产出带版本号的文件名（`Mihomo-<版本>-<架构>.fpk`）。
2. 更新 `fnpack.json`：新增版本键，填 `changelog`、`updated_at`、
   `download_url`、`sha256`、`size`。
3. 推 `main` 分支。客户端下次同步就会看到新版本。

本源的 `fnpack.json` 由应用项目里的 `go run ./scripts fndepot publish` 自动生成与推送，
**不要手写**：字段多，格式错一个字符就可能让整个源同步失败。

## 5. 自检

```bash
jq empty fnpack.json
sha256sum packages/<包>.fpk
stat -c '%s' packages/<包>.fpk
```

官方发布前检查清单：
<https://github.com/EWEDLCM/FnDepot#12-发布前检查清单>

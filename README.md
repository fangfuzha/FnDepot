# fangfuzha 的应用源

飞牛 NAS 第三方应用管理器 [FnDepot](https://github.com/EWEDLCM/FnDepot) 的外部应用源，
收录自研的飞牛 NAS 应用。

## 应用列表

| 应用 | 描述 | 版本 | 架构 |
|---|---|---|---|
| [Mihomo 代理](https://github.com/fangfuzha/fnOS-Mihomo) | 在飞牛 NAS 上运行 Mihomo 内核的本地代理应用：订阅管理、节点与策略组切换、国内直连国外代理的分流、NAS 本机出站流量接管 | 1.0.0 | x86 / arm |

## 安装

1. 在 NAS 上装好 **FnDepot**（飞牛 NAS 上的第三方应用管理器）。
2. 在 FnDepot 里「添加应用源」，填：

   ```text
   https://github.com/fangfuzha/FnDepot
   ```

3. 刷新应用列表，选应用安装。

源里的版本号每次发版都会更新，FnDepot 会提示新版本，装完也能在它里面直接升级。

> 安装包本体放在各自项目的 GitHub Release 上，本仓库只放索引与图标 ——
>> 一个源仓库放多个应用时，这样每个应用各管各的版本，谁发版都不影响别人。

## 目录结构

```text
FnDepot/
├── fnpack.json      # 源索引：客户端唯一读的东西
├── assets/          # 图标与预览图
├── apps/            # 可选：拆分模式的单应用详情 JSON
├── README.md
├── LAW.md           # 本源的构建规范
└── LICENSE
```

- **`fnpack.json`** —— 客户端按当前架构和系统版本过滤后，选版本号最高的那个版本，
  再和已安装包的版本比较来提示升级。**版本号写在这里，客户端才知道出了新版本。**
- **`assets/`** —— 图标与预览图，路径写相对地址（相对 `fnpack.json` 所在目录）。

## 开发者：加一个新应用

1. 在 `assets/` 下放图标（`assets/<应用键>.png`）。
2. 在 `fnpack.json` 的 `apps` 里加一条，
   键名必须与安装包 manifest 里的 `appname` 完全一致（含大小写）。
3. 细节见 [LAW.md](LAW.md)。

规范原文：[FnDepot 外部源 V2 编写说明](https://github.com/EWEDLCM/FnDepot/blob/main/README.md)。

## 说明

- 本源只收录自研应用，不收录第三方转存内容。
- 应用由各自作者维护，本源不做安全审核、担保或背书。

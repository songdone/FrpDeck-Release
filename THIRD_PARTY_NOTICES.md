# FrpDeck 第三方组件说明

FrpDeck 本身按《FrpDeck 最终用户许可协议》授权（见 LICENSE）。镜像和网页里用到了下面这些第三方组件，它们按各自的开源许可证提供，许可证原文在镜像的 `/usr/share/frpdeck/third_party/` 目录里。

| 组件 | 用在哪里 | 许可证 | 版权 |
|---|---|---|---|
| frp（frpc） | 镜像里自带的隧道客户端，取自 frp 官方发布包，未修改 | Apache License 2.0 | fatedier 及 frp 贡献者 |
| Vue.js 3.5.13 | 网页界面框架 | MIT | Copyright (c) 2018-present, Yuxi (Evan) You and Vue contributors |
| Inter | 界面的英文和数字字体 | SIL Open Font License 1.1 | Copyright (c) 2016 The Inter Project Authors |
| Lucide | 界面图标的造型 | ISC | Lucide Contributors；部分来自 Feather（Cole Bemis，MIT） |
| Go 标准库 | 后端程序 | BSD-3-Clause | Copyright (c) 2009 The Go Authors |
| go-toml/v2 2.4.3 | 校验并解析导入的 TOML 服务配置 | MIT | Copyright (c) 2021 - 2023 Thomas Pelletier |
| go-ini/ini 1.67.0 | 解析旧版 INI 服务配置 | Apache License 2.0 | go-ini/ini contributors |
| rsc.io/qr v0.2.0 | 在本机生成两步验证的二维码，代码放在 internal/qr，只改了导入路径 | BSD-3-Clause | Copyright (c) 2009 The Go Authors |
| Alpine Linux 基础镜像 | 镜像的基础系统 | 各软件包各自的开源许可证 | Alpine Linux 及各软件包的作者 |
| xterm.js 5.5.0 | 浏览器 SSH 终端显示组件，固定版本原样引入 | MIT | The xterm.js authors |
| fn-knock / Go-Reauth-Proxy | 可选的独立部署安全网关；接口适配参照 fn-knock 2.5.3 契约，对接功能向所有版本免费开放 | MIT | Copyright (c) 2026 KCI-LNK |

frp 的源代码：https://github.com/fatedier/frp 。FrpDeck 只是调用官方发布的 frpc 程序，没有修改 frp 的代码。

FrpDeck 兼容 Lucky 的 OpenToken 接口，但不包含、也不分发 Lucky 的任何代码。FrpDeck 和 frp、Lucky 两个项目的官方都没有关系。

感谢 fn-knock / KCI-LNK 提供开源网关能力。项目：https://github.com/kci-lnk/fn-knock-turborepo ，网关：https://github.com/kci-lnk/Go-Reauth-Proxy 。FrpDeck 提供独立编写的管理界面和连接适配，不捆绑运行 fn-knock，不代表获得其官方背书。MIT 许可原文随包保存在 `third_party/fn-knock-LICENSE.txt`，网页内也可在安全网关页查阅。

xterm.js 来源：https://github.com/xtermjs/xterm.js 。许可证随包保存在 `third_party/xterm-LICENSE.txt`。

# FrpDeck 第三方组件说明

FrpDeck 本身按《FrpDeck 最终用户许可协议》授权（见 LICENSE）。镜像和网页里用到了下面这些第三方组件，它们按各自的开源许可证提供，许可证原文在镜像的 `/usr/share/frpdeck/third_party/` 目录里。

| 组件 | 用在哪里 | 许可证 | 版权 |
|---|---|---|---|
| frp（frpc） | 镜像里自带的隧道客户端，取自 frp 官方发布包，未修改 | Apache License 2.0 | fatedier 及 frp 贡献者 |
| Vue.js 3.5.13 | 网页界面框架 | MIT | Copyright (c) 2018-present, Yuxi (Evan) You and Vue contributors |
| Inter | 界面的英文和数字字体 | SIL Open Font License 1.1 | Copyright (c) 2016 The Inter Project Authors |
| Lucide | 界面图标的造型 | ISC | Lucide Contributors；部分来自 Feather（Cole Bemis，MIT） |
| Go 标准库 | 后端程序 | BSD-3-Clause | Copyright (c) 2009 The Go Authors |
| rsc.io/qr v0.2.0 | 在本机生成两步验证的二维码，代码放在 internal/qr，只改了导入路径 | BSD-3-Clause | Copyright (c) 2009 The Go Authors |
| Alpine Linux 基础镜像 | 镜像的基础系统 | 各软件包各自的开源许可证 | Alpine Linux 及各软件包的作者 |

frp 的源代码：https://github.com/fatedier/frp 。FrpDeck 只是调用官方发布的 frpc 程序，没有修改 frp 的代码。

FrpDeck 兼容 Lucky 的 OpenToken 接口，但不包含、也不分发 Lucky 的任何代码。FrpDeck 和 frp、Lucky 两个项目的官方都没有关系。

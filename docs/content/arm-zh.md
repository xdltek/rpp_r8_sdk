## 文档目的

**XDL RPP SDK 官方 SOP** - ARM (aarch64) 平台部署

- 供 **终端客户、系统集成商和解决方案合作伙伴** 独立完成安装
- 覆盖内容：环境准备 -> SDK 安装 -> 功能验证
- 强调 **可复现性**、**稳定性** 和 **首次上电成功率**


## 适用范围

| 项目 | 说明 |
|------|------|
| 目标读者 | 系统管理员、解决方案工程师、AI 应用开发人员 |
| 部署场景 | 客户交付（生产 / 评估） |
| 所需技能 | Linux 命令行、基础 CMake、Python/Conda |



## 1. 系统要求

安装前，请确认目标系统满足最低要求：

- **操作系统**：Ubuntu 20.04 LTS (aarch64)
- **系统内存**：>= 16 GB DDR5
- **CPU 架构**：aarch64（支持 PCIe Gen3）
- **编译器**：GCC 9.4.0 · **CMake**：>= 3.26.5 · **Python**：3.11
- **构建工具**：`build-essential`、`linux-headers`

> 不满足上述要求可能会导致安装失败或运行不稳定



## 2. 安装前：硬件验证

**目标**：确认 RPP 加速卡已被 PCIe 总线正确识别

执行：

```bash
lspci | grep Co-processor
```

**预期输出**：`XX:XX.X Co-processor: Device 1f2e:00a1 (rev 01)`

- `XX:XX.X` = BDF，例如 **05:00.0**
- 出现 Co-processor 条目表示识别成功

> 如果未检测到设备，请检查卡是否插紧、PCIe 插槽以及 BIOS 设置



## 3. SDK 安装 - 步骤 1：下载并解压安装包

ARM SDK 安装包位于 `sdk` 目录中：

```
azurengine_sw_v1.7.3.14_aarch64_Ubuntu.tar.gz
```

先执行解压：

```bash
tar zxvf azurengine_sw_v1.7.3.14_aarch64_Ubuntu.tar.gz
cd azurengine_sw_v1.7.3.14_aarch64_Ubuntu
```

解压后的目录包含：

- `azurengine_sw_v1.7.3.14_aarch64_Ubuntu.run`
- `get_version.sh`
- `install.sh`
- `uninstall.sh`
- `upgrade.sh`
- `verify_run_file.sh`


## 3. SDK 安装 - 步骤 2：卸载旧版本（推荐）

```bash
bash /usr/local/rpp/doc/uninstall.sh
```

- 移除所有 RPP 软件包、驱动和支持文件
- 如果最终显示成功，过程中出现的少量告警可忽略

> **重要**：请在干净系统或完整卸载旧版本后安装；不支持多版本共存



## 3. SDK 安装 - 步骤 3：运行安装与升级脚本

先校验 SDK 安装包：

```bash
bash verify_run_file.sh
```

预期输出：

`Verification passed: MD5 checksum matches for file: azurengine_sw_v1.7.3.14_aarch64_Ubuntu.run`

检查当前已安装版本：

```bash
bash get_version.sh
```

示例输出：

`1.7.2.4`

升级到新版本 SDK：

```bash
bash upgrade.sh
```

如果安装成功，您应看到：

`Installation completed, sdk version: [1.7.3.14], creation timestamp: [20260404_181046].`

或者，您也可以通过附带脚本先卸载再安装：

```bash
bash uninstall.sh
bash install.sh
```

## 4. 驱动安装与验证

**加载驱动**（每次系统重启后都需要重新执行）：

```bash
cd $HOME/azurengine/rpp_drv
sudo insmod rpp.ko
sudo chmod 666 /dev/rpp0_entire_ctrl /dev/ve0_entire_ctrl
```

**验证方式**：`dmesg` 中无报错即表示驱动加载成功

> 如需自动化部署，请参考 XDL Demo Guide



## 5. 故障排查 - 驱动问题

| 错误 | 处理方式 |
|------|------|
| `File exists` | 驱动已加载，无需处理 |
| `Invalid module format` | 执行 `make clean && make -j$(nproc)` 重新编译后，再重复加载步骤 |



## 5. 故障排查 - PCIe 未识别

**现象**：`RPP accelerator card not found on PCIe bus`

**可能原因**：卡未插紧、插槽故障、BIOS 中禁用了 PCIe、BIOS 版本过旧

**诊断命令**：

```bash
lspci | grep -i Co-Processor
sudo lspci -s "05:00.0" -vv | awk '/LnkCap:|LnkSta:/ {print}'
```

**处理建议**：重新插拔加速卡（以 30 度角插入后压紧），在 BIOS 中启用 PCIe Gen4/5，关闭 ASPM，并更新 BIOS



## 总结

**RPP SDK 安装流程**

1. 检查系统要求
2. 硬件识别：`lspci | grep Co-processor`
3. 下载 -> 卸载旧版本 -> 运行安装程序
4. 每次重启后重新加载驱动
5. 如遇问题，请参考故障排查章节


## 6. 致谢

本指南由 XDL 技术支持团队基于大量客户交付经验和反馈整理而成。感谢所有参与验证并帮助持续改进本安装与部署流程的客户与合作伙伴。


## 7. 反馈与贡献

欢迎您对本文档提出反馈和改进建议。请将建议、更正或补充信息提交给 XDL 技术支持或授权支持渠道。您的反馈将帮助我们持续提升文档质量与准确性。


## 8. 免责声明

本文档所含信息由 XDL Technologies 按“现状”提供。XDL Technologies 不对信息的准确性、完整性或可靠性作任何明示或暗示的保证。XDL Technologies 保留在不另行通知的情况下修改或停止本文档中所述产品或服务的权利。


## 9. 版权

© 2026 XDL Technologies。保留所有权利。未经 XDL Technologies 事先书面许可，不得以任何形式或任何方式复制、传播本文档的任何部分，包括但不限于电子、机械、复印、录音或任何信息存储与检索系统。


## 10. 支持

如涉及客户交付，请联系您指定的 **XDL 技术支持** 或授权支持渠道。报告问题时，请提供以下信息：

* SDK 版本及创建时间戳
* 主机操作系统与内核版本
* RPP 硬件型号与 PCIe 拓扑
* 完整的安装或构建日志

提供以上信息将显著加快问题定位与解决。

如需更多帮助、性能调优或平台相关指导，请联系您的 **XDL 技术支持** 或授权支持渠道。


## 12. 法律声明

本文档仅供信息参考，不构成法律协议。XDL Technologies 不对本文档中的任何错误或遗漏承担责任。本文内容如有变更，恕不另行通知。


## 13. 商标

XDL 及 XDL 标识为 XDL Technologies 的商标。文中提及的其他产品名称、公司名称、商标及注册商标均归其各自所有者所有。


## 14. 附注

1. 所有规格和功能如有变更，恕不另行通知。
2. 性能数据基于内部测试，实际结果可能因系统配置和工作负载不同而有所差异。
3. 如需获取最新信息，请以 XDL Technologies 官方网站为准。


## 15. 安全与合规

XDL Technologies 产品符合相关安全和环保法规。建议用户遵循产品文档中的正确操作和使用规范，以确保安全并符合当地法规要求。


## 16. 发布说明

- **版本 2.0**（2026-01-18）：RPP SDK 安装指南 ARM（aarch64）平台初版发布。
- **版本 2.1**（2026-02-07）：根据客户反馈更新版式，并补充详细故障排查步骤。
- **版本 2.2**（2026-03-19）：更新为 ARM（aarch64）平台《安装与部署指南》。
- **版本 2.3**（2026-04-14）：更新 ARM SDK 安装说明，适配 `azurengine_sw_v1.7.3.14_aarch64_Ubuntu.tar.gz`。


## 修订历史

| 版本 | 日期       | 作者             | 说明 |
|------|------------|------------------|------|
| 2.0  | 2026-01-18 | XDL 技术支持团队 | 初版 |
| 2.1  | 2026-02-07 | XDL 技术支持团队 | 发布 |
| 2.2  | 2026-03-19 | XDL 技术支持团队 | 更新为安装与部署指南 |
| 2.3  | 2026-04-14 | XDL 技术支持团队 | 更新 ARM SDK 安装说明 |

**技术支持**：XDL Demo Guide · XDL 技术支持团队
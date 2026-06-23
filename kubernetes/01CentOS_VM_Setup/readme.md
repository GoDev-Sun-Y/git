# 虚拟机安装与配置指南

## 一、 创建虚拟机

>1. 点击 **VMware** 进入主页面。
>2. 点击 **Create a New Virtual Machine**。
>3. 选择 **Custom (高级)**，点击 **Next**。
>4. 默认 **Next**
>4. 选择 **I will install the operating system later.**，点击 **Next**。
>5. 默认然后 **Next**
>6. **Name**: 修改虚拟机名称（如 `node1`）
>7. **Location**: 修改虚拟机存放路径，点击 **Next**。
>8. **Number of cores per processor**: 修改为 **2**，点击 **Next**。
>9. **Memory**: 调整为 **2048 MB**，点击 **Next**。
>10. **Network Type**: 保持默认（NAT），点击 **Next**。
>11. **I/O Controller Types**: 保持默认，点击 **Next**。
>12. **Disk Type**: 保持默认，点击 **Next**。
>13. 默认 然后**Next**。
>14. **Disk Size**: 调整为 **50 GB**，点击 **Next**。
>15. **Disk File**: 保持默认，点击 **Next**。
>16. 确认硬件信息，点击 **Finish**。

## 二、 加载系统镜像 (ISO)
> **注意**：这一步原本写在最后，为了流程顺畅，建议在此处完成。

>1. 点击左侧刚刚创建的虚拟机，然后点击 **Edit virtual machine settings**。
>2. 在弹出窗口中，点击 **CD/DVD (SATA)**。
>3. 选择 **Use ISO image file**。
>4. 点击 **Browse**，找到并选择下载好的镜像文件（如 >`CentOS-7-x86_64-DVD-2009.iso`）。
>5. 点击 **OK** 保存设置。
## 三、 开始安装操作系统

>1. 开启虚拟机。
>2. 出现提示时，按 **回车键 (Enter)** 开始引导。
>3. 进入安装界面，选择语言（Language），建议选择 **中文** -> **简体中文**。点击 **继续**。
>4. **软件选择 (Software Selection)**: 选择 **基础设施服务器 (Infrastructure Server)**，点击 **完成**。
>5. **安装位置 (Installation Destination)**: 点击 **完成**（默认自动分区即可）。
>6. **网络和主机名 (Network & Hostname)** 进入新的页面:
 >  - 点击配置->弹出窗口
  > - 点击常规
   >- 点击可用时自动链接到这个网络（保证勾选框是选中的状态）

## 四、 网络详细配置

> **注意**：笔记中将网络配置写在了一边，这里整合到安装流程中更合理。

>1. 在 **配置 (Configure)** 窗口中，切换到 **IPv4 设置 (IPv4 Settings)** 选项卡。
>2. 方法 (Method): 下拉选择 **手动 (Manual)**。
>3. 点击 **Add (添加)**：
 >  - 地址 (Address): `192.168.88.xxx` (自定义，勿重复)(第三段因机器而异，在VMware中自行查看)
  > - 子网掩码 (Netmask): `255.255.255.0`
   >- 网关 (Gateway): `192.168.88.2`
>4. 点击 **DNS服务器 (DNS Servers)**: 填写 `223.5.5.5` (阿里 DNS) 或 `114.114.114.114`。
>5. **更改主机名：**________(应用)(完成)(开始安装)
>6. **点击ROOT密码:**_______(测试环境统一设置123)

## 五、 登录系统

>1. 等待安装完成。
>2. 提示重启时，点击 **重启 (Reboot)**。
>3. 重启后登录：
 >   * login : root
  >  * Password:_____

## 最后
```
如果到这里没有问题，大概就是安装好了，这个版本没有图形化，纯是命令行。  
对于部署k8s以及后续操作来说MobaXterm是一个比FinalShell更好用的工具。
```
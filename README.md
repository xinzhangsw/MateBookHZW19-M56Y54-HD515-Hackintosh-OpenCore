## 华为MateBookHZW19-M56Y54-HD515-Hackintosh-OpenCore

### 简单预览
![关于本机](https://github.com/xinzhangsw/MateBookHZW19-M56Y54-HD515-Hackintosh-OpenCore/blob/master/images/%E6%88%AA%E5%B1%8F2022-03-23%2004.25.11.png)
* 具体核显加速情况、跑分等参考images文件夹
* 本EFI支持High Sierra10.13.6至Monterey12.3.1
* 本EFI支持在线升级

### 支持情况
- [x] CPU M5-6Y54;睿频正常
- [x] iGPU HD515;正确设置缓冲帧,核显加速正常
- [x] 内存 海力士 8GB 1866 MHz LPDDR3
- [x] 声卡 瑞昱ALC298;注入`layout-id:13`,麦克风收声正常,`需输入开至最大`
- [ ] 内置无线网卡及蓝牙 博通BCM4356A2;通过`BrcmPatchRAM`注入对应Win驱动可实现蓝牙唤醒与搜索,但无线未驱动所以本EFI未提供,可自行尝试
- [x] 硬盘 三星PM871;需修改`CtlnaAHCIPort.kext`内对应硬盘Id识别,本EFI已修改
- [ ] 触摸屏 WCOM4837 2k; Retina正常但本EFI未注入;触摸屏中断方式为APIC中断,可通过`VoodooI2C`注入SSDT唤醒,`屏幕坏了`本EFI未注入,具体见下文
- [ ] 摄像头 英特尔GC5024;`VoodooI2C`下可唤醒,未深入研究
- [ ] 指纹 FPC8000;暂时无解
- [x] 电池 读数正常
- [ ] 背光 无法实现亮度自定义调节与亮度自动调节
- [x] 睡眠唤醒 概率性失效;具体表现为唤醒后屏幕无显示;通过修改`pmset`解决见下文
- [x] 触控板 Huawei Folio keyboard;`VoodooI2C`内USB方式为默认驱动实际可用,但不显示设备,具体可通过创建电池补丁实现,本EFI未注入
- [x] 自带扩展坞有线网卡 RTL8152B;使用`绿联CM121`驱动即可

### BIOS版本
* 华为BIOS堪称简洁(la kua),啥都没有。基本全靠EFI

当前BIOS版本：1.53 &nbsp;&nbsp; [BIOS下载地址](https://consumer-tkbdownload.huawei.com/ctkbfm/servlet/download/downloadServlet/H4sIAAAAAAAAAD2Py07DMBBF_8XrqhrX43HMijwcqQsIUsq6smsXrLZJlSYgivh3HBSxPJozuvd-s-kWht3XNbAHtmEr5vvPbkFKeIzn8GwvMz7ZMbi-P-2LbdPu-VqK9T1eF-fFju_JcRZQg6CD1gHJ8kyADl47QLDBeZ9sF-9bn9S2eWw5ASBXUs_JhyHYMfbdLs5x6aRRpD8OACt2i2-dHadhLlKXSJWgQokKTVVyo0yBvAQkI0tOBchcIaqaKiNBZvlGFZiR5HWeKUIDKevDnqN__V8-DlP4a7Ysbxv28ws6GTbhGgEAAA%3D%3D.zip)

### BIOS设置
* 安全启动：关闭
* 安全芯片：关闭

### OpenCore
OpenCore: 0.7.8

下载地址: [Download](https://github.com/acidanthera/OpenCorePkg/releases/)

### 系统安装
* 建议使用 【黑果小兵】macOS 安装镜像进行安装

### 关于USB重建的问题
* 本机已重建USB端口;为本机分配Type-c * 1 | USB铰链本机自带键盘 | USB 3.0 * 1 | USB 2.0 * 1
* 因本机Type-c接口仅为1;即3.0与2.0为多组Hub足够使用;仅有如需扩展请自行重建USB端口
 
### 关于触摸屏驱动的问题
* 本机选用`VoodooI2C`制作热补丁方式驱动;本EFI未提供因为屏幕坏了无法验证,但通过日志可知正常驱动
* 通过Win下设备管理或Mac下`IORegistryExplorer`可知本机`APIC PIN`为1024即BIOS默认选用APIC中断;同理也可通过GPIO中断模式驱动
* 通过以下二者驱动前需屏蔽`com.apple.driver.AppleIntelLpssI2C`及`com.apple.driver.AppleIntelLpssI2CController`防止驱动未加载
* 本EFI已提供但未勾选;具体在kernel内核设置添加项内勾选`VoodooI2C.kext`及对应的`VoodooI2CHID.kext`;触摸板需同时勾选`VoodooI2CELAN.kext`
* APIC及GPI0二者仅需使用其一
* 唤醒后务必查看对应日志

#### 屏蔽对应驱动
* 本EFI已提供但未勾选;具体在kernel内核设置补丁项内勾选`com.apple.driver.AppleIntelLpssI2C`及`com.apple.driver.AppleIntelLpssI2CController`

#### APIC中断
* 仅需注入`SSDT-XOSI.aml`;该aml作用于BIOS操作系统判断使其默认驱动触摸屏;自行编译以下内容生成aml并放入EFI并勾选
```dsl
/*
 * Intel ACPI Component Architecture
 * AML/ASL+ Disassembler version 20210930 (64-bit version)
 * Copyright (c) 2000 - 2021 Intel Corporation
 * 
 * Disassembling to symbolic ASL+ operators
 *
 * Disassembly of /Volumes/EFI/EFI/OC/ACPI/SSDT-XOSI.aml, Mon Mar 21 06:18:02 2022
 *
 * Original Table Header:
 *     Signature        "SSDT"
 *     Length           0x0000006D (109)
 *     Revision         0x02
 *     Checksum         0xE9
 *     OEM ID           "DRTNIA"
 *     OEM Table ID     "XOSI"
 *     OEM Revision     0x00001000 (4096)
 *     Compiler ID      "INTL"
 *     Compiler Version 0x20210930 (539035952)
 */
DefinitionBlock ("", "SSDT", 2, "DRTNIA", "XOSI", 0x00001000)
{
    Method (XOSI, 1, NotSerialized)
    {
        If (_OSI ("Darwin"))
        {
            Local0 = Package (0x0A)
                {
                    "Windows 2015"
                }
            Return ((Ones != Match (Local0, MEQ, Arg0, MTR, Zero, Zero)))
        }
        Else
        {
            Return (_OSI ("Windows 2015"))
        }
    }
}

```

#### GPI0中断
* 通过导出DSDT并制作`Hotpatch`修补,自行编译以下内容生成aml并放入EFI并勾选
* 具体思路为屏蔽原映射并新建映射使其默认驱动
```dsl
/*
 * Intel ACPI Component Architecture
 * AML/ASL+ Disassembler version 20210930 (64-bit version)
 * Copyright (c) 2000 - 2021 Intel Corporation
 * 
 * Disassembling to symbolic ASL+ operators
 *
 * Disassembly of /Volumes/EFI/EFI/OC/ACPI/SSDT-SCREENPAD.aml, Mon Mar 21 06:28:44 2022
 *
 * Original Table Header:
 *     Signature        "SSDT"
 *     Length           0x00000175 (373)
 *     Revision         0x02
 *     Checksum         0x91
 *     OEM ID           "ALASKA"
 *     OEM Table ID     "A M I "
 *     OEM Revision     0x01072009 (17244169)
 *     Compiler ID      "INTL"
 *     Compiler Version 0x20210930 (539035952)
 */
DefinitionBlock ("", "SSDT", 2, "ALASKA", "A M I ", 0x01072009)
{
    External (_SB_.PCI0.HIDD, MethodObj)    // 5 Arguments
    External (_SB_.PCI0.HIDG, IntObj)
    External (_SB_.PCI0.I2C3, DeviceObj)
    External (SDM1, FieldUnitObj)
    External (GPEN, FieldUnitObj)

    Scope (\)
    {
        If (_OSI ("Darwin"))
        {	
			Store (One, GPEN)  // 启用 GPI0
            Store (Zero, SDM1) // 禁用 TPL1
        }
    }

    Scope (_SB.PCI0.I2C3)
    {
		Name (SSCN, Package () { 528, 640, 30 })
		Name (FMCN, Package () { 128, 160, 30 })

        Device (TPLX)
        {
            Name (_HID, "XXXX0000")  // _HID: Hardware ID
            Name (_CID, "PNP0C50" /* HID Protocol Device (I2C bus) */)  // _CID: Compatible ID
            Name (_S0W, 0x04)  // _S0W: S0 Device Wake State
            Name (HID2, Zero)
            Method (_STA, 0, NotSerialized)  // _STA: Status
            {
                If (_OSI ("Darwin"))
                {
                    Return (0x0F)
                }
                Else
                {
                    Return (Zero)
                }
            }

            Method (_INI, 0, NotSerialized)  // _INI: Initialize
            {
                _HID = "WCOM4837"
                Return (Zero)
            }

            Method (_CRS, 0, NotSerialized)  // _CRS: Current Resource Settings
            {
                Name (SBFB, ResourceTemplate ()
                {
                    I2cSerialBusV2 (0x000A, ControllerInitiated, 0x00061A80,
                        AddressingMode7Bit, "\\_SB.PCI0.I2C3",
                        0x00, ResourceConsumer, , Exclusive,
                        )
                })
                Name (SBFG, ResourceTemplate ()
                {
                    GpioInt (Level, ActiveLow, Exclusive, PullDefault, 0x0000,
                        "\\_SB.PCI0.GPI0", 0x00, ResourceConsumer, ,
                        )
                        {   // Pin list
                            0x0081
                        }
                })
                Return (ConcatenateResTemplate (SBFB, SBFG))
            }

            Method (_DSM, 4, Serialized)  // _DSM: Device-Specific Method
            {
                If ((Arg0 == HIDG))
                {
                    Return (HIDD (Arg0, Arg1, Arg2, Arg3, HID2))
                }

                Return (Buffer (One)
                {
                     0x00                                             // .
                })
            }
        }
    }
}
```

### 通过`pmset`修复睡眠唤醒问题
* 电源供电设置
```
// 120 分钟后进入休眠，延长清醒时间
sudo pmset -c sleep 120
// 显示器休眠时间：30 分钟
sudo pmset -c displaysleep 30
// 硬盘休眠时间：60 分钟
sudo pmset -c disksleep 60
// 内存供电，内存镜像不写入硬盘
sudo pmset -c hibernatemode 0
// 关闭 standby 模式
sudo pmset -c standby 0
// 关闭 autopoweroff
sudo pmset -c autopoweroff 0
// 休眠时持续联网
sudo pmset -c tcpkeepalive 1
```

* 电池供电设置
```
// 20 分钟后进入休眠
sudo pmset -b sleep 20
// 向硬盘写入镜像，同时内存供电
sudo pmset -b hibernatemode 3
// 显示器休眠时间：15 分钟
sudo pmset -b displaysleep 15
// 硬盘休眠时间：30 分钟
sudo pmset -b disksleep 30
// 休眠时断网
sudo pmset -b tcpkeepalive 0
// 高电量下 standby: 4小时
sudo pmset -b standbydelayhigh 14400
// 低电量下 standby: 2小时
sudo pmset -b standbydelaylow 7200
// standby 电量阈值：75%
sudo pmset -b highstandbythreshold 75
// 开盖唤醒
sudo pmset -b lidwake 1
// 关闭被同一 iCloud 下的设备唤醒
sudo pmset -b acwake 0
```

### 关于Mac序列号的问题
* 下载 OpenCore Configurator,选中PlatformInfo-机型平台设置,并在下方选择`MacBook9,1`机型生成UUID并替换本部分缺失内容

```
<key>PlatformInfo</key>
    <dict>
        <key>Automatic</key>
        <true/>
        <key>CustomMemory</key>
        <false/>
        <key>Generic</key>
        <dict>
            <key>AdviseFeatures</key>
            <false/>
            <key>MaxBIOSVersion</key>
            <false/>
            <key>MLB</key>
            <string>XXXXXXXXXXXXXXXXX</string>
            <key>ProcessorType</key>
            <integer>1796</integer>
            <key>ROM</key>
            <data>ESIzRFVm</data>
            <key>SpoofVendor</key>
            <true/>
            <key>SystemMemoryStatus</key>
            <string>Auto</string>
            <key>SystemProductName</key>
            <string>MacBook9,1</string>
            <key>SystemSerialNumber</key>
            <string>XXXXXXXXXXXX</string>
            <key>SystemUUID</key>
            <string>XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX</string>
        </dict>
        <key>UpdateDataHub</key>
        <true/>
        <key>UpdateNVRAM</key>
        <true/>
        <key>UpdateSMBIOS</key>
        <true/>
        <key>UpdateSMBIOSMode</key>
        <string>Create</string>
        <key>UseRawUuidEncoding</key>
        <false/>
    </dict>
```

### Win+Mac双系统解决Win系统时间时差问题
* 在Windows下运行
```
Reg add HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
```

### 结语
* 本EFI依照OpenCore Guide: [文档地址](https://caizhiyuan.gitee.io/opencore-install-guide/prerequisites.html) 规范编写
* 备用机很多细节没有深究
* 修改本EFI请先自行通过健全性检查

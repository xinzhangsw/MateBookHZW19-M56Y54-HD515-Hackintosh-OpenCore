## 华为MateBookHZW19-M56Y54-HD515-Hackintosh-OpenCore

### 简单预览
![关于本机](https://github.com/xinzhangsw/MateBookHZW19-M56Y54-HD515-Hackintosh-OpenCore/blob/master/images/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202022-01-11%20%E4%B8%8B%E5%8D%884.24.49.png)
* 具体核显加速情况、跑分等参考images文件夹

### 支持情况
- [x] CPU M5-6Y54;睿频正常
- [x] iGPU HD515;正确设置缓冲帧,核显加速正常
- [x] 内存 海力士 8GB 1866 MHz LPDDR3
- [x] 声卡 瑞昱ALC298;注入`layout-id:13`,麦克风收声正常
- [ ] 内置无线网卡及蓝牙 博通BCM4356;截至20220111无解，目前使用外置USB网卡
- [x] 硬盘 三星PM871;需修改`CtlnaAHCIPort.kext`内对应硬盘Id识别,本EFI已修改
- [ ] 触摸屏 京东方WCOM4837 2k; Retina正常但本EFI未注入;触摸也许VoodooI2C有解,屏幕坏了无法尝试
- [ ] 摄像头 英特尔GC5024;未尝试驱动
- [ ] 指纹 FPC8000;暂时无解
- [x] 电池 读数正常
- [ ] 背光 无法实现亮度自定义调节与亮度自动调节
- [ ] 睡眠唤醒 概率性失效;具体表现为唤醒后屏幕无显示
- [ ] 触控板 Huawei Folio keyboard;触控板操作滑动正常,无法实现多指精确触控
- [ ] 自带扩展坞有线网卡 BCM2045A0;暂时无解

### BIOS版本
* 华为BIOS堪称简洁(la kua),啥都没有。基本全靠EFI

当前BIOS版本：1.53 &nbsp;&nbsp; [BIOS下载地址](https://consumer-tkbdownload.huawei.com/ctkbfm/servlet/download/downloadServlet/H4sIAAAAAAAAAD2Py07DMBBF_8XrqhrX43HMijwcqQsIUsq6smsXrLZJlSYgivh3HBSxPJozuvd-s-kWht3XNbAHtmEr5vvPbkFKeIzn8GwvMz7ZMbi-P-2LbdPu-VqK9T1eF-fFju_JcRZQg6CD1gHJ8kyADl47QLDBeZ9sF-9bn9S2eWw5ASBXUs_JhyHYMfbdLs5x6aRRpD8OACt2i2-dHadhLlKXSJWgQokKTVVyo0yBvAQkI0tOBchcIaqaKiNBZvlGFZiR5HWeKUIDKevDnqN__V8-DlP4a7Ysbxv28ws6GTbhGgEAAA%3D%3D.zip)

### BIOS设置
* 安全启动：关闭
* 安全芯片：关闭

### OpenCore
OpenCore: 0.7.6

下载地址: [Download](https://github.com/acidanthera/OpenCorePkg/releases/)

### 系统安装
* 建议使用 【黑果小兵】macOS 安装镜像进行安装

### 关于Mac序列号的问题
* 下载 OpenCore Configurator,选中PlatformInfo-机型平台设置,并在下方选择`MacBookPro9,1`机型生成UUID并替换本部分缺失内容

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
            <string>MacBookPro9,1</string>
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

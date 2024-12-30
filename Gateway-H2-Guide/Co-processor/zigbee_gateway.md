# Module Gateway H2 + CoreS3 运行 ESP Zigbee Gateway

ESP Zigbee Gateway 是一个基于 ESP32 系列 Wi-Fi SoC 和 ESP32-H2 802.15.4 SoC 的网关设备。它可以将 Zigbee 网络与 Wi-Fi 网络连接起来，实现智能家居设备的互联互通。

## 硬件要求

- M5Module-Gateway H2（作为 RCP）![M5Module-Gateway-H2](../images/M5Module-Gateway-H2.png)
- M5Stack CoreS3（作为 Host）![M5Stack-CoreS3](https://static-cdn.m5stack.com/resource/docs/products/core/CoreS3/img-c464672f-1f10-4935-a168-ee4e64f62f70.webp)
- Type-C 数据线
- ESP-IDF 环境

## 1. 安装 ESP-IDF
```bash
git clone --recursive https://github.com/espressif/esp-idf.git
cd esp-idf
./install.sh

. ./export.sh
```

## 2. 获取 ESP Zigbee SDK
```bash
git clone --recursive https://github.com/espressif/esp-zigbee-sdk.git
cd esp-zigbee-sdk
```

## 3. 编译 RCP 固件

在编译 Gateway 固件之前，需要先生成 RCP 固件。

```bash
cd $IDF_PATH/examples/openthread/ot_rcp
idf.py set-target esp32h2
```

在 menuconfig 中配置：
- Component config → OpenThread RCP Example
  - Enable OPENTHREAD_NCP_VENDOR_HOOK

编译并生成固件：
```bash
idf.py build
```

## 4. 编译 Gateway 固件

### 4.1 配置 Gateway
```bash
cd esp-zigbee-sdk/examples/esp_zigbee_gateway
idf.py set-target esp32s3 # 使用 CoreS3
idf.py menuconfig
```

在 menuconfig 中配置：
- ESP Zigbee gateway rcp update
  - [*] Update RCP automatically
  - Board Configuration
    - Pin to RCP reset: 7
    - Pin to RCP boot: 18
    - Pin to RCP TX: 10
    - Pin to RCP RX: 17
- Example Connection Configuration
  - WiFi SSID
  - WiFi Password

### 4.2 编译和烧录
```bash
idf.py build
idf.py erase_flash
idf.py flash # 根据实际端口修改
```

## 5. 运行

1. 将 H2 模块插入 CoreS3
2. 将 CoreS3 连接电脑
3. 运行监视器：`idf.py monitor`

正常运行时可以看到以下日志：
- RCP 固件版本检查
- Wi-Fi 连接成功
- Zigbee 网络创建成功
- 网络开放允许设备加入


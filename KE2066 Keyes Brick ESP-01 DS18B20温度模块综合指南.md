# KE2066 Keyes Brick ESP-01 DS18B20温度模块综合指南

![image-20250317165621861](media/image-20250317165621861.png)

---

## 1. 简介
KE2066是一个ESP-01 DS18B20温度模块，专为物联网和智能家居设计。该模块搭载ESP8266-01芯片，可以通过无线网络接入，远程监测环境的温度数据，并利用所测数据控制其他设备。模块提供了预编译的固件，支持SoftAP和Station两种工作模式，方便用户进行设置和操作。

![image-20250317165613527](media/image-20250317165613527.png)

---

## 2. 特点
- **无线控制**：通过ESP8266-01接入无线网络，实现远程温度监测。
- **双模式工作**：支持SoftAP模式和Station模式，灵活适应不同网络环境。
- **易于使用**：提供预编译固件，简化用户设置过程。
- **复位按键**：方便用户重置模块。

---

## 3. 规格参数
- **工作电压**：DC 3V - 3.3V  
- **主控芯片**：ESP8266-01  
- **按键功能**：复位按键  
- **测量范围**：-55°C 到 +125°C  
- **尺寸**：42mm x 24mm x 21mm  
- **重量**：4.9g  

![image-20250317165652103](media/image-20250317165652103.png)

---

## 4. 工作原理
KE2066模块通过ESP8266-01芯片连接到Wi-Fi网络，实时监测环境的温度数据。用户可以通过网络调试助手发送指令，模块将返回当前的温度值。模块支持两种工作模式：
- **Station模式**：连接到家庭Wi-Fi网络，获取IP地址。
- **SoftAP模式**：创建自己的Wi-Fi热点，供设备连接。

---

## 5. 接口
| 序号 | 名称 | 说明 |
|------|------|------|
| 1    | VCC  | 电源（DC 3V - 3.3V） |
| 2    | GND  | 地线 |
| 3    | DQ   | DS18B20数据引脚 |
| 4    | RST  | 复位引脚 |

---

## 6. 连接图
### 连接示例
1. 将模块的 VCC 引脚连接到 3.3V 电源。
2. 将模块的 GND 引脚连接到地。
3. 将模块的 DQ 引脚连接到 ESP8266 的数据引脚。

---

## 7. 使用方法
### 固件烧录
1. 将Keyes USB模块串口测试扩展板开关拨动到Uart Download，将ESP8266-01插入模块中，然后插入USB口。

	![image-20250317165710105](media/image-20250317165710105.png)

2. 打开FLASH_DOWNLOAD_TOOL，配置并上传固件。

	![image-20250317165727894](media/image-20250317165727894.png)![image-20250317165734340](media/image-20250317165734340.png)

	![image-20250317165745417](media/image-20250317165745417.png)

	![image-20250317165752870](media/image-20250317165752870.png)

### Station模式操作
1. 将家庭Wi-Fi的SSID设定为：KeyesWifi_S，密码设定为：KeyesWifi。

2. 将Keyes USB模块串口测试扩展板接入电脑USB，模块开关拨动到Flash Boot。

3. 使用PUTTY软件连接ESP8266，读取IP地址。

	![image-20250317165811045](media/image-20250317165811045.png)

	![image-20250317165828182](media/image-20250317165828182.png)

4. 将ESP8266-01插回DS18B20温度模块，并接入3.3V电源。

5. 使用网络调试助手发送控制信号（如PIN02=DS18B20，模块测试出所在环境中的温度数值）。

	![image-20250317165846421](media/image-20250317165846421.png)

	![image-20250317165859589](media/image-20250317165859589.png)

### SoftAP模式操作
1. 将家庭Wi-Fi更改为其他名称，防止Wi-Fi模块自动连接。

2. 将电脑连接Wi-Fi，名称为KeyesWifi_A，密码为KeyesWifi。

3. 接入DS18B20模块并接入3.3V电源，等待20秒。

4. 使用网络调试助手发送控制信号（如PIN02=DS18B20，模块测试出所在环境中的温度数值）。

	![image-20250317165914903](media/image-20250317165914903.png)

	![image-20250317165922775](media/image-20250317165922775.png)

---

## 8. 示例代码
以下是读取DS18B20温度数据的示例代码：
```cpp
#include <OneWire.h>
#include <DallasTemperature.h>
#include <ESP8266WiFi.h>

#define ONE_WIRE_BUS 2 // DS18B20 数据引脚

OneWire oneWire(ONE_WIRE_BUS);
DallasTemperature sensors(&oneWire);

const char* ssid = "KeyesWifi_S"; // Wi-Fi SSID
const char* password = "KeyesWifi"; // Wi-Fi 密码

void setup() {
  Serial.begin(115200);
  sensors.begin();
  WiFi.begin(ssid, password);
  
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi");
}

void loop() {
  sensors.requestTemperatures(); // 请求温度
  float temperature = sensors.getTempCByIndex(0); // 获取温度值
  
  Serial.print("Temperature: ");
  Serial.print(temperature);
  Serial.println(" °C");
  
  delay(2000);
}
```

---

## 9. 实验现象
在成功连接Wi-Fi后，用户可以通过网络调试助手发送控制信号，模块将返回当前的温度数据。用户可以在串口监视器中看到实时的温度值。

---

## 10. 注意事项
- **电源要求**：确保模块连接的电源电压在3V - 3.3V范围内，以避免损坏模块。
- **网络设置**：在Station模式下，确保ESP8266与电脑在同一局域网内。
- **信号干扰**：在使用SoftAP模式时，确保其他Wi-Fi网络不会干扰模块的连接。
- **复位操作**：如遇到问题，可以使用复位按键重置模块。


# esp8266 set time and get time

### 设置系统时间

```cpp
#include "time.h"
#include <ESP8266WiFi.h>

time_t epoch_t = 1651406376L; // 到秒的时间戳
struct timeval tv;
tv.tv_sec = epoch_t;
settimeofday(&tv, NULL);
```

### 获取系统时间

如果没有通过NTP服务器或其他网络服务校准时间，则返回的大概是1970年+运行的时间，所以通常会通过NTP服务器获取时间，然后调用“settimeofday(...)”来设置系统时间，后面再获取时间就获取系统时间就行了

```cpp
#include "time.h"

time_t rawtime;
time(&rawtime);
Serial.println(rawtime); // 到秒的时间戳
```

### 从NTP服务器获取时间

下面配置的NTP服务器是阿里云的

从NTP服务器获取时间后就可以设置到系统时间了，后面要获取时间直接获取系统时间就行了，不需要一直调用NTP刷新时间，隔一段时间刷新一次就行

```cpp
#include <NTPClient.h>
#include <ESP8266WiFi.h>
#include <WiFiUdp.h>

// 先要连接WiFi
WiFi.begin(ssid, password);
Serial.println("Connecting");
while(WiFi.status() != WL_CONNECTED) {
  delay(500);
  Serial.print(".");
}
Serial.println("");
Serial.print("Connected to WiFi network with IP Address: ");
Serial.println(WiFi.localIP());     

// 初始化并设置NTPClient
WiFiUDP ntpUDP;
timeClient = new NTPClient(ntpUDP, "ntp.aliyun.com"); //NTP地址
timeClient->begin();
timeClient->setTimeOffset(28800); //设置时区（+1区，偏移3600，+8区，偏移3600*8）

// 刷新并获取时间，获取时间后就可以设置系统时间，后面直接获取系统时间就行了，不需要一直刷新时间，每隔一段时间刷新一次就行
timeClient->update();
time_t epoch_t = timeClient->getEpochTime(); // 获取到秒的时间戳
Serial.println(epoch_t);
```


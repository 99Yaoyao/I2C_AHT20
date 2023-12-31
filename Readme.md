# I²C总线 - AHT20温湿度传感器（I2C_AHT20）

通过I²C读取AHT20温湿度计

板载AHT20传感器规格：（详细可参考资料包中的手册）


|    参数    |       值       |
| :--------: | :------------: |
|  芯片型号  | 广州奥松 AHT20 |
|  I²C地址   |      0x70      |
|  温度范围  |   -40 ~ 85 ℃   |
|  温度误差  |    ± 0.3 ℃     |
| 温度分辨率 |     0.01 ℃     |
|  湿度范围  |  0 ~ 100 %RH   |
|  湿度误差  |    ± 2 %RH     |
| 湿度分辨率 |   0.024 %RH    |

> **AHT20 对比 DHT20：**如图所示，DHT20内部是AHT20加上拉电阻、去耦电容，软件操作是完全相同的。

## 如何使用例程

- 编译并下载程序到学习板

- 使用配套TYPE-C数据线，将学习板连接到计算机

<img src="Doc/连接usb线.png" style="zoom:35%;" />

- 打开[波特律动 串口助手 (keysking.com)](https://serial.keysking.com/)在线串口调试助手，点击“选择串口”，选择USB Single Serial
- 此时即可收到温湿度信息，如图所示

<img src="Doc/串口.png" style="zoom:56%;" />


## 例程讲解

下面介绍了如何自己实现该例程的功能

### 1、工程配置

- **打开I²C外设：**Pinout&Configuration -> Connectivity -> I2C1，将I2C模式选择为I2C

- **打开串口2外设：**Pinout&Configuration -> Connectivity -> USART2，将Mode选择为Asynchronous

- **配置工程：**在Project Manager -> Code Generator页面中，勾选Generate peripheral initialization as ... per peripheral

- **启用float打印：**在cubeIDE菜单栏中，Project Properties -> C/C++ Build -> Settings -> Tool Settings -> MCU Settings，勾选Use float with printf ... -nano

  > 默认情况下，sprintf函数不能打印小数。因此我们需要配置一下编译器，使其能够打印小数

### 2、代码

#### (1) 初始化过程

- **拷贝库文件：**将aht20.c文件拷贝到Core -> Src目录下，将aht20.h文件拷贝到Core -> Inc目录下。
- **在main.c中添加include：**aht20.h
- **初始化AHT20：**

```c
// 初始化AHT20
AHT20_Init();
```

#### (2) 读取数据

```c
AHT20_Read(float *Temperature, float *Humidity)
```

- 预先定义两个float类型的变量，将指针传入该函数，用于接收读取结果
- 建议读取间隔大于500毫秒

### 3、AHT20与DHT20、DHT11的区别

三种型号都是广州奥松的温湿度传感器，他们的主要区别如下

- AHT20 vs DHT20
  - AHT20、DHT20在程序上完全通用，可以相互替代
  - AHT20是贴片封装，DHT系列是插针封装
  - AHT20精度比DHT20稍高一些
- DHT20 vs DHT11
  - DHT20是DHT11的全新升级产品，提升了精度、响应时间、测量范围，不推荐使用老产品。
  - DHT20是标准的I²C通信，DHT11是单总线通信


## 故障排除

### 工程建立和配置

- **找不到gpio.c文件：**工程目录中打开xxx.ioc配置文件，进入Project Manager-Code Generator，勾选Generate peripheral initialization as a pair of '.c/.h' files per peripheral，保存并重新生成代码，就有单独的gpio.c文件了

<img src="Doc/勾选生成独立初始化文件.png" style="zoom: 68%;" />

- **打开.ioc配置文件时弹出对话框”New STM32Cube firmware version available“：**因为您的软件版本比例程高，此时建议直接点击”Migrate“迁移到您的版本即可，以后再打开就不会出现了。

<img src="Doc/New version dialog.png" style="zoom: 80%;" />

### 程序下载

- **下载程序时，提示如图错误：Failed to start GDB server** 

<img src="Doc/错误提示.Png" style="zoom: 67%;" />

可能是由于上次下载程序时关闭了debug接口。请将Boot0、Boot1跳线帽切换到H，并按下RST键，再尝试下载。如果此时下载成功，将跳线帽切换回L，按下RST键后程序即正常运行。

**注意：为了避免下次出现该问题**，请在工程配置中，找到System Core -> SYS -> Debug，选择Serial Wire，重新生成代码。这样即可避免程序将debug接口关闭导致的下载失败。

<img src="Doc/选择debug模式.png" style="zoom:60%;" />

- **下载程序时，提示“需要升级ST-LINK固件”，如图所示**

<img src="Doc/需要升级STLINK固件.png" style="zoom:60%;" />

升级ST-LINK固件即可解决，请按如下步骤操作：

1、 顶部工具栏 Help -> ST-LINK更新，打开固件升级工具。

<img src="Doc/STLINK更新.png" style="zoom:60%;" />

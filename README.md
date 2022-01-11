# ESP32_nes_gamepad

基于esp32的开源NES游戏掌机，使用arduino软件编程，方便移植和DIY，游戏文件支持大文件（使用psram技术），游戏文件存放于存储卡中，游戏数量可以999+，充分享受游戏乐趣。

 原理图及pcb见立创开源社区：https://oshwhub.com/blueskyer/ji-yuesp32-diarduino-ruan-jian-bian-cheng-dines-mu-ni-qi-you-hu-ji     
 
 

目前看立创开源社区有两种esp32的游戏机，一个只能放到esp32的rom空间，游戏数量有限，另外一个就是espplay了，espplay很强大，但是音频电路很复杂而且让成本飙升，另外这2种游戏机都用IDF编写的，一般只能刷bin文件，想要移植很难，毕竟IDF安装到编译都挺复杂的，更别说去修改源码了。于是就在网上搜，搜到了这个项目，基于arduino编程的，原始项目只能运行第一个游戏，无法选择，我稍微修改了一下，支持选择游戏文件了。

 QQ技术群：801516857

原始项目地址：

https://www.instructables.com/Arduino-NES/

https://github.com/moononournation/arduino-nofrendo

 

其实在appcardv3已经完成了，但是那个按键实在有些小，手感也不怎么好，就有了这个新的版本，包含一个基板和面板。

 

一，硬件部分

1，pcb及焊接

pcb打板，基板无厚度要求，面板最好使用铝基板，另外尽量薄一些，0.8-1mm最合适。

焊接时，蜂鸣器引脚多的需要剪掉，其他无要求

2，组装

使用M3螺丝，M3双通铜柱，M3垫片
M3螺丝，8mm以上长度
M3双通铜柱根据你的电池厚度
M3垫片，厚度0.5mm，用于垫前面板和基板，背板及铜柱

视频中是因为垫片还没到货就没展示。

 

二，软件部分

可以直接烧写固件，也可以自行编译

1，直接烧写固件

（1）打开flash_download_tool后开发板选择esp32 。



（2）然后点击3个…选择正确的bin文件位置（不要手动输入），然后勾选，@后面填烧写地址0x00000，SPI SPEED(SPI 速度)选择40MHz，SPI MODE（SPI模式） 选择QIO，FLASH SIZE（内存大小）选择32Mbit(换算成M，需要除以8，也就是4M，如果你买的esp32 wrover是其他容量，自行修改)，DoNotChgbin（不改变bin文件）勾选，端口COM选择正确的端口（windows设备管理器-端口里面，有ch340的那个），BAUD（烧写速率）选择921600, 然后点击 “START”开始烧写。

 



 

 

（3）烧写完成后，会显示“FINISH 完成”，需要拔线或者手动关掉开关重启

 

（4）上述是首次使用esp32 wrover芯片教程，如果你之前刷过其他程序，可能调用了eeprom区，为防止数据异常，需要先点击“ERASE”进行清除，清除后再进行步骤（1）-（3）的操作。



 

 

2，程序使用

 

（1）将nes游戏固件拷贝到存储卡根目录，nes游戏名一般是.nes后缀的。

（2）开机后如果提示存储卡挂载失败（显示“Filesystem mount failed! try to restart.”），可以尝试重启，若两三次重启后仍然无法使用，请检查存储卡或者pcb电路。

（3）开机存储卡挂载正常后显示一张图片，显示”waiting”界面。
 
（4）这个界面下，使用left和right按键可以调整亮度，立即生效，left减10，right加10，亮度最小10，最大255，支持重启后记住之前的设定值。（按键定义参照之前的“4.appcardv3软件使用说明.pdf”，下同）

（5）这个界面下，按一次A键，则会浏览根目录文件，如果不是游戏文件，则会显示“Not a game file, please select again.”，需要继续按A键继续选择，直到出现.nes的游戏文件。

（6）出现.nes游戏文件名显示后，按mode按键即可开始游戏。

（7）后续根据游戏中的按键提示进行操作即可。

 

 

3，自行编译程序

 

(1)，将libraries文件夹两个库文件放置arduino的libraries目录，否则会编译失败。

(2)，arduino需要安装esp32开发板，教程自行搜索

(3)，arduino编译时，板子类型选择 esp32 wrover module，com口选择正确。

 

 

好了，这就是esp32gamepad的内容，高清演示视频见：https://www.bilibili.com/video/BV1Rb4y1n7Ta/

欢迎点赞和收藏

 

到这里还没完结，如果你手里只有esp32 wroom或者是其他屏幕怎么办，这里简单的讲述一下移植办法，新手可以跳过这部分内容。

 

第一步，先说明一下源代码文件包含什么

esp32gamepad.ino，主文件
controller.cpp，控制器，主要是按键类型和摇杆类定义
display.cpp，显示屏驱动文件
hw_config.h，文件系统定义，音频引脚及按键引脚
osd.c ，操作系统处理的一些函数
sound.c，音频系统一些函数

一般只需要修改esp32gamepad.ino，display.cpp，hw_config.h这3个文件

 

第二步，esp32 wroom和wrover区别

具体区别这里就不说了，这里只说2个，一个就是封装大小不同，如果你用wroom就得重新画原理图和pcb图了。

另外一个就是wroom没有psram（片外内存区，这个内存是运行内存，不是存储文件的那个内存），而wrover有。

psram有什么作用？游戏机处理是将nes文件加载到运行内存中的，一般小游戏文件一两百kb文件是没问题的，

但是游戏文件如果是1M呢，esp32默认的运行内存是装载不了的，这就需要片外ram了，psram大小有8M但是

实际只有4M可用，这样就足够1M大小的游戏文件运行了。

知道这点就好修改了，对应的文件是osd.c的28行内容

 

/* memory allocation */
extern void *mem_alloc(int size, bool prefer_fast_memory)
{
    if (prefer_fast_memory)
    {
        return heap_caps_malloc(size, MALLOC_CAP_8BIT);
    }
    else
    {
        return heap_caps_malloc_prefer(size, MALLOC_CAP_SPIRAM, MALLOC_CAP_DEFAULT);
    }
}

 

如果用wroom，将else里面的return改为和if里面一样的，同时注意游戏文件不能超过运行内容。

修改方法未验证，所以除非手里有多的esp32 wroom，否则建议还是直接买wrover完事。

 

第三步，屏幕移植

如果你用的其他屏幕（最低分辨率得240*240），想要移植的话

修改display.cpp文件，第47行else之后内容

 

#else

//显示屏亮度

#define TFT_BRIGHTNESS 128 /* 0 - 255 */

//显示屏控制背光引脚（背光负极）

#define TFT_BL 22

//显示屏驱动，定义spi引脚，miso可以不接为-1

Arduino_DataBus *bus = new Arduino_ESP32SPI(27 /* DC */, 5 /* CS */, 18 /* SCK */, 23 /* MOSI */, -1 /* MISO */);

//定义总线类型，其中rotation是旋转角度，IPS是否高清屏
Arduino_ST7789 *gamegfx = new Arduino_ST7789(bus, 26 /* RST */, 3 /* rotation */, false /* IPS */);

 

我的屏幕是7789，如果需要其他类型屏幕，可在库Arduino_GFX\src\display这个文件夹中查找，有列的就是支持的。

 

第四步，按键及文件系统修改

 

hw_config.h的第105行else之后开始

#else

//存储方式，21是SD的CS引脚，需要与显示屏的不同
#define FILESYSTEM_BEGIN SD.begin(21, SPI, 40000000, FSROOT); FS filesystem = SD;

//蜂鸣器音频
/* buzzer audio */
#define HW_AUDIO_BUZZER

//蜂鸣器引脚
#define HW_AUDIO_BUZZER_PIN 13
#define HW_AUDIO_SAMPLERATE 22050
// nofrendo minimum sample rate
//最低采样率，根据实际调整，默认22050

//按键引脚，X和Y我没用到，就分配到不使用的IO上了。
/* controller is GPIO */
#define HW_CONTROLLER_GPIO
//#define HW_CONTROLLER_GPIO_ANALOG_JOYSTICK
#define HW_CONTROLLER_GPIO_UP 39
#define HW_CONTROLLER_GPIO_DOWN 36
#define HW_CONTROLLER_GPIO_LEFT 35
#define HW_CONTROLLER_GPIO_RIGHT 34
#define HW_CONTROLLER_GPIO_SELECT 32
#define HW_CONTROLLER_GPIO_START 25
#define HW_CONTROLLER_GPIO_A 14
#define HW_CONTROLLER_GPIO_B 33
#define HW_CONTROLLER_GPIO_X 0
#define HW_CONTROLLER_GPIO_Y 2

 

音频有2种方式，一个是蜂鸣器，还有一个走DAC的，走DAC的可以参考注释的案例，注意引脚不能和其他引脚冲突。

 

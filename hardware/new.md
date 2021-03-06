# SPI interface

串行外设接口（Serial Peripheral Interface）是一种同步串行通信接口规范，广泛用于IC控制。因为SPI是同步接口，它的速度比UART等异步协议（一般最高1MHZ）快，能到数十MHz。由于SPI是单端接口而不是差分接口，不适合长距离（比如远于1米）传输

SPI有一个Master和若干个Slave，一般Master就是MCU，Slave是各种IC，如存储器

![](./images/SPI_single_slave.png)

有四个控制信号：

1. SCLK: Serial Clock
2. MOSI: Master Out Slave In
3. MISO: Master In Slave Out
4. <span style="text-decoration:overline">SS</span>: Slave Select

传输总是由Master发起，拉低SS之后就开始双向传输，没有其他的交流

有四种模式，Master和Slave必须用同一种模式：

<img src="./images/SPI_timing_diagram.png" style="zoom:67%;" />

其中，CPOL（Clock Polarity）定义了静息时的SCK电位高低，CPHA（Clock Phase）定义了信号与时钟的哪个沿对齐。两者排列组合就得到四种模式
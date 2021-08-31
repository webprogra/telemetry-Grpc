# telemetry-Grpc 
## 自动化运维 
### get 主动获取状态和配置信息 
      运维平台按需从交换机设备上获取关键配置信息或者软、硬件状态信息，配置信息如BGP配置、安全配置等，  
      状态信息如接口流量、接口状态、Buffer队列长度、丢包等等；满足机房巡检、故障排查等需求。 
### set 主动下发配置 
      运维平台按需对交换机下发变更配置，比如Shutdown端口、配置IP地址、配置水线阈值等；满足日常的业务变更需求。 
### alarm 主动上报异常状态 
      交换机内部，当满足一定触发条件后主动上报运维平台的Notification信息，比如CPU利用率超过安全阈值、队列水线达到阈值、端口Up/Down等；满足对异常状态的告警需求。 
### push 主动上报关键状态信息 
      设备端周期性主动上报一些状态信息，比如接口流量、队列水线、接口错包等；满足关键指标的持续监控需求。 
## 理想的北向运维接口 
### 北向运维接口 
北向接口：提供给其他厂家或运营商进行接入和管理的接口，即向上提供的接口。 
### 南向运维接口 
南向接口：管理其他厂家网管或设备的接口，即向下提供的接口。 
### 理想的北向运维接口
厂商无关性， YANG模型标准化， 全面的运维能力， 单一的运维接口， 结构化北向接口， 直观高效的数据描述， 统一树状YANG模型， 高效的数据传输， 基于RPC实现远程调用解耦， 安全可靠的数据传输。 
# gRRC 模型 
![image](https://user-images.githubusercontent.com/29565385/131430732-335b11b2-713a-47c6-b7f4-56bcdddf6454.png)
## 优势 
从运维角度，可以通过自动化平台收集信息，快速对网络进行适配，提升运维效率，从而打造更加可用、可靠、可控的网络来服务好业务。
## telemetry 
![image](https://user-images.githubusercontent.com/29565385/131456488-8de81d77-7ee5-4840-8ae7-a2dc0e426eb1.png)  
Telemetry=原始数据+数据模型+编码格式+传输协议 
## gRPC框架 
![image](https://upimg.ruijie.com.cn/Editor/Image/20190513180307/03.jpg) 
## gRPC交互过程（dial-out静态订阅）
![image](https://user-images.githubusercontent.com/29565385/131457959-9a78dbb1-1478-4055-95df-e56a39fcf5ac.png)  

●交换机在开启gRPC功能后充当gRPC客户端的角色，采集服务器充当gRPC服务器角色；

●交换机会根据订阅的事件构建对应数据的格式（GPB/JSON），通过Protocol Buffers进行编写proto文件，交换机与服务器建立gRPC通道，通过gRPC协议向服务器发送请求消息；

●服务器收到请求消息后，服务器会通过Protocol Buffers解译proto文件，还原出最先定义好格式的数据结构，进行业务处理；

●数据梳理完后，服务器需要使用Protocol Buffers重编译应答数据，通过gRPC协议向交换机发送应答消息；

●交换机收到应答消息后，结束本次的gRPC交互。 
## protocol buffers 
      更加灵活、高效的数据格式,在一些高性能且对响应速度有要求的数据传输场景非常适用。 
### 作用 
1. 定义数据结构  
3. 定义服务接口 
4. 序列化与反序列化，提升传输效率 （Protocol Buffers是以二进制的形式进行传输的）
5. 跨平台，多语言   
![image](https://upimg.ruijie.com.cn/Editor/Image/20190513180402/09.jpg)  
### 优势 
●简单，体积小，数据描述文件大小只有1/10至1/3；

●传输和解析的速率快，相比XML等，解析速度提升20倍甚至更高；

●可编译性强。  
## 基于HTPP2.0设计  
### 概念
**HTTP 1.X** 定义了四种与服务器交互的方式，分别为：GET、POST、PUT、DELETE，传输基本单位为文本数据  

**HTTP 2.0** 
1. GET、POST、PUT、DELETE, 
2. 双向流，多路复用（同时通过一条连接发起多个“请求-响应”消息）， 
3. HTTP 2.0传输的基本单元为二进制帧(传输数据体积小、负载低，保持更加紧凑和高效), 
4. 头部压缩  
因为HTTP是无状态协议，对于业务的处理没有记忆能力，每一次请求都需要携带设备的所有细节，特别是在头部都会包含大量的重复数据，对于设备来说就是在不断地做无意义的重复性工作。HTTP 2.0中使用“头表”来跟踪之前发送的数据，对于相同的数据将不再使用重复请求和发送，进而减少数据的体积。

# 总结 
基于gRPC的通信，客户端和服务端肯定要定义**proto文件**，需要通过proto文件定义服务接口，具体就是一些**原子操作**，比如Get、Set、Notification、Subscribe等，但是具体的**数据模型**，到底是基于JSON模型还是YANG模型，从简单维护和易扩展的角度，更加推荐YANG模型，但关键的难点，如何统一YANG模型，这个还需要进一步探索。

---
title: socket网络通信
categories:
  - 网络通信
tags:
  - java
  - socket
abbrlink: 4a0d6d40
date: 2024-03-10 00:00:00
cover:
---

## Socket通信的两种方式

### 一、TCP协议通信

```java
try {
    // 创建一个Socket对象，指定目标IP地址和端口号
    Socket socket = new Socket("127.0.0.1", 18080);
    // 获取输出流
    OutputStream outputStream = socket.getOutputStream();
    
    // 准备要发送的数据
    String message = "Hello world!";
    byte[] sendData = message.getBytes();
    
    // 发送数据
    outputStream.write(sendData);
    outputStream.flush();
    
    // 关闭Socket连接
    socket.close();
} catch (Exception e) {
	e.printStackTrace();
}
```



### 二、UDP协议通信

```java


try {
    // 创建 DatagramSocket 对象
    DatagramSocket socket = new DatagramSocket();
    
    // 服务器地址和端口
    InetAddress serverAddress = InetAddress.getByName("127.0.0.1");
    int serverPort = Integer.parseInt(18080);
    
    // 准备要发送的数据
    String message = "Hello world!"
    byte[] sendData = message.getBytes();
    
    // 创建 DatagramPacket 对象，包含要发送的数据和服务器地址信息
    DatagramPacket sendPacket = new DatagramPacket(sendData, sendData.length, serverAddress, serverPort);
    
    // 发送数据
    socket.send(sendPacket);
    
    // 关闭 socket
    socket.close();
} catch (IOException e) {
    e.printStackTrace();
}
```



### 三、两种通信协议的总结

UDP（User Datagram Protocol）和TCP（Transmission Control Protocol）是两种不同的传输层协议，用于在网络上发送数据。它们在以下几个方面有所不同：

1. **连接性：**TCP是面向连接的协议，它在通信之前建立连接，并确保数据的可靠传输。UDP是无连接的协议，它不需要在通信之前建立连接。
2. **可靠性：**TCP提供可靠的数据传输，它使用序列号、确认和重传机制来确保数据的可靠性。UDP不提供可靠性保证，它以尽最大努力交付数据，但不保证数据的顺序、完整性或可靠性。
3. **速度和效率：**由于TCP的连接管理和可靠性机制，它的传输速度相对较慢，并且在网络拥塞时可能会有较高的延迟。UDP没有这些机制，因此传输速度较快，延迟较低。
4. **数据大小限制：**TCP对发送的数据进行分段，并确保它们按正确的顺序组装。它没有严格的数据大小限制。UDP的数据包大小有限制，每个数据包的最大大小为64KB。
5. **适用场景：**TCP适用于需要可靠数据传输和按顺序传输的应用程序，例如文件传输、电子邮件和Web浏览器。UDP适用于对实时性要求较高的应用程序，例如音频/视频流传输、实时游戏和实时通信。

总之，TCP提供可靠的、面向连接的通信，适用于对数据可靠性和顺序要求较高的应用。UDP提供快速、无连接的通信，适用于实时性要求较高且可靠性要求相对较低的应用。选择使用哪种协议取决于应用的需求和特定的使用情境。

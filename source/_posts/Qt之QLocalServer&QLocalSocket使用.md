---
title: Qt之QLocalServer&QLocalSocket使用
date: 2017-08-10 19:47:43
categories: Qt
tags: QLocalSocket
---

> QLocalSocket类提供了一个本地的socket。在Windows中，这是一个命名管道；在Unix中，这是一个本地网域socket。提供IPC服务
> 如果发生错误，socketError()会返回错误的类型，errorString()则返回人类可读的错误描述。

> 虽然QLocalSocket是一个事件循环使用而设计，它也可以不被如此使用。这种情况下，必须使用waitForConnected()、waitForReadyRead()、waitForBytesWritten()、和waitForDisconnected()来进行阻塞，直到操作完成或超时。

<!--more-->

** 一、 QLocalServer 服务端 **
服务端通过listen接口监听，获取来自客户端的信息。
- 建立连接
```
m_server = new QLocalServer();
// 先移除原来存在的,如果不移除那么如果
// servername已经存在就会listen失败
QLocalServer::removeServer(strServerName);
// 监听
m_server->listen(serverName);
connect(m_server, SIGNAL(newConnection()), this, SLOT(onNewConnection()));
```
- 获取连接
```
void onNewConnection()
{
    m_socket = m_server->nextPendingConnection();
    m_socket->setReadBufferSize(0);
    connect(m_socket, SIGNAL(readyRead()), this, SLOT(onGetData()));
}
```

** 二、 QLocalSocket 客户端 **
客户端连接服务端，想服务端发送消息
```
m_socket = new QLocalSocket();
socket->connectToServer(serverName);
```

** 三、 传递信息 **
当客户端需要向服务端传递信息的时候，向socket写入数据
```
// 发送
QDataStream ds(m_socket);
ds << byteData;
m_socket->waitForBytesWritten();

// 接收
QDataStream dsIn(m_socket);
dsIn >> byteData;
```
此种方式，当数据发送间隔短，会导致数据被QDataStream截断，导致数据不完整, 修改如下：
***

- 发送数据
```
void sendData(QByteArray byteData)
{
	if (m_socket->waitForConnected())
	{
		QDataStream ds(m_socket);
		//ds << byteData;//此种方式，当数据发送间隔短，会导致数据被QDataStream截断，导致数据不完整
		char *buffer = byteData.data();
		quint32 len = strlen(buffer);
		// 先写入要传递数据的长度
		ds.writeRawData((char*)&len, sizeof(quint32));
		// 写入数据
		ds.writeRawData(buffer, len);
		m_socket->flush();
		m_socket->waitForBytesWritten();
	}
}
```
- 接收数据
```
void onGetData()
{
	if (!m_socket)
		return;
	m_socket->waitForReadyRead();
	m_socket->flush();

	QDataStream dsIn(m_socket);
	while (!dsIn.atEnd())
	{
		// 获取可用数据长度
		qint64 avaiableLen = m_socket->bytesAvailable();
		if (m_bReadLen)
		{
			if (availableLen >= sizeof(m_nLenWaitFor))
			{
				dsIn.readRawData((char*)(&m_nLenWaitFor), sizeof(m_nLenWaitFor));
				m_bReadLen = false;
			}
			else
			{
				break;
			}
		}

		// 获取数据
		availableLen = m_socket->byteAvailable();
		if (availableLen >= m_nLenWaitFor)
		{
			char *buffer = new char[m_nLenWaitFor + 1];
			dsIn.readRawData(buffer, m_nLenWaitFor);
			buffer[m_nLenWaitFor] = '\0';
			QByteArray byteData(buffer);
			delete buffer;
			m_bReadLen = true;
		}
	}
}
```
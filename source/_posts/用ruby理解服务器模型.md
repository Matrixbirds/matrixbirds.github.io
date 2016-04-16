title: 用ruby理解服务器模型
date: 2016-04-15 00:33:14
tags:
  - ruby
  - web开发
---

#### 前言
看别人写了篇[用 Python 理解服务器模型(上)](https://www.textarea.com/zhicheng/yong-python-lijie-fuwuqi-moxing-shang-566/)
感觉很不错。于是自己就心血来潮写一篇用[Ruby理解服务器模型](#)


#### 一个简单的服务器实现(基于TCP)
```ruby
require 'socket'

server = TCPServer.new('127.0.0.1', 3000)

# loop infinitely, processing one incoming
loop do

  socket, request, response = server.accept, socket.gets, "Hello World\n"

  STDOUT.puts request

  socket.print "HTTP/1.1 200 OK\r\n" +
               "Content-Type: text/plain\r\n" +
               "Content-Length: #{response.bytesize}\r\n" +
               "Connection: close\r\n"

  socket.print "\r\n"

  socket.print response

  socket.close
end
```
使用TCPServer创建了一个负责监听3000端口的socket


####

Socket

1)发送消息案例

```python
#客户端
import socket
print("clent is runing")
try:
    s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
    s.connect(("localhost",8001))
    b=bytes("你好服务器","utf8")
    s.send(b)
except Exception as e:
    print(e)
```

```python
#服务端
import socket
print("servre is runing")
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.bind(("localhost",8001))
s.listen(1)
while True:
    con,adress=s.accept()
    msg=con.recv(1024)
    print(msg.decode("utf8"))
```

2）发送文件案例

```python
#客户端
import socket
print("clent is runing")
try:
    s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
    s.connect(("localhost",8001))
    f=open("C:\\Users\\admin\\Desktop\\java_base\\15.jpg","rb")
    while True:
        d=f.readline()
        if d:
            s.send(d)
        else:
            break
    f.close()
    s.close()
except Exception as e:
    print(e)
```

```python
#服务端
import socket,time,random
print("servre is runing")
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.bind(("localhost",8001))
s.listen(1)
while True:
    con,adress=s.accept()
    print("收到请求")
    filename="C:\\Users\\admin\\Desktop\\java_base\\fl"+str(time.time())+str(random.random())+".jpg"
    f=open(filename,"wb")
    while True:
        msg=con.recv(1024)
        if msg:
            f.write(msg)
        else:
            break
    f.close()
    print("上传成功")
```


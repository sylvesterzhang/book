---
layout: post
title: JAVA机考题
tags: JAVA机考题
categories: backends
---

二叉排序数及中序遍历实现，socket应用，日志模拟的实现

# 试题一

~~~
/**
 * 实现要求：
 * 1、根据已有的代码片段，实现一个二叉排序树；
 * 2、用中序遍历输出排序结果，结果形如：0，1，2 ，3 ，4， 5， 6， 7， 8， 9，
 * 3、注意编写代码注释
 */

public class BinaryTree {

	public static void main(String[] args) {
		
		final int[] values = { 1, 3, 4, 5, 2, 8, 6, 7, 9, 0 };
		// TODO:
	}

	public static Node createBinaryTree(Node node, int value) {
		// TODO:
	}

	public static void inOrderTransval(Node node) {
		// TODO:
	}
}

class Node {

 	// 节点值
	private int value;

 	// 左节点
	private Node left;

	// 右节点
	private Node right;

	// TODO:
}
~~~

**答案**

```java
public class BinaryTree {

    public static void main(String[] args) {

        final int[] values = { 1, 3, 4, 5, 2, 8, 6, 7, 9, 0 };
        // TODO:
        //创建根节点
        Node root=new Node(values[0]);

        //将数组中的数追加到根节点下
        for (int i = 1; i <values.length ; i++) {
            createBinaryTree (root,values[i]);
        }

        //中序遍历根节点
        inOrderTransval (root);
    }

    public static Node createBinaryTree(Node node, int value) {
        // TODO:
        //为排序二叉树追加子节点
        /*
        @param node 传入的根节点对象
        @param value 需要插入到二叉树的值
        @return 返回插入二叉树后该值对应的节点对象
         */
        Node return_node=null;
        if (node.getValue ()>value){
            if(node.getLeft ()!=null){
                node=node.getLeft ();
                createBinaryTree (node,value);
            }else{
                Node node1=new Node(value);
                node.setLeft (node1);
                return_node=node1;
                return return_node;
            }
        }else{
            if(node.getRight ()!=null){
                node=node.getRight ();
                createBinaryTree (node,value);
            }else{
                Node node1=new Node(value);
                node.setRight (node1);
                return_node=node1;
                return return_node;
            }
        }

        return return_node;
    }

    public static void inOrderTransval(Node node) {
        // TODO:
        //中序遍历
        if(node==null){
            return;
        }
        BinaryTree.inOrderTransval (node.getLeft ());
        System.out.println (node.getValue ());
        BinaryTree.inOrderTransval (node.getRight ());
    }
}

class Node {

    // 节点值
    private int value;

    // 左节点
    private Node left;

    // 右节点
    private Node right;

    // TODO:
    //构造方法
    //getter和setter方法
    
    public Node(int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }

    public void setValue(int value) {
        this.value = value;
    }

    public Node getLeft() {
        return left;
    }

    public void setLeft(Node left) {
        this.left = left;
    }

    public Node getRight() {
        return right;
    }

    public void setRight(Node right) {
        this.right = right;
    }

}
```



# 试题二

~~~
/**
 * 实现要求：
 * 1、根据代码片段实现一个简单的SOCKET ECHO程序；
 * 2、接受到客户端连接后，服务端返回一个欢迎消息;
 * 3、接受到"bye"消息后， 服务端返回一个结束消息，并结束当前连接;
 * 4、支持通过telnet连接本服务端，并且可正常运行；
 * 5、注意代码注释书写。
 *
 */
public class EchoApplication {

	public static void main(String[] args) throws IOException, InterruptedException {

		final int listenPort = 12345;

		// 启动服务端
		EchoServer server = new EchoServer(listenPort);
		server.startService();

		// 启动客户端
		EchoClient client = new EchoClient(listenPort);
		client.startService();

		// 在控制台输入，服务端直接原文返回输入信息
		// 控制台结果示例：
		/**
			2020-03-31 16:58:44.049 - Welcome to My Echo Server.(from SERVER)
			
			Enter: hello!
			2020-03-31 16:58:55.452 - hello!(from SERVER)
			
			Enter: this is koal.
			2020-03-31 16:59:06.565 - this is koal.(from SERVER)
			
			Enter: what can i do for you?
			2020-03-31 16:59:12.828 - what can i do for you?(from SERVER)
			
			Enter: bye!
			2020-03-31 16:59:16.502 - Bye bye!(from SERVER)
		 */
	}

}

class EchoServer {
	// TODO
}

class EchoClient {
	// TODO
}

~~~

**答案**

```java
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;
import java.util.Scanner;

public class EchoApplication {

    public static void main(String[] args) throws IOException, InterruptedException {

        final int listenPort = 12345;

        // 启动服务端
        EchoServer server = new EchoServer(listenPort);
        server.startService();

        // 启动客户端
        EchoClient client = new EchoClient(listenPort);
        client.startService();

    }

}

class EchoServer {
    // TODO
    ServerSocket ss=null;
    public EchoServer(int listenPort) throws IOException {
        //初始化服务端
        ss=new ServerSocket(listenPort);
    }

    public void startService(){

        //为服务端单独开启一个线程
        new Thread (()->{
            try {
                Socket s=ss.accept();//开始监听
                
                //向客户端发送问候
                s.getOutputStream ().write ("Welcome to My Echo Server.".getBytes ());
                
                byte[] message_from_client=new byte[1024];//缓冲数组
                
                while (true){
                    InputStream is=s.getInputStream();
                    OutputStream os=s.getOutputStream ();
                    int len=is.read (message_from_client);
                    if(len==-1){
                        break;
                    }
                    byte[] message_from_client_handled=Arrays.copyOfRange (message_from_client,0,len);
                    
                    //收到客户端发来关闭信息时，关闭服务端，并返回关闭成功信息
                    if(new String (message_from_client_handled).equals ("bye")){
                        os.write ("Bye bye!".getBytes ());
                        s.close ();
                        ss.close ();
                        break;
                    }
                    os.write (message_from_client_handled);

                }
            } catch (IOException e) {
                e.printStackTrace ( );
            }
        }).start ();
    }

}

class EchoClient {
    // TODO
    Socket s=null;
    public EchoClient(int listenPort) throws IOException {
        //初始化客户端
        s=new Socket("localhost",listenPort);
    }
    public void startService() throws IOException {
        OutputStream os=s.getOutputStream();//输出流
        InputStream is=s.getInputStream();//输入流

        byte[] message_from_server=new byte[1024];//缓冲数组
        SimpleDateFormat sdf=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

        //连接成功，打印服务端传过来的问候语
        int len=is.read (message_from_server);
        System.out.println (sdf.format (new Date ())+" - " +new String (Arrays.copyOfRange (message_from_server,0,len))+" (from server)");
        
        while (true){
            Scanner sc = new Scanner(System.in);
            byte[] to_server_msg=sc.nextLine ().getBytes ();
            os.write (to_server_msg);
            len=is.read (message_from_server);
            if(len==-1){
                break;
            }
            System.out.println (sdf.format (new Date ())+" -" +new String (Arrays.copyOfRange (message_from_server,0,len))+" (from server)");
            
            //确认收到服务端关闭信息，关闭客户端
            if(new String (Arrays.copyOfRange (message_from_server,0,len)).equals ("Bye bye!")){
                s.close ();
                break;
            }

        }

    }

}
```



# 试题三

~~~
/**
 * 实现要求：
 * 1、根据以下代码片段，参考log4j/slf4j等公共日志库编写一个自定义的简易日志类；
 * 2、至少需要支持文件输出、控制台输出二种日志输出方式，并支持同时输出到文件和控制台；
 * 3、支持DEBUG/INFO/WARN/ERROR四种日志级别；
 * 4、请合理进行设计模式，进行接口类、抽象类等设计，至少包括FileLogger、ConsoleLogger二个子实现类；
 * 5、注意代码注释书写。
 */

public class KLLogger {

	public static void main(String[] args) {

		final KLLogger logger1 = KLLogger.getLogger(KLLogger.class);

		// TODO:
		
		logger1.setLogLevel(DebugLevel.DEBUG);

		logger1.debug("debug 1...");
		logger1.debug("debug 2...");
		logger1.info("info 1...");
		logger1.warn("warn 1...");
		logger1.error("error 1...");

		final KLLogger logger2 = KLLogger.getLogger(KLLogger.class);
		logger2.setLogLevel(DebugLevel.INFO);

		logger2.debug("debug 1...");
		logger2.debug("debug 2...");
		logger2.info("info 1...");
		logger2.warn("warn 1...");
		logger2.error("error 1...");

	}
}

// TODO:
class XXX1 {
}

// TODO:
class XXX2 {
}

enum DebugLevel {
	DEBUG, INFO, WARN, ERROR;
}

~~~

**答案**

```java
import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class KLLogger {

    public static void main(String[] args) {

        final KLLogger logger1 = KLLogger.getLogger(KLLogger.class);

        // TODO:
        //记录DEBUG级别以上的日志
        logger1.setLogLevel(DebugLevel.DEBUG);

        logger1.debug("debug 1...");
        logger1.debug("debug 2...");
        logger1.info("info 1...");
        logger1.warn("warn 1...");
        logger1.error("error 1...");

        System.out.println ("---------------------------------------------");

        final KLLogger logger2 = KLLogger.getLogger(KLLogger.class);
        //记录INFO级别以上的日志
        logger2.setLogLevel(DebugLevel.INFO);

        logger2.debug("debug 1...");
        logger2.debug("debug 2...");
        logger2.info("info 1...");
        logger2.warn("warn 1...");
        logger2.error("error 1...");

    }

    //将要进行日志记录的类
    private Class cls;

    //日志级别
    private Enum loglevel;

    //文件日志记录器
    static private Appender fileLogger=new FileLogger ();

    //控制台日志记录器
    static private Appender consoleLogger=new ConsoleLogger ();

    //文件日志输出路径
    public static String log_out_put_dir="D:\\log.txt";

    //时间格式化对象
    private SimpleDateFormat simpleDateFormat=new SimpleDateFormat ("yyyy-MM-dd HH:mm:ss");

    //构造方法，将要进行日志记录的类传入
    public KLLogger(Class cls) {
        this.cls=cls;
    }

    //日志对象的构建方法
    public static KLLogger getLogger(Class cls){
        return new KLLogger (cls);
    }

    //设置日志记录级别
    public void setLogLevel(Enum e){
        this.loglevel=e;
    }

    //生成debug类型日志
    public void debug(String message){
        if(this.loglevel.equals (DebugLevel.INFO) | this.loglevel.equals (DebugLevel.WARN) | this.loglevel.equals (DebugLevel.ERROR)){
            return;
        }
        consoleLogger.doAppend (this.cls,message,simpleDateFormat.format (new Date ()));
        fileLogger.doAppend (this.cls,message,simpleDateFormat.format (new Date ()));
    }

    //生成info类型日志
    public void info(String message){
        if(this.loglevel.equals (DebugLevel.WARN) | this.loglevel.equals (DebugLevel.ERROR)){
            return;
        }
        consoleLogger.doAppend (this.cls,message,simpleDateFormat.format (new Date ()));
        fileLogger.doAppend (this.cls,message,simpleDateFormat.format (new Date ()));
    }

    //生成warn类型日志
    public void warn(String message){
        if(this.loglevel.equals (DebugLevel.ERROR)){
            return;
        }
        consoleLogger.doAppend (this.cls,message,simpleDateFormat.format (new Date ()));
        fileLogger.doAppend (this.cls,message,simpleDateFormat.format (new Date ()));
    }

    //生成error类型日志
    public void error(String message){
        consoleLogger.doAppend (this.cls,message,simpleDateFormat.format (new Date ()));
        fileLogger.doAppend (this.cls,message,simpleDateFormat.format (new Date ()));
    }
}

// TODO:
//文件中记录日志
class FileLogger implements Appender {
    FileWriter fileWriter= null;
    @Override
    public void doAppend(Class cls,String message,String time){
        try {
            //将日志写入到文件
            FileWriter fw=getFileWriter ();
            fw.append (time+" "+cls.getName ()+" "+message+System.getProperty("line.separator", "\n"));
            fw.flush ();
        } catch (IOException e) {
            e.printStackTrace ( );
        }
    }
    //获取FileWriter（单例）
    private FileWriter getFileWriter(){
        if(fileWriter==null){
            try {
                File file=new File (KLLogger.log_out_put_dir);
                //判断文件是否存在，如果不存在则重新创建
                if(!file.exists ()){
                    file.createNewFile ();
                }
                fileWriter = new FileWriter (file,true);
            } catch (IOException e) {
                e.printStackTrace ( );
            }
        }
        return fileWriter;
    }
}

// TODO:
//控制台打印日志
class ConsoleLogger implements Appender {
    @Override
    public void doAppend(Class cls,String message,String time) {
        System.out.println (time+" "+cls.getName ()+" "+message );
    }
}

//日志输出接口
/*
@Param cls 调用日志的类的class
@Param message 日志信息
@Param time 当前时间
 */
abstract interface Appender{
    void doAppend(Class cls,String message,String time);
}
enum DebugLevel {
    DEBUG, INFO, WARN, ERROR;
}

```


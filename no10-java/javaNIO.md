内容：  
```
1. Java NIO 简介
2. Java NIO 与 IO 的主要区别
3. 缓冲区(Buffer)和通道(Channel)
4. 文件通道(FileChannel)
5. NIO 的非阻塞式网络通信
 选择器(Selector)

1  Java NIO 简介
Java NIO（New IO） 是从Java 1.4版本开始引入的
一个新的IO API，可以替代标准的Java IO API。
NIO与原来的IO有同样的作用和目的，但是使用
的方式完全不同， NIO支持面向缓冲区的、基于
通道的IO操作。 NIO将以更加高效的方式进行文
件的读写操作
2 Java NIO 与 IO 的主要区别
IO	NIO
面向流(Stream Oriented) 	面向缓冲区(Buffer Oriented)
阻塞IO(Blocking IO) 	非阻塞IO(Non Blocking IO)
(无) 	选择器(Selectors)

3通道和缓冲区
 Java NIO系统的核心在于：通道(Channel)和缓冲区
(Buffer)。通道表示打开到 IO 设备(例如：文件、
套接字)的连接。若需要使用 NIO 系统，需要获取
用于连接 IO 设备的通道以及用于容纳数据的缓冲
区。然后操作缓冲区，对数据进行处理。
简而言之， Channel 负责传输， Buffer 负责存储 
3.1 缓冲区
缓冲区（Buffer） ：一个用于特定基本数据类
型的容器。由 java.nio 包定义的，所有缓冲区
都是 Buffer 抽象类的子类。
 Java NIO 中的 Buffer 主要用于与 NIO 通道进行
交互，数据是从通道读入缓冲区，从缓冲区写
入通道中的。
Buffer 就像一个数组，可以保存多个相同类型的数据。根
据数据类型不同(boolean 除外) ，有以下 Buffer 常用子类：
 ByteBuffer
 CharBuffer
 ShortBuffer
 IntBuffer
 LongBuffer
 FloatBuffer
 DoubleBuffer
上述 Buffer 类 他们都采用相似的方法进行管理数据，只是各自
管理的数据类型不同而已。都是通过如下方法获取一个 Buffer
对象：
static XxxBuffer allocate(int capacity) : 创建一个容量为 capacity 的 XxxBuffer 对象
Buffer 中的重要概念：
 容量 (capacity) ： 表示 Buffer 最大数据容量，缓冲区容量不能为负，并且创
建后不能更改。
 限制 (limit)： 第一个不应该读取或写入的数据的索引，即位于 limit 后的数据
不可读写。缓冲区的限制不能为负，并且不能大于其容量。
 位置 (position)： 下一个要读取或写入的数据的索引。缓冲区的位置不能为
负，并且不能大于其限制
 标记 (mark)与重置 (reset)： 标记是一个索引，通过 Buffer 中的 mark() 方法
指定 Buffer 中一个特定的 position，之后可以通过调用 reset() 方法恢复到这
个 position.
 标记、 位置、 限制、 容量遵守以下不变式： 0 <= mark <= position <= limit <= capacity
Buffer常用方法
方 法 	描 述
Buffer clear() 	清空缓冲区并返回对缓冲区的引用
Buffer flip() 	将缓冲区的界限设置为当前位置，并将当前位置充值为 0
int capacity() 	返回 Buffer 的 capacity 大小
boolean hasRemaining() 	判断缓冲区中是否还有元素
int limit() 	返回 Buffer 的界限(limit) 的位置
Buffer limit(int n) 	将设置缓冲区界限为 n, 并返回一个具有新 limit 的缓冲区对象
Buffer mark() 	对缓冲区设置标记
int position() 	返回缓冲区的当前位置 position
Buffer position(int n) 	将设置缓冲区的当前位置为 n , 并返回修改后的 Buffer 对象
int remaining() 	返回 position 和 limit 之间的元素个数
Buffer reset() 	将位置 position 转到以前设置的 mark 所在的位置
Buffer rewind() 	将位置设为为 0， 取消设置的 mark

例如：
通道与缓存的使用
	FileInputStream rfs = new FileInputStream(
				"E:/StormMedia/StormCache/AEC87B9AC1F1137CE10AE2C896E55C49D7FC282F/meta.cache");
		FileOutputStream ofs = new FileOutputStream("d:/abc.cache");
		FileChannel wfc = rfs.getChannel();
		FileChannel ofc = ofs.getChannel();

		ByteBuffer buf = ByteBuffer.allocate(1024);
		while (wfc.read(buf) != -1) {
			buf.flip();
			ofc.write(buf);
			buf.clear();
		}
		ofc.close();
		wfc.close();
		rfs.close();
		ofs.close();
3.2 直接与非直接缓冲区
字节缓冲区要么是直接的，要么是非直接的。如果为直接字节缓冲区，则 Java 虚拟机会尽最大努力直接在
此缓冲区上执行本机 I/O 操作。也就是说，在每次调用基础操作系统的一个本机 I/O 操作之前（或之后），
虚拟机都会尽量避免将缓冲区的内容复制到中间缓冲区中（或从中间缓冲区中复制内容）。
 直接字节缓冲区可以通过调用此类的 allocateDirect() 工厂方法来创建。此方法返回的缓冲区进行分配和取消
分配所需成本通常高于非直接缓冲区。直接缓冲区的内容可以驻留在常规的垃圾回收堆之外，因此，它们对
应用程序的内存需求量造成的影响可能并不明显。所以，建议将直接缓冲区主要分配给那些易受基础系统的
本机 I/O 操作影响的大型、持久的缓冲区。一般情况下，最好仅在直接缓冲区能在程序性能方面带来明显好
处时分配它们。
 直接字节缓冲区还可以通过 FileChannel 的 map() 方法 将文件区域直接映射到内存中来创建。该方法返回
MappedByteBuffer 。 Java 平台的实现有助于通过 JNI 从本机代码创建直接字节缓冲区。如果以上这些缓冲区
中的某个缓冲区实例指的是不可访问的内存区域，则试图访问该区域不会更改该缓冲区的内容，并且将会在
访问期间或稍后的某个时间导致抛出不确定的异常。
 字节缓冲区是直接缓冲区还是非直接缓冲区可通过调用其 isDirect() 方法来确定。提供此方法是为了能够在
性能关键型代码中执行显式缓冲区管理。
例如：直接缓冲区复制
	FileChannel rd = FileChannel.open(Paths.get("E:/wu/centeros操作/centeros挂载u盘.txt"),StandardOpenOption.READ);
	FileChannel wr = FileChannel.open(Paths.get("d:/aa.txt"), StandardOpenOption.WRITE,StandardOpenOption.READ,StandardOpenOption.CREATE);
	
	MappedByteBuffer rmap = rd.map(MapMode.READ_ONLY, 0,rd.size());
	MappedByteBuffer wmap = wr.map(MapMode.READ_WRITE,0, rd.size());
  
	byte[] by=new byte[rmap.limit()];
	rmap.get(by);
	wmap.put(by);
	
	rd.close();
	wr.close();

5. NIO 的非阻塞式网络通信
 选择器(Selector)
例如：阻塞方式
package demo;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;
import java.nio.file.Paths;
import java.nio.file.StandardOpenOption;

import org.junit.Test;

public class JucBlockCh03 {
  @Test
  public void client() throws IOException{
	  SocketChannel socket = SocketChannel.open(new InetSocketAddress("127.0.0.1", 8877));
	  FileChannel rd=FileChannel.open(Paths.get("e:/homework1.zip"), StandardOpenOption.READ);
	  
	  ByteBuffer buf=ByteBuffer.allocate(1024*8);
	  while(rd.read(buf)!=-1){
		  buf.flip();
		  socket.write(buf);
		  buf.clear();
	  }
	  rd.close();
	  socket.close();
  }
  @Test
  public void server() throws IOException{
	  ServerSocketChannel server = ServerSocketChannel.open();
	  server.bind(new InetSocketAddress(8877));
	  //接收客户端请求
	  SocketChannel accept = server.accept();
	  //生成写入文件的本地通道
	  FileChannel wr=FileChannel.open(Paths.get("d:/aa.zip"),StandardOpenOption.WRITE,StandardOpenOption.CREATE);
	  
	  ByteBuffer buf=ByteBuffer.allocate(1024*8);
	  System.out.println("准备读取数据...");
	  //从客户端读取数据
	  while(accept.read(buf)!=0){
	
		  buf.flip();
		  wr.write(buf);
		  buf.clear();
	  }
	  accept.close();
	  wr.close();
	  server.close();
	  
  }
}
选择器（Selector）：是 SelectableChannel 的多路复用器。用于监控 SelectableChannel 的 IO 状况
//客户端
	@Test
	public void client() throws IOException{
		//1. 获取通道
		SocketChannel sChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 9898));
		
		//2. 切换非阻塞模式
		sChannel.configureBlocking(false);
		
		//3. 分配指定大小的缓冲区
		ByteBuffer buf = ByteBuffer.allocate(1024);
		
		//4. 发送数据给服务端
		Scanner scan = new Scanner(System.in);
		
		while(scan.hasNext()){
			String str = scan.next();
			buf.put((new Date().toString() + "\n" + str).getBytes());
			buf.flip();
			sChannel.write(buf);
			buf.clear();
		}
		
		//5. 关闭通道
		sChannel.close();
	}

	//服务端
	@Test
	public void server() throws IOException{
		//1. 获取通道
		ServerSocketChannel ssChannel = ServerSocketChannel.open();
		
		//2. 切换非阻塞模式
		ssChannel.configureBlocking(false);
		
		//3. 绑定连接
		ssChannel.bind(new InetSocketAddress(9898));
		
		//4. 获取选择器
		Selector selector = Selector.open();
		
		//5. 将通道注册到选择器上, 并且指定“监听接收事件”
		ssChannel.register(selector, SelectionKey.OP_ACCEPT);
		
		//6. 轮询式的获取选择器上已经“准备就绪”的事件
		while(selector.select() > 0){
			
			//7. 获取当前选择器中所有注册的“选择键(已就绪的监听事件)”
			Iterator<SelectionKey> it = selector.selectedKeys().iterator();
			
			while(it.hasNext()){
				//8. 获取准备“就绪”的是事件
				SelectionKey sk = it.next();
				
				//9. 判断具体是什么事件准备就绪
				if(sk.isAcceptable()){
					//10. 若“接收就绪”，获取客户端连接
					SocketChannel sChannel = ssChannel.accept();
					
					//11. 切换非阻塞模式
					sChannel.configureBlocking(false);
					
					//12. 将该通道注册到选择器上
					sChannel.register(selector, SelectionKey.OP_READ);
				}else if(sk.isReadable()){
					//13. 获取当前选择器上“读就绪”状态的通道
					SocketChannel sChannel = (SocketChannel) sk.channel();
					
					//14. 读取数据
					ByteBuffer buf = ByteBuffer.allocate(1024);
					
					int len = 0;
					while((len = sChannel.read(buf)) > 0 ){
						buf.flip();
						System.out.println(new String(buf.array(), 0, len));
						buf.clear();
					}
				}
				
				//15. 取消选择键 SelectionKey
				it.remove();
			}
		}
	}

案例2：

	@Test
	public void send() throws IOException{
		DatagramChannel dc = DatagramChannel.open();
		
		dc.configureBlocking(false);
		
		ByteBuffer buf = ByteBuffer.allocate(1024);
		
		Scanner scan = new Scanner(System.in);
		
		while(scan.hasNext()){
			String str = scan.next();
			buf.put((new Date().toString() + ":\n" + str).getBytes());
			buf.flip();
			dc.send(buf, new InetSocketAddress("127.0.0.1", 9898));
			buf.clear();
		}
		
		dc.close();
	}
	
	@Test
	public void receive() throws IOException{
		DatagramChannel dc = DatagramChannel.open();
		
		dc.configureBlocking(false);
		
		dc.bind(new InetSocketAddress(9898));
		
		Selector selector = Selector.open();
		
		dc.register(selector, SelectionKey.OP_READ);
		
		while(selector.select() > 0){
			Iterator<SelectionKey> it = selector.selectedKeys().iterator();
			
			while(it.hasNext()){
				SelectionKey sk = it.next();
				
				if(sk.isReadable()){
					ByteBuffer buf = ByteBuffer.allocate(1024);
					
					dc.receive(buf);
					buf.flip();
					System.out.println(new String(buf.array(), 0, buf.limit()));
					buf.clear();
				}
			}
			
			it.remove();
		}
	}
1.	多客户端读写例子
2.	import java.io.IOException;  
3.	import java.net.InetSocketAddress;  
4.	import java.net.ServerSocket;  
5.	import java.net.Socket;  
6.	import java.nio.ByteBuffer;  
7.	import java.nio.channels.SelectionKey;  
8.	import java.nio.channels.Selector;  
9.	import java.nio.channels.ServerSocketChannel;  
10.	import java.nio.channels.SocketChannel;  
11.	import java.nio.charset.Charset;  
12.	import java.util.HashMap;  
13.	import java.util.Map;  
14.	import java.util.Set;  
15.	  
16.	public class NIOSServer {  
17.	    private int port = 8888;  
18.	      
19.	    //解码buffer    
20.	    private Charset cs = Charset.forName("utf-8");  
21.	      
22.	    /*接受数据缓冲区*/  
23.	    private static ByteBuffer sBuffer = ByteBuffer.allocate(1024);  
24.	      
25.	    /*发送数据缓冲区*/  
26.	    private static ByteBuffer rBuffer = ByteBuffer.allocate(1024);  
27.	      
28.	    /*映射客户端channel */  
29.	    private Map<String, SocketChannel> clientsMap = new HashMap<String, SocketChannel>();  
30.	      
31.	    private static Selector selector;  
32.	      
33.	    public NIOSServer(int port) {  
34.	        this.port = port;  
35.	        try {  
36.	            init();  
37.	        }  
38.	        catch (Exception e) {  
39.	            e.printStackTrace();  
40.	        }  
41.	    }  
42.	      
43.	    private void init() throws IOException {  
44.	        /*  
45.	         *启动服务器端，配置为非阻塞，绑定端口，注册accept事件  
46.	         *ACCEPT事件：当服务端收到客户端连接请求时，触发该事件  
47.	         */  
48.	        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();  
49.	        serverSocketChannel.configureBlocking(false);  
50.	        ServerSocket serverSocket = serverSocketChannel.socket();  
51.	        serverSocket.bind(new InetSocketAddress(port));  
52.	        selector = Selector.open();  
53.	        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);  
54.	        System.out.println("server start on port:" + port);  
55.	    }  
56.	      
57.	    /**  
58.	     * 服务器端轮询监听，select方法会一直阻塞直到有相关事件发生或超时  
59.	     */  
60.	    private void listen() {  
61.	        while (true) {  
62.	            try {  
63.	                selector.select();//返回值为本次触发的事件数    
64.	                Set<SelectionKey> selectionKeys = selector.selectedKeys();  
65.	                for (SelectionKey key : selectionKeys) {  
66.	                    handle(key);  
67.	                }  
68.	                selectionKeys.clear();//清除处理过的事件    
69.	            }  
70.	            catch (Exception e) {  
71.	                e.printStackTrace();  
72.	                break;  
73.	            }  
74.	              
75.	        }  
76.	    }  
77.	      
78.	    /**  
79.	     * 处理不同的事件  
80.	    */  
81.	    private void handle(SelectionKey selectionKey) throws IOException {  
82.	        ServerSocketChannel server = null;  
83.	        SocketChannel client = null;  
84.	        String receiveText = null;  
85.	        int count = 0;  
86.	        if (selectionKey.isAcceptable()) {  
87.	            /*  
88.	             * 客户端请求连接事件  
89.	             * serversocket为该客户端建立socket连接，将此socket注册READ事件，监听客户端输入  
90.	             * READ事件：当客户端发来数据，并已被服务器控制线程正确读取时，触发该事件  
91.	             */  
92.	            server = (ServerSocketChannel)selectionKey.channel();  
93.	            client = server.accept();  
94.	            client.configureBlocking(false);  
95.	            client.register(selector, SelectionKey.OP_READ);  
96.	        }  
97.	        else if (selectionKey.isReadable()) {  
98.	            /*  
99.	             * READ事件，收到客户端发送数据，读取数据后继续注册监听客户端  
100.	             */  
101.	            client = (SocketChannel)selectionKey.channel();  
102.	            rBuffer.clear();  
103.	            count = client.read(rBuffer);  
104.	            if (count > 0) {  
105.	                rBuffer.flip();  
106.	                receiveText = String.valueOf(cs.decode(rBuffer).array());  
107.	                System.out.println(client.toString() + ":" + receiveText);  
108.	                dispatch(client, receiveText);  
109.	                client = (SocketChannel)selectionKey.channel();  
110.	                //client.register(selector, SelectionKey.OP_READ);  
111.	            }  
112.	        }  
113.	    }  
114.	      
115.	    /**  
116.	     * 把当前客户端信息 推送到其他客户端  
117.	     */  
118.	    private void dispatch(SocketChannel client, String info) throws IOException {  
119.	        Socket s = client.socket();  
120.	        String name =  
121.	            "[" + s.getInetAddress().toString().substring(1) + ":" + Integer.toHexString(client.hashCode()) + "]";  
122.	        if (!clientsMap.isEmpty()) {  
123.	            for (Map.Entry<String, SocketChannel> entry : clientsMap.entrySet()) {  
124.	                SocketChannel temp = entry.getValue();  
125.	                if (!client.equals(temp)) {  
126.	                    sBuffer.clear();  
127.	                    sBuffer.put((name + ":" + info).getBytes());  
128.	                    sBuffer.flip();  
129.	                    //输出到通道    
130.	                    temp.write(sBuffer);  
131.	                }  
132.	            }  
133.	        }  
134.	        clientsMap.put(name, client);  
135.	    }  
136.	      
137.	    public static void main(String[] args) throws IOException {  
138.	        NIOSServer server = new NIOSServer(7777);  
139.	        server.listen();  
140.	    }  
141.	} 
142.	import java.io.IOException;  
143.	import java.net.InetSocketAddress;  
144.	import java.nio.ByteBuffer;  
145.	import java.nio.channels.SelectionKey;  
146.	import java.nio.channels.Selector;  
147.	import java.nio.channels.SocketChannel;  
148.	import java.util.Date;  
149.	import java.util.Scanner;  
150.	import java.util.Set;  
151.	  
152.	public class NIOSClient {  
153.	    /*发送数据缓冲区*/  
154.	    private static ByteBuffer sBuffer = ByteBuffer.allocate(1024);  
155.	      
156.	    /*接受数据缓冲区*/  
157.	    private static ByteBuffer rBuffer = ByteBuffer.allocate(1024);  
158.	      
159.	    /*服务器端地址*/  
160.	    private InetSocketAddress SERVER;  
161.	      
162.	    private static Selector selector;  
163.	      
164.	    private static SocketChannel client;  
165.	      
166.	    private static String receiveText;  
167.	      
168.	    private static String sendText;  
169.	      
170.	    private static int count = 0;  
171.	      
172.	    public NIOSClient(int port) {  
173.	        SERVER = new InetSocketAddress("localhost", port);  
174.	        init();  
175.	    }  
176.	      
177.	    public void init() {  
178.	        try {  
179.	            /*  
180.	              * 客户端向服务器端发起建立连接请求  
181.	              */  
182.	            SocketChannel socketChannel = SocketChannel.open();  
183.	            socketChannel.configureBlocking(false);  
184.	            selector = Selector.open();  
185.	            socketChannel.register(selector, SelectionKey.OP_CONNECT);  
186.	            socketChannel.connect(SERVER);  
187.	            /*  
188.	             * 轮询监听客户端上注册事件的发生  
189.	             */  
190.	            while (true) {  
191.	                selector.select();  
192.	                Set<SelectionKey> keySet = selector.selectedKeys();  
193.	                for (final SelectionKey key : keySet) {  
194.	                    handle(key);  
195.	                }  
196.	                ;  
197.	                keySet.clear();  
198.	            }  
199.	        }  
200.	        catch (Exception e) {  
201.	            e.printStackTrace();  
202.	        }  
203.	    }  
204.	      
205.	    public static void main(String[] args) throws IOException {  
206.	        new NIOSClient(7777);  
207.	    }  
208.	      
209.	    private void handle(SelectionKey selectionKey) throws IOException {  
210.	        if (selectionKey.isConnectable()) {  
211.	            /*  
212.	             * 连接建立事件，已成功连接至服务器  
213.	             */  
214.	            client = (SocketChannel)selectionKey.channel();  
215.	            if (client.isConnectionPending()) {  
216.	                client.finishConnect();  
217.	                System.out.println("connect success !");  
218.	                sBuffer.clear();  
219.	                sBuffer.put((new Date() + " connected!").getBytes());  
220.	                sBuffer.flip();  
221.	                client.write(sBuffer);//发送信息至服务器    
222.	                /* 原文来自站长网 
223.	                 * 启动线程一直监听客户端输入，有信息输入则发往服务器端  
224.	                 * 因为输入流是阻塞的，所以单独线程监听  
225.	                 */  
226.	                new Thread() {  
227.	                    @Override  
228.	                    public void run() {  
229.	                        while (true) {  
230.	                            try {  
231.	                                sBuffer.clear();  
232.	                                Scanner cin = new Scanner(System.in);  
233.	                                sendText = cin.nextLine();  
234.	                                System.out.println(sendText);  
235.	                                /*  
236.	                                 * 未注册WRITE事件，因为大部分时间channel都是可以写的  
237.	                                 */  
238.	                                sBuffer.put(sendText.getBytes("utf-8"));  
239.	                                sBuffer.flip();  
240.	                                client.write(sBuffer);  
241.	                            }  
242.	                            catch (IOException e) {  
243.	                                e.printStackTrace();  
244.	                                break;  
245.	                            }  
246.	                        }  
247.	                    };  
248.	                }.start();  
249.	            }  
250.	            //注册读事件    
251.	            client.register(selector, SelectionKey.OP_READ);  
252.	        }  
253.	        else if (selectionKey.isReadable()) {  
254.	            /*  
255.	             * 读事件触发  
256.	             * 有从服务器端发送过来的信息，读取输出到屏幕上后，继续注册读事件  
257.	             * 监听服务器端发送信息  
258.	             */  
259.	            client = (SocketChannel)selectionKey.channel();  
260.	            rBuffer.clear();  
261.	            count = client.read(rBuffer);  
262.	            if (count > 0) {  
263.	                receiveText = new String(rBuffer.array(), 0, count);  
264.	                System.out.println(receiveText);  
265.	                client = (SocketChannel)selectionKey.channel();  
266.	                client.register(selector, SelectionKey.OP_READ);  
267.	            }  
268.	        }  
269.	    }  
270.	}

```
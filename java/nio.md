# nio
## 示意图

![image](https://github.com/mvpanda/projects/tree/master/java/imgs/nio.png)

## Buffer

四个属性的含义如下：

* 容量（Capacity）：缓冲区能够容纳的数据元素的最大数量。这一个容量在缓冲区创建时被设定，并且永远不能改变。
* 上界(Limit)：缓冲区的第一个不能被读或写的元素。或者说,缓冲区中现存元素的计数。
* 位置(Position)：下一个要被读或写的元素的索引。位置会自动由相应的 get( )和 put( )函数更新。
* 标记(Mark)：下一个要被读或写的元素的索引。位置会自动由相应的 get( )和 put( )函数更新。

Buffer的常见方法如下:

* flip(): 写模式转换成读模式
* rewind()：将 position 重置为 0 ，一般用于重复读。
* clear() ：清空 buffer ，准备再次被写入 (position 变成 0 ， limit 变成 capacity) 。
* compact(): 将未读取的数据拷贝到 buffer 的头部位。
* mark(): reset():mark 可以标记一个位置， reset 可以重置到该位置。

Buffer 常见类型： ByteBuffer 、 MappedByteBuffer 、 CharBuffer 、 DoubleBuffer 、 FloatBuffer 、 IntBuffer 、 LongBuffer 、 ShortBuffer 

## 实例

```java

public class Echo {
    private int threadNum = 2;
    private ThreadFactory threadFactory = new ThreadFactoryBuilder().setNameFormat("Echoer-%d").setDaemon(false).build();
    private ExecutorService executorService = Executors.newFixedThreadPool(threadNum, threadFactory);
    private ConcurrentLinkedQueue<Echoer> availableEchoers = new ConcurrentLinkedQueue<Echoer>();
    private int port = 2048;
    public Echo(int port) {
        this.port = port;
        for (int i = 0; i < threadNum; i++) {
            availableEchoers.add(new Echoer());
        }
    }

    public void start() throws IOException {
        System.out.printf("Starting echo at port: %d\n", port);

        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        serverSocketChannel.configureBlocking(false);
        ServerSocket serverSocket = serverSocketChannel.socket();
        serverSocket.bind(new InetSocketAddress(port));

        Selector selector = Selector.open();
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);

        while (true) {
            System.out.println("Start select");
            int num = selector.select();
            System.out.printf("Select num: %d\n", num);
            if (num == 0) {
                continue; // nothing to do
            }
            Set<SelectionKey> keys = selector.selectedKeys();
            Iterator<SelectionKey> it = keys.iterator();
            while (it.hasNext()) {
                SelectionKey selectionKey = it.next();
                if (selectionKey.isAcceptable()) {
                    System.out.println("Select new OP_ACCEPT");
                    ServerSocketChannel ssc = (ServerSocketChannel) selectionKey.channel();
                    SocketChannel sc = ssc.accept();
                    if (sc != null) {
                        sc.configureBlocking(false);
                        sc.register(selector, SelectionKey.OP_READ);
                        System.out.println("Accept and register a new SocketChannel");
                    }
                    it.remove();
                } else if (selectionKey.isReadable()) {
                    System.out.println("Select new OP_READ");
                    Echoer echoer = availableEchoers.poll(); // will block...
                    echoer.serviceChannel(selectionKey);
                    executorService.execute(echoer);
                    it.remove();
                }
            }
            keys.clear();
        }
    }

    private class Echoer implements Runnable {

        private ByteBuffer bb = ByteBuffer.allocate(1024);
        private SelectionKey selectionKey = null;

        public void serviceChannel(SelectionKey selectionKey) {
            this.selectionKey = selectionKey;
            selectionKey.interestOps(selectionKey.interestOps() & (~SelectionKey.OP_READ));
        }
        
        @Override
        public void run() {
            System.out.println("Echoer start");
            SocketChannel socketChannel = (SocketChannel) selectionKey.channel();
            int bytesRead = 0;
            int bytesReadTotal = 0;
            while (true) {
                bb.clear();
                try {
                    bytesRead = socketChannel.read(bb);
                    if (bytesRead <= 0) {
                        break;
                    }
                    bytesReadTotal += bytesRead;

                    bb.flip();
                    while (bb.hasRemaining()) {
                        socketChannel.write(bb);
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            try {
                if (bytesRead < 0) {
                    // Close channel on EOF, invalidates the key
                    //读取结束后关闭通道，使key失效
                    System.out.println("ServerSocket closed");
                    socketChannel.close();
                } else {
                    System.out.println("Echoer exit");
                    selectionKey.interestOps(selectionKey.interestOps() | SelectionKey.OP_READ);
                    // Cycle the selector so this key is active again
                    selectionKey.selector().wakeup();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }

            Echo.this.availableEchoers.add(this);
        }
    }

    public static void main(String[] args) throws Exception {
        Echo echo = new Echo(2048);
        echo.start();
    }
}


```

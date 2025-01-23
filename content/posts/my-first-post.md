+++
date = '2025-01-23T13:00:00+08:00'
draft = false
title = 'Tomcat淺談'
+++

最近面試被問到會什麼server side的技術，

對於只在開發端沒管理server經驗的我實在說不出個所以然，

頂多在設定檔上加DB連線資訊，查看server編碼之類的，

一直以來對server的理解都挺抽象的，

於是花了些時間，

弄清楚一個網頁應用軟體容器，如Tomcat，到底代工了哪些事情，

我也還在懵懂摸索中，

如果有說錯什麼再請各路大神指點，感謝。

<h1>一、處理Http請求</h1>

server要能夠處理http請求，必須
<ul>
    <li>1.開放某個port作為服務端口接收請求</li>
    <li>2.分配執行序處理請求</li>
</ul>
以Java簡易實作如下:
```Java
//準備執行緒池
private static ExecutorService threadPool = Executors.newCachedThreadPool();
public static void main(String[] args) throws IOException {
    //開啟本地端8080 port, 可用url: localhost:8080 訪問
    ServerSocket serverSocket = new ServerSocket(8080);
    while (!serverSocket.isClosed()) {
        Socket request = serverSocket.accept();
        threadPool.execute(() -> {
            try {
                InputStream inputStream = request.getInputStream();
                System.out.println("收到請求:");
                BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream, "utf-8"));
                String msg = null;
                while((msg = reader.readLine()) != null) {
                    if(msg.length()==0) {
                        break;
                    }
                    System.out.println(msg);
                }
            } catch (IOException e) {
                e.printStackTrace();
            }            
        });
    }
}
```

<h2>二、servlet與jsp容器</h2>
Tomcat能稱為「容器」，其實就是實作了官方規定的功能(Web Profile)，

其中包含servlet與jsp的生命週期，

簡化開發人員的工作。
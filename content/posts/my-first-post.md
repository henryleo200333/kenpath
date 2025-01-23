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

### 一、處理Http請求

server要能夠處理http請求，必須  
1. 開放某個port作為服務端口接收請求
2. 分配執行序處理請求

以Java簡易實作如下:
```Java
//準備執行緒池
private static ExecutorService threadPool = Executors.newCachedThreadPool();
public static void main(String[] args) throws IOException {
    //開啟本地端8080 port, 可用url: localhost:8080 訪問
    ServerSocket serverSocket = new ServerSocket(8080);
    while (!serverSocket.isClosed()) {
        Socket request = serverSocket.accept();
        //執行緒上班
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
                    //印出http請求內容
                    System.out.println(msg);
                }
                //TODO 組織回應內容
            } catch (IOException e) {
                e.printStackTrace();
            }            
        });
    }
}
```

### 二、servlet容器
Tomcat能稱為「容器」，其實就是實作了官方規定的功能(Web Profile)，  
其中包含servlet的生命週期，簡化開發人員的工作。  
servlet簡單講就是一個處理請求的程式，
透過設定檔定義url與servlet的配對關係。

### 三、主配置文件 server.xml
這份文件中，可以
1. 定義域名與應用的對應不同域名對到的ip都是這台機器，  
但可以拆分到不同應用服務上。
2. 設定port與服務對應關係
3. 設定部署的目標資料夾
4. 設定全局DB連線資訊

### 四、Log哪裡看
除錯時常需看server的log，  
預設在/logs/catalina.out

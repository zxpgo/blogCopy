---

title: HTTP网络
date: 2018-11-27 08:40:14
tags: APP开发
categories: APP开发

---

<!--more-->

### 网络简介

HTTP请求，需要明确指出要找的内容，以及内容的提供方式。

设备要在Internet上，从其他计算器获取信息，HTTP请求是基础部分，原理是首先请求某些信息，然后获取响应，并使用适当的方式提取这些信息。请求中包好URL。

URL：统一资源定位符

![](https://i.imgur.com/46jpQ1G.png)


### Android权限

默认情况下，APP不具有访问Internet的权限，Android操作系统拒绝APP发出HTTP请求，应用可能崩溃。

对于每个想要的每个权限，都要向应用的Android清单文件（AndroidManifest）中添加使用权限（uses-permission）元素。

例如，如果创建一个消息传递应用，且希望用户能够在你的应用内直接发送SMS消息，则需要在清单中声明该应用需要发送SMS(SEND_SMS)权限字符串。

![](https://i.imgur.com/yhUgN0q.png)


应用程序权限具有不同都的保护基本，两个最重要的级别是：

- 正常权限：不会对用户隐私或其他应用的操作构成威胁，因此系统自动批准这些权限。
- 危险权限：会访问用户的个人身份信息或私人信息，并且会访问可能影响其他应用操作方式的功能，其中包括：使用照相机，访问联系人或通话记录，或者通过麦克风录音，运行这些权限前必须先进行请求。用户必须通过系统对话明确授权。

### 超文本传送协议HTTP

Http是可以对通过万维网交换数据的方式进行处理的核心技术。Http代表超文本传输一些，并来自于早期的web(计算机进传输基本网页信息)。

协议仅仅是一组预先定义的规则，可允许计算机彼此进行通信和协调，以完成任务。

HTTP动作(RequestMethod)：

- Get:即从某处获取或检索一些数据
- Post:即创建某些新信息
- Put:即更新某些现有信息
- Delete: 即删除服务器上的某些现有的信息

[详细介绍](https://www.restapitutorial.com/lessons/httpmethods.html)


### HttpURLConnection

HttpUrlConnection是URLConnection的子类，继承了URLConnection所有的行为。

url.openConnection()返回值类型为HttpUrlConnection。

HttpUrlConnection是一个HTTP客户端，可用它来在Android中执行HTTP请求。Http连接帮助我们在Web内发送和接收数据。

打开HTTP连接过程：

- 设置HTTP连接
- 发起HTTP连接
- 接受响应


 	 
 		private String makeHttpRequest(URL url) throws IOException {
            String jsonResponse = "";
            HttpURLConnection urlConnection = null;
            InputStream inputStream = null;
            try {
                urlConnection = (HttpURLConnection) url.openConnection();
                urlConnection.setRequestMethod("GET");
                urlConnection.setReadTimeout(10000 /* milliseconds */);
                urlConnection.setConnectTimeout(15000 /* milliseconds */);
                urlConnection.connect();
                inputStream = urlConnection.getInputStream();
                jsonResponse = readFromStream(inputStream);
            } catch (IOException e) {
                // TODO: Handle the exception
            } finally {
                if (urlConnection != null) {
                    urlConnection.disconnect();
                }
                if (inputStream != null) {
                    // function must handle java.io.IOException here
                    inputStream.close();
                }
            }
            return jsonResponse;
        }

### Android系统架构

Android系统由4层组成：应用层、架构层(Framework)、操作系统层、硬件层

![](https://i.imgur.com/WpZStQy.png)

应用层：建立所有应用程序所在的层，在应用层我们使用一些Android Framework类，如TextView和Activity，当使用这些类的时候，仅仅需要通过一小行代码，就可以完成很多复杂的事情，例如，显示文本，打开网页。

Framework：连接应用和设备的桥梁，Framework类是我们清晰地展示了当用户使用应用时，设备如何表现。Framework类通过调用Android低级层来避免很多不必要的复杂事务，并把低级层作为起重设备。


### HTTP请求方法类型

 	urlConnection = (HttpURLConnection) url.openConnection();

HTTPUrlConnection对象只包含请求的目标地址。
 	
	urlConnection.setRequestMethod("GET");

定义服务器应在请求中执行的操作。

Get：通常是从服务器读取信息，并将其返回给客户端

Post: 通常是包含写入到服务器中的信息


	urlConnection.connect();

我们与服务器建立HTTP连接，前面的内容都是为了设置HTTP请求。之后的代码都是为了接收响应，并对在应用中对响应作出解释。

HTTP状态码：

|状态码|描述|
|---|
|200|一切正常，可接受到请求并且一切正常运行|
|301|页面被永久移除|
|404|无法找到页面|
|500|内部服务器错误|

[维基百科详细介绍](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)

[百度百科详细介绍](https://baike.baidu.com/item/HTTP%E7%8A%B6%E6%80%81%E7%A0%81/5053660?fr=aladdin)

- 1开头表示代表请求已被接受，需要继续处理
- 2开头代表请求已成功被服务器接收、理解、并接受
- 3开头表示重定向错误，代表页面等已暂时或永久转移，希望进行其他操作以继续请求
- 4开头代表了客户端看起来可能发生了错误，妨碍了服务器的处理，即设备或浏览器出现错误，这可能是URL出错或意外引发了格式错误的请求，而服务器不知道如何处理该请求
- 5、6开头代表了服务器在处理请求的过程中有错误或者异常状态发生，可能是服务器处理已崩溃或服务器在处理请求时遇到一些意外错误

通过HTTP码来判断是否对响应进行处理：

	//如果响应码为200，读取输入流和解析响应
	if (urlConnection.getResponseCode() == 200){
		inputStream = urlConnection.getInputStream();
		jsonResponse = readFromStream(inputStream);
	}
	return jsonResponse;

后续的处理，必须可以处理当jsonResponse为空的情况。如下：

 	if (TextUtils.isEmpty(earthquakeJSON)){
          return null;
    }


### 从输入流读取


通过inputStream来返回服务器响应的数据，需要在一个方法的帮助下，读取来自inputStream的数据。该方法为readFromStram()。

inputStream实际上并不会返回数字或字母等可读数据，而是返回原始的二进制0 1数据，而且并不会具体说明数据所代表的含义，数据可能是图片，音频，文字或一些需要解析的geoJSON数据。

	InputStreamReader inputStreamReader = new InputStreamReader(inputStream, Charset.forName("UTF-8"));
              
确定返回数据是文本，可以利用inputStreamReader处理从原始数据到可读字符的翻译过程。InputStreamReader一次只允许读取一个字符，这可能导致验证的性能问题，具体取决于底层inputStream实际提供数据的方式。

inputStream作为构造函数的一个参数传递给InputStreamReader实例，同时还为构造函数提供了一个字符集，简称Charset，Charset指定如何将inputStream的原始数据逐个字节翻译为可读字符。

	BufferedReader reader = new BufferedReader(inputStreamReader);

为了避免这种情况，可以将inputStreamReader包装到BufferReader中，BufferReader在接收到对某个字符的请求后，会读取并保存该字符前后的一大块数据。当继续请求另一个字符时，BufferReader就能够利用提前读取的数据，来满足请求，而无需再回到inputStreamReader。


### StringBulider

StringBulider是一个字符序列，但StringBulider是可变的，即在创建之后可以修改。

如果要通过若干个步骤，分步构建一个较长的文本字符串，则StringBulider更高效。否则，每一步都需要重新创建一个字符串，浪费内存资源。

示例：

![](https://i.imgur.com/IP2lpx0.png)


最后字符串为：Word builder.

下面代码，创建一个StringBuilder向其添加BufferedReader中所有可用的各行文本。首先创建builder，和reader，然后向BufferedReader请求一行文本，在文本不为null时，将该行添加到StringBuilder中，然后继续读取下一行。最后直到BufferedReader中没有文本，使用toString方法从StringBuilder中获取字符串，并返回最终输出。

 	 private String readFromStream(InputStream inputStream) throws IOException {
            StringBuilder output = new StringBuilder();
            if (inputStream != null) {
                InputStreamReader inputStreamReader = new InputStreamReader(inputStream, Charset.forName("UTF-8"));
                BufferedReader reader = new BufferedReader(inputStreamReader);
                String line = reader.readLine();
                while (line != null) {
                    output.append(line);
                    line = reader.readLine();
                }
            }
            return output.toString();
        }



### 异常

异常是基本的错误，但可以被抛出throw或捕获到catch。

异常类型很多，这些异常类型实际上是从异常超类中继承而来。

**try/catch**

如果URL构造函数一切正常，那么就不会引发异常，不会在catch块内执行任何代码。如果try块中的代码引发了异常，会立即跳转到包含该异常的catch代码块中，并执行该代码。然后，退出try catch语句并继续执行其他代码。

	URL url = null;
 	try {
   		url = new URL(stringUrl);
    } catch (MalformedURLException exception) {
        Log.e(LOG_TAG, "Error with creating URL", exception);
        return null;
    }
	return url;

注意url的定义在全局，如果在try块中，try catch语句完成之后，将无法访问URL变量。
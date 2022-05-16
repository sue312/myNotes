<h1><center>OKHttp</center></h1>

## 一、配置

1、build.gradle（app）

```
implementation "com.squareup.okhttp3:okhttp:4.9.0"
```

2、AndroidManifest.xml

```
<uses-permission android:name="android.permission.INTERNET" />
```

## 二、POST和GET

### 1、同步和异步的区别

同步使用的是，同步还需开个新线程。

```
Response response = call.execute();

new Thread() {
	@Override
	public void run() {
		getSync();
	}
}.start();
```

异步使用的是

```
		call.enqueue(new Callback() {
            @Override
            public void onFailure(@NonNull Call call, @NonNull IOException e) {

            }

            @Override
            public void onResponse(@NonNull Call call, @NonNull Response response) throws IOException {
                Log.d("wangxin888", "postAsync: " + response.body().string());
            }
        });
```

### 2、Post和Get的区别

Get直接将数据放在uri中，Post将数据放入FormBody（表格）中，然后将表post进url。

Get和Post都是先将uri放入request中，然后传入call。同步执行`call.execute()`,异步执行`call.enqueue()`。

> post同步
>
> ```
> 		FormBody formBody = new FormBody.Builder().add("a", "1").add("b", "2").build();
>         Request request = new Request.Builder().url("https://www.httpbin.org/post").post(formBody).build();
>         Call call = okHttpClient.newCall(request);
>         try {
>             Response response = call.execute();
>             Log.d("wangxin888", "postSync: " + response.body().string());
>         } catch (IOException e) {
>             e.printStackTrace();
>         }
> ```

> post异步
>
> ```
> 		FormBody formBody = new FormBody.Builder().add("a", "1").add("b", "2").build();
>         Request request = new Request.Builder().url("https://www.httpbin.org/post").post(formBody).build();
>         Call call = okHttpClient.newCall(request);
>         call.enqueue(new Callback() {
>             @Override
>             public void onFailure(@NonNull Call call, @NonNull IOException e) {
> 
>             }
> 
>             @Override
>             public void onResponse(@NonNull Call call, @NonNull Response response) throws IOException {
>                 Log.d("wangxin888", "postAsync: " + response.body().string());
>             }
>         });
> ```

> get同步
>
> 		Request request = new Request.Builder().url("https://www.httpbin.org/get?a=1&b=2").build();
> 	    //准备好call对象
> 	    Call call = okHttpClient.newCall(request);
> 	    try {
> 	        Response response = call.execute();
> 	        Log.d("wangxin888", "getSync: " + response.body().string());
> 	    } catch (IOException e) {
> 	        e.printStackTrace();
> 	    }

> get异步
>
> ```
> 		Request request = new Request.Builder().url("https://www.httpbin.org/get?a=1&b=2").build();
>         //准备好call对象
>         Call call = okHttpClient.newCall(request);
>         call.enqueue(new Callback() {
>             @Override
>             public void onFailure(@NonNull Call call, @NonNull IOException e) {
> 
>             }
> 
>             @Override
>             public void onResponse(@NonNull Call call, @NonNull Response response) throws IOException {
>                 if (response.isSuccessful()) {
>                     Log.d("wangxin888", "getAsync: " + response.body().string());
>                 }
>             }
>         });
> ```

## 三、数据传输与处理

通过下面的方法获取返回的数据，它是json字符串格式

> response.body().string()

```
{"data":
{"curPage":1,"datas":
[
{"author":"xiaoyang"
,"chapterId":440
,"chapterName":"官方"
,"courseId":13
,"desc":"
<p>前两天看公众号文章：
<br><a href=\"https://mp.weixin.qq.com/s/XHbJ1L5_rdvKs3AjLCvGiA\">
分享一个困惑很久的问题：App前台，Activity会被回收吗？</a></p>
\r\n
<p>文章中源码分析阶段，看到了一些核心类：</p>
\r\n
<pre><code> ActivityRecord、TaskRecord、ActivityStack、ActivityDisplay、ActivityStackSupervisor\r\n</code></pre>
<p>绕起来有点晕。</p>
\r\n
<p>问题来了：</p>
\r\n
<ol>
\r\n
<li>这些类分别起到什么作用？</li>
\r\n
<li>有什么好的方式帮助记忆吗？</li>
\r\n
</ol>
","envelopePic":""
,"id":250723
,"link":"https://wanandroid.com/wenda/show/21681"
,"niceDate":"24分钟前"
,"origin":""
,"originId":21681
,"publishTime":1648554404000
,"title":"每日一问 | ActivityRecord、TaskRecord、ActivityStack、ActivityDisplay、ActivityStackSupervisor 分别是干什么的？"
,"userId":129096
,"visible":0
,"zan":0}
]
,"offset":0
,"over":true,"pageCount":1,"size":20,"total":1}
,"errorCode":0
,"errorMsg":""}
```

我们可以通过Gson将它转换为对象，在对象中创建一个个字符串去接收它们，没有找到对应的字符串，对应数据将被舍弃。

为了方便我们将上述字符串分割只保留`[]`中的内容。bookList中有很多Books对象，得到其中一个对象，就可使用对象中的get方法获取它的数据了。

```
Gson gson = new Gson();
TypeToken tt = new TypeToken<ArrayList<Books>>() {};
Type userListType = tt.getType();
List<Books> bookList = gson.fromJson(cookie, userListType);
Books books = bookList.get(0);
```

获取数据的代码块

```
		//cookie缓存，得到okHttpClient
		OkHttpClient okHttpClient = new OkHttpClient.Builder()
                .cookieJar(new CookieJar() {
                    @Override
                    public void saveFromResponse(HttpUrl httpUrl, List<Cookie> list) {
                        cookies.put(httpUrl.host(), list);
                    }

                    @Override
                    public List<Cookie> loadForRequest(HttpUrl httpUrl) {
                        List<Cookie> cook = cookies.get(httpUrl.host());
                        return cook == null ? new ArrayList<>() : cook;
                    }
                }).build();
        //使用post登录
        FormBody formBody = new FormBody.Builder().add("username", "sue").add("password", "123456").build();
        Request request = new Request.Builder().url("https://www.wanandroid.com/user/login").post(formBody).build();
        Call call = okHttpClient.newCall(request);
        try {
            Response response = call.execute();
            Log.d("wangxin888", "getSync: "+response.body().string());
        } catch (IOException e) {
            e.printStackTrace();
        }

        //登录后，使用get获取收藏
        request = new Request.Builder().url("https://www.wanandroid.com/lg/collect/list/0/json").build();
        call = okHttpClient.newCall(request);
        try {
            Response response = call.execute();
            //得到返回值
            String string = response.body().string();
            Log.d("wangxin888", "getSync: " + string);
            //将返回值转换为对象
            books = getGson(string);
            //更新UI
            sendMessage("update_ui");
        } catch (IOException e) {
            e.printStackTrace();
        }
```

## 四、拦截器

给链接加头

```
    private void interceptor() {
        okHttpClient = new OkHttpClient.Builder().addInterceptor(new Interceptor() {
            @Override
            public Response intercept(Chain chain) throws IOException {
                //前置处理
                Request request = chain.request().newBuilder().addHeader("os", "wangxin")
                        .addHeader("version", "2.0").build();
                Response response = chain.proceed(request);
                //后置处理
                return response;
            }
        }).build();
    }
```


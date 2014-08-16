android_httpUtils_myself
========================

This is an HTTP toolkit, is from an  android projects, use very convenient


前言：
    下面介绍的是一款http工具包，是我从摘录出来的一个工具包，下面直接把包上传如下：

还有两个jar文件，也在这个包里面，在这里说明一下：
afinal-0.3.3-bin.jar：
   Afinal是一个android的ioc，orm框架，内置了四大模块功能：FinalAcitivity,FinalBitmap,FinalDb,FinalHttp。通过finalActivity，我们可以通过注解的方式进行绑定ui和事件。通过finalBitmap，我们可以方便的加载bitmap图片，而无需考虑oom等问题。通过finalDB模块，我们一行代码就可以对android的sqlite数据库进行增删改查。通过FinalHttp模块，我们可以以ajax形式请求http数据
    其中使用afinal的地方就是：BaseActivity继承了FinalActivity，这个地方我试验了一下，其实直接继承Activity也不会出错，也就是说，这个包只是引用了FinalActivity，但是关于FinalActivity中的好处，一点都没用（虽然说没有用，不过有机会的话，可以自己找些demo，研究一下，毕竟这个东西是开源框架，本身封装的有bitmap和http的相关内容）

httpmime-4.1.2.jar：
    这个包中封装了MultipartEntity，这个是用来进行文件上传使用的（关于mini规范，可以参看网络）。

一。包的使用方法
    介绍之前，先贴出这个包中所有的相关类的列表图：


注意：
1.可以看到，有上述这么多个类，除了MainActivity.java可以不用外，其他的类都需要使用，将来如果使用的话，可以直接把这个包作为工程导入，然后进行修改（包名，类名等）
2.所有定义的activity最好继承这个BaseActivity（实际不集成也没关系，只是其中定义了一些intent跳转的好方法）。
3.在AndroidManifest.xml中，需要把app制定为上述的CheckCardApp（可以把这个名字给改了，呵呵）
4.在MainActivity中加入了两个使用RequestVo的实例，一个是requestContent,还有一个是MultipartEntity，可以参考这两个进行操作（对应的，多加了两个jsonParser的类，就是对应的两个解析类）

二。包的功能分类
这个包分装了：
1.http post的一般请求，也就是说请求的内容就是一般的key value
2.MultipartEntity，也就说封装了可以上传文件的实体。
下面贴出RequestVo的内容：
public class RequestVo {
 public String requestUrl;
 public Context context;
 public String requestHeader;
 public JsonParser jsonParser;
 public String requestContent;
 public MultipartEntity reqEntity;
 public DataCallback responseCallback;
 public ArrayList<Integer> opidList;
  }
用于区分上述是第一种还是第二种的方法是判断requestContent == null，如果为null的话，就是MultipartEntity，呵呵，简单明了

三。扩展get请求
上述包内容没有get的相关操作，下面对包进行扩展，增加get相关操作。
下面是扩展后的包：

和原包相比，只修改了一个NetUtil.java文件的内容，对应的文件如下：


修改的核心代码如下：
。。。。。
HttpPost httpPost;
HttpGet httpGet;
HttpResponse response ;
try {
//如果两者都是null的话，就是get请求
if (vo.requestContent == null && vo.reqEntity == null) {
httpGet = new HttpGet(vo.requestUrl);
response = httpClient.execute(httpGet);
} else {
httpPost = new HttpPost(vo.requestUrl);
//为普通的post请求
if (vo.requestContent != null) {
httpPost.setEntity(new StringEntity(vo.requestContent, "utf-8"));
//Log.e(TAG, "vo.requestContent = " + vo.requestContent);
} //为文件上传请求
else {
httpPost.setEntity(vo.reqEntity);
}
response = httpClient.execute(httpPost);
}
int httpCode = response.getStatusLine().getStatusCode();
。。。。。


很明显，如何使用get请求，上面的注释部分已经解释很清楚了，就不多做叙述了。
至此，http自行定义的工具包已经完善，后续的是使用了（关于包中类的一些其他高级用法，如果涉及到的时候，可以参考移动考勤原始包里面的用法：项目名为：cmsbclockcard）



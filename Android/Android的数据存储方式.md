## 目录
- [SharedPreferences存储](#sharedpreferences存储)
- [文件存储](#文件存储)
- [网络存储](#网络存储)
- [ContentProvider存储](#contentprovider存储)
- [SQLite数据库存储](#sqlite数据库存储)







### SharedPreferences存储

首先说明 SharedPreferences 存储方式，它是 Android 提供的用来存储一些简单配置信息的一种机制，例如：登录用户的用户名与密码。其采用了 Map 数据结构来存储数据，以键值的方式存储，可以简单的读取与写入，具体实例如下：

```Java
void ReadSharedPreferences(){  
   String strName,strPassword;  
   SharedPreferences   user = getSharedPreferences(“user_info”,0);  
   strName = user.getString(“NAME”,””);  
   strPassword = user getString(“PASSWORD”,””);  
}  
void WriteSharedPreferences(String strName,String strPassword){  
   SharedPreferences   user = getSharedPreferences(“user_info”,0);  
   uer.edit();  
   user.putString(“NAME”, strName);  
   user.putString(“PASSWORD” ,strPassword);  
   user.commit();  
}  
```

数据读取与写入的方法都非常简单，只是在写入的时候有些区别：先调用 edit() 使其处于编辑状态，然后才能修改数据，最后使用 commit() 提交修改的数据。实际上SharedPreferences是采用了 XML 格式将数据存储到设备中，在 DDMS 中的 File Explorer 中的 /data/data/<package name>/shares_prefs 下。**使用 SharedPreferences 是有些限制的：只能在同一个包内使用，不能在不同的包之间使用。**



### 文件存储

文件存储方式是一种较常用的方法，在 Android 中读取/写入文件的方法，与 Java 中实现 I/O 的程序是完全一样的，提供了 openFileInput() 和 openFileOutput() 方法来读取设备上的文件。具体实例如下：

```Java
String fn = “moandroid.log”;  
FileInputStream fis = openFileInput(fn);  
FileOutputStream fos = openFileOutput(fn,Context.MODE_PRIVATE); 
```



### 网络存储

网络存储方式，需要与 Android 网络数据包打交道，关于 Android 网络数据包的详细说明，请阅读 Android SDK引用了 Java SDK 的哪些 package ？



### ContentProvider存储

- ContentProvider 简介

  当应用继承 ContentProvider 类，并重写该类用于提供数据和存储数据的方法，就可以向其他应用共享其数据。虽然使用其他方法也可以对外共享数据，但数据访问方式会因数据存储的方式而不同，如：采用文件方式对外共享数据，需要进行文件操作读写数据；采用 sharedpreferences 共享数据，需要使用sharedpreferences API 读写数据。而使用 ContentProvider 共享数据的好处是统一了数据访问方式。

- Uri 类简介

  Uri 代表了要操作的数据，Uri 主要包含了两部分信息：1. 需要操作的ContentProvider ，2. 对ContentProvider 中的什么数据进行操作，**一个 Uri 由以下几部分组成：**

  - scheme

    ContentProvider（内容提供者）的scheme已经由Android所规定为：content://…

  - 主机名（或Authority）

    用于唯一标识这个 ContentProvider，外部调用者可以根据这个标识来找到它。

  - 路径（path）

    可以用来表示我们要操作的数据，路径的构建应根据业务而定，如下：

    - 要操作contact表中id为10的记录，可以构建这样的路径:/contact/10
    - 要操作contact表中id为10的记录的name字段， contact/10/name
    - 要操作contact表中的所有记录，可以构建这样的路径:/contact?

    要操作的数据不一定来自数据库，也可以是文件等他存储方式，如下：

    - 要操作xml文件中contact节点下的name节点，可以构建这样的路径：/contact/name

    如果要把一个字符串转换成 Uri，可以使用 Uri 类中的 parse() 方法，如下：

    ```Java
    Uri uri = Uri.parse("content://com.changcheng.provider.contactprovider/contact")
    ```

- UriMatcher、ContentUrist 和 ContentResolver 简介

  因为 Uri 代表了要操作的数据，所以我们很经常需要解析 Uri，并从 Uri 中获取数据。Android 系统提供了两个用于操作 Uri 的工具类，分别为 `UriMatcher` 和 `ContentUris`。掌握它们的使用，会便于我们的开发工作。

  - UriMatcher

    用于匹配 Uri，它的用法如下：

    - 首先把你需要匹配 Uri 路径全部给注册上，如下：

      /* 常量 UriMatcher.NO_MATCH 表示不匹配任何路径的返回码 (-1)。*/

      ```Java
      UriMatcher uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
      ```

      /* 如果 match() 方法匹配 content://com.changcheng.sqlite.provider.contactprovider /contact 路径，返回匹配码为 1 */

      ```Java
      // 添加需要匹配 uri，如果匹配就会返回匹配码
      uriMatcher.addURI(“com.changcheng.sqlite.provider.contactprovider”, “contact”, 1);
      ```

      /*如果 match() 方法匹配 content://com.changcheng.sqlite.provider.contactprovider/contact/230路径，返回匹配码为 2 */

      ```Java
      //#号为通配符
      uriMatcher.addURI(“com.changcheng.sqlite.provider.contactprovider”, “contact/#”, 2);
      ```

    - 注册完需要匹配的 Uri 后，就可以使用 uriMatcher.match(uri) 方法对输入的 Uri 进行匹配，如果匹配就返回匹配码，匹配码是调用 addURI() 方法传入的第三个参数，假设匹配 content://com.changcheng.sqlite.provider.contactprovider/contact 路径，返回的匹配码为1。

  - ContentUris

    用于获取 Uri 路径后面的 ID 部分，它有两个比较实用的方法：

    - withAppendedId(uri, id)： 用于为路径加上ID部分
    - parseId(uri)：方法用于从路径中获取 ID 部分

  - ContentResolver

    当外部应用需要对 ContentProvider 中的数据进行添加、删除、修改和查询操作时，可以使用 ContentResolver 类来完成，要获取 ContentResolver 对象，可以使用 Activity 提供的getContentResolver() 方法。 ContentResolver 使用 insert、delete、update、query 方法，来操作数据。

    ​

### SQLite数据库存储


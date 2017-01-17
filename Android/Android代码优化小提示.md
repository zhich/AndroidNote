# Android代码优化小提示


- [数据相关](#数据相关)


### 数据相关
* 遍历一个 List 集合

	bad：

	```Java
	List<User> userList = new ArrayList<>();
	for (int i = 0; i < userList.size(); i++) {
    	User user = new User();
   	 	//省略 n 行代码...
    	userList.add(user);
	}
	```

	better：

	```Java
	List<User> userList = new ArrayList<>();
	User user = null;
	for (int i = 0, size = userList.size(); i < size; i++) {
    	user = new User();
    	//省略 n 行代码...
    	userList.add(user);
	}
	```

	> ①、第二种方式把 userList.size() 用变量 size 存值避免了每次迭代都去调用该方法（不要在 for 循环的第二个表达式的判		断调用对象的方法或字段）；②、把 user 变量的声明放到循环外边，避免每次使用都去声明一下 User 对象。
  	③、getCount() 方法的处理也类似，如用 int count = xxx.getCount() 缓存起来.

* 遍历HashMap的最佳方法

	```Java
	Map<String, User> userMap = new HashMap<>();
	Iterator it = userMap.entrySet().iterator();
	while (it.hasNext()) {
    	Map.Entry entry = (Map.Entry) it.next();
    	System.out.println(entry.getKey() + " = " + entry.getValue());
    	//迭代器方法遍历过程中可以通过 it.remove(); 删除当前遍历的元素
   		it.remove(); // 避免抛 ConcurrentModificationException 异常
	}
	```

* 字符串
  * 判断字符串 str 是否为null或空串

 	bad：

	 ```Java
	 if (null != str && !("".equals(str))) {
 	 }
	 ```

 	better：

 	```Java
	//可以写成这个样子，! 的优先级比 && 高，不需括号
 	if (null != str && !"".equals(str)) {
 	}

 	//更好的做法
 	if (!TextUtils.isEmpty(str)) {
 	}
 	```

 	> TextUtils 是一个非常好用的工具类，把 List 转成字符串，逗号分隔；逗号分隔的 String 字符串，切割成 List ，分别可以		用 TextUtils 的 join 和 split 方法。
   
   * xxxxx

* 可能报空指针的情况<br>
  * 判断一个字符串的内容是否为某值<br>
  	bad：

 	```Java
  	if (str.equals("hello")) {
  	}
 	```

  	better：

  	```Java
  	// 避免空指针异常，应该把常量写在前面
  	if ("hello".equals(str)) {
  	}
  	```

  * 判断集合某个元素对象的某个字段是否为空

 	bad：
 	
	```Java
 	// 如果元素对象为 null 这里就挂了吧
 	if (null != userList.get(i).name) {
 	}
 	```

 	better：

	```Java
 	// 使用对象的方法或字段时，考虑下对象本身是否可能为 null
 	if (null != userList.get(i) && null != userList.get(i).name) {
 	}
 	```

* 常用资源释放<br>
  * Cursor

 	```Java
 	Cursor cursor = null;
 	try {
        	cursor = context.getContentResolver().query(Uri.parse(PROVIDER_SETTINGFILE), null, null, null, null);
        	if (null == cursor) { // cursor 为 null，下面没法玩
           		return;
        	}
        	//省略 n 行代码...
    	} catch (Exception e) {
        	e.printStackTrace();
    	} finally {
        	try {
           		cursor.close();
        	} catch (Exception e) {
           		e.printStackTrace();
        	}
    	}
 	```

   * 流文件

   * Bitmap

   * WebView

   * Handler

* 其它
  * 反面判断条件

    bad：

    ```Java
    public void testMethod(ArrayList<User> userList) {
    	if (null != userList && userList.size() > 0) {
			for (int i = 0, size = userList.size(); i < size; i++) {
				// ...
			}
		}
	}
    ```

    better：

    ```Java
    public void testMethod(ArrayList<User> userList) {
		if (null == userList || userList.isEmpty()) {
			return;
		}
		for (int i = 0, size = userList.size(); i < size; i++) {
			// ...
		}
	}
    ```

    > 很多比较复杂的层级判断都可以从这些判断的反面出发，来降低程序的复杂性，从而提高可读性。

  * if 与 return 同用

    bad：

     ```Java
    public int testIfElse(String cmd) {
    	 if ("1".equals(cmd)) {
			 return 1;
		 } else if ("2".equals(cmd)) {
			 return 2;
		 } else if ("3".equals(cmd)) {
			 return 3;
		 } else {
			 return 4;
		 }
	 }
    ```

    better：

     ```Java
    public int testIfElse(String cmd) {
        if ("1".equals(cmd)) {
            return 1;
        }
        if ("2".equals(cmd)) {
            return 2;
        }
        if ("3".equals(cmd)) {
            return 3;
        }
        return 4;
    }
   ```







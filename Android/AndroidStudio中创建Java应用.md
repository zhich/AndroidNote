先新建一个Project，然后新建一个Module；

新建Modile时选择Java Library，然后Next直到Finish；

在MyClass里添加静态的main函数：

```Java
package com.zch;

public class MyClass {

    public static void main(String[] args) {
        System.out.println("Hello World.");
    }

}
```

此时还不能直接运行或者调试，需要配置一下。

点击菜单的Run->Edit Configuration，然后点击+选择Application。

![a.png](https://github.com/zhich/Note/blob/master/picture/AndroidStudio中创建java应用/a.png)
一、在使用butterknife的Module的gradle最上面添加

```Java
apply plugin: 'android-apt'
```

然后在dependencies领域添加butterknife依赖的版本

```Java
 compile 'com.jakewharton:butterknife:8.2.1'
 apt 'com.jakewharton:butterknife-compiler:8.2.1'
```

二、在Project的gradle里面的buildscript领域中的dependencies领域添加

```Java
classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
```
经过以上步骤即可使用butterknife。
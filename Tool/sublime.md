## 目录

- [关闭sublime自动检测更新提示](#关闭sublime自动检测更新提示)
- [sublime打开txt文件乱码的问题](#sublime打开txt文件乱码的问题)




### 关闭sublime自动检测更新提示

step 1: 

点击菜单栏 "Preferences" => "Settings-User" , 在最后添加 **"update_check": false**

如：

```Java
{
	"font_size": 14,
	"update_check": false
}
```

step 2:

点击菜单 help => Enter License , 输入如下序列号：

```Java
—– BEGIN LICENSE —–
Michael Barnes
Single User License
EA7E-821385
8A353C41 872A0D5C DF9B2950 AFF6F667
C458EA6D 8EA3C286 98D1D650 131A97AB
AA919AEC EF20E143 B361B1E7 4C8B7F04
B085E65E 2F5F5360 8489D422 FB8FC1AA
93F6323C FD7F7544 3F39C318 D95E6480
FCCC7561 8A4A1741 68FA4223 ADCEDE07
200C25BE DBBC4855 C4CFB774 C5EC138C
0FEC1CEF D9DCECEC D3A5DAD1 01316C36
—— END LICENSE ——
```

step 3:

重启，再打开后就不会有提示更新了。

### sublime打开txt文件乱码的问题

我们使用 Sublime 打开 txt 文件的时候，会经常因为编码的问题造成乱码。这是因为电脑自带的 txt 记事本的默认保存编码格式是 GBK , 而 Sublime text 不支持 GB2312 和 GBK 编码。

幸好，Sublime强大的扩展功能，可以通过其他插件来解决这个问题。这个插件就是 **ConvertToUTF8**

**安装插件**

要安装插件就要通过 Package install 命令来安装，通过 ctrl + shift + p 快捷键呼出命令行，输入 install 进入到 Package install 模式下，然后查找 ConvertToUTF8 . 安装成功后会自动打开该插件的介绍，以及配置文件。然后我们就能看到刚才乱码的文件，已经可以用正确的编码格式打开了。

**卸载插件**

当然，如果我们想要删掉某些插件，可以通过 remove 命令来卸载。然后选择要卸载的插件点击即可。

先导入 commons-io-2.4.jar 这个包，[下载地址](http://commons.apache.org/proper/commons-io/download_io.cgi)

代码如下：

```Java
import java.io.File;
import java.io.IOException;
import java.util.Collection;

import org.apache.commons.io.FileUtils;

public class Transcoding {
	
	public static void main(String[] args) throws IOException {
		//GBK编码格式源码路径
		String srcDirPath="E:\\workspace_android\\MyISchedule\\src";
		//转为UTF-8编码格式源码路径
		String utf8DirPath="E:\\src";
		
		//获取所有java文件 
		Collection<File> javaGbkFiles = 
         FileUtils.listFiles(new File(srcDirPath), new String[]{"java"}, true);
		for(File javaGbkFile : javaGbkFiles){
			//UTF8格式文件路径
			String utf8FilePath =
              utf8DirPath+javaGbkFile.getAbsolutePath().substring(srcDirPath.length());
			//使用GBK读取数据，然后用UTF-8写入数据 
			FileUtils.writeLines(new File(utf8FilePath), "UTF-8",    FileUtils.readLines(javaGbkFile,"GBK"));
		}
	}
}
```


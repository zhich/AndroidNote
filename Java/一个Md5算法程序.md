代码如下：

```Java
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;

public class Md5Util {

	private static final char HEX_DIGITS[] = { '0', '1', '2', '3', '4', '5',
		'6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F' };
	
	public static void main(String[] args) {
		System.out.println(md5("axadwrfgfdy56774fgfgfggff2t"));
		System.out.println(md5("axadwrfgfdy56774fgfgfggff2t"));
	}
	
	/**
	 * @param s：要加密的字符串
	 * @return 密文
	 */
	public static String md5(String s) {
		try {
			MessageDigest digest = java.security.MessageDigest.getInstance("MD5");
			if(s!=null){
				digest.update(s.getBytes());
				byte messageDigest[] = digest.digest();
				String hs=toHexString(messageDigest);
				if(hs.length()>16){
					hs=hs.substring(0, 15);
				}
				return hs;
			}
			
		} catch (NoSuchAlgorithmException e) {
			e.printStackTrace();
		}
		return "";
	}
	
	public static String toHexString(byte[] b) {
		StringBuilder sb = new StringBuilder(b.length * 2);
		for (int i = 0; i < b.length; i++) {
			sb.append(HEX_DIGITS[(b[i] & 0xf0) >>> 4]);
			sb.append(HEX_DIGITS[b[i] & 0x0f]);
		}
		return sb.toString();
	}
}
```

输出结果如下：

```Java
F5D04F35F39B7D4
F5D04F35F39B7D4
```
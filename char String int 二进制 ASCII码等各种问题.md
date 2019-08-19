# char 转换  int

直接转换会变成ASCII码

处理办法：

（1）把char转成字符串， Integer.parseInt(""+'1')
（2）因为char只能存储一个字符 所以一定是0-9    

​	char a=‘5'';			int x = a-'0';

# String反序

```
builder=builder.reverse();
```

# String字符串中查找小字符串

- haystack.**indexOf**(needle);

- ```
   for (int i=0;i<=haystack.length()-needle.length();i++){
   	  if (haystack.substring(i,i+needle.length()).equals(needle)){
                      System.out.println(i);;
         }
   }
  ```
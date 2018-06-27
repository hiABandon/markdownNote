how2j java中级 I/O
===

文件对象
---

文件和文件夹都用**File**表示
- 三种创建文件对象的方式
  - `File f1 = new File("d:/lolFolder");`
    - 参数为**绝对路径**
  - `File f2 = new File("lol.exe");`
    - 参数为**相对路径**，是相对于工作目录。eclipse中是相对于项目目录
  - `File f3 = new File(f1, "LOL.exe");`
    - 把f1作为**父目录**创建文件对象
    - 那么LOL.EXE就是相对于父目录的相对路径了
- 可以使使用 `f1.getAbsolutePath()---String`得到绝对路径

#### 文件的常用方法1
- f.exists();
  - 文件是否存在
- f.isDirectory();
  - 是否是文件夹
- f.isFile();
  - 是否是文件
- f.length();
  - 文件长度
- f.lastModified();
  - 文件最后修改时间，返回的是long,距离时间原点的毫秒
- f.setLastModified(0);
  - 设置文件修改时间为xxx，参数为距离时间原点的毫秒数

#### 文件的常用方法2
- f.list();---String[]
  - 返回当前文件夹下所有文件
- f.listFiles();
  - 以File[]返回当前文件夹下所有的文件
- f.getParent();
  - 以字符串形式返回获取所在文件夹
- f.getParentFile();
  - 以文件形式返回获取所在文件夹
- f.mkdir();
  - 创建文件夹，若父文件夹skin不存在，创建无效
- f.mkdirs();
  - 创建文件夹，若父文件夹skin不存在，就会创建父文件夹
- f.createNewFile();
  - 创建一个空文件，如果父文件夹skin不存在就会抛出异常
- f.getParentFile().mkidrs();
  - 创建一个空文件夹，并在此前创建父目录
- f.listRoots();
  - 列出所有盘符：c,d,e等
- f.delete();
  - 删除文件
- f.deleteOnExit();
  - JVM结束的时候，删除文件
***

流
---
- 当**不同的介质**之间有数据交互的时候，java就用流来实现。
- 数据源可以是
  - 文件
  - 数据库
  - 网络
- **InputStream** 输入流  **OutputStream** 输出流

#### 文件输入流 FileInputStream
创建了文件输入流，就可以使用该流将数据从硬盘的文件读取到JVM(内存)
`FileInputStream fis = new FileInputStream(new File);`


```java
public static void main(String[] args) {
  try {
    File f = new File("d:/lol.txt");
    // 创建基于文件的输入流
    FileInputStream fis = new FileInputStream(f);
    // 通过这个输入流，就可以把数据从硬盘读取到Java虚拟机中
    // 也就是读取到内存中
  } catch(Exception e) {
    e.printSrackTrace();
  }
}
```

字节流
---
- InputStream 字节输入流
- OutputStream 字节输入流
- 用于以字节的形式读取和写入数据

#### InputStream 读取文件
- 是字节输入流，同时也是抽象类，只提供方法声明
- FileputStream是InputStream子类

```java
public static void main(String[] args) {
  try {
    File f = new File("d:/lol.txt");
    FileInputStream fis = new FileInputStream(f);
    byte[] all = new byte[(int)f.length()];
    // 以字节流的形式读取文件所有内容
    fis.read(all);
    for(byte b : all) {
      System.out.print(b);
    }
    fis.close();//使用完流后应该将其关闭
  } cathch (IOExcpetion e) {
    e.printSrackTrace();
  }
}
```

#### OutputStream 向文件写入数据

- OutputStream是字节输出流，抽象类
- FileOutputStream是OutputStream子类，以FileOutputStream为例向文件写出数据
- 注：若d:/lol2.txt不存在，则操作会自动创建该文件
  - 若文件d:/xyz/lol2.txt中，目录xyz不存在，则会抛出异常

```java
public static void main(String[] args) {
  try {
    File f = new File("d:/lol2.txt");
    // 字节数据，用88,89表示对应字符x,y
    byte data[] = {88,89};
    // 创建基于文件的输出流
    FileOutputSteam fos = new FileOutputStream(f);
    //把数据写入到输出流
    for.write(data);
    // 关闭输出流
    fos.close();
  } catch(Exception e) {
    e.printSrackTrace();
  }
}
```

关闭流的方式
---

#### 在try中关闭
在try的作用域里关闭文件输入流
- 弊端：
  - 当读取出现问题抛出异常，就不会执行关闭的代码。**不推荐** 使用。

#### 在finally中关闭
标准的关闭流方式，但繁琐
```java
File f = new File(d:/lol.txt);
FileInputStream fis = null;
try {
  fis = new FileInputStream(f);
  byte[] all = new byte[(int) f.length()];
  fis.read(all);
  for(byte b : all) {
    System.out.print(b);
  }
} catch(Exception e) {
  e.printSrackTrace();
} finally {
  if(null != fis)
    try {
      fis.close();
    } catch(Exception e) {
      e.printSrackTrace();
    }
}
```
#### 使用try()的方式
把流定义在try()里，try,catch或者finally结束的时候会自动关闭。
该方式叫做try-with-resources

字符流
---
- Reade字符输入流
``` java
FileReader fr = new FileReader(f);
charp[] all = new char[(int) f.length()];
fr.read(all);
// 以字符流的形式读取文件所有内容
```
- Writer字符输出流
```java
FileWriter fr = new FileWriter(f);
String data="abcdefg123456790";
char[] cs = data.toCharArray();
fr.write(cs);
// 以字符流的形式吧数据写入到文件中
```
- 专门用于字符的形式读取和写入数据

```java
public class TestStream {
  public static void main(String[] args) {
    File f = new File("d:/lol.txt");
    try (FileReader fr = new FileReader(f); ) {
      char[] all = new char[(int) f.length()];
      fr.read(all);
      for(char b : all) {
        System.out.print(b);
      }
    } catch(Exception e) {
      e.printSrackTrace();
    }
  }
}

```

中文问题
---

缓存流
---
**字节流**和**字符流**是以硬盘位介质
- 每一次读写都会访问硬盘，当读写频率较高时，性能表现不佳

为了解决上述弊端，采用缓存流

缓存流在读取的时候，**会一次性读取较多的数据到缓存中**，以后每一次的读取都在缓存中访问,直到缓存中访问完了，再到硬盘中访问。

 写入数据同理

 #### 使用BufferedReader一次读取一行数据

BufferedReader有点像Scanner有readLine（）方法，构造的时候把字符流封装到BufferedReader里；

 ```java
 public static void main(String[] args) {
   File f = new File("d:/lol.txt");
   try {
     FileReader fr = new FileReader(f);
     BufferedReader br = new BufferedReader(fr);
     while (true) {
       String line = br.readline();
       if(null == line)
        break;
      System.out.print(line);
     }
   } catch(Exception e) {
    e.printSrackTrace();
   }
 }
 ```
#### 使用PrintWriter缓存字符输出流，一次写一行数据
```java
File f = new File("d:/lol2.txt");
FileWriter fw = new FileWriter(f);
PrintWriter pw = new PrintWriter(fw);
pw.println("garen kill teemo");
pw.println("fucking");
```


#### 使用flush把数据立刻写入到硬盘
```java
FileWriter fr = new FileWriter(f);
PrintWriter pw = new PrintWriter(fr);
pw.println("teemo revive after 1 minutes");
pw.flush();
```

数据流
---
- DataInputStream 数据输入流
- DataOutputStream 数据输出流

使用数据流的writeUTF和readUTF()可以进行数据的**格式化顺序读写**

注： 要用DataInputStream 读取一个文件，这个文件必须是由DataOutputStream 写出的，否则会出现EOFException，因为DataOutputStream 在写出的时候会做一些特殊标记，只有DataInputStream 才能成功的读取。

```java
public class TestStream {

    public static void main(String[] args) {
        write();
        read();
    }

    private static void read() {
        File f =new File("d:/lol.txt");
        try (
                FileInputStream fis  = new FileInputStream(f);
                DataInputStream dis =new DataInputStream(fis);
        ){
            boolean b= dis.readBoolean();
            int i = dis.readInt();
            String str = dis.readUTF();

            System.out.println("读取到布尔值:"+b);
            System.out.println("读取到整数:"+i);
            System.out.println("读取到字符串:"+str);

        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    private static void write() {
        File f =new File("d:/lol.txt");
        try (
                FileOutputStream fos  = new FileOutputStream(f);
                DataOutputStream dos =new DataOutputStream(fos);
        ){
            dos.writeBoolean(true);
            dos.writeInt(300);
            dos.writeUTF("123 this is gareen");
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
}
```

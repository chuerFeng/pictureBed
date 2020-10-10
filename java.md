## IO

### 节点流类型

| 类型          | 字符流                               | 字节流                                          |
| ------------- | ------------------------------------ | ----------------------------------------------- |
| File(文件)    | FileReader<br />FileWriter           | FileInputStream<br />FileOutputStream           |
| Memory Array  | CharArrayReader<br />CharArrayWriter | ByteArrayInputStream<br />ByteArrayOutputStream |
| Memory String | StringReader<br />StringWriter       | -                                               |
| Pipe(管道)    | PipedReader<br />PipedWriter         | PipedInputStream<br />PipedOutputStream         |



#### InputStream例子

```java
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;
public class TestFileInputStream {
	public static void main(String[] args) {
		int b = 0;
		FileReader fr = null;
		try {
			fr = new FileReader("xxxxx"); // 绝对路径
		} catch ( FileNotFountException e ) {
			System.out.print("找不到文件")
			System.exit(-1);
		}
		
		try {
			long num = 0;
			while( (b = fr.read()) != -1 ){
            	System.out.print((char)b);
            	num++;
			}
			fr.close();
			System.out.println();
			System.out.println("共读取了"+num+"个字节");
		} catch( IOException e ) {
			System.out.println("文件读取错误");
			System.exit(-1)
		}
	}
}
```



#### OutputStream例子

```java
package test;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;

public class TestFileOutputStream {

	public static void main(String[] args) {
		int b = 0;
		FileInputStream iStream = null;
		FileOutputStream oStream = null;
		
		try {
			iStream = new FileInputStream("D:\\work\\test\\src\\test\\TestArgsWords.java");
			oStream = new FileOutputStream("D:\\work\\test\\src\\test\\newFile.txt");
			
			while ((b = iStream.read()) != -1) {
				oStream.write(b);
			}
			iStream.close();
			oStream.close();
		} catch (FileNotFoundException e) {
			System.out.println("文件未找到");
			System.exit(-1);
		} catch (IOException e) {
			System.out.print("文件复制错误");
			System.exit(-1);
		}
		System.out.print("文件已复制");
	}

}
```





### 缓冲流

缓冲流要“套接”在节点流之上，对读写的数据提供了缓冲的功能，提高了读写的效率，并提供了一些新的方法。

J2SDK提供了四种缓冲流，常用的构造方法为:

```
BufferedReader(Reader in)
BufferedReader(Reader in, int sz) // sz为自定义缓冲区大小
BufferedWriter(Writer out)
BufferedWriter(Writer out, int sz)
BufferedInputStream(InputStream in)
BufferedInputStream(InputStream in, int sz)
BufferedOutputStream(OutputStream in)
BufferedOutputStream(OutputStream in, int sz)
```

```java
import java.io.*;
public class bufferWriter {

	public static void main(String[] args) {
		try {
			BufferedReader bf = new BufferedReader( new FileReader( "D:\\leisureWork\\test\\src\\TestFileInputStream.java"));
			BufferedWriter bw = new BufferedWriter(new FileWriter( "D:\\leisureWork\\test\\src\\new.java"));
			
			String s = null;
			for( int i =0; i <= 100; i++  ) {
				s = String.valueOf(Math.random());
				bw.write(s);
				bw.newLine();
			}
			bw.flush();
			while ( (s = bf.readLine()) != null  ) {
//				bw.write(s);
				System.out.println(s);
			}
			bw.close();
			bf.close();
			
			
		} catch (FileNotFoundException e) {
			System.out.println("文件未找到");
			System.exit(-1);
			e.printStackTrace();
			
		} catch (IOException e) {
			System.out.println("文件读取错误");
			e.printStackTrace();
			System.exit(-1);
		}
	}
}

```



### 转换流

`InputStreamReader `和` OutputStreamWriter`用与字节数据到字符数据之间的转换

`InputStreamReader` 需要和` InputStream`  "套接"

`OutputStreamWriter` 需要和` OutputStream`(FileOutputStream, ) "套接"

转换流在构造是可以指定其编码集合例如：

```
InputStream isr = new InputStreamReader( System.in, "ISO8859_1" );
```



```java
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStreamWriter;

public class TestTransform {

	public static void main(String[] args) {
		try {
			OutputStreamWriter ow = new OutputStreamWriter( 
					new  FileOutputStream("D:\\leisureWork\\test\\src\\newFile.txt") );
			
			ow.write("fdsfsdfsdf4132f12ds");
			ow.close();
			ow = new OutputStreamWriter( 
					new FileOutputStream("D:\\leisureWork\\test\\src\\newFile.txt", true), "ISO8859_1");
			
			ow.write("45646161231");
			System.out.println(ow.getEncoding());
			ow.close();			
			
		} catch (FileNotFoundException e) {
			System.out.println("文件未找到");
			e.printStackTrace();
		} catch (IOException e) {
			System.out.print("文件复制错误");
			e.printStackTrace();
		}
	}
}

```



### Object流

`ObjectOutputStream` 和 `ObjectInputStream` 分别与`FileOutputStream` 和 `FileInputStream`  一起使用时，可以为应用程序提供对对象图形的持久存储。ObjectInputStream  用于恢复那些以前序列化的对象。其他用途包括使用套接字流在主机之间传递对象，或者用于编组和解组远程通信系统中的实参和形参。 

- **序列化：将对象写入到IO流中**
- **反序列化：从IO流中恢复对象**
- **意义：序列化机制允许将实现序列化的Java对象转换位字节序列，这些字节序列可以保存在磁盘上，或通过网络传输，以达到以后恢复成原来的对象。序列化机制使得对象可以脱离程序的运行而独立存在。**

**使用场景：所有可在网络上传输的对象都必须是可序列化的，**比如RMI（remote method invoke,即远程方法调用），传入的参数或返回的对象都是可序列化的，否则会出错；**所有需要保存到磁盘的java对象都必须是可序列化的。通常建议：程序创建的每个JavaBean类都实现Serializeable接口。**

**如果想要把某个类的对象序列化，必须得实现 `Serializable` 接口**

```
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.io.Serializable;

public class TestObjectIO {

	public static void main(String[] args) throws IOException {
		T t =  new T();
		t.k = 881;
		try {
			FileOutputStream fos = new FileOutputStream("D:\\leisureWork\\test\\src\\newFile2.dat");
			ObjectOutputStream oos = new ObjectOutputStream(fos);
			oos.writeObject( t );
			oos.flush();
			oos.close();
			
			FileInputStream fls = new FileInputStream("D:\\leisureWork\\test\\src\\newFile2.dat");
			ObjectInputStream ois = new ObjectInputStream(fls);
			T readT = (T)ois.readObject();
			System.out.println("readT: "+ readT.k); // 881
			System.out.println("readT: "+ readT.L); // 0 
			
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (ClassNotFoundException e) {
			System.out.println("找不到序列化对象的类");
			e.printStackTrace();
		}

	}
	
}
class T implements Serializable {
	int i = 10;
	int j = 9;
	double d = 2.3;
	int k = 5;
	transient int L = 23;
}
```

#### transient 关键字 

意思为透明，可以用来修饰修饰成员变量. 被transient修饰的变量在序列化的时候`不予考虑`，赋值为默认的值

#### Serializable接口

用来标记该对象可以序列化，是ObjectStream`序列化`时必须要声明的

#### Externalizable接口

用于自定义序列化过程

+ readExternal
+ writeExternal



## 线程

线程是一个程序里面不同的执行路径

### 声明方式

	#### 第一种

 + 定义线程类实现Runnable接口

 + Thread myThread = new Thread(tarage) // tarage为Runnable接口类型

 + Runnable中只有一个方法

   	+  public void run() 用以定义线程运行体

 + 使用Runnable接口可以为多个线程提供共享数据

 + 在实现Runnable接口的类的run方法定义中可以使用Thread的静态方法

    +  public static Thread currenThead() 获取当前线程的引用

   

#### 第二种

+ 可以定义一个Thread的子类并重写其run方法

  ```
  class MyThread extends Thead {
  	public void run() {}
  }
  ```

  

+ 然后生成该类的对象

  ```
  MyThread mt = new MyThread()
  ```

```

public class TestThread {

	public static void main(String[] args) {
		Runner1 runner1= new Runner1();
		runner1.run();  // 阻塞式  非线程

		// 新建线程
		Thread t = new Thread(runner1); 
		t.start(); // 线程启动
		
		for (int i = 0; i <= 100; i++) {
			System.out.println("Main run " + i);
		}
	}
}

class Runner1 implements Runnable {
	public void run() {
		for( int i = 0; i <= 100; i++ ) {
			System.out.println("Runner"+ i );
		}
	}
}
```



### 线程的同步

#### 锁线程

关键字： **synchronized**

```java
public class TestSync implements Runnable{
	Timer timer = new Timer();
	public static void main(String[] args) {		
		TestSync testSync = new TestSync();
		
		Thread t1 = new Thread(testSync);
		Thread t2 = new Thread(testSync);
		t1.setName("test1");
		t2.setName("test2");
		t1.start();
		t2.start();
	}
	
	public void run() {
		timer.add( Thread.currentThread().getName() );
	}
}

class Timer {
	private static int num = 0;
	// 第一种
	public void add(String name) {
		synchronized (this) {
			num++;
			try {
				Thread.sleep(1);
			} catch (InterruptedException e) {}
			System.out.println(name + "这是第" + num + "个线程");
		}
	}
 
	// 第二种
    /*public synchronized void add(String name) {
            num++;
            try {
            Thread.sleep(1);
            } catch (InterruptedException e) {}
            System.out.println(name + "这是第" + num + "个线程");
        }
    }*/


```



#### 死锁

每个对象都对应一个可称为“互斥锁”的标记，这个标记保证在任一时刻，只能有一个线程访问该对象

```java

public class TestDeadLock implements Runnable{

	public int flag = 0;
	static Object o1 = new Object(),   o2 = new Object();
	@Override
	public void run() {
		System.out.println("flag:" + flag);
		if (flag == 1) {
			synchronized (o1) {
				try {
					Thread.sleep(500);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				
				synchronized (o2) {
					System.out.println("1");
				}
			}
		}
		
		if (flag == 0) {
			synchronized (o2) {
				try {
					Thread.sleep(500);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				
				synchronized (o1) {
					System.out.println("2");
				}
			}
		}
		
	}
	
	
	public static void main(String[] args) {
		TestDeadLock t1 = new TestDeadLock();
		TestDeadLock t2 = new TestDeadLock();
		t1.flag = 0;
		t1.flag = 1;				
		Thread thread1 = new Thread(t1);
		Thread thread2 = new Thread(t2);
		thread1.start();
		thread2.start();
	}

}

```

https://raw.githubusercontent.com/chuerFeng/pictureBed/master

![](https://github.com/img/20200925183955.png)

发


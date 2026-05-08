---
title: Java
date: 2024-11-26 19:46:26
tags:
author: 你好
cover: /images/Java.png # 文章封面
---

# Java

## 结构图（doc 文件和 txt 文件在 untitled 文件里）

<!-- ![结构图]("../../themes/butterfly/source/img/Java结构图.png") -->

![结构图](/images/Java_jgt.png)

## BuffersdTest

```

import java.io.*;
public class BuffersdTest {
    static File file1,file2;
    static FileInputStream finStream;
    static FileOutputStream foutStream;
    static InputStreamReader inStreamReader;
    static OutputStreamWriter outStreamWriter;
    static BufferedReader br;
    static BufferedWriter wr;
    public static void main(String[]args) {
        System.out.println(".\\shao");
        try {
            // file1指定到需要读的文件路径
            file1 = new File(".\\少年闰土.doc");
            file2 = new File(".\\少年闰土.txt");
            finStream = new FileInputStream(file1);
            foutStream = new FileOutputStream(file2);
            br = new BufferedReader(new InputStreamReader(finStream,"UTF-8"));
            wr = new BufferedWriter(new OutputStreamWriter(foutStream));
            String str = null;
            System.out.println("读写开始....");
            while ((str = br.readLine()) != null) {
                System.out.println(str);
                str = str + "\r\n";
                wr.write(str, 0, str.length());
                wr.flush();
            }
        }
        catch (FileNotFoundException e) {e.printStackTrace();}
        catch (IOException e) {e.printStackTrace();}
    }
}

```

## EmpDemo.java

```

abstract class Employee
{
    int salary;
    void setSalary(int salary)
    {
        this.salary=salary;
    }

    abstract void showSalary();
}

class Saler extends Employee{
    void showSalary()
    {
        System.out.println("销售员的月薪为￥"+salary*8*30);
    }
}
class Manager extends Employee{
    void showSalary()
    {
        System.out.println("经理的月薪为：￥"+salary);
    }
}
public class EmpDemo {
    public static void main(String[] args){
        Employee emp;
        emp=new Saler();
        emp.setSalary(15);
        emp.showSalary();
        emp=new Manager();
        emp.setSalary(5000);
        emp.showSalary();
    }
}

```

## FileDemo

```

import java.io.*;

public class FileIOTest {
    public static void main(String[] args){
        FileReader fr=null;
        FileWriter fw=null;
        try {
            <!-- 改为自己的路径 -->
            fr=new FileReader(".\\少年闰土.doc");
            fw=new FileWriter(".\\少年闰土1.txt");
            char c[]=new char[260];
            while (fr.read(c)!=-1){
                String str=new String(c);
                System.out.println("正在写入："+str);
                fw.write(c);
            }
            fr.close();
            fw.close();
        }
        catch (FileNotFoundException e){e.printStackTrace();}
        catch (IOException e){e.printStackTrace();}
    }
}

```

## InnerClassDemo

```

class OuterClass{
    String str;
    boolean outerClassAccessible;
    InnerClass in;
    public OuterClass(){
        str=new String("OuterClass variable");
        outerClassAccessible=true;
        InnerClass in = new InnerClass();
        System.out.println(str);
        System.out.println("外部类>访问外部类成员:"+outerClassAccessible);
        System.out.println("外部类>访问内部类成员:"+in.innerClassAccessible);
    }
    class InnerClass{
        String str;
        boolean innerClassAccessible;
        public InnerClass(){
            str=new String("InnerClass variable");
            innerClassAccessible=true;
            System.out.println(str);
            System.out.println("内部类>访问外部类成员:"+outerClassAccessible);
            System.out.println("内部类>访问内部类成员:"+innerClassAccessible);
        }
    }
}
class InnerClassDemo{
    public static void main(String args[]){
        OuterClass out=new OuterClass();
    }
}

```

## StudentInfo(需要与 TestArrayList 一起使用)

```

public class StudentInfo {
    int id;
    String name;
    String tel;
    public StudentInfo(int id,String name,String tel){
        this.id = id;
        this.name=name;
        this.tel=tel;
    }
    public int getId(){
        return id;
    }
    public void setId(int id){
        this.id=id;
    }
    public String getName(){
        return name;
    }
    public void setName(String name){
        this.name=name;
    }
    public String getTel(){
        return tel;
    }
    public void setTel(String tel){
        this.tel=tel;
    }
    public String toString(){
        return name+"的联系电话是:"+tel;
    }
}

```

## TestArrayList

```
import java.util.ArrayList;
import java.util.List;

public class TestArrayList02 {
    public static void printInfo(List arrList){
        for(int i=0;i<arrList.size();i++){
            StudentInfo stu = (StudentInfo) arrList.get(i);
            System.out.println("学号:"+stu.getId()+";姓名:"+stu.getName()+";电话号码:"+stu.getTel());
        }
    }
    public static void main(String[] args){
        StudentInfo stu01 = new StudentInfo(1,"张华","1111111");
        StudentInfo stu02 = new StudentInfo(2,"李平","2222222");
        StudentInfo stu03 = new StudentInfo(3,"王杰","3333333");
        List stuList = new ArrayList();
        stuList.add(stu01);
        stuList.add(stu02);
        stuList.add(stu03);
        StudentInfo stu04 = new StudentInfo(4,"陈波","4444444");
        stuList.add(2,stu04);
        printInfo(stuList);
        if(stuList.contains(stu04)){
            System.out.println("有陈波同学");
        }else {
            System.out.println("没有陈波同学");
        }
        //stuList.remove(1);//
        System.out.println("目前学生总数为:"+stuList.size()+"人");
    }
}

```

## Talk(跟 TalkServer 搭配使用，需先运行 TalkServer 在运行 Talk 文件)

```

import java.io.*;
import java.net.*;
import java.util.logging.*;

public class Talk {
    // 创建日志记录器
    private static final Logger logger = Logger.getLogger(Talk.class.getName());

    public static void main(String[] args) {
        try {
            // 建立与服务器的连接
            Socket clientSocket = new Socket("127.0.0.1", 2000); // 连接到本地服务器的 2000 端口

            // 建立接收服务器端信息的输入流对象 br
            BufferedReader br = new BufferedReader(new InputStreamReader(clientSocket.getInputStream(), "UTF-8"));

            // 建立向服务器端写出信息的输出流对象 pw
            PrintWriter pw = new PrintWriter(new OutputStreamWriter(clientSocket.getOutputStream(), "UTF-8"));

            // 建立接收本地客户端控制台输入的输入流对象 client_br
            BufferedReader client_br = new BufferedReader(new InputStreamReader(System.in, "UTF-8"));

            System.out.println("聊天客户端已经启动...");

            String client_info = client_br.readLine(); // 从客户端控制台读取信息
            System.out.println("您输出的信息是: " + client_info);

            // 进行聊天，直到输入 "bye" 为止
            while (!client_info.equals("bye")) {
                // 将客户端输入的信息传给服务器端
                pw.println(client_info);
                pw.flush(); // 刷新输出流

                // 输出来自服务器的信息
                System.out.println("来自服务器的信息是: " + br.readLine());

                // 继续接收客户端控制台的输入
                client_info = client_br.readLine();
            }

            // 关闭资源
            br.close();
            pw.close();
            clientSocket.close();
        } catch (Exception ex) {
            // 使用日志记录详细的错误信息
            logger.log(Level.SEVERE, "发生错误：", ex);
        }
    }
}

```

## TalkServer

```

import java.io.*;
import java.net.*;
import java.nio.charset.StandardCharsets;
import java.util.logging.*;

public class TalkServer {
    private static final Logger logger = Logger.getLogger(TalkServer.class.getName());

    public static void main(String[] args) {
        ServerSocket serverSocket = null;

        try {
            // 创建服务器端 ServerSocket 对象，监听端口 2000
            serverSocket = new ServerSocket(2000);
            logger.info("服务器已启动，等待客户端连接...");

            // 不断监听客户端连接
            while (true) {
                Socket clientSocket = serverSocket.accept(); // 接受客户端连接
                logger.info("有新客户端连接： " + clientSocket.getInetAddress().getHostAddress());

                // 为每个客户端连接创建一个新的线程来处理通信
                new Thread(new ClientHandler(clientSocket)).start();
            }

        } catch (IOException ex) {
            logger.log(Level.SEVERE, "服务器发生错误：", ex);
        } finally {
            try {
                if (serverSocket != null) {
                    serverSocket.close();
                }
            } catch (IOException ex) {
                logger.log(Level.WARNING, "关闭服务器套接字时发生错误：", ex);
            }
        }
    }

    // 定义客户端处理器线程
    static class ClientHandler implements Runnable {
        private Socket clientSocket;
        private BufferedReader br;
        private PrintWriter pw;
        private BufferedReader server_br;

        public ClientHandler(Socket clientSocket) {
            this.clientSocket = clientSocket;
        }

        @Override
        public void run() {
            try {
                // 获取客户端输入流和输出流
                br = new BufferedReader(new InputStreamReader(clientSocket.getInputStream(), StandardCharsets.UTF_8));
                pw = new PrintWriter(new OutputStreamWriter(clientSocket.getOutputStream(), StandardCharsets.UTF_8));

                // 获取服务器控制台的输入流
                server_br = new BufferedReader(new InputStreamReader(System.in, StandardCharsets.UTF_8));

                String clientInfo;
                String serverInfo;

                // 不断接收客户端信息并处理
                while (true) {
                    // 获取客户端发来的信息
                    clientInfo = br.readLine();
                    if (clientInfo == null || clientInfo.equals("bye")) {
                        break; // 如果客户端发送 "bye"，则关闭连接
                    }
                    logger.info("来自客户端的信息: " + clientInfo);

                    // 从服务器控制台读取信息并发送给客户端
                    serverInfo = server_br.readLine();
                    pw.println(serverInfo);
                    pw.flush(); // 刷新输出流

                    // 将服务器响应发送给客户端
                    System.out.println("服务器回复: " + serverInfo);
                }

            } catch (IOException ex) {
                logger.log(Level.SEVERE, "客户端通信处理发生错误：", ex);
            } finally {
                try {
                    // 关闭资源
                    if (br != null) br.close();
                    if (pw != null) pw.close();
                    if (clientSocket != null) clientSocket.close();
                } catch (IOException ex) {
                    logger.log(Level.WARNING, "关闭资源时发生错误：", ex);
                }
            }
        }
    }
}

```

## UserDefExceptionDemo

```

class MyArrayException extends NegativeArraySizeException{
    public MyArrayException(String message){
        super(message);
    }
    public String getInfo(){
        String info="如看到此信息,请重新创建数组!";
        return info;
    }
}
public class UserDefExceptionDemo{
    static int size;
    static int a[];
    static void checkArraySize() throws MyArrayException{
        if(size>0){
            a=new int[size];
            System.out.println("数组创建成功!");
        }
        else
            throw new MyArrayException("数组创建错误，请检查下标！");
    }
    public static void main(String[] args){
        if (args.length == 0) {
            System.out.println("请提供数组大小作为命令行参数！");
            return; // 如果没有传递参数，直接结束程序
        }
        size=Integer.parseInt(args[0]);
        try{
            checkArraySize();
        }
        catch (MyArrayException e){
            System.out.println(e.getMessage());
            System.out.println(e.getInfo());
        }
    }
}



```

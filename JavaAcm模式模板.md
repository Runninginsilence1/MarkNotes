# 读取多行整数
```java
import java.util.Scanner;
public class Main {
public static void main(String[] args) {
   Scanner scanner = new Scanner(System.in);
   // 每一个循环里面读取一行数据
   while(scanner.hasNext()) {
        int a = scanner.nextInt();
        int b = scanner.nextInt();
        System.out.println(a+b);
        }
    }
}

```

Input:
1 2
4 5

Output:
3
9


next + 数据类型 从stdin中返回一个对应数据类型的值；
例如scanner.nextInt() 返回的就是一个int类型的数据；


我还记得一个格式化输出的格式：
```java
souf("%1$d %2$s", 1, "str");

// 百分号加数字就表示后面第几个数据； 
```

Output:
1 str


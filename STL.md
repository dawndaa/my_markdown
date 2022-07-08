### STL

###### 	1. memset

```cpp
void *memset(void *s, int c, size_t n);
```

​	作用: 按照字节将 指定内存空间 初始化为 指定值

​	s是该内存空间的起始位置 	n是该内存空间的大小  n代表n字节		c是用来填充的内容 取它的低8位

​	示例: 

```cpp
//字符数组
char a[4];
memset(a, '1', sizeof(a)); // '1'只有八位  这条语句把a的每个元素都初始化为'1'

//整数数组
int a[4];
memset(a, -1, sizeof(a)); 
// -1按照补码表示为 111·····111 这条语句把a的每个元素都初始化为-1

// 结构体 or 结构体数组
typedef strcut stu{
    char name[20];
    int age;
} Stu;
Stu stu1;
memset(stu1, 0, sizeof(stu1));
Stu stu2[10]; 
memset(stu2, 0, sizeof(Stu) * 10);
```


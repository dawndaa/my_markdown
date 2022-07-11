### STL or Algorithm

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

###### 2. priority_queue

```cpp
template<
    class T,
    class Container = std::vector<T>, // 底层容器是vector或者deque
    class Compare = std::less<typename Container::value_type>
> class priority_queue;
```

​	优先队列，也可以叫做堆；默认的优先队列为大根堆，使用less进行比较，每次取出的都是最大的元素；

​	堆是一棵二叉树，对于大根堆来说，根节点是最大的，对于小根堆来说，根节点是最小的

​	底层容器需要满足的条件

```cpp
front();
push_back();
pop_back();
```

​	支持的操作

```cpp
priority_queue<int> pri;
int x;
pri.push(x);
pri.pop();
pri.top();
pri.size();
pri.empty();
pri.emplace(x);
pri.swap(pri_other);
```

​	示例：使用数组实现堆

​		前置知识: 

​			Q:如何通过下标来找到某一节点的子节点或父节点?

​			A:根节点是heap[0]，它的左孩子是heap[1], 右孩子是heap[2]; 依次类推

​			    某一个节点 i 的左孩子是2 * i + 1, 右孩子是2 * i + 2, 其父节点是 (i - 1) / 2；

​			Q:如何完成push和pop操作?

​			A:以大根堆为例，如果要push一个元素，我们需要将它放在最后一处，并和父节点进行比较 直到 该元素小于父节点或者成为根节点

​				如果要pop堆顶，我们需要将堆顶元素删除，然后将最后一个元素放至堆顶，将其和最大的孩子比较 直到 它比两个孩子都大或者没有孩子时

```cpp
# include <bits/stdc++.h>
using namespace std;

/*
实现一个大根堆
*/

const int maxn = 10000;
int heap[maxn], size = 0;

void push(int x) {
    int i = size++; // heap[i]是最后一个元素

    while (i > 0) {
        int p = (i - 1) / 2; // 父节点的index
        if (heap[p] >= x) break; // 当父节点的值大于等于x时 停止

        heap[i] = heap[p]; // 将小于x的父节点拉下来
        i = p; // i到父节点上
    }

    heap[i] = x;  // 更新
}

int pop() {
    int ret = heap[0];  // 获取最大值
    int x = heap[--size]; // 获取最后一个元素

    int i = 0;
    while (2 * i + 1 < size) {
        int a = 2 * i + 1, b = 2 * i + 2; // a b 分别为左孩子 右孩子 
        if (b < size && heap[b] > heap[a]) a = b; // 默认选择更大的孩子进行比较

        if (heap[a] <= x) break;  // 当x比两个孩子都大时 停止

        heap[i] = heap[a]; // 把节点a提上来
        i = a; // 移动到a处
    }

    heap[i] = x;  // 更新
    return ret;
}

int top() {
    return heap[0];
}

int main() {
    for (int i = 0; i < 100; ++i) push(i);
    for (int i = 0; i < 100; ++i) {
        cout << top() << " ";
        pop();
    }

    system("pause");
    return 0;
}
```

###### 3.并查集

​	用于管理元素分组，支持合并两个分组，以及查询两个元素是否在同一分组

​	主要操作

```cpp
unite(); // 合并
find(); // 查找根节点
same(); // 判断是否在同一个组 
init(); // 初始化数组
```

​	通过路径压缩 以及 按秩合并 可以大大降低时间复杂度

```cpp
vector<int> pre, rk;
void init(int n) {
	pre.assign(n, 0);
	rk.assing(n, 1);  // 初始化高度为1
	iota(pre.begin(), pre.end(), 0); // 初始化每个节点的父节点为本身
}

int find(int x) {
    if (pre[x] == x) return x;
    else return pre[x] = find(pre[x]); // 路径压缩
}

bool same(int x, int y) {
    return find(x) == find(y);
}

bool unite(int x, int y) { // 按秩合并
    x = find(x), y = find(y);
    if (x == y) return false; // 合并失败
    
    if (rk[x] < rk[y]) pre[x] = y; // 将秩低的连接到秩高的上
    else {
        if (rk[x] == rk[y]) ++rk[x];
        pre[y] = x;
    }
    
    return true; // 合并成功
}
```

###### 4.单源最短路

 	1. bellman-ford算法: 
     	1. 描述: es为边的集合  d[i]为i到源点的距离(初始化为d[源点]为0 其余为inf)  每次遍历所有边 寻找是否有可以进行更新的距离  如果没有 则说明所有的点到源点的距离都已经达到了最小  因此停止循环
     	2. 判负环: 对于无负环的图  最多对边集进行进行v - 1次松弛操作(遍历边集)  而对于有负环的图  可以无限制的进行松弛操作  因此可以通过松弛操作的次数来判断是否存在负环  如果将d[i]全部初始化为0  可以通过d[i]的值找出所有负环
     	3. 优化: 可以使用队列进行优化  因为之后松弛成功之后的节点  才可能作为下一次松弛的起点  所以可以将所有松弛成功的节点加入队列 来降低时间复杂度
     	4. 代码

```cpp
struct edge {int from, to, cost};
edge es[max_e];
int v, e, d[max_v];
int inf = 0x3f3f3f3f;

void shortest_path(int s) {
	memset(d, 0x3f, sizeof(d));
	d[s] = 0;
	
	while (true) {
		bool update = false;
		for (int i = 0; i < e; ++i) {
			edge tmp = es[i];
			if (d[tmp.from] != inf && d[tmp.to] < d[tmp.from] + tmp.cost) {
				d[tmp.to] = d[tmp.from] + tmp.cost;
				update = true;
			}
		}
		if (!update) break;
	}
}
```


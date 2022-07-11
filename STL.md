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

###### 4.最短路

1. dijkstra算法:

   1. 描述：每次找出已经确定了到源点的最短距离的点，以该点为中转，对其它和它相连的点进行更新距离，直到所有点的最短距离已经确定。为了判断一个点到源点的最短距离是否已经确定，需要维护一个点集，将已经确定的点加入集合中
   2. 缺陷: 无法处理存在负边的情况
   3. 优化：使用优先队列进行优化，以距离作为排列标准，每次弹出距离最短的点，并将成功更新的点加入到优先队列中
   4. 代码

   ```cpp
   #include<bits/stdc++.h> 
   using namespace std; 
   # define INF 0x3f3f3f3f 
     
   // iPair ==> Integer Pair（整数对）
   typedef pair<int, int> iPair; 
     
   // 加边
   void addEdge(vector <pair<int, int> > adj[], int u, int v, int wt) { 
       adj[u].push_back(make_pair(v, wt)); 
       adj[v].push_back(make_pair(u, wt)); 
   } 
     
   // 计算最短路
   void shortestPath(vector<pair<int,int> > adj[], int V, int src) 
   { 
       // 关于stl中的优先队列如何实现，参考下方网址：
       // http://geeksquiz.com/implement-min-heap-using-stl/ 
       priority_queue< iPair, vector <iPair> , greater<iPair> > pq; 
     
       // 距离置为正无穷大
       vector<int> dist(V, INF); 
       vector<bool> visited(V, false);
   
       // 插入源点，距离为0
       pq.push(make_pair(0, src)); 
       dist[src] = 0; 
     
       /* 循环直到优先队列为空 */
       while (!pq.empty()) 
       { 
           // 每次从优先队列中取出顶点事实上是这一轮最短路径权值确定的点
           int u = pq.top().second; 
           pq.pop(); 
           if (visited[u]) {
               continue;
           }
           visited[u] = true;
           // 遍历所有边
           for (auto x : adj[u]) 
           { 
               // 得到顶点边号以及边权
               int v = x.first; 
               int weight = x.second; 
     
               //可以松弛
               if (dist[v] > dist[u] + weight) 
               { 
                   // 松弛 
                   dist[v] = dist[u] + weight; 
                   pq.push(make_pair(dist[v], v)); 
               } 
           } 
       } 
     
       // 打印最短路
       printf("Vertex Distance from Source\n"); 
       for (int i = 0; i < V; ++i) 
           printf("%d \t\t %d\n", i, dist[i]); 
   } 
   int main() 
   { 
       int V = 9; 
       vector<iPair > adj[V]; 
       addEdge(adj, 0, 1, 4); 
       addEdge(adj, 0, 7, 8); 
       addEdge(adj, 1, 2, 8); 
       addEdge(adj, 1, 7, 11); 
       addEdge(adj, 2, 3, 7); 
       addEdge(adj, 2, 8, 2); 
       addEdge(adj, 2, 5, 4); 
       addEdge(adj, 3, 4, 9); 
       addEdge(adj, 3, 5, 14); 
       addEdge(adj, 4, 5, 10); 
       addEdge(adj, 5, 6, 2); 
       addEdge(adj, 6, 7, 1); 
       addEdge(adj, 6, 8, 6); 
       addEdge(adj, 7, 8, 7); 
     
       shortestPath(adj, V, 0); 
     
       return 0; 
   }
   ```

2. bellman-ford算法: 

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

3. floyd算法

- 描述：依次选取每一个节点，以该节点为中转节点，对任意两点之间的距离尝试进行更新
- 判负环：如果存在节点d[i] [i] < 0，说明存在负环
- 代码

```cpp
int d[v][v];
int v;

for (int k = 0; k < v; ++k) 
	for (int i = 0; i < v; ++i)
		for (int j = 0; j < v; ++j)
			d[i][j] = min(d[i][j], d[i][k] + d[k][j]);
```

 4. 路径还原

    有时我们不仅需要最短距离，还需要知道这条最短路上经过的节点，如何去还原这条最短路

    ​	使用prev数组，在最短距离求解过程中，核心的表示式如下

    ```c++
    d[i][j] = min(d[i][j], d[i][k] + d[k][j])
    ```

    ​	当d[i] [j]通过上面这样一个表达式更新成功时，我们可以知道j的前驱节点是k，因此我们可以 prev[i] = k

    ​	这样就可以通过prev数组来还原任意一条选定的最短路

###### 5.最小生成树

​	定义：对于一个带边权的连通图，如果它的一个子图中任意两点都互相连通并且是一棵树，那么这棵树就叫做生成树。当生成树中所有的边权之和最小时，这棵树就叫做最小生成树

​	求最小生成树的两种算法：1.prim算法		2.kruskal算法

- prim算法

  1.描述：任意选取一个节点作为起始节点，从与该节点相连的边中选出最短的那一条，然后将这条边连接的另外一个点加入到集合中；接着对于集合中的所有点，找出和它们相连的所有边中最短的那条，重复上述操作直到所有点都被加入集合中

  2.实现：

  ```cpp
  // 题目负责给出各个节点 以及节点之间存在的边
  priority_queue<pair<int, int>, vector<pair<int, int>>, 
  greater<pair<int, int>>> es[size];
  // 每个节点对应一个优先队列 优先队列中的元素是 <距离，相连节点>
  
  // 初始化es(略)
  
  unordered_set<int> nodes;
  int ret = 0;
  nodes.insert(0);
  while (nodes.size() < sz) {
      int id, dis = INT_MAX;
      for (const auto& n: nodes) {
          auto cur = es[n].top();
          while (nodes.count(cur.second)) {
              es[n].pop();
              cur = es[n].top();
          }
  
          if (cur.first < dis) {
              dis = cur.first;
              id = n;
          }
      }
  
      auto fin = es[id].top();
      es[id].pop();
      nodes.insert(fin.second);
      ret += fin.first;
  }
  
  return ret;
  ```

- kruskal算法

		1. 描述：将边按照权重从小到大排序，每次选取最小的那一条边，如果将这一条边加上之后会构成环，则跳过
		1. 实现：下述为伪代码，正常代码实现需要用到并查集来判断是否成环

```
KRUSKAL-FUNCTION(G, w)
1    F := 空集合
2    for each 图 G 中的顶点 v
3        do 将 v 加入森林 F
4    所有的边(u, v) ∈ E依权重 w 递增排序
5    for each 边(u, v) ∈ E
6        do if u 和 v 不在同一棵子树
7            then F := F ∪ {(u, v)}
8                将 u 和 v 所在的子树合并
```

​		

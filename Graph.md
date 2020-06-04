# DFS(深度優先搜索)
```c++
#include<stdio.h>
#include<string.h>
#define MAX 100
using namespace std;

bool visited[MAX];//标记顶点是否被遍历过
int map[MAX][MAX];
int n;
void DFS(int v)
{
    visited[v]=true;
    printf("%d ",v);
    for(int i=0;i<n;i++)
        if(!visited[i]&&map[v][i]==1)
            DFS(i);
}
//Non-recursive DFS
void DFS_nr(int start) {
    visited[start]= true;
    cout<<start<<" "<< endl;
    stack<int> s;
    s.push(start);
    bool is_push;
    while (!s.empty()) {
        is_push= false;
        int top= s.top();
        for (int i= 0; i< N; i++) {
            if (!visited[i]&& mat[top][i]) {
                visited[i]= true;
                cout<< "visit: "<< i<< endl;
                s.push(i);
                is_push= true;
                break;
            }
        }
        if (!is_push) {
            s.pop();
        }
    }
}
int main()
{
    printf("请输入结点数:");
    scanf("%d",&n);
    memset(map,0,sizeof(map));
    printf("请输入%d*%d的邻接矩阵:\n",n,n);
    for(int i=0;i<n;i++)
        for(int j=0;j<n;j++)
            scanf("%d",&map[i][j]);
    for(int i=0;i<n;i++)
        visited[i]=false;
    printf("遍历的结果为:");
    for(int i=0;i<n;i++)
        if(!visited[i])
            DFS(i);
            //DFS_nr(i);
    printf("\n");
}
```
---
# BFS(廣度優先搜索)
```c++
#include<stdio.h>
#include<string.h>
#include<queue>
#define MAX 100
using namespace std;
bool visited[MAX];//标记顶点是否被遍历过
int map[MAX][MAX];
int n;
void BFS(int v)
{
    printf("%d ",v);
    visited[v]=true;
    queue<int> Q;
    Q.push(v);
    while(!Q.empty())
    {
        int front=Q.front();
        Q.pop();
        for(int i=0;i<n;i++)
            if(!visited[i]&&map[front][i]==1)
            {
                visited[i]=true;
                printf("%d ",i);
                Q.push(i);
            }
    }
}
int main()
{
    printf("请输入结点数:");
    scanf("%d",&n);
    memset(map,0,sizeof(map));
    printf("请输入%d*%d的邻接矩阵:\n",n,n);
    for(int i=0;i<n;i++)
        for(int j=0;j<n;j++)
            scanf("%d",&map[i][j]);
    for(int i=0;i<n;i++)
        visited[i]=false;
    printf("遍历的结果为:");
    for(int i=0;i<n;i++)
        if(!visited[i])
            BFS(i);
    printf("\n");
}
```
---
# Dijkstra
>Question describe:
>给出顶点数(n)、边数(m)、起点(s)、终点(t)，求 s 到 t 的最短路径
```c++
#include <iostream>
#include <cstring>
#define INF 0x3f3f3f3f;
using namespace std;

int map[2550][2550],dis[2550];
bool visited[2550];
int n,m,s,t;

void init() {
    memset(visited, false, sizeof(visited));
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++)
            if (i == j)
                map[i][j] = 0;
            else
                map[i][j] = INF;
    }
}

void dijkstra(){
    for (int i = 1; i <= n; i++) {
        dis[i] = map[s][i];
    }
    visited[s] = true;
    for (int i=1; i<=n; i++) {
        int v = 0;
        int min = INF;
        for (int j=1; j<=n; j++) {
            if (!visited[j] && dis[j] < min) {
                min = dis[j];
                v = j;
            }
        }
        visited[v] = true;
        for (int k=1; k<=n; k++) {
            if (!visited[k] && dis[v] + map[v][k] < dis[k]) {
                dis[k] = dis[v] + map[v][k];
            }
        }
    }
    cout<<dis[t]<<endl;
}

int main(){

    cin>>n>>m>>s>>t;
    init();
    int si,ti,wi;
    for (int i=0; i<m; i++) {
        cin>>si>>ti>>wi;
        if (wi < map[si][ti]) {
            map[si][ti] = map[ti][si] = wi;
        }
    }
    dijkstra();
    return 0;
}
/*
 input:
 7 11 5 4
 2 4 2
 1 4 3
 7 2 2
 3 4 3
 5 7 5
 7 3 3
 6 1 1
 6 3 4
 2 4 3
 5 6 3
 7 2 1
 
 output:
 7
 */
```
>注释(这个也太难啦吧)：松弛松弛，比较比较。

# prim最小生成树

现有村落间道路的统计数据表中，列出了有可能建设成标准公路的若干条道路的成本，求使每个村落都有公路连通所需要的最低成本。

输入格式:

输入数据包括城镇数目正整数N（≤1000）和候选道路数目M（≤3N）；随后的M行对应M条道路，每行给出3个正整数，分别是该条道路直接连通的两个城镇的编号以及该道路改建的预算成本。为简单起见，城镇从1到N编号。

输出格式:

输出村村通需要的最低成本。如果输入数据不足以保证畅通，则输出−1，表示需要建设更多公路。

```
Input:
6 15
1 2 5
1 3 3
1 4 7
1 5 4
1 6 2
2 3 4
2 4 6
2 5 2
2 6 6
3 4 6
3 5 1
3 6 1
4 5 10
4 6 8
5 6 3

output:
12
```
```c++
//No.7
#include <iostream>
#include <vector>
#include <algorithm>
#define INF 0x3f3f3f3f;
using namespace std;

int N, M, result;
int map[1010][1010], lowCost[1010];

int findMin() {
    int minCost = INF;
    int k, j;
    for (k = 0, j = 1; j <= N; j++) {
        if (lowCost[j] && lowCost[j] < minCost) {
            minCost = lowCost[j];
            k = j;
        }
    }
    return k;
}

int prim() {

    for (int i = 1; i <= N; i++) {
        lowCost[i] = map[1][i];
    }
    lowCost[1] = 0;
    for (int i = 1; i < N; i++) {
        int K = findMin();
        if (K) {
            result += lowCost[K];
            lowCost[K] = 0;
            for (int j = 2; j <= N; j++) {
                if (lowCost[j] && map[K][j] < lowCost[j]) {
                    lowCost[j] = map[K][j];
                }
            }
        }
        else
            return -1;
    }
    return result;
}

int main() {
    int a, b, c;
    cin >> N >> M;
    for (int i = 1; i <= N; i++) {
        for (int j = 1; j <= N; j++)
            if (i == j)
                map[i][j] = 0;
            else
                map[i][j] = INF;
    }
    for (int i = 0; i < M; i++) {
        cin >> a >> b >> c;
        map[a][b] = map[b][a] = c;
    }
    cout << prim() << endl;
}
```















## [Codeforces Round 958 (Div. 2)](https://codeforces.com/contest/1988)

### D.  [The Omnipotent Monster Killer](https://codeforces.com/contest/1988/problem/D)

关键的猜想：最多删log(n)次点，而n最多为3e5，log(n)不超过20，故想到树形DP

$F[x][l]$表示点$x$在第$l$轮被删去，x和x子树内所有点贡献的attack points 的总和的最小值。
$g1[x]$表示当$F[x][l]$取最小值时，$g1[x]=l$   ; $g2[x]$表示当$F[x][l]$取次小值时，$g2[x]=l$
则对于$x$来说，$x$不能与其子节点在同一轮删去，故可以根据$g1[y]$（$y$为$x$的儿子节点）是否等于当前的$l$进行不同的状态转移。


```c++
#include <bits/stdc++.h>
#include <iostream>
#include <algorithm>
#include <vector>
typedef long long ll;
using namespace std;
const int N=3e5+1;
ll t,n,a[N];
ll f[N][20],g1[N],g2[N];
vector<ll>e[N];
ll read(){
    char ch=getchar();
    ll fl=1,res=0;
    while((ch>'9'||ch<'0')&&ch!='-')ch=getchar();
    if(ch=='-'){fl=-1;ch=getchar();}
    while(ch>='0'&&ch<='9'){
        res=res*10+ch-'0';
        ch=getchar();
    }
    return res*fl;
}
void dfs(int x,int fa){
    ll sum=0;
    for(int l=1;l<=20;l++){
        f[x][l]=0;
    }
    for(int i=0;i<e[x].size();i++){
        int y=e[x][i];
        if(y!=fa){
            dfs(y,x);
            for(int l=1;l<=20;l++){
                if(g1[y]==l){
                    f[x][l]+=f[y][g2[y]]; //x不能与其子节点在同一轮删去，故此时y的轮次要取次大值的情况。
                }
                else f[x][l]+=f[y][g1[y]];
            }
        }
    }
    g1[x]=0;g2[x]=0;
    f[x][0]=1e18;
    for(int l=1;l<=20;l++){
       f[x][l]+=a[x]*l;
       if(f[x][l]<f[x][g1[x]]){
           g2[x]=g1[x];
           g1[x]=l;
       }
       else if(f[x][l]<f[x][g2[x]]){
           g2[x]=l;
       }
    }
}
void work(){
   n=read();
   for(int i=1;i<=n;i++){
       a[i]=read();
   }
   ll x,y;
   for(int i=1;i<=n;i++)e[i].clear();
   for(int i=1;i<n;i++){
       x=read();y=read();
       e[x].push_back(y);
       e[y].push_back(x);
   }
   dfs(1,0);
   cout<<f[1][g1[1]]<<endl;
}
int main() {
    t=read();
    while(t--){
        work();
    }
    return 0;
}

```

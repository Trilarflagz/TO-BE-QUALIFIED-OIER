## [Codeforces Round 957 (Div. 3)](https://codeforces.com/contest/1992)

### D. [Test of Love](https://codeforces.com/contest/1992/problem/D)

动态规划，注意多组测试数据的初始化

$F[i]$表示到第$i$个点需要经历最少$W$的个数，输出$YES$的条件为$F[n+1]<=k$

```c++
#include <bits/stdc++.h>
#include <iostream>
#include <algorithm>
typedef long long ll;
using namespace std;
const int N=2e5+5;
ll t,n,m,k;
char a[N];
ll f[N];
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
int main() {
    t=read();
    a[0]='L';
    while(t--){
        n=read();m=read();k=read();
        for(int i=1;i<=n;i++){
           cin>>a[i];
           f[i]=1e9;
        }
        a[n+1]='L';//一定要有这一步初始化！不然多组数据会出问题
        f[n+1]=1e9;
        f[0]=0;
        for(int i=1;i<=n+1;i++){
            if(a[i]=='C')continue;
            if(a[i-1]=='W')f[i]=min(f[i],f[i-1]+1);
            for(int j=1;j<=m;j++){
             if(i<j)break;
             if(a[i-j]=='L'){
                 f[i]=min(f[i],f[i-j]);
             }
           }
        }
        if(f[n+1]<=k)cout<<"YES"<<endl;
        else cout<<"NO"<<endl;

    }
    return 0;
}

```

### E. [Novice's Mistake](https://codeforces.com/contest/1992/problem/E)

$a,b$两个变量之间有关联，且范围均小于$1e4$，且容易知道$1<=a-b<=5$ 直接搜索$a$就能很快知道$b$，反正亦然。

在时间复杂度满足题目需求的时候就不用找无用的规律了，否则只能常数优化还浪费时间。

```c++
#include <bits/stdc++.h>
#include <iostream>
#include <algorithm>
typedef long long ll;
using namespace std;
const int N=2e5+5;
ll t,n,m,k;
ll ans1[N],ans2[N];
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
int main() {
    t=read();
    while(t--){
     n=read();
     int tot=0;
     if(n<10){
         for(int b=1;b<=1e4;b++){
             for(int j=1;j<=5;j++){
                 ll a=b+j;
                 if(a>1e4)break;
                 ll cnt=j,res=0;
                 while(cnt){
                     res=res*10+n;
                     cnt--;
                 }
                 if(res==a*n-b){
                     ans1[++tot]=a;ans2[tot]=b;
                 }
             }

         }
     }
     else if(n<100){
         for(int b=1;b<=1e4;b++){
             for(int j=1;j<=5;j++){
                 if((b+j)%2)continue;
                 ll a=(b+j)/2;
                 if(a>1e4)break;
                 ll cnt1=j/2,cnt2=j%2,res=0;
                 while(cnt1){
                     res=res*100+n;
                     cnt1--;
                 }
                 if(cnt2){
                     res=res*10+n/10;
                 }
                 if(res==a*n-b){
                     ans1[++tot]=a;ans2[tot]=b;
                 }
             }

         }
     }
      //本来还写了一部分代码是n==100的情况，最后发现n==100时候答案为0就把这部分删了
     cout<<tot<<endl;
     for(int i=1;i<=tot;i++){
         cout<<ans1[i]<<" "<<ans2[i]<<endl;
     }
    }
    return 0;
}
```

### F. [Valuable Cards](https://codeforces.com/contest/1992/problem/F)

本来想的是贪心+队列求解，后面发现由于要维护队列中元素的大小顺序，且要遍历队列中的每个元素，这不是一个最佳的数据结构，用set会更好，因为同时兼顾了查找和插入的情况。

Set中维护$x$的因子$b$（由当前segment已经出现的一个或者多个$a_i$的乘积得到）。对于当前的数$a$,如果$a$不是$x$的因子就忽略，如果$a$是$x$的因子，就把$a$加入set，且把满足条件$k=a*b$且$k$为$x$的因子的$k$加入set。

#### set

`set` 是关联容器，含有键值类型对象的已排序集，搜索、移除和插入拥有对数复杂度。`set` 内部通常采用 [红黑树](https://oi-wiki.org/ds/rbtree/) 实现。[平衡二叉树](https://oi-wiki.org/ds/bst/) 的特性使得 `set` 非常适合处理需要同时兼顾查找、插入与删除的情况。和数学中的集合相似，`set` 中不会出现值相同的元素。如果需要有相同元素的集合，需要使用 `multiset`。`multiset` 的使用方法与 `set` 的使用方法基本相同。

关于set更详细见[OI wiki关于set](https://oi-wiki.org/lang/csl/associative-container/)

```c++
//#include <bits/stdc++.h>
#include <iostream>
#include <algorithm>
#include <set>
#include <vector>
typedef long long ll;
using namespace std;
const int N=2e5+5;
ll t,n,x;
set<ll>s;
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
int main() {
    t=read();
    while(t--){
        n=read();x=read();
        ll a,ans=1;
        s.clear();
        for(int i=1;i<=n;i++){
            a=read();
            if(x%a)continue;
            int fl=0;
            vector<ll>res;
            for(auto b:s){   //遍历set中每个元素
                ll now=a*b;
                if(now==x){fl=1;break;}
                if(now>x)break;
                if(x%now)continue;
                res.push_back(now);
            }
            if(fl){
                ans++;
                s.clear();
                s.insert(a);
                continue;
            }
            s.insert(a);
            for(auto now:res){ s.insert(now);}
        }
        cout<<ans<<endl;
    }
    return 0;
}
```

### G. [Ultra-Meow](https://codeforces.com/contest/1992/problem/G)

考虑当前对答案有贡献的mex为i，分为两种情况

1)当$i<=n$时，在$i$的左右两边分别插入$x$,$y$个数（对应的子集中包含$x$个数） 则找满足条件 $2*x+y+1=i$的点对$（x,y)$的个数乘以$i$；

2)当$i>n$时，在$i$的左边分别插入$x$个数（对应的子集中包含$x$个数） 则找满足条件 $2*x+1=i$的点$x$的个数乘以$i$；

```c++
#include <bits/stdc++.h>
#include <iostream>
#include <algorithm>
typedef long long ll;
using namespace std;
const int N=2e5+5,mod=1e9+7;
ll t,n;
ll c[5001][5001];
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
void pre(){
    c[0][0]=1;
    for(int i=1;i<=5000;i++){
        c[i][0]=1;
        c[i][i]=1;
        for(int j=1;j<i;j++){
            c[i][j]=(c[i-1][j-1]+c[i-1][j])%mod;
        }
    }
}
int main() {
    t=read();
    pre();
    while(t--){
        n=read();
        ll ans=0;
        for(int i=1;i<=n;i++){
            for(int y=0;y<=n-i;y++){
                int x=i-y-1;
                if(x&1||x<0)continue;
                x=x/2;
                if(x>i-1)continue;
                ans+=c[i-1][x]*c[n-i][y]%mod*i%mod;
                ans=ans%mod;
            }
        }
        for(int i=n+1;i<=2*n+1;i++){
            int x=i-1;
            if(x&1)continue;
            x=x/2;
            ans+=c[n][x]*i%mod;
            ans=ans%mod;
        }
        cout<<ans<<endl;
    }
    return 0;
}
```


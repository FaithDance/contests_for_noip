# Solution Of XJOI-1


[比赛链接 XJOI](http://www.hzxjhs.com:83/contest/753)

------------------------------

## A -钓鱼
### Problem Description
> ![the picture](http://www.hzxjhs.com:83/images/problem/1754/1.jpg)
> 
> 我们把钓鱼的过程放在坐标系里来考虑。图中蓝色的点为船，初始时它的坐标记为
(Ax,y)。河深为y，河宽为x。某个时刻会从左边界或右边界游出来一条鱼(左边的往右边游，右边的往左边游)，即鱼游出来时的横坐标为0或x，这条鱼每秒会游D个单位长度，鱼的长度为L。初始时刻为0，对于每个时刻x，船可以选择花费1s向左或向右移动最多Q个单位长度，或者选择在当前位置进行钓鱼，钓鱼的动作是瞬间的，且发生在时刻x，鱼还来不及移动就被钓上了。如果选择钓鱼，那么在时刻x就不能动。{x+1时刻可以选择移动}设当前位置为z，将鱼看成一条线段，当线段与直线x=z相交时就认为鱼上钩了，所以一次钓鱼动作可能会钓多条鱼。聪明的你告诉钓鱼者，在T时刻前最多能钓多少鱼?
> 
> 输入格式：
> 第一行：T
> 第二行：Maxx,Maxy,表示河宽和河深
> 第三行：两个数Ax,Q
> 第四行：N，表示有N条鱼
> 接下来N行描述每条鱼：每行共五个数，x,y,D,L,time
> x表示鱼头的初始位置，保证为0或maxx，y表示鱼头的初始深度，time表示鱼出现的时
> 刻(所有的数都为整数)
>
> 输出格式：
只有一行：ans，表示最多的钓鱼数
> 
> 样例输入：
> 3
> 4 5
> 4 1
> 3
> 0 1 3 1 0
> 4 2 2 1 0
> 0 3 3 1 2
> 
> 样例输出：
> 3

* Data Limit: 1<=N<=10,1<=T<=14
* Time Limit: 1s
* Memory Limit: 256MB
* Main Solution: 状压DP

### Solution
> 

### Code

```cpp
#include<iostream>
#include<cstdio>
using namespace std;
  
int T,Maxx,Maxy,Ax,Q,n,ans=0;
int dp[11][11][1<<15],bit[11][11],x[11],y[11],l[11],t[11],d[11];
  
void dfs(int x,int t,int st,int get){
    if (t>T){ans=max(get,ans);return;}
    if (dp[x][t][st]>get) return;
    dp[x][t][st]=get;
    for (int i=max(0,x-Q);i<=min(Maxx,x+Q);++i) dfs(i,t+1,st,get);
    int cnt=0,nxt_st=st|bit[t][x];
    for (int i=0;i<n;++i) if (nxt_st&(1<<i)) ++cnt;
    dfs(x,t+1,nxt_st,cnt);
}
  
int main(){
    cin >>T>>Maxx>>Maxy>>Ax>>Q>>n;
    for (int i=0;i<n;++i) scanf("%d%d%d%d%d",&x[i],&y[i],&d[i],&l[i],&t[i]);
    //for (int i=0;i<=T;++i) for (int j=0;j<=Maxx;++j) for (int k=0;k<=(1<<n);++k) dp[i][j][k]=-1;
    for (int i=0;i<=T;++i)
        for (int j=0;j<=Maxx;++j)
            for (int k=0;k<n;++k){
                if (x[k]==0 && (i-t[k])*d[k]>=j && (i-t[k])*d[k]-l[k]<=j) bit[i][j]|=(1<<k);
                if (x[k]==Maxx && Maxx-(i-t[k])*d[k]<=j && Maxx-(i-t[k])*d[k]+l[k]>=j) bit[i][j]|=(1<<k);
            }
    dfs(Ax,0,0,0);
    cout <<ans<<endl;
}
```

-----------------------

## B -雪
### Problem Description
> ![the picture](http://www.hzxjhs.com:83/images/problem/1755/3.jpg)
> 
> 树是由N个等腰三角形构成的，每个三角形的对称轴在y轴上，且每个三角形与上面和下面的三角形有接触。雪以与x轴成a角(0<a<pi)下落,问雪与三角形有接触的长度是多少?(也可以理解为成a角的平行光源射向树，问被照亮的长度)
> 
> 输入格式：
第一行：a
第二行：N
接下来N行：每行2个数，A，B。A表示底边的长度，B表示顶角的大小
(注意：角度都是弧度制)
> 
> 输出格式：
只有一行：ANS（保留1位小数，四舍五入）
> 
> 样例输入：
> 1.570796
> 2
> 2 1.570796
> 4 1.570796
> 
> 样例输出：
> 5.7


* Data Limit: 1<=N<=1000000
* Time Limit: 2s
* Memory Limit: 256MB
* Main Solution: 模拟/计算几何/三角函数


### Solution
> 分类讨论光线照在左边和照在右边的情况。（细节：如果Angle>pi/2，则Angle=pi/2-Angle）
> 
> 总体思路用三角函数来计算边的长度。用左边的计算方法举例：
> 
>  用R表示上一层底平面与光线的交点和中轴的距离。
>  1. 考虑如果上一层的树不能完全覆盖下一层的树，则上一层的R应该就是上一层底边的一半。通过三角函数计算光线与两个三角形的夹三角形的边长，可以算出被找到的长度，并R重新变为此层三角形的底边的一半。
>  2. 考虑如果上一层的树能覆盖下一层树，即下一层不能被照到；因为光线是向左边照的，故R应该会变小（为什么？）。

### Code
```cpp
#include<iostream>
#include<cstdio>
#include<cmath>
#include<cstring>
using namespace std;
const double pi=acos(-1);
struct tree{double nowa,lon,di,high,top;}t[1000010];
 
int main(){
    double a;int n;
    scanf("%lf%d",&a,&n);
    if (a>pi*0.5) a=pi-a;
    for (int i=1;i<=n;++i){
        scanf("%lf %lf",&t[i].di,&t[i].nowa);
        t[i].high=(t[i].di*0.5)/tan(t[i].nowa*0.5);
        t[i].lon=(t[i].di*0.5)/sin(t[i].nowa*0.5);
        //t[i].top=t[i-1].top+t[i].high;
    }
    double ans=t[1].lon,R=t[1].di*0.5;
    for (int i=2;i<=n;++i){
        double x=(R*tan(a))/(tan(pi*0.5-t[i].nowa*0.5)+tan(a));
        double l=x/sin(t[i].nowa*0.5);
        if (l>=t[i].lon) R-=t[i].high/tan(a);
            else ans+=t[i].lon-l,R=t[i].di*0.5;
    }
     
    double L;
    if (pi*0.5-t[1].nowa*0.5<a) L=t[1].di*0.5,ans+=t[1].lon;else L=t[1].high/tan(a);
    for (int i=2;i<=n;++i){
        if (pi*0.5-t[i].nowa*0.5>a) {L+=t[i].high/tan(a);continue;}
        double x=(L*tan(pi*0.5-a))/(tan(t[i].nowa*0.5)-tan(pi*0.5-a));
        double l=(x+L)/sin(t[i].nowa*0.5);
        if (l>=t[i].lon) L+=t[i].high/tan(a);
            else ans+=t[i].lon-l,L=t[i].di*0.5;
    }
    printf("%.1f",ans+0.001);return 0;
}
```

-------------------------------

## C -键盘
### Problem Description
> ![the picture](http://www.hzxjhs.com:83/images/problem/1756/1.jpg)
> 
> 上图是打字时，每根手指所控制的区域。每按一次键，拇指的疲劳度为a，食指的疲劳度为b，中指的疲劳度为c，无名指的疲劳度为d，小指的疲劳度为e。现在让你来设计键盘，即对于一个字符，安排一个键，一个键只拥有一个字符。使得打印给定的文章的疲劳度最小。规定：键盘只包括图中第2行'`'到'\',第三行'Q'到']',第四行'A'到''',第五行'Z'到'/'，第六行的空格键。字符只包括键盘上的字符，对于一个键有2个字符的，只考虑下面那个字符，即对于一个键X，它代表的字符是按一下X所打印的字符，而不是SHIFT+X打印出来的字符。一切标准以上图为主，输入不会出现其他字符。(英文字母为大写)
> 
> 输入格式：
第一行：a,b,c,d,e
第二行：一个数字，N，表示有N个字符
第三行：有N个字符
> 
> 输出格式：
只有一行：ANS
> 
> 样例输入：
> 1 2 3 4 5
10
JUST DO IT
> 
> 样例输出：
> 18

* Data Limit: 1<=N<=1000000
* Time Limit: 1s
* Memory Limit: 256MB
* Main Solution: 贪心/排序


### Solution
> 先数出五个手指各自能控制几个键盘。按花费升序排序，贪心选择即可。

### Code
```cpp
#include<iostream>
#include<cstring>
#include<cstdio>
#include<algorithm>
using namespace std;
int f[200],n,sz=0;
struct finger{int id,w,sz;}fin[10];
struct cha{int recy,asc;char c;}ch[200];
bool cmp1(finger a,finger b){return a.w<b.w;}
bool cmp2(cha a,cha b){return a.recy>b.recy;}
 
int main(){
    for (int i=1;i<=5;++i) scanf("%d",&fin[i].w),fin[i].id=i;
    fin[1].sz=1;fin[2].sz=16;fin[3].sz=8;fin[4].sz=8;fin[5].sz=15;
    scanf("%d\n",&n);
    string ss;getline(cin,ss);
    for (int i=0;i<n;++i) ++f[ss[i]];
    for (int i=1;i<=150;++i) if (f[i]) ch[++sz].asc=i,ch[sz].recy=f[i];
    //for (int i=1;i<=n;++i) scanf("%c",&ch[i].c),
     
    sort(fin+1,fin+6,cmp1);sort(ch+1,ch+1+sz,cmp2);
     
    int now=1,rest=fin[1].sz,ans=0;
    for (int i=1;i<=sz;++i){
        ans+=ch[i].recy*fin[now].w;
        rest--;
        if (rest==0) now++,rest=fin[now].sz;
    }
    cout <<ans<<endl;return 0;
}
```

-------------------


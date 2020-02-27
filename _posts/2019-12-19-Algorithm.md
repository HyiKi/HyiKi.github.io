---
title: Algorithm
date:  2019-12-19 14:20:36 +0800
category: Original
tags: Algorithm
excerpt: 记录一些有趣的算法，想与你分享。
---

#### merge-sort-list
1. 快慢指针找中点
2. 递归合并
3. 归并排序，复杂度O(nlogn)

#### insertion-sort-list
1. 假结点dummy

   ```
   ListNode *dummy = new ListNode(-1), *cur = dummy;
   ```

   避免直接用头结点导致误操作

2. 分别遍历head链表结点node和用cur指针遍历dummy表

3. 插入node结点到dummy的两结点之间或者是一个结点与空结点之间

   ```
   			while (cur->next && cur->next->val <= head->val) {
                   cur = cur->next;
               }
               head->next = cur->next;
               cur->next = head;
   ```

#### binary-tree-postorder-traversal

##### 递归

1. 递归根节点
2. 判空
3. 递归左结点，递归右结点，输出。

##### 迭代

1. 根节点判空

2. 根节点压入栈，将栈写入循环

3. 判断栈顶结点

   ```
   //若无左结点与右结点
   //或者前一个结点不为空且为自己的左结点或右结点
   //则出栈并输出
   if((tmp->left == nullptr && tmp->right == nullptr)||(pre!=nullptr&&(pre == tmp->left || pre == tmp->right))){
   	ans.push_back(tmp->val);
   	pre = tmp;
   	st.pop();
   }
   ```

4. 不是输出的情况则先判空右结点，再判空左结点，不为空则依次进栈

   ```
   if(tmp->right!=nullptr){
   	st.push(tmp->right);
   }
   	if(tmp->left!=nullptr){
   	st.push(tmp->left);
   }
   ```

#### STL

```
reverse(vec.begin(),vec.end());//反转
find(vec.begin(),vec.end(),val);//等于
distance(vec.begin(),it);//距离
lower_bound(vec.begin(),vec.end(),val);//大于等于
upper_bound(vec.begin(),vec.end(),val);//大于
it=unique(vec.begin(),vec.end());erase(it,vec.end());//删除重复元素

priority_queue<int,vector<int>,greater<int> >//升序
priority_queue <int,vector<int>,less<int> >//降序
set<int,greater<int> >//升序 ， 可用于string（按字典序）
set<int,less<int> >//降序
multiset<int>//升序 ， 这个调用STL快
```

#### 并查集

```
int find(int x){
    if(fa[x]==x){
        return x;
    }
    return find(fa[x]);
}
void un(int x,int y){
    int m=find(x);
    int n=find(y);
    if(quan[m]>quan[n]){
        fa[n]=m;
        quan[m]+=quan[n];
    }
    else{
        fa[m]=n;
        quan[n]+=quan[m];
    }
}
bool connected(int x,int y){
    return find(x)==find(y);
}
```

####  拓扑排序（判断成环） 

```
bool f(){
    int cnt=0;
    for(i=1;i<=x;i++){
        if(InDeg[i]==0){
            que.push(i);
        }
    }
    while(!que.empty()){
        int now=que.front();
        cnt++;
        que.pop();
        for(i=0;i<vec[now].size();i++){
            if(--InDeg[vec[now][i]]==0){
                que.push(vec[now][i]);
            }
        }
    }
    return cnt==x;
}
```

#### 快速幂模

```
int quickpower(int n,int m){
    if(m==0){
        return 0;
    }
    if(m==1){
        return n;
    }
    if(m%2==0){
        return quickpower(n,m/2) *quickpower(n,m/2);
    }
    else{
        return quickpower(n,m/2) *quickpower(n,m/2) *n;
    }
}
```

#### KMP

```
void next2(char *ptr,int *next,int pp){
    int k=-1;
    next[0]=-1;
    for(int p=1;p<pp;p++){
        while(k>-1&&ptr[p]!=ptr[k+1]){
            k=next[k];
        }
        if(ptr[p]==ptr[k+1]){
            k++;
        }
        next[p]=k;
    }
}

int KMP(char *ptr,char *str,int pp,int ss){
    int next[pp];
    next2(ptr,next,pp);
    int k=-1;
    int cnt=0;
    for(int p=0;p<ss;p++){
        while(k>-1&&str[p]!=ptr[k+1]){
            k=next[k];
        }
        if(str[p]==ptr[k+1]){
            k++;
            if(k==pp-1){
                cnt++;
            	k=next[k];
        	}
        }
    }
    return cnt;
}


 
```

#### 线段树

```
struct node{
    int m;
    int lf;
    int rt;
    int value;
}tree[4*MAXN];
void build(int L,int R,int v=1){
    tree[v].lf=L;
    tree[v].rt=R;
    tree[v].m=0;
    if(L==R){
        tree[v].value=1;
        return;
    }
    int mid=(L+R)/2;
    build(L,mid,v*2);
    build(mid+1,R,v*2+1);
    tree[v].value=tree[v*2].value+tree[v*2+1].value;
}
void pushdown(int v){
    if(tree[v].m){
        tree[v*2].m=tree[v].m;
        tree[v*2].value=(tree[v*2].rt-tree[v*2].lf+1)*tree[v*2].m;
        tree[v*2+1].m=tree[v].m;
        tree[v*2+1].value=(tree[v*2+1].rt-tree[v*2+1].lf+1)*tree[v*2+1].m;
        tree[v].m=0;
    }
}

void query(int L,int R,int v=1){
    if(L<=tree[v].lf&&R>=tree[v].rt){
        ans+=tree[v].v;
        return;
	}
	pushdown(v);
    if(L<=tree[v*2].rt){
        query(L,R,v*2);
    }
    if(R>=tree[val*2+1].lf){
        query(L,R,v*2+1);
    }
}
```

#### dijkstra

```
void dijkstra(int n){
    for(i=1;i<=n;i++){//记录low
        low[i]=mp[1][i];
        visit[i]=false;
    }
    for(i=1;i<=n;i++){//保证每个low都遍历一遍
        mn=-1;
        for(i2=1;i2<=n;i2++){//找最小量low遍历
            if(!visit[i2]&&low[i2]>mn){
                mn=low[i2];
                temp=i2;//记录遍历点
            }
        }
        visit[temp]=true;//已经遍历就记号
        for(i2=1;i2<=n;i2++){//1到i2的最小量和1到temp到i2最小量比较找最小值
            if(!visit[i2]&&low[i2]<min(low[temp],mp[temp][i2])){
                low[i2]=min(low[temp],mp[temp][i2]);
            }
        }
    }
}
```

#### 树状数组

```
int lowbit(int k){
    return k&-k;
}
void add(int k){
    while(k<=maxn){
        y[k]++;
        k+=lowbit(k);
    }
}
int sum(int k){
    int s=0;
    while(k>0){
        s+=y[k];
        k-=lowbit(k);
    }
    return s;
}
```

#### 素数打表

```
for(i=2;i<maxn;i++){
        if(isprime[i]){
            cnt[i]=t;
            prime[t]=i;
            t++;
        }
        for(i2=1;i2<=t&&i*prime[i2]< maxn;i2++){
            isprime[i*prime[i2]]=false;
            if(i%prime[i2]==0){
                break;
            }
        }
    }
```

#### 汉诺塔

```
void move(int n,char a,char b,char c)
{
    if(n==1)
        printf("\t%c->%c\n",a,c);   //当n只有1个的时候直接从a移动到c 
    else
    {   
        move(n-1,a,c,b);            //把a的n-1个盘子通过c移动到b 
        printf("\t%c->%c\n",a,c);   //把a的最后1个盘(最大的盘)移动到c 
        move(n-1,b,a,c);            //吧b上面的n-1个盘通过a移动到c 
    }   
}
```

#### 染色问题

```
void dfs(int x,int fa,int col){
   color[x]=col;//染色
   for(int i=0;i<a[x].size();i++){//遍历邻边
     int t=a[x][i];
     if(t==fa) continue;//与上一个点相同，不再遍历
     if(color[t]==-1) dfs(t,x,1-col);//未染色即染色
     else if(color[t]==col) flag=false;//连续两点同色
   }
}
```
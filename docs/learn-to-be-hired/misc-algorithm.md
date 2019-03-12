# Miscellaneous Algorithm

```
int Majority(list); // occ >= n/2.0
```

```
List {
	Record {
		string key;
		int time;
	}
	Record& getLatest();
	Record& getSmallest();
}
```


```
find k missing number in 1..n
1. k = 1
2. k = 2
```

seam carving

```
第三类：积分卡牌游戏
题目大意：有n张卡牌，每张上面有两个数字x,y，两人各自选择若干张卡牌，求他们各自选择的卡牌的x的总和相等时，所有选择的卡牌的y的总和的最大值。
题解：动态规划。
​d[j]​ 表示前i张牌，两人所选择的牌的差值为j时的最大值，转移方程为:
​d[j] = max(d[i-1][j], d[i-1][j-x] + y, d[i-1][j+x] + y)​

注意: 空间可能不够用，由于状态 ​d​ 只与状态 ​d[i-1]​ 有关，可以使用滚动数组来优化空间。
时间复杂度 ​O(n\*Σx)​

第四类：区间最大最小值
题目大意：给定两个序列a、b，求有多少个区间[l, r]满足，区间a[l, r]的最大值小于区间b[l, r]的最小值
题解：

解法1
通过观察发现，固定区间的左端点 ​l​，随着右端点 ​r​ 的右移，区间 ​a[l,r]​ 的最大值只会变大，区间 ​b[l,r]​ 的最小值只会变小，具有单调性
因此我们枚举区间的左端点 ​l​，使用二分查找的方法，找到 ​a[l,r]​ 的最大值小于 ​b[l,r]​ 的最小值的临界点 ​r​，此时答案增加计数 ​(r - l + 1)​
其中区间最值可以使用 ST 表查询，单次查询复杂度为常数
时间复杂度 ​O(n\*log(n))​

解法2
通过观察发现，对于两个满足条件的区间 ​[l, r]​和 ​[l', r']​，若 ​l'>=l​，那么 ​r'>=r​。
因此我们可以使用两个指针 ​l​ 和 ​r​，初始都在序列的第一个位置，并重复以下过程:
一直向右移动l，直到满足 ​a[l,r]​ 的最大值 ​< b[l,r]​ 的最小值或者 ​l>r​ 为止
答案增加计数 ​(r - l + 1)​
​r​ 向右移动一个单位
使用两个单调队列分别维护 ​a[l,r]​ 的最大值和 ​b[l,r]​ 的最小值。
时间复杂度 ​O(n)​

第五类：直播爱好者
题目大意：本题是区间调度问题，可以用贪心算法实现。
题解：

解法1
本题需要注意的是直播可能跨天，因此我们可以将原始输入复制一份，使其扩展到​2\*M​的时间区间内，再进行贪心算法求解
贪心算法基于优先选择结束时间最早的直播节目。因此我们可以将将输入数据按照结束时间排序
排序后，我们可以用两层循环，外层循环选定一个直播节目作为起点，内层循环用贪心算法获取这个节目结束到第二天该节目的开始时间之前，能够尽早结束的节目。时间复杂度为 ​O(n^2)​

解法2
解法1在确定第一个区间之后使用的贪心算法存在大量的重复计算，而对于某个区间 ​i​ 的下一个满足 ​t≤s[j]​ 的所有 ​j​ 中 ​t[j]​ 最小的那个区间是确定的，可以预处理并记录对应关系，此部分时间复杂度为 ​O(n\*logn)​
得到对应关系后我们可以使用倍增法，维护一个数组 ​next[j]​ 表示区间 ​i​ 在走 ​2^j​ 步后所到达的区间，再利用倍增函数对于每个区间只需要 ​O(logn)​ 的复杂度即可找到答案，因此整个算法的复杂度降为 ​O(n\*logn)​

解法3
本题还有一种除去排序外只需时间复杂度 ​O(n)​ 的解法。有兴趣的同学可以试着思考一下并在本文下讨论哦
```
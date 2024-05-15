# -外卖柜选址问题
给你一个楼栋数组buildings和一个整数k，其中buildings[]是第i栋房子在同一条街上的位置,k表示需要在这条街上建设的外卖柜个数。为了住户更好的拿外卖，请你规划外卖柜的位置，使得每栋房子与离他最近的外卖柜之间的距离之和最小，并返回这个最小距离和。
解答要求  
时间限制:C/C++1000ms,其他语言:2000ms内存限制:C/C++ 256MB,其他语言:512MB

输入  
第一行输入两个整数，第一个是楼栋的个数buildings.lengthe[1,100],第二个是要建设的外卖柜个数k∈[1,buildings.length]。  
接下来逐行输入楼栋的位置buildings[il∈[f1,10000]，楼栋位置buildings按升序排列。  
输出  
每栋房子与他最近外卖柜的距离之和的最小值。
输入：  
5 3  
1 4 8 10 20  
输出：  
5  
解释：有5栋房子，需要建设3个外卖柜，房子的位置分别处在[1,4,8,10,20]。  
可以将外卖柜放置在3，9，20处，距离之和为(3-1)+(4-3)+(9-8)+(10-9)+(20-20)=5 为所有可能的距离中最小。


思路：  
根据货仓选址问题，直接可以得出一个结论就是如果有n个仓库，我们建一个公司离这些仓库最近，那么公司位置在中点位置；  
dp[i][j]的含义是前i个楼栋，如果要放j个外卖柜的距离最小总和；   
  
那这个totalDistance是什么意思呢？    
  
在代码中，totalDistances 是一个二维数组，用于存储从第 i 个房屋到第 j 个房屋之间所有房屋到它们中点的最小距离总和。这个中点就是假设放置外卖柜的位置。

totalDistances[i][j] 的计算方式是这样的：对于每一对 i 和 j（其中 i < j），代码计算了 buildings[j] 和 buildings[(i + j) / 2] 之间的距离，并将这个距离加到 totalDistances[i][j - 1] 上。这样，totalDistances[i][j] 就代表了从第 i 个房屋到第 j 个房屋之间所有房屋到中点的最小距离总和。

这个预处理步骤是为了避免在动态规划的过程中重复计算这些距离，从而提高效率。在动态规划的过程中，dp[i][j] 表示前 i 个房屋放置了 j 个外卖柜时的最小总距离。在计算 dp[i][j] 时，我们可以直接使用 totalDistances 来获取任意两个外卖柜之间的房屋到外卖柜的最小距离总和，而不需要重新计算。

代码：  
## C++ 

```CPP 
#include <vector>
#include <iostream>
#include <algorithm>
#include <unordered_map>

using namespace std;

int num = 100;
int minTotalDistance(vector<int>& buildings, int k,int m) {
	int n = m;
	sort(buildings.begin(), buildings.end());

	// Precompute distances
	vector<vector<int>> totalDistances(n, vector<int>(n, 0));
	for (int i = 0; i < n; ++i) {
		for (int j = i + 1; j < n; ++j) {
			totalDistances[i][j] = totalDistances[i][j - 1] + (buildings[j] - buildings[(i + j) / 2]);
		}
	}

	// Dynamic programming
	vector<vector<int>> dp(n, vector<int>(k + 1, num));
	for (int i = 0; i < n; ++i) {
		dp[i][1] = totalDistances[0][i];
	}

	for (int j = 2; j <= k; ++j) {
		for (int i = j - 1; i < n; ++i) {
			for (int p = 0; p < i; ++p) {
				dp[i][j] = min(dp[i][j], dp[p][j - 1] + totalDistances[p + 1][i]);
			}
		}
	}

	return dp[n - 1][k];
}

int main() {
	int n, k;
	cin >> n >> k;
	vector<int> buildings(n);
	for (int i = 0; i < n; ++i) {
		cin >> buildings[i];
	}

	cout << minTotalDistance(buildings, k,n) << endl;
	return 0;
}
```

# LeetCode题解

## 动态或贪心

* **动态规划思想**

  通过拆分问题，定义问题状态和状态之间的关系，使得问题能够以递推（或者说分治）的方式去解决。

待完成……实战后再总结

------







### 121.买卖股票的最佳时机

```汉语
题目：给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。
如果你最多只允许完成一笔交易（即买入和卖出一支股票），设计一个算法来计算你所能获取的最大利润。
注意你不能在买入股票前卖出股票。

输入: [7,1,5,3,6,4]
输出: 5
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。

输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
```

**思路**

动态规划思想，另外，用min表示当天前的最小买入价格，max_profit表示最大收益（dp[i]的最大值）

* 使用`dp[i]`表示从第一天到当天成交一笔的最大收益，即局部最优解。//**其实只用一个变量滚动表示也行**
* 确定边界，即给一维数据自一个数赋值。显然`dp[0]`=0
* 状态转换`dp[i]=max(dp[i-1],当天价格-min)`，即比较当天卖出收益和前一天卖出的最大收益。（i>=1）
* 更新最小值min和最大收益max_profit

**code**

```c++
int maxProfit(vector<int>& prices) 
{
    //正规的动态规划
    if(prices.empty())
        return 0;
    vector<int> dp(prices.size());//第一天到当天最大的收益，存储局部最优解
    //状态转换 dp[i]=max(dp[i-1],prices[i]-min);
    dp[0]=0;//设置边界，初始化第一个数
    int max_profit=0;//要返回的结果
    int min=prices[0];//维护一个表示最低买入价格
    for(int i=1;i<prices.size();i++)
    {
        dp[i]=dp[i-1]>(prices[i]-min)?dp[i-1]:prices[i]-min;//状态转换，到当前的最大收益
        min=prices[i]>min?min:prices[i];//更新最小值
        max_profit=max_profit>dp[i]?max_profit:dp[i];//更新dp[i]中的最大值
    }
    return max_profit;
}
```



### 122.买卖股票的最佳时机 II

```汉语
题目：给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。
设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。
注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

输入: [7,1,5,3,6,4]
输出: 7
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。
     
输入: [1,2,3,4,5]
输出: 4
解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。
     因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。     
```

**思路**

贪心算法，只要当天有收益就卖出。

```c++
int maxProfit(vector<int>& prices)
{
    int max_profit=0;
    if(prices.empty())
        return max_profit;
    for(int i=1;i<prices.size();i++)
    {	
        //一旦有收益就可以卖出，这是一种典型的贪心思想，局部最优达到全局最优。
        if(prices[i]-prices[i-1]>0)
            max_profit+=prices[i]-prices[i-1];
    }
    return max_profit;
}
```



### 221.最大正方形

```汉语
在一个由 0 和 1 组成的二维矩阵内，找到只包含 1 的最大正方形，并返回其面积。

输入: 
1 0 1 0 0
1 0 1 1 1
1 1 1 1 1
1 0 0 1 0
输出: 4
```

**思路**

二维的动态规划

* 使用`dp[i][j]`表示点(i,j)作为右下角点时矩形的最大边长，表示局部解
* 确定边界，赋值第一行，本题就是输入数组第一行。
* 状态转换，从第二行开始
  * if当前点为0，则`dp[i][j]`=0
  * else `dp[i][j]=min(dp[i-1][j],dp[i][j-1],dp[i-1][j-1])+1`;
* 更新参数。即从局部最优解中找到全局最优解

**code**

```c++
int maximalSquare(vector<vector<char>>& matrix) 
{
    //动态规划
    if(matrix.empty())
        return 0;
    //创建一个二维数组dp[i][j],表示点(i,j)作为右下角点时矩形的最大边长
    //状态转换
    //if当前点为0，则dp[i][j]=0
    //else dp[i][j]=min(dp[i-1][j],dp[i][j-1],dp[i-1][j-1])+1;
    int maxRes=0;
    vector<vector<int>> dp(matrix.size(),vector<int>(matrix[0].size()));
    for(int i=0;i<matrix.size();i++)
    {
        for(int j=0;j<matrix[0].size();j++)
        {
            if(matrix[i][j]=='0')
                dp[i][j]=0;
            else
            {
                int minNum=0;
                if(i-1>=0&&j-1>=0)
                    minNum=min(min(dp[i-1][j],dp[i][j-1]),dp[i-1][j-1]);
                dp[i][j]=minNum+1;
            }
            maxRes=maxRes>dp[i][j]?maxRes:dp[i][j];//更新参数
        }
    }
    return maxRes*maxRes;
}
```



### 53.最大子序和

```汉语
题目：给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。
输入: [-2,1,-3,4,-1,2,1,-5,4],
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

**思路**
$$
f(i) = \begin{cases}  arr[i] & i=0 或 f(i-1)\leq0 \\f(i-1)+arr[i] & i>0并且f(i-1)>0\end{cases}
$$
动态规划，sum[i]数组也可以只用一个sum变量滚动表示

**code**

```c++
int maxSubArray(vector<int>& nums) {
    if(nums.size()==0)
        return 0;
    vector<int> sum(nums.size());//sum[i]表示到i的数组的最大和。
    sum[0]=nums[0];//确定边界
    int maxSum=nums[0];//要返回的结果
    for(int i=1;i<nums.size();i++)//从i=1开始
    {
        if(sum[i-1]<0)
            sum[i]=nums[i];
        else
            sum[i]=sum[i-1]+nums[i];//状态转换
        maxSum=maxSum>sum[i]?maxSum:sum[i];//更新参数
    }
    return maxSum;
}

//只用一个变量  另外的写法
int GetMaxOfSubArray(int* arr, int len)
{
	assert(len>0);
	assert(arr);

	int result = INT_MIN;//不能设置为零。有可能初始数据全是负数
	int sum = 0;//sum=arr[0]，下面i就从1开始
	for (int i =0 ; i < len; i++)
	{
		if (sum <= 0)
			sum = arr[i];
		else
			sum = sum+arr[i];//f(i)=f(i-1)+arr[i]//不用管这个arr[i]是正还是负数
		if (sum > result)//保存其中的最大值//result就是上一次的sum，现在比较取大的就行了。
			result = sum;
	}
	return result;
}
```


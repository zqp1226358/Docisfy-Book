# 动态规划
	
[TOC]

## 八皇后问题
**题目要求：**
在一个nxn的棋盘上放置n个棋子，使得每行每列和每条对角线上都只有一个棋子，求其摆放的方法数。
给定一个int n，请返回方法数，保证n小于等于15

**思路分析：**
利用一维数组来存储数据，以索引代表行数，以对应的value值代表列数，大大节省空间
利用dfs递归回溯，边判断边累计合适方案数
判断的要求是当前皇后是否与之前的皇后在同一行，同一列，同一对角线，如果是则此方案不通，继续换另一种方案。

**代码实现：**
```cpp
class Queens {
public:
    int nQueens(int n) {
        int count=0;//记录方案数
        int *a=new int[n+1];
        dfs(a,1,n,count);
        return count;
    }
    int place(int *a,int i){//判断此位置放皇后是否合适，即与前面几个皇后是否相撞
		for(int k=1;k<i;k++){//k表示行，遍历的范围是放这个i皇后之前的所有行
			if((a[i]==a[k])||(a[i]-a[k]==(i-k))||(a[i]-a[k]==k-i)){
				return 0;
				//剪枝，即判断是否符合条件来放,i表示皇后所在的行数，a[i]表示所在的列数，
	            //所以后面条件用来判断两个皇后是否在对角线上,前面用来判断是否在同一列上。
	            //行数不需要判断，因为他们本身的i就代表的是行数
			}
		}
		return 1;//说明此位置可以放第i个皇后，返回1
	} 
    void dfs(int a[],int i,int n,int &count){
		if(i>n){//递归截止条件：皇后数i大于所给的n
			count++;//记录下一种方案加1
			return ;
		}
		for(int k=1;k<=n;k++){
			a[i]=k;//第i个皇后放的列数
			if(place(a,i)){
				dfs(a,i+1,n,count);
			}
		}
	}
};
```
**运行结果：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200413214244426.png)


## 152. 乘积最大子数组

给你一个整数数组 nums ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。


示例 1:

>输入: [2,3,-2,4]

>输出: 6

解释: 子数组 [2,3] 有最大乘积 6。

示例 2:

>输入: [-2,0,-1]

>输出: 0

解释: 结果不能为 2, 因为 [-2,-1] 不是子数组。

思路分析：
动态规划
遍历数组时计算当前最大值，不断更新
由于存在负数，那么会导致最大的变最小的，最小的变最大的。因此还需要维护当前最小值imin
还要注意存在0的情况

```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        //maxi表示以当前节点为终结节点的最大连续子序列乘积 
		//mini表示以当前节点为终结节点的最小连续子序列乘积
        int res=INT_MIN, maxi=1,mini=1;
        for(int i=0;i<nums.size();i++){
            if(nums[i] > 0){
                maxi = max(maxi*nums[i],nums[i]);
                mini = min(mini*nums[i],nums[i]);
            }
            else if(nums[i] == 0){
                maxi=1,mini=1;
                res = max(res,0);
                continue;
            }
            else if(nums[i] < 0){
                int t=maxi;
                maxi = max(mini*nums[i],nums[i]);
                mini = min(t*nums[i],nums[i]);
            }
            res = max(res,maxi);
        }
        return res;
    }
};

```


## 300. 最长上升子序列

给定一个无序的整数数组，找到其中最长上升子序列的长度。

示例:

>输入: [10,9,2,5,3,7,101,18]

>输出: 4 

解释: 最长的上升子序列是 [2,3,7,101]，它的长度是 4。
说明:

可能会有多种最长上升子序列的组合，你只需要输出对应的长度即可。
你算法的时间复杂度应该为 O(n2) 。
进阶: 你能将算法的时间复杂度降低到 O(n log n) 吗?

```
int lengthOfLIS(vector<int>& nums) {
        int f[nums.size()+1];
        for(int i=0;i<nums.size();i++){
            f[i] = 1;
        }
        for(int i=0;i<nums.size();i++){
            for(int j=0;j<i;j++){
                if(nums[j]<nums[i]){
                    f[i] = max(f[i],f[j]+1);//i前面j个中最大值
                }
            }
        }
        int result = 0;
        for(int i=0;i<nums.size();i++){//动态规划表中的最大值
            result = max(result,f[i]);
        }
        return result;
    }
```


## 1143. 最长公共子序列
给定两个字符串 text1 和 text2，返回这两个字符串的最长公共子序列的长度。

一个字符串的 子序列 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。
例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。两个字符串的「公共子序列」是这两个字符串所共同拥有的子序列。

若这两个字符串没有公共子序列，则返回 0。

 
示例 1:

>输入：text1 = "abcde", text2 = "ace" 

>输出：3  
解释：最长公共子序列是 "ace"，它的长度为 3。

思路分析：

- 第一种情况 s1[i] == s2[j]
- 第二种情况 两个字符至少有一个不在lcs中

```
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        vector<vector<int> > res;
        int m=text1.length(),n=text2.length();
        for(int i=0;i<=m;i++){
            vector<int> tmp;
            for(int j=0;j<=n;j++){
                tmp.push_back(0);
            }
            res.push_back(tmp);
        }

        for(int i=1;i<=m;i++){
            for(int j=1;j<=n;j++){
                if(text1[i-1] == text2[j-1]){
                    res[i][j] = res[i-1][j-1]+1;
                }
                else{
                    res[i][j] = max(res[i-1][j],res[i][j-1]);//还可以加res[i-1][j-1]但好像没有必要
                }
            }
        }

        return res[m][n];
    }
};
```
## 26. Remove Duplicates from Sorted Array
- Difficulty: Easy
- Given a sorted array, remove the duplicates in place 
such that each element appear only once and return the new length.

- Do not allocate extra space for another array, you must do this in place 
with constant memory.

For example,
Given input array nums = [1,1,2],

Your function should return length = 2, with the first two elements of nums 
being 1 and 2 respectively. It doesn't matter what you leave beyond the new length.
题意：返回不重复元素个数，并更新为不重复数组
____________________________________________________________________________________

###代码一：
- 时间复杂度O(n),空间复杂度O(1).
####完整代码：
```
#include<iostream>
#include<vector>
using namespace std;
//代码一：
//时间复杂度O(n),空间复杂度O(1).
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
		int count=0;
      for(int p1=0; p1!=nums.size(); p1++)
		{
			if(p1==0) count=1; //当前元素的所指
			if( (p1+1)!=nums.size() && nums[p1]!=nums[p1+1])
				count++;	 //每突变一次，计数加一
		}
		return count;   //未更新数组的前n个数
    }
};

int main()
{
	vector<int> test1;
	test1.push_back(1);
	test1.push_back(2);
	test1.push_back(3);
	test1.push_back(4);
	test1.push_back(2);
	test1.push_back(3);
	//Solution s1;
	//cout<<s1.removeDuplicates(test1)<<endl;
	vector<int>::iterator iter=test1.begin();  
	cout<<*iter<<endl;
	cout<<*iter++<<endl;
	cout<<*iter<<endl;

}
```
- 测试用例：
   - 1. [1]  ; 1  done
   -  2. [1,1,1]  ; 1 done
   -  3. [1,1,2,2] ; 2
   
_____________________________________________

###代码二：
- 时间复杂度O(n),空间复杂度O(1).
```
#include<iostream>
#include<vector>

using namespace std;

class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
		if(nums.empty()) return 0; //排除0次,剩下的最少有1次

		int index=0; //重复值的首个索引
        for(int p=1; p!=nums.size(); p++)
		{
			if(nums[index]!=nums[p]) 
				nums[++index]=nums[p]; //将不重复的数字向前复制
		}
		return index+1;
    }
}; 
```
_____________________________________________
###代码三： 使用STL
- 时间复杂度O(n),空间复杂度O(1).
```
#include<iostream>
#include<vector>
#include<algorithm>  //unique()函数要用

using namespace std;

class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
		return distance( nums.begin(), unique( nums.begin(),nums.end() ) );
    }
};
```
####补充
- 在STL中unique函数是一个去重函数,需要头文件:#include<algorithm>.
- unique的功能是去除相邻的重复元素(只保留一个),
其实它并不真正把重复的元素删除，是把重复的元素移到后面去了，然后依然保存到了原数组中，
然后 返回去重后 数组后面一个元素 的地址，因为unique去除的是相邻的重复元素，
所以会自动进行一下排序,因此为排过序的前n个不重复元素+重复元素。
如果要擦除后面的重复元素：nums.erase(unique(), nums.end());
补充： http://www.cnblogs.com/heyonggang/archive/2013/08/07/3243477.html

________________________________________________________________________________________
##代码四： 使用STL,但自己实现去重函数。 模板编程
- 时间复杂度O(n),空间复杂度O(1).
```
#include<iostream>
#include<vector>
#include<algorithm>  //unique()函数\upper_bound要用
#include<iterator>
using namespace std;

class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
		return distance( nums.begin(), removeDuplicates( nums.begin(),nums.end(),nums.begin() ) );
		//return 0;
	}
	template<typename InIt, typename OutIt>
	OutIt removeDuplicates(InIt first, InIt last, OutIt output)  
	//此处使用模板类编程需要加头文件#include<iterator>,否则要用下面方式
	//vector<int>::iterator removeDuplicates(vector<int>::iterator first, vector<int>::iterator last, vector<int>::iterator output)
	{
		while(first!=last)
		{
			*output++=*first; //此处先驱*，再++
			first=upper_bound(first,last,*first);
		}

		return output;
	}
	
};
```
####补充：
- 1. ++和*的优先级：后++优先级高，*和前++优先级相同。 *output++,先++,再取*
- 2.ForwardIter upper_bound(ForwardIter first, ForwardIter last, const _Tp& val)算法返回一个非递减序列[first, last)中的第一个大于值val的位置。
```
STL中关于二分查找的函数有三个lower_bound 、upper_bound 、binary_search 。这三个函数都运用于有序区间（当然这也是运用二分查找的前提），下面记录一下这两个函数。
ForwardIter lower_bound(ForwardIter first, ForwardIter last,const _Tp& val)算法返回一个非递减序列[first, last)中的第一个大于等于值val的位置。

ForwardIter upper_bound(ForwardIter first, ForwardIter last, const _Tp& val)算法返回一个非递减序列[first, last)中的第一个大于值val的位置。

lower_bound和upper_bound如下图所示：


1.lower_bound函数源代码：

```
//这个算法中，first是最终要返回的位置
int lower_bound(int *array, int size, int key)
{
  int first = 0, middle;
  int half, len;
  len = size;

  while(len > 0)
  {
    half = len >> 1;
    middle = first + half;
    if(array[middle] < key) 
    {    
      first = middle + 1;         
      len = len-half-1;       //在右边子序列中查找
    }
    else
      len = half;            //在左边子序列（包含middle）中查找
  }
return first;
}
```

2.upper_bound函数源代码：
```
int upper_bound(int *array, int size, int key)
{
  int len = size-1;
  int half, middle;
  while(len > 0)
  {
    half = len >> 1;
    middle = first + half;
    if(array[middle] > key)     //中位数大于key,在包含last的左半边序列中查找。
    len = half;
    else
    {
      first = middle + 1;    //中位数小于等于key,在右半边序列中查找。
      len = len - half - 1;
    }
  }
return first;
}
```

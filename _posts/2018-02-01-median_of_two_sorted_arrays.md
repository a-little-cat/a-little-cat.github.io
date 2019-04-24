---
tag: [leetcode,hard]
description: 变种二分  边界处理
---
变种二分  边界处理
# Description
>There are two sorted arrays nums1 and nums2 of size m and n respectively.
>
>Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).
<!--more-->
# Example
```c++
nums1 = [1, 3]
nums2 = [2]

The median is 2.0
```
# 分析
把两个数组分成左右两部分,所有左边的元素不大于右边的元素,两边数量相等.

使用二分在较小的数组滑动.

难点在于边界处理.
# Code
第一版来自leetbook
```c++
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        if(nums1.size() == 0)
            return MedofArray(nums2);
        if(nums2.size() == 0)
            return MedofArray(nums1);
        int n = nums1.size();
        int m = nums2.size();
        if(n > m)   //保证数组1一定最短
            return findMedianSortedArrays(nums2,nums1);
        int L1,L2,R1,R2,c1,c2,lo = 0, hi = 2*n;  //我们目前是虚拟加了'#'所以数组1是2*n+1长度
        while(lo <= hi)   //二分
        {
            c1 = (lo+hi)/2;  //c1是二分的结果
            c2 = m+n- c1;
            L1 = (c1 == 0)?INT_MIN:nums1[(c1-1)/2];   //map to original element
            R1 = (c1 == 2*n)?INT_MAX:nums1[c1/2];
            L2 = (c2 == 0)?INT_MIN:nums2[(c2-1)/2];
            R2 = (c2 == 2*m)?INT_MAX:nums2[c2/2];

            if(L1 > R2)
                hi = c1-1;
            else if(L2 > R1)
                lo = c1+1;
            else
                break;
        }
        return (max(L1,L2)+ min(R1,R2))/2.0;
    }
    double MedofArray(vector<int>& nums)
    {
        if(nums.size() == 0)    return -1;
        return (nums[nums.size()/2]+nums[(nums.size()-1)/2])/2.0;
    }
};
```
第二版来自leetcode
```c++
static const auto _____ = []()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return nullptr;
}();
class Solution {
public:
	double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
		int n = nums1.size(), m = nums2.size();
		if (n > m) {
			swap(n, m);
			swap(nums1, nums2);
		}
		int l, r, mid, pos, id = (n + m + 1) / 2;
		l = 0, r = n;
		while (l <= r)
		{
			mid = (l + r) >> 1;
			pos = id - mid;
			if (mid < n && nums2[pos - 1] > nums1[mid]) l = mid + 1;
			else if (mid >= 1 && nums2[pos] < nums1[mid - 1]) r = mid - 1;
			else break;
		}
		double ans;
		if (mid == 0) ans = nums2[pos - 1];
		else if (pos == 0) ans = nums1[mid - 1];
		else ans = max(nums1[mid - 1], nums2[pos - 1]);
		if ((n + m) & 1) return ans;
		else
		{
			if (mid == n) ans = (ans + nums2[pos]) / 2.0;
			else if (pos == m) ans = (ans + nums1[mid]) / 2.0;
			else ans = (ans + min(nums1[mid], nums2[pos])) / 2.0;
		}
		return ans;
	}
};
```
---
title: weeklyCoding-getIndicesByTarget
date: 2018-04-19 23:54
categories: blog
tags: [algorithm]
---

> 每周一练-计算目标索引。假设给定数组中的两个元素和等于给定值计算这个索引。这道题坑最多等就是C++的了：数组参数传值拷贝，数组长度，返回数组总之是后背发凉。

## C++
```cpp
// getIndicesByTarget.cpp @ 2018-04-14
#include <stdlib.h>
#include <stdio.h>
class Solution{
    public:
    void getIndicesByTarget(int* num,int len,int tar,int (&indices)[2]){
        int tmp = -1;
        for(int i = 0; i < len; i++){
            if (tmp < 0 && num[i] < tar){
                indices[0] = i;
                tmp = tar - num[i];
            }else if(tmp == num[i]){
                indices[1] = i;
                return;
            }
        }
    }
};

int main(){
    int num[5] = {2,10,7,5,8};
    int len = sizeof(num)/sizeof(num[0]);
    int tar = 7;
    int indices[2] = {-1,-1};
    Solution *sol = new Solution();
    sol->getIndicesByTarget(num, len, tar, indices);
    if (indices[1] > 0){
        printf("get indices[%d,%d] of number array.\n",indices[0],indices[1]);
    }else{
        printf("sorry world!");
    }
    return 0;
}

```

## Python
```python
# getIndicesByTarget.py @ 2018-4-19 23:27
class Solution:
    def getIndicesByTarget(self,nums,tar):
        diff = -1
        indices = []
        for i,v in enumerate(nums):
            if v < tar:
                indices.append(i)
                tar = tar - v 
            elif v == tar:
                indices.append(i)
                return indices

if __name__ == '__main__':
    mySolution = Solution()
    nums = [2,10,7,5,8]
    tar = 7
    indices = mySolution.getIndicesByTarget(nums,tar)
    if len(indices) == 2:
        print(indices)
    else:
        print('sory world!')
```

> TODO:Golang
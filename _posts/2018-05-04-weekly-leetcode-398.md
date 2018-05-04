---
title: weeklyCoding-LeetCode-398-randomPickIndex
date: 2018-05-04 21:00
categories: blog
tags: [algorithm,leetcode]
---

> Given an array of integers with possible duplicates, randomly output the index of a given target number. 
You can assume that the given target number must exist in the array.
Note:
The array size can be very large. Solution that uses too much extra space will not pass the judge.
Example:
int[] nums = new int[] {1,2,3,3,3};
Solution solution = new Solution(nums);
// pick(3) should return either index 2, 3, or 4 randomly. Each index should have equal probability of returning.
solution.pick(3);
// pick(1) should return 0. Since in the array only nums[0] is equal to 1.
solution.pick(1);

[leetcode for 398. Random Pick Index](https://leetcode.com/problems/random-pick-index/description/)

## C++
```cpp
class Solution {
private:
    vector<int> nums;
    std::random_device rd;
public:
    Solution(vector<int> nums) {
        this->nums = nums;
    }
    
    int pick(int target) {
        auto p = begin(nums);
        auto e = end(nums);
        int i = -1;
        int c = 0;
        std::mt19937 gen(rd());
        while (p != e){
            if (*p == target){
                c++;
                std::uniform_int_distribution<> dis(1, c);
                if (dis(gen) == 0){
                    i = p - begin(nums);
                    break;
                }
            } 
        }
        return i;
    }
};

int main(){
    vector<int> nums = {1, 2, 3, 3, 3};
    Solution *sol = new Solution(nums);
    sol->pick(1);
    
    return 0;
}
```

## Python
```python
import random

class Solution0(object):
    singles = {}
    randoms = {}
    def __init__(self, nums):
        """

        :type nums: List[int]
        :type numsSize: int
        """
        for idx, tar in enumerate(nums):
            if tar in self.singles.keys():
                self.randoms[tar] = []
                self.randoms[tar].append(self.singles[tar])
                del self.singles[tar]
                print 'rmv %s to randoms idx=%s and singles=%s' % (tar, self.randoms[tar], self.singles)
                self.randoms[tar].append(idx)
                print 'put %s in randoms idx=%s' % (tar, self.randoms[tar])
            elif tar in self.randoms.keys():
                self.randoms[tar].append(idx)
                print 'put %s in randoms idx=%s' % (tar, self.randoms[tar])
            else:
                print 'put %s in singles idx=%s' % (tar, idx)
                self.singles[tar] = idx;

    def pick(self, target):
        """
        :type target: int
        :rtype: int
        """
        if target in self.singles.keys():
            return self.singles[target]
        idx = self.randoms[target][0]
        self.randoms[target].append(self.randoms[target].pop(0))
        return idx


class Solution(object):
    nums = []

    def __init__(self, nums):
        self.nums = nums

    def pick(self, target):
        cnt = 0
        res = -1
        for i, t in enumerate(self.nums):
            if t == target:
                cnt += 1
                if random.randrange(cnt) == 0:
                    res = i
        return res

if __name__ == '__main__':
    nums = [1, 2, 3, 3, 3]
    obj = Solution(nums)
    p = obj.pick(2)
    print p
    for i in range(5):
        print i, obj.pick(3)
```

## Golang
```go
package main

import (
	"fmt"
	"math/rand"
)
type Solution struct{
	nums []int
}

func Constructor(nums []int) Solution{
	sol := Solution{nums:nums}
	return sol
}

func (this *Solution) pickIndex(target int) int{
	lng := len(this.nums)
	idx := -1
	cnt := 0
	for i:=0; i<lng; i++{
		if this.nums[i] == target{
			cnt += 1
			if rand.Intn(cnt) == 0{
				idx = i
			}
		}
	}
	return idx
}

func main() {
	nums :=[6]int{1, 2, 3, 3, 3, 3}
	obj := Constructor(nums[:])
	for i:=0; i < 5; i++ {
		param_1 := obj.pickIndex(3)
		fmt.Println("pick(3) index =", param_1)
	}
}
```
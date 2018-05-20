---
title: weeklyCoding-Leetcode 3.LongestSubstring
date: 2018-05-11 23:00
categories: blog
tags: [algorithm,leetcode]
---

> Leetcode 3. Longest Substring Without Repeating Characters
DescriptionHintsSubmissionsDiscussSolution
Given a string, find the length of the longest substring without repeating characters.

Examples:
Given "abcabcbb", the answer is "abc", which the length is 3.
Given "bbbbb", the answer is "b", with the length of 1.
Given "pwwkew", the answer is "wke", with the length of 3. Note that the answer must be a substring, "pwke" is a subsequence and not a substring.

### [C++](https://github.com/bblu/algocpp/tree/master/weeklyPractice)
```cpp
#include <stdio.h>
#include <string>
#include <vector>

using namespace std;

class Solution {
    public:
    int lengthOfLongestSubstring(std::string s){
        //记录每个字符之前最大的起始位置(反向索引)
        std::vector<int> preCharPos(255,-1);
        int strLen = (int)s.length();
        if (strLen < 2) return strLen;
        int maxLen = 1;
        int curPos = -1;
        int prePos = 0;
        for(int i = 0; i<strLen; i++){
            prePos = preCharPos[s[i]];
            curPos = std::max(curPos,prePos);
            int curLen = i - curPos;
            preCharPos[s[i]] = i;
            maxLen = std::max(maxLen, curLen);
        }
        return maxLen;
    };

    int lengthOfLongestSubstring_old(std::string s) {
        this->s = s;
        int strlen =(int)s.length();
        if (strlen < 2) return strlen;
        int start = 0;
        int sublen = 1;
        int longest = 1;
        for(int pos=1; pos < strlen; pos++){
            if(hasCurrentChar(start,pos)){
                printf( "has s:%i, p:%i, l:%i\n",start, pos, sublen);
                start++;
                pos = start;
                if(sublen > longest) longest = sublen;
                //tempArray.push_back(sublen);
                sublen = 1;
            }else{
                sublen++;
                printf( "else sublen++=%i\n",sublen);
            }
        }
        if(sublen > longest) longest = sublen;
        return longest;
        //sort(tempArray.begin(), tempArray.end());
        //return tempArray.back();
    }
    private:
    std::string s;
    bool hasCurrentChar(int start, int pos){
        char p = s[pos];
        for(int i=start; i < pos; i++){
            if(p == s[i]) {
                printf( "cmp start:%i, pos[%i]=%c\n",start, pos, p);
                return true;
            }
        }
        return false;
    }
    
};

int main(){
    Solution *sol = new Solution();
    string str = "cdd";
    int longest = sol->lengthOfLongestSubstring(str);
    printf("longestsubstring len=%i\n", longest);
}
```

### [python](https://github.com/bblu/algopython/tree/master/weeklyPractice)
```python
class Solution(object):
    def lengthOfLongestSubstring(self, s):
        """
        :type s: str
        :rtype: int
        """
        maxLen, curPos, charPos= 0, 0, {}
        for idx, char in enumerate(s, 1):
            if charPos.get(char, -1) >= curPos:
                curPos = charPos[char]
            charPos[char] = idx
            maxLen = max(maxLen, idx - curPos)
        return maxLen


if __name__ == "__main__":
    sol = Solution()
    s = 'aabcdd'
    l = sol.lengthOfLongestSubstring(s)
    print l

```

### [go](https://github.com/bblu/golang/tree/master/weeklypractice)
```go
package main

type Solution struct {
}

func Constructor() Solution {
	sol := Solution{}
	return sol
}
func (this *Solution) max(x, y int32) int32 {
	if x > y {
		return x
	}
	return y
}
func (this *Solution) lengthOfLongestSubString(str string) int32 {
	maxLen, curPos := int32(0), int32(0)
	charPos := make(map[byte]int32, 256)
	var i int32
	for i = 0; i < 256; i++ {
		charPos[byte(i)] = -1
	}
	strLen := int32(len(str))
	for i = 0; i < strLen; i++ {
		prePos := charPos[str[i]]
		if prePos >= curPos {
			curPos = charPos[str[i]]
		}
		charPos[str[i]] = i
		maxLen = this.max(maxLen, i-curPos)
	}
	return maxLen
}

func main() {
	sol := Constructor()
	s := "aabcdd"
	len := sol.lengthOfLongestSubString(s)
	println(len)
}
```
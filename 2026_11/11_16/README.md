# 11/16

## 면접 복기

## 잔디 심기

[Longest Substring Without Repeating Characters - NeetCode](https://neetcode.io/problems/longest-substring-without-duplicates/question)

```cpp
class Solution {

public:
    int lengthOfLongestSubstring(string s) {
        int cnt[129]={};
        int sSize=s.length();
        int answer=0;
        int left=0;
        int right=0;

        for(int i=0;i<sSize;i++)
        {
            cnt[s[i]]++;

            while(cnt[s[right]]>1)
            //만일 현재 문자가 중복 되었다면 중복이 없어질 때까지 왼쪽
            //포인터 이동시켜서 중복되지 않은 문자열의 윈도우 크기가
            //되도록 left포인터를 계속 이동 시켜줌
            {
                cnt[s[left]]--;
                left++;
            }

            answer=max(answer,right-left+1);
            right++;
        }

        return answer;
    }
};
```

## 넷마블 1번 4번 항목 작성

## 으밤공 페이즈 문서 정리 + 시스템 작성

# 10/19

## 잔디 심기

[Minimum Window Substring - NeetCode](https://neetcode.io/problems/minimum-window-with-characters/question)

```cpp
class Solution {
public:
    string minWindow(string s, string t) {
        int sSize=s.size();
        int tSize=t.size();
        int aCnt[128]={};
        //현재 윈도우 내부의 문자 개수
        int tCnt[128]={};
        //t 문자열의 개수 
        int left=0;
        int right=0;
        string answer;
        int matched=0;
        int answerl=-1;
        int answerr=0;
        for(int i=0;i<tSize;i++)
        {
            tCnt[t[i]]++;
        }
        while(right<sSize)
        {
            aCnt[s[right]]++;

            if(aCnt[s[right]]<=tCnt[s[right]])
            {
                matched++;//조건에 맞는 문자열 충족하면 matched증가
            }
            while(matched==tSize)
            //일단 t의 모든 문자를 필요한 개수만큼 포함한 상태이고
            //조건을 유지하면서 최소 부분 문자열 찾는 과정
            {
                //처음 발견하거나
                //기존 정답보다 현재 윈도우가 더 짧다면 정답 인덱스 갱신
                if(answerl==-1||right-left<answerr-answerl)
                {
                    answerl=left;
                    answerr=right;

                }   
                aCnt[s[left]]--;
                //윈도우 가장 왼쪽 문자 제거

                //문자를 제거한 결과 t에서 필요한 개수보다 부족해졌다면
                //더이상 모든 조건을 충족 못하므로 matched 감소 
                if(aCnt[s[left]] < tCnt[s[left]])
                {
                    matched--;
                }

                left++;
            }
            right++;
        }    
        if(answerl==-1) return "";
        //answerl이 갱신 되지 않으면
        return s.substr(answerl, answerr-answerl+1);
        // 기존 문제 풀이 방식은 substr을 계속 조건에 맞을 때 마다 갱신했었는데
        //이때문에 시간초과 남 따라서 인덱스를 저장 시켜놓고 마지막에 정답 갱신
    }
};
```

## 자소서 다듬기

## VR 플젝

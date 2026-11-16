# 11/09

## 잔디 심기

[Find All Anagrams in a String - NeetCode](https://neetcode.io/problems/find-all-anagrams-in-a-string/question)

```cpp
class Solution {
    int cnt[26] = {};
    int cur[26] = {};

public:
    vector<int> findAnagrams(string s, string p) {
        int pSize = p.size();
        int sSize = s.size();
        vector<int> answer;

        if (sSize < pSize)
        {
            return answer;
        }//s가p보다 길이가 짧으면 애너그램 x

        for (int i = 0; i < pSize; i++)
        {
            cur[p[i] - 'a']++;
        }
        //맨처음 p의 크기만큼 알파갯 개수 저장

        for (int i = 0; i < sSize; i++)
        {
            //현재 문자 알파뱃 개수 ++
            cnt[s[i] - 'a']++;

            //윈도우 크기가 pSize를 초과하면
            //가장 왼쪽에 있는 문자를 제거
            if (i >= pSize)
            {
                cnt[s[i - pSize] - 'a']--;
            }
            //윈도우 크기가 pSize되면 비교 시작
            if (i >= pSize - 1)
            {
                bool isSame = true;
                //현재 윈도우와 p의 알파뱃 별 등장횟수 비교
                for (int j = 0; j < 26; j++)
                {
                    if (cnt[j] != cur[j])
                    {
                        isSame = false;
                        break;
                    }
                }

                if (isSame)
                {
                    answer.push_back(i - pSize + 1);
                }
            }
        }

        return answer;
    }
};
```

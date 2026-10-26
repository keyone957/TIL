# 10/15

## 잔디 심기

[Number of Visible People in a Queue - NeetCode](https://neetcode.io/problems/number-of-visible-people-in-a-queue/question)

```cpp
class Solution {
public:
    vector<int> canSeePersonsCount(vector<int>& heights) {
        int aSize=heights.size();

        // 단조 감소 스택
        // 사람의 키가 아닌 인덱스를 저장
        // 스택에는 오른쪽에서 자신보다 크거나 같은 사람을
        // 아직 만나지 못한 사람들의 인덱스가 저장됨
        stack<int> st;

        // answer[i] = i번째 사람이 오른쪽에서 볼 수 있는 사람 수
        vector<int> answer(aSize,0);

        /*
        왼쪽에서 오른쪽으로 탐색하는 단조 감소 스택

        현재 사람 i가 등장했을 때,
        스택에 있는 이전 사람들이 현재 사람을 볼 수 있는지 판단한다.

        1. 스택 top의 키 < 현재 사람의 키
           -> top에 있는 사람은 현재 사람을 볼 수 있으므로 answer 증가
           -> 현재 사람보다 작기 때문에 이후 사람들을 볼 수 없음
           -> 따라서 pop

        2. 스택 top의 키 >= 현재 사람의 키
           -> top에 있는 사람은 현재 사람을 볼 수 있음
           -> 하지만 이후 사람을 볼 가능성도 있으므로 pop하지 않음

        3. 현재 사람의 인덱스를 스택에 push
           -> 이후 등장하는 사람들과 비교하기 위해 저장
        */

        for(int i=0;i<aSize;i++)
        {
            while (!st.empty())
            {
                if (heights[st.top()] < heights[i])
                {
                    // 이전 사람은 현재 사람을 볼 수 있음
                    answer[st.top()]++;

                    // 현재 사람이 더 크므로
                    // 이전 사람은 이후 등장하는 사람을 볼 수 없음
                    st.pop();
                }
                else
                {
                    break;
                }
            }

            // 스택에 사람이 남아 있다면
            // top에 있는 사람도 현재 사람을 볼 수 있음
            if (!st.empty())
            {
                answer[st.top()]++;
            }

            // 현재 사람을 스택에 삽입
            st.push(i);
        }

        return answer;
    }
};
```

## vr 프로젝트

# 10/21

## 잔디 심기

[Online Stock Span - NeetCode](https://neetcode.io/problems/online-stock-span/question)

```cpp
class StockSpanner {
stack<pair<int,int>>st;
//주식 가격, 날짜
int curDay=0;
//현재 날짜
public:
    StockSpanner() {

    }

    int next(int price) {
        int answer=1;
        curDay++;
        if(curDay==1)
        {
            st.push({price,curDay}); 
            return 1;
        }//첫번째 날일때 
        while(!st.empty())
        {
            if(st.top().first<=price)
            {
                st.pop();
                if(!st.empty())
                {
                //스택이 비어있지 않으면 
                //현재 가격보다 높은 가장 가까운 이전 날짜가 존재함
                //따라서 스택의 맨 위에 있는 값의 날짜를 가져와서 계산
                    answer=curDay-st.top().second;
                }                
                else
                {//현재 가격보다 높은 이전 날짜가 없다.
                    //첫날부터 오늘까지 모두 포함
                    answer=curDay;
                }
            }
            else break;
        }
        st.push({price,curDay});

        return answer;
    }
};
```

## 자소서 다듬기

## CS 공부 및 문제 풀기

# 10/12

## 잔디 심기

[Next Greater Element I - NeetCode](https://neetcode.io/problems/next-greater-element-i/question)

## 단조 스택 블로그 정리

[[알고리즘] 단조 스택 (Monotonic Stack)](https://keyone957.tistory.com/53)

## CS 정리

### static 한정자

> 변수나 함수 클래스에 정적 속성을 부여하는 것으로 클래스로부터 객체를 생성하지 않고 변수나 함수를 호출할 수 있도록 해주는 키워드

⇒ 객체를 생성하지 않아도 사용할 수 있고 모든 인스턴스가 공유하는 멤버를 만들 때 유용함

⇒ static 변수 (정적 변수)는 데이터 영역에 저장됨. 즉 프로그램이 처음 구동될 때부터 메모리에 할당됨.

#### 예시 코드

```csharp
class Monster {
    public static int count = 0;  // 클래스에 속함 (모든 인스턴스가 공유)
    public int hp;                // 인스턴스에 속함 (객체마다 별도)

    public Monster() { count++; }
}

Monster a = new Monster();
Monster b = new Monster();
Debug.Log(Monster.count);  // 2 (a, b가 같은 count를 공유)
```

#### 특징

- static 멤버는 프로그램이 끝날 때까지 유지되므로 static 필드가 객체를 참조하고 있으면 그 객체는 GC로 회수되지 않음
- static 컬렉션에 계속 추가만 하고 제거하지 않으면 메모리 누수로 이어짐.
- 전역 상태라 어디서든 수정 가능해 결합도가 높아지고 버그 추적과 테스트가 어려움
- 멀티스레드에서 공유 상태이므로 동기화가 필요함

---

### 람다식 / 클로저 / 캡쳐

#### 람다식

> 이름 없는 익명 함수를 간결하게 표현하는 문법

EX)

```csharp
// 일반 메서드
int Add(int a, int b) { return a + b; }

// 람다식
Func<int, int, int> add = (a, b) => a + b;
Debug.Log(add(3, 4));  // 7
```

#### 캡쳐

> 람다식 바깥에서 선언된 지역변수를 사용 하는 것

EX)

```csharp
int bonus = 10;// 람다 밖의 변수

Func<int, int> addBonus = x => x + bonus;
// 람다 안에서 bonus를 사용 = 캡처
```

⇒ 값 자체를 복사하는 것이 아닌 같은 변수를 계속 참조 하는 것임.

EX)

```csharp
int number = 10;

Action action = () =>
{
    Debug.Log(number);
}
number = 20;
action(); // 20
//=> 람다식은 10이라는 값을 저장 X
// number라는 변수를 캡쳐함. 따라서 20 으로 출력
```

#### 클로저

> 외부 변수를 캡처한 함수가 그 변수의 상태를 유지하면서 사용할 수 있도록 하는 구조
> 
> ⇒ 캡쳐한 변수를 들고다니는 람다

```csharp
Func<int, int> MakeAdder()
{
    int bonus = 10;               // 이 함수 안의 지역 변수
    return x => x + bonus;        // bonus를 캡처한 람다를 반환
}

var adder = MakeAdder();          // MakeAdder 함수는 이미 끝남
Debug.Log(adder(5));              // 15 → bonus가 아직 살아있음!
```

⇒ 원래 지역 변수는 함수가 끝나면 사라지는데 bonus는 MakeAdder 가 끝난 뒤에도 살아있음. 람다가 bonus를 품고 있기 때문임. 이렇게 변수를 품은 람다가 클로저

#### 메모리 관점

지역 변수는 원래 스택에 있어서 함수가 끝나면 사라짐. 그래서 컴파일러가 `캡처된 변수를 힙으로 옮겨서 수명을 늘려줌`

```text
캡처 없음: 람다만 있음, 변수는 스택에서 정상 소멸
캡처 있음: bonus가 힙 객체로 이동 → 람다가 그 객체를 가리킴 →
함수가 끝나도 유지
```

#### 주의점

1. 값이 아니라 변수 자체를 가져옴.
2. `캡쳐가 있으면 힙 할당이 발생`
3. `for 문 안에서의 캡쳐`

```csharp
var actions = new List<Action>();

for (int i = 0; i < 3; i++)
{
    actions.Add(() => Debug.Log(i));
}
foreach (var a in actions) a();
// 실행하면 0,1,2가 아니라 3,3,3 출력
```

⇒ i는 하나뿐이라 모든 람다가 같은 i를 보는데 실행 시점에는 i가 3이 되어있음.

> 람다는 만들 때 실행되는 것이 아님 `“나중에 호출할 때 실행됨”`
> 
> `actions.Add(() => Debug.Log(i))`는 **“Debug.Log(i)를 하는 함수를 만들어서 리스트에 보관”** 하는 코드일 뿐, 그 순간 `Debug.Log`가 실행되는 게 아님

즉 위 코드에서 i의 값을 복사한 것이 아닌 i 변수 자체를 들고 있어 계속 같은 i를 가리키고 있다.

```text
람다1 ─┐
람다2 ─┼──→ [ i ]  ← 하나뿐인 칸 (0 → 1 → 2 → 3으로 계속 덮어씀)
람다3 ─┘
```

의도에 맞게 그러면 해결하는 방법

```csharp
for (int i = 0; i < 3; i++)
{
    int copy = i;                        // 매 반복마다 새 칸이 만들어짐
    actions.Add(() => Debug.Log(copy));
}
```

매 반복마다 변수를 생성해서 람다에 넣음

```text
1회차: [ copy=0 ] ← 람다1
2회차: [ copy=1 ] ← 람다2
3회차: [ copy=2 ] ← 람다3
```

⇒ `람다는 변수 자체를 캡처하므로, 반복마다 값을 따로 보존하려면 반복문 안에서 새 변수를 만들어 그것을 캡처해야 함`

---

### static 지역함수의 특징과 람다의 클로저 객체에 대해 정리해보시오

#### static 지역 함수

```csharp
void Calculate()
{
    int bonus = 10;
    static int Add(int a, int b)
    {
        return a + b;
    }
    Debug.Log(Add(10, 20));
}
```

- 메서드 내부에 선언하며 해당 지역 함수의 유효 범위 안에서만 호출할 수 있음
- 외부 지역 변수나 매개변수를 캡쳐할 수 없다. ⇒ 클로저 객체를 만들 필요가 없으므로 힙 할당이 발생하지 않는다.
- 실수로 캡처해서 GC 할당이 생기는 것을 방지하는 안전장치 역할

```csharp
void Calculate()
{
    int bonus = 10;

    static int Add(int a)
    {
        return a + bonus; // 컴파일 오류
    }
} //=> bonus는 외부 지역변수이기 때문에 static 지역 함수에서 사용 불가
```

```csharp
void Calculate()
{
    int bonus = 10;

    static int Add(int a, int additionalValue)
    {
        return a + additionalValue;
    }

    Debug.Log(Add(20, bonus)); // 30
}
//=> 이거는 가능
```

또다른 예시 코드

```csharp
void Process()
{
    int bonus = 10;

    static int Add(int a, int b) => a + b;
    // 가능 (캡처 없음)

    // static int AddBonus(int a) => a + bonus;
    // =>컴파일 에러! bonus 캡처 불가
}
```

> static 지역 함수가 외부 상태를 암묵적으로 캡처 하지 못하도록 컴파일 단계에서 제한함.

#### 람다의 클로저 객체

> 람다가 바깥 변수를 캡처하면 컴파일러가 캡처된 변수를 필드로 가진 클래스를 자동 생성하고 그 인스턴스를 `힙에 만듦` ⇒ 이것이 클로저 객체

```csharp
int bonus = 10;
Func<int, int> f = x => x + bonus;

// 컴파일러가 만드는 구조 (개념)
class DisplayClass
{
    public int bonus; // 캡처된 변수가 필드가 됨
    public int Lambda(int x) => x + bonus;
}
```

- bonus는 스택이 아닌 `힙의 클로저 객체 필드`로 존재 ⇒ `함수가 끝나도 수명이 유지`
- 캡처가 있으면 클로저 객체 + 델리게이트 객체 총 2개가 힙에 할당됨
- 같은 스코프에서 캡처하는 람다들은 하나의 클로저 객체를 공유함
- 캡처한 객체가 클로저에 담겨 있어 델리게이트가 살아 있는 동안 GC되지 않음 ⇒ 이벤트 해제 안하면 메모리 누수
- 캡처가 없다는 람다는 클로저 객체를 만들지 않고 델리게이트도 static 필드에 캐싱됨

#### 정리

> 람다가 바깥 변수를 캡처하면 컴파일러가 그 변수를 필드로 가진 클로저 객체를 힙에 만들어 수명을 유지시키므로, 매 프레임 호출 같은 곳에서는 GC 할당과 메모리 누수 위험이 생깁니다.
> 
> static 지역 함수(와 static 람다)는 바깥 변수 캡처를 컴파일 단계에서 금지하고 필요한 값을 매개변수로 받게 해서, 클로저 객체 생성 자체를 막고 힙 할당 없이 동작하게 하는 안전장치입니다.

---

### this vs base

> 클래스 내부에서 객체의 멤버에 접근할 때 사용

#### this

> 현재 객체 자신을 가리키는 키워드

ex)

```csharp
class Monster {
    int hp;
    public Monster(int hp) {
        this.hp = hp;   // 필드 hp = 매개변수 hp
    }
}
```

#### base

> 현재 클래스의 부모 클래스 멤버에 접근할 때 사용하는 키워드

1. 부모 생성자 호출
   
   ```csharp
   class Slime : Monster {
       public Slime(int hp) : base(hp) { }   // 부모 생성자 먼저 실행
   }
   ```

2. 오버라이드한 부모 메서드 호출
   
   ```csharp
   public override void Attack() {
       base.Attack(); // 부모 로직 실행
       Debug.Log("독 공격"); // 자식 로직 추가
   }
   ```

---

### C# Dictionary VS HashTable

> 두 자료구조 모두 Key - Value 형태로 데이터를 저장하는 자료구조

#### Dictionary ⇒ 제네릭 지원 자료구조

```csharp
Dictionary<int, string> players = new Dictionary<int, string>();

players.Add(1, "Warrior");
players.Add(2, "Mage");

Debug.Log(players[1]); // Warrior
```

##### 특징

- Key Value의 자료형을 명시
- 컴파일 시점에 타입 검사 가능
- 값 형식을 저장할 때 불필요한 박싱, 언박싱 줄임

#### HashTable ⇒ object 타입으로 저장하는 비제네릭 자료구조

```csharp
Hashtable players = new Hashtable();

players.Add(1, "Warrior");
players.Add("Level", 10);

Debug.Log(players[1]); // Warrior
```

##### 특징

- Key Value를 object 타입으로 관리
- 서로 다른 자료형을 저장 가능
- 값 꺼낼때 형변환
- int와 같은 값 형식을 저장하면 박싱 발생

---

### namespace , partial, using

#### namespace

- 클래스,구조체,인터페이스 열거형 등을 논리적으로 그룹화하기 위한 공간

#### partial

- 하나의 클래스,구조체,인터페이스를 여러 파일에 나눠서 정의할 수 있도록 하는 키워드

##### 사용이유

1. 코드 분할
   ⇒ 큰 클래스나 오토제너레이트된 코드와 사용자 코드를 분리

2. 유지보수
   ⇒ 협업 시 충돌을 줄이고 역할별 파일 분리 가능

3. 자동 생성 코드 통합
   ⇒ 디자이너 툴, 유니티 등에서 코드 자동 생성 시 유용

##### 주의사항

- 모든 partial 정의에는 접근 제한자와 제네릭 파라미터가 동일 해야함
- 컴파일 시점에 모든 partial 정의가 유효한 단일 정의로 합쳐져야함
- partial이 없는 정의와 partial이 있는 정의는 합쳐질 수 없다

#### using

- 네임스페이스를 가져옴
  특정 네임 스페이스 가져옴

- 리소스 자동 해제
  
  - IDisposable 인터페이스를 구현하는 객체의 경우 using 문을 사용하여 Dispose() 메서드를 자동 호출할 수 있다

```csharp
// using 문을 사용하여 리소스 자동 해제
using (FileStream fs = new FileStream("file.txt", FileMode.Open))
{
    // 파일 작업
} // 블록이 끝나면 fs.Dispose()가 자동으로 호출됨
```

---

### IDisposable 이란?

#### IDisposable

> 객체가 사용하던 자원을 명시적으로 해제할 수 있도록 해주는 인터페이스

#### 필요 이유

> GC는 사용하지 않는 객체의 관리 메모리를 회수하지만 파일이나 네트워크 연결 같은 자원을 원하는 시점에 바로 정리해 주지는 않음
> 
> 그래서 Dispose()를 호출해 사용이 끝난 자원을 명시적으로 해제함

```csharp
using (StreamReader reader = new StreamReader("data.txt"))
{
    string text = reader.ReadToEnd();
}
// 블록을 벗어나면 Dispose()가 자동 호출되어 파일이 닫힘
```

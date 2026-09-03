09/01
=====

잔디 심기
-----

[NeetCode | Coding Interview Prep, Courses, Versus Mode](https://neetcode.io/problems/minimum-size-subarray-sum/history?submissionIndex=0)

자소서 수정
-------------

cs 문제 풀이 및 정리
-------------

### 

### 가상 함수를 가진 클래스에서 부모 클래스의 소멸자에 virtual을 붙여야하는 이유?

<aside>

### 가상 함수를 가진 클래스에서 부모 클래스의 소멸자에 virtual을 붙여야 하는 이유

#### 요약

자식 클래스의 소멸자가 호출되지 않아 메모리 누수가 발생.

#### 문제 상황

    class Parent {
    public:
        virtual void Attack() { }
        ~Parent() { std::cout << "부모 소멸자 호출\n"; } // virtual이 없음!
    };
    
    class Child : public Parent {
    private:
        int* dynamicArray;
    public:
        Child() { dynamicArray = new int[100]; } 
        // 자식 내부에서 메모리 할당
        ~Child() { 
            delete[] dynamicArray; // 메모리 해제 로직
            std::cout << "자식 소멸자 호출\n"; 
        }
    };

위 상태에서  
Parent* ptr=new Child();  
delete ptr;

#### 발생 상황

1. delete ptr을 실행할 때 컴파일러는 포인터 변수의 타입인 `Parent` 클래스의 소멸자를 보러감.
2. 부모 소멸자에 `virtual` 키워드 없음 ⇒ 일반 함수로 인식하여 정적 바인딩을 함.
3. 결과적으로 부모 소멸자 호출만 찍히고 정작 실행되어야 할 자식의 소멸자는 호출 X
4. 자식 소멸자 호출 X 따라서 자식에서 할당된 new int[100]에서는 **메모리 누수**

#### virtual을 정상적으로 붙였을 때

    class Parent {
    public:
        virtual void Attack() { }
        virtual ~Parent() { std::cout << "부모 소멸자 호출\n"; }
    };

1. 부모 소멸자 virtual확인

2. 컴파일러는 주소를 고정 X **가상함수 테이블(vTable)**을 참조 하여 실제 객체인 Child 클래스의 소멸자를 찾아가 호출함 ⇒ `동적 바인딩`

3. 자식 소멸자가 먼저 호출되면서 new int[100] 가 안전하게 메모리 해제됨  
   
   </aside>

<aside>

정리
--

> 가상 소멸자는 부모 클래스 포인터로 자식 객체를 삭제할 때 자식 클래스의 소멸자까지 정상적으로 호출되도록 하기 위해 사용합니다. 상속 관계에서 **생성자는 부모→ 자식 순서**로 호출되고, **소멸자는 반대로 자식 → 부모 순서**로 호출됩니다. 부모 소멸자가 virtual이 아니면 부모 포인터로 delete할 때 자식 소멸자가 호출되지 않아 자식이 보유한 메모리나 리소스가 해제되지 않을 수 있습니다. 따라서 다형적으로 사용되는 기반 클래스에서는 소멸자를 virtual로 선언하는 것이 중요합니다

</aside>



### 객체 생성 및 소멸 과정에서 생성자, 소멸자에서 가상 함수를 호출하면 안되는 이유?

<aside>

> 자식 객체 생성 시에 가상함수를 호출하게 되면 부모가 먼저 생성될 때 자식 객체는 아직 초기화 되지 않은 상태이므로 자식 객체는 부모 클래스인 것처럼 동작한다. 즉 부모 클래스의 virtual이 호출된다. 그래서 의도하지 않은 동작을 하게 된다.

### 예시 상황

    class Monster {
    public:
        Monster() {
            Init();  // 생성자에서 가상 함수 호출
        }
        virtual void Init() {
            std::cout << "Monster::Init 호출\n";
        }
    };
    
    class Slime : public Monster {
    private:
        int poisonDamage;
    public:
        Slime() : Monster() {
            poisonDamage = 10;  // Monster() 생성자가 끝난 뒤에 실행됨
        }
        void Init() override {
            std::cout << "Slime::Init 호출, poisonDamage = " << poisonDamage << "\n";
        }
    };
    
    Slime* s = new Slime();

#### 실행 순서

1. new Slime() 호출
2. Slime 생성자 본문 실행 전 베이스 클래스인 Monster() 생성자부터 먼저 실행됨
3. Monster() 생성자 안에서 Init() 호출
4. 이 시점의 객체는 아직 Monster로만 존재함. Slime 부분은 아직 생성되지 않은 상태
5. 그래서 Int()을 가상 함수로 선언했어도 Slime : : Init()이 아니라 Monster : : Init()이 호출됨.

⇒ 따라서 이는 Slime :: Init() 이 호출될 것을 의도했지만 의도하지 않은 코드가 실행됨

</aside>

### 

### 페이지 폴트가 발생했지만 프로그램이 정상 계속 실행됐다. 가능한 이유는?

<aside>

> 페이지 폴트는 에러가 아니라 OS가 처리하는 정상적인 예외임. demand paging처럼 필요한 페이지가 Ram에 없을 경우 운영체제가 해당 페이지를 디스크에서 메모리로 가져오고 페이지 테이블을 갱신한 뒤 중단됐던 명령어를 다시 실행할 수 있습니다. 따라서 운영체제가 복구 가능한 페이지 폴트라면 프로그램은 정상적으로 계속 실행됩니다.  
> 반면 잘못된 주소 접근이나 권한 위반처럼 해결할 수 없는 경우에는 프로세스가 종료될 수 있습니다

</aside>



### 페이지 폴트가 발생 ⇒ 재개까지 과정

<aside>

#### 1. 페이지 폴트 발생

* MMU유효 비트 0을 감지하고 트랩을 발생시켜 커널 모드로 전환

#### 2. CPU 상태 백업

* 현재 레지스터, PC 등을 PCB 에 저장

#### 3. 페이지 폴트 처리 루틴 실행

정당성 검사 → 필요시 페이지 교체 → 디스크에서 페이지를 프레임에 적재 → 페이지 테이블의 유효 비트를 1로 갱신

#### 4. 상태 복원 및 명령어 재실행 (다시 사용자 모드로 전환)

* 백업 했던 CPU 상태를 복원하고 페이지 폴트를 일으켰던 명령어를 처음부터 다시 실행 ⇒ 이번엔 유효 비트가 1이므로 정상 접근 성공  
  
  </aside>



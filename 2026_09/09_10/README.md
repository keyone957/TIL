09/10
=====

테스트 대비 CS 공부
------------

### override vs new

### override → 동적 바인딩 ⇒ 다형성 O

부모의 virtual 메서드를 자식이 재정의 하는 것. 런타임에 실제 객체 타입을 보고 어떤 메서드를 호출할 지 결정됨

* 코드 예시
  
      class Monster {
          public virtual void Attack() {
              Console.WriteLine("Monster Attack");
          }
      }
      
      class Slime : Monster {
          public override void Attack() {
              Console.WriteLine("Slime Attack");
          }
      }
      
      Monster m = new Slime();
      m.Attack();  // "Slime Attack" 출력!
  
  

### new → 메서드 숨기기 (정적 바인딩)⇒ 다형성 X

부모 메서드를 재정의 하는것이 아니라 자식 클래스에서 완전히 별개의 새로운 메서드로 덮어씀. 부모의 메서드는 그대로 남아있고, 단지 자식 타입으로 호출할 때 어떤걸 부를지가 컴파일 타임 타입에 의해 결정됨

* 코드 예시
  
      class Monster {
          public void Attack() {
              Console.WriteLine("Monster Attack");
          }
      }
      
      class Slime : Monster {
          public new void Attack() {
              Console.WriteLine("Slime Attack");
          }
      }
      
      Monster m = new Slime();
      m.Attack();  // "Monster Attack" 출력! (기대와 다름)
      
      Slime s = new Slime();
      s.Attack();  // "Slime Attack" 출력
  
  

### 비교

|                | override                   | new                      |     |
| -------------- | -------------------------- | ------------------------ | --- |
| 필요 조건          | 부모가 virtual / abstract 여야함 | 조건 x                     |     |
| 바인딩 방식         | 동적 바인딩 ⇒ 런타임에 실제 타입 확인     | 정적 바인딩 ⇒ 컴파일 타임 선언 타입 기준 |     |
| 부모 타입 참조로 호출 시 | 자식 버전 호출됨 ⇒ 다형성 o          | 부모 버전 호출 됨 ⇒ 다형성 x       |     |

### 정리

override는 부모 클래스의 virtual 혹은 abstract 메서드를 자식 클래스에서 재정의 하는 것으로 다형성이 적용되어 부모 타입으로 참조하더라도 실제 객체 타입의 메서드가 호출됩니다.new는 부모의 멤버를 재정의 하는 것이 아니라 같은 이름의 새로운 멤버로 숨기는 것이기 때문에 참조 변수의 타입에 따라 호출되는 메서드가 달라질 수 있습니다.따라서 상속을 통한 다형성이 목적이라면 일반적으로 override를 사용하고 부모 멤버를 의도적으로 숨겨야 할 때 new를 사용합니다.

### lvalue rvalue

### Lvalue

식별 가능하고 참조 가능한 객체.이름이 있고 메모리 상에 고정된 위치를 가지며 이후에도 참조 가능한 값. 대입 연산자의 왼쪽에 올 수 있다고 해서 lvalue로 불림.

변수
    int x = 10;   // x는 lvalue
    x = 20;       // x에 다시 대입 가능 → 계속 존재하는 주소를 가짐

    int* p = &x;  // lvalue만 주소를 취할 수 있음 (&x 가능)

### Rvalue

한 번 평가되고 사라지는 값.이름 없고 임시로 생성되어 표현식이 끝나면 사라지는 값

10 + 5

MakeInt()
    int y = 10 + 5;   // (10 + 5)는 rvalue - 계산 결과로 잠깐 존재했다 사라짐
    int z = MakeInt(); // MakeInt()의 반환값은 rvalue - 임시 객체

    10 = y;  // 컴파일 에러! rvalue는 대입의 왼쪽에 올 수 없음
    &(10+5); // 컴파일 에러! rvalue는 주소를 취할 수 없음

### 이동 시멘틱

### 이동 시멘틱

객체의 데이터를 복사하는 대신 기존 객체가 가지고 있던 자원의 소유권을 다른 객체에게 이전하는 기능입니다.

* 값의 복사 비용이 큰 객체에서 std::move를 사용해 자원의 **소유권 이전**
* 임시 객체(rvalue)에 대해 복사 대신 이동을 수행

#### 기존 방식

    class Inventory {
        int* items;
    public:
        Inventory(int size) { items = new int[size]; }
        ~Inventory() { delete[] items; }
    };
    
    Inventory MakeInventory() {
        Inventory temp(1000);  // int 1000개 할당
        return temp;
    }
    
    Inventory a = MakeInventory();

temp가 함수 밖으로 나가는 순간 그 내용을 전부 복사해서 a에 새로 담고 temp는 그대로 버려짐. temp는 어차피 함수가 끝나면 사라질 임시 객체인데도 굳이 깊은 복사(메모리 재할당 + 값 복사)를 거친 뒤 버려지는 낭비가 발생함

#### 이동 시멘틱이 하는 일

temp처럼 곧 사라질 값이라면 복사 x, 포인터만 넘겨 받고 temp의 포인터는 비워버림
    Inventory(Inventory&& other) noexcept {  // 이동 생성자
        items = other.items;     // 포인터 값만 가져옴 (복사 아님)
        other.items = nullptr;   // 원본은 비움
    }

새 메모리 할당 없이 포인터 하나만 옮기고 끝. temp가 소멸돼도 items가 nullptr이라 delete[] nullptr은 안전하게 무시됨

#### 곧 사라질 값을 구분하는 방법

컴파일러는 temp처럼 이름 없이 곧 사라지는 임시 값을 rvalue라 부르고 &&로 이걸 받는 함수를 따로 만들 수 있음
    Inventory(const Inventory& other) { ... }   
    // lvalue용 - 복사 생성자
    Inventory(Inventory&& other) noexcept { ... } 
    // rvalue용 - 이동 생성자

* 이름 있는 변수(`a`, `temp` 자체)를 넘기면 → 복사 생성자 호출
* 함수가 반환한 임시 객체, `Inventory(10)` 같은 즉석 생성값을 넘기면 → 이동 생성자 호출

#### std::move ⇒ 이런 버려도 되는 값이야 라고 알려주기

    Inventory a(1000);
    Inventory b = a;
    // a는 lvalue → 복사 생성자 호출 (a는 그대로 남음)
    Inventory c = std::move(a);
    // a를 rvalue처럼 취급하도록 캐스팅 → 이동 생성자 호출
    // a는 이제 비워진 상태 (items == nullptr), 이후 쓰면 안 됨

std::move는 실제로 아무것도 옮기지 않음. "lvalue를 rvalue처럼 다뤄도 된다"는 타입 캐스팅일 뿐이고 실제 이동은 그 값을 받는 이동 생성자 / 이동 대입 연산자가 수행함

### 정리

곧 사라질 임시 객체는 굳이 복사할 필요 없이 갖고 있는 자원을 그냥 써도 안전하다는 원칙을 언어 차원에서 구현한 것이 이동 시멘틱이고, `&&`(이동 생성자/대입)와 `std::move`(강제 캐스팅)가 그 실행 도구.

### Rule of Five

### Rule of Five

자원의 중복 해제 혹은 누수를 방지하기 위한 규칙.

5가지를 모두 생략하거나 모두 정의해야 함

클래스가 메모리, 파일 핸들, 소켓 같은 자원을 직접 소유할 때 복사, 이동, 소멸 동작을 명확하게 정의해야 한다는 원칙

#### 안지키면?

하나라도 정의하면 나머지는 자동 생성되지 않거나 삭제됨

#### 5가지

1. 소멸자
   * 객체가 소유한 자원을 해제함
2. 복사 생성자
   * 새 객체를 기존 객체의 복사본으로 만듭니다
3. 복사 대입 연산자
   * 이미 존재하는 객체에 다른 객체를 복사합니다
4. 이동 생성자
   * 임시 객체 등의 자원 소유권을 새 객체로 넘깁니다.
5. 이동 대입 연산자
   * 기존 객체의 자원을 정리하고 다른 객체의 자원을 넘겨 받습니다.

#### why?

* 근데 왜 이런 규칙 생김?
  * 자동으로 생성되지 않고 예상치 못한 문제가 발생할 수 있음
* 왜 하필이면 이 5개임?
  * 이 특별 멤버 함수들이 동적 메모리, 스마트포인터가 아닌 생 포인터 등 직접적인 자원 소유를 복사/이동/소멸을 하는데 이런 자원 관리를 명확하게 해야하기 때문이다.

### 남은 메모리 보다 작은 공간을 할당하는 데 왜 out of memory 오류가 나는걸까?

### 외부 단편화

메모리 할당은 대부분 연속된 공간을 요구하기 때문. 여유 메모리의 총합은 요청 크기보다 커도 그 여유 공간이 여러 조각으로 흩어져 있으면 요청한 크기만큼의 연속 블록을 찾을 수 없어서 할당이 실패

### mipmap

### 정의

하나의 텍스쳐를 여러 해상도로 미리 만들어 두고 화면에서 보이는 크기나 카메라와의 거리에 따라 적절한 해상도의 텍스처를 사용하는 방식.멀리 있는 오브젝트에는 낮은 해상도의 텍스처를 사용함으로써 엘리어싱을 줄이고 렌더링 성능을 개선하는 기법.

* 엘리어싱

⇒ 멀리 있는 고해상도 텍스처를 그대로 축소해서 보여주면 화면이 반짝이거나 깨져 보이는 현상이 발생할 수 있다.

### 예시

2048 * 2048 텍스처를 그대로 사용하면 멀리 있는 오브젝트는 굳이 해상도를 높일 필요는 없으므로

1048 * 1048 / 512 * 512 ….단계 별로 크기를 절반씩 줄인 여러 단계의 텍스처를 미리 생성한다. 그래서 이제 거리에 맞게 텍스쳐를 사용함

### 가상 상속

### 가상 상속 ⇒ 다이아몬드 상속 문제 해결

C++에서 다중 상속 시 같은 부모 클래스가 여러 경로로 중복 상속되는 문제를 해결하기 위한 기능

#### 다이아몬드 상속 문제?

문제 상황
    class Animal {
    public:
        int age = 0;
    };

    class Mammal : public Animal {};
    class WingedAnimal : public Animal {};

    class Bat : public Mammal, public WingedAnimal {};

                일반 상속                
            Bat   
           /   \
      Mammal   Winged 
         |         |             
      Animal   Animal            

    Bat bat;
    // bat.age = 10;  
    // Bat 객체 안에는 Animal 부분 객체가 두 개 들어감.
    // 오류: 어느 Animal의 age인지 모호함
    //bat.Mammal::age
    //bat.WingedAnimal::age
    //==>위 두가지 경로가 존재해서 어느 animal인지 애매함.
    ==> 가상 상속을 사용하지 않으면 Animal의 age 값은 공유 되어야 하는 값인데 
    저렇게 둘다 animal을 일반 함수로 상속을 받으면 두가지 경로가 생겨
    어느 animal의 age를 사용해야할지 모호해지는 다이아몬드 상속 문제가 생김

#### 해결 방법 → 가상 상속을 사용해서 해결

중간 클래스들이 Animal을 virtual로 상속하면 됨
    class Animal {
    public:
        int age = 0;
    };

    class Mammal : virtual public Animal {};
    class WingedAnimal : virtual public Animal {};

    class Bat : public Mammal, public WingedAnimal {};

            가상 상속을 사용
            Animal
           /      \
       Mammal   WingedAnimal
           \      /
              Bat

이러면 Bat이 참조할 Animal의 age를 하나만 딱 참조하면 됨

#### 언제 사용함?

1. 다중 상속 구조가 필요할 때
2. 여러 부모 클래스가 같은 기반 클래스를 상속할 때
3. 최종 객체에서 기반 클래스가 하나만 존재해야 할 때

### 가상 상속 vs 가상 함수

둘 다 `virtual`을 사용하지만 목적이 다릅니다.가상 함수는 런타임 다형성을 구현하기 위해 사용합니다. 부모 클래스의 포인터나 참조로 자식 객체를 가리킬 때 실제 객체 타입에 맞는 재정의된 함수를 호출하도록 합니다.가상 상속은 다중 상속에서 같은 기반 클래스가 중복으로 상속되는 문제를 해결하기 위해 사용합니다. 대표적으로 다이아몬드 상속 구조에서 부모 클래스가 두 번 포함되면서 발생하는 데이터 중복이나 멤버 접근 모호성을 막고 하나의 기반 클래스 인스턴스만 공유하도록 합니다.

### virtual / override

### 개요

→ virtual과 override는 둘 다 다형성을 구현하기 위해 사용.

### virtual

* **역할** : 이 함수는 자식 클래스에서 재정의 될 수 있는 **`가상 함수(동적 바인딩 활성화)`**임을 선언함.
* **위치** : 부모 클래스의 원본 함수 선언부에만 붙이면 됨
* **특징** : 부모 클래스에서 한번 `virtual`로 선언된 함수는 자식 클래스에서 `virtual` 키워드를 생략하더라도 자동으로 가상 함수 성격을 유지하며 상속됨.

### override

* **역할:** 자식 클래스에서 부모의 가상 함수를 "내가 정확하게 재정의하고 있다"는 것을 컴파일러에게 명시하고 검증받는 키워드입니다.
* **위치:** 자식 클래스의 재정의 함수 뒤쪽(끝)에 붙입니다.
* **특징:** 문법적으로 필수 사항은 아니지만, 개발자의 치명적인 실수를 방지하는 **안전장치** 역할을 합니다.

### 정리

virtual은 부모 클래스에서 해당 함수를 가상 함수로 선언하여 런타임 다형성을 가능하게 하는 키워드.override는 자식 클래스에서 부모의 가상 함수를 실제로 재정의하고 있다는 것을 명시하는 키워드.일반적으로 부모에는 virtual, 자식에서는 override를 사용

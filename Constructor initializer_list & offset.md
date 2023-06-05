## Most Vexing Parse  

    class Test
    {
    public:
        Test() { cout << "run the constructor"<<endl; }
        int a, b, c;
    };

    int main()
    {
        Test CallConstructor(); // 생성자? or 함수 선언?

        return 0;
    }

## 결과
![image](https://github.com/KyeongHo-Kim/CPP/assets/80688911/73230e15-f0a7-48f0-9110-72af877cc1f4)
<br>
아무것도 출력되지 않음. Most Vexing Parse는 문법이 모호할 경우 격을 수 있는 문제를 의미한다.  
위 코드는 함수선언과 객체 생성을 구분하기 애매한 예시인데, 컴파일러는 Test타입을 반환받는 CallConstructor()함수 선언으로 해석한다.

<br><br>

## Uniform Initialization  <br>  

- { }를 사용해 객체를 초기화 시키는 문법.  
- 자동 형 변환을 방지함  

<b>

    class Test
    {
    public:
        Test() { cout << "run the constructor" << endl; }

        Test(int i) { cout << i << endl; }
    };


    int main()
    {
        Test CallConstructor{};

        Test t1(5.3); // 출력값	5
        Test t2{5.3}; // error C2398: 요소 '1': 'double'에서 'int'(으)로 변환하려면 축소 변환이 필요합니다.

        return 0;
    }
> Test CallConstructor{ } : 앞서 ( )달리 원하는대로 동작함 "run the constructor" 출력됨
> 
> Test t1(5.3) : 자동 형 변환이 일어남 5가 출력됨
>
> Test t2{5.3} : 위에서 언급했듯  Uniform Initialization은 자동 형 변환이 안됨  
> 의도치 않은 오류를 잡을 수 있다는 장점이 있음.


<br><br>

## Initializer_list  <br>  

- { }를 사용하여 여러 개의 요소를 초기화하는 데 사용되는 문법  
- initializer_list는 고정된 크기의 요소들을 가지는 배열처럼 동작함
- initializer_list 객체는 자체적으로 메모리를 소유하지 않으며, 다른 객체의 일부로만 존재함  
  즉 포인터와 요소의 개수를 가지고 있지만, 요소들을 저장하기 위한 실제 메모리 공간을 소유하지는 않음  

<br>

    using std::initializer_list;
    using std::vector;

    template<typename T>
    class Base
    {
    public:
        
        Base(initializer_list< T> p) : v(p) {};

        void GetInitializer()
        {
            for (T tmep : v)
            {
                cout << tmep << endl;
            }
        }

        void GetOffset()
        {
            cout << (size_t) & static_cast<Base*>(nullptr)->v;
        }

    private:
        vector<T> v;
    };

    int main()
    {
        Base<int> i_list = { 1,2,3 };
        Base<const char*> c_list = { "AA", "BB", "CC"};

        i_list.GetInitializer(); // 1, 2, 3 출력
        c_list.GetInitializer(); // AA, BB, CC 출력

        return 0;
    }


<br><br>

## Offset
- "offset"은 특정 멤버 변수의 주소(위치)가 해당 객체의 시작 메모리 주소로부터 얼마나 떨어져 있는지를 나타낸다.  
  즉 멤버 변수가 객체의 시작 위치로부터 상대적으로 얼마나 떨어져 있는지를 의미함  

<br>

    class Base
    {
    public:
        int a;
        int b;
        int c;
    };

    int main()
    {
        cout << (size_t) & static_cast<Base*>(nullptr)->a << endl; // 0
        cout << (size_t) & static_cast<Base*>(nullptr)->b << endl; // 4
        cout << (size_t) & static_cast<Base*>(nullptr)->c << endl; // 8

        cout << "-"<<endl;

        Base base;
        cout << reinterpret_cast<char*>(&base.a) - reinterpret_cast<char*>(&base) << endl; // 0
        cout << reinterpret_cast<char*>(&base.b) - reinterpret_cast<char*>(&base) << endl; // 4
        cout << reinterpret_cast<char*>(&base.c) - reinterpret_cast<char*>(&base) << endl; // 8

        return 0;
    }

> cout << (size_t) & static_cast<Base*>(nullptr)->a << endl;
- nullptr을 사용하여 가짜 객체를 생성하고, 해당 객체의 멤버 변수 a의 주소를 구함  
- 컴파일 시간에 실행되며, 객체를 생성하지 않고 멤버 변수의 주소를 계산

### 동작

1. static_cast<test*>(nullptr) : nullptr을 test* 포인터로 형변환함, 이렇게 변환된 포인터는 가짜 객체를 나타냄  
2. static_cast<test*>(nullptr)->a : 가짜 객체의 멤버 변수 a를 참조한다. 이 때, 가짜 객체는 실제로 존재하지 않기 때문에 접근 시도는 무의미함. 하지만 컴파일러는 이 코드를 통해 멤버 변수 a의 타입을 알 수 있음  
3. & static_cast<test*>(nullptr)->a : 가짜 객체의 멤버 변수 a의 주소를 구한다. 컴파일 타임에 계산되며, 실제 메모리 주소는 사용되지 않음  
4. (size_t) : 주소 값을 size_t 형식으로 형변환 하여 변환된 주소 값을 정수로 표현할 수 있음  

<br>

> cout << reinterpret_cast<char*>(&ofset.a) - reinterpret_cast<char*>(&ofset) << endl;
- 실제 객체 base의 멤버 변수 a의 주소와 객체 base의 주소를 구함
- 런타임에 실행되며, 실제 객체를 생성하고 멤버 변수의 주소를 계산

### 동작
1. reinterpret_cast<char*>(&base.a) : base.a의 주소를 char* 포인터로 캐스팅하는 것을 의미함  
   char*는 Base a의 주소를 바이트 단위로 표현하기 위함
2. reinterpret_cast<char*>(&base) : 마찬가지로 base의 주소를 char* 포인터로 캐스팅하는 것
3. 두 포인터의 차이를 계산하면 base.a의 주소가 base의 시작 주소로부터 얼마나 떨어져 있는지가 나타난다.

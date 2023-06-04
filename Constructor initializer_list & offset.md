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
![image](https://github.com/KyeongHo-Kim/CPP/assets/80688911/b9c60ea4-e923-4ec8-80ce-640b65712aab)

아무것도 출력되지 않음. Most Vexing Parse는 문법이 모호할 경우 격을 수 있는 문제를 의미한다.  
위 코드는 함수선언과 객체 생성을 구분하기 애매한 예시인데, 컴파일러는 Test타입을 반환받는  CallConstructor()함수 선언으로 해석한다.



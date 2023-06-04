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
image.png
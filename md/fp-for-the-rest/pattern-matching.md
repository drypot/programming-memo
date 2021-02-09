# Pattern Matching

Pattern matching is not a new or innovative feature. In fact, it has little to do with functional programming. The only reason why it's usually attributed to FP is that functional languages have had pattern matching for some time, while modern imperative languages still don't.

패턴 매칭은 새롭거나 혁명적인 기능이 아닙니다. 사실 FP 와는 큰 관계도 없습니다. 패턴 매칭이 FP 의 결과라고 언급되는 유일한 이유는 펑셔널 언어들이 패턴 매칭을 가지고 있었기 때문입니다. 현대적인 절차 언어들은 패턴 매칭을 아직 가지고 있지 않습니다.

Let's dive into pattern matching with an example. Here's a Fibonacci function in Java:

예제를 통해 패턴 매칭을 알아보겠습니다. Java 로 된 피보나치 수열이 있습니다.

    int fib(int n) {
        if(n == 0) return 1;
        if(n == 1) return 1;

        return fib(n - 2) + fib(n - 1);
    }

And here's an example of a Fibonacci function in our Java-derived language that supports pattern matching:

Java 에서 유도된 패턴 매칭 지원 언어에서는 다음과 같이 쓸 수 있습니다.

    int fib(0) {
        return 1;
    }

    int fib(1) {
        return 1;
    }

    int fib(int n) {
        return fib(n - 2) + fib(n - 1);
    }

What's the difference? The compiler implements branching for us.

뭐가 다를 까요? 분기를 컴파일러가 대신 구현했습니다.

What's the big deal? There isn't any. Someone noticed that a large number of functions contain very complicated switch statements (this is particularly true about functional programs) and decided that it's a good idea to abstract that away. We split the function definition into multiple ones, and put patterns in place of some arguments (sort of like overloading). When the function is called, the compiler compares the arguments with the definitions at runtime, and picks the correct one. This is usually done by picking the most specific definition available. For example, int fib(int n) can be called with n equal to 1, but it isn't because int fib(1) is more specific.

이게 대단한가요? 대단한 것 없습니다. 많은 펑션들이 매우 복잡한 스위치 문을 가지고 있는 것을 누군가가 인식하고 이것을 추상화 시키는 것이 멋진 아이디어라고 결정내렸습니다. (FP 에서는 확실히 복잡한 스위치 문이 많이 생깁니다.) 펑션 정의를 여러 조각으로 나누고 인자 자리에 대신 패턴을 채웠습니다. (오버로딩 비슷하게) 펑션을 호출하면 컴파일러 런타임은 인자를 펑션정의와 비교해서 알맞은 펑션을 선택합니다. 유효한 펑션중 정의가 가장 구체적인 것이 선택됩니다. 예를 들어, `n` 이 `1` 일 때 `int fib(int n)` 도 매칭되지만 보다 구체적인 `int fib(1)` 이 호출됩니다.

Pattern matching is usually more complex than our example reveals. For example, an advanced pattern matching system will allow us to do the following:

패턴 매칭은 보통 우리 예제보다 더 복잡합니다. 발전된 패턴 매칭 시스템에서는 다음과 같은 것도 가능합니다.

    int f(int n < 10) { ... }
    int f(int n) { ... }

When is pattern matching useful? In a surprisingly large number of cases! Every time you have a complex structure of nested ifs, pattern matching can do a better job with less code on your part. A good function that comes to mind is a standard WndProc function that all Win32 applications must provide (even though it's often abstracted away). Usually a pattern matching system can examine collections as well as simple values. For example, if you pass an array to your function you could pick out all arrays in which the first element is equal to 1 and the third element is greater than 3.

패턴 매칭이 언제 유용할까요? 상상외로 무척 많은 경우에 유용합니다. 중첩된 if 구문이 나타날 때마다 패턴 매칭은 보다 적은 코드로 더 나은 일처리를 보여줍니다. 떠오르는 좋은 예로 `WndProc` 펑션이 있습니다. 모든 Win32 어플리케이션이 어 펑션을 제공행야하지요. 일반적으로 패턴 매칭 시스템은 단순 값 뿐만 아니라 컬랙션도 조사할 수 있습니다. 예로, 어래이를 펑션에 전달해서 첫 원소가 1 이고 세 번째 원소가 3 인 것을 골라낼 수 있습니다.

Another benefit of pattern matching is that if you need to add or modify conditions, you don't have to go into one huge function. You simply add (or modify) appropriate definitions. This eliminates the need for a whole range of design patterns from the GoF book. The more complex your conditions are, the more pattern matching will help you. Once you're used to it, you start wondering how you ever got through your day without it.

패턴 매칭의 또 다른 잇점은 조건을 추가하거나 수정할 때 하나의 거대한 평션을 다룰 필요가 없다는 겁니다. 적당한 정의를 간단히 추가하가나 수정할 수 있습니다. GoF 책 디자인 패턴 전체가 필요없어 집니다. 조건이 복잡할 수록 더 큰 도움이 됩니다. 한번 써보면, 이것 없이 어떻가 살았는가 싶으실 겁니다.

[Functional Programming For The Rest Of Us](index.md)

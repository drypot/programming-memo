# Lazy Evaluation / 지연 평가

Lazy (or delayed) evaluation is an interesting technique that becomes possible once we adopt a functional philosophy. We've already seen the following piece of code when we were talking about concurrency:

펑셔널 철학을 받어들이면 지연 평가라는 재밋는 기술이 가능해 집니다. 병렬처리를 이야기하면서 아래 코드를 한번 보았습니다.

    String s1 = somewhatLongOperation1();
    String s2 = somewhatLongOperation2();
    String s3 = concatenate(s1, s2);

In an imperative language the order of evaluation would be clear. Because each function may affect or depend on an external state it would be necessary to execute them in order: first somewhatLongOperation1, then somewhatLongOperation2, followed by concatenate. Not so in functional languages.

절차적 언어에서는 평가 순서가 명확합니다. 각 펑션이 외부 상태에 영향을 미치거나 이에 의존하기 때문에 `somewhatLongOperation1`, `somewhatLongOperation2`, `concatenate` 을 순서대로 실행할 필요가 있습니다. 하지만 펑셔널 언어에서는 그렇지 않습니다.

As we saw earlier somewhatLongOperation1 and somewhatLongOperation2 can be executed concurrently because we're guaranteed no function affects or depends on global state. But what if we don't want to run the two concurrently, do we need to run them in order? The answer is no. We only need to run these operations when another function depends on s1 and s2. We don't even have to run them before concatenate is called - we can delay their evaluation until they're required within concatenate. If we replace concatenate with a function that has a conditional and uses only one of its two parameters we may never evaluate one of the parameters at all!

앞서 보았듯이 펑션이 전역 상태에 영향을 준다거나 의존적이지 않기 때문에 `somewhatLongOperation1` 와 `somewhatLongOperation2` 는 동시에 실행될 수 이었습니다. 그런데 두 펑션을 동시에 실행할 필요가 없다면, 꼭 순서대로 실행할 필요가 있을까요? 그럴 필요가 없습니다. 단지 제 3 의 펑션이 `s1` 과 `s2` 에 의존성을 가질 때만 이들을 실행하면 됩니다. `concatenate` 펑션이 호출되기 전까지는 실행할 필요가 없습니다. 즉, `concatenate` 에서 사용하기 전까지 펑션의 평가를 미룰 수 있습니다. `concatenate` 펑션이 조건문을 가지고 두 인자중 하나만 사용하도록 바꾼다면 나머지 인자는 아예 평가에서 제외할 수 있습니다.

Haskell is an example of a delayed evaluation language. In Haskell you are not guaranteed that anything will be executed in order (or at all) because Haskell only executes code when it's required.

Haskell 은 지연 평가 언어중의 하나 입니다. 하스켈에서는 코드가 실행된다거나 꼭 순서대로 실행된다는 보장이 없습니다. 실행이 필요할 경우만 실행됩니다.

Lazy evaluation has numerous advantages as well as disadvantages. We will discuss the advantages here and will explain how to counter the disadvantages in the next section.

지연 평가는 많은 잇점과 함께 단점도 가집나다. 잇점을 먼지 보고 마지막에 단점을 보도록하겠습니다.

## Optimization / 최적화

Lazy evaluation provides a tremendous potential for optimizations. A lazy compiler thinks of functional code exactly as mathematicians think of an algebra expression - it can cancel things out and completely prevent execution, rearrange pieces of code for higher efficiency, even arrange code in a way that reduces errors, all guaranteeing optimizations won't break the code. This is the biggest benefit of representing programs strictly using formal primitives - code adheres to mathematical laws and can be reasoned about mathematically.

지연 평가는 최적화에 엄청난 가능성을 제공합니다. 지연형 컴파일러는 수학자들이 대수학 수식을 생각하는 것과 같은 방식으로 펑셔널 코드를 생각합니다. 일정 부분을 약분하여 실행에서 완전 제외시킬 수도 있고, 높은 효율성을 위해 코드의 일정 부분을 재배열할 수도 있고, 오류를 줄이기는 방식으로 코드를 재편성할 수도 있습니다. 최적화를 한다고 코드가 깨지지 않습니다. 이와같은 큰 잇점들은 수학 규칙에 부합하고 수학적으로 추론할 수 있는 형식 어휘들을 사용하여 프로그램을 엄격히 표현함으로써 얻는 것입니다.

## Abstracting Control Structures / 제어 구문 추상화

Lazy evaluation provides a higher order of abstraction that allows implementing things in a way that would otherwise be impossible. For example consider implementing the following control structure:

지연 평가는 고차원의 추상화를 제공합니다. 이것은 다른 방식으로는 구현이 불가능합니다. 다음 제어 구문을 만든다고 가정해 보겠습니다.

    unless(stock.isEuropean()) {
        sendToSEC(stock);
    }

We want sendToSEC executed unless the stock is European. How can we implement unless? Without lazy evaluation we'd need some form of a macro system, but in a language like Haskell that's unnecessary. We can implement unless as a function!

`stock` 이 European 이면 `sendToSec` 을 실행하길 원합니다. `unless` 를 어떻게 구현할 수 있을까요? 지연 평가가 없다면 어떤 형태의 매크로 시스템이 필요할 겁니다. 하지만 Haskell 같은 언어에서 그런 것은 필요없습니다. `unless` 를 펑션으로 구현할 수 있습니다.

    void unless(boolean condition, List code) {
        if(!condition)
            code;
    }

Note that code is never evaluated if the condition is true. We cannot reproduce this behavior in a strict language because the arguments would be evaluated before unless is entered.

condition 이 true 일 때, code 는 평가되지 않습니다. 정적 언어에서는 unless 가 호출되기전에 인자가 평가되기 때문에 이런 작동방식을 재현할 수가 없습니다.

## Infinite Data Structures / 무한한 데이터 구조

Lazy languages allow for definition of infinite data structures, something that's much more complicated in a strict language. For example, consider a list with Fibonacci numbers. We clearly can't compute and infinite list in a reasonable amount of time or store it in memory. In strict languages like Java we simply define a Fibonacci function that returns a particular member from the sequence. In a language like Haskell we can abstract it further and simply define an infinite list of Fibonacci numbers. Because the language is lazy, only the necessary parts of the list that are actually used by the program are ever evaluated. This allows for abstracting a lot of problems and looking at them from a higher level (for example, we can use list processing functions on an infinite list).

지연형 언어들은 무한한 데이터 구조의 정의가 가능합니다. 정적 언어에서는 많이 복잡합니다. 예로 피보나치 수열을 생각해 보겠습니다. 적정한 시간과 메모리 제한 안에서 수열을 무한히 나열할 수 없다는 것은 확실합니다. 자바와 같은 정적 언어에서는 수열의 특정 요소를 리턴하는 피보나치 펑션을 정의합니다. 하스켈같은 언어에서는 이것을 추상화해서 무한한 피보나치 수열을 간단히 정의할 수 있습니다. 하스켈은 지연형 언어이기 때문에 수열중 프로그램에 의해 실재로 사용되는 부분만 평가가 됩니다. 이로서 많은 문제들을 추상화할 수 있고, 보다 높은 관점에서 문제를 바라볼 수 있습니다.

## Disadvantages / 단점들

Of course there ain't no such thing as a free lunch(tm). Lazy evaluation comes with a number of disadvantages. Mainly that it is, well, lazy. Many real world problems require strict evaluation. For example consider the following:

물론 공짜 점심은 없습니다. 지연 평가는 몇 가지 단점을 수반합니다. 무엇보다도 지연 평가는 모든 것을 지연시킵니다. 현실 세계의 많은 문제들은 정적 평가를 필요로 합니다.

    System.out.println("Please enter your name: ");
    System.in.readLine();

In a lazy language you have no guarantee that the first line will be executed before the second! This means we can't do IO, can't use native functions in any meaningful way (because they need to be called in order since they depend on side effects), and can't interact with the outside world! If we were to introduce primitives that allow ordered code execution we'd lose the benefits of reasoning about our code mathematically (which would take all of the benefits of functional programming with it).

지연형 언어에서는 첫 번째 문장이 두 번째보다 먼저 실행된다는 보장이 없습니다. 이 말은 입출력을 할 수 없고, 네이티브 펑션을 정해진 바 대로 사용할 수 없고, 외부 세계와 소통할 수 없다는 말입니다. (네이티브 펑션은 부수 효과에 의존하기 때문에 순서대로 호출되어야 합니다.) 코드를 수서대로 실행하는 구문들을 도입하면 코드를 수학적으로 추론하면서 얻는 잇점들을 잃을 것입니다. 이 말은 펑셔널 프로그래밍의 모든 장점을 잃는다는 말입니다.

Fortunately not all is lost. Mathematicians got to work and developed a number of tricks to ensure code gets executed in particular order in a functional setting. We get the best of both worlds! These techniques include continuations, monads, and uniqueness typing. In this article we'll only deal with continuations. We'll leave monads and uniqueness typing for another time. Interestingly, continuations are useful for many things other than enforcing a particular order of evaluation. We'll talk about that as well.

다행히 모든 것이 사라지진 않습니다. 수학자들은 펑셔널 기반에서 특정 순서대로 코드가 수행되는 것을 보장하는 몇 가지 트릭들을 개발했습니다. 두 세계에서 최적의 것들을 얻는 것입니다. 이 기술들은 continuation, monad, uniqueness typing 입니다. 이 연재에서는 continuation 만 다루겠습니다. continuation 은 평가순서를 강제하는 일 외에도 많은 곳에 유용합니다. 꽤 흥미로우실 겁니다.

[Functional Programming For The Rest Of Us](index.md)

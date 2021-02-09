# Functional Programming

Functional programming is a practical implementation of Alonzo Church's ideas. Not all lambda calculus ideas transform to practice because lambda calculus was not designed to work under physical limitations. Therefore, like object oriented programming, functional programming is a set of ideas, not a set of strict guidelines. There are many functional programming languages, and most of them do many things very differently. In this article I will explain the most widely used ideas from functional languages using examples written in Java (yes, you could write functional programs in Java if you felt particularly masochistic). In the next couple of sections we'll take Java as is, and will make modifications to it to transform it into a useable functional language. Let's begin our quest.

FP 는 Alonzo Church 의 아이디어를 실용적으로 구현한 것입니다. Lambda calculus 는 물리적인 한계를 상정하고 디자인되지 않았기 때문에 lambda calculus 의 모든 개념들이 현실화되진 못했습니다. OOP 가 그렇듯이 FP 또한 엄격한 가이드라인의 집합이 아니라 몇 가지 아이디어의 집합이라 할 수 있습니다. 많은 FP 언어들이 있는데 이들 대부분은 많은 것들을 매우 다르게 구현하고 있습니다. 이어지는 글에서는 FP 언어에서 가장 널리 사용되는 아이디어들을 자바 예제를 가지고 설명할 겁니다. (맞습니다. 자학적이라면 자바로도 F적 프로그램을 만들 수 있습니다.) 있는 그대로의 자바를 먼저 보고, 자바를 가용한 F적 언어로 변모시키기 위해 수정을 가할 겁니다. 그럼 퀘스트를 시작해 봅시다.

Lambda calculus was designed to investigate problems related to calculation. Functional programming, therefore, primarily deals with calculation, and, surprisingly, uses functions to do so. A function is a very basic unit in functional programming. Functions are used for almost everything, even the simplest of calculations. Even variables are replaced with functions. In functional programming variables are simply aliases for expressions (so we don't have to type everything on one line). They cannot be modified. All variables can only be assigned to once. In Java terms this means that every single variable is declared as final (or const if we're dealing with C++). There are no non-final variables in FP.

Lambda calculus 는 계산(calculation)과 연관된 문제들을 조사하기 위해 디자인 되었습니다. 그러므로 FP 란 기본적으로 계산을 다루는데 놀랍게도 이 작업에 펑션을 사용합니다. 펑션은 FP 의 가장 기초적인 단위입니다. 펑션은 가장 단순한 계산 뿐만 아니라 거의 모든 것에 사용됩니다. 무려 변수들 조차 펑션으로 대신합니다. FP 에서 변수란 수식에 대한 단순 별칭일 뿐입니다. 변수값은 수정될 수 없습니다. 모든 변수는 딱 한번만 초기화될 수 있습니다. 자바의 용어로 말하자면 모든 변수가 final 로 선언된다는 말입니다. FP 에는 final 이 아닌 변수는 없습니다.

    final int i = 5;
    final int j = i + 3;

Since every variable in FP is final two fairly interesting statements can be made. It does not make sense to always write the keyword final and it does not make sense to call variables, well... variables. We will now make two modifications to Java: every variable declared in our functional Java will be final by default, and we will refer to variables as symbols.

FP의 모든 변수가 final 이미 때문에 매우 인상적인 두 문장을 만들 수 있었습니다. 그러나 매번 final 키워드를 사용하는 것은 비상식적이고 이럴 때 변수를 변수라 부르는 것도 좀 이치에 맞질 안습니다. 그러니 당장 자바에 두 가지 수정을 가해보도록 하겠습니다. 이제부터 F 적 Java 에서는 모든 변수 선언이 기본적으로 final 이라 가정합니다. 그리고 변수는 심볼이라 부르도록 하겠습니다.

By now you are probably wondering how you could possibly write anything reasonably complicated in our newly created language. If every symbol is non-mutable we cannot change the state of anything! This isn't strictly true. When Alonzo was working on lambda calculus he wasn't interested in maintaining state over periods of time in order to modify it later. He was interested in performing operations on data (also commonly referred to as "calculating stuff"). However, it was proved that lambda calculus is equivalent to a Turing machine. It can do all the same things an imperative programming language can. How, then, can we achieve the same results?

이쯤되면 이 새로운 언어로 적당히 복잡한 프로그램을 만들 수나 있을지 의문스러울 겁니다. 모든 심볼이 불변이라면 우리는 어떠한 것의 상태도 변경할 수 없다는 말 아닌가 말입니다. 하지만 이 말이 꼭 맞는 말은 아닙니다. Alonzo 는 lambda calculus 를 연구하면서 추후 수정을 가하기 위해 어떤 상태를 유지하는 것에는 관심을 갖지 않았습니다. 그는 대신 데이타에 가하는 연산에 관심을 가졌습니다. 하지만, lambda calculus 가 Turing machine 과 등치라는 것은 증명이 되어 있습니다. Lambda calculus 는 절차적 프로그래밍 언어가 할 수 있는 모든 것들을 할 수 있습니다. 그렇다면 어떻게 동일한 결과를 얻을 수 있을까요?

It turns out that functional programs can keep state, except they don't use variables to do it. They use functions instead. The state is kept in function parameters, on the stack. If you want to keep state for a while and every now and then modify it, you write a recursive function. As an example, let's write a function that reverses a Java string. Remember, every variable we declare is final by default5.

F적 프로그램은 상태를 유지할 수 있습니다. 하지만 변수를 사용하진 않습니다. 대신 펑션을 사용합니다. 상태는 스택에 쌓이는 펑션 인자에 유지됩니다. 잠시동안 상태를 유지하면서 매 순간 수정하고 싶다면 재귀호출 펑션을 만들어야 합니다. 예를들어 자바 스트링을 뒤집는 펑션을 만들어 봅시다. 기억하세요. 우리가 선언하는 모든 변수는 기본적으로 final 이라는 것을.

    String reverse(String arg) {
        if(arg.length == 0) {
            return arg;
        }
        else {
            return reverse(arg.substring(1, arg.length)) + arg.substring(0, 1);
        }
    }

(역자주: i 라던가 buf 라던가 하는 로컬 변수가 없다는 것을 확인하세요. 모든 변수는 펑션 인자에만 정의됩니다.)

This function is slow because it repeatedly calls itself6. It's a memory hog because it repeatedly allocates objects. But it's functional in style. You may be interested why someone would want to program in this manner. Well, I was just about to tell you.

이 펑션은 반복해서 자신을 부르기 때문에 느립니다. 반복해서 오브젝트를 생성하기 때문에 메모리를 많이 소모합니다. 하지만 펑셔널 스타일입니다. 누군가가 왜 이런 식으로 프로그램하기를 원하는지 좀 의심스러울 수 있겠습니다. 곧 말씀드리도록 하지요.

첨언: 재귀호출은 불필요한 CPU 사이클을 필요로 하기 때문에 많은 FL 컴파일러들은 재귀호출을 while 스타일의 루프로 변환하는 능력이 있습니다. 이 개념을 tail recursion 이라고 합니다. 단 Java 에서는 Java 6 까지 tail recursion 옵티마이징이 불가능했습니다. Java 7 에서야 JVM 레벨에서 이에 대한 지원이 추가되었습니다.

http://en.wikipedia.org/wiki/Tail_recursion

[Functional Programming For The Rest Of Us](index.md)

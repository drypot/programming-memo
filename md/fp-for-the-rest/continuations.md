# Continuations / 리턴을 다시 생각하다

(이 꼭지에는 이상하게 중언부언이 많아서 번역에선 약간 줄였습니다.)

Continuations to programming are what Da Vinci Code is to human history: an amazing revelation of the greatest cover-up known to man. Well, may be not, but they're certainly revealing of deceit in the same sense as square roots of negative numbers.

다빈치 코드는 여태까지 알려진 가장 위대한 비밀의 극적인 발견입니다. 인간 역사에 다 빈치 코드가 있었다면, 프로그래밍에는 컨티뉴에이션이 있습니다. 아닌가요? 뭐 어쨌든, 음수에 제곱근을 가하는 경우처럼 컨티뉴에이션은 확실히 어떤 문제점을 밝히는 역할을 합니다.

When we learned about functions we only learned half truths based on a faulty assumption that functions must return their value to the original caller. In this sense continuations are a generalization of functions. A function must not necessarily return to its caller and may return to any part of the program. A "continuation" is a parameter we may choose to pass to our function that specifies where the function should return. The description may be more complicated than it sounds. Take a look at the following code:

우리는 펑션을 배울 때 진실의 절반만 배웠습니다. 펑션은 반드시 호출자에게 값을 리턴해야 한다는 잘못된 가정을 했기 때문입니다. 펑션은 호출자에게 돌아가지 않아도 됩니다. 프로그램의 어떤 부분으로도 점프할 수 있습니다. 이를 위해 펑션이 작업을 마치고 어디로 가야하는지 지정하는 것이 컨티뉴에이션입니다. 컨티뉴에이션은 펑션에 인자로 전달됩니다. 설명이 괜히 복잡해 진 것 같습니다. 다음 코드를 보겠습니다.

    int i = add(5, 10);
    int j = square(i);

The function add returns 15 to be assigned to i, the place where add was originally called. After that the value of i is used to call square. Note that a lazy compiler can't rearrange these lines of code because the second line depends on successful evaluation of the first.

펑션 add 는 15 를 리턴하고 이 15 는 `i` 에 대입됩니다. `add` 는 처음 호출된 곳으로 돌아왔습니다. `i` 는 이어서 `square` 를 호출하기위해 사용됩니다. 두 번째 줄이 첫 번째 줄의 평가에 의존하고 있기 때문에 지연형 컴파일러는 이 행들을 재배열할 수 없습니다.

We can rewrite this code block using Continuation Passing Style or CPS, where the function add doesn't return to the original caller but instead returns its result to square.

이 코드를 Continuation Passing Style 로 다음과 같이 고쳐쓸 수 있습니다. 펑션 `add` 는 처음 호출된 곳으로 돌아오지 않습니다. 대신 자신의 결과를 `square` 에 바로 넘깁니다. (역자주 : `add` 가 `square` 를 부르고, `square` 가 끝난 후, `square` 에서 `add` 로 리턴하고, `add` 에서 처음 호출지로 리턴하는 개념이 아닙니다. `add` 가 `square` 로 제어를 넘기는 순간 `add` 의 스텍 프레임이 사라지는 줄거리입니다. 처음으로 어떻게 돌어올까요? 돌아오지 않습니다.)

    int j = add(5, 10, square);

In this case add gets another parameter - a function that add must call with its result upon completion. In this case square is a continuation of add. In both cases j will equal 225.

`add` 는 실행이 끝났을 때 자신의 결과를 가지고 이어서 호출할 펑션을 인자로 받습니다. `square` 는 `add` 의 컨티뉴에이션이 됩니다.

Here lays the first trick to force a lazy language to evaluate two expressions in order. Consider the following (familiar) IO code:

지현형 언어에서 두 수식을 순서대로 평가하도록 강제하는 첫 번째 트릭을 보겠습니다.

    System.out.println("Please enter your name: ");
    System.in.readLine();

The two lines don't depend on each other and the compiler is free to rearrange them as it wishes. However, if we rewrite this code in CPS, there will be a dependency and the compiler will be forced to evaluate the two lines in order!

두 문장은 서로 의존적이지 않습니다. 컴파일러는 자유롭게 이들을 재배열할 수 있습니다. 하지만, CPS 식으로 다시 쓰면 의존성이 생겨서 컴파일러는 두 문장을 순서대로 평가해야 합니다.

    System.out.println("Please enter your name: ", System.in.readLine);

In this case println needs to call readLine with its result and return the result of readLine. This allows us to ensure that the two lines are executed in order and that readLine is evaluated at all (because the whole computation expects the last value as a result). In case of Java println returns void but if it were to return an abstract value (that readLine would accept), we'd solve our problem!

이 경우 `println` 은 자신의 결과 값으로 `readLine` 을 호출해야하고 `readLine` 의 결과를 리턴해야 합니다. 두 문장이 순서대로 실행된다는 것이 보장되고, 전체 계산에서 마지막 값이 결과로 요청되기 때문에 readLine 이 반드시 평가된다는 것도 보장됩니다. Java 의 경우 `println` 은 `void` 를 리턴하지만 `readLine` 이 받을 수 있는 어떤 추상적인 값을 리턴한다고 가정하면 코드 실행 순서를 결정할 수 없었던 문제를 해결했다고 볼 수 있습니다!

Of course chaining function calls like that will quickly become unreadable, but it isn't necessary. We could add syntactic sugar to the language that will allow us to simply type expressions in order, and the compiler would chain the calls for us automatically. We can now evaluate expressions in any order we wish without losing any of the benefits of FP (including the ability to reason about our programs mathematically)! If this is still confusing, remember that a function is just an instance of a class with one member. Rewrite above two lines so that println and readLine are instances of classes and everything will become clear.

물론 이런식으로 펑션을 연결하면 코드는 곧 읽기 불가능해질 것입니다. 하지만 꼭 그렇지도 않습니다. 언어에 문법적 기름칠을 좀 하면 간단히 순서대로 적은 수식을 컴파일러가 펑션 호출 연쇄로 자동 변환하게 할 수 있습니다. 이제 프로그램을 수학적으로 추론하는 등의 FP 잇점들을 희생하지 않고도 원하는 순서대로 수식을 평가할 수 있게 되었습니다.

I would now wrap up this section, except that we've only scratched the surface of continuations and their usefulness. We can write entire programs in CPS, where every function takes an extra continuation argument and passes the result to it. We can also convert any program to CPS simply by treating functions as special cases of continuations (functions that always return to their caller). This conversion is trivial to do automatically (in fact, many compilers do just that).

여기서 이 장을 마무리고 싶습니다만, 아직은 컨티뉴에이션과 그 유용함의 맛만 보았을 뿐입니다. 프로그램 전체를 CPS 방식으로 작성할 수 있습니다. 모든 펑션이 추가 컨티뉴에이션 인자를 받아서 결과를 컨티뉴에이션에 전달하도록 할 수 있습니다. 어떤 프로그램도 CPS 로 방식으로 변환할 수 있습니다. 펑션은 항상 호출자로 리턴하는 컨티뉴에이션의 특별한 경우라고 보면 됩니다. 이 변환은 간단해서 자동화가 가능하고, 실재로 많은 컴파일러들이 그렇게 하고 있습니다.

Once we convert a program to CPS it becomes clear that every instruction has some continuation, a function it will call with the result, which in a regular program would be a place it must return to. Let's pick any instruction from above code, say add(5, 10). In a program written in CPS style it's clear what add's continuation is - it's a function that add calls once it's done. But what is it in a non-CPS program? We could, of course, convert the program to CPS, but do we have to? It turns out that we don't.

프로그램을 CPS 방식으로 변환하면 어떤 명령어라도 컨티뉴에이션을 갖는다는 것이 명확해 집니다. 반복하지만 컨티뉴에이션은 실행 결과를 가지고 이어서 호출하는 펑션입니다. 인반적인 프로그램에서는 리턴할 곳이 컨티뉴에이션이 됩니다. 위에서 아무 코드나 뽑아보겠습니다. `add(5,10)` 를 볼까요? 프로그램이 CPS 방식으로 씌여진 경우 `add` 의 컨티뉴에이션이 무엇인지 명확했습니다. 인자로 넘간 `square` 였죠. CPS 가 아닌 코드에서 `add` 의 컨티뉴에이션은 무엇이었나요? 네, 명시적으로 나타나진 않았네요.

Look carefully at our CPS conversion. If you try to write a compiler for it and think about it long enough you'll realize that the CPS version needs no stack! No function ever "returns" in the traditional sense, it just calls another function with the result instead. We don't need to push function arguments on the stack with every call and then pop them back, we can simply store them in some block of memory and use a jump instruction instead. We'll never need the original arguments - they'll never be used again since no function ever returns!

CPS 변환을 잘 보세요. CPS 변환 컴파일러를 만든다고 가정하고, 충분히 생각해보면, CPS 버전은 스택이 필요없다는 것을 깨닫게 될 것입니다. 펑션들은 전통적인 방식으로 리턴하지 않습니다. 대신 자신의 실행이 끝나면 결과를 가지고 다른 펑션을 호출하기만 합니다. 펑션을 호출할 때마다 인자를 스택에 푸쉬하고 나중에 다시 팝할 필요가 없습니다. 단지 적당한 메모리 블럭에 인자를 저장하고 컨티뉴에이션으로의 점프 명령을 쓰면 됩니다. 처음 인자는 다시 필요하지 않습니다. 어떤 펑션도 리턴하지 않으므로 이전 인자들이 다시 사용될 일은 없습니다!

So, programs written in CPS style have no stack but have an extra argument with a function to call. Programs not written in CPS style have no argument with a function to call, but have the stack instead. What does the stack contain? Simply the arguments, and a pointer to memory where the function should return. Do you see a light bulb? The stack simply contains continuation information! The pointer to the return instruction in the stack is essentially the same thing as the function to call in CPS programs! If you wanted to find out what continuation for add(5, 10) is, you'd simply have to examine the stack at the point of its execution!

그래서, CPS 방식으로 작성된 프로그램은 스택을 가지지 않습니다. 다음에 호출할 펑션을 인자로 가집니다. CPS 방식으로 쓰여지지 않은 프로그램은 호출할 펑션인자를 가지지 않습니다. 대신 스택을 갖습니다. 스택에는 뭐가 쌓일까요? 네, 펑션 인자와 펑션이 리턴해야할 메모리의 포인터가 쌓입니다. 혹시 눈이 번쩍 뜨이십니까? 아, 스택은 컨티뉴에이션 정보를 가지고 있을 뿐이군요! 스택에 있는 리턴 포인터는 CPS 프로그램의 컨티뉴에이션 펑션과 근본적으로 같은 것입니다. 위에 질문으로 돌아가서 CPS 가 아닌 코드에서 `add` 의 컨티뉴에이션을 알고 싶다면, 실행시점의 스택을 조사하기만 하면 되는 것입니다!

So that was easy. A continuation and a pointer to the return instruction in the stack are really the same thing, only a continuation is passed explicitly, so that it doesn't need to be the same place where the function was called from. If you remember that a continuation is a function, and a function in our language is compiled to an instance of a class, you'll realize that a pointer to the return instruction in the stack and the continuation argument are really the same thing, since our function (just like an instance of a class) is simply a pointer. This means that at any given point in time in your program you can ask for a current continuation (which is simply the information on the stack).

대박쉽죠? 컨티뉴에이션과 스택에 있는 리턴 포인터는 사실상 같은 것입니다. 컨티뉴에이션은 명시적으로 전달될 뿐입니다. 명시적으로 쓸 수 있으니 펑션이 처음 호출된 곳과 같을 필요가 없는 것입니다. 원한다면 프로그램 실행중 언제라도 현재 컨티뉴에이션을 알 수 있습니다.

Ok, so we know what a current continuation is. What does it mean? When we get a current continuation and store it somewhere, we end up storing the current state of our program - freezing it in time. This is similar to an OS putting itself into hibernation.

자, 컨티뉴에이션이 무엇인지는 알았습니다. 이게 무슨 의미를 가질까요? 현재 컨티뉴에이션을 얻어서 어딘가에 저장한다면 프로그램의 현재 상태를 저장하게 된다고 말할 수 있습니다. 즉시 냉동보관 할 수 있다는 말이지요. OS 를 하이버네이션 모드로 전환하는 것과 비슷합니다.

A continuation object contains the information necessary to restart the program from the point where the continuation object was acquired. An operating system does this to your program all the time when it context switches between the threads. The only difference is that it keeps all the control.

컨티뉴에이션 오브젝트는 자신이 생성된 시점에서 프로그램을 재시작하는데 필요한 정보를 가지고 있습니다. OS 는 이와 비슷하게 스레드간 컨텍스트 스위칭이 발생할 때마다 프로그램의 현재 상태를 저장합니다. 차이점이 있다면 OS 는 모든 제어정보를 저장한다는 겁니다.

If you ask for a continuation object (in Scheme this is done by calling call-with-current-continuation function) you'll get an object that contains the current continuation - the stack (or in a CPS case the function to call next). You can store this object in a variable (or alternatively, on disk). When you choose to "restart" your program with this continuation object you will "transform" to the state of the program when you grabbed the continuation object. It's the same thing as switching back to a suspended thread or waking up an OS from hibernation, except you can do it again and again. When an OS wakes up, the hibernation information is destroyed. If it wasn't, you'd be able to wake up from the same point over and over again, almost like going back in time. You have that control with continuations!

컨티뉴에이션 오브젝트을 요청하면 현재 컨티뉴에이션을 가지고 있는 오브젝트를 얻게 됩니다. CPS 의 경우는 다음에 호출할 펑션이 되겠고 CPS 가 아니라면 스택이 됩니다. 이 오브젝트를 변수나 디스크에 저장할 수 있습니다. 컨티뉴에이션 오브젝트를 가지고 프로그램을 재시작 시키면 컨티뉴에이션 오브젝트를 만들었던 시점으로 프로그램의 상태를 바꿀 수 있습니다. 이것은 정지된 스레드로 스위칭해서 돌아오는 일이나 하이버네이션에서 OS 를 깨우는 일과 같은 것입니다. 컨티뉴에이션의 경우 이 작업을 반복해서 할 수 있다는 것이 다르긴 합니다. OS 는 일단 깨면 하이버네이션에 정보를 삭제합니다. 삭제하지 않는다면 같은 시점에서 반복해서 깰 수 있을 것입니다. 컨티뉴에이션에서는 이것을 제어할 수 있습니다.

In what situations are continuations useful? Usually when you're trying to simulate state in an application of inherently stateless nature to ease your life. A great application of continuations are web applications. Microsoft's ASP.NET goes to tremendous lengths to try and simulate state so that you can write your application with less hassle. If C# supported continuations half of ASP.NET's complexity would disappear - you'd simply store a continuation and restart it when a user makes the web request again. To a programmer of the web application there would be no interruption - the program would simply start from the next line! Continuations are an incredibly useful abstraction for some problems. Considering that many of the traditional fat clients are moving to the web, continuations will become more and more important in the future.

어떤 경우에 컨티뉴에이션이 유용할까요? 상태에 기반하지 않는 어플리케이션에서 상태를 시뮬레이션하는데 유용합니다.컨티뉴에이션 최고의 응용은 웹 어플리케이션입니다. Microsoft 의 ASP.NET 은 HTTP 프로그램을 쉽게 작성할 수 있도록 상태를 시뮬레이션합니다. 이를 위해 엄청나게 긴 코드를 만들었습니다. C# 에서 컨티뉴에이션이 제공되었다면 ASP.NET 의 복잡성은 절반이 되었을 겁니다. 컨티뉴에이션을 간단히 저장하고 사용자가 웹 요청을 보내면 재시작합니다. 웹 어플리케이션 프로그래머에게는 중단점이 보이지 않을 겁니다. 프로그램은 그냥 다음 줄에서 시작합니다. 특정 어플리케이션에 있어서 컨티뉴에이션은 믿을 수 없을 만큼 유용한 추상성을 제공합니다. 구식 대형 클라이언트들이 점점더 많이 웹으로 이동하고 있다는 것을 생각하면 컨티뉴에이션은 미래에 더 큰 중요성을 가질 것입니다. (역: Smalltalk Seaside 등 컨티뉴에이션을 사용하는 웹 프레임웍들은 위키피디아 Continuation 에서 찾아 보실 수 있습니다.)

[Functional Programming For The Rest Of Us](index.md)

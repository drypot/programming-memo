# Closures / 클로저, 묶음

So far we've discussed features in the context of "pure" functional languages - languages that are implementations of lambda calculus and don't include features that conflict with Church's formalism. However, many of the features of functional languages are useful outside of lambda calculus framework. While an implementation of an axiomatic system is useful because it allows thinking about programs in terms of mathematical expressions, it may or may not always be practical.

여기까진 순수 펑셔널 언어의 맥락에 있는 기능들을 논의해 보았습니다. 순수 펑셔널 언어란 lambda calculus 의 구현으로 Church 의 공식과 모순되는 기능들을 포함하지 않는 언어입니다. 하지만, 펑셔널 언어의 많은 기능들은 lambda calculus 프레임웍 밖에서도 꽤 유용합니다. 공리적 시스템을 구현하면 프로그램을 수학 용어를 써서 생각할 수 있기 때문에 한편 유용하지만, 이것이 항상 실용적인지는 단언하기가 어렵습니다.

Many languages choose to incorporate functional elements without strictly adhering to functional doctrine. Many such languages (like Common Lisp) don't require variables to be final - you can modify things in place. They also don't require functions to depend only on their arguments - functions are allowed to access state outside of their boundaries. But they do include functional features - like higher order functions.

많은 언어들은 펑셔널 원칙을 엄격히 고수하지 않으면서도 펑셔널 요소들을 포함하고 있습니다. (Common Lisp 같은) 많은 이런 언어들의 변수는 꼭 final 이지 않습니다. 사용중인 것들을 변경할 수 있습니다. 펑션이 인자에만 의존해야한다는 요구도 하지 않습니다. 펑션이 자신의 영역 밖에 있는 상태에 접근하는 것도 허용합니다. 그러면서 고차 펑션같은 펑셔널한 기능을 가지고 있습니다.

Passing functions around in impure languages is a little bit different than doing it in the confines of lambda calculus and requires support for an interesting feature often referred to as lexical closure. Let's take a look at some sample code. Remember, in this case variables aren't final and functions can refer to variables outside of their scope:

비순수 언어에서 펑션을 던지는 것은 lambda calculus 의 영역에서 펑션을 던지는 것과는 조금 다릅니다. 이때는 클로져 (lexical closure) 라 불리는 흥미로운 기능을 필요로 합니다. 간단한 예제를 보겠습니다. 이 예제에서는 변수들이 final 이 아니라는 것과 펑션이 영역 밖의 변수를 참조할 수 있다는 것을 기억하세요.

    Function makePowerFn(int power) {
        int powerFn(int base) {
        return pow(base, power);
        }

        return powerFn;
    }

    Function square = makePowerFn(2);
    square(3); // returns 9

The function make-power-fn returns a function that takes a single argument and raises it to a certain power. What happens when we try to evaluate square(3)? The variable power isn't anywhere in scope of powerFn because makePowerFn has returned and its stack is long gone. How can square work, then? The language must, somehow, store the value of power somewhere for square to work. What if we create another function, cube, that raises something to the third power? The runtime must now store two copies of power, one for each function we generated using make-power-fn. The phenomenon of storing these values is called a closure.

`makePowerFn` 펑션은 (인자 `base` 를 받아서 `base` 의 `power` 제곱을 구하는) 펑션을 리턴합니다. `square(3)` 의 값을 계산할 때 어떤 일들이 벌어질까요? `makePowerFn` 이 리턴한 `powerFn` 이 `square` 에 대입되었는데 이때 `square` 가 사용할 변수 `power` 는 이미 `square`, 즉 `powerFn` 펑션의 영역에 있지 않습니다. `makePowerFn` 은 리턴했고 `makePowerFn` 의 스텍 또한 날라갔기 때문입니다. `power` 가 날라갔는데 `square` 는 어떻게 작동할 수 있을까요? `square` 가 작동하려면 언어에서 `power` 값이 어딘가 저장되도록 해야합니다. `makePowerFn` 안에 어떤 값의 세제곱을 구하는 `cube` 펑션을 추가하면 어떻게 될까요? 런타임은 `makePowerFn` 안에서 생성한 펑션 각각을 위해 `power` 의 복제 두 개를 저장해야합니다. 이렇게 펑션 외부 변수를 자동 저장해서 펑션 생명 주기와 외부 변수 주기가 싱크되도록 만든 펑션 + 외부변수 묶음을 클로저라고 합니다. 즉, 외부 변수가 붙어있는 펑션이 클로져입니다. (원문이 부족해 보여 막 문장은 역자 생각으로 대신했습니다.)

Closures don't only store arguments of a host function. For example, a closure can look like this:

클로저는 호스트 펑션의 인자를 저장하는데만 사용되는 것이 아닙니다. 다음과 같이 보일 수도 있습니다.

    Function makeIncrementer() {
        int n = 0;

        int increment() {
            return ++n;
        }
        return increment;
    }

    Function inc1 = makeIncrementer();
    Function inc2 = makeIncrementer();

    inc1(); // returns 1;
    inc1(); // returns 2;
    inc1(); // returns 3;
    inc2(); // returns 1;
    inc2(); // returns 2;
    inc2(); // returns 3;

The runtime manages to store n, so incrementers can access it. Furthermore, it stores various copies, one for each incrementer, even though they're supposed to disappear when makeIncrementer returns. What does this code compile to? How do closures work behind the scenes? Fortunately, we have a back stage pass.

`makeIncrementer` 가 리턴하면 `n` 이 사라져 버릴 것 처럼 보이지만, 런타임이 `n` 을 저장하기 때문에 `increment` 펑션은 `n` 을 사용할 수 있습니다. 그리고, `n` 은 각 `increment` 펑션별로 여러벌 저장됩니다. 이 코드들은 도대체 어떤 코드로 컴파일 되는 것일까요? 클로저는 뒤에서 어떻게 움직이는 것일까요? 다행이도 우리는 무대뒤 입장권을 가지고 있습니다.

A little common sense goes a long way. The first observation is that local variables are no longer limited to simple scope rules and have an undefined lifetime. The obvious conclusion is that they're no longer stored on the stack - they must be stored on the heap instead8. A closure, then, is implemented just like a function we discussed earlier, except that it has an additional reference to the surrounding variables:

관찰해 보면 로컬 변수가 기본적인 영역 규칙에 제한받지 않고 생명주기가 고정되어있지 않다는 것입니다. 여기서 얻을 수 있는 결론은 이들이 더이상 스텍에 저장되지 않는다는 것입니다. 대신 힙에 저장되어야 합니다. 클로저는 이전 글에서 언급한 펑셔널 펑션과 비슷하게 구현됩니다. 차이점이 있다면 클로저는 외부 변수에 대한 레퍼런스를 추가로 갖습니다.

    class some_function_t {
        SymbolTable parentScope;

        // ...
    }

When a closure references a variable that's not in its local scope, it consults this reference for a parent scope. That's it! Closures bring functional and OO worlds closer together. Every time you create a class that holds some state and pass it to somewhere else, think of closures. A closure is just an object that creates "member variables" on the fly by grabbing them from the scope, so you don't have to!

클로저는 로컬 영역에 속하지 않은 변수를 참조할 때 부모 영역에 대한 레퍼런스를 참조합니다. 이게 다 입니다. 클로저는 펑셔널 세계와 OO 세계를 서로 가깝게 만듭니다. 어떤 상태를 갖는 오브젝트를 만들고 다른 곳으로 전달할 때마다 클로져를 떠올리십시오. 클로져는 영역의 변수들을 그때그때 낚아채서 멤버 변수로 만든 오브젝트입니다. 완전 자동이죠!

[Functional Programming For The Rest Of Us](index.md)

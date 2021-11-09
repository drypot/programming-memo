# Polymorphism

초안: 2021-11-09

OOP 의 다형성에 관한 간단한 설명.

## 일의 시작은 간단하다

Json 파일에서 데이터를 읽는 루틴을 만들었다고 가정하자.

    class JsonReader {
      readData() { ... }
    }

이 클래스를 프로젝트의 여러 곳에서 사용했다.

    reader = new JsonReader()
    processData(reader)
    ...

    function processData(JsonReader reader) {
      data = reader.readData()
    }

## 새로운 요구사항이 발생한다

어느날 요구 사항이 들어온다.
데이터를 Xml 파일에서도 읽어야한다고 한다.

이렇게 기존 기능에 다른 방식을 추가해야 할 일이 자주 발행한다.
이럴 때는 기존 클래스에서 인터페이스를 추출하고 이 인터페이스를 여러 클래스가 공유하도록 만든다.

작업 순서는 아래와 같다.

## 인터페이스 추출

일단 JsonReader 에서 인터페이스를 추출한다.
인터페이스를 자동으로 추출하는 기능은 모든 통합환경이 가지고 있다.

    interface Reader {
      readData()
    }

    class JsonReader extends Reader {
      readData() { ... }
    }

JsonReader 를 사용하던 코드들을 찾아 인터페이스를 사용하도록 바꾼다.

    function processData(Reader reader) {
      data = reader.readData()
    }

이렇게 바꾸어도 기존 코드는 그대로 잘 작동해야 한다.

    reader = new JsonReader()
    processData(reader)

## 새로운 기능 추가

새로운 요구사항을 처리할 클래스를 추가한다.

    class XmlReader extends Reader {
      readData() { ... }
    }

만든 코드가 잘 작동하는지 기존 코드에 연결해서 확인한다.

    reader = new XmlReader()
    processData(reader)

잘 작동할 것이다;

## 결론

이런 식으로 인터페이스, 제네릭, 오버로딩 등을 사용하면 기존 코드를 크게 수정하지 않고 가변적인 상황에 대처하기가 수월해진다. 이 개념을 폴리모피즘이라고 한다.

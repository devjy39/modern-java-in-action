# PART6

# 함수형 관점으로 생각하기

## 프로그램 유지보수

- 프로그램이 시스템의 구조를 이해하기 쉽게 클래스 계층으로 반영해야한다.
- 결합성과 응집성으로 평가할 수 있다.
    - 결합성 : 시스템의 각 부분의 상호 의존성
    - 응집성 : 시스템의 다양한 부분이 서로 어떤 관계를 갖는지
- 코드 크래시 디버깅 문제
    - 예상치 못한 변숫값 때문에 발생하는 실제 가장 많이 겪는 문제
    - 함수형 프로그래밍의 no side effect, immutability로 해결하는데 도움을 줄 수 있다.
    

## 공유된 가변데이터

- 변수가 예상치 못한 값을 갖는 이유는 여러 메서드에서 공유된 가변 데이터 구조를 읽고 갱신하기 때문이다.


# 함수형 프로그래밍의 특성

### no side effect

- 클래스의 상태를 바꾸지 않으며 return 문을 통해서만 자신의 결과를 반환하는 메서드를 순수 메서드 또는 side-effect free 메서드라고 부른다.

### immutability

- 인스턴스화 한 다음 객체의 상태를 바꿀 수 없는 객체
- 함수 동작에 영향을 받지 않는다. → side effect 없음, thread safe

### 프로그래밍으로 시스템을 구현하는 방식

- 명령형 프로그래밍
    - “어떻게” 동작 할 것인가에 집중하는 프로그래밍 형식 (할당, 조건, 분기, 루프 등)
- 선언형 프로그래밍
    - “무엇을” 에 집중하는 방식 → (stream) 내부 반복을 통해서 질의문의 구체적인 구현 방법은 라이브러리가 결정한다.
    - 질의문으로 문제 자체가 코드로 명확하게 드러난다.
    - 함수형 프로그래밍은 선언형 프로그래밍의 대표적인 방식

### 함수형 프로그래밍에서 함수란?

- 함수란 수학적인 함수와 같다.
- 0개 이상의 인수를 가지며, 한 개 이상의 결과를 반환하지만 부작용이 없어야한다.
- 자바에서는 수학적인 함수냐 아니냐가 메서드와 함수를 구분하는 핵심이된다.
- 함수나 메서드는 지역 변수만을 변경해야 함수형이라고 할 수 있고, 참조하는 객체가 있다면 불변 객체여야 한다.
- 어떤 예외도 일으키지 않아야 한다. → return 으로 결과를 반환할 수 없게 되기 때문 ( 인수를 전달해서 결과를 받는다는 블랙박스 모델이 깨진다고 주장하는 이들 존재) → Optional을 사용하면 이 조차 방지할 순 있음
- 모두 처리할 필욘 없고 함수형 프로그래밍과 순수 함수형의 장단점을 실용적으로 고려해서 짜면된다.

### 참조 투명성

- 같은 인수로 함수를 호출하면 항상 같은 결과를 반환하는 성질
- 캐싱을 통한 최적화가 가능함.
- 자바에서 같은 요소를 갖는 list반환하는 함수라도 다른 메모리 공간의 리스트를 반환하기 때문에 참조 투명 메서드가 아니라는 결론이 나온다.
    - 불변 객체를 사용해야 참조 투명성을 지킬 수 있다.
- 레퍼런스가 달라도 논리적으로 같은 객체라면 같다고 판단할 수 있다. equals()

### 자바에서 프로그래밍 형식

- 모든 것을 객체로 간주하고 프로그램이 객체의 필드를 갱신, 메서드 호출, 객체 갱신하는 익스트림 객체지향 방식
- 참조 투명성을 중시하는, 변화를 허용하지 않는 함수형 프로그래밍 형식 두 가지를 혼합해서 사용한다.

## 재귀와 반복

```java
반복 방식의 팩토리얼
static int factorialIterative(int n) { 
	int r = 1;
	for (int i = 1; i <= n; i++) { 
		r *= i;
	}
	return r; 
}
// 매 반복마다 r과 i가 갱신됨.

재귀 방식의 팩토리얼
static long factorialRecursive(long n) {
	return n == 1 ? 1 : n * factorialRecursive(n-1);
}
// 좀 더 수학적인 형식으로 해결
```

- 재귀코드는 반복 방식보다 비싸다. 호출 시 마다 호출 스택에 새 스택 프레임이 만들어지고 메모리 사용량이 증가하기 때문이다.
    - 큰 입력값이 들어오면 Stackoverflow 발생 가능성도 있다.

### 재귀 꼬리 호출 최적화 ( tail - call optimization )

```java
	// acc = 중간결과 연산 값, n은 depth	

   static long factorialHelper(long acc, long n) {
			return n == 1 ? acc : factorialHelper(acc * n, n-1);
	 }
```

- 이전 함수는 마지막 연산이 곱셈이었지만, 이번엔 재귀가 마지막 호출이므로 꼬리 재귀이다.
- 중간 결과를 각각 스택 프레임으로 저장해야 하는 일반 재귀와 달리 꼬리 재귀에선 컴파일러가 하나의 스택 프레임을 재활용할 가능성이 생긴다.
- 안타깝게도 자바에선 이 같은 최적화를 제공하지 않지만, 그래도 컴파일러 최적화 여지를 남겨둘 수 있는 꼬리 재귀 방식을 사용하는 것이 좋다.
    - 스칼라와, 그루비 등 최신 jvm 언어는 이와 같은 재귀를 반복으로 변환하는 최적화를 제공한다.
- Stream을 이용하면 반복을 스트림으로 대체할 수 있다.
    - 반복을 재귀로 바꾸면 좀 더 간결하고, 부작용이 없는 알고리즘을 만들 수 있다.
    

# 19장 함수형 프로그래밍 기법

### 일급 함수

- 마치 일반값처럼 사용할 수 있는 함수
    - 인수로 전달 가능
    - 함수 결과로 반환 받기 가능
    - 자료구조에 저장할 수 있음
- 자바에서 함수형 프로그래밍, 메서드 참조, 람다로 일급 함수를 지원한다.

### 고차원 함수

- 하나 이상의 함수를 인수로 받아서 함수를 결과로 반환하는 함수
- ex ) Comparator.comparing()
- 함수를 조합하는 기능을 콤비네이터(combinator) 라고 한다.

### 커링

- 함수를 모듈화하고 코드 재사용성에 도움을 주는 기법
- 두 인 수를 받는 함수를 한 개의 인수를 받는 함수로 대체하는 기법 - 최종 반환 값은 같음
    - 자바에서 함수를 반환하는 팩토리 메서드 방식으로 구현 가능

## 영속 자료구조의 사용

- 저장된 값이 다른 누군가에 의해 영향을 받지 않는 상태
- 함수형 프로그래밍에서는 참조 투명성을 지키기 위해 함수형 자료구조, 불변 자료구조를 사용해야만 한다.
- 함수의 반환값에 new 연산으로 새로운 객체를 반환한다면 순수 함수를 만들 수 있다.

# 20장 OOP와 FP의 조화

## 간단한 스칼라 언어 소개

- 자바와 스칼라는 객체지향과 함수형 프로그래밍 모두를 하나의 프로그래밍 언어로 수용
한다. 두 언어 모두 JVM에서 실행되며 넓은 의미에서 상호운용성을 갖는다.
- 스칼라는 자바처럼 리스트, 집합, 맵, 스트림, 옵션 등의 추상 컬렉션을 제공한다. 또한
튜플도 추가로 제공한다.
- 스칼라는 자바에 비해 풍부한 함수 관련 기능을 제공한다. 스칼라는 함수 형식, 지역 변수
에 접근할 수 있는 클로저, 내장 커링 형식 등을 지원한다.
- 스칼라의 클래스는 암묵적으로 생성자, 게터, 세터를 제공한다.
- 스칼라는 트레이트를 지원한다. 트레이트는 필드와 디폴트 메서드를 포함할 수 있는 인터
페이스다

# 21장 자바의 미래, 결론

## java 8 기능 리뷰

- java 8에 큰 변화가 생긴 이유
    - 함수형 프로그래밍을 쉽게 적용할 수 있도록 도와준다.
    - 하드웨어 발전에 따른 멀티코어 프로세서의 활용을 위해
    - 선언형으로 데이터를 처리하는 방식, 간결하게 데이터 컬렉션을 처리하려면 불변객체, 불변 컬렉션이 필요하다.

### 동작 파라미터화 ( 람다, 메서드 참조 )

- 함수의 파라미터로 조건을 간단하게 받도록 만어서 복잡한 코드를 구현하면서도 유지보수성을 더 좋게 만들었다.
- 전체적인 기능에서 꼭 필요한 개념은 아니지만 자바 8의 새로운 스트림 api에서는 람다를 효과적으로 사용하면서 자바의 핵심 기능으로 힘을 더해주고 있다.

### 스트림

- 자바 8 설계자는 기존의 컬렉션에 람다를 활용한 filter, map 등을 추가해서 DB SQL 같은 기능을 제공하는 비교적 쉬운 방법을 선택할 수 있었지만, 그렇게 하지 않고 완전히 새로운 Stream API를 만들었다.  **왜 그랬을까?**
- 컬렉션에 여러 연산을 적용한다면 각각 연산 개수 만큼 탐색해야한다.
- 하지만 스트림의 게으른 연산으로 한 번의 탐색으로 파이프라인의 모든 연산을 수행 가능하다.
- 큰 데이터 집합일수록 스트림의 데이터 처리 방식이 효율적이며, 또한 메모리 캐시 등의 관점에서도 탐색 횟수를 최소화하는 것이 아주 중요하다.
- 부작용 없는 연산으로 동시성처리 걱정 없이 멀티코어 CPU를 활용한 병렬 처리도 매우 중요한 점이다.

### CompletableFuture

- 자바5의 Future와 자바8의 CompletableFuture는 컬렉션과 스트림의 관계와 같다고 한다.
- CompletableFuture는 Future와 관련한 공통 디자인 패턴을 함수형 프로그래밍으로 간결하게 표현할 수 있도록 thenCompose, thenCombine, allOf 등을 제공하고 명령형에서 발생하는 불필요한 코드를 피할 수 있도록 하였다.

### Optional

- 프로그램을 쉽게 이해하고 문서화하는데 큰 도움을 준다.
- 제대로 사용하면 NullPointerException이 발생하지 않을 것이다.
- 스트림과 비슷하게 함수형으로 체인 메서드들을 제공해주며, 값을 내부적으로 검사하는 것과 외부적으로 검사하는 것은 시스템 라이브러리가 내부 반복을 하느냐 외부 반복을 하느냐와 같은 의미를 갖는다.
- 자바 9에선 Optional API에 stream(), or(), ifPresentOrElse() 등의 기능들을 더 제공해준다.

### Flow API

- 자바 9에선 리액티브 스트림과 리액티브 당김 기반 역압력 프로토콜을 표준화했다.
- 호환성을 높일 수 있도록 네 개의 인터페이스 Publisher, Subscriber, Subscription, Processor를 포함한다.

### Default method

- 인터페이스에 새로운 기능을 추가했을 때 기존의 인터페이스를 구현하는 클래스들이 새로 추가된 기능을 구현하지 않을 수 있게 되었다는 점에서 디폴트 메서드는 라이브러리 설계자에게 아주 훌륭한 도구다.

### 자바 9 모듈 시스템

- 자바 9의 핵심은 새 모듈 시스템이다.
- [module-info.java](http://module-info.java) 파일이 추가되면서 언어적으론 바뀐게 없지만, 아키텍처 관점에서 애플리케이션을 설계하고 구현하는 방식이 바뀌었고 하위 부분간의 경계와 상호작용 정의가 명확해졌다.
- 안타깝게도 자바9는 다른 버전에 비해 과거 호환성을 많이 해쳤지만, 적절한 모듈화를 얻으려면 어쩔 수 없다.
- 패키지간의 캡슐화 강화는 호환성 희생의 한가지 이유이다.
- 새로운 자바 모듈 시스템 덕분에 자바 런타임이 작은 부분으로 나눠질 수 있게 되었으므로 애플리케이션에서 필요한 부분만 사용할 수 있다.

### 모듈 시스템의 장점

- 안정적 설정 : 모듈 요구사항을 명시적으로 선언함으로 의존성 빠짐, 충돌, 순환 등의 문21장 - 결론 그리고 자바의 미래 643
제를 런타임이 아니라 빌드 과정에서 일찍 확인할 수 있다.
- 강한 캡슐화 : 자바 모듈 시스템은 특정 패키지만 노출한 다음 각 모듈에서 공개할 부분
과 내부 구현의 영역 접근을 분리할 수 있다.
- 보안성 개선 : 사용자가 모듈의 특정 부분을 사용할 수 없도록 함으로 해커가 보안 제어
를 뚫기가 어려워졌다.
- 성능 개선 : 클래스가 런타임이 로드된 다른 클래스를 참조하는 상황보다는 적은 수의 컴
포넌트를 참조할 때 최적화 기술이 더 효과를 발휘한다.
- 확장성 : 자바 모듈 시스템은 자바 SE 플랫폼을 작은 부분으로 나눔으로 실행중인 애플리
케이션에서 필요한 부분만 사용할 수 있다.

> 장기적으로는 유지보수라는 관점에서 모듈화에 투자하는 것이 결국은 바람직할 것이라 독자는 믿는다.
> 

## 자바 10 지역 변수형 추론 : var

- 형식이 생략되면 컴파일러가 생략된 형식을 추론한다.
- 한 개의 식별자로 구성된 형식에 형식 추론을 사용하면 다양한 장점이 생긴다.
- 우선 한 형식을다른 형식으로 교체할 때 편집 작업이 줄어든다. 하지만 형식의 크기가 커지면서 제네릭이 다른 제네릭 형식에 의해 파라미터화될 수 있다. 이런 상황에서는 형식 추론으로 가독성이 좋아질 수 있다.
- var 키워드를 사용하면 컴파일러가 할당문의 오른쪽 내용을 기초로 형식을 추론한다.

```java
var myMap = new HashMap<String, List<String>>();

class Car extends Vehicle;

var x = new Car(); // 이 경우 Car일까? Vehicle일까?
변수의 형식은 초기화의 형식과 같다고 간주할 수 있다. 자바 10에서는 초깃값이 없을 때는 var를 사용할 수 없다.
정답은 당연히 Car이다.
```

- 지역 변수형 추론이라 부른다.

# 자바의 미래

- JDK 개선 제안JDK Enhancement Proposal 웹사이트인 [http://openjdk](http://openjdk/).[java.net/jeps/0](http://java.net/jeps/0) 에서 좋은 제안임에도 채택되지 못한 것들을 살펴보자.

### 패턴 매칭

- switch 문에 객체를 넣고 instanceof 연산자로 객체를 매핑해주는 기능은 함수형 프로그래밍 관점에서 좋지만, 전통적인 객체지향 설계자라면 오버라이드된 방문자 형식의 메서드로 구현하는게 더 좋다고 생각할 수 있어서 논쟁 주제이다.

## 제네릭

- 자바 5에서 기존 JVM과 호환성을 위해 List<String> 이나 List<Integer> 는 런타임 표현이 같게 되었다.
- 이를 제네릭 다형성의 삭제 모델이라고 한다. 이 때문에 약간의 런타임 비용을 지불하게 되었으며 제네릭에 객체만 사용할 수 있게 되었다.
- 예를들어 제네릭에 List<int> 를 지원한다면, List 컨테이너가 String같은 객체값을 포함하는 것인지 42 같은 기본형 int값을 포함하는 것인지 알 수 없게 된다.
- 이를 걱정해야 하는 건 **가비지컬렉션 때문이다.**
    - **런타임에 List의 요소가 Integer 참조(GC에서 사용중 으로 표시) 인지 int 인지 ( GC 수행 불가) 분간할 수 없기 때문이다.**

### 구체화된 제네릭

- 다형성 구체화 모델이라 부르는 구체화된 제네릭은 참조형과 기본형 변수 들을 모두 수용할 수 있다.
- 비슷한 문제로 메서드 반환 형식이 아무 값도 없는 것임을 가리키는 void와 값으로 null을 갖는
객체형 Void가 있다.
- Supplier<T> 같은 특별한 유형의 Function은 ( ) => T처럼 표현할 수 있는데 이때 기본형과 객체형의 차이가 더욱 두드러진다. 구체화된 제네릭으로 이런 문제를 해결 할 수 있다

## 발전하는 자바

- 자바 아키텍트는 과거 릴리스 정책으로는 빠르게 언어가 진화할 수 없으므로 더 이상 유지할 수 없음을 깨달았다. 실제 자바를 넘어서는 기능을 제공하는 스칼라, 코틀린 같은 JVM 언어가 등장한 이유도 이 때문이다.
- 이런 이유로 이제부터 자바는 6개월 개발 주기를 갖기로 결정했다.
- 자바 아키텍트는 이런 빠른 개발 주기가 언어 자체에도 이로울 뿐 아니라 새로운 기
술을 끊임없이 시험하는 빠르게 변화하는 회사와 개발자에게도 도움이 된다는 사실을 깨달았
다. 반면 느린 속도로 소프트웨어를 갱신하는 보수적인 회사라면 문제가 될 수 있다.
- 이런 이유로 자바 아키텍트는 **매 3년마다 이후 3년 동안 지원을 보장하는 장기 지원long-term support(LTS) 릴리스도 결정했다.**

# 기타 언어 업데이트 내용

## 어노테이션

- 자바 8의 어노테이션은 두 가지가 개선되었다.
    - 어노테이션을 반복할 수 있다.
    - 모든 형식에 어노테이션을 사용할 수 있다
- 자바의 어노테이션annotation은 부가 정보를 프로그램에 장식할 수 있는 기능이다(자바 8 이전
에는 선언에만 어노테이션을 사용할 수 있었다). 즉, 어노테이션은 문법적 메타데이터 syntactic
metadata다.

```java
Junit의 어노테이션 사용 예시

@Before
public void setUp() {
 this.list = new ArrayList<>();
}

@Test
public void testAlgorithm() {
 ...
 assertEquals(5, list.size());
}
```

- JUnit의 콘텍스트에서 어노테이션으로 설정 작업을 하는 메서드와 단위 테스트를 실행하
는 메서드를 구분할 수 있다.
- 문서화에 사용 :더 이상 사용하지 말아야 할 메서드에@Deprecated 어노테이션을 사용한다.
- 자바 컴파일러도 어노테이션을 사용해서 에러를 검출하고, 경고를 줄이고, 코드를 생성할
수 있다.
- 자바 EE에서 엔터프라이즈 애플리케이션을 설정할 때 어노테이션을 많이 사용한다.

### 어노테이션 반복

```java
@Authors(
 { @Author(name="Raoul"), @Author(name="Mario") , @Author(name="Alan") }
)
class Book{}
```

1. 어노테이션을 @Repeatable로 표시
2. 컨테이너 어노테이션 제공

```java
다음처럼 반복할 수 있는 @Author 어노테이션을 만들 수 있다.

@Repeatable(Authors.class)
@interface Author { String name(); }
@interface Authors {
	Author[] value();
}

// 그럼 Book 클래스에 여러 @Author 어노테이션을 사용할 수 있다.

@Author(name="Raoul") @Author(name="Mario") @Author(name="Alan")
class Book{ }

// Class 클래스는 반복된 어노테이션에 사용할 수 있는 getAnnotationsByType을 제공한다.

Author[] authors = Book.class.getAnnotationsByType(Author.class);
Arrays.asList(authors).forEach(a -> { System.out.println(a.name()); });

```

- 자바 8에서는 모든 형식에 어노테이션을 적용할 수 있다

```java
@NonNull String name = person.getName();
List<@NonNull Car> cars = new ArrayList<>();
```

### 제네릭의 대상 형식 추론

```java
List<Car> cars = Collections.<Car>emptyList();

// 기존의 제네릭 인수 추론 기능이 개선돼서 암시적으로 제네릭 인수를 추론할 수 있다.
List<Car> cars = Collections.emptyList();
```

# 라이브러리 업데이트

- Map
    - computeIfAbsent ⇒ key가 없다면 동작
- 스트림
    - removeIf  ⇒ 프레디케이트와 일치하는 모든 요소를 컬렉션에서 제거
- 리스트
    - replaceAll ⇒ 모든 요소에 인수 함수를 적용한 값으로 대체한다.

### 동시성

- java.util.concurrent.atomic 패키지는 AtomicInteger, AtomicLong 등 단일 변수에 아토믹
연산을 지원하는 숫자 클래스를 제공한다
- Adder와 Accumulator
    - 자바 API는 여러 스레드에서 읽기 동작보다 갱신 동작을 많이 수행하는 상황이라면 Atomic 클래스 대신 LongAdder, LongAccumulator, DoubleAdder, DoubleAccumulator를 사용하라고 권고한다

- ConcurrentHashMap
    - 키가 같은 해시코드를 반환하는 상황에서는 O(n ) 성능의 리스트로 버킷을 구현해야 하므로 성능이 나빠진다.
    - 자바 8에서 버킷이 너무 커지면 동적으로 리스트를 정렬 트리로(O(long(n ) )의 성능으로) 교체한다. 키가 Comparable일때만 이 기능을 사용할 수 있다.
    - mappingCount ⇒ 맵의 매핑개수를 long으로 반환

## Arrays

- parallelSort ⇒ 특정 배열을 병렬로 정렬하는 기능
- setAll ⇒ 지정된 배열의 모든 요소를 순차적으로 설정
parallelSetAll ⇒ 모든 요소를 병렬로 설정
- parallelPrefix ⇒ 제공된 이항 연산자를 이용해서 배열의 각 요소를 병렬로 누적하는
동작을 수행

### Number

- Short, Integer, Long, Float, Double 클래스에는 정적 메서드 sum, min, max가 추가되었
다.
- Integer와 Long 클래스에는 부호가 없는 값을 처리하는 compareUnsigned, divideUnsigned,
remainderUnsigned, toUnsignedString 등의 메서드가 추가되었다.
- Integer와 Long 클래스에는 문자열을 부호가 없는 int나 long으로 파싱하는 정적 메서
드 parseUnsignedInt와 parseUnsignedLong이 추가되었다.
- Byte와 Short 클래스는 인수를 비부호 int나 long으로 변환하는 toUnsignedInt와
toUnsignedLong 메서드를 제공한다. 마찬가지로 Integer 클래스에도 toUnsignedLong
정적 메서드가 추가되었다.
- Double과 Float 클래스에는 인수가 유한 소수점인지 검사하는 정적 메서드 isFinite가
추가되었다.
- Boolean 클래스에는 두 불리언값에 and, or, xor 연산을 적용하는 정적 메서드
logicalAnd, logicalOr, logicalXor이 추가되었다.
- BigInteger 클래스에는 BigInteger를 다양한 기본형으로 바꿀 수 있는 byteValueExact,
shortValueExact, intValueExact, longValueExact 메서드가 추가되었다. 그러나 변환
과정에서 정보 손실이 발생하면 산술 연산 예외가 발생한다

### Math

- 연산 결과에 오버플로가 발생했을 때 산술 예외를 발생시키는 addExact,
subtractExact, multiplyExact, incrementExact, decrementExact, negateExact 등의 메
서드가 추가됨.

## Files

- Files 클래스에는 파일에서 스트림을 만들 수 있는 기능이 추가되었다.
- Files.list
    - 주어진 디렉터리의 개체를 포함하는 Stream<Path>를 생성한다. 이 과정은 재귀가 아니다. 스트림은 게으르게 소비되므로 특히 큰 디렉터리를 처리할 때 유용한 메서드다.
- Files.walk
    - Files.list와 마찬가지로 주어진 디렉터리의 개체를 포함하는 Stream<Path>를 생성한다. 이 과정은 재귀적으로 실행되며 깊이 수준을 설정할 수 있다. 깊이 우선depth-first 방식으로 탐색을 수행한다.
- Files.find
    - 디렉터리를 재귀적으로 탐색하면서 주어진 프레디케이트와 일치하는 개체를 찾아서 Stream<Path>를 생성한다.
    

### String

- join ⇒ delimiter로 문자열을 연결할 수 있는 정적 메서드가 추가되었다.

## JVM과 람다

### 익명 클래스

- 컴파일러는 익명 클래스에 대응하는 새 클래스 파일을 생성한다.
- 익명 클래스는 ClassName$1 등의 파일명을 갖는데 여기서 ClassName은 익명 클래스를 포함하는 클래스의 이름이다
- 클래스 파일을 사용하려면 먼저 각각의 클래스를 로드하고 검증하는 과정이 필요하므로 애플리케이션 스타트업의 성능에 악영향을 미친다.
- 새로운 익명 클래스는 클래스나 인터페이스의 새로운 서브형식을 만든다. Comparator를
표현하는 수백 개의 람다가 있다면 결국 수백 가지의 Comparator 서브형식이 생긴다는 의
미다. 
이와 같은 상황에서는 JVM이 런타임 성능을 개선하기 어려울 수 있다

- 람다 표현식은 익명 내부 클래스와는 다른 방식으로 컴파일된다.

### invokedynamic

- JVM의 동적 형식 언어를 지원할 수 있도록 JDK7에 invokedynamic이라는 명령어가 추가되었다.
- invokedynamic은 메서드를 호출할 때 더 깊은 수준의 재전송과 동적 언어에 의존하는 로직이 대
상 호출을 결정할 수 있는 기능을 제공한다.
- 실제 호출할 메서드를 결정하는 언어 종속적 로직을 구현하는 부트스트랩 메서드의 형
태로 구성된다. 부트스트랩 메서드는 연결된 호출 사이트 call site를 반환한다.
- invokedynamic을 사용해서 람다 표현식을 구현하는 코드의 생성을 런타임으로 미룰 수 있다.
- 람다 표현식의 바디를 바이트코드로 변환하는 작업이 독립적으로 유지된다. 따라서 변환
작업이 동적으로 바뀌거나 나중에 JVM 구현에서 이를 더 최적화하거나 변환 작업을 고칠
수 있다. 변환 작업은 독립적이므로 바이트코드의 과거버전 호환성을 염려할 필요가 없다.
- 람다 덕분에 추가적인 필드나 정적 초기자 등의 오버헤드가 사라진다.
- 상태 없는(캡처하지 않는) 람다에서 람다 객체 인스턴스를 만들고, 캐시하고, 같은 결과
를 반환할 수 있다. 자바 8 이전에도 사람들은 이런 방식을 사용했다. 예를 들어 정적 final
변수에 특정 Comparator 인스턴스를 선언할 수 있다.
- 람다를 처음 실행할 때만 변환과 결과 연결 작업이 실행되므로 추가적인 성능 비용이 들지
않는다. 즉, 두 번째 호출부터는 이전 호출에서 연결된 구현을 바로 이용할 수 있다.

### 람다의 바이트코드 변환 과정

1. 가장 간단하게 변환할 수 있는 람다 상태를 포함하지 않는 람다 형식은 컴파일러는 람다 표현식과 같은 시그니처를 갖는 메서드를 생성한다
2. final(또는 final에 준하는) 지역 변수나 필드를 캡처하는 다음과 같은 람다 표현식의 변환 과
정은 생성된 메서드에 람다를 감싸는 콘텍스트의 추가 상태를 전달할 인수가 필요하므로
메서드의 시그니처와 람다 표현식이 시그니처와 일치하지 않는다. 
가장 간단한 해결 방법은 람다 표현식의 인수에 캡처한 각 변수를 추가하는 것이다.
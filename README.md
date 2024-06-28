# studysave

# Rust를 여행하는 히치하이커를 위한 안내서

## ㄱ. Promise와 Future의 개념 비교

강의록에 있는 js의 예시와 다른 점이 있다. Rust에는 Promise가 없다. Promise와 Future는 비동기 작업의 결과를 나중에 받을 수 있다는 점에서 개념적으로 비슷하지만, 구현 방식이나 사용법에 차이가 있다. 

### 1. JavaScript의 비동기 방식

- **Promise**:
	- 비동기 작업의 결과나 오류를 처리하기 위한 메커니즘.
    - JavaScript의 Promise는 비동기 작업을 다루기 위한 기본적인 방법이며, 함수가 호출되면 그 즉시 실행되면서 비동기 작업이 시작됨.
    - Promise는 생성됨과 동시에 스케줄링까지 자동으로 이루어져, `then`이나 `catch`를 통해 결과를 처리할 수 있음.
    - 상태가 `pending`에서 `fulfilled`(성공) 또는 `rejected`(실패)로 변경됨.

### 2. Rust의 비동기 방식

- **Future**:
	- 비동기 작업의 결과를 나타내는 타입.
    - Rust의 Future는 생성될 때 아무 작업도 즉시 실행되지 않음. 이는 단지 비동기 작업의 정의일 뿐, 실제로 실행되려면 Executor가 필요함.
    - Future 객체는 `poll` 메서드를 통해 상태를 확인할 수 있으며, 이 작업은 Executor에 의해 관리됨.
    - 상태가 `Poll::Pending`에서 `Poll::Ready`로 변경됨.
	- 직접 `poll`을 호출하는 대신, `async/await` 키워드를 사용해 비동기 작업을 더 쉽게 처리 가능.

- **Executor**:
    
    - Executor는 Future를 스케줄링하고, 실행하며, 완료될 때까지 반복적으로 `poll`을 호출함.
    - Rust에서 가장 널리 사용되는 Executor는 `tokio`이며, `async-std`도 자주 사용됨.

####  Future와 Executor의 상호 작용

- **Future 객체 생성**:
    - Future 객체를 생성하면, 이는 단순히 비동기 작업의 정의를 나타내는 것이고, 실제로 아무 작업도 실행되지 않음.
    - 예를 들어, `async fn`으로 정의된 함수는 호출 시 Future를 반환하지만, 이 Future는 실행되지 않음.

- **Executor의 역할**:
    - Executor는 Future를 실행하고, 스케줄링하며, `poll`을 반복적으로 호출하여 Future가 완료될 때까지 상태를 관리함.
    - `tokio::main` 매크로는 tokio Executor를 설정하여 비동기 작업을 관리하고 실행함.

### 3. 왜 이런 일이 일어났는가?

두 언어에서 비동기 작업을 다루는 방식이 본질적으로 다른 이유는 각각의 언어가 지닌 철학, 설계 목표, 주된 사용 사례, 그리고 비동기 프로그래밍 기능이 도입된 시기와 관련이 있음.

#### JavaScript

#### 개발자의 의도 및 언어 철학
- **언어 철학**: JavaScript는 웹 브라우저에서 동작하는 언어로 설계되었으며, 비동기 작업을 매우 쉽게 다루어야 했다. 이는 사용자 인터페이스(UI)가 끊기지 않고 반응성을 유지하는 것이 중요하기 때문이다.
- **주된 사용 사례**: 웹 개발, 특히 클라이언트-서버 간의 비동기 통신을 쉽게 처리하기 위해 고안되었다. 예를 들어, 사용자 입력을 처리하면서도 백그라운드에서 네트워크 요청을 수행해야 한다.
- **기능 추가 시기**: JavaScript는 원래 콜백 함수를 사용해 비동기 작업을 처리했지만, 콜백 지옥(callback hell) 문제를 해결하기 위해 2015년에 ES6 표준에서 Promise가 도입되었다. 이후 2017년에 ES8에서 async/await가 추가되면서 비동기 프로그래밍이 더욱 직관적이고 간편해졌다.

#### 실제로 사용되는 영역
- **웹 애플리케이션**: 비동기 네트워크 요청, UI 업데이트, 파일 읽기/쓰기 등에서 주로 사용된다.
- **서버사이드**: Node.js 환경에서 비동기 I/O 작업을 효율적으로 처리하는 데 사용된다.

#### Rust

#### 개발자의 의도 및 언어 철학
- **언어 철학**: Rust는 시스템 프로그래밍 언어로 설계되었으며, 안전성과 성능을 최우선으로 한다. Rust는 메모리 안전성을 보장하면서도 C++ 수준의 성능을 제공하는 것을 목표로 한다.
- **주된 사용 사례**: 시스템 소프트웨어, 네트워크 서비스, 고성능 애플리케이션 등에서 사용된다. 비동기 작업을 통해 높은 성능과 효율성을 유지할 수 있도록 설계되었다.
- **기능 추가 시기**: Rust의 비동기 프로그래밍 모델은 비교적 최근에 도입되었다. Rust 1.39 (2019년)에 async/await 기능이 안정화되면서 비동기 프로그래밍이 본격적으로 지원되었다.

#### 실제로 사용되는 영역
- **시스템 프로그래밍**: 비동기 I/O 작업, 네트워크 프로토콜 구현, 서버 애플리케이션 등에서 주로 사용된다.
- **고성능 애플리케이션**: 비동기 작업을 통해 높은 성능을 요구하는 애플리케이션에서 활용된다.

#### 본질적인 차이점 정리

1. **언어의 목적과 설계 목표**:
   - JavaScript는 주로 웹 개발을 위한 언어로, 비동기 작업을 쉽게 처리해 UI의 반응성을 유지하는 데 중점을 둔다.
   - Rust는 시스템 프로그래밍 언어로, 안전성과 성능을 극대화하기 위해 설계되었다. 비동기 작업을 통해 I/O 작업의 효율성을 높이는 데 중점을 둔다.

2. **비동기 작업의 실행 방식**:
   - JavaScript는 Promise가 생성됨과 동시에 비동기 작업이 자동으로 시작되고 스케줄링된다.
   - Rust는 Future가 생성될 때 아무 작업도 즉시 실행되지 않으며, Executor가 이를 실행하고 관리한다.

3. **비동기 프로그래밍 기능의 도입 시기**:
   - JavaScript는 ES6 (2015년)에 Promise를 도입하고, ES8 (2017년)에 async/await를 도입하여 비동기 프로그래밍을 더 간편하게 만들었다.
   - Rust는 2019년에 비동기 프로그래밍을 지원하기 시작했으며, 이를 통해 시스템 프로그래밍에서 비동기 작업을 효율적으로 처리할 수 있게 되었다.

#### 예시

#### JavaScript 예시

```javascript
// 비동기 작업을 다루는 Promise
function fetchData() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve("Data fetched");
        }, 1000);
    });
}

async function main() {
    const result = await fetchData();
    console.log(result);
}

main();
```

#### Rust 예시

```rust
// 비동기 작업을 다루는 Future와 Executor
use tokio::time::{sleep, Duration};

async fn fetch_data() -> &'static str {
    sleep(Duration::from_secs(1)).await;
    "Data fetched"
}

#[tokio::main]
async fn main() {
    let result = fetch_data().await;
    println!("{}", result);
}
```

#### 정리
- **JavaScript**: 비동기 작업을 쉽게 다룰 수 있도록 Promise와 async/await를 제공하며, 웹 개발에 최적화되어 있다.
- **Rust**: 안전성과 성능을 최우선으로 하여 Future와 Executor를 통해 비동기 작업을 처리하며, 시스템 프로그래밍에 최적화되어 있다.

---
## ㄴ. Rust의 비동기 작업은 어떻게 작동하는가

#### a) Future와 async/await의 개념
- **Future**: 저수준 비동기 작업을 표현하는 타입. 비동기 작업의 상태를 관리.
- **async/await**: 개발자의 편의를 위해 비동기 작업을 동기 코드처럼 작성할 수 있게 해주는 문법. Future를 반환하고, `await` 키워드로 Future가 완료될 때까지 기다림.

#### b) Future와 Executor
- **Future**: C++의 함수 정의와는 다소 다르지만, 비슷한 개념으로 비동기 작업을 정의함.
- **Executor**: Future를 실행하고 관리하는 역할. 비동기 작업의 상태를 계속해서 `poll`하여 완료 여부를 확인함.

#### c) async/await과 병렬 실행
- **async/await**: 비동기 작업을 동기 코드처럼 작성하는 문법. 단일 비동기 작업을 순차적으로 기다림.
- **join!**: 여러 비동기 작업을 병렬로 실행하여 모든 작업이 완료될 때까지 기다림.

#### c-1) 병렬 실행의 원리
- **병렬 실행**: 실제로는 CPU의 시간 분할(Time-Slicing) 및 멀티스레딩을 통해 여러 작업을 동시에 처리하는 것처럼 보이게 만듦. Rust의 Executor는 이를 효율적으로 관리하여 여러 비동기 작업을 병렬로 실행함.

#### 요약
1. **Promise와 Future**: 개념적으로 비슷하지만, 언어와 구현에 따라 사용법에 차이가 있음.
2. **Future와 async/await**: 비동기 작업을 표현하고 처리하는 방식. Future는 저수준, async/await은 고수준 문법.
3. **Executor**: Future를 실행하고 관리하는 런타임.
4. **병렬 실행**: `join!` 등을 사용하여 여러 비동기 작업을 병렬로 처리할 수 있음.


![[YourFile.png]]
Future 한 개가 어떻게 돌아가는가에 대해 정리한 그림. 이보다 명료할 수 없다..고생각

#### 추가 정보
- **Concurrency vs Parallelism**: 동시성과 병렬성은 다른 개념이다. 동시성은 여러 작업이 번갈아가며 실행되는 것이고, 병렬성은 실제로 여러 작업이 동시에 실행되는 것.
- **Rust의 특징**: Rust의 비동기 프로그래밍은 메모리 안전성과 성능을 중시함. Future와 Executor를 통해 효율적인 비동기 작업 처리가 가능함.

---
## ㄷ. 예시를 통해 살펴본 Future와 async/await 작동 방식
### 1. Future 방식

```rust
use std::future::Future;
use std::pin::Pin;
use std::task::{Context, Poll};

struct SimpleFuture {
    complete: bool,
}

impl Future for SimpleFuture {
    type Output = u32;

    fn poll(mut self: Pin<&mut Self>, _cx: &mut Context<'_>) -> Poll<Self::Output> {
        if self.complete {
            Poll::Ready(42)
        } else {
            self.complete = true;
            Poll::Pending
        }
    }
}

fn main() {
    let mut future = SimpleFuture { complete: false };
    let waker = std::task::noop_waker();
    let mut context = Context::from_waker(&waker);

    match Pin::new(&mut future).poll(&mut context) {
        Poll::Ready(value) => println!("Got: {}", value),
        Poll::Pending => println!("Not ready yet."),
    }
}
```

#### 코드 분석

#### 1. `SimpleFuture` 구조체 정의
```rust
struct SimpleFuture {
    complete: bool,
}
```
- `SimpleFuture`라는 구조체를 정의하고, `complete`라는 boolean 필드를 가짐. 이 필드는 비동기 작업이 완료되었는지 여부를 나타냄.

#### 2. `Future` 트레이트 구현
```rust
impl Future for SimpleFuture {
    type Output = u32;

    fn poll(mut self: Pin<&mut Self>, _cx: &mut Context<'_>) -> Poll<Self::Output> {
        if self.complete {
            Poll::Ready(42)
        } else {
            self.complete = true;
            Poll::Pending
        }
    }
}
```
- `SimpleFuture` 구조체에 대해 `Future` 트레이트^[객체에 특정한 메소드를 구현하도록 지정하는 방법] 를 구현함.
- `type Output = u32;`는 이 Future가 완료되면 `u32` 타입^[unsigned 32 bit]의 값을 반환함을 나타냄.
- `poll` 메서드는 Future의 상태를 체크함. `poll` 메서드는 두 가지 값을 반환할 수 있음:
  - `Poll::Ready(value)`: Future가 완료되어 결과 `value`를 반환함.
  - `Poll::Pending`: Future가 아직 완료되지 않았음을 나타내고, 나중에 다시 체크해야 함.
- 여기서는 `complete`가 `true`면 `Poll::Ready(42)`를 반환하고, `false`면 `Poll::Pending`을 반환하며 `complete`를 `true`로 설정함.
#### 3. `main` 함수
```rust
fn main() {
    let mut future = SimpleFuture { complete: false };
    let waker = std::task::noop_waker();
    let mut context = Context::from_waker(&waker);

    match Pin::new(&mut future).poll(&mut context) {
        Poll::Ready(value) => println!("Got: {}", value),
        Poll::Pending => println!("Not ready yet."),
    }
}
```
- `SimpleFuture` 인스턴스를 `complete: false`로 생성함.
- `waker`는 비동기 작업이 완료되었음을 알리는 데 사용됨. 여기서는 테스트용으로 아무 작업도 하지 않는 `noop_waker`를 사용함.
- `context`는 `waker`를 포함한 비동기 작업의 현재 상태를 나타냄.
- `Pin::new(&mut future).poll(&mut context)`는 Future의 상태를 체크함.
  - `Poll::Ready(value)`면 작업이 완료되었음을 나타내고 결과 값을 출력함.
  - `Poll::Pending`면 작업이 아직 완료되지 않았음을 나타내고 메시지를 출력함.

이 예시는 Rust의 Future가 어떻게 동작하는지 보여주기 위한 간단한 예제로, 실제로는 더 복잡한 비동기 작업을 처리할 때 더 유용하게 사용될 수 있다. Future와 poll을 직접 다루는 건 저수준의 작업이므로, 보통은 `async/await`를 사용하는 게 훨씬 간단하고 직관적이다.

### 2. async/await 방식

이제 Rust에서 비동기 프로그래밍을 더 쉽게 할 수 있도록 `async/await` 키워드를 사용해 보자.
Promise 스타일을 async/await 스타일로 변환할 때, 비동기 함수를 `async` 키워드로 정의하고 `await` 키워드를 사용하여 Future가 완료될 때까지 기다리도록 하면 된다. 위의 비동기 코드를 아래 동기 코드처럼 작성할 수 있다. 

```rust
use tokio::time::{sleep, Duration};

async fn do_something() -> u32 {
    sleep(Duration::from_secs(1)).await;
    42
}

#[tokio::main]
async fn main() {
    let result = do_something().await;
    println!("Got: {}", result);
}```

얼마나 간단한가?

여기서 `do_something` 함수는 `async` 키워드를 사용하여 비동기 함수를 정의했다. 이 함수는 1초 동안 한숨 자고 `42`를 반환한다. `await` 키워드를 사용하여 이 비동기 함수가 완료될 때까지 기다릴 수 있다. 

여기서 `async fn`은 비동기 함수로, Future를 반환하는 함수 구조체라고 생각할 수 있다. `await` 키워드는 비동기 작업이 완료될 때까지 현재 함수를 일시 중지하고, 완료된 결과를 반환한다. 이를 통해 비동기 작업을 동기 코드처럼 처리할 수 있게 해준다. 

잘 안읽힌다. 당신이 비동기적 rust 에스프레소바에 왔다고 생각해 보자. 

### 2-1. Rust 에스프레소바에서 커피를 내릴 때 사용하는 `await`

- **예시**: 원두를 가는 시간 20초, 에스프레소를 추출하는 시간 30초
- **비유**: 원두를 가는 동안 20초 동안 기다리고, 에스프레소를 추출하는 동안 30초 동안 기다리는 것처럼 각각의 작업을 기다리는 시간을 나타냄.
- **설명**: 함수 정의 내에서의 `await`는 해당 작업이 완료될 때까지 그 부분에서 일시 중지하고 기다린다는 의미이다.

```rust
async fn prepare_espresso() -> String {
    grind_beans().await; // 원두를 가는 시간 20초를 기다림
    brew_espresso().await; // 에스프레소를 추출하는 시간 30초를 기다림
    "Espresso is ready".to_string()
}
```

#### main 함수에서의 `await`

- **예시**: 커피 한 잔이 고객에게 갈 때까지 일련의 과정들을 기다리자고 선언한 것
- **비유**: 커피 한 잔을 만들기 위해 원두를 가는 것부터 에스프레소를 추출하고, 최종적으로 커피가 완성되어 고객에게 전달될 때까지 전체 과정을 기다리는 것.
- **설명**: main 함수에서의 `await`는 전체 과정이 완료될 때까지 기다린다는 의미이다. 이 때 각 과정은 비동기적으로 실행될 수 있다.

```rust
#[tokio::main]
async fn main() {
    let coffee = prepare_espresso().await; // 에스프레소 준비 과정 전체를 기다림
    println!("Got: {}", coffee); // 최종적으로 커피가 준비되면 출력
}
```

#### 두 부분에서 `await`의 미묘한 뉘앙스의 차이점

- **함수 정의 내에서의 `await`**: 각 비동기 작업이 완료될 때까지 기다린다는 의미로, 개별 작업 단위의 기다림을 나타냄.
  - 예시: 원두를 가는 시간 20초, 에스프레소를 추출하는 시간 30초
- **main 함수에서의 `await`**: 전체 비동기 작업의 일련의 과정이 완료될 때까지 기다린다는 의미로, 전체 프로세스의 기다림을 나타냄.
  - 예시: 커피 한 잔이 고객에게 갈 때까지 전체 과정을 기다림

그런데, 이러면 파이썬에서의 wait() 매서드와 별 다를게 없는거 아닌가? 비동기라는 개념은 어디에서 나온 것인가? 를 알아보기 위해, 에스프레소바에서 카페로 업종을 바꿔보자.
### 3. Rust 카페에서 라떼를 내리는 일련의 과정
#### 1. 비동기 함수 정의

- **원두 가는 작업 (20초)**: `grind_beans`
- **에스프레소 추출 작업 (30초)**: `brew_espresso`
- **컵에 얼음 담기 (10초)**: `fill_ice`
- **우유 준비 (15초)**: `prepare_milk`

#### 비동기 함수 구현

```rust
use tokio::time::{sleep, Duration};

async fn grind_beans() {
    sleep(Duration::from_secs(20)).await;
    println!("Beans are ground");
}

async fn brew_espresso() {
    sleep(Duration::from_secs(30)).await;
    println!("Espresso is brewed");
}

async fn fill_ice() {
    sleep(Duration::from_secs(10)).await;
    println!("Ice is filled in the cup");
}

async fn prepare_milk() {
    sleep(Duration::from_secs(15)).await;
    println!("Milk is prepared");
}
```

#### 2. 병렬 실행: `join!` 사용

`join!` 매크로를 사용하여 비동기 함수들을 병렬로 실행할 수 있다. 각 작업이 동시에 시작되어 개별 작업이 완료될 때까지 기다리게 된다.

```rust
#[tokio::main]
async fn main() {
    tokio::join!(
        grind_beans(),
        brew_espresso(),
        fill_ice(),
        prepare_milk()
    );

    println!("All tasks are completed. Ready to serve the coffee!");
}
```

#### 설명

1. **각 비동기 함수 정의**:
   - `grind_beans`: 원두를 가는 작업을 나타내며, 20초 동안 기다린 후 완료됨.
   - `brew_espresso`: 에스프레소를 추출하는 작업을 나타내며, 30초 동안 기다린 후 완료됨.
   - `fill_ice`: 컵에 얼음을 담는 작업을 나타내며, 10초 동안 기다린 후 완료됨.
   - `prepare_milk`: 우유를 준비하는 작업을 나타내며, 15초 동안 기다린 후 완료됨.

2. **병렬 실행 (`join!`)**:
   - `tokio::join!` 매크로는 모든 비동기 작업을 병렬로 실행하고, 모든 작업이 완료될 때까지 기다림.
   - 각 작업은 동시에 시작되고, 서로 다른 작업이 동시에 진행되므로 전체 작업 시간이 단축됨.

#### 실제 실행 예시

```text
Beans are ground
Ice is filled in the cup
Milk is prepared
Espresso is brewed
All tasks are completed. Ready to serve the coffee!
```

- 모든 작업이 병렬로 진행되어 가장 시간이 오래 걸리는 작업(에스프레소 추출, 30초)이 완료될 때까지 기다리게 됨.
- 각 작업이 완료될 때마다 메시지가 출력되며, 모든 작업이 완료되면 최종 메시지가 출력됨.

### 3-1. 추가 예시: 더 복잡한 시나리오

이제, 상황에 인과를 부여하여 조금 더 들여다 보자. 만약, 에스프레소 추출 후 우유를 추가하는 작업이 필요하다면, 이러한 작업을 순차적으로 연결할 수 있다.

```rust
use tokio::time::{sleep, Duration};

async fn grind_beans() {
    sleep(Duration::from_secs(20)).await;
    println!("Beans are ground");
}

async fn brew_espresso() {
    sleep(Duration::from_secs(30)).await;
    println!("Espresso is brewed");
}

async fn fill_ice() {
    sleep(Duration::from_secs(10)).await;
    println!("Ice is filled in the cup");
}

async fn prepare_milk() {
    sleep(Duration::from_secs(15)).await;
    println!("Milk is prepared");
}

async fn add_milk_to_espresso() {
    println!("Adding milk to espresso...");
    sleep(Duration::from_secs(5)).await;
    println!("Milk added to espresso");
}

#[tokio::main]
async fn main() {
    // 병렬 작업
    let (espresso, _, _) = tokio::join!(
        async {
            grind_beans().await;
            brew_espresso().await;
            "Espresso"
        },
        fill_ice(),
        prepare_milk()
    );

    // 우유 추가 작업
    add_milk_to_espresso().await;

    println!("{} with milk is ready to serve!", espresso);
}
```

이 예시에서는 `grind_beans`와 `brew_espresso` 작업이 병렬로 진행되며, `prepare_milk`와 `fill_ice`도 병렬로 진행된다. 그래프로 timestamp를 나타내면 다음과 같다.

![[output.png]]

모든 준비가 완료된 후에는 `add_milk_to_espresso` 작업을 수행해 최종 음료를 준비한다. 만약, 이 모든 과정을 Future으로 구현한다면, 무슨 짓을 해야 할까?

### 4. 라떼를 만드는 일련의 과정을 Future로 구현하기

Rust에서 직접 Future를 구현하는 것은 다소 복잡할 수 있지만, 이를 통해 Future의 작동 방식을 이해할 수 있다.

#### 1. 필요한 모듈과 구조체 정의

```rust
use std::pin::Pin;
use std::task::{Context, Poll, Waker};
use std::future::Future;
use std::time::{Duration, Instant};
use tokio::time::sleep;

// TimerFuture 구조체 정의
struct TimerFuture {
    when: Instant,
}

impl TimerFuture {
    fn new(duration: Duration) -> Self {
        TimerFuture {
            when: Instant::now() + duration,
        }
    }
}

impl Future for TimerFuture {
    type Output = ();

    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output> {
        if Instant::now() >= self.when {
            Poll::Ready(())
        } else {
            let waker = cx.waker().clone();
            let when = self.when;
            tokio::spawn(async move {
                let now = Instant::now();
                if now < when {
                    sleep(when - now).await;
                }
                waker.wake();
            });
            Poll::Pending
        }
    }
}
```

#### 2. 각 작업을 Future로 정의

```rust
struct GrindBeans {
    timer: TimerFuture,
}

impl GrindBeans {
    fn new() -> Self {
        GrindBeans {
            timer: TimerFuture::new(Duration::from_secs(20)),
        }
    }
}

impl Future for GrindBeans {
    type Output = ();

    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output> {
        self.timer.poll(cx)
    }
}

struct BrewEspresso {
    timer: TimerFuture,
}

impl BrewEspresso {
    fn new() -> Self {
        BrewEspresso {
            timer: TimerFuture::new(Duration::from_secs(30)),
        }
    }
}

impl Future for BrewEspresso {
    type Output = ();

    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output> {
        self.timer.poll(cx)
    }
}

struct FillIce {
    timer: TimerFuture,
}

impl FillIce {
    fn new() -> Self {
        FillIce {
            timer: TimerFuture::new(Duration::from_secs(10)),
        }
    }
}

impl Future for FillIce {
    type Output = ();

    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output> {
        self.timer.poll(cx)
    }
}

struct PrepareMilk {
    timer: TimerFuture,
}

impl PrepareMilk {
    fn new() -> Self {
        PrepareMilk {
            timer: TimerFuture::new(Duration::from_secs(15)),
        }
    }
}

impl Future for PrepareMilk {
    type Output = ();

    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output> {
        self.timer.poll(cx)
    }
}

struct AddMilkToEspresso {
    timer: TimerFuture,
}

impl AddMilkToEspresso {
    fn new() -> Self {
        AddMilkToEspresso {
            timer: TimerFuture::new(Duration::from_secs(5)),
        }
    }
}

impl Future for AddMilkToEspresso {
    type Output = ();

    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output> {
        self.timer.poll(cx)
    }
}
```

#### 3. 메인 함수에서 병렬로 작업 실행

```rust
#[tokio::main]
async fn main() {
    // 병렬 작업 실행
    let grind = GrindBeans::new();
    let fill_ice = FillIce::new();
    let prepare_milk = PrepareMilk::new();

    tokio::join!(grind, fill_ice, prepare_milk);

    // 에스프레소 추출 및 우유 추가 작업
    let brew = BrewEspresso::new();
    brew.await;

    let add_milk = AddMilkToEspresso::new();
    add_milk.await;

    println!("Latte is ready to serve!");
}
```

살펴본 바와 같이, Future 구조를 직접 정의하여 사용하는 것은 너무 어렵고 힘든 길이다. 마지막으로, 두 방식의 단점을 비교하는 것으로 마무리하자.

### 5. 두 방식의 단점 비교
#### Future 방식의 단점

- **이해하기 어려움**: Future를 직접 구현하는 것은 상당히 복잡하고, 특히 비동기 상태를 관리하기 위한 추가적인 코드가 필요하다.
- **보일러플레이트 코드**: 직접 Future를 구현하려면 많은 보일러플레이트 코드가 필요하여 코드가 길어지고 복잡해진다.
- **상태 관리 어려움**: 비동기 작업의 상태를 수동으로 관리해야 하기 때문에 코드가 복잡해지고 오류가 발생하기 쉽다.

#### async/await 방식의 단점

- **디버깅 어려움**: 비동기 코드에서 발생하는 오류는 동기 코드보다 디버깅이 어려울 수 있다. 특히, Future가 어디서 중단되고 재개되는지를 추적하기 어렵다.
- **런타임 오버헤드**: 각 비동기 작업이 Future로 래핑되고, Executor에 의해 스케줄링되기 때문에 약간의 런타임 오버헤드가 발생할 수 있다.
- **컴파일 타임 증가**: 비동기 코드는 컴파일 타임이 늘어날 수 있다. 특히, 큰 프로젝트에서는 이 문제가 더 두드러질 수 있다.

추가로, 만약 async fn에 대해 자세히 알고 싶으면 참고 파트를 참고하자.

---
## ㄹ. 보편적으로 쓰이는 Executor

### 1. async-std

Rust에서 사용되는 비동기 런타임 라이브러리. `async-std`는 표준 라이브러리 스타일의 비동기 API를 제공하고, 표준 라이브러리와 비슷한 인터페이스를 사용해 비동기 프로그래밍을 쉽게 할 수 있게 해준다.

#### a. 주요 구성 요소

1. **Runtime (런타임)**
2. **Task (작업)**
3. **비동기 I/O**
4. **타이머와 네트워크**

#### b. 구성 요소별 설명
##### 1. Runtime (런타임)

- **정의**: `async-std` 런타임은 비동기 작업을 관리하고 실행하는 환경을 제공함
- **역할**: 런타임은 비동기 작업을 스케줄링하고 실행한다
- **생성**: `async-std` 런타임은 `async-std::task::block_on` 함수나 `#[async_std::main]` 매크로를 통해 생성할 수 있다.

```rust
#[async_std::main]
async fn main() {
    println!("Hello from async-std runtime!");
}
```

- **분석**:
  - `#[async_std::main]`: 이 매크로는 `async-std` 런타임을 초기화하고, `main` 함수를 비동기 함수로 만들어 준다.
  - `async fn main()`: 비동기 함수 `main`을 정의해. 이 함수는 `async-std` 런타임 내에서 실행될 비동기 작업의 시작점이다.
  - `println!`: "Hello from async-std runtime!"을 출력. 이 줄은 단순히 출력 예제일 뿐이다.

##### 2. Task (작업)

- **정의**: Task는 비동기 작업을 나타내는 단위.
- **역할**: 비동기 함수의 실행 단위를 나타내며, 런타임에 의해 스케줄링된다.
- **생성 및 실행**: Task는 `async_std::task::spawn`을 통해 생성하고 실행할 수 있다.

```rust
use async_std::task;

#[async_std::main]
async fn main() {
    let handle = task::spawn(async {
        println!("Running a task");
    });

    handle.await;
}
```

- **분석**:
  - `use async_std::task;`: `async_std::task` 모듈을 가져온다. `#include`같은 느낌.
  - `task::spawn(async { ... });`: 비동기 작업을 스폰한다.^[비동기 작업을 `spawn`한다는 의미는, 새로운 비동기 작업을 백그라운드에서 실행하도록 스케줄링하고, 메인 프로그램의 흐름과 독립적으로 실행되도록 한다는 것을 의미한다.] 이 작업은 런타임에 의해 관리되고 실행된다.
  - `handle.await;`: 스폰된 작업이 완료될 때까지 기다리고, 작업이 완료되면 결과를 처리한다.

##### 3. 비동기 I/O

`async-std`는 비동기 I/O 작업을 표준 라이브러리와 비슷한 방식으로 제공한다. 예를 들어, 파일을 비동기적으로 읽거나 네트워크 요청을 처리할 수 있다다.

```rust
use async_std::fs::File;
use async_std::prelude::*;
use async_std::task;

#[async_std::main]
async fn main() -> std::io::Result<()> {
    let mut file = File::open("example.txt").await?;
    let mut contents = String::new();
    file.read_to_string(&mut contents).await?;
    println!("File contents: {}", contents);
    Ok(())
}
```

- **분석**:
  - `use async_std::fs::File;`: `async_std`의 파일 시스템 모듈에서 `File`을 가져온다. 이 모듈은 비동기 파일 I/O를 제공한다.
  - `File::open("example.txt").await?`: 파일을 비동기적으로 연다. 파일이 열릴 때까지 기다린 다음 파일 핸들을 반환한다.
  - `file.read_to_string(&mut contents).await?`: 파일 내용을 비동기적으로 읽고, 파일 읽기가 완료될 때까지 기다린 다음 내용을 문자열에 저장함.
  - `println!`: 파일 내용을 출력함.

##### 4. 타이머와 네트워크

타이머와 네트워크 작업도 비동기적으로 처리할 수 있다.

```rust
use async_std::task;
use async_std::net::TcpListener;
use async_std::prelude::*;
use std::time::Duration;

#[async_std::main]
async fn main() -> std::io::Result<()> {
    // 타이머 예제
    task::sleep(Duration::from_secs(2)).await;
    println!("2 seconds have passed");

    // 네트워크 예제
    let listener = TcpListener::bind("127.0.0.1:8080").await?;
    println!("Listening on port 8080");

    while let Some(stream) = listener.incoming().next().await {
        let stream = stream?;
        task::spawn(handle_connection(stream));
    }
    Ok(())
}

async fn handle_connection(mut stream: async_std::net::TcpStream) {
    let mut buffer = [0; 1024];
    while let Ok(n) = stream.read(&mut buffer).await {
        if n == 0 {
            break;
        }
        stream.write_all(&buffer[0..n]).await.unwrap();
    }
}
```

- **분석**:
  - `task::sleep(Duration::from_secs(2)).await;`: 2초 동안 비동기적으로 대기.
  - `TcpListener::bind("127.0.0.1:8080").await?`: 지정된 주소와 포트에 TCP 리스너를 비동기적으로 바인딩함.
  - `listener.incoming().next().await`: 새로운 TCP 연결을 비동기적으로 수락한다.
  - `task::spawn(handle_connection(stream));`: 새로운 연결을 처리하기 위해 비동기 작업을 스폰함.
  - `handle_connection` 함수는 비동기적으로 TCP 스트림을 읽고 씀.

### 2 Tokio
Tokio는 Rust에서 널리 사용되는 비동기 런타임 라이브러리로, 비동기 작업을 효율적으로 관리하고 실행하기 위한 다양한 기능을 제공한다.

#### a. 왜 이름이 Tokio인지

Tokio는 "Tokyo"의 변형된 이름으로, 일본의 수도 도쿄에서 유래된 것으로 보인다. 이름 자체가 기술적인 의미를 가지진 않지만, 프로젝트의 독특하고 기억하기 쉬운 이름을 위해 선택된 것으로 추정된다.

#### b. 누가 만들었는지

Tokio는 Rust 언어 커뮤니티에서 주로 사용되는 비동기 런타임 라이브러리로, 주로 Alex Crichton이 시작했다. Alex Crichton은 Rust 언어 팀의 일원으로, 비동기 프로그래밍 모델을 Rust에 도입하는 데 중요한 역할을 했다. 현재는 여러 개발자들이 참여하는 오픈 소스 프로젝트로, 커뮤니티의 기여로 유지 및 발전되고 있다.

#### c. 왜 보편적으로 쓰이는지

Tokio가 보편적으로 사용되는 이유는 여러 가지가 있다:

1. **성능**: Tokio는 매우 높은 성능을 제공하는 비동기 런타임이다. 이는 Rust의 메모리 안전성과 성능을 최대한 활용하도록 설계되었다.

2. **완전한 생태계**: Tokio는 비동기 프로그래밍을 위해 필요한 모든 도구와 라이브러리를 제공한다. 예를 들어, 네트워킹, 타이머, 파일 시스템 I/O 등을 비동기적으로 처리할 수 있는 다양한 모듈을 포함하고 있다.

3. **Rust 언어와의 밀접한 통합**: Tokio는 Rust의 비동기 프로그래밍 모델인 `async/await`와 완벽하게 통합되어 있다. 이를 통해 Rust에서 비동기 작업을 쉽게 작성하고 관리할 수 있다.

4. **강력한 커뮤니티 지원**: Rust 커뮤니티에서 활발히 사용되고 있으며, 많은 오픈 소스 프로젝트와 상용 소프트웨어에서 Tokio를 사용하고 있다. 이에 따라 다양한 자료와 예제 코드, 그리고 커뮤니티 지원을 받을 수 있다.

5. **유연성과 확장성**: Tokio는 다양한 사용 사례에 맞게 유연하게 사용할 수 있으며, 필요한 경우 확장할 수 있는 구조로 설계되어 있다.

#### d. 주요 구성 요소

1. **Runtime (런타임)**
2. **Executor (실행기)**
3. **Reactor (반응기)**
4. **Task (작업)**
5. **Scheduler (스케줄러)**

#### e. 구성 요소별 설명

##### 1. Runtime (런타임)
- **정의**: Tokio 런타임은 비동기 작업을 관리하고 실행하는 환경을 제공한다.
- **역할**: 런타임은 Executor와 Reactor를 포함하며, 비동기 작업을 스케줄링하고 실행한다.
- **생성**: Tokio 런타임은 `tokio::main` 매크로나 `tokio::runtime::Runtime` 객체를 통해 생성할 수 있다.

```rust
#[tokio::main]
async fn main() {
    println!("Hello from Tokio runtime!");
}
```

**분석**:

- `#[tokio::main]`: 이 매크로는 Tokio 런타임을 초기화하고, `main` 함수를 비동기 함수로 만들어준다.
- `async fn main()`: 비동기 함수 `main`을 정의한다. 이 함수는 Tokio 런타임 내에서 실행될 비동기 작업의 시작점이다.
- `println!`: "Hello from Tokio runtime!"을 출력함. 이 줄은 단순히 출력 예제일 뿐이다.

##### 2. Executor (실행기)
- **정의**: Executor는 Future를 실행하는 역할을 한다.
- **역할**: Future를 스케줄링하고 `poll` 메서드를 반복적으로 호출하여 Future가 완료될 때까지 상태를 관리한다.
- **작동 방식**: Executor는 작업을 큐에 넣고, 작업이 준비되면 실행한다.

```rust
use tokio::task;

#[tokio::main]
async fn main() {
    let handle = task::spawn(async {
        println!("Hello from a Tokio task!");
    });

    handle.await.unwrap();
}
```

**분석**:

- `use tokio::task;`: `tokio::task` 모듈을 가져온다.
- `task::spawn(async { ... });`: 비동기 작업을 스폰한다. 이 작업은 Executor에 의해 관리되고 실행된다.
- `handle.await.unwrap();`: 스폰된 작업이 완료될 때까지 기다리고, 작업이 완료되면 결과를 처리한다.
##### 3. Reactor (반응기)
- **정의**: Reactor는 I/O 이벤트를 감지하고 처리하는 역할을 한다.
- **역할**: 네트워크나 파일 시스템에서 발생하는 I/O 이벤트를 비동기적으로 처리한다.
- **작동 방식**: Reactor는 비동기 I/O 작업이 완료되었을 때 Executor에게 이를 알려준다.

```rust
use tokio::net::TcpListener;

#[tokio::main]
async fn main() {
    let listener = TcpListener::bind("127.0.0.1:8080").await.unwrap();
    println!("Listening on port 8080");

    loop {
        let (socket, _) = listener.accept().await.unwrap();
        tokio::spawn(async move {
            println!("Accepted connection");
        });
    }
}
```

**분석**:

- `use tokio::net::TcpListener;`: `tokio::net` 모듈에서 `TcpListener`를 가져온다. 
- `TcpListener::bind(...).await.unwrap();`: 지정된 주소와 포트에 TCP 리스너를 바인딩함. 비동기로 실행되어 리스너가 준비될 때까지 기다린다.
- `listener.accept().await.unwrap();`: 새로운 TCP 연결을 비동기적으로 수락한다. 연결이 수락되면 계속해서 루프를 돌며 새로운 연결을 기다린다.
- `tokio::spawn(async move { ... });`: 새로운 연결을 처리하기 위해 비동기 작업을 스폰한.
- 
##### 4. Task (작업)
- **정의**: Task는 비동기 작업을 나타내는 단위이다.
- **역할**: 비동기 함수의 실행 단위를 나타내며, Executor에 의해 스케줄링된다.
- **생성 및 실행**: Task는 `tokio::task::spawn`을 통해 생성하고 실행할 수 있다.

```rust
use tokio::task;

#[tokio::main]
async fn main() {
    let handle = task::spawn(async {
        println!("Running a task");
    });

    handle.await.unwrap();
}
```

**분석**:

- `task::spawn(async { ... });`: 비동기 작업을 생성하고 Executor에 의해 실행되도록 스폰한다.
- `handle.await.unwrap();`: 스폰된 작업이 완료될 때까지 기다리고, 작업이 완료되면 결과를 처리한다.

#### 5. Scheduler (스케줄러)
- **정의**: Scheduler는 Task를 효율적으로 스케줄링하는 역할을 한다.
- **역할**: 작업의 우선순위를 관리하고, 적절한 시점에 Task를 실행한다.
- **작동 방식**: Tokio는 여러 종류의 스케줄러를 제공하며, 작업의 성격에 따라 최적의 스케줄러를 선택해 사용한다.

Tokio 스케줄러는 Task의 우선순위를 조정하고, 효율적으로 작업을 관리한다. 일반적인 사용에서는 스케줄러를 직접 다룰 필요가 없지만, 성능 최적화를 위해 다양한 설정을 할 수 있다.

#### f. Tokio의 실행 방식 요약

1. **런타임 초기화**: `tokio::main` 매크로나 `Runtime` 객체를 통해 Tokio 런타임이 초기화된다.
2. **작업 스케줄링**: `tokio::task::spawn` 등을 사용해 비동기 작업이 스케줄링된다.
3. **이벤트 감지 및 처리**: Reactor가 I/O 이벤트를 감지하고 처리한다.
4. **작업 실행**: Executor가 스케줄링된 작업을 실행하며, Future의 상태를 지속적으로 관리한다.
5. **작업 완료**: Future가 완료되면, Executor는 결과를 반환하고, 필요한 후속 작업을 수행한다.

#### g. 네트워크 서버 예시를 통한 Tokio의 실행 과정

```rust
use tokio::net::TcpListener;

#[tokio::main]
async fn main() {
    // 1. 런타임 초기화
    let listener = TcpListener::bind("127.0.0.1:8080").await.unwrap();
    println!("Listening on port 8080");

    loop {
        // 2. 작업 스케줄링
        let (socket, _) = listener.accept().await.unwrap();
        tokio::spawn(async move {
            // 3. 작업 실행
            handle_connection(socket).await;
        });
    }
}

async fn handle_connection(socket: tokio::net::TcpStream) {
    // 비동기 작업 처리
    println!("Handling connection");
}
```

이 예제에서는 다음과 같은 과정이 이루어진다:
1. Tokio 런타임이 `tokio::main` 매크로를 통해 초기화된다.
2. `TcpListener`를 통해 네트워크 연결을 기다리며, 새로운 연결이 발생하면 `listener.accept()`가 Future를 반환하고 이를 기다린다.
3. 새로운 연결이 수락되면 `tokio::spawn`을 통해 비동기 작업이 스케줄링된다.
4. 스케줄링된 작업이 Executor에 의해 실행된다.
5. Reactor가 네트워크 I/O 이벤트를 감지하고 처리한다.

Tokio는 이러한 방식으로 효율적으로 비동기 작업을 관리하고 실행한다. 이를 통해 Rust 프로그램에서 높은 성능과 반응성을 유지할 수 있다.

### 3. Tokio와 async-std의 비교
#### async-std

**장점**:

1. **표준 라이브러리 스타일**: 표준 라이브러리와 유사한 인터페이스를 제공하여, Rust 표준 라이브러리를 사용해본 사람에게 익숙하다.
2. **사용 편의성**: 간단하고 직관적인 API를 제공하여 초보자도 쉽게 접근할 수 있다.
3. **경량**: Tokio에 비해 상대적으로 가벼운 런타임을 제공하여, 작은 애플리케이션에서 효율적이다.

**단점**:

1. **기능 제한**: Tokio에 비해 제공하는 기능이 제한적일 수 있으며, 복잡한 비동기 작업 관리에는 적합하지 않을 수 있다.
2. **성능**: 특정 고성능 요구사항에서는 Tokio에 비해 성능이 다소 떨어질 수 있다.

**사용 상황**:

- **간단한 비동기 애플리케이션**: 간단한 비동기 작업이 필요한 애플리케이션에서 사용된다.
- **경량 애플리케이션**: 작은 애플리케이션이나 빠른 프로토타이핑에 적합하다.
- **개인 프로젝트 및 학습**: 비동기 프로그래밍을 배우거나 간단한 프로젝트를 수행할 때 사용된다.
- 
#### Tokio

**장점**:

1. **고성능**: Tokio는 매우 높은 성능을 제공하는 비동기 런타임이다. 이는 고성능 네트워크 서버 및 애플리케이션에서 중요한 요소다.
2. **완전한 생태계**: Tokio는 비동기 프로그래밍을 위한 완전한 생태계를 제공한다. 네트워킹, 타이머, 파일 시스템 I/O 등을 포함한 다양한 모듈이 있다.
3. **확장성**: 다양한 고급 기능과 확장성을 제공하여, 복잡한 비동기 작업을 처리하는 데 적합하다.
4. **커뮤니티와 지원**: Rust 커뮤니티에서 가장 널리 사용되는 비동기 런타임으로, 많은 자료와 예제 코드가 존재한다.

**단점**:

1. **러닝 커브**: 고급 기능과 복잡한 API 때문에 초보자에게는 다소 어렵게 느껴질 수 있다.
2. **큰 바이너리**: 많은 기능을 포함하고 있어, 다른 경량 라이브러리와 비교하면 바이너리 크기가 클 수 있다.

**사용 상황**:

- **고성능 네트워크 서버**: 예를 들어, HTTP 서버나 웹소켓 서버와 같은 고성능 네트워크 애플리케이션에서 사용된다.
- **복잡한 비동기 작업**: 많은 비동기 작업을 관리하고 조율해야 하는 애플리케이션에서 적합하다.
- **장기 실행 서비스**: 안정적이고 효율적인 비동기 작업 관리가 필요한 서비스에서 사용된다.

---
## 참고

### `async fn`에 대한 설명

Rust에서 `async fn`은 비동기 함수로, 비동기 작업을 수행할 수 있는 함수 구조체라고 생각하면 된다. `async fn`을 사용하면 함수가 Future를 반환하고, `await` 키워드를 통해 이 Future가 완료될 때까지 기다릴 수 있다. 이 Future는 실행 중단점을 포함하고 있어, 비동기 작업이 완료될 때까지 다른 작업을 수행할 수 있도록 한다.

#### 1. `async`

- **역할**: 함수가 비동기적으로 실행될 수 있음을 나타낸다.
- **기능**: 함수가 Future를 반환하도록 만든다. 비동기 함수는 Future를 반환하며, 실제 실행은 나중에 Executor에 의해 이루어진다.

#### 2. `fn`

- **역할**: Rust에서 함수를 정의하는 키워드이다.
- **기능**: 함수의 본문을 정의하고, 인수와 반환 타입을 지정한다.

#### 비동기 함수의 반환 타입 정의

비동기 함수의 반환 타입은 항상 Future로, `async fn`의 반환 타입은 `impl Future<Output = T>` 형태로 정의된다. 여기서 `T`는 비동기 함수가 완료된 후 반환할 값의 타입이다.

##### 예시: 비동기 함수 정의와 반환 타입

```rust
use tokio::time::{sleep, Duration};
use std::future::Future;

// 비동기 함수 정의
async fn do_something() -> u32 {
    sleep(Duration::from_secs(1)).await;
    42
}

// Future를 반환하는 함수 정의
fn do_something_future() -> impl Future<Output = u32> {
    async {
        sleep(Duration::from_secs(1)).await;
        42
    }
}

#[tokio::main]
async fn main() {
    // 비동기 함수 호출 및 결과 대기
    let result = do_something().await;
    println!("Got: {}", result);

    // Future 반환 함수 호출 및 결과 대기
    let result_future = do_something_future().await;
    println!("Got from future: {}", result_future);
}
```

#### 비동기 함수의 반환 타입 설명

1. **비동기 함수 (`async fn`)**:
   - `async fn do_something() -> u32`: 이 함수는 비동기적으로 실행되며, Future를 반환한다. Future가 완료되면 `u32` 값을 반환한다.
   - Future의 구체적인 타입을 직접 명시할 필요가 없으며, Rust 컴파일러가 이를 자동으로 추론한다.

2. **Future 반환 함수 (`impl Future`)**:
   - `fn do_something_future() -> impl Future<Output = u32>`: 이 함수는 Future를 반환하는데, 해당 Future가 완료되면 `u32` 값을 반환한다.
   - `impl Future<Output = u32>`는 Future가 반환하는 값의 타입을 명시한다.

#### Future의 작동 방식

- 비동기 함수가 호출되면, 함수 본문이 실행되기 전에 Future 객체가 반환된다.
- Future 객체는 Executor에 의해 관리되며, `await` 키워드를 통해 특정 시점에서 Future가 완료될 때까지 기다린다.
- Future가 완료되면 결과 값이 반환되고, 다음 코드가 실행된다.

#### 요약

- `async fn`은 비동기 함수로, Future를 반환하는 함수 구조체라고 생각할 수 있다.
- `async` 키워드는 함수가 비동기적으로 실행됨을 나타내고, Future를 반환하도록 만든다.
- 비동기 함수의 반환 타입은 `impl Future<Output = T>` 형태로 정의되며, `T`는 함수가 완료된 후 반환할 값의 타입이다.
- 비동기 함수는 `await` 키워드를 통해 Future가 완료될 때까지 기다릴 수 있다.

### Waker는 또 무엇인가?

1. **Waker의 역할**:
   - Waker는 비동기 작업이 완료되었거나 진행 가능한 상태로 전환되었을 때, Executor에게 이를 알리는 역할을 한다.

2. **Waker와 Executor의 관계**:
   - Waker는 Future의 `poll` 메서드가 `Poll::Pending`을 반환했을 때, 나중에 다시 `poll`을 호출하도록 Executor에게 신호를 보낸다.
   - 이 신호는 Future가 어떤 이벤트(예: 타이머 만료, 네트워크 데이터 수신)로 인해 진행 가능해질 때 트리거된다.

3. **Waker와 시간 조건**:
   - Waker 자체가 시간 조건을 가지지는 않는다. 대신, Waker는 다른 이벤트(예: 타이머, 네트워크 이벤트)에 의해 트리거된다.
   - 예를 들어, `tokio::time::sleep` 같은 타이머가 2초 후에 Future를 깨우도록 설정되면, 이 타이머가 만료될 때 Waker를 호출하여 Executor가 Future를 다시 `poll`하도록 한다.

#### 예시 코드

```rust
use tokio::time::{sleep, Duration};
use std::future::Future;
use std::pin::Pin;
use std::task::{Context, Poll};

struct SimpleFuture {
    complete: bool,
}

impl Future for SimpleFuture {
    type Output = u32;

    fn poll(mut self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output> {
        if self.complete {
            Poll::Ready(42)
        } else {
            let waker = cx.waker().clone();
            tokio::spawn(async move {
                sleep(Duration::from_secs(2)).await;
                waker.wake();
            });
            self.complete = true;
            Poll::Pending
        }
    }
}

#[tokio::main]
async fn main() {
    let future = SimpleFuture { complete: false };
    let result = future.await;
    println!("Got: {}", result);
}
```

이 예제에서 `SimpleFuture`는 처음 `poll`될 때 2초 후에 Waker를 호출하도록 `tokio::spawn`을 사용하여 타이머를 설정한다. 2초 후 타이머가 만료되면 Waker가 `wake` 메서드를 호출하여 Executor에게 Future를 다시 `poll`하도록 신호를 보낸다. 그 결과, Future는 2초 후에 `Poll::Ready(42)`를 반환하게 된다.

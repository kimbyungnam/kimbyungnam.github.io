---

title: "The Rust Programming Language - 4"
subtitle: "6. Enums and Pattern Matching"
categories: dev
tags: Rust
---

# Defining an Enum  

## Enum Values

아래와 같이 Enum 타입을 선언 및 사용할 수 있다  
```Rust
enum IpAddrKind {
    V4,
    V6,
}

fn main() {
    let four = IpAddrKind::V4;
    let six = IpAddrKind::V6;

    route(IpAddrKind::V4);
    route(IpAddrKind::V6);
}

fn route(ip_kind: IpAddrKind) {}
```

```Rust
fn main() {
    enum IpAddr {
        V4(String),
        V6(String),
    }

    let home = IpAddr::V4(String::from("127.0.0.1"));

    let loopback = IpAddr::V6(String::from("::1"));
}
```

- Enum variant 에 데이터를 직접 넣어줄 수 있다  
- Variant 자체가 같은 타입을 가지는 생성자가 된다  

```Rust
fn main() {
    enum IpAddr {
        V4(u8, u8, u8, u8),
        V6(String),
    }

    let home = IpAddr::V4(127, 0, 0, 1);

    let loopback = IpAddr::V6(String::from("::1"));
}
```

- Enum 에도 method 를 정의할 수 있다  
```Rust
fn main() {
    enum Message {
        Quit,
        Move { x: i32, y: i32 },
        Write(String),
        ChangeColor(i32, i32, i32),
    }

    impl Message {
        fn call(&self) {
            // method body would be defined here
        }
    }

    let m = Message::Write(String::from("hello"));
    m.call();
}
```

## The Option Enum and Its Advantages Over Null Values  
* Rust 는 **null** 값이 없다  
- Option enum 은 기본적으로 포함되어 명시적으로 포함할 필요 없고 네임스페이스를 붙이지 않아도 된다  
```Rust
enum Option<T> {
    None,
    Some(T),
}
```
- invalid 한 값을 가질 가능성이 있는 변수에 타입을 Option 을 활용하여 항상 valid 할 것이라 여겨지는 타입과 다른 취급을 하여 null 과 연관된 오류를 방지  
> 값이 있을 것이라 여겼지만 사실 없는 문제  

# The **match** Control Flow Construct  

```Rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => {
            println!("Lucky penny!");
            1
        }
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}

fn main() {}
```

- **match** 는 pattern matching 을 활용하여 컴파일러가 모든 가능한 경우가 처리되도록 보장할 수 있다  
- 위에서 부터 순차적으로 pattern matching 을 수행해 처음으로 매칭된 코드 블락을 실행하게 된다  
- **if** 는 조건문에서 bool 타입을 반환해야 하지만 match 는 어떤 값이든 가능하다  

## Patterns that Bind to Values  

```Rust
#[derive(Debug)]
enum UsState {
    Alabama,
    Alaska,
    // --snip--
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        }
    }
}

fn main() {
    value_in_cents(Coin::Quarter(UsState::Alaska));
}
```
- Enum 에 연관된 데이터를 직접적으로 위와 같이 사용 가능  

## Matches Are Exhaustive  

모든 패턴에 대해서 arm 을 서술하지 않으면 컴파일 오류가 발생한다  

## Catch-all Patterns and the _ Placeholder  

```Rust
fn main() {
    let dice_roll = 9;
    match dice_roll {
        3 => add_fancy_hat(),
        7 => remove_fancy_hat(),
        other => move_player(other),
    }

    fn add_fancy_hat() {}
    fn remove_fancy_hat() {}
    fn move_player(num_spaces: u8) {}
}
```
- 위와 같이 변수를 사용해서 나머지에 대해서 같이 처리 가능하다  
> catch-all pattern  
- **_** 는 특별한 패턴으로 모든 값이랑 매칭되지만 값을 bind 하지 않는다  

# Concise Control Flow with if let  

```Rust
#[derive(Debug)]
enum UsState {
    Alabama,
    Alaska,
    // --snip--
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}

fn main() {
    let coin = Coin::Penny;
    let mut count = 0;
    if let Coin::Quarter(state) = coin {
        println!("State quarter from {:?}!", state);
    } else {
        count += 1;
    }
}
```

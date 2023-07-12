---

title: "The Rust Programming Language - 4"
subtitle: "6. Enums and Pattern Matching"
categories: dev
tags: Rust
---

> 해당 부분은 공식 문서를 읽는 것을 추천합니다  

# Packages and Crates  

- A **crate** is the smallest amount of code that the Rust compiler considers at a time  

- crate 는 두 가지 형태가 있다
    1. binary crate 는 실행파일로 컴파일하는 프로그램으로 main 함수가 반드시 있어야 한다  
    2. library crate 는 main 함수가 없으며 실행파일로 컴파일되지 않으며 여러 프로젝트에 공유될 목적으로 기능을 정의한다  

- crate root 는 컴파일러가 시작하는 소스 파일  

- **package** 는 한 개 이상의 crate 번들이다  

- package 는 **Cargo.toml** 파일을 가지며 crate 들을 어떻게 빌드할지 나타낸다  

- src/main.rs 는 binary crate 의 crate root 이다  
- src/lib.rs 는 library crate 의 create root 이다  
- src/bin 폴더에 binary crate 을 여러개 추가할 수 있다  

# Defining Modules to Control Scope and Privacy  

## Modules Cheat Sheet  

- **pub** keyword 로 기본적으로 module 안 코드는 노출되지 않지만 변경할 수 있다  
- **use** keyword 로 crate shortcut 을 사용해 긴 path 를 사용하지 않아도 된다  

# Paths for Referring to an Item in the Module Tree  

- path 는 두 가지 형태가 있다
    1. absolute path 로 crate root 부터 시작하는 완전 path 로 crate 로 시작한다  
    2. relative path 로 현재 모듈 부터 시작하며 self, super 또는 현재 모듈의 identifier 를 사용  

- **::** 로 분리된 identifier 사용  

- module 안에 함수, struct, enum 은 기본적으로 private  
- 동일 crate 내라면 path 를 이용해서 어디서나 참조 가능  
> 다만 private, public 영향은 그대로 받는다  

## Making Structs and Enums Public  
- struct 앞에 pub 을 붙이면 struc 은 사용 가능하지만 field 는 여전히 private 이다  
- enum 은 public 으로 만들면 모든 variant 도 public 이다  

# Brining Paths into Scope with the use Keywork  
```Rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use crate::front_of_house::hosting;

mod customer {
    pub fn eat_at_restaurant() {
        hosting::add_to_waitlist();
    }
}
```
- use 는 scope 에 symbolic link 를 생성하는 것과 같다. 따라서 scope 을 위와 같이 벗어나면 사용할 수 없어 컴파일 오류가 발생한다  

## Providing New Names with the as Keyword  
```Rust
use std::fmt::Result;
use std::io::Result as IoResult;

fn function1() -> Result {
    // --snip--
    Ok(())
}

fn function2() -> IoResult<()> {
    // --snip--
    Ok(())
}
```

## Re-exporting Names with pub use  
- use 키워드로 scope 에 가져온 이름은 기본적으로 private 이다  

## Using External Packages  
- **Cargo.toml** 에서 외부 패키지를 명시하면 사용할 수 있다  
> rand = "0.8.5"
```Rust
use std::io;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    println!("The secret number is: {secret_number}");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {guess}");
}
```

## Using Nested Paths to Clean Up Large use Lists  
```Rust
use std::cmp::Ordering;
use std::io;
```
```Rust
use rand::Rng;
use std::{cmp::Ordering, io};
```

- self 활용도 가능  
```Rust
use std::io;
use std::io::Write;
```
```Rust
use std::io::{self, Write};
```

## The Glob Operator  
모든 아이템을 가져오고 싶을 때는 glob operator 사용 가능  
```Rust
use std::collections::*;
```

# Separating Modules into Different Files  
- mod 선언은 다른 언어에서의 include 와는 성격이 다르다  
- mod 는 Module tree 에서 코드를 어디서 불러올 지 나타내는 것  

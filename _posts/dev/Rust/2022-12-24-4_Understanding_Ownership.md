---

title: "The Rust Programming Language - 2"
subtitle: "4.Understanding Ownership"
categories: dev
tags: Rust
---

# What is Ownership?    

## Ownership Rules  
- 각각의 값은 **owner** 가 있다  
- 동시에 오로지 하나의 owner 만이 있을 수 있다  
- owner 가 scope 에서 사라질 때 값은 없어진다  

## Variable Scope  
프로그램 내에서 아이템이 유효한 범위  
> A scope is the range within a program for which an item is valid  
```Rust
fn main() {
    {                      // s is not valid here, it’s not yet declared
        let s = "hello";   // s is valid from this point forward

        // do stuff with s
    }                      // this scope is now over, and s is no longer valid
}
```

## The String Type  
Chapter 3 에서 다룬 데이터 타입들은 Stack 에서 유지되고 길이가 compile 시간에 값이 정해지지만 String 타입은 Heap 에서 유지되고 길이를 변경할 수 있다  

## Memory and Allocation  
- GC 를 가진 언어에서는 GC 가 메모리를 계속 추적하고 정리
- GC 없는 다른 언어들은 보통 메모리 관리의 책임이 프로그래머에게 있고 이는 error-prone

Rust 에서는 변수의 **owner** 가 **scope** 를 벗어나게 되면 자동으로 메모리를 반환  
> **drop** 이라는 함수를 실행하여 메모리 반환  
> Resource Acquisition Is Initialization(RAII)  

## Variables and Data Interacting with Move  
```Rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1;

    println!("{}, world!", s1);
}
```
```console
$ cargo run
   Compiling ownership v0.1.0 (file:///projects/ownership)
error[E0382]: borrow of moved value: `s1`
 --> src/main.rs:5:28
  |
2 |     let s1 = String::from("hello");
  |         -- move occurs because `s1` has type `String`, which does not implement the `Copy` trait
3 |     let s2 = s1;
  |              -- value moved here
4 | 
5 |     println!("{}, world!", s1);
  |                            ^^ value borrowed here after move
  |
  = note: this error originates in the macro `$crate::format_args_nl` (in Nightly builds, run with -Z macro-backtrace for more info)

For more information about this error, try `rustc --explain E0382`.
error: could not compile `ownership` due to previous error
```

위 코드를 통해 s2 = s1 을 하면 s1 이 유효하지 않다는 것을 알 수 있다  
> Rust 에서 이러한 것을 **move** 라고 한다.

* move : heap 데이터를 복사하지 않고 가르키는 pointer 를 포함하는 stack 데이터만 복사하고 이전 변수는 invalid 로 처리  

- Rust 에서는 저절로 Deep copy 하지 않기 때문에 automtic copy 는 실행 성능 관점에서 영향이 크지 않다  

## Variables and Data Interacting with Clone  
Clone 은 Heap 데이터까지 복사하는 deep copy  
```Rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1.clone();

    println!("s1 = {}, s2 = {}", s1, s2);
}
```

## Stack-Only Data: Copy  
- 컴파일 시 알 수 있는 고정된 길이를 가져 완전히 stack 에 저장되는 타입들은 copy 되어 이전 변수가 여전히 valid  
```Rust
fn main() {
    let x = 5;
    let y = x;

    println!("x = {}, y = {}", x, y);
}
```

- Stack 에 저장되는 타입에 **Copy** trait 을 구현하면 move 대신 copy 를 한다  

- **Drop** trait 을 구현한 타입에는 copy trait 을 붙일 수 없다  

## Ownership and Functions  
- 함수로 매개 변수 전달도 할당과 마찬가지로 변수는 move 또는 copy 된다  
```Rust
fn main() {
    let s = String::from("hello");  // s comes into scope

    takes_ownership(s);             // s's value moves into the function...
                                    // ... and so is no longer valid here

    let x = 5;                      // x comes into scope

    makes_copy(x);                  // x would move into the function,
                                    // but i32 is Copy, so it's okay to still
                                    // use x afterward

} // Here, x goes out of scope, then s. But because s's value was moved, nothing
  // special happens.

fn takes_ownership(some_string: String) { // some_string comes into scope
    println!("{}", some_string);
} // Here, some_string goes out of scope and `drop` is called. The backing
  // memory is freed.

fn makes_copy(some_integer: i32) { // some_integer comes into scope
    println!("{}", some_integer);
} // Here, some_integer goes out of scope. Nothing special happens.
```

## Return Values and Scope  
```Rust
fn main() {
    let s1 = String::from("hello");

    let (s2, len) = calculate_length(s1);

    println!("The length of '{}' is {}.", s2, len);
}

fn calculate_length(s: String) -> (String, usize) {
    let length = s.len(); // len() returns the length of a String

    (s, length)
}
```
함수 매개 변수로 사용한 변수를 재사용하기 위해서는 함수에서 다시 반환해야 하는 불편함이 있을 때 **reference** 라는 것을 사용하여 ownership transfer 없이 값을 사용할 수 있다  

# References and Borrowing  
**Reference** 는 다른 변수가 owner 인 데이터의 주소를 가르키는 pointer 같은 것

- pointer 와 다르게 reference 는 유효한 데이터를 가르키는 것이 보장된다  

- ampersand & 는 reference 를 나타낸다

```Rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

- reference 는 함수가 끝나도 drop 되지 않는다. ownership 이 없기 때문에  

- reference 를 만드는 행위를 **borrowing** 이라 한다  

- 기본적으로 immutable 이기에 함수 내에서 값을 변경할 수 없다  

## Mutable References  
```Rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

- 한 값에 mutable reference 는 한 개만 가질 수 있다  
- mutable reference 에 이런 큰 제약을 두어서 컴파일 시간에 **data race** 를 방지할 수 있다  

- Data race 아래 3가지 조건에서 발생한다
    - 2 개 이상의 pointer 가 동시에 같은 데이터를 접근
    - 그 중 최소 한 pointer 가 데이터에 쓰기를 위해 사용됨
    - 데이터 접근을 동기화하는 메커니즘이 없음

- 한 값에 이미 immutable reference 가 있을 때는 mutable reference 를 가질 수 없다  
- 한 값에 여러 개의 immutable reference 는 가능  

* scope of reference 는 도입된 후 부터 마지막으로 사용될 때 까지로 아래 코드는 에러가 없다  
```Rust
fn main() {
    let mut s = String::from("hello");

    let r1 = &s; // no problem
    let r2 = &s; // no problem
    println!("{} and {}", r1, r2);
    // variables r1 and r2 will not be used after this point

    let r3 = &mut s; // no problem
    println!("{}", r3);
}
```

## Dangling References  
Rust 에서는 컴파일러가 dangling reference 가 생기지 않도록 강제합니다  
> reference 가 먼저 scope 에서 사라지기 전에는 컴파일러가 데이터가 사라지지 않는 것을 보장  

```Rust
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String { // dangle returns a reference to a String

    let s = String::from("hello"); // s is a new String

    &s // we return a reference to the String, s
} // Here, s goes out of scope, and is dropped. Its memory goes away.
  // Danger!

```
```console
$ cargo run
   Compiling ownership v0.1.0 (file:///projects/ownership)
error[E0106]: missing lifetime specifier
 --> src/main.rs:5:16
  |
5 | fn dangle() -> &String {
  |                ^ expected named lifetime parameter
  |
  = help: this function's return type contains a borrowed value, but there is no value for it to be borrowed from
help: consider using the `'static` lifetime
  |
5 | fn dangle() -> &'static String {
  |                ~~~~~~~~

For more information about this error, try `rustc --explain E0106`.
error: could not compile `ownership` due to previous error
```

## The Rules of References  
1. 하나의 mutable reference 를 가지거나 다수의 immutable reference 만을 가질 수 있다  
2. Reference 는 항상 유효하다  

# The Slice Type  
- Slice 는 reference 의 한 종류로 ownership 이 없다  

## String Slices  
```Rust
fn main() {
    let s = String::from("hello world");

    let hello_1 = &s[0..5];
    let hello_2 = &s[..5]; // hello_1 == hello_2
    let world_1 = &s[6..11];
    let wordl_2 = &s[6..]; // word_1 == word_2
    let hello_world = &s[..]; // hello_world == s
}
```

- &str 타입

- 내부적으로 slice 데이터 구조체는 시작 지점과 길이를 저장  

- String slice 는 UTF-8 경계에 유효하도록 range 가 구성되지 않으면 에러가 발생  

## String Literals as Slices  
```Rust
fn main() {
    let s = "Hello, world!";
}
```
String literal 은 바이너리에 저장되며 위의 코드에서 s 는 바이너리의 특정 지점을 가르키는 slice reference 가 된다

## String Slices as Parameters  
String 을 가지고 있을 떄 일부만 전달하고 싶을 때 slice 를 보내면 되고 만약 전부를 전달하고 싶을 때는 String reference 를 보내면 된다  
> 이러한 유연함은 **Deref coercions** 기능을 활용한다  

## Other Slices  
```Rust
fn main() {
    let a = [1, 2, 3, 4, 5];

    let slice = &a[1..3];

    assert_eq!(slice, &[2, 3]);
}
```
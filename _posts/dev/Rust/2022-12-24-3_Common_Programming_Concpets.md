---

title: "The Rust Programming Language - 1"
subtitle: "3.Common Programming Concepts"
categories: dev
tags: Rust
---

# Variables and Mutability  
- 변수는 기본적으로 불변(immutable)
- 변수 이름 앞에 **mut** 을 추가하여 변수를 가변(mutable)으로 선언 가능

## Constants
- mut 을 사용할 수 없음
- let 대신 **const** keyword 를 사용하여 선언
- 항상 type 이 정해져야 함
- 오로지 constant expression 으로만 설정 가능. runtime 계산 값은 불가능

## Shadowing  
- 같은 이름으로 변수를 선언하면 처음 선언된 변수는 shadow 되었다고 표현하며 컴파일러는 나중에 선언된 변수만 인식한다  
- scope 가 끝나거나 또 다른 선언으로 shadow 될 때 까지 사용된다
- Shadowing vs Mutable Variable
    1. immutable 로 선언된 변수의 특성은 변하지 않는다(새로운 변수를 만든거지 기존 변수에 값을 변경하는 것이 아님)
    2. 이름은 같지만 다른 타입의 변수를 만들 수 있다

# Data Types  
* Rust is Statically typed language : 모든 변수의 타입은 컴파일 시간에 정해져야 한다  
- Compiler 는 값과 사용하는 방식에서 타입을 식별할 수 있는 경우 자동으로 할당하지만 여러 타입이 가능한 경우에는 컴파일 오류를 뱉는다  

## Scalar Types
단일 값 타입 : Integers, Floating-point numbers, Booleans, Characters  

### Integer  
|Length|	Signed|	Unsigned|
| -- | -- | -- |
|8-bit|	i8|	u8|
|16-bit|	i16|	u16|
|32-bit|	i32|	u32|
|64-bit|	i64|	u64|
|128-bit|	i128|	u128|
|arch|	isize|	usize|
> Integer Types in Rust   

|Number literals|	Example|
| -- | -- |
|Decimal|	98_222|
|Hex|	0xff|
|Octal|	0o77|
|Binary|	0b1111_0000|
|Byte (u8 only)|	b'A'|
> Integers Literals in Rust  

* Default Integer Type : **i32**  

### Floating-Point Types  
* f32, f64; Default Types : f64  

### Boolean Type
* 1-byte in size  

### Character Type  
* 4-byte Unicode represented as **''**

## Compound Types  

### Tuple Type  
- 다양한 타입의 여러 값들을 하나로 그룹 지어주며 한 번 선언되면 사이즈는 변할 수 없다  
- parentheses () 와 , 을 사용하여 값을 나열  
```Rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```
- 개별 값을 얻기 위해서는 패턴 매칭을 사용할 수 있다  
```Rust
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of y is: {y}");
}
```
- period . 뒤에 인덱스를 사용하여 개별 값을 사용할 수 있다  
```Rust
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let five_hundred = x.0;

    let six_point_four = x.1;

    let one = x.2;
}
```

* unit : empty tuple 로 empty value 또는 empty return 타입을 나타내며 명시적으로 반환값이 없는 경우 표현식은 unit 을 반환  

### Array Type  
- 동일한 타입을 가진 여러 값을 그룹 지을 수 있다  
- 고정된 길이를 가진다  
- square bracket [] 과 , 을 이용하여 값을 나열
```Rust
fn main() {
    let a: [i32; 5] = [1, 2, 3, 4, 5];
    let a = [3; 5];//[3,3,3,3,3]
}
```
- 인덱싱을 통해 개별 값에 접근 가능. ex) a[1]
- index out of bounds 는 runtime error 를 일으킨다
> C/C++ 에서는 부적절한 메모리 접근을 할 수 있다

# Functions  
- **fn** keyword 를 사용하여 함수를 선언  
- 함수가 유효한 scope 안에 선언되어 있다면 순서는 상관 없이 사용할 수 있다  

## Parameters  
- 모든 매개 변수는 타입이 지정 해줘야 한다  

## Statements and Expressions  
- Statements 는 값을 반환하지 않는다
- Expression 은 결과 값을 계산(evaluate)한다  
- Expression 은 semicolon 으로 끝나지 않는다, semicolon 으로 끝나면 Statement 로 return 값이 없는 것이 된다  
```Rust
{
    let x = 3;
    x + 1
}
```

## Functions with Return Values  
이름은 없지만 arrow(->) 를 활용하여 매개 변수 이후 타입을 지정해야 한다  
```Rust
fn five() -> i32 {
    5
}
```
- 함수 바디의 마지막 expression 의 값이 반환 값이 된다
- **return** keyword 를 활용해서도 값을 반환할 수 있다  

# Comment  
> // hello, world  

# Control Flow  

## **if** Expressions  
```Rust
fn main() {
    let number = 3;

    if number < 5 {
        println!("condition was true");
    } else {
        println!("condition was false");
    }
}
```
- if 에서 사용되는 조건문은 무조건 bool 타입이여야 한다  
- if 는 Expression 이므로 let statement 에서도 사용 가능  
```Rust
fn main() {
    let condition = true;
    let number = if condition { 5 } else { 6 };

    println!("The value of number is: {number}");
}
```

## Repetition with Loops  
- **loop**, **while**, **for**  

### loop  
```Rust
fn main() {
    loop {
        println!("again!");
    }
}
```
- loop 은 Expression 으로 break keyword 를 활용하여 값을 반환할 수 있다  
```Rust
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    println!("The result is {result}");
}
```
- loop 안에 loop 가 있는 구조에서는 break 와 continue 는 가장 안쪽에 있는 loop 에 적용된다  
- loop label 을 활용하여 특정 loop 를 지정하여 break, continue 가능  
```Rust
fn main() {
    let mut count = 0;
    'counting_up: loop {
        println!("count = {count}");
        let mut remaining = 10;

        loop {
            println!("remaining = {remaining}");
            if remaining == 9 {
                break;
            }
            if count == 2 {
                break 'counting_up;
            }
            remaining -= 1;
        }

        count += 1;
    }
    println!("End count = {count}");
}
```

### while  
조건문이 true 이면 반복  

### for  
- while 로 array 의 값을 순환하는 반복문을 사용하게 되면 첫 번째로 index out of bounds 에러 발생 가능성이 있고 두 번째로 매번 인덱스 확인을 하기 때문에 성능이 느려진다 -> for 사용
```Rust
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a {
        println!("the value is: {element}");
    }
}
```

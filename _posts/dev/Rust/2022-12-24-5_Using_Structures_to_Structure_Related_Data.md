---

title: "The Rust Programming Language - 3"
subtitle: "5. Using Structs to Structure Related Data"
categories: dev
tags: Rust
---

# Defining and Instantiating Structs  
```Rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

fn main() {
    let mut user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };

    user1.email = String::from("anotheremail@example.com");
}
```

- structure 에서 특정 필드만 mutable 로 설정할 수 없어 모든 요소를 mutable 로 설정해야 값 변경이 가능  

## Using the Field Init Shorthand  
Field 의 이름과 같은 이름을 가진다면 따로 field 이름 서술 없이도 *field init shorthand* 문법 사용하여 instance 생성 가능  

```Rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
}
```

## Creating Instances From Other Instances With Struct Update Syntax  
*struct update* 문법을 활용하여 다른 instance 에서 새로운 instance 를 생성할 수 있다  
```Rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

fn main() {
    // --snip--

    let user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };

    let user2 = User {
        email: String::from("another@example.com"),
        ..user1
    };
}
```

- *struct update* 문법은 할당문 처럼 **=** 을 사용하기 때문에 **move** 가 발생한다  

- 하지만 재사용되는 모든 field 들이 move 없이 copy 된다면 이전 struct 변수는 move 가 발생하지 않아 여전히 유효하다  


## Using Tuple Structs without Named Fields to Create Different Types  

**tuple struct** 는 field 에 이름은 없지만 타입을 가지는 struct  

```Rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
}
```

- 위 코드에서 black, origin 은 다른 타입으로 tuple struct 가 모든 필드 타입이 같더라고 같이 취급할 수 없다  

## Unit-Like Structs Without Any Fields  
- **unit-like struct** : 어떤 field 도 가지지 않는 struct  
- 타입 자체에는 저장할 데이터가 없지만 trait 을 구현하고 싶을 떄 유용  

## Ownership of Struct Data  
Reference 를 struct field 에서 사용할 경우 **lifetime** 을 사용해야 한다  
```Rsut
struct User {
    active: bool,
    username: &str,
    email: &str,
    sign_in_count: u64,
}

fn main() {
    let user1 = User {
        email: "someone@example.com",
        username: "someusername123",
        active: true,
        sign_in_count: 1,
    };
}
```
```console
$ cargo run
   Compiling structs v0.1.0 (file:///projects/structs)
error[E0106]: missing lifetime specifier
 --> src/main.rs:3:15
  |
3 |     username: &str,
  |               ^ expected named lifetime parameter
  |
help: consider introducing a named lifetime parameter
  |
1 ~ struct User<'a> {
2 |     active: bool,
3 ~     username: &'a str,
  |

error[E0106]: missing lifetime specifier
 --> src/main.rs:4:12
  |
4 |     email: &str,
  |            ^ expected named lifetime parameter
  |
help: consider introducing a named lifetime parameter
  |
1 ~ struct User<'a> {
2 |     active: bool,
3 |     username: &str,
4 ~     email: &'a str,
  |

For more information about this error, try `rustc --explain E0106`.
error: could not compile `structs` due to 2 previous errors
```

# An Example Program Using Sturcts  

# Method Syntax  

## Defining Methods  
```Rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn width(&self) -> bool {
        self.width > 0
    }
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    if rect1.width() {
        println!("The rectangle has a nonzero width; it is {}", rect1.width);
    }
}
```
- **impl** 블록에 **method** 들을 정의 할 수 있다  
- 첫 번째 매개 변수는 항상 self 로 타입의 instance 가 전달되며 용도의 따라 &, &mut 또는 ownership 을 가져올 수 있게 선언할 수 있다  

- Rust 에서는 -> eotls *automatic referencing and dereferencing* 이란 기능을 활용해서 self 정의를 확인하여 알아서 알맞게 변형하여 method 를 호출 한다  
> p1.distance(&p2);  
> (&p1).distance(&p2);  

## Methods with More Parameters  

## Associated Functions  
- **imple** 블록에 정의된 모든 함수는 **associated function** 이라 한다  
- associated function 중 self 가 첫 번째 매개변수로 오지 않는(method 가 아닌) 함수를 정의할 수 있다
> 생성자로 많이 사용 **new**  

```Rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn square(size: u32) -> Self {
        Self {
            width: size,
            height: size,
        }
    }
}

fn main() {
    let sq = Rectangle::square(3);
}
```
위 코드에서 Self 는 impl 다음에 오는 키워드의 대체어로 동일하게 볼 수 있다  

- **::** 문법은 모듈의 namespace 와 associated function 에 모두 사용된다  

## Multiple imple Blocks
하나의 타입에 여러 개의 impl 블록을 두는 것이 가능하다  
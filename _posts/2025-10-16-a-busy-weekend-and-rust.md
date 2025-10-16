---
layout: post
title: "a busy weekend and rust!"
date: 2025-10-16
categories: 
---
## What I Built

this is less of a daily devlog, and more of a diary at this point.
after my previous iteration of our boids system, "I wish I kept videos aside from the couple I've downloaded, I'll make a comparison video on the rough performance of each language"
I went to a family reunion and hunting with my dad after that, during the reunion I learned the very basics of rust and got a development environment setup on my machine for it, along with getting godot-rust setup, I was and am determined to get to rewriting in rust.
during my studies of rust I've found a couple cool things.

Quick note on code examples, probably won't compile, because I am writing this in a markdown file without a compiler.

```rust
//closures
fn main() {
    let add_ten = make_adder(10);
    println!("{}", add_ten(5)); // should print 15
    println!("{}", add_ten(20)); // should print 30
}
fn make_adder(x: i32) -> impl Fn(i32) -> i32 {
    move |y| x + y
}
```

with closures we can create functions that create other functions! Not sure of the practical implementation quite yet of much of my knowledge, but I learned a couple more things such as 

```rust
//rust variables are immutable by default (readonly)
let x = 5;
// x = 10; // this would throw a compiler error!

//if you want mutable variables we use `mut`
let mut x = 5;
x = 10;
//the above compiles.

//this also compiles, but "shadows" the previous binding
let x = 5;
let x = 10;

//we can type our variables like so
let x: &str = "string";
let x: String = String::from("string");
//etc...

//rust works with something called the borrow checker, iirc the borrow checker only lets one scope "own" the data, and other scopes can "borrow" said data.
//small values on the stack implement a "trait" called Copy, to make this easier
let x = 5; // i32 implements Copy
my_func(x);
println!("{}", x); // this works!

// big values stored on the heap don't implement Copy and move ownership
let x = String::from("string");
my_func(x);
// println!("{}", x); // ERROR: value moved!

// we can fix this by passing a reference instead
let x = String::from("string");
my_func(&x);
println!("{}", x); // this works now!

fn my_func(s: &String) {
    // do something with the borrowed string
}

// if we want to modify the value, we need a mutable reference
let mut x = String::from("string");
my_func_mut(&mut x);
println!("{}", x); // prints the modified string

fn my_func_mut(s: &mut String) {
    s.push_str(" modified");
}

// rust also has some crazy shorthand for iteration
fn apply_to_all(nums: Vec<i32>, f: impl Fn(i32) -> i32) -> Vec<i32> {
    let mut result = Vec::new();
    for number in nums {
        result.push(f(number));
    }
    result
}

// can just be shortened to one line with iterator methods
fn apply_to_all(nums: Vec<i32>, f: impl Fn(i32) -> i32) -> Vec<i32> {
    nums.into_iter().map(f).collect()
}
//NOTE: rust has an amazing compiler that literally won't let you compile code with logic errors it can detect.
```

## What I Learned

coming from C#, rust feels both familiar and alien. the type system feels right at home, but the ownership model really makes you think differently about your code. no more passing objects around willy-nilly like in managed languages!

the compiler is honestly the best teacher I've ever had. every error message points you exactly where you went wrong and usually suggests how to fix it. it's like pair programming with someone who never gets tired of your mistakes.

the performance potential for my boids system is what really excites me. no garbage collector pauses, direct memory control, and zero-cost abstractions. plus, godot-rust gives me the best of both worlds - rust's performance with godot's fantastic editor and scene system.

one thing that really clicked for me: rust forces you to be explicit about who owns what data and for how long. it's like the language enforces the single source of truth principle I already believe in. no more wondering if some random method three layers deep is mutating your state!

## Tomorrow's Goals

- port my boid struct to rust and get basic movement working
- benchmark the difference between my C# and rust implementations
- figure out how to properly structure a godot-rust project (the examples online are all over the place)
- learn more about lifetimes - still confused when I need `'a` and when I don't
- maybe start on the spatial partitioning system if I get the basics working

honestly, learning rust during a family reunion wasn't the most focused environment, but there's something about sitting by a campfire debugging borrow checker errors that really makes it stick in your brain. next time though, I'm bringing a second monitor to the cabin.

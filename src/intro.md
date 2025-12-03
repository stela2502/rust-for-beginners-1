# Introduction

Programming in Rust is totally different from coding in C or C++ - and of cause also very different from coding in R or Python. I will talk about typed variables later on, but want to first highlight the security features Rust implements.

In Rust a variable is 'owned' by a function - possibly comparable to R. But instead of R which simply copies the data into every function in Rust the variables can not be changed inside of the function. It is especially prohibited to e.g. give a function a file object to write to. By default only the function that did create the file object can write to it.

This is also true for e.g a HashMap that stores objects (some data) in combination with e.g. a string key. If you want to modify the stored object you need the class that 'owns' this object make the changes. I have not hidden any of these problems inside this tutorial. That is something you need to learn the hard way.

# Variables

Rust uses strong typed variables. This does mean that you need to define your variables as a specific type.
As an example you can store integer values as u8, u16, u32, u64, u128, i8, i16, i32, i64, i128, usize or integer.
All of these types can not be compared against each other. So if you want to create some data structures it does help to choose the same type for all data (eg. u64). 
The difference between the integer types are the range of values they can contain: u8 and i8 are 8 bit long whereas u128 and i128 are 128 bit long; the u types are unsigned and can not contain negative values. 
Usize is the default integer type and is dependent on your computer type. On 64 bit computers it is the same as a u64 - but you still can not compare it directly to u64.

## Target Audience

This tutorial does not explain to you how to program. I assume you are absolutely able to do that.
In addition there a re tons of other tutorials that explain that to you.

What I found most important for my work is to try to understand code I have not written and hunt bugs I am not responsible for. In addition I assume you all benefit the most of a larger set of code snippets you can go back to and copy code from. Code you can double check how something could work.

## What you For this Tutorial

But first you need the Rust compiler. Best is - if you not already have it to follow [the official axplanations](https://rust-lang.org/tools/install/).
In addition I do recommend a programming GUI like the [SublimeText](https://www.sublimetext.com/download) and [SublimeMerge](https://www.sublimemerge.com/download) combination I use since years.
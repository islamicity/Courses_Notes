# A brief introduction to Haskell

Haskell is a **purely functional** programming language. In imperative languages you get things done by giving the computer a sequence of tasks and then it executes them. 



Haskell **is lazy.** That means that unless specifically told otherwise, Haskell won't execute functions and calculate things until it's really forced to show you a result. That goes well with referential transparency and it allows you to think of programs as a series of *transformations on data*. It also allows cool things such as infinite data structures. 

Haskell is **statically typed.** When you compile your program, the compiler knows which piece of code is a number, which is a string and so on. That means that a lot of possible errors are caught at compile time. If you try to add together a number and a string, the compiler will whine at you. Haskell uses a very good type system that has type inference.













## Resources

- https://www.schoolofhaskell.com/school/starting-with-haskell/introduction-to-haskell/1-haskell-basics
- 
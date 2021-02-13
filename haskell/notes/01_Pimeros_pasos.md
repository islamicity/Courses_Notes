# Getting started with Haskell.

- The haskell community has put together a single, easily installable package of useful tools referred to as the ***Haskell Platform***. This package includes the following:

  - The Glasgow Haskell Compiler (GHC)
  - An interactive interpreter (GHCi)
  - The stack tool for managing Haskell projects
  - A bunch of useful Haskell packages

  **url:** https://www.haskell.org/downloads/#platform

  ```bash
  $ sudo apt-get install haskell-platform
  ```

  

  ## **First example:** *Hello World*. 

  In your text editor of choice, create a new file named hello.hs and enter the following code:

  ```haskell
  --hello.hs my first Haskell file!
  main = do
      print "Hello World!"
  ```

  Now, that you have a simple file, you can run GHC passing in your file as an argument:

  ```haskell
  ghc hello.hs
  [1 of 1] Compiling Main             ( hello.hs, hello.o )
  Linking hello ...
  ```

  If the compilation was successful, GHC will have created three files:

  -  hello  (binary executable)
  - hello.hi
  - hello.o

  You can simply run the file:

  ```bash
  $ ./hello
  "Hello World!"
  ```

  Like most command-line tools, GHC support a wide range of optional flags. 

  ```bash
  $ ghc hello.hs -o helloworld
  Linking helloworld ...
  
  $ ./helloworld 
  "Hello World!"
  ```

  For a more complete listing of compiler options:

  ```bash
  $ ghc --help
  ```

  

  ## Interacting with Haskell (GHCi).

  One of the most useful tools for writing Haskell programs is GHCi, an interactive face for GHC. GHCi is started with a simple command: *ghci*. When we start GHCi, we'll be greeted with a new prompt:

  ```bash
  $ ghci
  GHCi, version 8.6.5: http://www.haskell.org/ghc/  :? for help
  Prelude>
  Prelude> :set prompt "ghci> "
  ghci> 
  ```
  
  The prompt here is Prelude> but because it can get longer when load stuff into the session.  We can change it, just type in :set prompt "ghc> ".
  
  Here's some simple examples
  
  **ARITHMETIC**
  
  ```
  ghci> 3 + 3
  6
  ghci> 33 * 40
  1320
  ghci> 1231231 - 3423423
  -2192192
  ghci> 5 / 2
  2.5
  ghci>  (50 * 300) - 200
  14800
  ```
  
  **BOOLEAN ALGEBRA**
  
  ```bash
  ghci>  True && False
  False
  ghci> True && True
  True
  ghci> not False
  True
  ```
  
  **TESTING EQUALITY**
  
  ```bash
  ghci> 5 == 5
  True
  ghci> 1 == 0
  False
  ghci> 5 /= 5
  False
  ghci> "hello" == "hello"
  True
  ```
  
  
  
   
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  











## RESOURCES

- GHC User Guide https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/
- 
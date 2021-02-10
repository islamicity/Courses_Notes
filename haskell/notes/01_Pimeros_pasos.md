# Haskell. Primeros pasos.

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

  

  











## RESOURCES

- GHC User Guide https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/
- 
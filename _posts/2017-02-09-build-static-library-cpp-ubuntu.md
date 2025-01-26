---
layout: post
title:  "Build static library cpp in Ubuntu"
date:   2017-02-09 20:30:00 +0700
categories: [Cpp, Ubuntu]
tags: [Static library, Makefile] 
---

Library is very important when you build some kind of big systems. Some advantages of library are:
  * You have to write less code
  * Easy to fix code
  * Easy to share to others
  * ...

In this post, I am going to share one way to build static library in Ubuntu. First of all, you can get source files on [Github](https://github.com/phamvanlam/stack-problems/tree/master/build-static-library-cpp-ubuntu).

#### Structure of project
  
  * test
    * main.cc
    * Makefile
  * lib.h
  * lib.cc
  * Makefile

#### Detail
  
  * **lib.h**:
    ```cpp
    #ifndef _LIB_H_
    #define _LIB_H_
      void sayHi();
    #endif
    ```
  
  * **lib.cc**:
    ```cpp
    #include "lib.h"
    #include <stdio.h>
    void sayHi()
    {
      printf("Hello from static library\n");
    }
    ```
 
  * **Makefile**:
    ```make
    CC = g++
    libmylib.a: lib.o
        ar rcs $@ $^
    lib.o: lib.cc lib.h
        $(CC) -c -o $@ $<
    clean:
        rm -f *.o *.a
    ```

    * Compiler: g++
    * Output: libmylib.a
    * $@: means target
    * $^: all dependencies
    * $<: first dependency
    * "ar rcs ..." is to build static library.
   
  * **test/main.cc**   
    ```cpp
    #include "../lib.h"
    #include <stdio.h>
    int main()
    {
      sayHi();
      return 0;
    }
    ```
   
  * **test/Makefile**
    ```make
    TARGET = prog
    CC = g++
    LDFLAGS = -L..
    LIBS = -static -lmylib
    $(TARGET): main.o 
      $(CC) $^ $(LDFLAGS) $(LIBS) -o $@
    main.o: main.cc 
      $(CC) -c $^ -o $@ 
    clean: rm -f *.o $(TARGET)
    ```

    * LDFLAGS: directory of library (libmylib.a) with option -L
    * LIBS: name of library (mylib) with option -l (without prefix "lib" and suffix ".a")

#### Building static library

  * Assuming that you are in **/build-static-library-cpp-ubuntu** directory.
    ```
    make
    ```

#### Testing static library

  * Run:
    ```
    cd test
    make
    ./prog
    ```

  * Result should be: Hello from static library

Thanks for reading. If you have some questions, feel free to ask me. <br/>LP Devs
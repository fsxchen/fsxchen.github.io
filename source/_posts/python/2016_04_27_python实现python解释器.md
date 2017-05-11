---
title: python实现python解释器
date: 2016-4-27 15:55
category:
	- Python
tags:
	- Python
---
# Python实现的Python解释器
原文地址
https://github.com/aosabook/500lines/blob/b5ee54148551b2e71991cf0a4858d86b671f6e52/interpreter/interpreter.markdown

[项目直达](https://github.com/aosabook/500lines)
这是一个500行实现的一个例子.
[该文章的中文翻译地址](http://qingyunha.github.io/taotao/)


## 介绍

`Byterun` is a Python interpreter implemented in Python. Through my work on Byterun, I was surprised and delighted to discover that the fundamental structure of the Python interpreter fits easily into the 500-line size restriction. This chapter will walk through the structure of the interpreter and give you enough context to explore it further. The goal is not to explain everything there is to know about interpreters—like so many interesting areas of programming and computer science, you could devote years to developing a deep understanding of the topic.

Byterun was written by Ned Batchelder and myself, building on the work of Paul Swartz. Its structure is similar to the primary implementation of Python, CPython, so understanding Byterun will help you understand interpreters in general and the CPython interpreter in particular. (If you don't know which Python you're using, it's probably CPython.) Despite its short length, Byterun is capable of running most simple Python programs[^versions].

[^versions]: This chapter is based on bytecode produced by Python 3.5 or earlier, as there were some changes to the bytecode specification in Python 3.6.


## Python解释器
解释器: 指的就是REPL(A read–eval–print loop )

在解释器接手之前，Python会执行其他3个步骤：词法分析，语法解析和编译。这三步合起来把源代码转换成code object,它包含着解释器可以理解的指令。而解释器的工作就是解释code object中的指令。

## A Python Python Interpreter
gcc就是C语言写的

## Building an Interpreter

Python解释器是一个虚拟机,模拟真实计算机的软件。我们这个虚拟机是栈机器，它用几个栈来完成操作（与之相对的是寄存器机器，它从特定的内存地址读写数据）。
Python解释器是一个字节码解释器：它的输入是一些命令集合称作字节码。当你写Python代码时，词法分析器，语法解析器和编译器生成code object让解释器去操作。每个code object都包含一个要被执行的指令集合 --- 它就是字节码 --- 另外还有一些解释器需要的信息。字节码是Python代码的一个中间层表示：它以一种解释器可以理解的方式来表示源代码。这和汇编语言作为C语言和机器语言的中间表示很类似。

## A Tiny Interpreter
理解3个指令
`LOAD_VALUE`
`ADD_TOW_VALUE`
`PRINT_ANSWER`

假设你输入的是
```
7+5
```
生成
```
what_to_execute = {
    "instructions": [("LOAD_VALUE", 0),  # the first number
                     ("LOAD_VALUE", 1),  # the second number
                     ("ADD_TWO_VALUES", None),
                     ("PRINT_ANSWER", None)],
    "numbers": [7, 5] }
```

Python解释器是一个栈机器，所以它必须通过操作栈来完成这个加法。(Figure 1.1)解释器先执行第一条指令，LOAD_VALUE，把第一个数压到栈中。接着它把第二个数也压到栈中。然后，第三条指令，ADD_TWO_VALUES,先把两个数从栈中弹出，加起来，再把结果压入栈中。最后一步，把结果弹出并输出。

![栈机器](http://7xrn62.com1.z0.glb.clouddn.com/a2e07f8d634f98373fe498bd2a9e8ae5.png)


```
class Interpreter:
    def __init__(self):
        self.stack = []

    def LOAD_VALUE(self, number):
        self.stack.append(number)

    def PRINT_ANSWER(self):
        answer = self.stack.pop()
        print(answer)

    def ADD_TWO_VALUES(self):
        first_num = self.stack.pop()
        second_num = self.stack.pop()
        total = first_num + second_num
        self.stack.append(total)
```

上面3个方法完成了解释器的3个指令.

```
def run_code(self, what_to_execute):
       instructions = what_to_execute["instructions"]
       numbers = what_to_execute["numbers"]
       for each_step in instructions:
           instruction, argument = each_step
           if instruction == "LOAD_VALUE":
               number = numbers[argument]
               self.LOAD_VALUE(number)
           elif instruction == "ADD_TWO_VALUES":
               self.ADD_TWO_VALUES()
           elif instruction == "PRINT_ANSWER":
```


## 变量

```
what_to_execute = {
        "instructions": [("LOAD_VALUE", 0),
                         ("STORE_NAME", 0),
                         ("LOAD_VALUE", 1),
                         ("STORE_NAME", 1),
                         ("LOAD_NAME", 0),
                         ("LOAD_NAME", 1),
                         ("ADD_TWO_VALUES", None),
                         ("PRINT_ANSWER", None)],
        "numbers": [1, 2],
        "names":   ["a", "b"] }
```

变量

```
class Interpreter:
    def __init__(self):
        self.stack = []
        self.environment = {}

    def STORE_NAME(self, name):
        val = self.stack.pop()
        self.environment[name] = val

    def LOAD_NAME(self, name):
        val = self.environment[name]
        self.stack.append(val)

    def parse_argument(self, instruction, argument, what_to_execute):
        """ Understand what the argument to each instruction means."""
        numbers = ["LOAD_VALUE"]
        names = ["LOAD_NAME", "STORE_NAME"]

        if instruction in numbers:
            argument = what_to_execute["numbers"][argument]
        elif instruction in names:
            argument = what_to_execute["names"][argument]

        return argument

    def run_code(self, what_to_execute):
        instructions = what_to_execute["instructions"]
        for each_step in instructions:
            instruction, argument = each_step
            argument = self.parse_argument(instruction, argument, what_to_execute)

            if instruction == "LOAD_VALUE":
                self.LOAD_VALUE(argument)
            elif instruction == "ADD_TWO_VALUES":
                self.ADD_TWO_VALUES()
            elif instruction == "PRINT_ANSWER":
                self.PRINT_ANSWER()
            elif instruction == "STORE_NAME":
                self.STORE_NAME(argument)
            elif instruction == "LOAD_NAME":
                self.LOAD_NAME(argument)
```

可以看到run_code的方法已经很冗长了.使用python的动态方法查找

```
def execute(self, what_to_execute):
    instructions = what_to_execute["instructions"]
    for each_step in instructions:
       instruction, argument = each_step
       argument = self.parse_argument(instruction, argument, what_to_execute)
       bytecode_method = getattr(self, instruction)
       if argument is None:
           bytecode_method()
       else:
           bytecode_method(argument)
```


## Real Python Bytecode

python字节码

```
>>> def cond():
...     x = 3
...     if x < 5:
...         return 'yes'
...     else:
...         return 'no'
...

```
使用`cond.__code__`可以看到其字节码,但是看上去难以理解

```
>>> cond.__code__.co_code  # the bytecode as raw bytes
b'd\x01\x00}\x00\x00|\x00\x00d\x02\x00k\x00\x00r\x16\x00d\x03\x00Sd\x04\x00Sd\x00
   \x00S'
>>> list(cond.__code__.co_code)  # the bytecode as numbers
[100, 1, 0, 125, 0, 0, 124, 0, 0, 100, 2, 0, 107, 0, 0, 114, 22, 0, 100, 3, 0, 83,
 100, 4, 0, 83, 100, 0, 0, 83]
```

使用标准库中的`dis` module来解决这个问题.

```
>>> dis.dis(cond)
  2           0 LOAD_CONST               1 (3)
              3 STORE_FAST               0 (x)

  3           6 LOAD_FAST                0 (x)
              9 LOAD_CONST               2 (5)
             12 COMPARE_OP               0 (<)
             15 POP_JUMP_IF_FALSE       22

  4          18 LOAD_CONST               3 ('yes')
             21 RETURN_VALUE

  6     >>   22 LOAD_CONST               4 ('no')
             25 RETURN_VALUE
             26 LOAD_CONST               0 (None)
             29 RETURN_VALUE
```

第一列的数字标示对应的源码行数
第二列数字是字节码的索引
第三列是指令本省对应的名字
第四列标示之列那个 参数
第五列标示关于参数的提示

## Conditrionals and Loops

```
>>> def loop():
...      x = 1
...      while x < 5:
...          x = x + 1
...      return x
...
>>> dis.dis(loop)
  2           0 LOAD_CONST               1 (1)
              3 STORE_FAST               0 (x)

  3           6 SETUP_LOOP              26 (to 35)
        >>    9 LOAD_FAST                0 (x)
             12 LOAD_CONST               2 (5)
             15 COMPARE_OP               0 (<)
             18 POP_JUMP_IF_FALSE       34

  4          21 LOAD_FAST                0 (x)
             24 LOAD_CONST               1 (1)
             27 BINARY_ADD
             28 STORE_FAST               0 (x)
             31 JUMP_ABSOLUTE            9
        >>   34 POP_BLOCK

  5     >>   35 LOAD_FAST                0 (x)
             38 RETURN_VALUE

```

## Explore Bytecode

我鼓励你用dis.dis来试试你自己写的函数。一些有趣的问题值得探索：

* 对解释器而言for循环和while循环有什么不同？
* 能不能写出两个不同函数，却能产生相同的字节码?
* elif是怎么工作的？列表推导呢？


## Frames

一个frame是一些信息的集合和代码的执行上下文。frames在Python代码执行时动态的创建和销毁。每个frame对应函数的一次调用。--- 所以每个frame只有一个code object与之关联，而一个code object可以很多frame。比如你有一个函数递归的调用自己10次，这时有11个frame。总的来说，Python程序的每个作用域有一个frame，比如，每个module，每个函数调用，每个类定义。

ex:

```
>>> def bar(y):
...     z = y + 3     # <--- (3) ... and the interpreter is here.
...     return z
...
>>> def foo():
...     a = 1
...     b = 2
...     return a + bar(b) # <--- (2) ... which is returning a call to bar ...
...
>>> foo()             # <--- (1) We're in the middle of a call to foo ...
```

![python frame 概念](http://7xrn62.com1.z0.glb.clouddn.com/fe2d2dacab4fd28e6d6cd4acdf1f5ff2.png)

在，解释器在foo函数的调用中。调用栈中有3个fram：一个对应于module层，一个对应函数foo,别一个对应函数bar。(Figure 1.2)一旦bar返回，与它对应的frame就会从调用栈中弹出并丢弃。

我惊讶的发现Python真的很少依赖于每个frame有一个数据栈这个特性。在Python中几乎所有的操作都会清空数据栈，所以所有的frame公用一个数据栈是没问题的。在上面的例子中，当bar执行完后，它的数据栈为空。即使foo公用这一个栈，它的值也不会受影响。然而，对应生成器，一个关键的特点是它能暂停一个frame的执行，返回到其他的frame，一段时间后它能返回到原来的frame，并以它离开时的同样的状态继续执行。

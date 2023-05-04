Download Link: https://assignmentchef.com/product/solved-cs502-project-1-arithmetic-expressions
<br>
<h1></h1>

The goal of this project is to setup the environment and to learn how to parse and translate mathematical expressions into x86_64 assembly code. Our approach will be simple and the objective is to highlight some of the key aspects and ideas behind a compiler.

For this rst project, we are going to keep things very simple and take very small steps. Make sure that you understand everything correctly before going to the next step.

At the end of the project, we will be able to parse mathematical expressions with single digit numbers, addition, substraction, multiplication, division, and parentheses. Our parser will generate an intermediate representation in the form of an Abstract Syntax Tree (AST). The de nition of the AST is the one used during the lecture.

In parallel, we will implement a generator that converts the AST into x86_64 code.

Here is a small example of what we will be able to generate by the end of the project:

[info] Running project1.Runner 2+3

============= AST ================   Plus(Lit(2),Lit(3))

==================================

============ OUTPUT ==============

.text

.global entry_point

entry_point:   push %rbp # save stack frame for C convention

mov %rsp, %rbp




# beginning generated code

movq $2, %rbx   movq $3, %rcx   addq %rcx, %rbx   movq %rbx, %rax   # end generated code

# %rax contains the result




mov %rbp, %rsp  # reset frame   pop %rbp   ret

==================================

Result: 5

Step 1: Getting Started

<a href="https://tiarkrompf.github.io/cs502/getting_started.html">The rst step of this assignment is to set up your environment as described in the </a><a href="https://tiarkrompf.github.io/cs502/getting_started.html">Getting Started – Tools page</a><a href="https://tiarkrompf.github.io/cs502/getting_started.html">. If you have any trouble installing the tools for the course, ask the TAs for help.</a>

Note that all of the prerequisites (even IDEs like Eclipse) are installed on the CS Department Linux machines, and you are free to use an X server such as <a href="https://sourceforge.net/projects/xming/">xming</a> to do the project remotely or follow these <a href="https://www.cs.purdue.edu/resources/facilities/remote-access.html#RShell">instructions</a> to access the lab machines remotely. Alternatively you may do the project on your own machine and then upload your results to your home directory on data.cs.purdue.edu and turn in your results using turnin.

The project has been designed and tested for Linux/Mac OS. If you have only Windows installed on your laptop consider running Linux in a VM, or use the lab machines for the project. (However it should be working on Windows as well)

If you use remote access to work on your project, please use one of the lab machines pod1-1 to pod1-20 with the suf x cs.purdue.edu (e.g. pod1-1.cs.purdue.edu) Download the skeleton le <a href="https://www.cs.purdue.edu/homes/wang603/cs502/proj1.tar.gz">here</a> .

tar xzvf proj1.tar.gz cd proj1

Step 2: Project Structure

<strong>Open Project in Your Favorite Scala IDE</strong>

You can use your editor or IDE of choice. The <a href="https://www.scala-lang.org/">Scala website</a> has instructions for a range of alternatives.

Browse the Files

A description of the different les is provided below. The two les you need to complete are <strong>Parser.scala </strong>and <strong>Generator.scala</strong>. Each parser that we are going to implement builds on top of another, so you need to implements them in order by following the comments in the code. We will implement two different generators using two different strategies. It is recommended to implement the <strong>StackASMGenerator</strong> class when it is suggested in the <strong>Parser.scala</strong> le, and implement the <strong>RegASMGenerator</strong> at the end.

build.sbt

The Scala Build Tool (sbt) is an open source build tool for Scala and Java projects. These les contain the information necessary to compile this project. You should not have to modify them.

To use sbt, launch a terminal and go to the project directory (proj1) and type ‘sbt’. It will lauch an sbt console. You can run the program from there:

run “arg1” “arg2” // run main program with arguments arg1 and arg2     run “1+3*(5-8)”

test              // run all the tests application (in src/test) <strong>src/main/scala/project1/Util.scala</strong>

This le de nes multiple classes that are used to generate the code and run it on your machine. Nothing needs to be modi ed, but it is recommanded to read it and have an idea of what is happening behind the scenes.

gen/bootstrap.c

As we are generating assembly code which is OS dependent, we are using GCC to do the heavy lifting for us.

The boostrap le is a generic C le that is calling a function <strong>entry_point</strong> and is printing the result in <strong>stdout</strong>. Our compiler will generate the le <strong>gen/gen.s</strong> and will be assembled and bootstrapped by gcc: gcc bootstrap.c gen.s -o out

<strong>out</strong> will then print the result of our compiled expression.

NOTE: the assembly/compilation and running steps are executed through the code. (see Util.scala#L88 and

Main.scala#L39) src/main/scala/project1/Main.scala

The main function is de ned in this le. During this project, you will be implementing many different parsers in order to test them through the main function. You will need to modify the parser or generator you want to use.

src/main/scala/project1/Parser.scala

This class contains the de nition of our intermediate language. This is the language we are using in class; please refer to the lecture for more information.

The class <strong>SimpleParser</strong> de ned in this le is the basic parser that we are going to use for this project. It is reading a stream of characters one at a time and can extract a single digit number <strong>getNum</strong> or a single letter name <strong>getName</strong>. It does not handle whitespace.

We are keeping the parser very simple at the beginning to focus on the important concepts. Later on, we will improve it to handle multiple digits numbers and more expressions. We will also support whitespace.

The rest of the le is the de nition of each parser we are building, starting from single number expressions to the full language we want to target. What you have to do for this project is highlighted with TODOs in the code. We encourage you to read the comments and code carefully before proceeding. <strong>src/main/scala/project1/Generator.scala</strong>

In this le, we are de ning some generators that can convert our AST into x86_64 code.

We consider two different ways of generating the assmbly code, each of which has pros and cons: <strong>Stackbased</strong> code, which is not very ef cient but can handle arbitrarily complex expressions; and <strong>Register-based </strong>code, which is ef cient but can not handle arbitrarily complex expressions. We will see later that compilers use a hybrid approach.

<h2>src/test/scala/project1/*Test.scala</h2>

These les contain some unit tests for the rst parsers. You will have to write your own tests for the others. There are some functions given to you in order to make the implementation easier.



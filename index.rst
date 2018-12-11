.. The JCoCo Virtual Machine documentation master file, created by
   sphinx-quickstart on Mon Mar 13 12:51:40 2017.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

The JCoCo Virtual Machine
====================================

JCoCo is a virtual machine, based on the Python 3.2 virtual machine. The JCoCo virtual machine reads and executes assembly language. Learning this assembly language is made relatively easy by writing Python programs and disassembling them. JCoCo is a stack-based virtual machine. Each instruction gets is operands from an operand stack and leaves its results on the operand stack.

[This virtual machine was implemented in Java. Another version of this project called just CoCo, was written in C++. You can find documentation on the C++ version at http://cs.luther.edu/CoCo. JCoCo is backwards compatible with CoCo.]

JCoCo was designed for educational purposes for learning Java, assembly language, and higher level language implementation. However, JCoCo is a full-fleged virtual machine supporting function definition, higher order functional programming, classes, object instantiation, inheritance, and polymporphism. Source code for a reduced functionality JCoCo is available on GitHub free of charge. You may download it by cloning the github repository or by downloading a zip file of the source. A full implementation jar file is available in the next section.


Download & Source Code
========================
You can be up and running the JCoCo virtual machine in minutes. Download :download:`the JCoCo zip file <jcoco.zip>` and unzip it in your bin directory or some place in your path. That's it. You should then be able to run coco by typing *coco* at a command prompt. You must have Java installed to run this program, but most systems do have Java these days. If yours does not, you can `download Java here <https://www.java.com/>`_.

You can access the source code for most of the virtual machine at `http://github.com/kentdlee/JCoCo <http://github.com/kentdlee/JCoCo>`_. The full version of the source code
is hidden from students to allow them to extend it as a project. Full source code access can be granted on github upon request from non-students.

You may also want the Python disassembler. This will let you disassemble many Python programs to the assembly language format needed by the JCoCo virtual machine. You can :download:`download the disassembler by clicking here <disassembler.py>`. You must have Python 3.2 installed on your system to run this disassembler. You can determine if you have Python 3.2 by opening up a terminal windows and typing *python3.2*. If the program is installed, and it is in your path, it should run. If not, you can download the appropriate file below and install it on your system.

	* :download:`python-3.2.5-macosx10.3.dmg` - For Mac OS X 10.3 and later.
	* :download:`python-3.2.5-macosx10.6.dmg` - For Mac OS X 10.6 and later.
	* :download:`python-3.2.5.amd64.msi` - For 64-bit Microsoft Windows.
	* :download:`python-3.2.5.msi` - For 32-bit Microsoft Windows.
	* :download:`Python-3.2.6.tgz` - Source code which can be compiled and used to install Python 3.2 if you have a C compiler.

Read below for how to use the disassembler and for what it can and cannot do.

Overview of the Virtual Machine
======================================



JCoCo, like Python, is a virtual machine (i.e. an interpreter) for bytecode instructions. JCoCo is written in Java using object-oriented principles and does not store its instructions in actual bytecode format. Instead, it reads an assembly language file and builds an internal representation of the program as a sequence of functions and classes, each with their own sequence of ByteCode objects.

.. container:: figboxright

   .. figure:: coco.png

Each function of a JCoCo program, when called, results in a new stack frame object being created. A stack frame contains all local variables that are defined in the function. Each stack frame also contains two stacks, an operand stack and a block stack.

The operand stack is used by the virtual machine to store all intermediate results of instruction execution. Operands are popped from the operand stack when an instruction is executed possibly leaving its result on the operand stack for use by a subsequent instruction. This style of computation has been in use a long time, from Hewlett Packard mainframe computers of the 1960's through the 1980's to calculators made by Hewlett Packard still today. The Java virtual machine is a stack machine as is the Python virtual machine.

The other stack in a frame is a block stack. The block stack keeps track of exit points for blocks of code within a JCoCo program. When a loop is entered, the exit point of the loop is pushed onto the block stack. In this way, if a break instruction is executed inside a loop, the exit point of the loop can be found and the execution of the break instruction will jump to that address. Exception handlers also push the address of the handler onto the block stack. If an exception occurs, execution jumps to the exception handler by popping the address from the block stack. When a loop or try block is exited, the corresponding block stack address is popped from the block stack.

A program counter (abbreviated PC) is responsible for holding the address of the next instruction to be executed. The machine proceeds by fetching an instruction from the code, incrementing the PC, and executing the fetched instruction. Execution proceeds this way until a RETURN_VALUE instruction is executed or an exception occurs.

When an exception occurs, if no matching exception handler is found, execution of the function terminates and control is returned to the previously called function where the exception continues to propagate back until a matching exception handler is found. If no matching handler is found, the complete traceback of the exception is printed.

If no exception occurs during the running of a program, execution terminates when the main function executes the RETURN_VALUE instruction.

Usage
=====

JCoCo is a full-fledged virtual machine. It reads a source file in JCoCo assembly format and interprets the instructions. JCoCo is a interpreter for Python assembly language instructions. In addition, included with JCoCo is a Python disassembler that diassembles Python programs into JCoCo format. With these two tools together you can learn a lot about not only the syntax of casm files (the extension understood as CoCo assembly language) but also the Python language itself. Two examples below describe the usage of JCoCo and the associated disassembler.

Example
---------

Consider the following Python program. The disassembler is a module found in the tests subdirectory of the source code distribution or you can :download:`download it here <disassembler.py>`. To disassemble correctly, Python programs must contain a series of functions including a main function. The disassembler is imported at the top. Instead of calling the main function, the disassemble function of the same module is called on each function.

.. code-block:: python

	import disassembler

	def main():
	    x = 5
	    y = x + 5
	    print(y+5)

	disassembler.disassemble(main)

To run the disassembler you must use Python 3.2 as follows.

.. code-block:: bash

	Kent's Mac> python3.2 test1.py

where *test1.py* is the name of this program. Calling the disassembler on the main function in the example program *test1.py* produces this output to the screen.

.. code-block:: python

	Function: main/0
	Constants: None, 5
	Locals: x, y
	Globals: print
	BEGIN
	          LOAD_CONST                     1
	          STORE_FAST                     0
	          LOAD_FAST                      0
	          LOAD_CONST                     1
	          BINARY_ADD
	          STORE_FAST                     1
	          LOAD_GLOBAL                    0
	          LOAD_FAST                      1
	          LOAD_CONST                     1
	          BINARY_ADD
	          CALL_FUNCTION                  1
	          POP_TOP
	          LOAD_CONST                     0
	          RETURN_VALUE
	END

If you wish to save this to a file to execute with JCoCo you can do the following.

.. code-block:: bash

	Kent's Mac> python3.2 test1.py > test1.casm
	Kent's Mac> coco test.casm
	15

In the *test1.py* program there is one function called main. The assembly indicates main has 0 formal parameters. Constants that are used in the code include None and 5. There are two local variables in the function: x and y. The global print function is called and so is in the list of globals. Every function in CoCo has at least these categories of identifiers and values within each defined function. Sometimes one or more of these categories may be empty and can be omitted in that case.

The instructions of the code follow the begin keyword and preceed the end keyword. LOAD_CONST 1 means to load the constant value at index 1 (zero based) of the constants onto the operand stack. CoCo is a stack machine and therefore all operations are performed with operands pushed and popped from the operand stack.

The STORE_FAST instruction stores a value in the locals list, in this case at offset 0, the location of x. LOAD_FAST does the opposite, pushing a value on the operand stack from the locals list of variables. BINARY_ADD pops two elements from the stack and adds them together, pushing the result. CALL_FUNCTION pops the number of arguments specified in the instruction (1 in this case) and then pops the function from the stack. Finally, it calls the popped function with the popped arguments. The result of the function call is left on the top of the operand stack. In the case of the print function, None is returned and left on the stack. The POP_TOP instruction pops the None from the stack and discards it only to have the *main* function push a None on the stack just before returning. RETURN_VALUE pops the top argument from the operand stack and returns that value to the calling function.

To run this code, make sure that you have the *coco* executable in your path some place. Then you can execute the following code to try this example.

.. code-block:: bash

	cd tests

	python3.2 test1.py > test1.casm

	coco test1.casm

A More Complex Example
-----------------------
JCoCo is capable of handling complex functions that may be nested inside one another. It can handle functions that return functions and functions that take other functions as parameters. Consider the following Python program.

.. code-block:: python

	# If 1 2 3 4 is entered, and 5 for the second prompt
	# then the answer should be [6, 13, 20, 27]
	from disassembler import *

	def main():
	    def g(aVal):
	        def f(x):
	            return aVal + lstInts[0] + x

	        return f

	    x = input("Please enter a list of integers: ")
	    lst = x.split()

	    lstInts = []
	    for y in lst:
	        lstInts.append(int(y))

	    myFun = g(6)

	    print(myFun(lstInts[2]))

	#main()
	disassemble(main)

Calling disassemble on main will disassemble any nested functions as well, so it is only necessary to call disassemble on all top-level functions. You can read the code above and the comments to understand how this program works. The disassembled code appears below.

.. code-block:: python

	Function: main/0
	    Function: g/1
	        Function: f/1
	        Constants: None, 0
	        Locals: x
	        FreeVars: aVal, lstInts
	        BEGIN
	                  LOAD_DEREF                     0
	                  LOAD_DEREF                     1
	                  LOAD_CONST                     1
	                  BINARY_SUBSCR
	                  BINARY_ADD
	                  LOAD_FAST                      0
	                  BINARY_ADD
	                  RETURN_VALUE
	        END
	    Constants: None, code(f)
	    Locals: aVal, f
	    FreeVars: lstInts
	    CellVars: aVal
	    BEGIN
	              LOAD_CLOSURE                   0
	              LOAD_CLOSURE                   1
	              BUILD_TUPLE                    2
	              LOAD_CONST                     1
	              MAKE_CLOSURE                   0
	              STORE_FAST                     1
	              LOAD_FAST                      1
	              RETURN_VALUE
	    END
	Constants: None, code(g), "Please enter a list of integers: ", 6, 2
	Locals: g, x, lst, y, myFun
	CellVars: lstInts
	Globals: input, split, append, int, print
	BEGIN
	          LOAD_CLOSURE                   0
	          BUILD_TUPLE                    1
	          LOAD_CONST                     1
	          MAKE_CLOSURE                   0
	          STORE_FAST                     0
	          LOAD_GLOBAL                    0
	          LOAD_CONST                     2
	          CALL_FUNCTION                  1
	          STORE_FAST                     1
	          LOAD_FAST                      1
	          LOAD_ATTR                      1
	          CALL_FUNCTION                  0
	          STORE_FAST                     2
	          BUILD_LIST                     0
	          STORE_DEREF                    0
	          SETUP_LOOP               label02
	          LOAD_FAST                      2
	          GET_ITER
	label00:  FOR_ITER                 label01
	          STORE_FAST                     3
	          LOAD_DEREF                     0
	          LOAD_ATTR                      2
	          LOAD_GLOBAL                    3
	          LOAD_FAST                      3
	          CALL_FUNCTION                  1
	          CALL_FUNCTION                  1
	          POP_TOP
	          JUMP_ABSOLUTE            label00
	label01:  POP_BLOCK
	label02:  LOAD_FAST                      0
	          LOAD_CONST                     3
	          CALL_FUNCTION                  1
	          STORE_FAST                     4
	          LOAD_GLOBAL                    4
	          LOAD_FAST                      4
	          LOAD_DEREF                     0
	          LOAD_CONST                     4
	          BINARY_SUBSCR
	          CALL_FUNCTION                  1
	          CALL_FUNCTION                  1
	          POP_TOP
	          LOAD_CONST                     0
	          RETURN_VALUE
	END

From the code above you can observe several things that are worth a little more explanation.

	* A nested function appears right after the outer function's Function: declaration line.
	* FreeVars is another category of variables in a function. FreeVars are variables that are referenced in the function, but not defined within the function. Instead, they are referenced from the enclosing function's scope.
	* CellVars are yet another category of variable. A cellvar is a variable that is local to the function, but is referenced by some other function and therefore must be stored with an indirect reference so it can be referenced in both function.
	* The code(g) and code(f) represent the code of function g and the code of function f respectively. The code of a nested function is a constant to the outer function.
	* Labels are used to symbolically indicate the targets of jumps and other branching instructions.

Example of Classes and Inheritance
------------------------------------
A third example illustrates the definition of classes in JCoCo including the use of inheritance and inner classes. Consider the following Python program.

.. code-block:: python

	import disassembler
	import sys

	class Base:
		def __init__(self,z):
			self.z = z
			print("Base __init__")


	class A(Base):
		def __init__(self,x,y):
			u = super()
			print(super)
			print(u)
			print(type(u))
			super().__init__(6)
			self.x = x + self.z
			self.y = y + self.z

		def getX(self):
			return self.x

		def slope(self):
			return self.x / self.y

		def __str__(self):
			return "This is an A object"


	def main():
		y = 6 # must be before class definition to
		      # reference in class B. The closure is
		      # taken during the class definition time
		      # and not when the instance of B gets
		      # created.

		class B:
			z = 10

			def __init__(self,x):
				self.x = y

			def getX(self):
				return self.x

		a = A(5,3)

		b = B(5)

		print(a)
		print(type(a))
		print(type(A))
		print(type(6))

		slope = a.slope()

		bx = b.getX()

		bx2 = B.getX(b)

		slope2 = A.slope(a)

		print(slope,slope2,bx,bx2)

	if len(sys.argv) == 1:
		main()
	else:
		disassembler.disassemble(Base)
		disassembler.disassemble(A)
		disassembler.disassemble(main)

The disassembled code from this example is provided here.

.. code-block:: python

	Class: Base
	BEGIN
	    Function: __init__/2
	    Constants: None, "Base __init__"
	    Locals: self, z
	    Globals: z, print
	    BEGIN
	              LOAD_FAST                      1
	              LOAD_FAST                      0
	              STORE_ATTR                     0
	              LOAD_GLOBAL                    1
	              LOAD_CONST                     1
	              CALL_FUNCTION                  1
	              POP_TOP
	              LOAD_CONST                     0
	              RETURN_VALUE
	    END
	END
	Class: A(Base)
	BEGIN
	    Function: slope/1
	    Constants: None
	    Locals: self
	    Globals: x, y
	    BEGIN
	              LOAD_FAST                      0
	              LOAD_ATTR                      0
	              LOAD_FAST                      0
	              LOAD_ATTR                      1
	              BINARY_TRUE_DIVIDE
	              RETURN_VALUE
	    END
	    Function: __str__/1
	    Constants: None, "This is an A object"
	    Locals: self
	    BEGIN
	              LOAD_CONST                     1
	              RETURN_VALUE
	    END
	    Function: getX/1
	    Constants: None
	    Locals: self
	    Globals: x
	    BEGIN
	              LOAD_FAST                      0
	              LOAD_ATTR                      0
	              RETURN_VALUE
	    END
	    Function: __init__/3
	    Constants: None, 6
	    Locals: self, x, y, u
	    FreeVars: __class__
	    Globals: super, print, type, __init__, z, x, y
	    BEGIN
	              LOAD_GLOBAL                    0
	              CALL_FUNCTION                  0
	              STORE_FAST                     3
	              LOAD_GLOBAL                    1
	              LOAD_GLOBAL                    0
	              CALL_FUNCTION                  1
	              POP_TOP
	              LOAD_GLOBAL                    1
	              LOAD_FAST                      3
	              CALL_FUNCTION                  1
	              POP_TOP
	              LOAD_GLOBAL                    1
	              LOAD_GLOBAL                    2
	              LOAD_FAST                      3
	              CALL_FUNCTION                  1
	              CALL_FUNCTION                  1
	              POP_TOP
	              LOAD_GLOBAL                    0
	              CALL_FUNCTION                  0
	              LOAD_ATTR                      3
	              LOAD_CONST                     1
	              CALL_FUNCTION                  1
	              POP_TOP
	              LOAD_FAST                      1
	              LOAD_FAST                      0
	              LOAD_ATTR                      4
	              BINARY_ADD
	              LOAD_FAST                      0
	              STORE_ATTR                     5
	              LOAD_FAST                      2
	              LOAD_FAST                      0
	              LOAD_ATTR                      4
	              BINARY_ADD
	              LOAD_FAST                      0
	              STORE_ATTR                     6
	              LOAD_CONST                     0
	              RETURN_VALUE
	    END
	END
	Function: main/0
	    Function: B/1
	        Function: __init__/2
	        Constants: None
	        Locals: self, x
	        FreeVars: y
	        Globals: x
	        BEGIN
	                  LOAD_DEREF                     0
	                  LOAD_FAST                      0
	                  STORE_ATTR                     0
	                  LOAD_CONST                     0
	                  RETURN_VALUE
	        END
	        Function: getX/1
	        Constants: None
	        Locals: self
	        Globals: x
	        BEGIN
	                  LOAD_FAST                      0
	                  LOAD_ATTR                      0
	                  RETURN_VALUE
	        END
	    Constants: 10, code(__init__), code(getX), None
	    Locals: __locals__
	    FreeVars: y
	    Globals: __name__, __module__, z, __init__, getX
	    BEGIN
	              LOAD_FAST                      0
	              STORE_LOCALS
	              LOAD_NAME                      0
	              STORE_NAME                     1
	              LOAD_CONST                     0
	              STORE_NAME                     2
	              LOAD_CLOSURE                   0
	              BUILD_TUPLE                    1
	              LOAD_CONST                     1
	              MAKE_CLOSURE                   0
	              STORE_NAME                     3
	              LOAD_CONST                     2
	              MAKE_FUNCTION                  0
	              STORE_NAME                     4
	              LOAD_CONST                     3
	              RETURN_VALUE
	    END
	Constants: None, 6, code(B), "B", 5, 3
	Locals: B, a, b, slope, bx, bx2, slope2
	CellVars: y
	Globals: A, print, type, slope, getX
	BEGIN
	          LOAD_CONST                     1
	          STORE_DEREF                    0
	          LOAD_BUILD_CLASS
	          LOAD_CLOSURE                   0
	          BUILD_TUPLE                    1
	          LOAD_CONST                     2
	          MAKE_CLOSURE                   0
	          LOAD_CONST                     3
	          CALL_FUNCTION                  2
	          STORE_FAST                     0
	          LOAD_GLOBAL                    0
	          LOAD_CONST                     4
	          LOAD_CONST                     5
	          CALL_FUNCTION                  2
	          STORE_FAST                     1
	          LOAD_FAST                      0
	          LOAD_CONST                     4
	          CALL_FUNCTION                  1
	          STORE_FAST                     2
	          LOAD_GLOBAL                    1
	          LOAD_FAST                      1
	          CALL_FUNCTION                  1
	          POP_TOP
	          LOAD_GLOBAL                    1
	          LOAD_GLOBAL                    2
	          LOAD_FAST                      1
	          CALL_FUNCTION                  1
	          CALL_FUNCTION                  1
	          POP_TOP
	          LOAD_GLOBAL                    1
	          LOAD_GLOBAL                    2
	          LOAD_GLOBAL                    0
	          CALL_FUNCTION                  1
	          CALL_FUNCTION                  1
	          POP_TOP
	          LOAD_GLOBAL                    1
	          LOAD_GLOBAL                    2
	          LOAD_CONST                     1
	          CALL_FUNCTION                  1
	          CALL_FUNCTION                  1
	          POP_TOP
	          LOAD_FAST                      1
	          LOAD_ATTR                      3
	          CALL_FUNCTION                  0
	          STORE_FAST                     3
	          LOAD_FAST                      2
	          LOAD_ATTR                      4
	          CALL_FUNCTION                  0
	          STORE_FAST                     4
	          LOAD_FAST                      0
	          LOAD_ATTR                      4
	          LOAD_FAST                      2
	          CALL_FUNCTION                  1
	          STORE_FAST                     5
	          LOAD_GLOBAL                    0
	          LOAD_ATTR                      3
	          LOAD_FAST                      1
	          CALL_FUNCTION                  1
	          STORE_FAST                     6
	          LOAD_GLOBAL                    1
	          LOAD_FAST                      3
	          LOAD_FAST                      6
	          LOAD_FAST                      4
	          LOAD_FAST                      5
	          CALL_FUNCTION                  4
	          POP_TOP
	          LOAD_CONST                     0
	          RETURN_VALUE
	END

One part to notice in particular is the definition of class *B* inside the main function. Class *B* references a value outside the scope of the class. As a result a function *B* is defined in the assembly language program whose responsibility it is to instantiate the class. Every class has a list of attributes that include the methods of their object instances. When the function *B* is executed, the locals of function *B* are the attributes of class *B*. So when functions are created and stored into the locals of *B* they are actually being stored into the attributes of class *B*. Defining the class *B* this way enables the free variable y to be referenced from outside the scope of the *B* class.

Another interesting thing to note: Functions are stored as attributes of a class. When an object of a class is instantiated, the functions are instantiated as methods. A method automatically passes *self* as the first parameter to the function that the method encapsulates.

Turtle Graphics
=================
Recently, Turtle Graphics support has been added to JCoCo. You can view documentation for the `Turtle Graphics API <https://docs.python.org/3/library/turtle.html?highlight=turtle>`_ by following this link. JCoCo
sees Turtle Graphics as a module. For instance, it is possible to disassemble this program and run the disassembled program with JCoCo.

.. code-block:: python

  import turtle
  import disassembler
  import sys

  def main():

      t = turtle.Turtle()

      #t.up()
      t.speed(0)
      t.rt(360)
      t.left(360)
      t.right(45)
      t.fd(30)
      t.dot(20)
      t.home()
      t.ht()
      print(t.isvisible())
      print(t.isdown())
      t.left(45)
      t.goto(-50,-100)
      t.home()
      s = turtle.Turtle()
      s.pencolor("red")
      #s.speed(0)
      s.hideturtle()
      s.width(10)
      s.left(45)
      s.goto(0,0)
      s.hideturtle()
      s.begin_fill()
      s.circle(100)
      s.end_fill()
      s.pencolor("blue")
      s.write("Hello World!!!!!",False,"right",("Arial",24,"bold"))
      t.hideturtle()
      t.home()
      t.fd(50)
      stamp_id = t.stamp()
      for i in range(7):
          t.fd(10)
          t.rt(50)
          stamp_id = t.stamp()
      t.undo()
      t.clearstamps()
      t.home()
      t.goto(-75,100)
      for i in range(7):
          t.fd(10)
          t.rt(50)
          stamp_id = t.stamp()
      t.goto(-200,200)
      for i in range(7):
          t.fd(10)
          t.rt(50)
          stamp_id = t.stamp()
      t.undo()


      screen = t.getscreen()
      screen.exitonclick()

  if len(sys.argv) == 1:
  	main()
  else:
  	disassembler.disassemble(main)

While much of Turtle Graphics is implemented, it is not a complete implementation so consider this experimental code.

Assembly Program Syntax
================================

The syntax of the JCoCo assembly language is pretty well illustrated by the three examples above. The complete syntax of the language is given here as a BNF. There are just a few things to note in the BNF below that weren't illustrated by either example above.

	* Instructions may have as many labels defined on them as necessary. The definition of labeled instruction is recursive.
	* The <null> below indicates an empty production. So a ClassFunctionList may be empty for instance, meaning that there might not be a class and function list in a function definition or put another way, the list may be empty. In this case that simply means a function might or might not have some nested functions or classes.
	* [ and ] indicate an optional part of a JCoCo program.
	* Of course, the ... indicates there are more Unary and Binary mnemonics that are not listed in the BNF. The complete list of instructions and descriptions of each of them are given below.
	* The JCoCo language is not line oriented. This BNF completely describes the language which has no line requirements. However, formatting a program like the disassembler output will help in the clarity of the code you are writing.

.. code-block:: text

	CoCoAssemblyProg ::= ClassFunctionListPart EOF

	ClassFunctionListPart ::= ClassFunDef ClassFunctionList

	ClassFunctionList ::= ClassFunDef ClassFunctionList | <null>

	ClassFunDef ::= ClassDef | FunDef

	ClassDef ::= Class colon Identifier [ ( Identifier ) ] BEGIN ClassFunctionList END

	FunDef ::= Function colon Identifier slash Integer ClassFunctionList ConstPart
	         LocalsPart FreeVarsPart CellVarsPart GlobalsPart BodyPart

	ConstPart ::= <null> | Constants colon ValueList

	ValueList ::= Value ValueRest

	ValueRest ::= comma ValueList | <null>

	Value ::= None | True | False | Integer | Float | String | code(Identifier) | TupleVal

	TupleVal ::= ( Value ) | ( Value ValueRest )
	(* the Scanner sees None, True, False, as Identifiers. *)

	LocalsPart ::= <null> | Locals colon IdList

	FeeVarsPart ::= <null> | FreeVars colon IdList

	CellVarsPart ::= <null> | CellVars colon IdList

	IdList ::= Identifier IdRest

	IdRest ::= comma IdList | <null>

	GlobalsPart ::= <null> | Globals colon IdList

	BodyPart ::= BEGIN InstructionList END

	InstructionList ::= <null> | LabeledInstruction InstructionList

	LabeledInstruction ::= Identifier colon LabeledInstruction | Instruction | OpInstruction

	Instruction ::= STOP_CODE | NOP | POP_TOP | ROT_TWO | ROT_THREE | ...

	OpInstruction ::= OpMnemonic Integer | OpMnemonic Identifier

	OpMnemonic ::= LOAD_CONST | STORE_FAST | SETUP_LOOP | COMPARE_OP | POP_JUMP_IF_FALSE | ...

Types
========

JCoCo supports the following types within the language.

	* The **type** type is the type of all types, including itself.
	* The **NoneType** is the type of None.
	* **bool** is the type of boolean types.
	* **int** is for integer types (implemented as a Java int, which according to documentation is 32 bits in length).
	* **float** is the type for floating point numbers (implemented as a double precision floating point number in JCoCo).
	* **str** is the type for all strings.
	* **str_iterator** is the type for iterators over strings.
	* **function** is the type of all user-defined functions.
	* **method** is the type of all user-defined methods of an instance object.
	* **built_in_function_or_method** is the type of all built-in functions or methods.
	* **range** is the type of range objects.
	* **range_iterator** is the type of range iterator objects.
	* **Exception** is the type of all exceptions.
	* **list** is the type of list objects like the original Python list objects.
	* **list_iterator** is the type of iterators over lists.
	* **funlist** is the type of functional list objects. This is a new type not supported in Python with the properties of lists from functional languages that are constructed from a head and a tail. funlist values are immutable as opposed to the list type.
	* **funlist_iterator** is the type of iterators over funlists.
	* **tuple** is type type of tuple objects. Tuples are like lists, but are immutable.
	* **tuple_iterator** is the type of iterators over tuples.
	* **dict** is the type of all dictionaries, i.e. maps.
	* **dict_keyiterator** is the type of dictionary key iterators.
	* **_io.TextIOWrapper** is the type of all files created with the open built-in function.
	* **code** is the type of code objects (i.e. functions).
	* **cell** is the type of all indirect reference objects.
	* **super** is the type of super class objects used in object instantiation in the presence of inheritance.

Methods
=============================

Magic Methods
--------------
One of the powerful features of the Python language results from methods being looked up on objects at run-time. This means that new types of objects can easily be added to the language because the virtual machine instructions presented below will polymorphically call the proper methods since lookup happens at run-time. In support of this, JCoCo, like Python, has what have traditionally been called magic methods. These methods typically begin and end with two underscores. Magic methods are used by instructions as needed. For instance, the __add__ magic method is used by the BINARY_ADD instruction.

JCoCo includes support for many of the magic methods that are defined by Python. While support is there for the whole list, not all magic methods are implemented on each type of object. The magic methods that are supported by a type of object are controlled by its type or class definition. When a magic method is called, the magic method is first looked up on the type and if it is supported, the call is made. Otherwise, an IllegalOperationException is raised. The use of magic methods is illustrated below in the descriptions of the JCoCo instructions.

The possible magic methods include the following: __cmp__, __eq__, __ne__, __lt__, __gt__, __le__, __ge__, __pos__, __neg__, __abs__, __invert__, __round__, __floor__, __ceil__, __trunc__, __add__, __sub__, __mul__, __floordiv__, __div__, __truediv__, __mod__, __divmod__, __pow__, __lshift__, __rshift__, __and__, __or__, __xor__, __radd__, __rsub__, __rmul__, __rfloordiv__, __rdiv__, __rtruediv__, __rmod__, __rdivmod__, __rpow__, __rlshift__, __rand__, __ror__, __rxor__, __iadd__, __isub__, __imul__, __ifloordiv__, __idiv__, __itruediv__, __imod__, __ipow__, __ilshift__, __iand__, __ior__, __ixor__, __int__, __long__, __float__, __bool__, __cmplex__, __oct__, __hex__, __index__, __coerce__, __str__, __list__, funlist__, __repr__, __unicode__, __format__, __hash__, __nonzero__, __dir__, __sizeof__, __getattr__, __setattr__, __delattr__, __getattribute__, __len__, __getitem__, __setitem__, __delitem__, __reversed__, __contains__, __missing__, __instancecheck__, __subclasscheck__, __call__, __copy__, __deepcopy__, __iter__, __next__, __type__, __excmatch__. The last two magic methods are specific to JCoCo. The __type__ magic method is called when the type function is called on an object. The __excmatch__ magic method is called when matching an exception in an exception handler.

Other Methods
--------------
Some additional methods are defined on some of these built-in types that are not implemented as magic methods. Here are the other defined methods in JCoCo and the types of values on which they are defined.

Dictionaries
++++++++++++++

	* **keys()** - the keys of the dictionary as a list.
	* **values()** - the values of the dictionary as a list.

Files
+++++++

	* **readline()** - reads one line from the file and returns it.
	* **write(s)** - writes *s* to the file.
	* **close()** - closes a file.

FunLists
+++++++++

FunLists are not found in Python but are defined in JCoCo to support *funlist* objects which are defined to have a head and a tail like lists in functional languages.

	* **head()** - returns the *head* or first item of a FunList.
	* **tail()** - returns the *tail* or the rest of the FunList.
	* **concat()** - returns a string resulting from concatenating all the items of the FunList together.

Lists
+++++++

	* **append(item)** - appends an item at the end of the list.

Strings
++++++++

	* **split([delim])** - splits a string into a list of strings based on the delimiter which is optional. If omitted the delim characters are whitespace (i.e. blanks, tabs, newlines).
	* **strip([delim])** - strips characters from both ends of a string that appear in delim. If omitted, delim characters are whitespace.


In addition, some objects have additional methods defined on them that are accessed like traditional method calls on objects. For instance, str objects have a split method that can be called to split a string on separator characters. Here is the list of attr methods defined in JCoCo: split, append, head, tail, concat.

Global Built-Ins
===========================
JCoCo supports the following globally available built-in functions and values.

	* **print** is a built-in function that prints a variable number of arguments to standard output, followed by a newline character, and returns None, just as print does in Python. The objects passed to print are printed by calling the __str__ magic method on each of them and appending their strings with an extra space between each pair of objects.
	* **fprint** prints exactly one argument. This is a built-in function that is specific to JCoCo and is not part of the standard Python language. It prints its argument by calling the __str__ magic method on the object to convert it to a string. This function returns itself, which can be useful when chaining together print expressions.
	* **tprint** prints exactly one argument, which may be a tuple, and returns None. tprint can be thought of as tuple print, because if a tuple is provided, the contents of the tuple are printed, separated by spaces, just as print does. However, tprint takes only one argument which may be a tuple. print takes a variable number of arguments. tprint is specific to JCoCo and is not part of the standard Python language. The values of the tuple are converted to strings using the __str__ magic method on each object. None is returned by tprint.
	* **input** is a built-in function that prints its prompt to standard output and returns one line of input as a string, just as input does in Python.
	* **iter** is a built-in function that constructs and returns an iterator over the object that is passed to it, just as Python's iter function works. This is implemented by calling the __iter__ magic method on the object.
	* **len** is a built-in function that returns the length of the sequence that is passed to it. It does this by calling the __len__ magic method on the object given to it.
	* **concat** is built-in function that returns a string representation of the elements of its sequence concatenated together. The concat function in turn calls the concat method on the object that is passed to it.
	* **int**, **float**, **str**, **funlist**, **list**, **type**, and **bool** are all calls to types. When the type is called, the corresponding magic method of __int__, __float__, __str__, __funlist__, __list__, __type__, or __bool__ is called on the object that is passed to it. In this way, the object itself is in charge of how it is converted to the specified type.
	* **range** is a call to the range type that constructs a range object over the specified range. As in Python, the range function has 1, 2, or 3 arguments passed to it, representing the start, stop, and increment of the range of integer values. The start and increment values are optional.
	* **Exception** is a call to the exception type that constructs and returns an exception object that may be raised or thrown and caught by an exception handler.
	* **super** may be called in an instance method to gain access to the base class of an object. Single inheritance is supported in JCoCo. Unlike Python, multiple inheritance is not supported.
	* **open(filename, mode)** is called to open a file for writing or reading. The *mode* is either *r* or *w*. If omitted, *r* is assumbed for the mode.
	* **sys.stdin** is pre-defined to be standard input.
	* **sys.stdout** is pre-defined to be standard output.

Virtual Machine Instructions
===================================
JCoCo implements the following instructions. These instructions are a subset of the instructions supported by the Python Virtual Machine plus a few extra instructions. There are two minor differences from the Python Virtual Machine.

	* JCoCo instructions each take up exactly one location of space. The Python Virtual Machine uses one or more bytes for each instruction and therefore some instructions are composed of multiple bytes. JCoCo does not store its instructions as bytes and therefore each instruction takes exactly one location within the JCoCo virtual machine interpreter.
	* JCoCo branch and jump instructions jump to labeled instructions. When read by the JCoCo assembler, the labels are converted to target locations which are always absolute addresses. In the Python Virtual Machine some branch instructions provide absolute addresses to jump to and others provide relative offsets to add to the current program counter. The disassembler provided with JCoCo converts all relative and absolute target addresses in jumps and branches to labels for JCoCo. This makes disassembled code more readable and compatible with the JCoCo format.

Other than these two minor differences, the implementation of the instructions is pretty faithful to a subset of the Python Virtual Machine implementation. The full Python 3.2 instruction descriptions are available at `Python 3.2 Instruction Definitions <https://docs.python.org/release/3.2.5/library/dis.html>`_.

And here are the JCoCo instruction descriptions. Again, this is a subset of the full Python 3.2 instruction set with the addition of a few extra instructions and a couple of minor differences.

In the instructions below TOS refers to the top element on the operand stack. TOS1 refers to the element on the operand stack that is second from the top. TOS2, and so on are similarly defined.

Arithmetic Instructions
-------------------------

BINARY_ADD
+++++++++++

**BINARY_ADD** implements TOS = TOS1 + TOS by making the call TOS.__add__(TOS1).

BINARY_SUBTRACT
+++++++++++++++

*BINARY_SUBTRACT** implements TOS = TOS1 - TOS by making the call TOS.__sub__(TOS1).

BINARY_MULTIPLY
++++++++++++++++

**BINARY_MULTIPLY** implements TOS = TOS1 * TOS by making the call TOS.__mul__(TOS1).

BINARY_MODULO
++++++++++++++++

**BINARY_MODULO** implements TOS = TOS1 % TOS by making the call TOS.__mod__(TOS1).

BINARY_FLOOR_DIVIDE
++++++++++++++++++++

**BINARY_FLOOR_DIVIDE** implements TOS = TOS1 // TOS by making the call TOS.__floordiv__(TOS1).

BINARY_TRUE_DIVIDE
++++++++++++++++++++

**BINARY_TRUE_DIVIDE** implements TOS = TOS1 / TOS by making the call TOS.__truediv__(TOS1).

BINARY_POWER
++++++++++++++++

**BINARY_POWER** implements TOS = TOS1 ** TOS by making the call TOS.__pow__(TOS1).

INPLACE_ADD
+++++++++++

**INPLACE_ADD** implements in-place TOS = TOS1 + TOS. Exactly the same as BINARY_ADD by making the call TOS.__add__(TOS1).

Load and Store Instructions
-----------------------------

BINARY_SUBSCR
+++++++++++++++

**BINARY_SUBSCR** implements TOS=TOS1[TOS]. This instruction provides indexing into a list, tuple, or other object that supports subscripting. This is implemented as TOS1.__getitem__(TOS).


DELETE_FAST
+++++++++++++++++++++

**DELETE_FAST(var_num)** does nothing in JCoCo which varies from the Python implementation. The purpose of this instruction seems to be implementation dependent. In the Python Virtual Machine it performs cleanup after an exception has occurred. The handling of exceptions is different in JCoCo so this instruction exists to make it work with the disassembler, but it is ignored.

LOAD_ATTR
+++++++++++++++++++

**LOAD_ATTR(var_num)**
loads the attribute named at var_num in the globals from the object found at TOS. TOS is popped and the value that the attribute maps to is pushed onto the stack.

LOAD_CLOSURE
+++++++++++++++++

**LOAD_CLOSURE(i)**
pushes a reference to the cell contained in slot i of the cell and free variable storage. The name of the variable is co_cellvars[i] if i is less than the length of co_cellvars. Otherwise it is co_freevars[i-len(co_cellvars)].

LOAD_CONST
++++++++++++++++++++

**LOAD_CONST(consti)**
pushes the constant found at index *consti* of the constants list of values onto the stack.

LOAD_DEREF
+++++++++++++++

**LOAD_DEREF(i)**
loads the cell contained in slot i of the cell and free variable storage. Pushes a reference to the object the cell contains on the stack.

LOAD_FAST
+++++++++++++++++++

**LOAD_FAST(var_num)** pushes a reference to the local found at index *var_num* onto the stack.

LOAD_GLOBAL
+++++++++++++++++++

**LOAD_GLOBAL(namei)** loads the global named co_names[namei] onto the stack.

LOAD_NAME
+++++++++++++++++++

**LOAD_NAME(var_num)** loads a value from the locals dictionary that is named in the globals at *var_num*. It pushes the loaded value onto the stack. Preference should be given to using LOAD_FAST if possible.

STORE_ATTR
+++++++++++++++++++++

**STORE_ATTR(var_num)** stores the object found at TOS1 in the object found at TOS in an attribute name found in the globals at var_num.

STORE_DEREF
++++++++++++++++

**STORE_DEREF(i)** stores TOS into the cell contained in slot i of the cell and free variable storage.

STORE_FAST
++++++++++++++++++++

**STORE_FAST(var_num)** stores TOS into the local co_varnames[var_num].

STORE_LOCALS
++++++++++++++

**STORE_LOCALS**, used during class instantiation, pops the dictionary from TOS and uses it as the locals for the currently executing function, replacing any locals dictionary already in use. The new dictionary is the attributes of the class which includes methods to be instantiated upon object instantiation for objects of the class.

STORE_NAME
+++++++++++++++++++++

**STORE_NAME(var_num)** uses the name found in the globals at *var_num* to store a named value in the locals dictionary.
Preference should be given to STORE_FAST if possible.

STORE_SUBSCR
+++++++++++++

**STORE_SUBSCR** implements TOS1[TOS]=TOS2. This instruction provides indexing into a list or other object that supports subscripting and is mutable. This instruction is implemented by calling TOS1.__setitem__(TOS,TOS2).


List, Tuple, and Dictionary Instructions
---------------------------------------------

BUILD_MAP
+++++++++++++++++++++++++++++

**BUILD_MAP(initial_capacity)** creates an empty dictionary object and pushes it onto the stack. The initial capacity is ignored by JCoCo.


STORE_MAP
++++++++++++++++++++

**STORE_MAP** performs TOS2[TOS]=TOS1. TOS1 is the value to be stored at key TOS in dictionary TOS2.

BUILD_TUPLE
++++++++++++++++++++

**BUILD_TUPLE(count)** creates a tuple consuming count items from the stack, and pushes the resulting tuple onto the stack.

SELECT_TUPLE
++++++++++++++++++++

**SELECT_TUPLE(count)** pushes the contents of the tuple with count elements onto the operand stack. The count must match the tuple's size or an illegal operation exception will be thrown. The elments of the tuple are pushed so the left-most element is left on the top of the stack. This instruction is not part of the Python Virtual Machine. It is JCoCo specific.

BUILD_LIST
++++++++++++++++++

**BUILD_LIST(count)** works as BUILD_TUPLE, but creates a list.

BUILD_FUNLIST
++++++++++++++++++

**BUILD_FUNLIST** works as BUILD_TUPLE, but creates a functional list (i.e. a funlist).

SELECT_FUNLIST
+++++++++++++++++

**SELECT_FUNLIST**
pushes the head and the tail of a funlist found at TOS onto the operand stack. The funlist found at TOS is popped from the stack. The head of the list is left on the top of the operand stack. The tail is below it on the stack. This instruction is JCoCo specific.

CONS_FUNLIST
+++++++++++++

**CONS_FUNLIST** pops two elements from the operand stack. TOS should be a funlist and TOS-1 should be an element. The instruction create a new funlist from the two pieces with TOS-1 the head and TOS the tail of the new list. It pushes this new list onto the operand stack. This instruction is JCoCo specific.

Class Creation Instructions
-----------------------------

LOAD_BUILD_CLASS
+++++++++++++++++

**LOAD_BUILD_CLASS** loads the built-in function for building classes onto the operand stack. This function, when called, takes two arguments. The name of the class must be at TOS. The function that will instantiate the class must be at TOS1. When called with CALL_FUNCTION, the built-in function for building classes will be at TOS2. The built-in function leaves the instantiated class on the top of the stack.

Stack Manipulation Instructions
---------------------------------

POP_TOP
++++++++

**POP_TOP** removes the top-of-stack (TOS) item.

ROT_TWO
++++++++

**ROT_TWO**
swaps the two top-most stack items.

DUP_TOP
+++++++++

**DUP_TOP** duplicates the reference on top of the stack.

Conditional and Iterative Execution Instructions
--------------------------------------------------

GET_ITER
++++++++++

**GET_ITER** implements TOS=iter(TOS).

BREAK_LOOP
+++++++++++

**BREAK_LOOP** terminates a loop due to a break statement.

POP_BLOCK
++++++++++

**POP_BLOCK** removes one block from the block stack. Per frame, there is a stack of blocks, denoting nested loops, try statements, and such.

POP_EXCEPT
++++++++++++

**POP_EXCEPT** removes one block from the block stack. The popped block must be an exception handler block, as implicitly created when entering an except handler. In addition to popping extraneous values from the frame stack, the last three popped values are used to restore the exception state.

END_FINALLY
+++++++++++++

**END_FINALLY** terminates a finally clause. The interpreter recalls whether the exception has to be re-raised, or whether the function returns, and continues with the outer-next block.

COMPARE_OP
+++++++++++++++++++

**COMPARE_OP(opname)** performs a Boolean operation. Both TOS1 and TOS are popped from the stack and the boolean result is left on the operand stack after the execution of this instruction. opname is an integer corresponding to the following comparisons.

	* 0 :TOS1 < TOS or TOS1.__lt__(TOS)
	* 1 :TOS1 <= TOS or TOS1.__le__(TOS)
	* 2 :TOS1 = TOS or TOS1.__eq__(TOS)
	* 3 :TOS1 != TOS or TOS1.__ne__(TOS)
	* 4 :TOS1 > TOS or TOS1.__gt__(TOS)
	* 5 :TOS1 >= TOS or TOS1.__ge__(TOS)
	* 6 :TOS contains TOS1 or TOS.__contains__(TOS1)
	* 7 :TOS1 not in TOS or TOS.__notin__(TOS1)
	* 8 :TOS1 is TOS or TOS.is__(TOS1)
	* 9 :TOS1 is not TOS or TOS.is_not(TOS1)
	* 10 : exception TOS1 matches TOS or TOS1.__excmatch__(TOS)

JUMP_FORWARD
+++++++++++++++++++++

**JUMP_FORWARD(target)** sets the Program Counter (i.e. PC) to target.

POP_JUMP_IF_TRUE
++++++++++++++++++++++++

**POP_JUMP_IF_TRUE(target)**
checks TOS. If true it sets the Program Counter (i.e. PC) to target. TOS is popped.

POP_JUMP_IF_FALSE
++++++++++++++++++++++++++

**POP_JUMP_IF_FALSE(target)** checks TOS. if TOS is false it sets the Program Counter (i.e. PC) to target. TOS is popped.

JUMP_ABSOLUTE
++++++++++++++++++++++

**JUMP_ABSOLUTE(target)** sets the Program Counter (i.e. PC) to target.

FOR_ITER
+++++++++++++++++

**FOR_ITER(target)** pops TOS and expects it to be an iterator. The instruction calls the iterators __next__() method. If this yields a new value, the iterator and the value are pushed on the stack (leaving the iterator below the value). If the iterator indicates it is exhausted the iterator remains popped, and the PC is set to target.

SETUP_LOOP
++++++++++++++++++

**SETUP_LOOP(target)** pushes a block for a loop onto the block stack. The block spans from the current instruction to target.

SETUP_EXCEPT
+++++++++++++++++++++

**SETUP_EXCEPT(target)** pushes a try block from a try-except clause onto the block stack. target points to the first except block.

SETUP_FINALLY
+++++++++++++++++++++++

**SETUP_FINALLY(target)** pushes a try block from a try-except clause onto the block stack. target points to the finally block.

RAISE_VARARGS
+++++++++++++++++++++

**RAISE_VARARGS(argc)** varies from the Python version slightly. In JCoCo the argc must be one. This is because exceptions in JCoCo automatically contain the traceback which is not necessarily the case in the Python Virtual Machine. The argument on the stack should be an exception. The exception is thrown by this instruction.

Function Execution Instructions
---------------------------------
RETURN_VALUE
+++++++++++++

**RETURN_VALUE** returns with TOS to the caller of the function.

CALL_FUNCTION
++++++++++++++++++++

**CALL_FUNCTION(argc)** calls a function. The low byte of argc indicates the number of positional parameters, the high byte the number of keyword parameters. On the stack, the opcode finds the keyword parameters first. For each keyword argument, the value is on top of the key. Below the keyword parameters, the positional parameters are on the stack, with the right-most parameter on top. Below the parameters, the function object to call is on the stack. Pops all function arguments, and the function itself off the stack, and pushes the return value.

MAKE_FUNCTION
++++++++++++++++++++

**MAKE_FUNCTION(argc)** pushes a new function object on the stack. TOS is the code associated with the function. The function object is defined to have argc default parameters, which are found below TOS.

MAKE_CLOSURE
++++++++++++++++++++

**MAKE_CLOSURE(argc)**
creates a new function object, sets its __closure__ slot, and pushes it on the stack. TOS is the code associated with the function, TOS1 the tuple containing cells for the closure’s free variables. The function also has argc default parameters, which are found below the cells.

Special Instructions
----------------------
BREAK_POINT
+++++++++++++++

**BREAK_POINT** drops you into the interactive debugger.

Acknowledgements
===================
I would like to thank Jonathan Opdahl for his work in prototyping this Java version of the CoCo virtual machine. Thank you, Jon, for your work and interest in working with me to solve some of the design challenges we came up against and for your hard work in converting a significant portion of this virtual machine from C++ to Java.

.. Contents:

.. .. toctree::
..    :maxdepth: 2



.. Indices and tables
.. ==================

.. * :ref:`genindex`
.. * :ref:`modindex`
.. * :ref:`search`

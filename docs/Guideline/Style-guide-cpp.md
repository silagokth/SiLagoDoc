# C++ Style Guide

## Basics

In general, all C++ code should follow the [LLVM Style](https://llvm.org/docs/CodingStandards.html).

The preferred editor for C++ program is [Visual Studio Code](https://code.visualstudio.com/). It works on all major operating systems.

## Package Organization

The package should be organized in the following way:

```
ROOT_DIR/
  +--README.md
  +--LICENSE.md
  +--CMakeLists.txt
  +--config/
      +--[some files]
  +--src/
      +--[some files]
  +--test/
      +--[some files]
  +--build/
      +--[some files]
```

## Naming Convention

Each class ``Foo`` is defined by a header file ``Foo.hpp`` and a source file ``Foo.cpp``. Class name will uses capital letters to disdinguish each words. For example, ``FooBar`` is a valid class name.

!!! example
	``` c++
		class FooBar {
			...
		};
	```

Each header file will have a gardian macro. The gardian marcro will be the logic path of the header file. The macro should be in upper case letters and seperated by underscore symbol (``_``). It should also begin and end with double underscore symbols (``__``).

!!! example
	``` c++
	#ifndef __PRIMARY_NAMESPACE_SECONDARY_NAMESPACE_FOO_BAR_HPP__
	#define __PRIMARY_NAMESPACE_SECONDARY_NAMESPACE_FOO_BAR_HPP__
	...
	#endif // __PRIMARY_NAMESPACE_SECONDARY_NAMESPACE_FOO_BAR_HPP__
	```

One should use hierarchical namespaces if required. There is no limitation on the level of namespace nor the amount of namespaces in each level. Name space should be all lower case letters. When enclose a piece of code with hierarchical namespaces, write them in seperate bracks.

!!! note
	You shouldn't put indentation space for namespaces.

!!! example
	``` c++
	namespace foo{
	namespace bar{
	...
	}
	}
	```

Macro names in general should be in all upper case letters and seperated by underscore symbol.

!!! example
	``` c++
	#define MACRO_ONE 1
	#define MACRO_TWO 2
	```

Function names in general should be in all lower case letters and seperated by underscore symbol.

!!! example
	``` c++
	int FooBar::foo_bar(){
		...
	}
	```

Variable names should be in all lower case letters and seperated by underscore symbol. If the variable is a private or protected variable in a class, it should also start with an underscore symbol. Public variables should not start with underscore symbol. If the variable is the input argument of a function, it should also end with an underscore symbol.

!!! example
	``` c++
		class FooBar {
		private:
			int _var_1;
		public:
			float var_2;
		public:
			void func_1(int arg_1_, int arg_2_);
		};
	```

There is no rule to name temporary variables. As long as the variable name does not confuse readers, it can be accepted. However, there are some tradition of naming temporary variables one should follow. For example, ``i``, ``j``, ``k`` are usually reserved for loop iterators.

!!! example
	``` c++
		int func_1(){
			int a;
			for(int i=0; i<5; i++){
				int b = 2;
				int a = a+i;
			}
			return a;
		}
	```
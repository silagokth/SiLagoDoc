!!! warning
    Documentation is not complete!

# Quick guide for code documentation

SiLago project try to use doxygen to generate reference manual for its source
code. The comment is crutial for the quality of generated documentation. Therefore,
we make some guidelines for commenting the source code.

## C++

### Comment environment

Use QT's commenting style to form a doxygen readable comment block. As shown in
the example below.

```c++
/*!
 * This is a block comment
 */
```

Single line comment is also allowed and should using style like this:

```c++
//! This is a single line comment
```

### Comment a file
Every header file should be documented with a short description about the role of
this file.

Additional information like author, license, modification history, etc should also
be included inside the comment block.

Example:

```c++
/*!
 * \file Global.hpp
 *
 * Defines all the global variables.
 *
 * Author: author <author@domain.com>
 * Licese: MIT
 * Modification:
 *   2017-01-03    author        created
 *   2017-02-05    user1         add feature of xxx
 *   2017-02-07    user2         fix bug xxx
 */
```


### Comment a class

Every class should be documented right before its defination. Description of the
function of the class is very important.

Example:

```c++
/*!
 * Description of class A
 */
 class A : public B{
   ....
 };
```

### Comment a method/function

Method function should be documented right before its declaration. The description,
parameters, return type should be included inside the comment block.

Example:

```c++
 class A : public B{
 public:
   /*!
    * Constructor
    */
   A();
   
   /*!
    * Some other function
    * 
    * \param p0 some input
    * \param p1 the second input
    * \return the calculate result
    */
   int func(int p0, int p1);
 };
```


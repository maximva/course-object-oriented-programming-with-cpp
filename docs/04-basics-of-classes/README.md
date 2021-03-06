---
description: This chapter will introduce the basics classes in the C++ language.
title: 04 - Basics of Classes
---

# Chapter 04 - Basics of Classes

Classes are the central feature of C++ that support the Object Oriented Programming paradigm. They are often called user-defined types. Classes are the representation of elements from the problem space inside the solution space. They combine both **data** (attributes) and **behavior** (methods) into one neat little package (also known as **encapsulation**).

In C++ the attributes and methods within a class are called the **members** of that class.

## Class Definition

To define a class is to create a sort of blueprint for objects of that class. A class definition does not actually reserve any memory. It does however inform the compiler:

* what the **name of the class** is;
* what **data an object** of the class will hold - called the **attributes**;
* and what **operations** can be performed on objects of the class - called the **methods**.

A class **definition** in C++ is constructed using the format shown below:

```cpp
class <name_of_class> {
};
```

For example:

```cpp
class Apple {
};
```

A class definition consists of the following important parts:

* the **class** keyword
* the **name of the class**
  * *PascalCased* starting with capital letter
  * Make sure to use sane and clear names for your classes. This is considered a good programming skill
  * Most often a Noun (Car, Point, Student, Record, Cat, Animal, ...)
* Two **curly brace** `{}`
  * Data and behavior will be defined between these curly braces.
* A termination **semicolon** `;`

<!-- Declaration is when we specify the name of the class but no definition - typical used for forward declaration -->

## Header files

The class definition can be placed before your main function in C++ as shown below.

```cpp
#include <iostream>

class Apple {
};

int main(void) {
  return 0;
}
```

This will however make for a terrible mess once your program starts to become bigger. Secondly, it is not possible to share classes like this between different programs without copy pasting the code from one application to another. And that's a big no-no.

For these reasons, class definitions will always be placed in separate files called **header files**. These files carry the same name as the class, though mostly in lower case letters (preferred *snake_case*, although not mandatory), and have the extension ".h". Take for example a class called `RgbLed`, the header file would be called `rgb_led.h`.

::: warning Case-sensitivity
Do take note that where Windows is not case-sensitive, other operating systems might be. Take for example Linux. This means that if you are not careful when naming and including your files, you might have a working application on Windows but not on Linux.
:::

Let's continue with a class `Color`, where the class definition is placed inside a file called `color.h`.

```cpp
// color.h
class Color {
};
```

However if you want to create objects of your class somewhere else you will need to tell the compiler where to find the class definition. This can be achieved by **including the header file** using a **preprocessor** `#include` directive.

```cpp
#include <iostream>   // Include standard/external libraries
#include "color.h"    // Include project header files

using namespace std;

int main(void) {
  //...
}
```

As can be seen from the previous example code, there are different ways to use an `#include` directive.

* When using `<....>` with an `#include` directive you are telling the compiler to search for the header file within the **standard libraries of C++** and within the include paths made available to the compiler (for example externally installed libraries).
* When using `"...."` with an `#include` directive you are telling the compiler to search for the header file within the **current project directory**.

### Include Guards

When including files, the preprocessor will actually take the content from the header-file and replace the `#include` directive with the content.

::: tip Intermediate files
You can actually test this by telling the compiler to save the intermediate files that are generated by the different compiler tools. Pass the argument `-save-temps` to the `g++` compiler and you will be able to access the intermediate files. For example the `.ii` files are the result files of the preprocessor. Open the `.ii` file and take a look how big it has gotten.
:::

Now what happens if you include the same header file multiple times? Actually C++ states that a variable, a function, a class, ... can only be defined once in a single application. This is also known as the **One-Definition Rule (ODR)**. When the linker is uniting all the object modules, it will complain if it finds more than one definition for the same variable/function/class/...

This is where **include guards** come into play. These are a safety mechanism that will make the preprocessor only include header files that have not been included yet. A typical include guard looks something like this:

```cpp
// color.h
#ifndef _HEADER_COLOR_
#define _HEADER_COLOR_

class Color {

};

#endif
```

Basically it does as the directives state. If the label `_HEADER_COLOR_` has not yet been defined, then define it, include the class definition and end it. If the label has already been defined than the class definition is not included anymore. The label can be chosen freely by the developer.

These include guards are **only of importance for the preprocessor**, therefore they are prefixed with a hashtag `#`.

One disadvantage of the include guards as shown above it that you need to declare a **label which must be unique** throughout your whole program (including all libraries you include). Because of this, a new system was introduced using the `#pragma once` directive. This directive tells the preprocessor to only include the file once.

Rewriting the previous example using the `#pragma once` directive looks like this:

```cpp
// color.h
#pragma once

class Color {

};
```

It also makes the code shorter and more clear.

## Always use Namespaces

When defining classes one should **always place them inside a namespace**. This can be achieved by surrounding the class definition with a namespace definition as shown in the next code snippet.

```cpp
// color.h
#pragma once

namespace Visual {

  class Color {

  };

};
```

Note that one doesn't have to invent a namespace name for every class. A namespace can hold an unlimited number of classes/functions/structs/... **Namespaces can even include other namespace**, allowing for a hierarchical namespace structure.

::: warning Unique Namespace
Make sure that the name of your namespace is unique if possible. The chances of including a library that has the same namespace name is low but not uncommon. Just make sure to name namespaces differently than your classes, otherwise their might be ambiguity between the types.
:::

## Adding Data - Attributes

**Attributes** or **class instance variables** are the way to **store data inside an object** of that particular class.

Defining attributes is very similar to creating a variable inside a method or function. Let's add red, green and blue components to the `Color` class.

```cpp
// color.h
#pragma once

namespace Visual {

  class Color {

    // Attributes
    private:
      int red = 0;
      int green = 0;
      int blue = 0;

  };

};
```

::: warning Initialize Attributes
Make sure to always initialize your attributes as shown in the previous example using direct initialization or use a constructor. Never leave attributes uninitialized as they will contain undefined values.
:::

Notice that an **access modifier** (such as `private`, `public` and `protected`), followed by a colon `:`, can be specified for all the attributes/methods that follow it. **Default access in C++ classes is** `private`. This means that not specifying anything would have had the same effect in the previous example. However it is always safer to explicitly declare the attributes as `private` as it may lead to leaks when adding `public` things before the attributes.

**Data hiding** is an important feature of OOP. It allows developers to prevent direct access to internal representation of a class. In other words access to class members can be restricted making only the available those that are needed by the user of the class. This is facilitated by the access modifiers.

Another important concept is that other classes should not depend on data but rather on behavior. So its better to make data private and the required methods public.

<!-- More info about the concept above => SOLID -->

C++ has the following definitions for the access modifiers inside a class definition:

* `public`: A public member is **accessible from anywhere outside** the class. Attributes should almost never be made public, to prevent direct access from outside the class. Methods such as getters, setters, constructors and such are often made public as they need to be accessible.
* `private`: A private member **cannot be accessed from outside** the class (not even read - in case of an attribute). Only the class and friend functions/methods can access private members. **Not even an inherited class can access** it's parent private attribute/methods.
* `protected`: A protected attribute or method is very **similar to a private** member but it provided one additional benefit - that is that they **can be accessed in child classes** (aka derived classes).

A class can have multiple `public`, `protected`, or `private` access modifiers. Each modifier remains in effect until either another modifier or the closing brace of the class body is seen. The default access for members and classes is `private`.

Analyze the example below. The comments show what access is granted to each section.

```cpp
class Foo {
  //... default is private here

  protected:
    //... all protected here

  private:
    //... all private here

  public:
    //... all public here

  protected:
    //... all protected here

  public:
    //... again public
};
```

## Adding Behavior - Methods

While attributes store the data of our objects, methods allow objects to have behavior. In C++ the declaration and actual definition of methods are separated. Inside the class definition we put the declaration of method, also known as the method **prototype**.

The prototype of a method takes on the following form:

```cpp
<return_type> <name>(<comma_separated_argument_list);
```

This consists of:

* The return type of a method can be `void` (nothing to return), a primitive type such as `int`, `char`, `double`, ..., a pointer, a custom type such as a class, an enum, ...
* The name of a method should be a very clear and indicating name of what the actual method does. There are no real naming conventions in C++ as there are in Java. The most important rule is to stay consistent.
* A comma separated list of the arguments, and for the prototype the only thing that is mandatory are the types. This means that you actually don't have to state the names. However most of the time is good practice to do it anyway. Especially to a user of your class.

Let's for example declare a method called `set_color` that does not return anything (the return type is therefore `void`). It does however take 3 arguments, more specifically the red, green and blue colors. Again as with the attribute, we can specify the access modifier for all method declarations following.

```cpp
// color.h
#pragma once

namespace Visual {

  class Color {

    // Methods
    public:
      void set_color(int red, int green, int blue);

    // Attributes
    private:
      int red = 0;
      int green = 0;
      int blue = 0;

  };

};
```

Notice how the private attributes are placed at the bottom of the class definition. This is not a requirement. It is however often done by developers as the public members are more important for a user of the class, so they should be encountered first.

### Separating method declaration from definition

The header file contains the class definition and the class contains both the attribute definitions and method declarations. This makes for a clear separation between method declaration and actual implementation (definition).

Why? Because when we supply a class to a user of that class we may not want to provide the actual implementation as readable code. We may just wish to provide the compiled object code. However for the compiler and linker of the user to be able to use our class it will actually need to know what the class looks like - it **needs to know the interface of that class**, because the compiler performs all sort of checks to make sure you use the class as intended. This is where the header file comes into play. The user of the class will provide both the header file and the object file to the compiler and linker, allowing it to be integrated into his/her program.

::: warning Inline methods
Do note that it is also possible to place the definition of a method inside the header file. This is called inline methods. The inline methods are a C++ enhancement feature to increase the execution time of a program. When compiling the application, the compiler can be instructed to make methods inline, meaning that a method call in code is actually replaced with the code inside of that method. This does not mean that it is a good idea to make every method inline, as this will be a burden on the actual memory usage of your class. Actually, compilers these days are smart enough to decide for themselves if they should make methods inline or not.
:::

Let us start by implementing a method that allows us to change the color of a `Color` object. This is called a **setter method** because it does nothing else but set a part of the state of the object. To actually implement the `set_color` method, we will need to create a file with the same name as the header file, excluding the extension, which should now be `.cpp`.

```cpp
// color.cpp
#include "color.h"

namespace Visual {

  void Color::set_color(int red, int green, int blue) {
    this->red = red;
    this->green = green;
    this->blue = blue;
  }

};
```

A lot can be said about the code above, so let's start.

In the example above, the name of the file is specified in comments. While some programmers do this, mostly it is a bad idea. It is a comment that may become misleading and stand in the way of change (changing the name of file if ever needed). Every decent editor will display the name of the file you are working in. It is only done here to make it more clear to you as a reader of this course.

The first actual line of code is `#include "color.h"`, an include directive. It is necessary to include the class definition when defining the methods of that class.

Next the namespace is stated again. This is required as the actual method definition must also be placed inside of the namespace. Note again that after the closing curly brace of the namespace `{`, a semicolon `;` is required.

Next is the method definition. The signature is almost the same as the prototype except for the **scope resolution operator** `::` prefixed with the name of the class. This tells the compiler that the method definition that follows belongs to the specified class. Here the names of the arguments are mandatory, contrary to the method prototype.

The last part of this definition is a C++ block indicated by the parentheses `{}`. Inside of these parentheses the implementation of the method is specified. Here we just save the values provided as arguments to the method, inside the attributes that we defined in our class definition. Because both the arguments and the attributes have the same names, we need to use `this->` when we want to use the attributes.

If we had used `red = red`, it would have resulted in the arguments being assigned to themselves. This because of the fact that the scope of the arguments is closer by and would of overruled the attributes.

<!-- See [todo @ chapter TODO] for more info on scope. -->

No access modifier needs to be specified here for the definition of the methods. This is only done inside the class definition.

Let us expand this example with methods that return the values of the different colors. Since these methods do not change the state of the object and just return a part of it's state to the outside world, they are called **getter methods**.

```cpp
// color.h
#pragma once

namespace Visual {

  class Color {

    // Methods
    public:
      void set_color(int red, int green, int blue);

    public:
      int get_red(void);
      int get_green(void);
      int get_blue(void);

    // Attributes
    private:
      int red = 0;
      int green = 0;
      int blue = 0;

  };

};
```

::: tip Getters
Often getter methods are named with a prefix `get_`. This is however not mandatory. C++ does not allow methods to have the same name as attributes, otherwise the methods could been called `red`, `green` and `blue` which would be preferred. That is why some developers name attributes starting with an `m` from member or `_` to differentiate between attributes and arguments or methods.
:::

As a next step it is required to give the methods an implementation.

```cpp
// color.cpp
#include "color.h"

namespace Visual {

  void Color::set_color(int red, int green, int blue) {
    this->red = red;
    this->green = green;
    this->blue = blue;
  }

  int Color::get_red(void) {
    return red;
  }

  int Color::get_green(void) {
    return green;
  }

  int Color::get_blue(void) {
    return blue;
  }

};
```

Note that for the getters there is no ambiguity for the attributes. Therefore, we do not need to explicitly specify `this->`, however it is not wrong to do so. Just more typing work. Basically when a getter method of an object is called, a value is returned.

## Creating Objects

To create objects on the stack, the same syntax can be used as for creating variables of a primitive type.

So to create a `Color` class instance (aka object), the following code can be used.

```cpp
Color yellow;
```

Important to note is that you will need to include the header file that contains the class definition before this will work. You will also need to add a `using namespace Visual` or specify the namespace before the classname using the scope resolution operator, for example `Visual::Color lime`.

```cpp
#include <iostream>
#include "color.h"

using namespace std;
using namespace Visual;

int main()
{
  cout << "Creating colors" << endl;

  Color yellow;

  return 0;
}
```

While this is pretty awesome, the code above doesn't do a lot. As stated before, an OOP program is collection of objects that send messages to each other. These messages are actual requests made to the objects to show of their behavior. So in other words, we need to make method calls to the objects to make them perform actions.

Let us set the color of the `yellow` color using the setter method and retrieve it back using the getter methods.

```cpp
#include <iostream>
#include "color.h"

using namespace std;
using namespace Visual;

int main()
{
    cout << "Creating the color yellow" << endl;

    Color yellow;

    yellow.set_color(255, 255, 0);

    cout << "Yellow has the following components:" << endl;
    cout << "R: " << yellow.get_red() << endl;
    cout << "G: " << yellow.get_green() << endl;
    cout << "B: " << yellow.get_blue() << endl;

    return 0;
}
```

Methods of objects can be called by using the member operator `.`. The color of the `yellow` object can be set by specifying **literal** values as arguments or by passing the names of variables that hold an integer value.

:::tip Literals
A literal is some data that's presented directly in the code, rather than indirectly through a variable or method call. Some examples are `"Hello World"`, `15`, `0x05`, `'a'`, ... The data constituting a literal cannot be modified by a program, but it may be copied into a variable for further use.
:::

The code below prints out the values of the color components by retrieving them via the getter methods. They could also have been saved in temporary variables first and then used for displaying.

```cpp
cout << "R: " << yellow.get_red() << endl;
cout << "G: " << yellow.get_green() << endl;
cout << "B: " << yellow.get_blue() << endl;
```

To compile this application one will need to supply both implementation files to the compiler using the following command:

```bash
g++ main.cpp color.cpp -o colors
./colors
```

::: codeoutput
<pre>
Creating the color yellow
Yellow has the following components:
R: 255
G: 255
B: 0
</pre>
:::

## Constructors

The attributes in the Color example class have already been initialized to a default value of 0. However when creating a `Color` object it would be much cleaner if it could be initialized on creation with the correct color.

This is where constructors come into play. Constructors are

* methods that carry the **exact same name as the class**
* have **no return type**

The main purpose of a constructor is to **initialize new objects and put them in a valid state, ready for first use**.

Let's create a basic constructor for the `Color` class that initializes the color components to a given. First a prototype needs to be added to the class definition.

```cpp
// color.h
#pragma once

namespace Visual {

  class Color {

    // Constructors
    public:
      Color(int red, int green, int blue);

    // Methods
    public:
      void set_color(int red, int green, int blue);

    public:
      int get_red(void);
      int get_green(void);
      int get_blue(void);

    // Attributes
    private:
      int red = 0;
      int green = 0;
      int blue = 0;

  };

};
```

The constructor here takes three arguments, namely the color components. While it is not mandatory to split up the constructors and methods, it often creates a clearer image for the user of the class.

The implementation of the constructor can actually use the `set_color()` method to implement it behavior. That's DRY!

```cpp
// color.cpp
#include "color.h"

namespace Visual {

  Color::Color(int red, int green, int blue) {
    set_color(red, green, blue);
  }

  void Color::set_color(int red, int green, int blue) {
    this->red = red;
    this->green = green;
    this->blue = blue;
  }

  int Color::get_red(void) {
    return red;
  }

  int Color::get_green(void) {
    return green;
  }

  int Color::get_blue(void) {
    return blue;
  }

};
```

Now when creating `Color` objects, the components can be set when the object is instanciated.

```cpp
#include <iostream>
#include "color.h"

using namespace std;
using namespace Visual;

int main()
{
    cout << "Creating the color yellow" << endl;

    Color yellow(255, 255, 0);

    cout << "Yellow has the following components:" << endl;
    cout << "R: " << yellow.get_red() << endl;
    cout << "G: " << yellow.get_green() << endl;
    cout << "B: " << yellow.get_blue() << endl;

    return 0;
}
```

### Default Constructors

There is one thing missing from this implementation and that is a default constructor.

Just as in Java, C++ generates a default constructor (one without arguments) for you if you do not define any constructor yourself. At the moment you define a constructor inside the class, even if not a default constructor, you lose this functionality from the compiler.

This means that before we created the constructor with the color compoments, the C++ compiler generated a constructor for us, which we lost when we added our own constructor.

This basically means that the original instantiation of a `Color` object will fail:

```cpp
Color white;        // Fails, no default constructor
```

Luckily in C++, a class can have more than one constructor, as long as each has a different list of arguments. This concept is known as **constructor overloading** and is quite similar to method/function overloading.

* Overloaded constructors essentially have the same name (name of the class) and different number of arguments.
* A constructor is called depending upon the number and type of arguments passed.
* While creating the object, arguments must be passed to let compiler know, which constructor needs to be called.

So to add a default constructor to the `Color` class we first need to declare it in the class:

```cpp
// color.h
#pragma once

namespace Visual {

  class Color {

    // Constructors
    public:
      Color(void);    // Default constructor
      Color(int red, int green, int blue);

    // Methods
    public:
      void set_color(int red, int green, int blue);

    public:
      int get_red(void);
      int get_green(void);
      int get_blue(void);

    // Attributes
    private:
      int red = 0;
      int green = 0;
      int blue = 0;

  };

};
```

The implementation of the default constructor can actually be left empty because the attributes are already being initialized to zero. It can however be argued that it's cleaner to call the `set_color()` method using three zero values.

<!-- Should we introduce constructor initialization list here or how to call other constructors? -->

```cpp
// color.cpp
#include "color.h"

namespace Visual {

  Color::Color(void) {
  }

  Color::Color(int red, int green, int blue) {
    set_color(red, green, blue);
  }

  void Color::set_color(int red, int green, int blue) {
    this->red = red;
    this->green = green;
    this->blue = blue;
  }

  int Color::get_red(void) {
    return red;
  }

  int Color::get_green(void) {
    return green;
  }

  int Color::get_blue(void) {
    return blue;
  }

};
```

Now both constructors can be used to create objects of the class `Color`:

```cpp
Color yellow(255, 255, 0);      // Init constructor
Color white;                    // Default constructor
```

::: warning Default Constructor Parentheses
Don't place parenthes `()` after the object name when creating an instance using the default constructor. This will throw off the C++ compiler and make it think you are declaring a function `white()` that returns an object of type `Color`. Welcome to the world of C++ :).
:::

# Interview-Note

__cpp篇__

## 多态的含义：

  一个对象能够以多种形式表现的特性。通过在基类中声明虚函数，派生类可以重写（Override）这些虚函数，并根据自身的实现提供不同的行为。

## 虚函数：

  虚函数表是在包含虚函数的类被编译时创建的。对于包含虚函数的类，编译器会在其虚函数表中生成一组指向虚函数的指针。当类的对象被创建时，对象中通常会包含一个指向虚函数表的指针（即虚指针）。这个指针被称为虚表指针（vptr），它指向该类的虚函数表。  当通过基类指针或引用调用虚函数时，实际调用的是对象所属类的虚函数表中对应的函数。如果派生类重写了基类的虚函数，那么派生类中的函数地址会被放入虚函数表的对应位置，而不是基类的函数地址。这样，在运行时，通过基类指针或引用调用虚函数时，会根据对象的实际类型来确定调用哪个函数，从而实现了多态性。

## 虚基类：

  虚基类通常用于解决菱形继承（Diamond Inheritance）中的问题。菱形继承是指派生类同时继承自两个或多个直接或间接共同的基类，导致在派生类中存在多个实例的同名成员变量或函数，引发歧义。

```cpp
    class Base {
public:
    int x;
};

class Derived1 : virtual public Base {
public:
    int y;
};

class Derived2 : virtual public Base {
public:
    int z;
};

class DerivedDerived : public Derived1, public Derived2 {
public:
    int w;
};

```

在这个例子中，Derived1 和 Derived2 类都将 Base 声明为虚基类。这样，DerivedDerived 类中只包含一个 Base 对象的实例。

## 抽象类：

  抽象类是一种不能被实例化的类，它用于定义接口和提供部分实现，但不完整或不具体到足以创建对象。抽象类通常包含纯虚函数（Pure Virtual Function），这些函数没有实际的实现，而是用 = 0 进行声明。一个类如果包含了至少一个纯虚函数，那么它就是抽象类。

## 构造函数or析构函数可以是虚函数吗：

  构造函数不能是虚函数，因为在对象创建时，调用的构造函数是根据对象的静态类型而不是动态类型来确定的。
  虚析构函数允许通过基类指针删除派生类对象时调用正确的析构函数，确保派生类对象的析构函数被正确调用，从而避免内存泄漏。

```cpp
#include <iostream>

class Base {
public:
    virtual ~Base() {
        std::cout << "Base Destructor" << std::endl;
    }
};

class Derived : public Base {
public:
    ~Derived() {
        std::cout << "Derived Destructor" << std::endl;
    }
};

int main() {
    Base* ptr = new Derived();
    delete ptr;

    return 0;
}
```

在这个例子中，基类 Base 的析构函数被声明为虚析构函数，因此当我们通过基类指针删除派生类对象时，会依次调用派生类 Derived 和基类 Base 的析构函数。这样就确保了派生类和基类中的资源被正确释放。
如果不使用虚析构函数，就会直接调用 Base 的析构函数，导致 Derived 类中的资源没有得到释放。

## STL、线程安全：

  在C++标准库（STL）中，并没有提供对多线程操作的内建支持，因此大部分STL容器和算法都不是线程安全的。这意味着如果多个线程同时访问和修改同一个STL容器，可能会导致未定义行为或数据损坏。
  然而，C++11引入了std::mutex、std::lock_guard等线程支持库，使得我们可以通过加锁等机制来保护STL容器以实现线程安全。除此之外，C++17还引入了一些并行算法，如std::for_each、std::transform等，这些算法可以在多线程环境下进行操作。
  有一些库提供了线程安全的STL容器的实现，比如Boost库中的boost::thread_safe::vector、boost::thread_safe::map等。这些容器通常使用了锁或其他同步机制来确保在多线程环境中的安全使用。
  总之，在使用STL容器和算法时，如果涉及到多线程操作，一定要注意线程安全性，并采取相应的措施来保护数据结构的完整性。

## 迭代器有哪些类型：

  在C++标准库中，迭代器（Iterator）是一种用于遍历容器（例如vector、list、map等）中元素的对象。迭代器提供了一种统一的访问容器元素的方式，无论容器的类型如何，都可以使用迭代器进行遍历和操作。根据功能和操作方式的不同，C++中的迭代器可以分为以下几种类型：

1. Input Iterator（输入迭代器）：
   用于读取容器中的元素。
   支持单次遍历，不支持多次遍历。
   只能递增，不能递减。
   用于++、==、!=等操作。
2. Output Iterator（输出迭代器）：
   用于向容器中写入元素。
   支持单次遍历，不支持多次遍历。
   只能递增，不能递减。
   用于++、=等操作。
3. Forward Iterator（前向迭代器）：
   比输入迭代器功能更强，支持多次遍历。
   支持读取和写入元素。
   可以递增，但不能递减。
   支持++、==、!=等操作。
4. Bidirectional Iterator（双向迭代器）：
   比前向迭代器功能更强，支持逆向遍历。
   可以递增和递减。
   支持++、--、==、!=等操作。
5. Random Access Iterator（随机访问迭代器）：
   比双向迭代器功能更强，支持随机访问容器中的元素。
   可以进行任意位置的跳转。
   支持递增和递减，以及随机访问。
   支持++、--、+、-、[]等操作。
   这些迭代器类型形成了一个迭代器概念的层次结构，即越高级的迭代器支持的功能越多。在使用STL容器和算法时，根据需要选择适当的迭代器类型，以提高代码的效率和可读性。

## 左值和右值：

```cpp
#include <iostream>

int main() {
    int x = 5; // x 是左值

    // int* ptr1 = &x; // 可以取 x 的地址
    // int* ptr2 = &10; // 编译错误，不能取字面量 10 的地址

    return 0;
}

```

左值引用就是给左值取地址。

## 智能指针的原理和类型：

  智能指针是C++中的一种重要的语言特性，用于管理动态分配的内存，并在对象不再需要时自动释放内存，以避免内存泄漏和悬挂指针等问题。C++标准库提供了三种主要的智能指针：std::unique_ptr、std::shared_ptr 和 std::weak_ptr。以下是它们的类型和原理：

1. std::unique_ptr：
   类型：std::unique_ptr 实现了独占所有权的智能指针，即一个 std::unique_ptr 实例拥有对其所指向对象的唯一所有权。

原理：std::unique_ptr 使用了移动语义（move semantics）来转移指针的所有权，使得只有一个 std::unique_ptr 可以拥有对特定资源的所有权。当 std::unique_ptr 被销毁时，它所管理的资源会被自动释放。

```cpp
#include <iostream>
#include <memory>

int main() {
    // 创建一个 std::unique_ptr，并分配一个整数
    std::unique_ptr<int> uniquePtr(new int(5));

    // 使用智能指针访问值
    std::cout << "Value: " << *uniquePtr << std::endl;

    // 释放资源并销毁智能指针
    uniquePtr.reset();

    return 0;
}
```

在这个示例中，我们创建了一个 std::unique_ptr 智能指针，并分配了一个整数。然后，我们通过 *uniquePtr 来访问其指向的值。最后，通过 reset() 方法释放了资源并销毁了智能指针。

2. std::shared_ptr：
   类型：std::shared_ptr 实现了共享所有权的智能指针，多个 std::shared_ptr 实例可以共享对同一个对象的所有权。

原理：std::shared_ptr 使用引用计数（reference counting）来跟踪有多少个 std::shared_ptr 实例共享对资源的所有权。当最后一个 std::shared_ptr 被销毁时，资源会被释放。

```cpp
#include <iostream>
#include <memory>

int main() {
    // 创建一个 std::shared_ptr，并分配一个整数
    std::shared_ptr<int> sharedPtr = std::make_shared<int>(10);

    // 使用智能指针访问值
    std::cout << "Value: " << *sharedPtr << std::endl;

    // 创建另一个 shared_ptr，与前一个共享资源
    std::shared_ptr<int> anotherPtr = sharedPtr;

    // 输出引用计数
    std::cout << "Reference count: " << sharedPtr.use_count() << std::endl;

    return 0;
}

```

在这个示例中，我们创建了一个 std::shared_ptr 智能指针，并分配了一个整数。然后，我们创建了另一个 std::shared_ptr 并将其指向原始资源，这样它们共享相同的资源。最后，我们通过 use_count() 方法输出了引用计数。

3. std::weak_ptr：
   类型：std::weak_ptr 是 std::shared_ptr 的一种辅助类，它不控制对象的生命周期，只是提供对由 std::shared_ptr 管理的对象的非拥有式访问。

原理：std::weak_ptr 通过引用计数来监控对象的生命周期，但它不会增加引用计数。如果最后一个 std::shared_ptr 被销毁，那么所有相关的 std::weak_ptr 会被置为空。

这些智能指针的设计和原理使得在C++中管理动态内存变得更加安全和方便，可以避免常见的内存管理问题，如内存泄漏和悬挂指针。选择使用哪种智能指针取决于具体的场景和需求，如是否需要共享所有权、是否存在循环引用等。

```cpp
#include <iostream>
#include <memory>

int main() {
    // 创建一个 std::shared_ptr，并分配一个整数
    std::shared_ptr<int> sharedPtr = std::make_shared<int>(20);

    // 创建一个 std::weak_ptr，与前一个共享资源
    std::weak_ptr<int> weakPtr = sharedPtr;

    // 检查资源是否仍然存在
    if (auto lockedPtr = weakPtr.lock()) {
        std::cout << "Value: " << *lockedPtr << std::endl;
    } else {
        std::cout << "Resource no longer exists." << std::endl;
    }

    // 释放资源
    sharedPtr.reset();

    return 0;
}

```

在这个示例中，我们创建了一个 std::shared_ptr 智能指针，并分配了一个整数。然后，我们创建了一个 std::weak_ptr 并将其指向原始资源。我们通过 lock() 方法检查资源是否仍然存在，并通过 reset() 方法释放了资源。

## C++11新特性：

- __auto 关键字：__ 用于自动推断变量的类型。
- __范围 for 循环：__ 简化了容器的遍历操作。
- __nullptr 关键字：__ 用于表示空指针。
- __智能指针：__ 包括 std::unique_ptr、std::shared_ptr 和 std::weak_ptr，用于管理动态分配的内存。
- __lambda 表达式：__ 允许在代码中定义匿名函数。
- __右值引用：__ 引入了 && 符号，支持移动语义和完美转发，提高了性能和效率。
- __初始化列表：__ 允许使用列表语法来初始化数组和容器。
- __委托构造函数：__ 允许一个构造函数调用另一个构造函数。
- __删除函数和默认函数：__ 通过 = delete 和 = default 关键字可以删除或者指定默认的特殊成员函数。
- __静态断言：__ 引入了 static_assert 关键字，用于在编译时进行断言检查。
- __用户自定义字面值：__ 允许用户自定义字面值后缀。
- __模板别名：__ 允许为模板类型定义别名。
- __并发编程库：__ 引入了一些支持并发编程的库，如 \<thread\>、\<mutex\>、\<future\> 等。
- __constexpr 关键字：__ 允许函数或变量在编译时求值，用于提高程序的性能。
- __类型推断：__ 包括 decltype、decltype(auto) 和 auto 关键字，用于自动推断变量类型。
这些新特性使得 C++11 成为了一个更加强大和现代化的编程语言，提供了更多的编程工具和技术来简化代码、提高性能和增强安全性。

## auto类型：

  __自动推断变量类型:__

```cpp
auto x = 10;         // 推断为 int 类型
auto pi = 3.1415926; // 推断为 double 类型
auto name = "John";  // 推断为 const char* 类型
```

## 范围 for 循环:

范围 for 循环是 C++11 引入的一种循环语法，用于遍历容器、数组等序列类型的元素。它可以大大简化遍历操作的语法，并提高代码的可读性。范围 for 循环的语法格式如下：

```cpp
for (auto& element : container) {
    // 使用 element 进行操作
}
```

## lambda 表达式:

Lambda 表达式是 C++11 引入的一种匿名函数语法，它允许我们在需要函数对象的地方使用一个轻量级的匿名函数。Lambda 表达式的语法形式如下:

```cpp
[capture](parameters) -> return_type { body }
```

其中：

- capture：捕获列表，用于捕获外部变量，可以为空，也可以包含一个或多个外部变量，可以按值捕获或按引用捕获。
- parameters：形参列表，用于指定 lambda 表达式的参数。
- return_type：返回类型，用于指定 lambda 表达式的返回类型，可以省略，编译器会自动推断。
- body：函数体，用于指定 lambda 表达式的具体实现。

## 右值引用

右值引用是 C++11 引入的一种新的引用类型，用于支持移动语义和完美转发，提高代码的性能和效率。右值引用的语法是使用 && 符号来声明，例如 int&& 表示一个右值引用类型的整数引用。

右值引用的主要特点包括：

- 支持移动语义：右值引用使得程序员能够控制资源的所有权，实现资源的高效转移。通过移动语义，可以避免不必要的对象拷贝，提高程序的性能。
- 支持完美转发：右值引用可以在函数调用中实现完美转发，即将参数原封不动地转发给其他函数，避免了不必要的对象拷贝。
- 区分左值和右值：右值引用可以明确区分左值和右值。左值引用只能绑定到左值，而右值引用只能绑定到右值，这样可以在编译期间进行类型检查和优化。
- 用于移动语义的标识符：std::move() 是一个用于将对象转换为右值引用的标识符。它将一个左值强制转换为右值引用，从而可以调用移动构造函数或移动赋值运算符，实现资源的高效移动。
- 右值引用参数的重载：函数可以重载接受右值引用参数的版本，从而在传递右值和左值时实现不同的行为，例如优化资源管理、提高性能等。

## 移动语义

移动语义是 C++11 引入的一种语言特性，用于优化资源管理和提高程序性能。它允许将资源（如堆上分配的内存、文件句柄等）从一个对象转移到另一个对象，而不是进行昂贵的资源拷贝操作。移动语义的核心思想是通过将资源的所有权从一个对象转移到另一个对象来实现高效的资源移动。

移动语义的实现依赖于右值引用和移动构造函数（以及移动赋值运算符）。右值引用允许我们绑定到临时对象或表达式（右值），而移动构造函数允许我们使用这些右值来实现资源的高效转移。

移动语义的主要优势包括：

- 避免不必要的资源拷贝：在某些情况下，进行资源拷贝是不必要的，例如当对象即将被销毁时。使用移动语义，可以将资源从一个对象转移到另一个对象，而不是进行昂贵的资源拷贝操作，从而提高了程序的性能。
- 减少动态内存分配：移动语义使得在动态内存管理中更容易实现零成本抽象，即在传递资源所有权时，无需进行额外的内存分配或释放操作，从而提高了程序的效率。
- 简化异常安全性：移动语义可以简化异常安全性的实现。在传递资源所有权时，可以使用移动语义来确保资源在异常发生时能够正确释放，从而避免资源泄漏。

```cpp
#include <iostream>
#include <string>

class MyData {
public:
    MyData() { std::cout << "Default constructor" << std::endl; }
    MyData(const MyData&) { std::cout << "Copy constructor" << std::endl; }
    MyData(MyData&&) noexcept { std::cout << "Move constructor" << std::endl; }
};

int main() {
    MyData data1; // 调用默认构造函数
    MyData data2 = std::move(data1); // 调用移动构造函数
    return 0;
}

```

在这个示例中，我们定义了一个 MyData 类，其中包含了默认构造函数、拷贝构造函数和移动构造函数。在 main() 函数中，我们首先创建了一个 MyData 对象 data1，然后使用 std::move() 将其转换为右值引用，并赋值给 data2，这样就调用了移动构造函数。通过这个示例，可以看到移动语义的使用和效果。

## 完美转发

完美转发（perfect forwarding）是 C++11 引入的一种语言特性，旨在允许函数在传递参数时完美地保持它们的类型和特性。通过完美转发，可以实现参数的原样传递，无论是左值还是右值，以及参数的 const 和 volatile 限定符等都可以被正确地传递。

在 C++11 之前，通过模板来实现参数转发可能会遇到一些问题，因为模板参数的推导可能会丢失引用、const 限定符和 volatile 限定符等信息。完美转发通过引入右值引用、模板参数推导和 std::forward 等语言特性来解决这些问题。

完美转发的主要特点包括：

- 保持参数类型：完美转发允许函数将接收到的参数原样转发给其他函数，保持参数的类型和特性不变。无论是左值还是右值、带有 const 限定符还是 volatile 限定符等，都可以被正确地传递。
- 用于泛型编程：完美转发特别适用于泛型编程，可以在模板函数中正确地传递参数，而无需手动处理每种类型和每种限定符。
- 提高代码的灵活性和复用性：完美转发可以帮助我们编写更灵活、更通用的代码，提高代码的复用性和可维护性。

实现完美转发通常需要结合右值引用和 std::forward 等语言特性。std::forward 是一个模板函数，用于将接收到的参数原样转发给其他函数。它在模板函数内部使用，并根据参数的值类型和左值/右值属性选择性地转发参数。

```cpp
#include <iostream>
#include <utility>

void forwarder(int& x) {
    std::cout << "Lvalue reference: " << x << std::endl;
}

void forwarder(int&& x) {
    std::cout << "Rvalue reference: " << x << std::endl;
}

template<typename T>
void wrapper(T&& arg) {
    forwarder(std::forward<T>(arg));
}

int main() {
    int x = 10;

    wrapper(x); // 调用 forwarder(int&)
    wrapper(20); // 调用 forwarder(int&&)

    return 0;
}

```

在这个示例中，我们定义了两个重载的 forwarder 函数，一个接受左值引用，另一个接受右值引用。然后，我们定义了一个模板函数 wrapper，它接受一个泛型参数 arg。在 wrapper 函数内部，我们使用 std::forward 将参数 arg 完美转发给 forwarder 函数。在 main() 函数中，我们分别调用 wrapper 函数传递左值和右值参数，可以看到参数被正确地转发到相应的 forwarder 函数。

## 委托构造函数

委托构造函数是 C++11 引入的一种构造函数特性，允许一个构造函数调用同一个类中的另一个构造函数来完成对象的初始化。这样做可以减少代码重复，提高构造函数的复用性和可读性。
委托构造函数的特点包括：

- 简化构造函数：通过委托构造函数，可以将对象的初始化工作委托给同一个类中的其他构造函数来完成，从而简化了构造函数的实现。
- 提高代码重用性：委托构造函数可以避免代码重复，提高了构造函数的复用性和可维护性。
- 支持初始化列表：委托构造函数可以使用初始化列表来初始化成员变量，从而实现更加灵活的对象初始化。

需要注意的是，委托构造函数不能导致循环调用，即不能形成构造函数之间的无限循环调用。在委托构造函数中，可以使用其他构造函数进行初始化，但不能形成循环调用。

```cpp
#include <iostream>

class MyClass {
public:
    MyClass() : MyClass(0, 0, 0) {} // 委托构造函数
    MyClass(int x, int y) : MyClass(x, y, 0) {} // 委托构造函数
    MyClass(int x, int y, int z) : x(x), y(y), z(z) {} // 主构造函数

    void print() const {
        std::cout << "x: " << x << ", y: " << y << ", z: " << z << std::endl;
    }

private:
    int x, y, z;
};

int main() {
    MyClass obj1; // 调用委托构造函数
    obj1.print();

    MyClass obj2(1, 2); // 调用委托构造函数
    obj2.print();

    MyClass obj3(3, 4, 5); // 调用主构造函数
    obj3.print();

    return 0;
}

```

在这个示例中，我们定义了一个包含委托构造函数和主构造函数的类 MyClass，并在 main() 函数中创建了几个对象，演示了委托构造函数的使用。

## vector的实现原理

std::vector 是 C++ 标准模板库（STL）中的一个常用容器，它是一个动态数组，提供了连续存储的功能，并且支持动态扩容。

以下是 std::vector 的基本实现原理：

- 连续存储：std::vector 使用一块连续的内存来存储元素，这使得它能够通过指针算术快速地访问任意元素。内存连续性也有利于 CPU 缓存的有效利用，提高了访问性能。
- 动态扩容：当 std::vector 的存储空间不足以容纳新元素时，它会自动进行动态扩容。通常情况下，std::vector 会分配一个比当前容量更大的新内存区域，并将旧元素复制到新的内存区域中。动态扩容的策略通常是以指数级别增长，即每次扩容的大小是当前容量的一定倍数，这样可以减少频繁的扩容操作，提高效率。
- 内存管理：std::vector 通过 new[] 和 delete[] 运算符来进行内存分配和释放，它使用动态分配的内存来存储元素，这使得它能够灵活地管理内存，并且能够根据需要动态调整大小。
- 迭代器支持：std::vector 提供了迭代器（iterator）支持，可以通过迭代器来遍历容器中的元素。迭代器可以指向容器中的任意位置，并支持随机访问，使得对容器中元素的访问操作更加灵活和高效。
- 异常安全性：std::vector 的操作通常具有异常安全性，即在发生异常时能够保证程序状态的一致性和正确性。这是通过使用 RAII（资源获取即初始化）技术和异常处理机制来实现的。

总的来说，std::vector 的实现原理基于动态数组和动态内存分配，通过连续存储和动态扩容来提供高效的元素访问和管理功能。它是 C++ 中常用的数据结构之一，具有灵活性、高效性和通用性。

## list的实现原理

std::list 是 C++ 标准模板库（STL）中的一个双向链表容器，它提供了一种灵活的数据结构，可以在任意位置高效地插入和删除元素。

以下是 std::list 的基本实现原理：

- 双向链表：std::list 内部使用双向链表来存储元素，每个节点包含两个指针，一个指向前一个节点，一个指向后一个节点。这种结构使得在任意位置进行插入和删除操作的时间复杂度为 O(1)，即与链表长度无关。
- 动态内存分配：每次插入新元素时，std::list 都会动态地分配新节点的内存空间。这种动态内存分配的方式使得 std::list 能够在运行时灵活地调整大小，而不受固定的内存限制。
- 迭代器支持：std::list 提供了迭代器（iterator）支持，可以通过迭代器来遍历容器中的元素。std::list 的迭代器是双向迭代器，支持正向和反向遍历，可以在常数时间内进行前进和后退操作。
- 元素访问：std::list 不支持随机访问，即不能像数组一样通过下标直接访问元素。如果需要访问特定位置的元素，必须通过迭代器来遍历链表，这会导致访问操作的时间复杂度为 O(n)，其中 n 是链表长度。
- 内存管理：由于 std::list 使用动态内存分配，因此它不需要像数组一样预先分配一块连续的内存空间。这使得 std::list 能够更加灵活地管理内存，并且能够避免因连续内存分配而导致的内存碎片问题。

总的来说，std::list 的实现原理基于双向链表，通过动态内存分配来存储元素，并提供了迭代器支持，使得在任意位置进行插入和删除操作的时间复杂度为 O(1)。它是一种灵活、高效的数据结构，适用于需要频繁插入和删除操作的场景。

## stack的实现原理

在 C++ 标准库中，std::stack 是一个容器适配器（Container Adapter），它提供了一种简单的封装方式，用于在栈数据结构上执行操作。std::stack 默认使用 std::deque 作为其底层容器，但你也可以指定其他容器类型作为其底层容器。
如果你想使用其他容器作为 std::stack 的底层容器，可以显式地指定 Container 模板参数。例如，如果要使用 std::vector 作为底层容器，可以这样声明 std::stack：

```cpp
std::stack<int, std::vector<int>> myStack;
```

## deque的实现原理

std::deque 是 C++ 标准库中的一个双端队列（double-ended queue）容器，它允许在两端进行高效的插入和删除操作，并且支持随机访问。

以下是 std::deque 的基本实现原理：

- 双端队列：std::deque 内部使用一系列的块（或称为缓冲区）来存储元素，每个块都是一个固定大小的数组，通常是连续存储的。这种结构使得 std::deque 能够在两端进行高效的插入和删除操作，时间复杂度为 O(1)。
- 动态内存分配：std::deque 的块是动态分配的，即在需要时会动态地分配新的块来存储元素。这种动态内存分配的方式使得 std::deque 能够在运行时灵活地调整大小，而不受固定的内存限制。
- 随机访问：std::deque 支持随机访问，即可以通过索引来直接访问元素。由于每个块都是一个固定大小的数组，因此可以通过指针算术来快速定位到指定索引处的元素，使得随机访问的时间复杂度为 O(1)。
- 内存管理：std::deque 使用动态内存分配来管理元素的存储空间，每个块都是独立分配和释放的。这种内存管理的方式使得 std::deque 能够更加灵活地管理内存，并且能够避免因连续内存分配而导致的内存碎片问题。

总的来说，std::deque 的实现原理基于双端队列和动态内存分配，通过一系列的块来存储元素，从而实现了高效的插入、删除和随机访问操作。它是一种灵活、高效的数据结构，适用于各种需要频繁插入和删除操作的场景。

## const

在 C++ 中，const 是一个关键字，用于声明常量或指示函数的不可修改性。const 关键字可以用于多种上下文中，其作用取决于其所修饰的对象或函数。

- 常量声明：在变量声明中，const 可以用来声明一个常量，表示该变量的值不能被修改。例如：

```cpp
const int MAX_VALUE = 100;
```

- 指针修饰：在指针声明中，const 可以用来修饰指针所指向的对象，表示指针指向的对象是常量，不能通过该指针修改对象的值。例如：

```cpp
const int* ptr; // ptr 是一个指向常量整数的指针，不能通过 ptr 修改所指向的整数的值
int const* ptr; // 与上述声明等价
```

- 常量指针：在指针声明中，const 还可以用来修饰指针本身，表示指针本身是常量，不能通过该指针修改指向的对象。例如：

```cpp
int x = 10;
int* const ptr = &x; // ptr 是一个常量指针，不能通过 ptr 修改指向的对象
```

- 成员函数修饰：在成员函数声明或定义中，const 用来修饰函数，表示该函数是一个常量成员函数，不能修改对象的成员变量。这种函数被称为 const 成员函数。例如：

```cpp
class MyClass {
public:
    void foo() const; // 声明一个 const 成员函数
};
```

const 关键字的作用是确保数据的不可修改性，从而提高代码的可读性、可维护性和安全性。通过使用 const 关键字，可以有效地避免在程序中意外地修改了不应该被修改的变量，从而减少了错误的发生。

## 常量成员函数

常量成员函数是类中的一种特殊类型的成员函数，它被声明为 const 关键字修饰。常量成员函数的作用在于表明该函数不会修改类的任何成员变量，因此它可以在常量对象上调用，保证了对常量对象的操作不会改变其状态。

常量成员函数的主要作用包括：

- 保证对象的状态不变性：常量成员函数承诺不修改对象的任何成员变量，这意味着在常量对象上调用常量成员函数不会改变对象的状态。这样可以增强代码的可靠性和可维护性，确保对象的状态不被意外修改。
- 允许在常量对象上调用：常量成员函数可以在常量对象上被调用，因为它不会修改对象的状态。这样可以增加代码的灵活性，使得常量对象也能够调用类中的函数。
- 优化代码：编译器可以根据常量成员函数的特性进行优化，例如，可以在常量对象上调用常量成员函数时进行内联展开，从而减少函数调用的开销。
- 约束函数行为：通过将成员函数声明为常量成员函数，可以明确告知用户该函数不会对对象的状态进行修改，从而使得代码更加清晰易懂。这样可以减少程序员在理解和维护代码时的困惑和错误。

总的来说，常量成员函数的作用在于保证对象的状态不变性，允许在常量对象上调用，优化代码，以及约束函数的行为。通过使用常量成员函数，可以提高代码的可靠性、可维护性和安全性，从而编写出更加高质量的代码。

## volatile

在C++中，volatile 是一个关键字，用于声明变量可能会在没有明确被代码修改的情况下被外部因素更改，例如硬件或其他线程。volatile 告诉编译器不应该对该变量进行优化，因为其值可能会在不同的时间点被外部因素更改。

volatile 的主要作用包括：

- 防止编译器优化：编译器在优化代码时会对变量进行优化，例如将变量的值缓存在寄存器中。但是对于声明为 volatile 的变量，编译器不会进行这种优化，因为它们的值可能会在程序的运行过程中由外部因素更改。
- 标识内存映射硬件：在嵌入式系统中，通常会使用 volatile 来声明与内存映射硬件相关的变量。这样可以确保编译器不会对这些变量进行优化，以保持与硬件的实时交互。
- 多线程编程：在多线程编程中，volatile 变量的值可能会被其他线程更改，因此需要使用 volatile 来确保对这些变量的读取和写入操作是原子的。
- 需要注意的是，虽然 volatile 可以确保对变量的读取和写入是原子的，但它并不能保证对变量的复杂操作是原子的。如果需要对变量进行原子操作，应该使用原子类型或者互斥量等线程同步机制。

总的来说，volatile 关键字用于标识那些可能会在没有明确被代码修改的情况下被外部因素更改的变量，以防止编译器对这些变量进行优化。

## final

在 C++11 中引入了 final 关键字，用于声明一个类、成员函数或虚函数不允许被继承、重写或覆盖。
final 关键字的作用主要有两个方面

1. 类声明中的使用：
   在类声明中，final 用于声明一个类不允许被继承。例如：

```cpp
class Base final {
    // 类成员
};
```

2. 成员函数声明中的使用：
   在成员函数声明中，final 用于声明一个虚函数不允许在派生类中重写或覆盖。例如：

```cpp
class Base {
public:
    virtual void foo() const final; // final 限定 foo() 方法不可被重写
};
```

## override

## explict

## malloc和new的区别

malloc 和 new 都用于在内存中动态分配空间，但它们之间有一些重要的区别：

语法：

- malloc 是 C 语言中的库函数，用于动态分配内存空间。它的语法是：

```cpp
void* malloc(size_t size);
```

- new 是 C++ 中的运算符，用于动态分配对象，并调用对象的构造函数进行初始化。它的语法是：

```cpp
new type;
```

- 返回值：
  malloc 返回一个 void* 指针，指向分配内存的起始位置。
  new 返回一个指向动态分配的对象的指针，指向对象的起始位置。
- 类型安全：
  malloc 返回的是 void* 指针，需要进行显式的类型转换，并且在使用时需要手动管理内存的大小和释放。
  new 返回的是特定类型的指针，不需要进行显式的类型转换，并且在对象的生命周期结束时会自动调用析构函数来释放内存。
- 构造函数调用：
  malloc 分配的内存空间是未初始化的，不会调用对象的构造函数。
  new 分配对象时会调用对象的构造函数，以确保对象被正确初始化。
- 异常处理：
  如果 malloc 分配内存失败，它将返回空指针，需要手动检查并处理分配失败的情况。
  如果 new 分配对象失败，它将抛出 std::bad_alloc 异常，可以通过异常处理机制来捕获和处理异常。

总的来说，new 是 C++ 中用于动态分配对象的更高级的方式，它提供了更方便、类型安全、异常安全的内存分配方式，并且能够自动调用对象的构造函数进行初始化和析构函数进行清理。而 malloc 则是 C 语言中的库函数，需要手动管理内存的分配和释放，并且不支持对象的构造和析构。

## free和delete的区别

free 和 delete 都用于释放动态分配的内存，但它们之间有一些重要的区别：

1. 用法和语法：

- free 是 C 语言中的库函数，用于释放通过 malloc、calloc 或 realloc 分配的内存空间。它的语法是：

```cpp
void free(void* ptr);
```

- delete 是 C++ 中的运算符，用于释放通过 new 运算符动态分配的对象。它的语法是：

```cpp
delete ptr;
```

- 注意：delete 运算符后面的 ptr 必须是通过 new 运算符分配的内存地址，否则行为是未定义的。

2. 释放的对象类型：

- free 释放的是通过 malloc、calloc 或 realloc 分配的内存空间，它只能释放内存，并不调用对象的析构函数。
- delete 释放的是通过 new 运算符动态分配的对象，它不仅会释放对象所占用的内存，还会调用对象的析构函数进行资源的释放。

3. 类型安全：

- free 不会对释放的内存进行类型检查，需要手动保证传递的指针是动态分配的内存地址。
- delete 会对传递的指针进行类型检查，如果传递的指针不是通过 new 运算符动态分配的对象的地址，则行为是未定义的。

4. 内存管理：

- free 需要手动管理内存的分配和释放，不支持自动调用对象的构造和析构函数。
- delete 在释放对象时会自动调用对象的析构函数进行清理，并且支持在数组释放时调用对象的析构函数。

总的来说，free 是 C 语言中的库函数，用于释放动态分配的内存空间，而 delete 是 C++ 中的运算符，用于释放动态分配的对象，并调用对象的析构函数进行资源的释放。在 C++ 中，通常应该使用 new 和 delete 来进行动态内存的分配和释放，以确保对象的生命周期管理和资源的正确释放。

## 四种强制类型转换

在C++中，有四种强制类型转换的方法，分别是：static_cast、dynamic_cast、const_cast、reinterpret_cast。它们各自有不同的作用和适用场景：

1. static_cast：

- 用于在编译时进行静态类型转换，主要用于基本类型的转换、向上转型、具有继承关系的指针或引用之间的转换。
- 可以进行隐式类型转换，但是不会进行运行时的类型检查。
- 不能用于在继承层次中进行向下转型。

```cpp
double d = 3.14;
int i = static_cast<int>(d); // 将 double 类型转换为 int 类型
```

2. dynamic_cast：
   用于在运行时进行动态类型转换，主要用于具有继承关系的指针或引用之间的转换。
   可以进行安全的向下转型，如果无法完成转换，则返回空指针（对于指针）或抛出异常（对于引用）。
   只能用于具有虚函数的类之间的转换。

```cpp
Base* ptr = new Derived();
Derived* dptr = dynamic_cast<Derived*>(ptr); // 将基类指针转换为派生类指针
```

3. const_cast：
   用于移除变量的 const 或 volatile 属性，可以进行指针或引用的类型转换，主要用于修改 const 修饰的变量。
   用于增加程序的灵活性，但需要谨慎使用，因为可能会导致未定义行为。

```cpp
const int x = 10;
int* px = const_cast<int*>(&x); // 移除 const 属性
```

4. reinterpret_cast：
   用于进行不安全的类型转换，通常用于指针和整数类型之间的转换，以及不同类型的指针之间的转换。
   对于指针之间的转换，不会进行任何类型检查，可能导致未定义行为。
   尽量避免使用 reinterpret_cast，除非绝对有必要，并且清楚其行为。

```cpp
int* iptr = reinterpret_cast<int*>(0x12345678); // 将整数转换为指针
```
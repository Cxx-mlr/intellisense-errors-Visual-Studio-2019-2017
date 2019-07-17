
- default arguments : https://en.cppreference.com/w/cpp/language/default_arguments
```cpp
#include <iostream>

// example1

void foo(int, int = 6);

// error: default argument no at end of parameter list (shouldn't be marked as error)
void foo(int x = 12, int y) {
    std::cout << x << ' ' << y << '\n';
}

// ~example1


// example2

void bar() {
    void foo(int, int = 2);
    
// error: default argument no at end of parameter list (shouldn't be marked as error)
    void foo(int = 4, int);
  
    foo();
}

// ~example2


// example3

template <class, class = void>
struct sss;

// error: default argument no at end of parameter list (shouldn't be marked as error)
template <class = void, class>
struct sss {
};

// ~example3


// main

// no intellisense errors in the caller
int main() {
    foo(); bar();
    sss <> {};
    return 0;
}

// ~main
```
- template specialization
```cpp
// example1

template <auto>
struct sss;

// error: template parameter "class_t" is not used in or cannot be deduced from the template argument list of class template "sss<ptr>" (shouldn't be marked as error)
// error: template parameter "member_t" is not used in or cannot be deduced from the template argument list of class template "sss<ptr>" (shouldn't be marked as error)
template <class class_t, class member_t, member_t class_t::*ptr>
struct sss <ptr> {
};

struct point {
	int x, y, z;
};

// error: incomplete type is not allowed (shouldn't be marked as error)
sss <&point::x> ptr_to_x{};
// error: incomplete type is not allowed (shouldn't be marked as error)
sss <&point::y> ptr_to_y{};
// error: incomplete type is not allowed (shouldn't be marked as error)
sss <&point::z> ptr_to_z{};

// ~example1

// example2

template <auto x, auto y>
struct S {
    constexpr static int value = 0;
};

//error: a template argument list is not allowed in a declaration of a primary template (shouldn't be marked as error)
template <auto x, decltype(x) y>
struct S <x, y> {
    constexpr static int value = 2;
};

int main() {
    printf("%d", S <'a', 0>::value);
    printf("%d", S <'a', 'b'>::value);
    return 0;
}

// ~example2

int main() {
    return 0;
}
```

- others
```cpp
#include <cstdio>

template <class T>
class wrapper {
    public:
        constexpr wrapper(T func) : f(func) {
        }

        template <class X0>
        constexpr decltype(auto) operator< (X0 val) {
            return [f_ = f, val](auto... args) {
                return f_(args...) < val;
            };
        }

    private:
        const T f;
};

constexpr int transform(int val) {
    return val * 2;
}

int main() {
// error: expression must have a constant value
// error: access to uninitialized subobject (member "lambda []auto (auto ...args)->auto::f_") (shouldn't be marked as error)
    constexpr auto cond = wrapper{transform} < 12;

//transform(2) < 12
    if constexpr (cond(2)) { putchar('t'); }

    else {
        putchar('f');
    }
    return 0;
}
```

```cpp
```

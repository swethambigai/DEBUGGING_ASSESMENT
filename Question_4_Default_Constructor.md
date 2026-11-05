# Question 4 – Array of Objects Missing Default Constructor

## 1. Why does `Student batch[3];` fail?

When a class has no constructor declared by the programmer, the compiler can implicitly declare a default constructor in the usual circumstances. However, once a programmer declares a constructor with parameters, the compiler does not automatically generate a no-argument default constructor.

The statement

```cpp
Student batch[3];
```

must default-construct each of the three array elements. The class only has `Student(int, string)`, which requires two arguments. Therefore, the compiler cannot construct the array elements and reports an error.

## 2. Two ways to solve the problem

**Technique A – Add an explicit default constructor**

Provide a constructor with no parameters that initializes the data members. The array can then be declared first and each element can later be assigned a temporary `Student` object created with the parameterized constructor.

**Technique B – Use an array initializer list**

Create every element directly with the parameterized constructor:

```cpp
Student batch[3] = {
    Student(101, "Aarav"),
    Student(102, "Bhavna"),
    Student(103, "Chirag")
};
```

This does not require a default constructor because each array element is initialized with the supplied arguments.

## 3. Corrected C++ program using Technique A

```cpp
#include <iostream>
#include <string>
using namespace std;

class Student {
private:
    int rollNumber;
    string name;

public:
    // Explicit default constructor.
    Student() {
        rollNumber = 0;
        name = "";
    }

    // Parameterized constructor.
    Student(int r, string n) {
        rollNumber = r;
        name = n;
    }

    void display() const {
        cout << "Roll: " << rollNumber
             << ", Name: " << name << endl;
    }
};

int main() {
    Student batch[3];

    batch[0] = Student(101, "Aarav");
    batch[1] = Student(102, "Bhavna");
    batch[2] = Student(103, "Chirag");

    for (int i = 0; i < 3; ++i) {
        batch[i].display();
    }

    return 0;
}
```

## 4. Expected console output

```text
Roll: 101, Name: Aarav
Roll: 102, Name: Bhavna
Roll: 103, Name: Chirag
```

The explicit default constructor allows the array to be created. The later assignments replace each default-initialized object with an object containing the supplied student details.

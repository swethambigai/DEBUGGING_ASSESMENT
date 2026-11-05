# Question 3 – Function Overload Ambiguity via Default Arguments

## 1. Why is the one-argument call ambiguous?

For the call

```cpp
computeVolume(5.0);
```

both of these overloads are viable:

```cpp
double computeVolume(double length, double width = 1.0,
                     double height = 1.0);

double computeVolume(double side);
```

The first overload can be called with one argument because its second and third parameters have default values. The second overload accepts exactly one argument. Both are equally good matches for a `double`, so the compiler cannot choose one. The call is ambiguous.

## 2. Does the two-argument call also conflict?

Yes. For

```cpp
computeVolume(3.0, 7.0);
```

the cuboid overload can use `length = 3.0`, `width = 7.0`, and its default `height = 1.0`. The cylinder overload accepts the two arguments as `radius = 3.0` and `height = 7.0`.

Both are viable and both arguments match `double` parameters exactly. Neither overload is a better match, so this call is also ambiguous. The one-argument cube overload is not viable for a two-argument call.

## 3. Refactoring

All dimensions use the same type (`double`), so parameter types cannot distinguish the intended shape when the number of supplied arguments overlaps. The clearest unambiguous refactoring is to use descriptive function names for the three different operations. The cuboid function retains its default dimensions.

## 4. Corrected C++ code

```cpp
#include <iostream>
using namespace std;

// Cuboid: width and height default to 1.0.
double computeCuboidVolume(double length,
                           double width = 1.0,
                           double height = 1.0) {
    return length * width * height;
}

// Cube: one side dimension.
double computeCubeVolume(double side) {
    return side * side * side;
}

// Cylinder: radius and height.
double computeCylinderVolume(double radius, double height) {
    const double PI = 3.14159;
    return PI * radius * radius * height;
}

int main() {
    cout << "Cube Volume: "
         << computeCubeVolume(5.0) << endl;

    cout << "Cuboid Volume: "
         << computeCuboidVolume(5.0) << endl;

    cout << "Cylinder Volume: "
         << computeCylinderVolume(3.0, 7.0) << endl;

    cout << "Cuboid (custom dimensions): "
         << computeCuboidVolume(2.0, 3.0, 4.0) << endl;

    return 0;
}
```

## Expected console output

```text
Cube Volume: 125
Cuboid Volume: 5
Cylinder Volume: 197.92
Cuboid (custom dimensions): 24
```

The names communicate the intended shape, remove overload ambiguity, and preserve flexible cuboid dimensions through default arguments.

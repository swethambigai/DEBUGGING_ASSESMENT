# Question 1 – Dangling Reference in Return-by-Reference

## 1. What happens to `result` after the function returns?

In `getMaximumScaled`, `result` is a local automatic variable. It is stored in the function's stack frame (activation record). When the function finishes, its lifetime ends and its stack frame is no longer valid. Returning `result` by reference therefore returns a reference to an object that no longer exists.

The reference `danglingRef` does not keep the local variable alive. Accessing it after the function has returned is undefined behaviour. It may appear to print an old value, print garbage, or cause a crash. The exact behaviour cannot be predicted.

## 2. Why is `getArrayElement` safe?

The array `data` is created in `main`, so it remains alive while `main` is executing. The parameter `arr` refers to that caller-owned array. Returning a reference to `arr[index]` refers to an element whose lifetime continues after `getArrayElement` returns.

This is safe provided the index is valid and the original array remains alive. The function does not return a reference to its local parameter variable; it returns a reference to an element in the caller's array.

## 3. Corrected C++ code

The scaled maximum is a calculated value, so the simplest correct solution is to return it by value. The array-element function can continue returning a reference.

```cpp
#include <iostream>
using namespace std;

// Return the calculated result by value.
int getMaximumScaled(int a, int b, int scaleFactor) {
    int maxVal = (a > b) ? a : b;
    int result = maxVal * scaleFactor;
    return result;
}

// Return a reference to an element in the caller-owned array.
int& getArrayElement(int arr[], int index) {
    return arr[index];
}

int main() {
    int x = 10, y = 20;

    int scaledMax = getMaximumScaled(x, y, 2);
    cout << "Scaled Max: " << scaledMax << endl;

    int data[3] = {100, 200, 300};
    getArrayElement(data, 1) = 999;
    cout << "Updated Array[1]: " << data[1] << endl;

    // A value copy remains valid after the function has returned.
    cout << "Scaled Max Value: " << scaledMax << endl;

    return 0;
}
```

## 4. Dry run

| Step | Statement / operation | Value or effect |
|---:|---|---|
| 1 | `x = 10`, `y = 20` | Inputs are initialized |
| 2 | Compare `a = 10` and `b = 20` | Maximum is 20 |
| 3 | Multiply maximum by 2 | `result = 40` inside the function |
| 4 | Return 40 by value | `scaledMax` in `main` receives 40 |
| 5 | `getArrayElement(data, 1) = 999` | Second array element changes from 200 to 999 |
| 6 | Print the values | 40, 999 and 40 are displayed |

## Expected console output

```text
Scaled Max: 40
Updated Array[1]: 999
Scaled Max Value: 40
```

The corrected program does not retain a reference to a local variable after its lifetime ends.

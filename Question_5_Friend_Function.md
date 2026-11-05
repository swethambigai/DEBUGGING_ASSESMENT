# Question 5 – Friend Function Declaration and Scope Resolution Flaws

## 1. Why must `alertThreshold` be qualified?

`alertThreshold` is a private data member of `TelemetryAuditor`. It is not a local variable or a global variable in `inspectDiagnostics`. The friend function must access it through the auditor object:

```cpp
a.alertThreshold
```

A friend function is not a member function. It has no implicit `this` pointer. Friendship grants permission to access private and protected members, but it does not remove the need to identify the object whose member is being accessed.

The engine fields must similarly be accessed through the engine object, for example `e.temperature` and `e.rpm`.

## 2. Why does `v8.inspectDiagnostics(v8, audit)` fail?

`inspectDiagnostics` is a non-member friend function, not a member of `Engine`. It therefore cannot be called using the object-member syntax `v8.inspectDiagnostics(...)`.

Call it like an ordinary function:

```cpp
inspectDiagnostics(v8, audit);
```

## 3. Why is a forward declaration required?

The friend declaration inside `Engine` mentions `TelemetryAuditor` as a parameter type before the full `TelemetryAuditor` class definition appears. The compiler must know that this class name is a type.

A forward declaration

```cpp
class TelemetryAuditor;
```

before the definition of `Engine` introduces the class name. The complete class definition can then appear later. `Engine` is also forward-declared before the friend function prototype.

## 4. Fully corrected C++ code

```cpp
#include <iostream>
using namespace std;

// Forward declarations.
class Engine;
class TelemetryAuditor;

// Declare the non-member function using the incomplete class types.
void inspectDiagnostics(const Engine& e,
                        const TelemetryAuditor& a);

class Engine {
private:
    int temperature;
    int rpm;

public:
    Engine(int t, int r) : temperature(t), rpm(r) {}

    friend void inspectDiagnostics(const Engine& e,
                                   const TelemetryAuditor& a);
};

class TelemetryAuditor {
private:
    int alertThreshold;

public:
    TelemetryAuditor(int threshold)
        : alertThreshold(threshold) {}

    friend void inspectDiagnostics(const Engine& e,
                                   const TelemetryAuditor& a);
};

// Friend function definition: it is not a member of either class.
void inspectDiagnostics(const Engine& e,
                        const TelemetryAuditor& a) {
    if (e.temperature > a.alertThreshold) {
        cout << "Warning: Engine overheat!" << endl;
    } else {
        cout << "Engine operating nominally at "
             << e.rpm << " RPM." << endl;
    }
}

int main() {
    Engine v8(105, 3200);
    TelemetryAuditor audit(100);

    inspectDiagnostics(v8, audit);

    return 0;
}
```

## Expected console output

```text
Warning: Engine overheat!
```

The engine temperature is 105 and the auditor's alert threshold is 100. Since 105 is greater than 100, the warning message is displayed.

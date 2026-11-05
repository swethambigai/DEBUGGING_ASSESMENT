# Question 2 – Static Data Member Linkage and Member Function Scoping

## 1. Linker error: undefined reference

The declaration

```cpp
static double totalBankReserves;
```

inside the class declares a static data member, but in the version of C++ assumed by this question it does not, by itself, provide the required out-of-class definition. A definition is needed to allocate storage for the shared variable:

```cpp
double BankAccount::totalBankReserves = 0.0;
```

Without that definition, code that uses the member can compile but fail at the linking stage with an undefined-reference error.

## 2. Compilation error in `displayAudit()`

`displayAudit()` is static, so it belongs to the class rather than to one particular `BankAccount` object. It has no implicit `this` pointer and does not know which object's `accHolder` or `balance` should be used.

A static member function can directly access static class members, such as `totalBankReserves`. To display an individual account holder, use a non-static member function called on a particular object.

## 3. Complete corrected C++ code

```cpp
#include <iostream>
#include <string>
using namespace std;

class BankAccount {
private:
    string accHolder;
    double balance;
    static double totalBankReserves;

public:
    BankAccount(string name, double initialDeposit) {
        accHolder = name;
        balance = initialDeposit;
        totalBankReserves += initialDeposit;
    }

    // Static function: displays class-wide information.
    static void displayAudit() {
        cout << "Total Reserves in Vault: $"
             << totalBankReserves << endl;
    }

    // Non-static function: displays information for this object.
    void displayAccount() const {
        cout << "Account Holder: " << accHolder << endl;
        cout << "Account Balance: $" << balance << endl;
    }
};

// Definition and initialization of the static data member.
double BankAccount::totalBankReserves = 0.0;

int main() {
    BankAccount acc1("Alice", 5000.0);
    BankAccount acc2("Bob", 3000.0);

    BankAccount::displayAudit();

    acc1.displayAccount();
    acc2.displayAccount();

    return 0;
}
```

## 4. Expected console output

```text
Total Reserves in Vault: $8000
Account Holder: Alice
Account Balance: $5000
Account Holder: Bob
Account Balance: $3000
```

The two deposits update the one shared static variable. Individual account details are displayed through non-static member functions on `acc1` and `acc2`.

**C++ version note:** Since C++17, a static data member may alternatively be declared `inline static` and initialized inside the class. This answer uses the separate definition requested by the question.

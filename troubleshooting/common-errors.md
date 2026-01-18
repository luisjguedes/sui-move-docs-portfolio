# Troubleshooting: Common Move Compilation Errors

This guide helps you diagnose and fix the most common errors when compiling Move code for Sui.

## Quick Reference

| Error Message | Common Cause | Quick Fix |
|---------------|--------------|-----------|
| "Unused parameter" | Parameter not used in function | Add `_` prefix or remove |
| "Unbound module" | Module not imported | Add `use` statement |
| "Ability constraint not satisfied" | Missing `key`, `store`, `copy`, or `drop` | Add required ability |
| "Invalid borrow" | Borrowing rules violated | Check mutable/immutable usage |
| "Type mismatch" | Wrong type passed | Verify function signature |

---

## Error Categories

### 1. Compilation Errors

These prevent your code from building.

#### Error: "Unused parameter 'x'"

**What it means:** You declared a parameter but never used it in the function body.

**Example:**
```move
public fun example(value: u64, unused: u64): u64 {
    value * 2  // 'unused' is never used
}
```

**Solutions:**

Option 1 - **Remove the parameter:**
```move
public fun example(value: u64): u64 {
    value * 2
}
```

Option 2 - **Prefix with underscore:**
```move
public fun example(value: u64, _unused: u64): u64 {
    value * 2
}
```

Option 3 - **Use it in the function:**
```move
public fun example(value: u64, multiplier: u64): u64 {
    value * multiplier
}
```

---

#### Error: "Unbound module 'object'"

**What it means:** You're trying to use a module that hasn't been imported.

**Example:**
```move
module my_package::example {
    public struct MyStruct has key {
        id: UID,  // Error: UID not found
    }
}
```

**Solution - Add the import:**
```move
module my_package::example {
    use sui::object::UID;  // Import UID
    
    public struct MyStruct has key {
        id: UID,
    }
}
```

**Common imports you'll need:**
```move
use sui::object::{Self, ID, UID};
use sui::transfer;
use sui::tx_context::{Self, TxContext};
use std::string::String;
use std::option::Option;
```

---

#### Error: "Ability constraint not satisfied"

**What it means:** Your struct is missing a required ability for how you're trying to use it.

**Example:**
```move
public struct Item {  // Missing 'key' ability
    id: UID,
}

public fun create(ctx: &mut TxContext) {
    let item = Item { id: object::new(ctx) };
    transfer::transfer(item, tx_context::sender(ctx));  // Error!
}
```

**Solution - Add the required ability:**
```move
public struct Item has key {  // Now has 'key'
    id: UID,
}
```

**Ability reference:**
- `key` - Can be owned, stored as global state
- `store` - Can be stored inside other structs
- `copy` - Can be copied
- `drop` - Can be discarded

**Common combinations:**
```move
// Top-level object (NFT, account)
public struct MyObject has key { id: UID }

// Can be stored and transferred
public struct Token has key, store { id: UID }

// Can be copied and dropped (primitive data)
public struct Config has copy, drop { value: u64 }
```

---

#### Error: "Invalid borrow"

**What it means:** You're violating Rust/Move's borrowing rules.

**Example:**
```move
public fun invalid_borrow(obj: &mut MyObject) {
    let ref1 = &obj;
    let ref2 = &mut obj;  // Error: can't have mutable and immutable refs
}
```

**The Rules:**
1. You can have **multiple immutable references** OR **one mutable reference**
2. You can't have both at the same time

**Solution - Choose one type:**
```move
// Option 1: Both immutable
public fun read_twice(obj: &MyObject) {
    let ref1 = obj;
    let ref2 = obj;  // OK: both immutable
}

// Option 2: One mutable
public fun modify(obj: &mut MyObject) {
    obj.value = 10;  // OK: only one mutable reference
}
```

---

#### Error: "Type mismatch: expected 'u64', found 'u8'"

**What it means:** You're passing the wrong type to a function.

**Example:**
```move
public fun set_value(value: u64) { /*...*/ }

public fun caller() {
    let small: u8 = 5;
    set_value(small);  // Error: u8 ≠ u64
}
```

**Solution - Cast the type:**
```move
public fun caller() {
    let small: u8 = 5;
    set_value((small as u64));  // Cast u8 to u64
}
```

**Common type casts:**
```move
// Numeric casts
let a: u8 = 5;
let b: u64 = (a as u64);

// Vector to String
let bytes = b"Hello";
let text = string::utf8(bytes);
```

---

### 2. Runtime Errors

These occur when your code runs.

#### Error: "Assertion failed"

**What it means:** An `assert!()` check failed.

**Example:**
```move
public fun withdraw(amount: u64, balance: u64) {
    assert!(amount <= balance, 0);  // Fails if amount > balance
    // ... withdrawal logic
}
```

**How to debug:**
1. Check the error code (the number in `assert!`)
2. Find where that error code is used
3. Verify the condition logic

**Solution - Add helpful error constants:**
```move
const EInsufficientBalance: u64 = 0;
const EUnauthorized: u64 = 1;
const EInvalidAmount: u64 = 2;

public fun withdraw(amount: u64, balance: u64) {
    assert!(amount <= balance, EInsufficientBalance);
    assert!(amount > 0, EInvalidAmount);
}
```

---

#### Error: "Object does not exist"

**What it means:** You're trying to access an object that was deleted or transferred.

**Example:**
```move
// Transaction 1: Transfer object
transfer::transfer(my_object, recipient);

// Transaction 2: Try to use it again
let value = my_object.value;  // Error: object already moved!
```

**Solution - Understand object lifecycles:**

✅ **Correct - Use object once:**
```move
public entry fun transfer_and_done(obj: MyObject, recipient: address) {
    transfer::transfer(obj, recipient);
    // Don't use 'obj' after this!
}
```

✅ **Correct - Borrow instead of moving:**
```move
public fun read_value(obj: &MyObject): u64 {
    obj.value  // Borrowed, not moved
}
```

---

### 3. Publishing Errors

#### Error: "Dependency not found"

**What it means:** Your `Move.toml` references a package that can't be found.

**Example:**
```toml
[dependencies]
Sui = { local = "../sui-framework" }  # Path doesn't exist
```

**Solution - Use Git dependency:**
```toml
[dependencies]
Sui = { 
    git = "https://github.com/MystenLabs/sui.git", 
    subdir = "crates/sui-framework/packages/sui-framework", 
    rev = "framework/mainnet" 
}
```

---

#### Error: "Gas budget exceeded"

**What it means:** Your transaction costs more gas than the budget allows.

**Example:**
```bash
sui client publish --gas-budget 1000000  # Too low!
```

**Solution - Increase the budget:**
```bash
sui client publish --gas-budget 100000000  # Much better
```

**Typical gas budgets:**
- Simple transactions: 10,000,000
- Publishing packages: 100,000,000
- Complex operations: 500,000,000+

---

## Debugging Workflow

When you encounter an error:

1. **Read the full error message** - Don't just skim
2. **Identify the error type** - Compilation, runtime, or publishing?
3. **Locate the source** - Which file and line?
4. **Check this guide** - Is it a common error?
5. **Verify your assumptions** - Add debug prints if needed
6. **Test incrementally** - Comment out code to isolate the problem

---

## Testing Tips

### Add Debug Output

```move
use std::debug;

public fun my_function(value: u64) {
    debug::print(&b"Current value:");
    debug::print(&value);
}
```

### Write Unit Tests

```move
#[test]
fun test_my_function() {
    let result = my_function(5);
    assert!(result == 10, 0);
}
```

### Use the REPL

```bash
sui move build
sui console
```

---

## Getting More Help

If you're still stuck:

1. **Check official docs:** [docs.sui.io](https://docs.sui.io)
2. **Search GitHub issues:** [github.com/MystenLabs/sui/issues](https://github.com/MystenLabs/sui/issues)
3. **Ask on Discord:** [Sui Developer Community](https://discord.gg/sui)
4. **Review examples:** [github.com/MystenLabs/sui/tree/main/examples](https://github.com/MystenLabs/sui/tree/main/examples)

---

## Error Message Decoder

Common cryptic messages explained:

| You See | It Means |
|---------|----------|
| "Expected reference, found value" | You need `&` or `&mut` |
| "Cannot move out of borrowed content" | Don't move from `&` or `&mut` |
| "Borrow checker failed" | Violating borrow rules |
| "Resource not found" | Module or struct doesn't exist |
| "Signature mismatch" | Function arguments don't match |

---

**Related:**
- [Quickstart Guide](../getting-started/quickstart.md)
- [Understanding Sui Objects](../concepts/sui-objects.md)
- [Sui CLI Reference](../api-reference/sui-cli.md)

**Last updated:** January 2026

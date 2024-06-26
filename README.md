# Resulty 🐍💞🦀

[![PyPI](https://img.shields.io/pypi/v/resulty)](https://pypi.org/project/resulty/)
[![Python Version](https://img.shields.io/pypi/pyversions/resulty)](https://pypi.org/project/resulty/)
[![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/remikalbe/resulty/test-check.yaml)](https://github.com/RemiKalbe/resulty/actions/workflows/test-check.yaml)
[![Codecov](https://img.shields.io/codecov/c/github/remikalbe/resulty)]()

`Resulty` is a Python library that provides a `Result` type for handling errors and exceptions in a functional programming style, inspired by the `Result` type in Rust 🦀. It offers a wide range of methods and features to handle errors and exceptions in a more expressive and composable way.

## Key Features ☝️

- `Result` type for representing success (`Ok`) or failure (`Err`) outcomes
- Extensive set of methods for checking the state of a `Result`, unwrapping values, handling errors, and transforming `Result` values (near 1:1 mapping with Rust's `Result` methods)
- Unique decorator-based error propagation with `propagate_result` and `async_propagate_result`, mimicking the `?` operator in Rust
- Supports both synchronous and asynchronous code

## Table of Contents

1. [Installation](#installation)
2. [Creating a `Result`](#creating-a-result)
3. [Checking the State of a `Result`](#checking-the-state-of-a-result)
   - [`is_ok()`](#is_ok)
   - [`is_ok_and(predicate)`](#is_ok_andpredicate)
   - [`is_err()`](#is_err)
   - [`is_err_and(predicate)`](#is_err_andpredicate)
   - [`and_also(result)`](#and_alsoresult)
   - [`or_if(result)`](#or_ifresult)
4. [Unwrapping Values and Handling Errors](#unwrapping-values-and-handling-errors)
   - [`ok()`](#ok)
   - [`err()`](#err)
   - [`expect(msg)`](#expectmsg)
   - [`expect_err(msg)`](#expect_errmsg)
   - [`unwrap()`](#unwrap)
   - [`unwrap_err()`](#unwrap_err)
   - [`unwrap_or(default)`](#unwrap_ordefault)
   - [`unwrap_or_else(f)`](#unwrap_or_elsef)
5. [Transforming and Mapping `Result` Values](#transforming-and-mapping-result-values)
   - [`map(f)`](#mapf)
   - [`map_err(f)`](#map_errf)
   - [`map_or(default, f)`](#map_ordefault-f)
   - [`map_or_else(default, f)`](#map_or_elsedefault-f)
6. [Combining `Result` Values](#combining-result-values)
   - [`and_then(f)`](#and_thenf)
   - [`or_else(f)`](#or_elsef)
   - [`inspect(f)`](#inspectf)
   - [`inspect_err(f)`](#inspect_errf)
7. [Error Propagation with Decorators](#error-propagation-with-decorators)
   - [`@propagate_result`](#propagate_result)
   - [`@async_propagate_result`](#async_propagate_result)
8. [Contributing](#contributing)
9. [License](#license)

## Installation

You can install `resulty` using pip:

```bash
pip install resulty
```

## Creating a `Result`

To create a `Result` instance, you can use the `Ok` and `Err` functions:

```python
from resulty import Ok, Err

# Creating an Ok result
ok_result = Ok(42)

# Creating an Err result
err_result = Err("An error occurred")
```

Both `Ok` and `Err` functions can wrap any value.

## Checking the State of a `Result`

`resulty` provides several methods to check the state of a `Result` instance and perform conditional operations based on its variant (`Ok` or `Err`).

### `is_ok()`

The `is_ok()` method returns `True` if the `Result` is an `Ok` variant, and `False` otherwise.

Example:

```python
from resulty import Ok, Err

result = Ok(42)
print(result.is_ok())  # Output: True

result = Err("An error occurred")
print(result.is_ok())  # Output: False
```

### `is_ok_and(predicate)`

The `is_ok_and(predicate)` method returns `True` if the `Result` is an `Ok` variant and the provided predicate function returns `True` for the contained value. It returns `False` otherwise.

Example:

```python
from resulty import Ok, Err

result = Ok(42)
print(result.is_ok_and(lambda x: x > 0))  # Output: True
print(result.is_ok_and(lambda x: x < 0))  # Output: False

result = Err("An error occurred")
print(result.is_ok_and(lambda x: True))  # Output: False
```

### `is_err()`

The `is_err()` method returns `True` if the `Result` is an `Err` variant, and `False` otherwise.

Example:

```python
from resulty import Ok, Err

result = Ok(42)
print(result.is_err())  # Output: False

result = Err("An error occurred")
print(result.is_err())  # Output: True
```

### `is_err_and(predicate)`

The `is_err_and(predicate)` method returns `True` if the `Result` is an `Err` variant and the provided predicate function returns `True` for the contained error. It returns `False` otherwise.

Example:

```python
from resulty import Ok, Err

result = Ok(42)
print(result.is_err_and(lambda x: True))  # Output: False

result = Err("An error occurred")
print(result.is_err_and(lambda x: len(x) > 0))  # Output: True
print(result.is_err_and(lambda x: len(x) == 0))  # Output: False
```

### `and_also(result)`

The `and_also(result)` method returns the provided `result` if the current `Result` is an `Ok` variant. If the current `Result` is an `Err` variant, it returns the current `Result`.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
other_result = Ok("Hello")
print(ok_result.and_also(other_result))  # Output: Ok("Hello")

err_result = Err("An error occurred")
print(err_result.and_also(other_result))  # Output: Err("An error occurred")

other_err_result = Err("Another error occurred")
print(err_result.and_also(other_err_result))  # Output: Err("An error occurred")
```

### `or_if(result)`

The `or_if(result)` method returns the current `Result` if it is an `Ok` variant. If the current `Result` is an `Err` variant and the provided `result` is an `Ok` variant, it returns the provided `result`. If both the current `Result` and the provided `result` are `Err` variants, it returns the provided `result`.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
other_ok_result = Ok(0)
err_result = Err("An error occurred")
other_err_result = Err("Another error occurred")

print(ok_result.or_if(other_ok_result))  # Output: Ok(42)
print(ok_result.or_if(err_result))  # Output: Ok(42)
print(err_result.or_if(other_ok_result))  # Output: Ok(0)
print(err_result.or_if(other_err_result))  # Output: Err("Another error occurred")
```

## Unwrapping Values and Handling Errors

`resulty` provides several methods to safely unwrap the value contained in a `Result` instance and handle potential errors. These methods allow you to access the contained value or error, or provide default values in case of an `Err` variant.

### `ok()`

The `ok()` method returns the contained value if the `Result` is an `Ok` variant, and `None` otherwise.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
print(ok_result.ok())  # Output: 42

err_result = Err("An error occurred")
print(err_result.ok())  # Output: None
```

### `err()`

The `err()` method returns the contained error if the `Result` is an `Err` variant, and `None` otherwise.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
print(ok_result.err())  # Output: None

err_result = Err("An error occurred")
print(err_result.err())  # Output: "An error occurred"
```

### `expect(msg)`

The `expect(msg)` method returns the contained value if the `Result` is an `Ok` variant. If the `Result` is an `Err` variant, it raises a `ResultException` with the provided error message.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
print(ok_result.expect("Expected an Ok value"))  # Output: 42

err_result = Err("An error occurred")
# Raises ResultException with the message "ResultException: Expected an Ok value\nErr(An error occurred)"
err_result.expect("Expected an Ok value")
```

### `expect_err(msg)`

The `expect_err(msg)` method returns the contained error if the `Result` is an `Err` variant. If the `Result` is an `Ok` variant, it raises a `ResultException` with the provided error message.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
# Raises ResultException with the message "ResultException: Expected an Err value\nOk(42)"
ok_result.expect_err("Expected an Err value")

err_result = Err("An error occurred")
print(err_result.expect_err("Expected an Err value"))  # Output: "An error occurred"
```

### `unwrap()`

The `unwrap()` method returns the contained value if the `Result` is an `Ok` variant. If the `Result` is an `Err` variant, it raises a `ResultException`.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
print(ok_result.unwrap())  # Output: 42

err_result = Err("An error occurred")
# Raises ResultException with the message "ResultException: called `.unwrap()` on an `Err` value\nErr(An error occurred)"
err_result.unwrap()
```

### `unwrap_err()`

The `unwrap_err()` method returns the contained error if the `Result` is an `Err` variant. If the `Result` is an `Ok` variant, it raises a `ResultException`.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
# Raises ResultException with the message "ResultException: called `.unwrap_err()` on an `Ok` value\nOk(42)"
ok_result.unwrap_err()

err_result = Err("An error occurred")
print(err_result.unwrap_err())  # Output: "An error occurred"
```

### `unwrap_or(default)`

The `unwrap_or(default)` method returns the contained value if the `Result` is an `Ok` variant. If the `Result` is an `Err` variant, it returns the provided default value.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
print(ok_result.unwrap_or(0))  # Output: 42

err_result = Err("An error occurred")
print(err_result.unwrap_or(0))  # Output: 0
```

### `unwrap_or_else(f)`

The `unwrap_or_else(f)` method returns the contained value if the `Result` is an `Ok` variant. If the `Result` is an `Err` variant, it calls the provided function `f` with the contained error and returns the result.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
print(ok_result.unwrap_or_else(lambda x: 0))  # Output: 42

err_result = Err("An error occurred")
print(err_result.unwrap_or_else(lambda x: len(x)))  # Output: 17 (length of the error message)
```

## Transforming and Mapping `Result` Values

`resulty` provides several methods to transform and map the value contained in a `Result` instance. These methods allow you to apply functions to the contained value or error, or provide default values in case of an `Err` variant.

### `map(f)`

The `map(f)` method applies the provided function `f` to the contained value if the `Result` is an `Ok` variant, and returns a new `Result` with the mapped value. If the `Result` is an `Err` variant, it returns the original `Result` without applying the function.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
mapped_result = ok_result.map(lambda x: x * 2)
print(mapped_result)  # Output: Ok(84)

err_result = Err("An error occurred")
mapped_result = err_result.map(lambda x: x * 2)
print(mapped_result)  # Output: Err("An error occurred")
```

### `map_err(f)`

The `map_err(f)` method applies the provided function `f` to the contained error if the `Result` is an `Err` variant, and returns a new `Result` with the mapped error. If the `Result` is an `Ok` variant, it returns the original `Result` without applying the function.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
mapped_result = ok_result.map_err(lambda x: f"Error: {x}")
print(mapped_result)  # Output: Ok(42)

err_result = Err("An error occurred")
mapped_result = err_result.map_err(lambda x: f"Error: {x}")
print(mapped_result)  # Output: Err("Error: An error occurred")
```

### `map_or(default, f)`

The `map_or(default, f)` method applies the provided function `f` to the contained value if the `Result` is an `Ok` variant, and returns the result. If the `Result` is an `Err` variant, it returns the provided default value.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
mapped_value = ok_result.map_or(0, lambda x: x * 2)
print(mapped_value)  # Output: 84

err_result = Err("An error occurred")
mapped_value = err_result.map_or(0, lambda x: x * 2)
print(mapped_value)  # Output: 0
```

### `map_or_else(default, f)`

The `map_or_else(default, f)` method applies the provided function `f` to the contained value if the `Result` is an `Ok` variant, and returns the result. If the `Result` is an `Err` variant, it calls the provided function `default` with the contained error and returns the result.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
mapped_value = ok_result.map_or_else(lambda x: 0, lambda x: x * 2)
print(mapped_value)  # Output: 84

err_result = Err("An error occurred")
mapped_value = err_result.map_or_else(lambda x: len(x), lambda x: x * 2)
print(mapped_value)  # Output: 18
```

## Combining `Result` Values

`resulty` provides several methods to combine and chain multiple `Result` values together. These methods allow you to perform operations on the contained values or errors of multiple `Result` instances.

### `and_then(f)`

The `and_then(f)` method applies the provided function `f` to the contained value if the `Result` is an `Ok` variant, and returns the result of `f` as a new `Result`. If the `Result` is an `Err` variant, it returns the original `Result` without applying the function.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
combined_result = ok_result.and_then(lambda x: Ok(x * 2))
print(combined_result)  # Output: Ok(84)

err_result = Err("An error occurred")
combined_result = err_result.and_then(lambda x: Ok(x * 2))
print(combined_result)  # Output: Err("An error occurred")
```

### `or_else(f)`

The `or_else(f)` method applies the provided function `f` to the contained error if the `Result` is an `Err` variant, and returns the result of `f` as a new `Result`. If the `Result` is an `Ok` variant, it returns the original `Result` without applying the function.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
combined_result = ok_result.or_else(lambda x: Err(f"Error: {x}"))
print(combined_result)  # Output: Ok(42)

err_result = Err("An error occurred")
combined_result = err_result.or_else(lambda x: Ok(0))
print(combined_result)  # Output: Ok(0)
```

### `inspect(f)`

The `inspect(f)` method applies the provided function `f` to the contained value if the `Result` is an `Ok` variant, and returns the original `Result`. The function `f` is executed for its side effects and does not affect the returned `Result`.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
inspected_result = ok_result.inspect(lambda x: print(f"Value: {x}")) # Output: Value: 42
print(inspected_result) # Output: Ok(42)

err_result = Err("An error occurred")
inspected_result = err_result.inspect(lambda x: print(f"Value: {x}")) # No output
print(inspected_result)  # Output: Err("An error occurred")
```

### `inspect_err(f)`

The `inspect_err(f)` method applies the provided function `f` to the contained error if the `Result` is an `Err` variant, and returns the original `Result`. The function `f` is executed for its side effects and does not affect the returned `Result`.

Example:

```python
from resulty import Ok, Err

ok_result = Ok(42)
inspected_result = ok_result.inspect_err(lambda x: print(f"Error: {x}")) # No output
print(inspected_result)  # Output: Ok(42)

err_result = Err("An error occurred")
inspected_result = err_result.inspect_err(lambda x: print(f"Error: {x}")) # Output: Error: An error occurred
print(inspected_result)  # Output: Err("An error occurred")
```

## Error Propagation with Decorators

`resulty` provides decorators that simplify error propagation and handling in function calls. These decorators allow you to automatically propagate errors when calling other functions that return a `Result` and handle them in a more concise and readable way. They try to mimic the `?` operator in Rust for error propagation.

### `@propagate_result`

The `@propagate_result` decorator is used to automatically propagate errors from called functions that return a `Result`. When a function decorated with `@propagate_result` calls another function that returns a `Result`, it can use the `up()` method to unwrap the value if it's an `Ok` variant, or propagate the error if it's an `Err` variant.

Example:

```python
from resulty import Result, Ok, Err, propagate_result

def divide(a: int, b: int) -> Result[float, str]:
    if b == 0:
        return Err("Cannot divide by zero")
    return Ok(a / b)

@propagate_result
def calculate() -> Result[float, str]:
    result1 = divide(10, 2).up()
    result2 = divide(20, 4).up()
    return Ok(result1 + result2)

result = calculate()
print(result)  # Output: Ok(10.0)

@propagate_result
def calculate_with_error() -> Result[float, str]:
    result1 = divide(10, 2).up()
    result2 = divide(20, 0).up() # Raises PropagatedResultException
    return Ok(result1 + result2)

result = calculate_with_error()
print(result) # Output: Err("Cannot divide by zero")
```

In this example, the `calculate` function is decorated with `@propagate_result`. It calls the `divide` function twice and uses the `up()` method to unwrap the values if they are `Ok` variants. If any of the calls to `divide` return an `Err` variant, the `up()` method will raise a `PropagatedResultException`, which will be caught by the `@propagate_result` decorator and returned as an `Err` variant.

### `@async_propagate_result`

The `@async_propagate_result` decorator works similarly to `@propagate_result`, but it is used for asynchronous functions. It automatically propagates errors when calling other functions that return a `Result` and uses the `up()` method to unwrap the values or propagate the errors.

Example:

```python
from resulty import Result, Ok, Err, async_propagate_result

async def async_divide(a: int, b: int) -> Result[float, str]:
    if b == 0:
        return Err("Cannot divide by zero")
    return Ok(a / b)

@async_propagate_result
async def async_calculate() -> Result[float, str]:
    result1 = (await async_divide(10, 2)).up()
    result2 = (await async_divide(20, 4)).up()
    return Ok(result1 + result2)

async def main():
    result = await async_calculate()
    print(result)  # Output: Ok(10.0)

import asyncio
asyncio.run(main())
```

In this example, the `async_calculate` function is decorated with `@async_propagate_result`. It calls the `async_divide` function twice and uses the `up()` method to unwrap the values if they are `Ok` variants. If any of the calls to `async_divide` return an `Err` variant, the `up()` method will raise a `PropagatedResultException`, which will be caught by the `@async_propagate_result` decorator and returned as an `Err` variant.

These decorators provide a convenient way to handle error propagation when calling other functions that return a `Result`. By using these decorators and the `up()` method, you can write more concise and readable code when working with `Result` values and error handling.

## Contributing

Contributions are welcome! If you find any issues or have suggestions for improvements, please open an issue or submit a pull request.

## License

`resulty` is licensed under the [MIT License](LICENSE).

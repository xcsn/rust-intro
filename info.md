### 1. build new project
`cargo new your_project_name --lib`
* The `--lib` flag indicates that you want to create a Rust library instead of a binary (executable) project. A Rust library is a collection of code that can be reused in other Rust projects. Libraries are typically used to share code between multiple projects, making it easier to maintain and update common functionality.
* When you create a library project using `--lib`, Cargo sets up the project structure with a `lib.rs` file in the `src` directory. The `lib.rs` file is the entry point for the library code, and it contains the public API and the implementation of the library.
* Using the `--lib` flag is beneficial when you intend to create a reusable Rust library that can be included as a dependency in other Rust projects. If you were creating a standalone executable, you would omit the `--lib` flag, and Cargo would set up the project for a binary with a `main.rs` file instead.

### 2. #[no_mangle]
In Rust, the `#[no_mangle]` attribute is used to instruct the compiler not to mangle the name of a function when generating the compiled code. Mangling is a process that the compiler uses to encode function and symbol names in a way that avoids naming conflicts and facilitates linking.

When Rust code is compiled to an object file or a shared library, the names of functions and symbols can undergo a transformation known as name mangling. This transformation is especially common in languages like C++ to handle function overloading and other features.

Here's how `#[no_mangle]` is used:

```rust
#[no_mangle]
pub extern "C" fn my_function() {
    // Function implementation
}
```

In this example:

- `#[no_mangle]` indicates that the function's name should not be mangled.
- `pub` makes the function publicly accessible outside the module.
- `extern "C"` specifies the calling convention, which is important for interoperability with other languages, especially languages like C.

The use of `#[no_mangle]` is often seen in scenarios where Rust code needs to interface with code written in other languages (like C) or when you want to control the exact symbol name that will be present in the compiled output.

For example, when creating a WebAssembly (Wasm) module, you might use `#[no_mangle]` to ensure that the exported functions have predictable names that can be easily called from JavaScript.

Here's a simplified example of Rust code that might be compiled to WebAssembly:

```rust
#[no_mangle]
pub extern "C" fn add_numbers(a: i32, b: i32) -> i32 {
    a + b
}
```

The `#[no_mangle]` ensures that the function is exported with the name `add_numbers`, making it straightforward to reference from the JavaScript side when interacting with the Wasm module.

### 3. extern
In Rust, the `extern` keyword is used in the context of function declarations to specify the calling convention and linkage of the function. It is often used in conjunction with the `#[no_mangle]` attribute, especially when interfacing with code written in other languages like C.

The syntax for an `extern` function declaration looks like this:

```rust
extern "C" {
    // function declarations go here
}
```

The string inside the double quotes specifies the "ABI" (Application Binary Interface), which defines how functions are called and how their arguments and return values are represented at the binary level. `"C"` is a common ABI used to ensure compatibility with C code.

When used in the context of a function definition, the `extern` keyword is often followed by the ABI specification and other attributes. For example:

```rust
#[no_mangle]
pub extern "C" fn add_numbers(a: i32, b: i32) -> i32 {
    a + b
}
```

In this example:

- `#[no_mangle]` ensures that the function name is not mangled during compilation, making it easier to interface with from other languages.
- `extern "C"` specifies the C calling convention, which is important for ensuring compatibility with C code.

The use of `extern` is common when writing Rust code that needs to interface with external libraries or other languages. It helps ensure that the Rust code follows the conventions expected by those external components. This is particularly important for scenarios like FFI (Foreign Function Interface) or when working with low-level system libraries.

### 4. #[cfg(test)]
In Rust, `#[cfg(test)]` is an attribute used to conditionally compile code based on whether the Rust compiler is building the code in a testing environment. This attribute is often used to include or exclude certain code specifically for testing purposes.

Here's a typical example of how `#[cfg(test)]` is used in Rust:

```rust
#[cfg(test)]
mod tests {
    #[test]
    fn my_test() {
        // Test code goes here
    }
}
```

In this example:

- The `#[cfg(test)]` attribute is applied to the `mod tests` block. This means that the entire module, including its contents, will only be compiled when running tests.

- Inside the `tests` module, the `#[test]` attribute is applied to the `my_test` function. This indicates that `my_test` is a unit test, and it will be executed when running tests with tools like `cargo test`.

When you run `cargo test` or a similar testing command, the Rust compiler will include the code marked with `#[cfg(test)]` in the compilation, and the tests will be executed.

This conditional compilation allows you to have separate code sections for tests, ensuring that testing-related code doesn't get included in the final release build of your application. This can be useful for avoiding unnecessary dependencies or for including additional debugging information specifically for tests.

Here's another example showing how you might use `#[cfg(test)]` to conditionally compile some code only when running tests:

```rust
#[cfg(test)]
fn only_compiled_for_tests() {
    // Code included only in the test build
}

fn main() {
    // Code included in the main (non-test) build
}
```

In this case, `only_compiled_for_tests` will only be compiled if the Rust compiler is building the code for testing. The function won't be included in the final binary when you build the application for release or other non-testing purposes.

### 5. cargo build --target
In Rust, the `cargo build --target` command is used to build a Rust project for a specific target architecture or platform. The `--target` flag allows you to specify the target platform and architecture, enabling cross-compilation for different environments.

The basic syntax for using `cargo build --target` is as follows:

```bash
cargo build --target <target-specifier>
```

Here, `<target-specifier>` is a string that represents the target architecture and platform. It typically follows the format `architecture-vendor-os` or `architecture-vendor-os-environment`.

For example, to build your Rust project for a 64-bit Linux target, you might use:

```bash
cargo build --target x86_64-unknown-linux-gnu
```

Here's a breakdown of the parts in a target specifier:

- `x86_64`: The architecture, representing a 64-bit x86 processor.
- `unknown`: The vendor, indicating that the vendor is not specified.
- `linux`: The operating system, in this case, Linux.
- `gnu`: The environment, specifying the GNU toolchain.

Rust supports a variety of target specifiers for different platforms and architectures, allowing you to cross-compile your code for various environments. Common target specifiers include:

- `x86_64-unknown-linux-gnu`: 64-bit Linux.
- `i686-unknown-linux-gnu`: 32-bit Linux.
- `x86_64-apple-darwin`: 64-bit macOS.
- `x86_64-pc-windows-msvc`: 64-bit Windows with the MSVC toolchain.

Using `cargo build --target` is particularly useful when you need to compile your Rust code for a different platform than the one you are currently on. This is common when targeting embedded systems, cross-compiling for specific hardware, or building for platforms other than the host system.
for this project: `cargo build --target wasm32-unknown-unknown`
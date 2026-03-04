# Rust Foundations

## Data Types:

### Integer:

- Following are the integer data-types

    <table>
    <thead>
    <tr>
    <th>Length</th>
    <th>Signed</th>
    <th>Unsigned</th>
    </tr>
    </thead>
    <tbody>
    <tr>
    <td>8-bit</td>
    <td>i8</td>
    <td>u8</td>
    </tr>

    <tr>
    <td>16-bit</td>
    <td>i16</td>
    <td>u16</td>
    </tr>

    <tr>
    <td>32-bit</td>
    <td>i32</td>
    <td>u32</td>
    </tr>

    <tr>
    <td>64-bit</td>
    <td>i64</td>
    <td>u64</td>
    </tr>

    <tr>
    <td>128-bit</td>
    <td>i128</td>
    <td>u128</td>
    </tr>

    <tr>
    <td>Architecture-dependent</td>
    <td>isize</td>
    <td>usize</td>
    </tr>
    </tbody>
    </table>

    ```rs
    // Integer
    let x: i32 = 5;
    let y: i32 = 5;

    println!("x * y: {}", x * y);
    ```

## Boolean:

- Only 2 values <code>**true / false**</code>

    ```rs
    // Boolean
    let mut flag: bool = true;
    println!("Flag: {}", flag);

    flag = false;

    println!("Flag: {}", flag);
    ```

## String:

- A complex type in <code>**Rust**</code>

    ```rs
    // String
    let str: String = String::from("Learning Rust!");

    println!("String: {}", str);
    ```

- To access a character at an index

    ```rs
    // Accessing character at an index in string
    let ch: Option<char> = str.chars().nth(100);

    // Checking character is abailable at "nth" index safely by pattern matching
    match ch {
        Some(c) => println!("{}", c),
        None => println!("No character is found!"),
    }
    ```

## Mutability in Variables:
- Using <code>**mut**</code> keyword
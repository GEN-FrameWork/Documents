# GEN Script language G (`.g`)

Dialect notes versus Lua / JavaScript (runtime behaviour after GEN Script updates).

## Entry point

- If `main` exists, it is called (as before).
- If `main` is missing, `Run` succeeds with return value `0` (no `FUNC_UNDEF`).

## Control flow

- `if` / `else` / `while` / `do` / `for` accept a single statement **or** a `{ … }` block.
- `switch` still requires a `{ … }` body with `case` labels.

## Numeric types

- Keywords: `char`, `int`, `float`, `string`.
- Floating values (keyword `float` and real literals) are stored and computed as **IEEE double**.
- There is no separate `double` keyword; `float` is the floating type name.
- Integers remain 32-bit `int`.

## Libraries

Native `XVARIANT` `DOUBLE` / `FLOAT` values map into G `float` variables with double storage (no forced float32 truncate on assign).

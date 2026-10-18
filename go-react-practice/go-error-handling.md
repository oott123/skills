# Error handling in golang

## Wrapping

1. Errors from other packages (standard library, third-party, other attogit packages) and from interface methods must not be returned as is. Wrap them with `fmt.Errorf`, adding this layer's context, and reference the cause with `%w`.
2. Functions in the same package may pass errors through unchanged; the callee is responsible for wrapping what it got from outside.
3. The wrapping text describes the operation this layer was performing, as a lowercase verb phrase such as `create main database: %w` or `write host key: %w`:
   - no `failed to`, `error`, `unable to`; the chain already says it failed;
   - no trailing punctuation;
   - do not repeat what the cause already carries, e.g. `*os.PathError` already includes the path.
4. Errors a function creates itself include its key arguments, following `*os.PathError`; e.g. errors from `datadir.Init` include the data directory path.
5. When one `fmt.Errorf` references several errors, use `%w` for each. Collect parallel validation errors with `errors.Join`.

## Exceptions

When implementing a third-party interface whose contract requires returning a sentinel unchanged and whose callers compare it with `==` (e.g. `io.EOF` from `io.Reader`), return the sentinel directly and mark the line with `//nolint:wrapcheck // <reason>`.

## Output

1. Errors are printed once, at the outermost layer: `main` writes them to stderr.
2. Intermediate layers must not both log and return the same error.

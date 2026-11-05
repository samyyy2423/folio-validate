# validate

Checks for what a [Folio](https://github.com/samyyy2423/folio) program is given (a form's fields, JSON a request
sent, a settings file), with a message for each field that's wrong, and the values made the kinds they should
be: `"42"` from a form is 42, `"on"` from a checkbox is `yes`. In Folio alone, so it works everywhere Folio does.

```text
folio add validate
```

```
use the package "validate" as validate

set rules to validate's rules()
validate's field(rules, "name", "text")
validate's field(rules, "age", "whole number")
validate's field(rules, "email", "email")
validate's optional(rules, "news", "yes or no", no)
validate's at_most(rules, "name", 40)
validate's at_least(rules, "age", 13)

set result to validate's checked(rules, dictionary where "name" is " Sam ", "age" is "12", "email" is "sam@")
say result's ok                  # no
say result's problems            # age must be at least 13, email must be an email address
```

With a form sent to a server, `request's body` read into a dictionary:

```
set result to validate's checked(rules, fields)
if not result's ok
    return record with status 400, body result's first_problem
end
set person to result's values    # "age" is 20, a number; "news" is no when the box wasn't ticked
```

## What it has

| | |
|---|---|
| `rules()` | rules with no fields yet |
| `field(rules, name, kind)` | a field that has to be there, of a kind |
| `optional(rules, name, kind, otherwise)` | a field that may be left out or empty: its value is then `otherwise` (`nothing` if none is given) |
| `at_most(rules, name, n)`, `at_least(rules, name, n)` | text of at most (or at least) n letters, a number at most n, or a list of at most n items |
| `one_of(rules, name, choices)` | one of these values |
| `check(rules, name, f, why)` | any check: `f(value)` gives yes when it's right, and `why` says what's wrong; with `text.folio`, a pattern: `function with v gives text's matches("^[A-Z]{3}$", v)`, `"must be three capital letters"` |
| `message(rules, name, words)` | words said instead of any of that field's messages |
| `checked(rules, given)` | a record of `ok`, `values` (each field made its kind), `problems` (each wrong field's message, in order) and `first_problem` |
| `is_email(words)`, `is_address(words)` | one value checked |

A field's kind is `"text"`, `"number"`, `"whole number"`, `"yes or no"`, `"email"`, `"address"` (an http:// or
https:// one), `"date"` (`"2026-11-04"`, made a date as `dates.folio` makes them), `"list"`, `"dictionary"`,
`"any"`, or rules of their own, for a field that holds a record: its problems are named `home.town`.

Text is taken with the spaces at its ends left out, and a needed field that's empty text is missing, as a form
sends one. Fields the rules don't name are left out of the values, so what a program keeps is only what it
asked for.

## Tests

```text
folio test validate.test.folio
```

## License

Apache License 2.0, as Folio is: see [LICENSE](LICENSE).

#Elm

![logo](assets/img/logo.png)

#VSLIDE
### What is it?

- Made in 2012 by Evan Czaplicki
- Easy to use functional programming language
- Made to develop webapps

#VSLIDE
### What is it?

- Compiles to JavaScript
- Can be used with and interact with JavaScript code
- **Produces no runtime exception**

#HSLIDE
## Compiler

The aim of Elm's compiler is to compile your Elm code into a crashless JS code
- makes sure that there are no type mismatches
- makes sure that all cases/branches are handled

With the absence of null/undefined in the language,
crashes are then automatically detected.

#VSLIDE

When you have errors,
it tries to be very friendly and helpful

```elm
"Hello" + " world"
```

![](assets/img/elm-string-concat-operator-error.png)

#VSLIDE

Variable not found

```elm
addSomeNumbers a b = a + b

result = addSomeNumber 1 2
```

![](assets/img/elm-variable-not-found.png)

#HSLIDE
## Language Basics

#VSLIDE
### Basic types

```elm
1 + 2 -- number
"hello" ++ " world" -- String - only with " quotes
```

#VSLIDE
### Lists & tuples

- List: Contains an undefined number of elements of the same type
- Tuple: Contains a defined number of elements whose type are defined but can be different

```elm
names = ["John", "Jane"] -- List String
result = (404, "Not found") -- Tuple -- (number, String)

List.reverse names
-- ["Jane", "John"] -- List String
```

#VSLIDE
### Records

```elm
httpResult = { code = 404, error = "Not found" }
-- { code : number, error : String }

httpResult.code -- 404
.code httpResult -- 404

httpResultWithSuccess = { httpResult | code = 200 }
-- { code = 200, error = "Not found" }
```

#VSLIDE
### Function declaration

```elm
add a b =
  a + b -- function body

divide a b =
  let
    quotient = a // b
    remainder = a % b
  in
    (quotient, remainder)
```

#VSLIDE
### Functions usage

```elm
result = add 1 2

-- functions are curried
add1 = add 1
result = add1 2
```

#VSLIDE
### If else

- No if without else
- It's an expression

```elm
result =
  if foo == 0 then
    1
  else
    2
```

#HSLIDE
## Types

#VSLIDE
### Type annotations

Type annotations are meant for programmers,
because Elm can discover most of the types on its own.

```elm
add: number -> number -> String
--   number -> -- Takes a number as a parameter
--             number -> -- Takes a number as a parameter
--                       String -- returns a String
add a b = toString (a + b)

getName: { name: String } -> String
getName a = a.name
```

#VSLIDE
### Creating types

```elm
type Bool = True | False
-- This is actually how Bool is implemented

type HttpResponse
  = HttpError number String
  | HttpSuccess String

type Maybe a
  = Nothing
  | Just a

type alias User = {
  name : String
}
```

#VSLIDE
### Conditional case

```elm
fib n =
  case n of
    0 -> 0
    1 -> 1
    _ -> fib (n - 1) + fib (n - 1)

errorMessage: Maybe String -> String
errorMessage error =
  case error of
    Just message -> message
    Nothing -> ""
```

#HSLIDE
## Concepts

#VSLIDE
### Immutability

All values in Elm are immutable.
- You can declare variables, but there is no reassignment
- There are no operators or functions that mutate data

```elm
a = 1 -- OK: declaring a variable
a = 2 -- NOT OK: reassigning a variable
```

#VSLIDE
### No side-effects

Effects are represented by Tasks.
Tasks are plain data structures composed of:
- The action to do
- What to do (dispatch) on success
- What to do (dispatch) on failure

They are passed to Elm's runtime which will execute it

```elm
getRandomGif topic =
  let
    url =
      "https://api.giphy.com/v1/gifs/random?api_key=dc6zaTOxFJmzC&tag=" ++ topic
  in
    Task.perform HttpError HttpSuccess (Http.get decodeGifUrl url)
```

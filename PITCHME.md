Elm

![logo](assets/img/logo.png)

#HSLIDE
What I want

#VSLIDE
I want to build products

#VSLIDE
but I am scared of breaking things

#VSLIDE

```js
validationPromise = () => {
  return new Promise((resolve, reject) => {
    return this.validate() ? resolve() : reject('invoice')
  })
}

onErrorSubmit = error => {
  // not very nice. could improve with error throwing
  const expandInvoiceFields =
    error == 'invoice' ? true : this.state.invoiceFields
  // ...
}
```

#VSLIDE
Can I modify this piece of code without breaking anything?

#VSLIDE

- Flow / TypeScript
- ESLint (I wrote 75 rules, and we enabled 328 rules at my previous company)
- Test-Driven Development
- React propTypes
- Functional programming techniques

They help, but they **only** help

#VSLIDE
I spent my time fixing bugs

#VSLIDE
Bug fixing **!=** building a product

#VSLIDE
I want guarantees that my changes will not break anything

#VSLIDE
If only I had a tool to give me these guarantees...

#HSLIDE
# Elm

#VSLIDE

- Easy to use functional programming language
- Designed to develop webapps
- **Produces no runtime exception**

#VSLIDE

- Compiles to JavaScript
- Can be used with and interact with JavaScript code

#VSLIDE
## Compiler

- Ensures that the resulting code won't crash
- Helps you fix the errors

#VSLIDE

Type mismatch

```elm
addNumbers a b = a + b

result = addNumber 1 "2"
```

![](assets/img/elm-type-mismatch.png)
https://ellie-app.com/3DtT78bwsqxa1

#VSLIDE

Variable not found

```elm
addNumbers a b = a + b

result = addNumber 1 2
```

![](assets/img/elm-variable-not-found.png)
https://ellie-app.com/3DtCx94GVkda1

#VSLIDE

Import cycle

```elm
-- in Main.elm
import Foo exposing (..)

-- in Foo.elm
import Main exposing (..)
```

![](assets/img/elm-import-cycle.png)

#HSLIDE
## Language Basics

#VSLIDE
### Immutability

All variables in Elm are constants.
- You can declare constants, but there is no reassignment
- There are no operators or functions that mutate data

```elm
a = 1 -- OK: declaring a variable
a = 2 -- NOT OK: reassigning a variable
```

#VSLIDE
### Basic types

```elm
1 + 2 -- Int
"hello" ++ " world" -- String - only with " quotes
```

#VSLIDE
### Lists & tuples

- List: Contains an undefined number of elements of the same type
- Tuple: Contains a defined number of elements whose type are defined but can be different

```elm
names = ["John", "Jane"] -- List String
result = (404, "Not found") -- Tuple -- (Int, String)

List.reverse names
-- ["Jane", "John"] -- List String
```

#VSLIDE
### Records

```elm
httpResult = { code = 403, error = "Forbidden" }
-- { code : Int, error : String }

httpResult.code -- 403
.code httpResult -- 403

httpResultWithSuccess = { httpResult | code = 200 }
-- { code = 200, error = "Forbidden" }
```

#VSLIDE
### If else

- No if without an else
- The whole thing is an expression
- Every "branch" needs to return the same type

```elm
result =
  if foo == 0 then
    1
  else
    2
```

#VSLIDE
### Function declaration

```elm
add a b =
  a + b -- function return value

divide a b =
  let
    -- function intermediate constants
    quotient = a // b
    remainder = a % b
  in
    (quotient, remainder) -- function return value
```

#VSLIDE
### Currying and partial application

Functions are curried and support partial application

```elm
result = add 1 2
-- 3

-- add1: Function that will add 1 to parameter that it's given
add1 = add 1
result = add1 2
-- 3
```

#HSLIDE
## Types

#VSLIDE
### Type annotations

Type annotations are meant for programmers,
because Elm can discover most of the types on its own.

```elm
add: Int -> Int -> String
--   Int -> -- Takes a integer as a parameter
--          Int -> -- Takes a integer as a parameter
--                 String -- returns a String
add a b =  String.fromInt (a + b)

getName: { name: String } -> String
getName a = a.name
```

#VSLIDE
### Type aliases

Great for documentation and simplifying

```elm
type alias User =
    { name : String
    , age: Int
    }

updateName : String -> User -> User
-- Similar to
updateName : String -> {name: String, age: Int} -> {name: String, age: Int}
```

#VSLIDE
### Type aliases

Fields are not optional

```elm
type alias User =
    { name : String
    , age: Int
    }

-- The compiler will not compile, as `age` is absent
user : User
user =
    { name = "Jeroen"
    }
```

#VSLIDE
### Creating custom types

```elm
type Bool = True | False
-- This is actually how Bool is implemented

type HttpResponse
  = HttpError Int String -- HttpError will contain a Int and a String value
  | HttpSuccess String -- HttpSuccess will contain a String value

type Maybe a -- Maybe is generic and can take any type as argument
  = Nothing
  | Just a
```

#VSLIDE
### Pattern matching

```elm
errorMessage: Maybe String -> String
errorMessage error =
  case error of
    Nothing -> ""
    Just message -> message
```

#HSLIDE
## Concepts

#VSLIDE
### Functional programming

Functional programming is about eliminating side-effects
where you can, control them where you can't.
                                            Kris Jenkins

https://www.youtube.com/watch?v=tQRtTSIpye4
http://blog.jenkster.com/2015/12/what-is-functional-programming.html

#VSLIDE
### No side-effects, only well-handled effects

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
    Http.send HttpError HttpSuccess (Http.getString url)
```

#HSLIDE
### The Elm Architecture

#### Model view update

Basic components that form a reusable architecture

```elm
import Browser

main =
  Browser.sandbox
    { init = initialModel
    , view = view
    , update = update
    }
```


#VSLIDE
### Model

The state of your application

```elm
type alias Model =
  { userNames: List String,
  , error: Maybe String
  }

initialModel : Model
initialModel =
  { userNames: [], error: Nothing }
```

#VSLIDE
### update

A way to update your state
```elm
type Msg
    = FetchUsersSuccess (List String)
    | FetchUsersError String
    | AddUser String

update: Msg -> Model -> Model
update msg model =
  case msg of
    FetchUsersSuccess users ->
      { model | users = users, error = Nothing }
    FetchUsersError error ->
      { model | error = Just error }
    AddUser user ->
      { model | users = user :: model.users}
```

#VSLIDE
### view

A way to view your state as HTML

Uses Elm's own virtual dom library

```elm
import Html exposing (..)

view : Model -> Html Msg
view model =
  div [] [ viewError model.error
         , viewBrands model.brands
         , button
            [onClick (AddUser "Jeroen")]
            [text "Add user"]
         ]
```

#VSLIDE
### view

```elm
viewError: Maybe String -> Html Msg
viewError error =
  case error of
    Just err -> span [ class "error" ] [ text err ]
    Nothing -> span [] [ text "" ]

viewBrand: String -> Html Msg
viewBrand brand =
  span
    [ class "brand", onClick (\_ -> EditBrand brand) ]
    [ text brand ]

viewBrands: List String -> Html Msg
viewBrands brands =
  div [ class "brands" ]
      (List.map viewBrand brands)
```

#VSLIDE
### In a bad drawing

![](assets/img/the-elm-architecture.jpg)


#VSLIDE
### Re-using a component

```elm
import BrandsPage

type alias Model = {
  brands: BrandsPage.Model,
  users: UserPage.Model
}

type Msg
  = Reset
  | BrandsPage.Msg

update msg model =
  case msg of
    BrandsPage.Msg ->
      { model | brands = BrandsPage.update msg model.brands }

view model =
  div [] [ BrandsPage.view model.brands ]
```

#VSLIDE
### Optional: Subscriptions

```elm
import WebSocket

main =
  Browser.document
    { init = init, view = view, update = update, subscriptions = subscriptions }

subscriptions : Model -> Sub Msg
subscriptions model =
  WebSocket.listen "ws://echo.websocket.org" NewMessage

update: Msg -> Model -> (Model, Cmd Msg)
update msg model =
  case msg of
    Send ->
      (model, WebSocket.send "ws://echo.websocket.org" "a message")

    NewMessage str ->
      ({ model | messages = str :: messages }, Cmd.none)
```

#HSLIDE
## Ports

System to communicate safely with JavaScript code by message passing.

The JS code may crash, but the Elm code won't.

#VSLIDE
## Package manager

Elm has its own package manager.

Packages are semver enforced: breaking changes require a major version.

Only Elm code can be published (no ports)

#HSLIDE
### Overview

#VSLIDE
### Elm is great for refactoring

#VSLIDE

What weird states can I have here?
```elm
type alias Model =
    { questions : List String
    , answers : List (Maybe String)
    }
```

#VSLIDE

What weird states can I have here?
```elm
type alias Model =
    { questions : List String
    , answers : List (Maybe String)
    }

{ questions: []
, answers: [Just "Yes"]
}
```

#VSLIDE
## Creating guarantees

```elm
type alias Question =
    { question : String
    , response : Maybe String
    }

type alias Model = List Question
```
Having more answers than questions is now impossible to represent

#VSLIDE
## Creating guarantees (resources)

"Making Impossible States Impossible" by Richard Feldman
https://www.youtube.com/watch?v=IcgmSRJHu_8

"How Elm slays a UI antipattern" by Kris Jenkins
http://blog.jenkster.com/2016/06/how-elm-slays-a-ui-antipattern.html

#VSLIDE
## Keep less in your head

Somewhere in our code, we are sending a HTTP request, let's look for it
```elm
updateName : String -> User -> User
updateBirthday : Date -> User -> User
saveUser : User -> Cmd Msg
```

#VSLIDE
## Keep less in your head

"Scaling Elm apps" by Richard Feldman
https://www.youtube.com/watch?v=DoA4Txr4GUs

"Advanced Types in Elm (series)" by Charlie Koster
https://medium.com/@ckoster22/advanced-types-in-elm-opaque-types-ec5ec3b84ed2

#VSLIDE
## No magic

- The code is explicit, and there is no weird voodoo magic
- Instead there is boilerplate

- "One thing I've learned from Rails: there are worse things than boilerplate." Richard Feldman

#VSLIDE
## Things that will be hard

- Re-learning everything that we used side-effects for
  - Updating a value in an array -> List.map
  - Randomness, getting the time, ... -> Cmd
  - ...

#VSLIDE
## The language will evolve

- Language features will be added or removed to make the language **simpler**
- Breaking changes
- Elm 0.18 -> 0.19: 21 months

#HSLIDE
## How to go forward

https://elm-lang.org/blog/how-to-use-elm-at-work

#VSLIDE
## The checklist

- Have an advocate: Vincent & Me
- Start Small: Introduce Elm gradually
- Fix a Problem: Resolve issues that hurt your team every day.
- Write Elm Code: Write Elm, and compare it to the JavaScript code
- Use Elm to fix a problem, not to just replace it

- No big refactor

#VSLIDE
## Additional resources

- https://guide.elm-lang.org (Official learning guide)
- https://elmlang.slack.com/ (#beginners)
- https://ellie-app.com/ (Online REPL)
- https://www.manning.com/books/elm-in-action
- https://github.com/avh4/elm-format

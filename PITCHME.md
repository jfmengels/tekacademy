Elm

![logo](assets/img/logo.png)

#HSLIDE
# Elm

- Easy to use functional programming language
- Designed to develop webapps
- **Produces no runtime exception**

#VSLIDE

- Compiles to JavaScript
- Can be used with and interact with JavaScript code

#HSLIDE
## Language Basics

#VSLIDE
### Immutability

All variables in Elm are constants.
- You can declare constants, but there is no reassignment
- There are no operators or functions that mutate data

#VSLIDE
### Basic types

```elm
1 + 2 -- Int
"hello" ++ " world" -- String
```

#VSLIDE
### Lists & tuples

- List: Contains an undefined number of elements of the same type
- Tuple: Contains a defined number of elements whose type are defined but can be different

```elm
names = ["John", "Jane"] -- List String

result = (404, "Not found") -- Tuple -- (Int, String)
```

#VSLIDE
### Records

```elm
httpResult = { code = 403, error = "Forbidden" }
-- { code : Int, error : String }

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
### Pattern matching

```elm
boolToString: Bool -> String
boolToString bool =
  case bool of
    True -> "True"
    False -> "False"
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
### Function usage

Use the name of the function, then add the arguments separated by spaces.
No parens or commas.

```elm
result = add 1 2
-- 3
```

#VSLIDE
### Currying

Functions are curried and support partial application

```elm
-- add1 is a function that will add 1 to parameter that it's given
add1 = add 1
result = add1 2
-- 3
```

#HSLIDE
## Compiler

- Ensures that the resulting code won't crash
- Helps you fix the errors with helpful messages

#VSLIDE

Type mismatch

```elm
addNumbers a b = a + b

result = addNumbers 1 "2"
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

Let's code an app: Message board

https://ellie-app.com/6fjwgPWpqyja1

#VSLIDE

Display messages

https://ellie-app.com/3DYTwcT2tLma1

#VSLIDE

```elm
main =
    Browser.sandbox
        { init = init
        , view = view
        , update = update
        }
```

#VSLIDE

```elm
init =
    { messages = [ "Oh hi there!", "How you doing?" ] }

update msg model =
    model

view model =
    ul [] (List.map viewMessage model.messages)

viewMessage message =
    li [] [ text message ]
```

#VSLIDE

```elm
li [] [ text message]
```
```react
<li /* No attributes */ >
  {message}
</li>
```
```HTML
<li>
  Oh hi there!
</li>
```


#VSLIDE

Add an input field

https://ellie-app.com/6fjG6Jfdt3sa1

#VSLIDE

```elm
init = { messages = [ "Oh hi there!", "How you doing?" ], content = "" }

update msg model =
    case msg.kind of
        "UserUpdatedContent" -> { model | content = msg.str }
        _ -> model

view model =
    ...
    , input [
        type_ "text",
        onInput (\str -> { kind = "UserUpdatedContent", str = str}),
        value model.content
      ] []
```

#VSLIDE

Post a message

https://ellie-app.com/6fjNwVL85dwa1

#VSLIDE

```elm
update msg model =
  case msg.kind of
    "UserClickedOnPostButton" ->
      { model | content = "", messages = List.append model.messages [ model.content ] }

view model =
  ...
  , button [
    onClick { kind = "UserClickedOnPostButton", str = "" }
  ] [ text "Post" ]
```

#VSLIDE
### The Elm architecture

![](assets/img/the-elm-architecture.jpg)

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
add a b = String.fromInt (a + b)

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
updateName :
  String -> {name: String, age: Int} -> {name: String, age: Int}
```

#VSLIDE
### Custom types

```elm
type CardColor = Black | Red
type CardValue = Ace | King | Queen | Jack | Two | Three | ...
type alias Card = { color: CardColor, value: CardValue }

type HttpResponse
  = HttpError Int String -- will contain a Int and a String value
  | HttpSuccess String   -- will contain a String value
```

#HSLIDE
## Effects

#VSLIDE
### Functional programming

Functional programming is about eliminating side-effects
where you can, control them where you can't.
                                            Kris Jenkins

https://www.youtube.com/watch?v=tQRtTSIpye4
http://blog.jenkster.com/2015/12/what-is-functional-programming.html

#VSLIDE
### No side-effects, only well-handled effects

To apply effects, create a request for it, and return it to the runtime.

```elm
update : Msg -> Model -> ( Model, Cmd Msg ) =
update msg model =
    case msg.kind of
      "RequestUrl" -> (model, getRandomGif "cats")
      "NewGif" -> ({model | body = msg.body}, Cmd.none)

getRandomGif topic =
  Http.get {
    url = "https://api.giphy.com/v1/gifs/random?api_key=dc6zaTOxFJmzC",
    expect = (\body-> { kind = "NewGif", body = body })
  }
```

#VSLIDE
## Ports

System to communicate safely with JavaScript code by message passing.

The JS code may crash, but the Elm code won't.

#HSLIDE
# Creating guarantees

#VSLIDE
## Problem modeling


#VSLIDE
## Modeling data fetching in JS

```js
const state = {
  isLoading: true | false,
  data: []
}
```

#VSLIDE
## Modeling data fetching in JS

Handling errors

```js
const state = {
  isLoading: true | false,
  data: [...],
  error: null | String
}
```

#VSLIDE
## Modeling data fetching in JS

What happened here? And what should we do?

```js
const state = {
  isLoading: true,
  data: ["Some", "data"],
  error: "HTTP 404"
}
```

#VSLIDE
## Modeling data fetching in Elm

```js
type State
  = Loading
  | Errored Http.Error
  | Succeeded (List String)
```

#VSLIDE
## Modeling data fetching in Elm

Use custom types for the `Msg`
https://ellie-app.com/3DYXXYSBFRga1

```elm
type Msg = SetContent String | PostMessage

update : Msg -> Model -> Model
update msg model =
    case msg of
        SetContent str -> { model | content = str }
        PostMessage -> ...

view : Model -> Html Msg
view : button [ onClick PostMessage ] [ text "Post" ]
```

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
type alias QuestionAndAnswer =
    { question : String
    , answer : Maybe String
    }

type alias Model = List QuestionAndAnswer
```
Having more answers than questions is now impossible to represent

#VSLIDE
## Creating guarantees (resources)

"Making Impossible States Impossible" by Richard Feldman
https://www.youtube.com/watch?v=IcgmSRJHu_8

"How Elm slays a UI antipattern" by Kris Jenkins
http://blog.jenkster.com/2016/06/how-elm-slays-a-ui-antipattern.html

#VSLIDE
## No magic

- The code is explicit, and there is no weird voodoo magic
- Instead there is boilerplate

- "One thing I've learned from Rails: there are worse things than boilerplate." Richard Feldman

#HSLIDE
## Workshop

Take the previous message board application,
and add a "flush" button that removes all messages.

Start with https://ellie-app.com/3DYXXYSBFRga1
Solution: https://ellie-app.com/3DYYWy3nrjBa1

#VSLIDE

Go further
- Disable post button if field is empty
- Add an input field to set a user name that will display next to each message

#HSLIDE

# Tips for being productive

#VSLIDE

Learn Elm!
Go through the Elm official guide: http://guide.elm-lang.org

#VSLIDE

All variables can be found in a file or in the imports, except for those in
the following list https://github.com/elm/core/blob/1.0.2/README.md#default-imports

(They all come from [`elm/core`](https://package.elm-lang.org/packages/elm/core/latest))

#VSLIDE

You can use [`Debug.log`](https://package.elm-lang.org/packages/elm/core/latest/Debug#log) to print a message in your console.

```elm
Debug.log "helpful message" value
```
Warning: It will not log anything if you only pass it one argument!

#VSLIDE

If you're stuck, you can ask for help on the [Elm slack](http://elmlang.herokuapp.com/)
on #beginners or #help

They're very nice and helpful (and I answer sometimes too)
(Please do this if I'm not here)
#VSLIDE

Configure your editor to show compiler errors

#VSLIDE

Read the compiler errors to the end!

#VSLIDE

When failing to compile, prioritize having the code recompile
i.e. Don't make big changes all at once
https://incrementalelm.com/articles/moving-faster-with-tiny-steps

#VSLIDE

Configure your editor to format on save using https://github.com/avh4/elm-format

#VSLIDE

The version of Elm we're using is `0.19.0`. `O.19.1` will be out in a shortish while.

#VSLIDE

You can find the list of dependencies in the `elm.json` file.

#VSLIDE

Install https://github.com/dmy/elm-package-info in your browser

This will tell you the Elm version of a package on https://package.elm-lang.org/.
Packages for old versions on Elm are still googlable
(like those from elm-lang/* and evancz/)

#VSLIDE

Our Main file is `Main.elm`, and it all starts with the `main` function.
From there on, no magic anymore (but some complex code).

#VSLIDE

- On the funnel, to develop, run `npm run watch -s` and `npm run watch-elm-analyse -s`
- To run tests, run `npm test`

#HSLIDE

# Quick explanations
(and avoiding easy pitfalls)

#VSLIDE

When working with JSON or decoders, read
https://guide.elm-lang.org/effects/json.html
(go down to the "JSON" section, or read it all)

and ask questions on the Elm slack if you don't get it!

#VSLIDE

To understand the `|>`, `<|`, `>>` and `<<` operators that you see:
- https://package.elm-lang.org/packages/elm/core/latest/Basics#(%3C|)
- https://www.smoothterminal.com/articles/understanding-pipes-in-elm

#VSLIDE

What are subscriptions?

They are the way to get events that don't depend on user input (subscribing to the current time). They will be transformed explicitly into `Msg` and sent to `update`.

```elm
subscriptions : Model -> Sub Msg
subscriptions model =
  Time.every 1000 Tick
```

#VSLIDE

What does this mean?
```elm
type Foo = Foo Something
```
This is an opaque type, it wraps `Something`, and basically prevents other modules
from using anything from the implementation. (They're awesome)

https://medium.com/@ckoster22/advanced-types-in-elm-opaque-types-ec5ec3b84ed2

#VSLIDE

I ran into an error that looks like
```
This argument is a list of type:

    List (Html Msg)

But `div` needs the 2nd argument to be:

    List (Html msg)
```

https://gitlab.legalstart.fr/yolaw/knowledgebase/merge_requests/68

#VSLIDE

```elm
import Data.Funnel as Funnel exposing (Funnel)
```
- `import Data.Funnel`: That's the imported module
- `as Funnel`: That's the alias we give to the module, so that we can write `Funnel.someFunction` instead of `Data.Funnel.someFunction`
- `exposing (Funnel)`: We import the `Funnel` type, declared in the module.
You can use [`Debug.log`](https://package.elm-lang.org/packages/elm/core/latest/Debug#log) to print a message in your console.

#HSLIDE

![](assets/img/elm-lol.png)

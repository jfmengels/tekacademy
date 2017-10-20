# Test-Driven Development
### and their uses

#HSLIDE
### What is an AST?

- Representation of code in a big tree-like object
- Only represents the syntax, not the style
- (With style: Concrete Syntax Trees)

#VSLIDE
### What does it look like?

```js
A
```

```json
{
  "type": "Identifier",
  "name": "A"
}
```

#VSLIDE
### What does it look like?

```js
A + 1
```

```json
{
  "type": "BinaryExpression",
  "left": {
    "type": "Identifier",
    "name": "A"
  },
  "operator": "+",
  "right": {
    "type": "Literal",
    "value": 1
  }
}
```

#VSLIDE
### What does it look like?

```js
A + 1 - "bar"
```

```js
{
  "type": "BinaryExpression",
  "left": { /* tree from previous slide */ },
  "operator": "-",
  "right": {
    "type": "Literal",
    "value": "bar"
  }
}
```

#VSLIDE
### What does it look like?

```js
foo(bar)
```

```js
{
  "type": "CallExpression",
  "callee": {
    "type": "Identifier",
    "name": "foo"
  },
  "arguments": [
    {
      "type": "Identifier",
      "name": "bar"
    }
  ]
}
```

#VSLIDE
### What does it look like?

The style does not matter.
These are represented the same way.
```js
A + 1 - "bar"
```

```js
(A + 1) - 'bar';
```


#VSLIDE
### Different parsers, different ASTs

- Parser: tool that transforms your code into an AST
- Plenty of parsers: acorn, espree, babylon, babel-eslint, typescript.












#HSLIDE
## ESLint

![eslint](assets/img/eslint.png)

#VSLIDE
### ESLint

- Looks at your source code and reports things
- Consists of a lot of different rules (core/plugin) reporting different things


#VSLIDE
### ESLint rules

```js
module.exports = {
  create: create,
  meta: { /* ... */ }
};

function create(context) {
  // context: rule options, global settings,
  //          getFilename(), ...
  context.report({
    node: <node>,
    message: 'Stop doing things this way'
  });

  return { /* visitors */ };
}
```

#VSLIDE
### ESLint rules - Visitors

```js
{
  "type": "BinaryExpression",   // 1
  "left": {
    "type": "BinaryExpression", // 2
    "left": {
      "type": "Identifier",     // 3
      "name": "A"
    },
    "operator": "+",
    "right": {                  // 4
      "type": "Literal",
      "value": 1
    }
  },
  "operator": "-",
  "right": {                    // 5
    "type": "Literal",
    "value": "bar"
  }
}
```

#VSLIDE
### ESLint rules - Visitors

```js
function create(context) {
  return {
    Literal(node) {
      // node: {
      //   "type": "Literal",
      //   "value": string | number
      // }
      if (typeof node.value === 'number' &&
        node.value > 9000
      ) {
        context.report({
          node,
          message: "It's over 9000!"
        });
      }
    }
  };
}
```

#VSLIDE
### ESLint rules - No describe.only

```js
describe.only(/* anything */)
```

```json
{
  "type": "CallExpression",
  "callee": {
    "type": "MemberExpression",
    "object": {
      "type": "Identifier",
      "name": "describe"
    },
    "property": {
      "type": "Identifier",
      "name": "only"
    }
  }
}
```

#VSLIDE
### ESLint rules - No describe.only

```js
function create(context) {
  return {
    CallExpression(node) {
      if (node.callee.type === 'MemberExpression' &&
          node.callee.object.type === 'Identifier' &&
          node.callee.object.name === 'describe' &&
          node.callee.property.type === 'Identifier' &&
          node.callee.property.name === 'only'
      ) {
        context.report({
          node,
          message: 'No describe.only!'
        });
      }
    }
  };
}
```

#VSLIDE
### ESLint rules - Testing

```js
const {RuleTester} = require('eslint');
const rule = require('./path/to/rule');
const ruleTester = new RuleTester();

ruleTester.run('no-describe-only', rule, {
    valid: [
      'describe()',
      'only()',
      'describe.foo()'
    ],
    invalid: [
      {
        code: 'describe.only()',
        errors: [ { message: 'No describe.only!' } ]
      },
    ]
});
```







#HSLIDE
## Babel

![babel](assets/img/babel.png)

#VSLIDE
### Babel

- Transforms your source code and writes it into a new file
- Meant to be used in your build chain
- Uses Babylon as the parser
- Uses
  - New syntactic language features (ES2015+)
  - Minification
  - Test coverage
  - Flow

#VSLIDE
### Babel - Making const immutable

```js
const variable = value;
```

-->

```js
const variable = Object.freeze(value);
```

#VSLIDE
### Babel - Making const immutable

```js
const variable = value;
```

```json
{
  "type": "VariableDeclaration",
  "kind": "const",
  "declarations": [
    {
      "type": "VariableDeclarator",
      "id": {
        "type": "Identifier",
        "name": "variable"
      },
      "init": {
        "type": "Identifier",
        "name": "value"
      }
    }
  ]
}
```

#VSLIDE
### Babel - Making const immutable

```js
export default function (babel) {
  const { types: t } = babel;

  return {
    visitor: {
      VariableDeclaration(path) {
        if (path.node.kind === 'const') {
          path.node.declarations.forEach(declaration => {
            declaration.init = t.callExpression(
              t.memberExpression(
                t.identifier('Object'),
                t.identifier('freeze')
              ),
              [declaration.init]
            );
          });
        }
      }
    }
  };
}
```



#HSLIDE
## Codemods

### jscodeshift

#VSLIDE
### jscodeshift

- Transforms your source code and saves the changes
- Meant to be used to update your source code easily
  - var -> let/const
  - Lodash v3 -> v4
  - Treant -> React
- Uses Recast as the parser


#VSLIDE
### jscodeshift - Making const immutable

```js
export default function transformer(file, api) {
  const j = api.jscodeshift;
  return j(file.source)
    .find(j.VariableDeclaration, {kind: 'const'})
    .forEach(path => {
      path.node.declarations.forEach(declaration => {
        declaration.init = j.callExpression(
          j.memberExpression(
            j.identifier('Object'),
            j.identifier('freeze')
          ),
          [declaration.init]
        );
      });
    })
    .toSource();
}
```



#HSLIDE
### Tools

- https://github.com/jfmengels/eslint-ava-rule-tester
- https://github.com/jfmengels/jscodeshift-ava-tester
- https://astexplorer.net/

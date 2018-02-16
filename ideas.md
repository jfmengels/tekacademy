# TDD

- TDD is a disciplined method of writing code, that puts the emphasis on testing before writing code in an iterative process

- Brings benefits that can only be gained by writing the code in a certain way. The benefits are absent if the tests are written afterwards.

- Consists of a discipline
  - :warning: "I've finished the code, now I'm adding tests"

- List the benefits
  - Almost guaranteed 100% code coverage
  - Resulting code is really well-tested
  - Makes sure each component is testable, by making it easy to test

- List the cons
  - If the problem is not well analyzed, you may be concentrating a lot of effort on something that you'll have to throw away.
  - Hard to do when components are not easily testable
  - Painful when tests are slow
  - Need to find where the tests are for existing functions before working on it, and understand them

- Explain process [TDD-graph]
  - Write a test
  - Run the test, and make sure that it fails. If it doesn't, then the test is probably not well written
  - Add the code to make the test pass. Try to make the code as simple as possible, **even stupidly simple**
  - Run the test that it passes
  - Refactor the code without breaking tests or adding support for new cases [Suggest not to do it every time)]

- Writing a test is either writing a new test, writing a new assertion in an existing test. The effect is the same.

- When adding code, always try to think "What could go wrong with what I just added?" (dividing by zero, accessing a property of something undefined, not `_.find`ing a value in an array), and add tests for those cases (that you may skip for now, but will need to handle at some point).

- Whenever you think of a case, write the test title `test.todo('should...')` and implement it later.

- If you make the code more complex at an iteration, then you may have handled a case for which you do not have a test.

- What to do if code coverage is not 100%?
  - Find out if the not-covered code is necessary.
    - If it is unnecessary
      - Remove the code
    - If it is necessary
      - comment out the code
      - Re-add it the TDD way

- How to apply this when solving bugs
  - Find out the cause of the bug without writing any code, or solve it to make sure you found the cause of the issue and then remove all the code you've written (put it in a branch so it won't get lost)
  - Write tests and (re)write the code the TDD way

- What if add additional tests to something that already works?
  - Add the tests, then temporarily comment out the related piece of code to make sure they fail
  - Don't hesitate to add tests as soon as you think of them (what if number is 0 in this case?)

- Remind what tests are good for
  - Non-regression
  - Documentation
  - Ease of mind (breaking things)

- Anatomy of a good test
  - Title
    - You should understand what the function does if you only read the test titles
    - Good title
      - explains the results based on the conditions of the test, both being generalized.
      - A test should indicate the factor that makes it behave the way it does (should X when/if <factor>)
      - Behavior?
      - Form: expect <result> given <conditions>.
      - Example:
        - should return false when password is less than expected length
        - should return false when password does not contain a number and number is required
        - should return true when password contains a number and number is required
        - should return true when password does not contain a number and number is not required
    - Bad title: should return false for "abcd" # Which behavior is tested here?
    - Bad title: should work
    - Compare good and bad list of test titles. Example: isAdmin
      - Bad
        - should return true for Simon // Why is he considered as an admin
        - should return true for Paul // How are Paul and Simon different, and why does it mandate a different test
        - should return false for Samantha // Oh, now I get it. It's a guy thing. Well, f*** you
      - Good
        - should return true if "admin" is in the user's list of roles // Ok, makes sense
        - should return true if "admin" is in the user's list of roles, but not the first role // Ok, we probably had a bug with this at some point
        - should return false if "admin" is not in the user's list of roles // Gotcha
  - Self-sufficient with no effects on other tests
  - Assertions should be as precise as possible
    - `t.is(user.displayname, 'John Smith')` instead of `t.is(typeof user.displayname, 'string')` or `t.truthy(user.displayname)` (aka `expect(user.displayname).to.exist`) whenever possible.
    - `t.deepEqual(result, {a: 1, b: 2})` instead of `t.is(result.a, 1); t.is(result.b, 2)`, unless the test explicitly checks the value of a certain field (grey area)

- Comparison of tests written in TDD style and in tests written post-code style, needed to get 100% code coverage

- Code coverage is a measure of non-tested code, not a measure of test quality
  - "70% code coverage" should not be read as "70% of the code is (well-) tested", but as "30% of the code can be deleted without tests failing".
- There are no good metrics to indicate and/or verify how well tested a piece of code is. That's why the benefits of TDD are important and hard to reproduce after the fact.
- A well-written test suite and function should fail if any part of the tested code is removed or changed. This might be the only metric important as to the real coverage of a piece of code.
- An object with plenty of fields counts as one line or instruction. It's easy to think that it's covered without having any tests that check the produced values.

- Removes the need to ask oneself "Have I written enough tests? Have I covered all cases?". If you have more code to write, you have not written enough tests. More tests are always welcome though, as long as they are relatively fast).

- This method requires being able to run tests really fast, in order to make the developer as productive as possible.
  - Easy to do for simple systems (functions), but harder when working with more complicated ones (API, E2E, tests with access to DB, tests that depend on each other...)
  - Run tests in watch mode: `NODE_ENV=test <ava|mocha> /path/to/my/file -w`.
  - Run one or a few tests in file using `.only` and `.skip` while developing.

- Hard to apply on several levels: (ex: Unit test AND E2E test)

- Example to avoid:

```js
function foo(value) {
  return {
    type: 'bar',
    value,
    a: Math.floor(value * 20 / 100) + 100 / value,
    b: Array.from({length: value}).map(n => ({n, value: 100 / value}))
  };
}

test('should foo', t => {
  foo(); // 100% code coverage for `foo`, woot!
});

// But tests would still pass and code coverage would still be 100% if we change `foo` to
const foo = () => {};
```

- Example process of writing a function TDD-style (see process-example.md)

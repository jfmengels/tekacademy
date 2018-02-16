# TDD - Test-Driven Development

#HSLIDE
### What is Test-Driven Development?

- TDD is a **disciplined** method of writing code, that puts the emphasis on testing before writing code in an iterative process

- Brings benefits that can only be gained by writing the code in a certain way. The benefits are absent if the tests are written afterwards

#VSLIDE
### What TDD is absolutely not

"I've finished the code, now I'm adding tests"

![Grumpy](assets/img/grumpy.gif)



#HSLIDE
### The process
![TDD graph](assets/img/tdd-graph.png)
- If the test doesn't fail at first, then the test is probably not well written
- Refactor the code without breaking tests or adding support for new cases (optional)

#VSLIDE
### The process

- Adding a test: new test, new assertion in an existing test

- Whenever you think of a case, write the test title `test.todo('should...')` and implement it later.

#VSLIDE
### The process

- When writing code, try to make it **stupidly simple**, just enough to pass the test

- If you make the code more complex at an iteration, then you may have handled a case for which you do not have a test

- When adding code, always try to think "What could go wrong with what I just added?", and add tests for those cases

#VSLIDE
### The process when fixing bugs

- Find out the cause of the bug without writing any code (or by removing/stashing it)
- Write tests and (re)write the code the TDD way

#VSLIDE
### What to do if code coverage is not 100%?

- Find out if the piece of code is still useful and relevant
- It is isn't, remove it
- If it is, remove it and then re-add it the TDD way


#HSLIDE
## Good tests

#VSLIDE
### Good test title
- Reading the test titles should make you understand what the function does and when
- Explains the results based on the conditions of the test, both being generalized.
- Form "should <results> when/if <conditions>"

#VSLIDE
### Good test title

```
// Bad
should return false for "abcd"
  // Which behavior is tested here?
should work // GTFO

// Good
should return false when password
  is less than expected length
should return false when password
  does not contain a number and number is required
should return true when password
  contains a number and number is required
should return true when password
  does not contain a number and number is not required
```

#VSLIDE
### Good test title

```
// Bad
should return true for Jack // Why is he considered an admin?
should return true for Paul // How do Paul and Simon differ
  // and why does it mandate a different test?
should return false for Samantha // Is it a gender thing?

// Good
should return true if "admin" is in the user's list of roles
should return true if "admin" is in the user's list of roles
  but not the first role // Maybe this was a bug once
should return false if "admin"
  is not in the user's list of roles
```

#HSLIDE
### Benefits
- Almost guaranteed 100% code coverage
- Resulting code is really well-tested
- Components are testable, and therefore probably well-architectured
- Good test documentation

#VSLIDE
### Cons
- Time-consuming in the short run
- Requires good analysis of the problem, in order not to throw away a lot of work
- Painful when tests are slow
- Hard to do when components are not easily testable

# Testing Note

- [Testing Note](#testing-note)
  - [Tips](#tips)
  - [Code Coverage](#code-coverage)

---

## Tips

It is **recommended** to always come up with a descriptive name for the test method so it is immediately obvious what the unit test does without looking at the implementation of the test method.

Use `Assert.fail("Not yet implemented");` as a placeholder before you implement the test code.

---

## Code Coverage

Code coverage refers to how much of the source code of your software (i.e., how many lines or blocks) is tested by a set of tests.

It is a good idea to aim for high coverage. **Recommend** aiming for 70%–90%.

However, code coverage is not necessarily a good metric of how well you are testing your software. It does not say anything about the quality of your tests. 

Popular code coverage tools in Java include JaCoCo, Emma, and Cobertura. 
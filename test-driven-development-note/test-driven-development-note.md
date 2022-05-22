# Test Driven Development (TDD) Note

- [Test Driven Development (TDD) Note](#test-driven-development-tdd-note)
  - [Why Use TDD?](#why-use-tdd)
  - [TDD Cycle](#tdd-cycle)
  - [Mocking](#mocking)
    - [Mockito](#mockito)

---

## Why Use TDD? 

Benefits:

- Writing a test at a time will help you focus and refine the requirements by correctly implementing one thing at a time.
- It’s a way to ensure a relevant organization for your code. For example, by writing a test first, you need to think hard about the public interfaces for your code.
- You are building a comprehensive test suite as you iterate through the requirements, which increases confidence that you are matching the requirements and also reduces the scope of bugs.
- You don’t write code that you don’t need (over-engineer) because you’re just writing code that passes the tests.

---

## TDD Cycle

Steps: 

1. Write a test that fails. (due to without implementation)
2. Run all tests. (fail)
3. Make the implementation work.
4. Run all tests. (pass)

To follow the TDD philosophy, the test will initially fail. You always need to run the tests to begin with to ensure that they fail, otherwise you may write a test that accidentally passes.

---

## Mocking

How to write a test for a function that does not return any result? Mocking is a technique to verify that function operates correctly. 

### Mockito 

A popular mocking library for Java.

Steps: 

1. Create a mock.
2. Verify that a method is called.

Mockito allows you to specify sophisticated verification logic such as how many times a method should be invoked, with certain arguments, etc. 
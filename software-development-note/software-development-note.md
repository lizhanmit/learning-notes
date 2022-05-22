# Software Development Note

- [Software Development Note](#software-development-note)
  - [Experience](#experience)
    - [Java](#java)

---

## Experience 

Developers spend more time reading code than writing it. Therefore, you should think
about optimizing for ease of reading over ease of writing. You should always be focusing on what helps your teammates read your code.

### Java

When you explicitly declare the type of a variable, use the `final` keyword. Otherwise, use the `var` keyword without `final` for brevity (Java 10 feature) (not compulsory, if your teammates are not happy reading code with `var`, then do not use it).
# Software Development Note

- [Software Development Note](#software-development-note)
  - [Experience](#experience)
    - [Java](#java)

---

## Experience 

- Developers spend more time reading code than writing it. Therefore, you should think about optimizing for ease of reading over ease of writing. You should always be focusing on what helps your teammates read your code.
- Making decision at the beginning of your project and being forced to live with it forever is not a great architectural decision.
- Avoid coupling yourself to a specific technology.
- Software development needs just enough upfront design to avoid it collapsing into chaos, but architecture without coding enough bits to make it real can quickly become sterile and unrealistic.
- Introducing state in an API is bad because it will result in coupling.


### Java

When you explicitly declare the type of a variable, use the `final` keyword. Otherwise, use the `var` keyword without `final` for brevity (Java 10 feature) (not compulsory, if your teammates are not happy reading code with `var`, then do not use it).
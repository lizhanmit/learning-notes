# Web API Note

## REST API

REST: REpresentational State Transfer

REST 实际上只是一种设计风格，它并不是标准。

REST 是**无状态**的：服务器端不会存储有关客户端会话的任何状态。

REST 是**面向资源**的，资源是通过 URI 进行暴露。

URI 的设计只要负责把资源通过合理方式暴露出来就可以了。对资源的操作与它无关，操作是通过 HTTP动词来体现。不要在 URI 中出现动词。

REST API 是基于 HTTP的。REST很好地利用了HTTP本身就有的一些特征，如

- HTTP动词: GET, POST, PUT, DELETE ...
- HTTP状态码: 200, 400, 500 ...
- HTTP报头: Authorization, Cache-Control, Cnotent-Type ...

---

## Web API Design

Use single quote to escape comma in the index value if any, and use `\'` to escape `'` in the index value.  For example, we have 3 values `"abc"`,`"ab,c"` and `"it's ab,c"`, then it will be `indexName=abc,'ab,c','it\'s ab,c'` or `indexName=abc1[to]abc2,'ab,c1'[to]'ab,c2','it\'s ab,c1'[to]'it\'s ab,c2'`.
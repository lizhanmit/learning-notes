# Web API Note

## Web API Design

Use single quote to escape comma in the index value if any, and use `\'` to escape `'` in the index value.  For example, we have 3 values `"abc"`,`"ab,c"` and `"it's ab,c"`, then it will be `indexName=abc,'ab,c','it\'s ab,c'` or `indexName=abc1[to]abc2,'ab,c1'[to]'ab,c2','it\'s ab,c1'[to]'it\'s ab,c2'`.
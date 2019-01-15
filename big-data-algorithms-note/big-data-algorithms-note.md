# Big Data Algorithms Note

[亿万级数据处理的高效解决方案](https://www.jianshu.com/p/5448f130b94d)

## Typical Scenarios

### Large File，Small Memory，Min / Max Stat

Solution

1. Divide and conquer. Use "mod 1000 or 5000" (value here may vary) to divide the large file into multiple small files that can be loaded into memory. Same value will be allocated to same file or node.
2. Use HashMap or Trie Tree to get min / max in each small file.
3. Use file Heap or Merge Sort to get the final result.

---

## Heap

Find the Top N max numbers - 最小堆求前n大
Find the Top N min numbers - 最大堆求前n小

For typical TOP N problem，you can use HashMap + Heap Sort to solve it.

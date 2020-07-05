## 布隆过滤器和LRU缓存

### Bloom Filter vs Hash Table

一个很长的二进制向量和一系列随机映射函数。布隆过滤器可以用于检索 一个元素是否在一个集合中。

优点是空间效率和查询时间都远远超过一般的算法，

 缺点是有一定的误识别率和删除困难。

**具体的操作流程**：假设集合里面有3个元素{x, y, z}，哈希函数的个数为3。首先将位数组进行初始化，将里面每个位都设置位0。对于集合里面的每一个元素，将元素依次通过3个哈希函数进行映射，每次映射都会产生一个哈希值，这个值对应位数组上面的一个点，然后将位数组对应的位置标记为1。查询W元素是否存在集合中的时候，同样的方法将W通过哈希映射到位数组上的3个点。如果3个点的其中有一个点不为1，则可以判断该元素一定不存在集合中。反之，如果3个点都为1，则该元素可能存在集合中。注意：此处不能判断该元素是否一定存在集合中，可能存在一定的误判率。可以从图中可以看到：假设某个元素通过映射对应下标为4，5，6这3个点。虽然这3个点都为1，但是很明显这3个点是不同元素经过哈希得到的位置，因此这种情况说明元素虽然不在集合中，也可能对应的都是1，这是误判率存在的原因。

**解决缓存击穿、垃圾邮件识别、集合判重**

### 布隆过滤器具体实现

```java
package com.github.lovasoa.bloomfilter;
import java.util.BitSet;
import java.util.Random;
import java.util.Iterator;
public class BloomFilter implements Cloneable {
private BitSet hashes;
private RandomInRange prng;
private int k; // Number of hash functions
private static final double LN2 = 0.6931471805599453; // ln(2)
/**
   * Create a new bloom filter.
   * @param n Expected number of elements
   * @param m Desired size of the container in bits
   **/
public BloomFilter(int n, int m) {
    k = (int) Math.round(LN2 * m / n);
if (k <= 0) k = 1;
this.hashes = new BitSet(m);
this.prng = new RandomInRange(m, k);
  }
/**
   * Create a bloom filter of 1Mib.
   * @param n Expected number of elements
   **/
public BloomFilter(int n) {
this(n, 1024*1024*8);
  }
/**
  * Add an element to the container
  **/
public void add(Object o) {
    prng.init(o);
for (RandomInRange r : prng) hashes.set(r.value);
  }
/** 
  * If the element is in the container, returns true.
  * If the element is not in the container, returns true with a probability ≈ e^(-ln(2)² * m/n), otherwise false.
  * So, when m is large enough, the return value can be interpreted as:
  *    - true  : the element is probably in the container
  *    - false : the element is definitely not in the container
  **/
public boolean contains(Object o) {
    prng.init(o);
for (RandomInRange r : prng)
if (!hashes.get(r.value))
return false;
return true;
  }
/**
   * Removes all of the elements from this filter.
   **/
public void clear() {
    hashes.clear();
  }
/**
   * Create a copy of the current filter
   **/
public BloomFilter clone() throws CloneNotSupportedException {
return (BloomFilter) super.clone();
  }
/**
   * Generate a unique hash representing the filter
   **/
public int hashCode() {
return hashes.hashCode() ^ k;
  }
/**
   * Test if the filters have equal bitsets.
   * WARNING: two filters may contain the same elements, but not be equal
   * (if the filters have different size for example).
   */
public boolean equals(BloomFilter other) {
return this.hashes.equals(other.hashes) && this.k == other.k;
  }
/**
   * Merge another bloom filter into the current one.
   * After this operation, the current bloom filter contains all elements in
   * other.
   **/
public void merge(BloomFilter other) {
if (other.k != this.k || other.hashes.size() != this.hashes.size()) {
throw new IllegalArgumentException("Incompatible bloom filters");
    }
this.hashes.or(other.hashes);
  }
private class RandomInRange
implements Iterable<RandomInRange>, Iterator<RandomInRange> {
private Random prng;
private int max; // Maximum value returned + 1
private int count; // Number of random elements to generate
private int i = 0; // Number of elements generated
public int value; // The current value
RandomInRange(int maximum, int k) {
      max = maximum;
      count = k;
      prng = new Random();
    }
public void init(Object o) {
      prng.setSeed(o.hashCode());
    }
public Iterator<RandomInRange> iterator() {
      i = 0;
return this;
    }
public RandomInRange next() {
      i++;
      value = prng.nextInt() % max;
if (value<0) value = -value;
return this;
    }
public boolean hasNext() {
return i < count;
    }
public void remove() {
throw new UnsupportedOperationException();
    }
  }
}
```

https://github.com/Baqend/Orestes-Bloomfilter

https://github.com/lovasoa/bloomfilter/blob/master/src/main/java/BloomFilter.java

### Cache 缓存

1. 记忆
2. 钱包 - 储物柜
3. 代码模块

### LRU Cache

两个要素： 大小、替换策略

Hash Table + Double LinkedList

O(1) 查询 、 O(1) 修改更新

### 替换策略

**LFU - least frequently used** 

**LRU - least recently used**

替换算法总览:

https://en.wikipedia.org/wiki/Cache_replacement_policies

### 实现

```java
class LRUCache extends LinkedHashMap<Integer, Integer>{
    private int capacity;
    
    public LRUCache(int capacity) {
        super(capacity, 0.75F, true);
        this.capacity = capacity;
    }

    public int get(int key) {
        return super.getOrDefault(key, -1);
    }

    public void put(int key, int value) {
        super.put(key, value);
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
        return size() > capacity; 
    }
}
```


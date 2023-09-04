HashMap<K,V> 数组+链表 无序,key,value可为null

***看这一篇文章就够了:https://www.jianshu.com/p/dde9b12343c1***

***拓展:linkedHashMap:https://www.jianshu.com/p/8f4f58b4b8ab***

```java
HashMap类
Entry<K, V>[] table;

Entry类
int hash,
K key,
V value,
Entry<K, V> next
```

put<key, value> 方法
```java
public V put(K key, V value) {
        // 对key为null的处理
        if (key == null)
            return putForNullKey(value);
        // 根据key算出hash值
        int hash = hash(key);
        // 根据hash值和HashMap容量算出在table中应该存储的下标i
        int i = indexFor(hash, table.length);
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            // 先判断hash值是否一样，如果一样，再判断key是否一样
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }

        modCount++;
        addEntry(hash, key, value, i);
        return null;
    }


static int indexFor(int h, int length) {
        return h & (length-1);
    }

void addEntry(int hash, K key, V value, int bucketIndex) {
        // 当前HashMap存储元素的个数大于HashMap扩容的阀值，则进行扩容
        if ((size >= threshold) && (null != table[bucketIndex])) {
            resize(2 * table.length);
            hash = (null != key) ? hash(key) : 0;
            bucketIndex = indexFor(hash, table.length);
        }
        // 使用key、value创建Entry并加入到table中
        createEntry(hash, key, value, bucketIndex);
    }


void createEntry(int hash, K key, V value, int bucketIndex) {
        // 取出table中下标为bucketIndex的Entry
        Entry<K,V> e = table[bucketIndex];
        // 利用key、value来构建新的Entry
        // 并且之前存放在table[bucketIndex]处的Entry作为新Entry的next
        // 把新创建的Entry放到table[bucketIndex]位置
        table[bucketIndex] = new Entry<>(hash, key, value, e);
        // HashMap当前存储的元素个数size自增
        size++;
    }
```

总结:put过程,先通过key计算hash,然后根据hash和table.length计算在table中的下标,然后便利判断是存在相同key的值,如果存在则更新value,不如做则插入

### foreach + remove = ConcurrentModificationException

---

### 问题：

```java
List<String> list = new ArrayList<>();
list.add("0");
list.add("1");
list.add("2");
list.add("3");
for (String s : list) {
    if (条件语句) {
        list.remove(s);
    }
}
```
在上述代码的 if 语句中依次使用下列条件语句并执行代码：

|   条件语句    |                      执行结果                       |
| :-----------: | :-------------------------------------------------: |
| "0".equals(s) | 抛出 java.util.ConcurrentModificationException 异常 |
| "1".equals(s) | 抛出 java.util.ConcurrentModificationException 异常 |
| "2".equals(s) |                      正常执行                       |
| "3".equals(s) | 抛出 java.util.ConcurrentModificationException 异常 |

### 分析：

对代码进行反编译，可以发现 foreach 只是一个**语法糖**，最终需要转换为 iterator 进行实现：

```java
ArrayList<String> list = new ArrayList<String>();
list.add("0");
list.add("1");
list.add("2");
list.add("3");
Iterator iterator = list.iterator();
while (iterator.hasNext()) {
    String s = (String)iterator.next();
    if (!"2".equals(s)) continue;
    list.remove(s);
}
```

ArrayList 存在一个名为 Itr 的实现了 Iterator 接口的内部类，通过 iterator() 方法可以获取该内部类的实例：

```java
public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, java.io.Serializable {

    transient Object[] elementData;
    
    private int size;
    
    protected transient int modCount = 0;
    
    public Iterator<E> iterator() { return new Itr(); }
    
    private class Itr implements Iterator<E> {

        int cursor = 0;

        int lastRet = -1;

        int expectedModCount = modCount;

        public boolean hasNext() {...}

        public E next() {...}

        public void remove() {...}

        final void checkForComodification() {...}
    }
}
```
- **elementData**

    集合中存储的所有元素，elementDate 的数组长度代表了 集合的<u>容量</u>。

- **size**

    集合<u>包含元素的个数</u>。

- **modCount** 

    集合发生“结构性修改”的次数。对集合进行结构性修改的常见方式为调用add()、remove()等方法改变集合包含的元素数量。

    ```java
    ArrayList<String> list = new ArrayList<String>(); // modCount == 0
    list.add("0"); // modCount == 1
    list.add("1"); // modCount == 2
    list.add("2"); // modCount == 3
    list.add("3"); // modCount == 4
    Iterator iterator = list.iterator();
    while (iterator.hasNext()) {
        String s = (String)iterator.next();
        if (!"2".equals(s)) continue;
        list.remove(s); // modCount == 5
    }
    ```

    就本文分析的代码而言，创建 ArrayList 实例时，modCount 的初值为0，即尚未对集合进行任何修改，在调用了4次 add() 方法后，modCount的值就累加到了4。

- **cursor**

    当前遍历到的集合元素的下标。

- **lastRet**

    上一次遍历到的集合元素的下标。

- **expectedModCount**

    在只有当前迭代器对集合进行“结构性修改”的情况下，集合应该具有的结构性修改次数。

- **checkForComodification()**

    检查集合是否发生“并发修改”。所有并发修改，指的是除了当前迭代器，外界也对集合进行了修改。由于 ArrayList 没有对并发访问进行控制，因此并发修改将使得当前迭代器后续的行为变得不可控。

    就 Itr 而言，它通过比较 modCount 和 expectedModCount 来判断是否还有元素尚未遍历。

    ```java
    final void checkForComodification() {
        if (modCount != expectedModCount)
            throw new ConcurrentModificationException();
    }
    ```

- **hasNext()**

    判断是否还有元素尚未遍历。就 Itr 而言，它通过比较 cusor 和 size 来判断是否还有元素尚未遍历。

    ```java
    public boolean hasNext() {
        return cursor != size;
    }
    ```

- **next()**

    向后遍历一个集合元素。

    ```java
    public E next() {
        checkForComodification();
        
        int i = cursor;
    	if (i >= size)
    		throw new NoSuchElementException();
        
    	Object[] elementData = ArrayList.this.elementData;
    	if (i >= elementData.length)
    		throw new ConcurrentModificationException();
        
    	cursor = i + 1;
    	return (E) elementData[lastRet = i];
    }
    ```

- **remove()**

    从集合中移除上一次调用 next() 方法遍历到的元素。

    ```java
    public void remove() {
        // 每调用一次 next() 方法 最多只能调用一次 remove() 方法。
        if (lastRet < 0)
            throw new IllegalStateException();    
        
        checkForComodification();
    
        try {
            ArrayList.this.remove(lastRet);  // 从集合中移除元素，remove方法会更新modCount的值。
            cursor = lastRet;
            lastRet = -1;                    // 每调用一次 next() 方法 最多只能调用一次 remove() 方法。
            expectedModCount = modCount;     // 记录新的modCount。
        } catch (IndexOutOfBoundsException ex) {
            throw new ConcurrentModificationException();
        }
    }
    ```

|   条件语句    | 代码序列                                                     |                 执行结果                  |
| :-----------: | :----------------------------------------------------------- | :---------------------------------------: |
| "0".equals(s) | hashNext(): true<br />    cusor = 0,size = 4<br /><br />next(): “0”<br />    checkForComodification(): OK<br />        modCount = 4,expectedModCount = 4<br />    cusor = 1<br /><br />”0”.equals(“0”): true<br />    remove(): OK<br />        modCount = 5,size = 3<br /><br />hashNext(): true<br />    cusor = 1,size = 3<br /><br />**next():  Exception<br />    checkForComodification(): Exception<br />        modCount = 5,expectedModCount = 4<br />** | java.util.ConcurrentModificationException |
| "1".equals(s) | 略                                                           | java.util.ConcurrentModificationException |
| "2".equals(s) | hashNext(): true<br />    cusor = 2,size = 4<br /><br />next(): “2”<br />    checkForComodification(): OK<br />        modCount = 4,expectedModCount = 4<br />    cusor = 3<br /><br />”2”.equals(“2”): true<br />    remove(): OK<br />        modCount = 5,size = 3<br /><br />**hashNext(): false<br />    cusor = 3,size = 3**<br /> |                 正常执行                  |
| "3".equals(s) | 略                                                           | java.util.ConcurrentModificationException |

上面4个条件语句都在迭代器外部对集合进行了结构性修改，不同之处在于，"2".equals(s) 条件语句对集合进行结构行修改以后，就停止了在迭代器中修改集合，这就避免了冲突的发生，所以没有抛出异常。

### 附件：

完整源码：

```java
public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
    /**
     * The array buffer into which the elements of the ArrayList are stored.
     * The capacity of the ArrayList is the length of this array buffer. Any
     * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
     * will be expanded to DEFAULT_CAPACITY when the first element is added.
     */
    transient Object[] elementData; // non-private to simplify nested class access
    
    /**
     * The size of the ArrayList (the number of elements it contains).
     *
     * @serial
     */
     private int size;
    
    /**
     * The number of times this list has been <i>structurally modified</i>.
     * Structural modifications are those that change the size of the
     * list, or otherwise perturb it in such a fashion that iterations in
     * progress may yield incorrect results.
     *
     * <p>This field is used by the iterator and list iterator implementation
     * returned by the {@code iterator} and {@code listIterator} methods.
     * If the value of this field changes unexpectedly, the iterator (or list
     * iterator) will throw a {@code ConcurrentModificationException} in
     * response to the {@code next}, {@code remove}, {@code previous},
     * {@code set} or {@code add} operations.  This provides
     * <i>fail-fast</i> behavior, rather than non-deterministic behavior in
     * the face of concurrent modification during iteration.
     *
     * <p><b>Use of this field by subclasses is optional.</b> If a subclass
     * wishes to provide fail-fast iterators (and list iterators), then it
     * merely has to increment this field in its {@code add(int, E)} and
     * {@code remove(int)} methods (and any other methods that it overrides
     * that result in structural modifications to the list).  A single call to
     * {@code add(int, E)} or {@code remove(int)} must add no more than
     * one to this field, or the iterators (and list iterators) will throw
     * bogus {@code ConcurrentModificationExceptions}.  If an implementation
     * does not wish to provide fail-fast iterators, this field may be
     * ignored.
     */
    protected transient int modCount = 0;
    
    public Iterator<E> iterator() {
        return new Itr();
    }
    
    /**
     * An optimized version of AbstractList.Itr
     */
    private class Itr implements Iterator<E> {
        int cursor;       // index of next element to return
        int lastRet = -1; // index of last element returned; -1 if no such
        int expectedModCount = modCount;

        Itr() {}

        public boolean hasNext() {
            return cursor != size;
        }

        @SuppressWarnings("unchecked")
        public E next() {
            checkForComodification();
            int i = cursor;
            if (i >= size)
                throw new NoSuchElementException();
            Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length)
                throw new ConcurrentModificationException();
            cursor = i + 1;
            return (E) elementData[lastRet = i];
        }

        public void remove() {
            if (lastRet < 0)
                throw new IllegalStateException();
            checkForComodification();

            try {
                ArrayList.this.remove(lastRet);
                cursor = lastRet;
                lastRet = -1;
                expectedModCount = modCount;
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }

        @Override
        @SuppressWarnings("unchecked")
        public void forEachRemaining(Consumer<? super E> consumer) {
            Objects.requireNonNull(consumer);
            final int size = ArrayList.this.size;
            int i = cursor;
            if (i >= size) {
                return;
            }
            final Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length) {
                throw new ConcurrentModificationException();
            }
            while (i != size && modCount == expectedModCount) {
                consumer.accept((E) elementData[i++]);
            }
            // update once at end of iteration to reduce heap write traffic
            cursor = i;
            lastRet = i - 1;
            checkForComodification();
        }

        final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }
    }
    
}
```
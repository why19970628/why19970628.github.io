---
title: JDK8-Map接口源码研读
date: 2019-05-15 10:10:53
tags: [Java]
categories: Java
thumbnail: https://tva1.sinaimg.cn/large/007rAy9hgy1g333f2rj17j31hc0u0b2a.jpg
---
## Map接口源码
<!-- more -->
## 接口说明

* Map是一个可以将key映射为value的对象。map不可以包含重复的key，每一个key最多可映射一个value
* Map接口代替了`Dictionary`类，`Dictionary`是抽象类而不是接口
* Map接口提供了三种集合视图
  * keys的set集合
  * values的集合
  * key-value的set集合

* Map返回元素的顺序取决于某个集合视图迭代器返回元素的顺序，一些map的实现，比如`TreeMap`，对返回元素的顺序有特殊的规定，其他的像`HashMap`则没有
* 当一个易变的对象作为map的key时，你需要特别关注一下。如果map中一个key发生了改变，并且影响了`equals()`方法的使用，那么map并不会提示我们，因此，我们禁止使用map自身作为map的key，尽管我们允许使用map自身作为value，但是我们还是要注意，在这样一个map中你的`equals`和`hashCode`方法可能不起作用
* 所有通用map实现类都应该提供两个标准的构造器
   * 一个无参且返回值为void的构造器
   * 仅含有一个参数且类型为map的构造器，该方法会创建一个新的map（拥有和参数map相同的key-value映射）,也就是说，我们可以根据给定的map复制出一个一模一样的map

* 如果某种可以修改map的操作不被map所支持，将会抛出`UnsupportedOperationException`异常。对于上述情况，map可能但不一定会抛出`UnsupportedOperationException`异常，并且也不会对map造成什么影响
* 一些map的实现类会对它们包含的key和value有一定的限制，例如，一些map实现不允许key或者value为null，一些map则限制了key的类型。尝试插入一个不合法的key或者value将会抛出非检查型异常，代表性的有`NullPointerException`，`ClassCastException`。尝试查询不符合规定的key或者value也会抛出异常,或者仅返回false;具体是上述哪种情况,和具体的map实现类自身相关。更一般地，对于非法的key或者value进行操作，插入失败可能会抛出异常，但也可能插入成功，这取决于具体的map实现类。这类异常在Map接口中被标记为可选择的
* 集合框架接口定义了许多跟`equals`相关的方法，比如`containsKey()`方法：当且仅当map包含了键k的定义是：*key==null ? k==null : key.equals(k)*，这一规范的写法，不能被理解为：如果调用方法使用的是一个非null参数，然后就只是调用*key.equals(k)*方法就可以了。具体实现可以通过避免调用`equals()`方法来实现优化，比如，可以先比较两个key的哈希值
* Map接口是集合框架中的一员

## 方法研读

### 查询操作

```java
/**
* 返回map中key-value映射的个数，如果这个数超过了Integer.MAX_VALUE则返回Integer.MAX_VALUE
*/
int size();

/**
* 如果map不包含任何key-value映射则返回true，否则返回false
*/
boolean isEmpty();

/**
* 如果map包含指定的key则返回true，通常情况是，当且仅当map包含了一个key的映射：
* 映射情况是：key==null ? k==null : key.equals(k),此时返回true
*/
boolean containsKey(Object key);

/**
* 如果map有一个或更多的key可以映射到指定的value，则返回true
* 在所有map接口的实现类中,这一操作都需要map大小的线性时间来完成
*/
boolean containsValue(Object value);

/**
* 返回指定key映射的value，如果没有则返回null
* 如果map允许null值,返回null值并不一定意味着map不存在指定key的映射;因为这也可能是这个key对应的value值* 就是null
*/
V get(Object key);
```

### 修改操作

```java
/**
* put方法是将指定的key-value存储到map里面的操作.如果map之前包含了一个此key对应的映射,那么此key对应的旧* value值会被新的value值替换
*/
V put(K key, V value);

/**
* 移除map中已有的某个key，更一般的讲,如果map包含了一个满足条件key==null ?  k==null : key.equals(k)
* 的映射,这一映射就会被移除.(map最多包含一个这样的映射)
* 本方法会返回移除的key对应的value值,如果map这个key没有对应的value值,则返回null
* 如果map允许null值,返回null值并不一定意味着map不存在指定key的映射;因为这也可能是这个key对应的value值* 就是null
*/
V remove(Object key);
```

### 块操作

```java
/**
* 将指定map中的所有key-value映射拷贝到当前map，这一操作类似于将指定map的key-value对通过put方法一个个 * 拷贝过来
* 在拷贝过程中,如果指定的这个map被更改了,那么这时候会出现什么情况,并不清楚
*/
void putAll(Map<? extends K, ? extends V> m);

/** 
* 移除所有的key-value映射，调用该方法后，map将为空
*/
void clear();
```

### 视图

```java
/**
* 此方法返回map里存储的所有映射的视图.
* 当然,这个返回的set集合,也由map作为后台支持(这一点和前两种方法一样),因此对map的改变会体现在set上面,反  * 之亦然.
* 基于set集合的迭代器在遍历过程中,如果map结构发生了改变(除去迭代器自身的remove方法造成的map结构改变,或  * 者迭代器对map条目调用了setValue方法),则迭代器对输出结果的影响是怎么样的,并没有给出定义.
* 返回的set集合支持移除元素,这种移除操作会同时反映在map上面.
* 移除操作类型包括5种:
* <tt>Iterator.remove</tt>,
* <tt>Set.remove</tt>,
* <tt>removeAll</tt>,
* <tt>retainAll</tt>,
* <tt>clear</tt>
*/
Set<K> keySet();

/**
* 返回所有value的集合，
* 这一集合也由map集合提供后台支持.因此map的更改会体现在返回的集合里面,反之亦然.(这一点和keySet方法完全一  * 样)
* 这一返回集合支持移除元素,这会同时移除map中对应的映射.移除操作类型包括5类
* <tt>Iterator.remove</tt>,
* <tt>Collection.remove</tt>,
* <tt>removeAll</tt>,
* <tt>retainAll</tt>,
* <tt>clear</tt>
* 注意:返回集合不支持add或者addAll操作
*/
Collection<V> values();

/**
* 返回该map所有映射的视图
* 当然,这个返回的set集合,也由map作为后台支持(这一点和前两种方法一样),因此对map的改变会体现在set上面
* 反之亦然
* 返回的set集合支持移除元素,这种移除操作会同时反映在map上面
* 移除操作类型包括5种:
* <tt>Iterator.remove</tt>,
* <tt>Set.remove</tt>,
* <tt>removeAll</tt>,
* <tt>retainAll</tt>,
* <tt>clear</tt>
*/
Set<Map.Entry<K, V>> entrySet();
```

### Map条目

```java
/**
* map条目（key-value对）
* Map.entrySet方法返回的就是map的集合视图,map视图中的元素就是来源于此类
* 获取map条目的唯一方式就是来源于集合视图的迭代器.只有在迭代的过程中,Map.Entry对象才是有效的;
* 通常,如果通过迭代器获得的map条目,在遍历过程中,作为后台支持的map被修改了,那么map条目会如何被影响,对此
* 并没有做出具体规定（除了setValue方法）
*/
interface Entry<K,V> {
    
    /**
    * 返回当前map条目的key
    */
    K getKey();
    
    /**
    * 返回当前map条目的value
    * 如果映射从后台map中移除了(通过迭代器的remove方法),这一调用的结果会出现什么影响未被定义
    */
    V getValue();
    
    /**
    * 用指定值替换当前条目中的value.这一更改会被写入map中
    * 如果映射从map中被移除了,再调用这一方法,会产生什么异常并没有定义
    */
    V setValue(V value);
    
    /**
    * 将指定对象与当前条目做比较
    * 如果给定的对象是一个条目并且两个条目代表同一个映射,则返回true
    * 一般,两个条目拥有相同映射满足的条件是：
    * if<pre>
    *     (e1.getKey()==null ?
    *      e2.getKey()==null : e1.getKey().equals(e2.getKey()))  &amp;&amp;
    *     (e1.getValue()==null ?
    *      e2.getValue()==null : e1.getValue().equals(e2.getValue()))
    * </pre>
    * 上面的两个条件保证了,对Map.Entry接口的不同实现,equals方法都能正确.
    */
    boolean equals(Object o);
    
    /**
    * 返回map条目的哈希值.
    * map条目的哈希值定义是:二者求异或值
    * (e.getKey()==null   ? 0 : e.getKey().hashCode()) ^
    *     (e.getValue()==null ? 0 : e.getValue().hashCode())
    * 这才能保证两个条目相等时,通过Object.hashCode方法得到的他们的哈希值也一定是相等的.
    */
    int hashCode();
    
    //---------------- since 1.8------------------------
    
    /**
    * 返回一个比较map.entry的比较器,按照key的自然顺序排序
    * 返回的比较器支持序列化
    * 如果map中的entry有key=null情况,则抛出空指针异常(因为返回结果要按照key排序)
    * 注意:传入参数k必须支持Comparable接口,因为需要按照key排序
    * @since 1.8
    */
    public static <K, V> Comparator<Map.Entry<K, V>> comparingByKey(Comparator<? super K> cmp) {
            Objects.requireNonNull(cmp);
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> cmp.compare(c1.getKey(), c2.getKey());
      }
    
    /**
    * 返回一个map.enty的比较器,按照value的自然顺序排序
    * 返回的比较器支持序列化
    * 如果map中的entry有value=null情况,则抛出空指针异常(因为返回结果要按照value排序)
    * 注意:传入参数value必须支持Comparable接口,因为按照value排序
    * @since 1.8
    */
 	public static <K, V extends Comparable<? super V>> Comparator<Map.Entry<K,V>> comparingByValue() {
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> c1.getValue().compareTo(c2.getValue());
        }   
    
    /**
    * 返回一个map.entry的比较器,根据传入比较器对key排序
    * 如果传入的比较器支持序列化,则返回的结果比较器也支持序列化
    * @since 1.8
    */
    public static <K, V> Comparator<Map.Entry<K, V>> comparingByKey(Comparator<? super K> cmp) {
            Objects.requireNonNull(cmp);
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> cmp.compare(c1.getKey(), c2.getKey());
      }
    
    /**
    * 返回一个map.entry的比较器,根据传入比较器对value排序
    * 如果传入的比较器支持序列化,则返回的结果比较器也支持序列化
    * @since 1.8
    */
    public static <K, V> Comparator<Map.Entry<K, V>> comparingByValue(Comparator<? super V> cmp) {
            Objects.requireNonNull(cmp);
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> cmp.compare(c1.getValue(), c2.getValue());
        }
    
}
```

### equals()和hashCode()

```java
/**
* 将传入对象和当前map进行比较.
* 返回结果为true需要满足两个条件:
* 1.传入对象类型为map类型
* 2.传入对象和当前map键值对完全一致.
* 更标准的讲,两个map的entrySet在equals结果为true,则表示键值对一致.这才能保证对于实现map接口的不同类在
* equals方法调用过程中正常使用.
*/
boolean equals(Object o);

/**
* 返回map的哈希值.
* map的哈希值=sum(每一个entry的哈希值).
* map的哈希值求法保证了:如果map1.equals(map2)结果为true,则map1.hashCode()=map2.hashCode(),
* 当然这样的设计也符合Object中对hashCode的定义.
* @see Map.Entry#hashCode()
* @see Object#equals(Object)
* @see #equals(Object)
*/
int hashCode();
```

### 默认方法

```java
/**
* 返回指定key对应的value,如果没有对应的映射,则返回传入参数中的默认值defaultValue
* 此默认方法对于线程同步和执行过程的原子性并不能保证.任何实施提供原子性保证必须重写此方法并记录其方法.
* @since 1.8
*/
default V getOrDefault(Object key, V defaultValue) {
        V v;
        return (((v = get(key)) != null) || containsKey(key))
            ? v
            : defaultValue;
}

/**
* 对map中每一个entry执行action中定义对操作,直到全部entry执行完成or执行中出现异常为止
* 除非map的实现类有规定,否则forEach的执行顺序为entrySet中entry的顺序
* 执行中的异常最终抛给方法的调用者
* 本方法的默认实现和下面的代码是等价的:
* for (Map.Entry<K, V> entry : map.entrySet())
*     action.accept(entry.getKey(), entry.getValue());
* }
* 此默认方法对于线程同步和执行过程的原子性并不能保证.
* 任何实施提供原子性保证必须重写此方法并记录其方法.
* @since 1.8
*/
default void forEach(BiConsumer<? super K, ? super V> action) {
        Objects.requireNonNull(action);
        for (Map.Entry<K, V> entry : entrySet()) {
            K k;
            V v;
            try {
                k = entry.getKey();
                v = entry.getValue();
            } catch(IllegalStateException ise) {
                // this usually means the entry is no longer in the map.
                throw new ConcurrentModificationException(ise);
            }
            action.accept(k, v);
        }
}


/**
 * 对于map中每一个entry,将其value替换成BiFunction接口返回的值.直到所有entry替换完or出现异常为止.
 * 如果执行过程中出现异常,则抛给调用者.
 * @implSpec
 * 本方法的默认执行和下面代码等价:
 * for (Map.Entry<K, V> entry : map.entrySet())
 *     entry.setValue(function.apply(entry.getKey(), entry.getValue()));
 * }
 *
 * 此默认方法对于线程同步和执行过程的原子性并不能保证.
 * 任何实施提供原子性保证必须重写此方法并记录其方法.
 *
 * @since 1.8
 */    
default void replaceAll(BiFunction<? super K, ? super V, ? extends V> function) {
        Objects.requireNonNull(function);
        for (Map.Entry<K, V> entry : entrySet()) {
            K k;
            V v;
            try {
                k = entry.getKey();
                v = entry.getValue();
            } catch(IllegalStateException ise) {
                // this usually means the entry is no longer in the map.
                throw new ConcurrentModificationException(ise);
            }

            // ise thrown from function is not a cme.
            v = function.apply(k, v);

            try {
                entry.setValue(v);
            } catch(IllegalStateException ise) {
                // this usually means the entry is no longer in the map.
                throw new ConcurrentModificationException(ise);
            }
        }
}

/**
* 如果指定的键尚未与值相关联（或被映射为null）,则将它与给定的值相关联并返回null，否则返回当前值
* 此默认方法对于线程同步和执行过程的原子性并不能保证.
* 任何实施提供原子性保证必须重写此方法并记录其方法
* @since 1.8
*/
default V putIfAbsent(K key, V value) {
        V v = get(key);
        if (v == null) {
            v = put(key, value);
        }

        return v;
}

/**
* 如果给定的参数key和value在map中是一个entry,则删除这个entry
* @since 1.8
*/
default boolean remove(Object key, Object value) {
        Object curValue = get(key);
        if (!Objects.equals(curValue, value) ||
            (curValue == null && !containsKey(key))) {
            return false;
        }
        remove(key);
        return true;
}

/**
* 如果给定的key和value在map中有entry,则为指定key的entry,用新value替换旧的value
* 对于不支持value为null的map来说,如果旧value为null,本方法默认不抛出异常,除非新value也是null
* @since 1.8
*/
default boolean replace(K key, V oldValue, V newValue) {
        Object curValue = get(key);
        if (!Objects.equals(curValue, oldValue) ||
            (curValue == null && !containsKey(key))) {
            return false;
        }
        put(key, newValue);
        return true;
}


/**
* 如果指定key在map中有value,则用参数value进行替换.
* @since 1.8
*/
default V replace(K key, V value) {
        V curValue;
        if (((curValue = get(key)) != null) || containsKey(key)) {
            curValue = put(key, value);
        }
        return curValue;
}

/**
* 如果指定key在map中没有对应的value,则使用输入参数,即函数接口mappingfunction为其计算一个value.
* 如果计算value不为null,则将value插入map中
* 如果计算function返回结果为null,则不插入任何映射.如果函数function本身抛出(非检查型)异常,则异常会被重 * 新抛出,自然也不会插入任何映射
* 最常见的用法是构造一个新的对象作为初始映射值或memoized结果，如下所示：
* map.computeIfAbsent(key, k -> new Value(f(k)));
*
* or是为了实现一个多value的map(如Map<k,Collection<v>>),从而支持一个key对应多个value.
*
* map.computeIfAbsent(key, k -> new HashSet<V>()).add(v);
* }</pre>
*
* 本方法的默认实现和下面代码等价:
* if (map.get(key) == null) {
*     V newValue = mappingFunction.apply(key);
*     if (newValue != null)
*         map.put(key, newValue);
* }
* }
*
* 此默认方法对于线程同步和执行过程的原子性并不能保证.
* 任何实施提供原子性保证必须重写此方法并记录其方法.
* 特别是,所有ConcurrentMap的子接类,必须给出说明:如果当前value不存在,赋值的function操作是否为原子性的.
* @since 1.8
*/
default V computeIfAbsent(K key,
            Function<? super K, ? extends V> mappingFunction) {
        Objects.requireNonNull(mappingFunction);
        V v;
        if ((v = get(key)) == null) {
            V newValue;
            if ((newValue = mappingFunction.apply(key)) != null) {
                put(key, newValue);
                return newValue;
            }
        }

        return v;
}


/**
* 如果map中存在指定key对应的value,且不为null,则本方法会尝试使用function,并利用key生成一个新的value.
* 如果function接口返回null,map中原entry则被移除.如果function本身抛出异常,则当前map不会发生改变.
* 此默认方法对于线程同步和执行过程的原子性并不能保证.
* 任何实施提供原子性保证必须重写此方法并记录其方法.
* 特别是,所有ConcurrentMap的子接类,必须给出说明:如果当前value不存在,赋值的function操作是否为原子性的.
* @since 1.8
*/
default V computeIfPresent(K key,
            BiFunction<? super K, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(remappingFunction);
        V oldValue;
        if ((oldValue = get(key)) != null) {
            V newValue = remappingFunction.apply(key, oldValue);
            if (newValue != null) {
                put(key, newValue);
                return newValue;
            } else {
                remove(key);
                return null;
            }
        } else {
            return null;
        }
}

/**
* 利用指定key和value计算一个新映射.
* 比如:向一个value映射中新增或者拼接一个String.
* 下面的merge()方法在这样的用途上面通常使用更方便一些.
* 如果function接口返回null,则map中原entry被移除(如果本来就不存在,则不执行移除操作).
* 如果function本身抛出(非检查型)异常,异常会被重新抛出,当前map也不会发生改变.
* 此默认方法对于线程同步和执行过程的原子性并不能保证.
* 任何实施提供原子性保证必须重写此方法并记录其方法.
* 特别是,所有ConcurrentMap的子接类,必须给出说明:如果当前value不存在,赋值的function操作是否为原子性的.
* @since 1.8
*/
default V compute(K key,
            BiFunction<? super K, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(remappingFunction);
        V oldValue = get(key);

        V newValue = remappingFunction.apply(key, oldValue);
        if (newValue == null) {
            // delete mapping
            if (oldValue != null || containsKey(key)) {
                // something to remove
                remove(key);
                return null;
            } else {
                // nothing to do. Leave things as they were.
                return null;
            }
        } else {
            // add or replace old mapping
            put(key, newValue);
            return newValue;
        }
}

/**
* 如果指定key没有value,或者其value为null,则将其改为给定的非null的value.
* 否则,用给定的function返回值替换原value
* 如果给定参数value和function返回结果都为null,则删除map中这个entry
* 这一方法常用于:对一个key合并多个映射的value时.
* 比如:要创建或追加一个String给一个值映射.
*
* 如果function返回null,则map中原entry被移除.如果function本身抛出异常,则异常会被重新抛出,且
* 当前map不会发生更改.
*
* 此默认方法对于线程同步和执行过程的原子性并不能保证.
* 任何实施提供原子性保证必须重写此方法并记录其方法.
* 特别是,所有ConcurrentMap的子接类,必须给出说明:如果当前value不存在,赋值的function操作是否为原子性的
* @since 1.8
*/
default V merge(K key, V value,
            BiFunction<? super V, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(remappingFunction);
        Objects.requireNonNull(value);
        V oldValue = get(key);
        V newValue = (oldValue == null) ? value :
                   remappingFunction.apply(oldValue, value);
        if(newValue == null) {
            remove(key);
        } else {
            put(key, newValue);
        }
        return newValue;
}
```


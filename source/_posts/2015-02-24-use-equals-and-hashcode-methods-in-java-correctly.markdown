---
layout: post
title: "在Java中正确地使用equals()和hashCode()方法"
date: 2015-02-24 15:20:34 +0800
comments: true
categories:
- Java
---

最近在用Java写一个跟购物有关的小程序。写测试的时候遇到了判断两个对象是否相等的问题。看了些资料，说一下自己对`equals()`和`hashCode()`这两个方法的理解。

<!-- more -->

在`Object`类中定义了`equals()`和`hashCode()`这两个方法。`Object`类是类继承结构的基础，所以是每一个类的父类。所有的对象，包括数组，都实现了在`Object`类中定义的方法。

###**equals**

`equals()`方法是用来判断其他的对象是否和该对象相等，它的性质有：

- 自反性（reflexive）。对于任意不为`null`的引用值x，`x.equals(x)`一定是`true`。

- 对称性（symmetric）。对于任意不为`null`的引用值`x`和`y`，当且仅当`x.equals(y)`是`true`时，`y.equals(x)`也是`true`。

- 传递性（transitive）。对于任意不为`null`的引用值`x`、`y`和`z`，如果`x.equals(y)`是`true`，同时`y.equals(z)`是`true`，那么`x.equals(z)`一定是`true`。

- 一致性（consistent）。对于任意不为`null`的引用值`x`和`y`，如果用于equals比较的对象信息没有被修改的话，多次调用时`x.equals(y)`要么一致地返回`true`要么一致地返回`false`。

- 对于任意不为`null`的引用值`x`，`x.equals(null)`返回`false`。

对于`Object`类来说，`equals()`方法在对象上实现的是差别可能性最大的等价关系，即，对于任意非`null`的引用值`x`和`y`，当且仅当`x`和`y`引用的是同一个对象，该方法才会返回`true`。

需要注意的是当`equals()`方法被override时，`hashCode()`也要被override。按照一般`hashCode()`方法的实现来说，相等的对象，它们的hash code一定相等。

###**hashCode**

`hashCode()`方法给对象返回一个hash code值。这个方法被用于hash tables，例如HashMap。

它的性质是：

- 在一个Java应用的执行期间，如果一个对象提供给equals做比较的信息没有被修改的话，该对象多次调用`hashCode()`方法，该方法必须始终如一返回同一个integer。

- 如果两个对象根据`equals(Object)`方法是相等的，那么调用二者各自的`hashCode()`方法必须产生同一个integer结果。

- 并不要求根据`equals(java.lang.Object)`方法不相等的两个对象，调用二者各自的`hashCode()`方法必须产生不同的integer结果。然而，程序员应该意识到对于不同的对象产生不同的integer结果，有可能会提高hash table的性能。

大量的实践表明，由`Object`类定义的`hashCode()`方法对于不同的对象返回不同的integer。

来看一个例子。创建一个`Book`类：

```java
public class Book {
    private String name;
    private String author;

    public Book(String name, String author) {
        this.name = name;
        this.author = author;
    }
}
```

`Book`类有两个非常基础的属性：书名`name`和作者`author`。现在，来比较两本书：

```java
public class BookTest {
    @Test
    public void shouldTwoBookWithSameNameAuthor_return_equal() {
        Book oneBook = new Book("A Book", "Jim");
        Book anotherBook = new Book("A Book", "Jim");

        assertEquals(oneBook, anotherBook);
    }
}
```

测试没有通过。虽然两本书有着同样的书名和作者，但是它们是两本“不同”的书。

###**关于改写**

改写`equals()`方法看起来非常简单，但是有许多改写的方式会导致错误，并且后果非常严重。要避免问题最重要的办法是不改写`equals()`方法。

那么什么时候应该改写`Object.equals`呢？当一个类有自己特有的“逻辑相等”概念（不同于对象身份的概念），而且超类也没有改写`equals()`以实现期望的行为，这时需要改写`equals()`方法。这通常适合于[value object](http://martinfowler.com/bliki/ValueObject.html)。

再说一遍，在每个改写了`equals()`方法的类中，必须要改写`hashCode()`方法。如果不这样做，就会违反`Object.hashCode`的通用约定，从而导致该类无法与所有基于hash的集合类结合在一起正常运作，这样的集合类包括HashMap、HashSet和HashTable。

需要注意的是
```java
public int hashCode() {
    return 0;
}
```
这个`hashCode()`方法是合法的，因为相等的对象总是具有同样的散列码。但是它使得每一个对象都具有同样的散列码。因此，每个对象都被映射到同一个散列桶中，从而散列表被退化为链表。对于规模很大的散列表而言，这关系到散列表能否正常工作。

一个好的散列函数通常倾向于“为不相等的对象产生不相等的散列码”。

如果一个类是非可变的，并且计算散列码的代价也比较大，那么你应该考虑把散列码缓存在对象内部，而不是每次请求的时候都重新计算散列码。

不要试图从散列码计算中排除掉一个对象的关键部分以提高性能。

我使用的是IntelliJ，使用`^`＋`N`快捷键可以很好的override`equals()`和`hashCode()`方法：

```java
public class Book {
    private String name;
    private String author;

    public Book(String name, String author) {
        this.name = name;
        this.author = author;

    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        Book book = (Book) o;

        if (author != null ? !author.equals(book.author) : book.author != null) return false;
        if (name != null ? !name.equals(book.name) : book.name != null) return false;

        return true;
    }

    @Override
    public int hashCode() {
        int result = name != null ? name.hashCode() : 0;
        result = 31 * result + (author != null ? author.hashCode() : 0);
        return result;
    }
}
```

这个时候再跑前面的测试就会发现测试通过了。

那么如果我没有override`hashCode()`会发生什么呢？

```java
public class Book {
    private String name;
    private String author;

    public Book(String name, String author) {
        this.name = name;
        this.author = author;

    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        Book book = (Book) o;

        if (author != null ? !author.equals(book.author) : book.author != null) return false;
        if (name != null ? !name.equals(book.name) : book.name != null) return false;

        return true;
    }
}
```

这个时候你会发现之前的测试依然通过，但是如果换一下测试：

```java
public class BookTest {
    @Test
    public void shouldSearchWithSameBook_return_correctBorrowPeople() {
        Book oneBook = new Book("A Book", "Jim");
        Book antherBook = new Book("A Book", "Jim");

        Map<Book, String> borrowMap = new HashMap<Book, String>();
        borrowMap.put(oneBook, "Li Lei");

        assertThat(borrowMap.get(antherBook), is("Li Lei"));
    }
}
```

逻辑上，我用相同的书在借书表里查询，应该返回对应的人名。但是这条测试并没有通过，事实上返回的是`null`。因为没有改写`hashCode()`方法，从而导致两个相等的实例具有不相等的散列码，违反了`hashCode()`的规定。`put()`方法把LiLei借出的书对象存放在一个散列桶中，`get()`方法会在另一个散列桶中查找LiLei所借的书对象，返回`null`也就不出意外了。要想修正，只要提供适当的`hashCode()`方法就可以了。

**参考文献:**

1. [Java中正确使用hashCode和equals方法](http://www.oschina.net/question/82993_75533)
2. [Class Object](http://docs.oracle.com/javase/7/docs/api/java/lang/Object.html)
3. [Effective Java 中文版](http://book.douban.com/subject/1103015/)
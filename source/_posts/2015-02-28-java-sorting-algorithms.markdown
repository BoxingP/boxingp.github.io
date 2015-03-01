---
layout: post
title: "一些排序算法的Java实现"
date: 2015-02-28 23:33:52 +0800
comments: true
categories:
- Java
- Algorithm
---

最近在~~学习~~复习《数据结构与算法分析》，对一些典型的排序算法自己用Java实现，在这个练习的过程中加深对算法的了解。

<!-- more -->

###插入排序（Insertion sort）

插入排序是最简单的排序算法。它每次排好最终结果数组的一个元素。

它的一般原理是第p次时，将位置p上的元素向左移动，直到它在前p+1个元素中的正确位置被找到。

![Insertion sort](/images/2015/insertion-sort.png)

```java
package com.boxing.algorithm;

public class InsertionSort implements SortAlgorithm{

    @Override
    public int[] doSort(int[] array) {
        for (int index = 1; index < array.length; index++) {
            for (int i = index; i > 0; i--) {
                if (isLessThanBefore(array, i)) {
                    swapNumbers(array, i);
                }
            }
        }
        return array;
    }

    private boolean isLessThanBefore(int[] array, int i) {
        return array[i] < array[i - 1];
    }

    private void swapNumbers(int[] array, int i) {
        int temp;
        temp = array[i - 1];
        array[i - 1] = array[i];
        array[i] = temp;
    }
}
```

```java
public class InsertionSortTest {
    @Test
    public void shouldInputAnArray_return_sortedArray() {
        SortAlgorithm sortAlgorithm = new InsertionSort();
        int[] inputArray = {10, 34, 2, 56, 7, 67, 88, 42};
        int[] outputArray = sortAlgorithm.doSort(inputArray);

        assertThat(outputArray, is(new int[]{2, 7, 10, 34, 42, 56, 67, 88}));
    }
}
```

测试通过。

###冒泡排序（Bubble sort）

冒泡排序，也叫sinking sort，是一种简单的排序算法。它重复地遍历要排序的数组，比较每一对相邻的元素，如果它们次序错误，就把它们进行交换。它的名字来自这种相对小的元素“上浮”到数组的顶部的方式。因为它对元素只用到了比较的操作方式，所以是一种比较排序。

{% img /images/2015/bubblesort.jpeg 312 469 %}

```java
package com.boxing.algorithm;

public class BubbleSort implements SortAlgorithm {
    @Override
    public int[] doSort(int[] array) {
        for (int index = array.length; index >= 0; index--) {
            for (int i = 0; i < array.length - 1; i++) {
                if (isLargerThanAfter(array, i)) {
                    swapNumbers(array, i);
                }
            }
        }

        return array;
    }

    private boolean isLargerThanAfter(int[] array, int i) {
        return array[i] > array[i + 1];
    }

    private void swapNumbers(int[] array, int i) {
        int temp = array[i];
        array[i] = array[i + 1];
        array[i + 1] = temp;
    }
}
```

```java
public class BubbleSortTest {
    @Test
    public void shouldInputAnArray_return_sortedArray() {
        SortAlgorithm sortAlgorithm = new BubbleSort();
        int[] inputArray = {10, 34, 2, 56, 7, 67, 88, 42};
        int[] outputArray = sortAlgorithm.doSort(inputArray);

        assertThat(outputArray, is(new int[]{2, 7, 10, 34, 42, 56, 67, 88}));
    }
}
```

测试通过。

###快速排序（Quick sort）

快速排序是实践中一种快速的排序算法。它是一种分治的递归算法。它首先把一个大的数组分成两个小的子数组：较小的数的数组和较大的数的数组。再对这子数组做递归处理。

实现快速排序的步骤：

1. 从数组中选择一个元素，称为pivot。一般来说，pivot是数组中位于中间的元素。

2. 对数组进行重新排序，所有比pivot小的数在pivot前面，所有比pivot大的数在pivot后面（相等的可以在任何一边）。经过这样的分区后，pivot就在它最终的位置上了。这个步骤叫做分区操作。

3. 把上述的步骤对含有较小数的子数组和含有较大数的子数组进行递归操作。

{% img /images/2015/quick-sort1.png 250 304 %}

**参考文献**

1. [Java Sorting Algorithms](http://www.java2novice.com/java-sorting-algorithms/)

2. [数据结构与算法分析：Java语言描述](http://book.douban.com/subject/3351237/)

3. [Sorting Algorithm Animations](http://www.sorting-algorithms.com/)
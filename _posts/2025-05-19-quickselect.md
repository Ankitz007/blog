---
title: The Quickselect Algorithm
date: 2025-05-19 13:30:00 +0530
author: ankitz007
categories: [Programming, Algorithms]
tags: [Programming, Algorithms, Divide & Conquer]
description: Learn how to implement the Quickselect algorithm in Python for efficient selection of the k-th smallest element.
image:
    path: https://res.cloudinary.com/ankitz007/image/upload/v1748766309/programming/algorithms/quickselect_zroxru.webp
    lqip: https://res.cloudinary.com/ankitz007/image/upload/e_blackwhite/v1748766309/programming/algorithms/quickselect_zroxru.webp
    alt: Source - ChatGPT
---

When you need to find the *k-th smallest element* in an unsorted array, sorting the entire array is unnecessary and inefficient. **Quickselect**, a selection algorithm developed by Tony Hoare, is an optimal choice for such tasks.

> **Tony Hoare**, the inventor of Quicksort, also created Quickselect to efficiently find the k-th smallest element without fully sorting the array.
{: .prompt-tip}

Quickselect is based on the **partitioning logic** of Quicksort but only processes one half of the array, making it more efficient for selection problems. In this post, we’ll explore the algorithm and compare two partitioning strategies it can use: **Lomuto** and **Hoare**.

## What is Quickselect?

Quickselect is a divide-and-conquer algorithm that, given an array and a number `k`, returns the element that would be in the k-th position if the array were sorted.

* **Time complexity (average):** O(n)
* **Time complexity (worst-case):** O(n²)
* **Space complexity:** O(1) (in-place)

## Lomuto Partitioning

The Lomuto partition scheme always places the pivot at its final sorted position and partitions the array into two halves: elements less than or equal to the pivot go to the left; elements greater go to the right.

```python
def lomuto_partition(arr, low, high):
    random_pivot_index = random.randint(low, high)
    arr[random_pivot_index], arr[high] = arr[high], arr[random_pivot_index]
    
    pivot = arr[high]
    i = low - 1
    for j in range(low, high):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    arr[i + 1], arr[high] = arr[high], arr[i + 1]
    return i + 1
```

### Quickselect with Lomuto Partitioning

```python
def quickselect_lomuto(arr, k):
    if not (0 <= k < len(arr)):
        raise ValueError("k is out of bounds")

    def lomuto_partition(arr, low, high):
        # (same as above)

    arr_copy = list(arr)
    low, high = 0, len(arr_copy) - 1

    while low <= high:
        if low == high:
            return arr_copy[low]

        pivot_index = lomuto_partition(arr_copy, low, high)

        if k == pivot_index:
            return arr_copy[k]
        elif k < pivot_index:
            high = pivot_index - 1
        else:
            low = pivot_index + 1

    return -1
```

### ✅ Pros of Lomuto Partitioning

* **Simplicity:** The algorithm is intuitive and easy to implement.
* **Final Pivot Placement:** The pivot ends up exactly where it belongs in the sorted array, making logic straightforward.
* **Educational Value:** Often used in textbooks and teaching due to its clarity.

### ❌ Cons of Lomuto Partitioning

* **More Swaps:** Every element less than or equal to the pivot is swapped, which increases operation count.
* **Less Efficient in Practice:** On average, requires more comparisons and writes than Hoare’s scheme.
* **Poor on Duplicates:** Can degrade when many elements equal the pivot.

## Hoare Partitioning

Unlike Lomuto, Hoare’s scheme doesn't guarantee the pivot ends at its final position. Instead, it ensures that all values left of the returned index are less than or equal to the pivot, and those to the right are greater or equal—but the pivot might be anywhere.

```python
def hoare_partition(arr, low, high):
    pivot_index_random = random.randint(low, high)
    arr[low], arr[pivot_index_random] = arr[pivot_index_random], arr[low]
    pivot = arr[low]
    
    i = low - 1
    j = high + 1
    while True:
        i += 1
        while arr[i] < pivot:
            i += 1
        j -= 1
        while arr[j] > pivot:
            j -= 1
        if i >= j:
            return j
        arr[i], arr[j] = arr[j], arr[i]
```

### Quickselect with Hoare Partitioning

```python
def quickselect_hoare(arr, k):
    if not (0 <= k < len(arr)):
        raise ValueError("k is out of bounds")

    def hoare_partition(arr, low, high):
        # (same as above)

    arr_copy = list(arr)
    low, high = 0, len(arr_copy) - 1

    while low <= high:
        if low == high:
            return arr_copy[low]

        p = hoare_partition(arr_copy, low, high)

        if k <= p:
            high = p
        else:
            low = p + 1

    return -1
```

### ✅ Pros of Hoare Partitioning

* **Fewer Swaps:** Elements are only swapped when necessary, resulting in fewer write operations.
* **Better Performance:** On average, performs better than Lomuto in terms of comparisons and memory writes.
* **Handles Duplicates Well:** Naturally groups equal values, making it more efficient in such scenarios.

### ❌ Cons of Hoare Partitioning

* **More Complex:** The logic is trickier, especially when integrating with recursive or iterative selection.
* **Pivot Placement Uncertain:** Pivot is not guaranteed to be in its final sorted place, requiring more careful logic for selection.

## Conclusion

Quickselect is a powerful tool for efficiently finding the k-th smallest element. While both Lomuto and Hoare partition schemes are valid and commonly used, your choice should depend on:

* **Ease of implementation?** → Choose **Lomuto**.
* **Performance on large datasets or duplicates?** → Choose **Hoare**.

Understanding both gives you the flexibility to optimize for clarity or speed depending on the task.

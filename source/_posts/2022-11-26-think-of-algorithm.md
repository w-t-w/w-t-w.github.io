---
title: think of algorithm
date: 2022-11-26 18:57:40
tags: [algorithm, 算法]
categories: algorithm
---

# 算法

#### 排序

> 冒泡排序

```javascript
function bubbleSort(array) {
    const result = [...array],
        length = result.length;
    for (let i = 0; i < length; i++) {
        for (let j = 0; j < length - i; j++) {
            if (result[j] > result[j + 1]) {
                const temp = result[j];
                result[j] = result[j + 1];
                result[j + 1] = temp;
            }
        }
    }
    return result;
}
```

> 插入排序

```javascript
function insertSort(array) {
    const result = [...array],
        length = result.length;
    for (let i = 0; i < length; i++) {
        let j = i;
        while ((result[j - 1] > result[j]) && j > 0) {
            const temp = result[j];
            result[j] = result[j - 1];
            result[j - 1] = temp;
            j--;
        }
    }
    return result;
}
```

> 归并排序

```javascript
function merge(a, b) {
    const a_length = a.length,
        b_length = b.length,
        arr = [];
    let i = 0,
        j = 0;
    while (i < a_length && j < b_length) {
        if (a[i] <= b[j]) {
            arr.push(a[i]);
            i++;
        } else {
            arr.push(b[j]);
            j++
        }
    }
    if (i === a_length && j < b_length) {
        while (j < b_length) {
            arr.push(b[j]);
            j++;
        }
    }
    if (j === b_length && i < a_length) {
        while (i < a_length) {
            arr.push(a[i]);
            i++
        }
    }
    return arr;
}

function mergeSort(arr) {
    const length = arr.length;
    if (length <= 1) {
        return arr;
    }
    const middle = Math.floor(length / 2),
        left = arr.slice(0, middle),
        right = arr.slice(middle);
    return merge(mergeSort(left), mergeSort(right));
}
```

> 快速排序

```javascript
function benchMark(arr, left, right) {
    let i = left,
        j = right,
        x = arr[left];
    while (i < j) {
        while (i < j && arr[j] >= x) {
            j--;
        }
        if (i < j) {
            arr[i] = arr[j];
        }
        while (i < j && arr[i] <= x) {
            i++
        }
        if (i < j) {
            arr[j] = arr[i];
        }
    }
    arr[i] = x;
    return i;
}

function quickSort(arr, left = 0, right = arr.length - 1) {
    if (left < right) {
        const index = benchMark(arr, left, right);
        quickSort(arr, left, index - 1);
        quickSort(arr, index + 1, right);
    }
    return arr;
}
```


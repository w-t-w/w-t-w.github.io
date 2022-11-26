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

> 快速排序



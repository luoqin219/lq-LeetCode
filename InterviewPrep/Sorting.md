# Summary of All Sorting Algorithms

## Overview

### Big O Analysis (the base of log here is 2)

| Sort Algorithim | Time Complexity (Average) | Time Complexity (Worst) | Time Complexity (Best) | Space Complexity | Stability |
| :-------------: | :-----------------------: | :---------------------: | :--------------------: | :--------------: | :-------: |
|   Bubbel Sort   |          O(n^2)           |         O(n^2)          |          O(n)          |       O(1)       |    Yes    |
| Selection Sort  |          O(n^2)           |         O(n^2)          |         O(n^2)         |       O(1)       |    No     |
|   Quick Sort    |         O(nlogn)          |         O(n^2)          |        O(nlogn)        |     O(nlogn)     |    No     |
|   Merge Sort    |         O(nlogn)          |        O(nlogn)         |        O(nlogn)        |       O(n)       |    Yes    |
|                 |                           |                         |                        |                  |           |
|                 |                           |                         |                        |                  |           |
|                 |                           |                         |                        |                  |           |
|                 |                           |                         |                        |                  |           |
|                 |                           |                         |                        |                  |           |

### Helper Method Defined

- #### Swap

  ```java
  public void static swap(int[] arr, int num1, int num2) {
      int temp = arr[num1];
      arr[num1] = arr[num2];
      arr[num2] = temp;
  }
  ```

### Navigation

- [Bubble Sort](#bubble-sort)
- [Selection Sort](#selection-sort)

## 1. Bubble Sort

### Description

Bubble sort works by swapping adjacent elements if they're not in the desired order. This process repeats from the beginning of the array until all elements are in order.

### Algorithm

1. Compare adjacent elements. If the first one is larger than the second one, swap them;
2. Iterate setp 1 until we get to the last pair in the array.

### Optimized Algorithm

Set a flag as ***sorted***. If there is no swap in the inner loop, set ***sorted*** as true, terminating the program. That will save unnecessary works when the input array has been sorted.

**Note**: The time complexity would *always* be **O(n^2)** if we do not set the `sorted` boolean check.

### Implementation in Java

```java
public void static bubbleSort(int[] arr) {
    boolean sorted = false;
    while (!sorted) {
        sorted = true;
        for (int i = 0; i < arr.length-1; i++) {
            if (arr[i+1] < arr[i]) {
                swap(arr, i, i+1);
            	sorted = false;
            }
        }
    }
}
```



## 2. Selection Sort

### Description

Selection Sort divides the array into a sorted and unsorted subarray (which is similar to Bubble Sort). Though, the sorted subarray is formed by inserting the minimum element of the unsorted subarray at the end of the sorted array, by swapping.

### Algorithm

Set the length of the input array is n.

1. Initial state: unsorted subarray - Arr[1...n], sorted subarray - empty;
2. The kth sorting (k=1, 2, 3...n-1) starts, current unsorted subarray - Arr[k...n] and sorted subarray - Arr[1...k-1]. Pick the smallest element from unsorted subarray Arr[indexOfSmallest], and swap it with the first element in the unsorted subarray. Then the unsorted subarray would be Arr[k+1...n] while the sorted subarray would be Arr[1...k];
3. Iterate n-1 times.

### Implementation in Java

```java
public void static selectionSort(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
        int min = arr[i], minIndex = i;
        for (int j = i+1; j < arr.length; j++) {
            if (arr[j] < min) {
            	min = arr[j];
                minIndex = j;
            }
        }
        swap(arr, i, minIndex);
    }
}
```



## 2. Quick Sort

### Description

Quick Sort is a Divide and Conquer algorithm. It picks one element of an array as the pivot and sorts all of the other elements around it, for example smaller elements o the left, and larger to the right. This guarantees that the pivot is in its proper place affect the process. Then the algorithm recursively does the same for the left and right portions of the array.

### Algorithm

Use Divide and Conquer to separate a list into two sub-lists.

1. Pick one element from the array ramdomly and set it as the ***pivot***;
2. Rearrage the array to make elements smaller than ***pivot*** placed before it while elements larger than ***pivot*** behind it; This process is called *partition*;
3. Recursively pick pivot and rearrange the array.

### Implementation in Java

```java
public void static quickSort(int[] arr, int start, int end) {
    if (end <= start) return;
    int pivot = partition(arr, start, end);
    quickSort(arr, start, pivot-1);
    quickSort(arr, pivot+1, end);
}

public void static partition(int[] arr, int start, int end) {
    int pivot = end;
    
    int count = 0;
    for (int i = 0; i < arr.length; i++) {
        if (arr[pivot] > arr[i]) {
            swap(arr, count, i);
            count++;
        }
    }
    swap(arr, pivot, count);
    return count;
}
```


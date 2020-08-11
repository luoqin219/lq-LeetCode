# Summary of All Sorting Algorithms

## Overview

### Big O Analysis (the base of log here is 2)

| Sort Algorithim | Time Complexity (Average) | Time Complexity (Worst) | Time Complexity (Best) | Space Complexity | Stable* |
| :-------------: | :-----------------------: | :---------------------: | :--------------------: | :--------------: | :-----: |
|   Bubbel Sort   |          O(n^2)           |         O(n^2)          |          O(n)          |       O(1)       |   Yes   |
| Selection Sort  |          O(n^2)           |         O(n^2)          |         O(n^2)         |       O(1)       |   No    |
|   Quick Sort    |         O(nlogn)          |         O(n^2)          |        O(nlogn)        |     O(nlogn)     |   No    |
|   Merge Sort    |         O(nlogn)          |        O(nlogn)         |        O(nlogn)        |       O(n)       |   Yes   |
| Insertion Sort  |          O(n^2)           |         O(n^2)          |          O(1)          |       O(1)       |   Yes   |
|  Counting Sort  |          O(n+k)           |         O(n+k)          |         O(n+k)         |       O(k)       |   Yes   |
|                 |                           |                         |                        |                  |         |
|                 |                           |                         |                        |                  |         |
|                 |                           |                         |                        |                  |         |

*Stable: If element a is before b and a equals to b before sorting is processed, stable algorithm will keep a before b after being processed. Otherwise, the algorithm is not stable.

### Helper Method Defined

- #### Swap

  ```java
  public void static swap(int[] arr, int num1, int num2) {
      int temp = arr[num1];
      arr[num1] = arr[num2];
      arr[num2] = temp;
  }
  ```



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



## 3. Quick Sort

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



## 4. Merge Sort

### Description

Merge Sort uses recursion to solve the problem of sorting efficiently, and in particular it uses a Divide and Conquer approach.

### Algorithm

1. Separate the n-length input into two 2/n-length sublists;
2. Sort these two sublists using merge sort separately;
3. Merge these two sublists to be a whole sorted list.

### Implementation in Java

```java
public static void mergeSort(int[] arr, int left, int right) {
    if (left >= right) return;
    int mid = left + (right-left)/2; // avoid stackOverFlow
    mergeSort(arr, left, mid);
    mergeSort(arr, mid+1, right);
    merge(arr, left, mid, right);
}

public static void merge(int[] arr, int left, int mid, int right) {
    int lenLeft = mid - left + 1;
    int lenRight = right - mid;
    
    // copy sorted sub-arrays to temporary arrays
    int[] leftArr = new int[lenLeft];
    int[] rightArr = new int[lenRight];
    System.arraycopy(arr, 0, leftArr, 0, lenLeft);
    System.arraycopy(arr, mid+1, rightArr, 0, lenRight);
    
    // cursors for left and right
    int leftIndex = 0, rightIndex = 0;
    
    // sort left and right back into one array
    for (int i = left; i < right+1; i++) {
        if (leftIndex < lenLeft && rightIndex < lenRight) { // there are still elements uncopied from both arrays
            if (leftArr[leftIndex] < rightArr[rightIndex]) {
                arr[i] = leftArr[leftIndex];
                leftIndex++;
            } else {
                arr[i] = rightArr[rightIndex];
                rightIndex++;
            }
        } else if (leftIndex < lenLeft) { // only left one has uncopied elements
            arr[i] = leftArr[leftIndex];
            leftIndex++;
        } else if (rightIndex < lenRight) { // only right one has uncopied elements
            arr[i] = rightArr[rightIndex];
            rightIndex++;
        }
    }
}
```



## 5. Insertion Sort

### Description

The idea behind Insertion Sort is dividing the array into the sorted and unsorted subarrays. For unsorted elements, scan the sorted ones from its end to its start to find a position for the element to be inserted.

### Algorithm

1. Start from the first element which we can regard it to be sorted;
2. Pick next element, scan the sorted list from end to start;
3. If the element in the sorted list is larger than new unsorted one, move this element to next position;
4. Repeat step 3 until we find the element in the sorted list that is smaller than or equal to the new unsorted one;
5. Insert new element behind that element;
6. Repeat step 2-5.

### Implementation in Java

```java
public static void insertionSort(int[] arr) {
    for (int i = 1; i < arr.length; i++) {
        int curr = arr[i];
        int j = i - 1;
        while (j >= 0 && curr < arr[j]) {
            arr[j+1] = arr[j];
            j--;
        }
        arr[j+1] = curr;
    }
}
```



## 6. Counting Sort

### Description

[Counting sort](http://en.wikipedia.org/wiki/Counting_sort) is a sorting technique based on keys between a specific range. It works by counting the number of objects having distinct key values (kind of hashing). Then doing some arithmetic to calculate the position of each object in the output sequence.

### Algorithm

1. Find the largest element and smallest element in the unsorted array;
2. Count the number of occurence of every element with value i, and store the count in to arr[i];
3. Sum up all count (starting from arr[0], and resulting from ...+arr[i-1]+arr[i]);
4. Reversly input into the target array: put every elemnt i into the new array newArr[arr[i]], and once after putting it minus arr[i] by 1;

### Implementation in Java

```java
public static int[] CountingSort(int[] array) {
        if (array.length == 0) return array;
        int bias, min = array[0], max = array[0];
        for (int i = 1; i < array.length; i++) {
            Math.max(max, array[i]);
            Math.min(min, array[i]);
        }
        bias = 0 - min;
        int[] bucket = new int[max - min + 1];
        Arrays.fill(bucket, 0);
        for (int i = 0; i < array.length; i++) {
            bucket[array[i] + bias]++;
        }
        int index = 0, i = 0;
        while (index < array.length) {
            if (bucket[i] != 0) {
                array[index] = i - bias;
                bucket[i]--;
                index++;
            } else
                i++;
        }
        return array;
    }
```


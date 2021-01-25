> 可以用 [912. 排序数组](https://leetcode-cn.com/problems/sort-an-array/) 验证代码正确性。

## 快速排序

```java
class Solution {
    public int[] sortArray(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
        return nums;
    }

    private void quickSort(int[] a, int l, int r) {
        if (l < r) {
            int m = partition(a, l, r);
            quickSort(a, l, m);
            quickSort(a, m + 1, r);
        }
    }

    private int partition(int[] a, int l, int r) {
        int pivot = a[l];
        while (l < r) {
            while (l < r && a[r] >= pivot) {
                --r;
            }
            a[l] = a[r];
            while (l < r && a[l] < pivot) {
                ++l;
            }
            a[r] = a[l];
        }
        a[l] = pivot;
        return l;
    }
}
```

## 归并排序

```java
class Solution {
    public int[] sortArray(int[] nums) {
        mergeSort(nums, 0, nums.length - 1);
        return nums;
    }

    private void mergeSort(int[] a, int l, int r) {
        if (l < r) {
            int m = l + (r - l) / 2;
            mergeSort(a, l, m);
            mergeSort(a, m + 1, r);
            merge(a, l, r, m);
        }
    }

    private void merge(int[] a, int l, int r, int m) {
        int len = r - l + 1;
        int[] temp = new int[len];
        int i = l, j = m + 1, k = 0;
        while (i <= m && j <= r) {
            if (a[i] < a[j]) {
                temp[k++] = a[i++];
            } else {
                temp[k++] = a[j++];
            }
        }
        while (i <= m) {
            temp[k++] = a[i++];
        }
        while (j <= r) {
            temp[k++] = a[j++];
        }
        System.arraycopy(temp, 0, a, l, len);
    }
}
```

## 堆排序

```java
class Solution {
    public int[] sortArray(int[] nums) {
        heapSort(nums);
        return nums;
    }

    private void heapSort(int[] a) {
        int len = a.length;
        for (int i = len / 2 - 1; i >= 0; --i) {
            sink(a, i, len);
        }
        for (int i = len - 1; i > 0; --i) {
            swap(a, 0, i);
            sink(a, 0, i);
        }
    }

    private void sink(int[] a, int i, int len) {
        for (;;) {
            int l = i * 2 + 1;
            int r = l + 1;
            int largest = i;
            if (l < len && a[l] > a[largest]) {
                largest = l;
            }
            if (r < len && a[r] > a[largest]) {
                largest = r;
            }
            if (largest == i) {
                break;
            }
            swap(a, i, largest);
            i = largest;
        }
    }

    private void swap(int[] a, int i, int j) {
        a[i] = a[i] ^ a[j];
        a[j] = a[i] ^ a[j];
        a[i] = a[i] ^ a[j];
    }
}
```

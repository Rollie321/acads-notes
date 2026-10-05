# 8 Sorting Algorithms in C++

## 1. Bubble Sort

```cpp
void bubbleSort(int arr[], int n) {
    for (int i = 0; i < n - 1; i++) {
        for (int j = 0; j < n - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
            }
        }
    }
}
```
**Explanation:** Repeatedly compares adjacent elements and swaps them if they're out of order, pushing the largest unsorted element to the end each pass. Time complexity: O(n²).

**Common usage:** Teaching sorting concepts; rarely used in production code.
**Advantages:** Very simple to understand and implement; detects if the list is already sorted (can exit early).
**Disadvantages:** Extremely slow on large datasets; O(n²) comparisons and swaps even in typical cases.

---

## 2. Selection Sort

```cpp
void selectionSort(int arr[], int n) {
    for (int i = 0; i < n - 1; i++) {
        int minIdx = i;
        for (int j = i + 1; j < n; j++) {
            if (arr[j] < arr[minIdx]) {
                minIdx = j;
            }
        }
        int temp = arr[minIdx];
        arr[minIdx] = arr[i];
        arr[i] = temp;
    }
}
```
**Explanation:** Finds the minimum element from the unsorted portion and swaps it into its correct position at the front. Time complexity: O(n²).

**Common usage:** Small datasets, or when memory writes are costly (it performs at most n swaps).
**Advantages:** Simple; minimizes the number of swaps compared to other O(n²) algorithms.
**Disadvantages:** Still O(n²) comparisons regardless of input order; not stable; poor performance on large datasets.

---

## 3. Insertion Sort

```cpp
void insertionSort(int arr[], int n) {
    for (int i = 1; i < n; i++) {
        int key = arr[i];
        int j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;
    }
}
```
**Explanation:** Builds the sorted array one element at a time by inserting each new element into its correct position among the already-sorted elements. Time complexity: O(n²), but fast on nearly-sorted data.

**Common usage:** Small or nearly-sorted datasets; used as the base case inside hybrid algorithms like Timsort and Introsort.
**Advantages:** Simple; efficient for small or nearly-sorted inputs (O(n) best case); stable; sorts in-place; works well as an online algorithm (can sort data as it arrives).
**Disadvantages:** O(n²) on large, randomly-ordered datasets; many shifting operations required.

---

## 4. Merge Sort

```cpp
void merge(int arr[], int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;
    int* L = new int[n1];
    int* R = new int[n2];

    for (int i = 0; i < n1; i++) L[i] = arr[left + i];
    for (int j = 0; j < n2; j++) R[j] = arr[mid + 1 + j];

    int i = 0, j = 0, k = left;
    while (i < n1 && j < n2) {
        arr[k++] = (L[i] <= R[j]) ? L[i++] : R[j++];
    }
    while (i < n1) arr[k++] = L[i++];
    while (j < n2) arr[k++] = R[j++];

    delete[] L;
    delete[] R;
}

void mergeSort(int arr[], int left, int right) {
    if (left < right) {
        int mid = left + (right - left) / 2;
        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }
}
```
**Explanation:** A divide-and-conquer algorithm that recursively splits the array in half, sorts each half, then merges the sorted halves. Time complexity: O(n log n), stable and predictable.

**Common usage:** Sorting linked lists; external sorting (large datasets that don't fit in memory); any case requiring stable, guaranteed O(n log n) performance.
**Advantages:** Consistent O(n log n) performance regardless of input order; stable; well-suited to parallelization and external sorting.
**Disadvantages:** Requires O(n) extra space; slower than in-place algorithms like quicksort in practice due to memory allocation and copying overhead.

---

## 5. Quick Sort

```cpp
int partition(int arr[], int low, int high) {
    int pivot = arr[high];
    int i = low - 1;
    for (int j = low; j < high; j++) {
        if (arr[j] < pivot) {
            i++;
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
        }
    }
    int temp = arr[i + 1];
    arr[i + 1] = arr[high];
    arr[high] = temp;
    return i + 1;
}

void quickSort(int arr[], int low, int high) {
    if (low < high) {
        int pi = partition(arr, low, high);
        quickSort(arr, low, pi - 1);
        quickSort(arr, pi + 1, high);
    }
}
```
**Explanation:** Picks a pivot element and partitions the array so smaller elements go left and larger go right, then recursively sorts each partition. Time complexity: O(n log n) average, O(n²) worst case.

**Common usage:** General-purpose sorting; the default algorithm behind many standard library sort functions (often combined with other algorithms, e.g., introsort).
**Advantages:** Very fast in practice due to good cache locality and low constant factors; sorts in-place (low memory overhead).
**Disadvantages:** Worst-case O(n²) on poorly chosen pivots (e.g., already-sorted data with a naive pivot choice); not stable; recursive calls can cause stack overhead on large inputs.

---

## 6. Heap Sort

```cpp
void heapify(int arr[], int n, int i) {
    int largest = i;
    int left = 2 * i + 1;
    int right = 2 * i + 2;

    if (left < n && arr[left] > arr[largest]) largest = left;
    if (right < n && arr[right] > arr[largest]) largest = right;

    if (largest != i) {
        int temp = arr[i];
        arr[i] = arr[largest];
        arr[largest] = temp;
        heapify(arr, n, largest);
    }
}

void heapSort(int arr[], int n) {
    for (int i = n / 2 - 1; i >= 0; i--) {
        heapify(arr, n, i);
    }
    for (int i = n - 1; i > 0; i--) {
        int temp = arr[0];
        arr[0] = arr[i];
        arr[i] = temp;
        heapify(arr, i, 0);
    }
}
```
**Explanation:** Builds a max-heap from the array, then repeatedly extracts the maximum element and places it at the end. Time complexity: O(n log n), in-place but not stable.

**Common usage:** Systems with strict memory constraints; priority queue implementations; situations needing guaranteed O(n log n) with O(1) space (e.g., embedded systems).
**Advantages:** Guaranteed O(n log n) in all cases; sorts in-place with O(1) extra space; no worst-case blowup like quicksort.
**Disadvantages:** Not stable; generally slower in practice than quicksort due to poor cache locality; more complex to implement correctly.

---

## 7. Counting Sort

```cpp
void countingSort(int arr[], int n) {
    if (n == 0) return;
    int maxVal = arr[0];
    for (int i = 1; i < n; i++) {
        if (arr[i] > maxVal) maxVal = arr[i];
    }

    int* count = new int[maxVal + 1]();
    for (int i = 0; i < n; i++) count[arr[i]]++;

    int idx = 0;
    for (int val = 0; val <= maxVal; val++) {
        while (count[val] > 0) {
            arr[idx++] = val;
            count[val]--;
        }
    }

    delete[] count;
}
```
**Explanation:** Counts occurrences of each value, then reconstructs the sorted array from those counts. Only works well for non-negative integers within a known, limited range. Time complexity: O(n + k), where k is the range of input values.

**Common usage:** Sorting integers within a small, known range (e.g., exam scores, ages, digit-based sorting as a subroutine in radix sort).
**Advantages:** Linear time O(n + k) when k is small; stable; no comparisons needed.
**Disadvantages:** Impractical when the range of values (k) is very large relative to n; only works for discrete values (typically non-negative integers); requires extra memory proportional to the value range.

---

## 8. Radix Sort

```cpp
int getMax(int arr[], int n) {
    int mx = arr[0];
    for (int i = 1; i < n; i++)
        if (arr[i] > mx) mx = arr[i];
    return mx;
}

void countSortByDigit(int arr[], int n, int exp) {
    int* output = new int[n];
    int count[10] = {0};

    for (int i = 0; i < n; i++)
        count[(arr[i] / exp) % 10]++;

    for (int i = 1; i < 10; i++)
        count[i] += count[i - 1];

    for (int i = n - 1; i >= 0; i--) {
        int digit = (arr[i] / exp) % 10;
        output[count[digit] - 1] = arr[i];
        count[digit]--;
    }

    for (int i = 0; i < n; i++)
        arr[i] = output[i];

    delete[] output;
}

void radixSort(int arr[], int n) {
    int m = getMax(arr, n);
    for (int exp = 1; m / exp > 0; exp *= 10)
        countSortByDigit(arr, n, exp);
}
```
**Explanation:** Sorts numbers digit by digit, from least significant to most significant, using counting sort as a stable subroutine at each digit position. Time complexity: O(d × (n + k)), where d is the number of digits and k is the digit range (10).

**Common usage:** Sorting large sets of fixed-length integers or strings (e.g., phone numbers, IP addresses, postal codes).
**Advantages:** Can beat comparison-based sorts (O(n log n)) when the number of digits is small relative to n; stable; no direct comparisons between elements.
**Disadvantages:** Only works on data with a defined digit/character structure (integers, fixed-length strings); performance depends heavily on digit count and base; requires extra memory for the counting subroutine.

---

## Quick Comparison

| Algorithm      | Best Case   | Average Case | Worst Case  | Space   | Stable |
|----------------|-------------|---------------|-------------|---------|--------|
| Bubble Sort    | O(n)        | O(n²)         | O(n²)       | O(1)    | Yes    |
| Selection Sort | O(n²)       | O(n²)         | O(n²)       | O(1)    | No     |
| Insertion Sort | O(n)        | O(n²)         | O(n²)       | O(1)    | Yes    |
| Merge Sort     | O(n log n)  | O(n log n)    | O(n log n)  | O(n)    | Yes    |
| Quick Sort     | O(n log n)  | O(n log n)    | O(n²)       | O(log n)| No     |
| Heap Sort      | O(n log n)  | O(n log n)    | O(n log n)  | O(1)    | No     |
| Counting Sort  | O(n + k)    | O(n + k)      | O(n + k)    | O(k)    | Yes    |
| Radix Sort     | O(d(n + k)) | O(d(n + k))   | O(d(n + k)) | O(n + k)| Yes    |

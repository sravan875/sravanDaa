#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef int (*compare_fn)(void*, void*);

void swap(void** a, void** b) {
    void* t = *a;
    *a = *b;
    *b = t;
}

int partition(void* arr[], int low, int high, compare_fn compare) {
    void* pivot = arr[high];
    int i = (low - 1);

    for (int j = low; j <= high - 1; j++) {
        if (compare(arr[j], pivot) < 0) {
            i++;
            swap(&arr[i], &arr[j]);
        }
    }
    swap(&arr[i + 1], &arr[high]);
    return (i + 1);
}

void quickSort(void* arr[], int low, int high, compare_fn compare) {
    if (low < high) {
        int pi = partition(arr, low, high, compare);
        quickSort(arr, low, pi - 1, compare);
        quickSort(arr, pi + 1, high, compare);
    }
}

void merge(void* arr[], int l, int m, int r, compare_fn compare) {
    int i, j, k;
    int n1 = m - l + 1;
    int n2 = r - m;
    void* L[n1];
    void* R[n2];
    for (i = 0; i < n1; i++)
        L[i] = arr[l + i];
    for (j = 0; j < n2; j++)
        R[j] = arr[m + 1 + j];
    i = 0;
    j = 0;
    k = l;
    while (i < n1 && j < n2) {
        if (compare(L[i], R[j]) <= 0) {
            arr[k] = L[i];
            i++;
        } else {
            arr[k] = R[j];
            j++;
        }
        k++;
    }
    while (i < n1) {
        arr[k] = L[i];
        i++;
        k++;
    }
    while (j < n2) {
        arr[k] = R[j];
        j++;
        k++;
    }
}

void mergeSort(void* arr[], int l, int r, compare_fn compare) {
    if (l < r) {
        int m = l + (r - l) / 2;
        mergeSort(arr, l, m, compare);
        mergeSort(arr, m + 1, r, compare);
        merge(arr, l, m, r, compare);
    }
}

void printArray(void* arr[], int size, int isString) {
    if (isString) {
        for (int i = 0; i < size; i++)
            printf("%s ", (char*)arr[i]);
    } else {
        for (int i = 0; i < size; i++)
            printf("%d ", *(int*)arr[i]);
    }
    printf("\n");
}

int int_compare(void* a, void* b) {
    return (*(int*)a - *(int*)b);
}

int str_compare(void* a, void* b) {
    return strcmp((char*)a, (char*)b);
}

void sort(void* arr[], int n, compare_fn compare, int isString) {
    if (n<20) {
        printf("Quicksort\n");
        mergeSort(arr, 0, n - 1, compare);
    } else {
        printf("mergesort\n");
        quickSort(arr, 0, n - 1, compare);
    }
    printf("Sorted data is \n");
    printArray(arr, n, isString);
}

int main() {
    int n, isString;
    printf("Enter data size: ");
    scanf("%d", &n);
    printf("Enter 1 for strings or 0 for integers: ");
    scanf("%d", &isString);

    void* a[n];
    if (isString) {
        for (int i = 0; i < n; i++) {
            char* str = malloc(8 * sizeof(char));
            for (int j = 0; j < 7; j++) {
                str[j] = 'a' + rand() % 26;
            }
            str[7] = '\0';
            a[i] = str;
        }
        sort(a, n, str_compare, isString);
    } else {
        for (int i = 0; i < n; i++) {
            int* num = malloc(sizeof(int));
            *num = rand() % 100;
            a[i] = num;
        }
        sort(a, n, int_compare, isString);
    }

    // Free dynamically allocated memory
    for (int i = 0; i < n; i++) {
        free(a[i]);
    }

    return 0;
}

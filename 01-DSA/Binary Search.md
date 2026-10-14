# Binary Search
## Idea

Binary search works on sorted arrays.
It repeatedly divides the search space into half.

## Time Complexity

- Best Case: O(1)
- Average Case: O(log n)
- Worst Case: O(log n)

## Algorithm

1. Find the middle index.
2. Compare target with the middle element.
3. If target is smaller -> search left half
4. Else -> search right half

## Code

```cpp
int binarySearch(int arr[], int n, int target) {
	int low = 0;
	int high = n - 1;
	
	while (low <= high) {
		int mid = (low + high) / 2
		
		if (arr[mid] == target) {
			return mid;
		} elseif (arr[mid] <= target) {
			low = mid + 1
		} else {
			high = mid - 1
		}
	}
		
	return -1
}
```

## Important Note

- Array must be sorted.
- Much faster than linear search.

## Related Notes
[[Arrays]]
[[Divide & Conquer]]




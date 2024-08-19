Those below are some of the undefined behaviors in `C++`:
* Dereferencing a null pointer.
* Dereferencing an `end` iterator.
* Casting a pointer to a incompatible type and **dereferencing** it. Exception: `char*` and `unsigned char*` are compatible with any type.
* Accessing an union member that was not the last one written.
* Overflowing a signed integer. Exception: unsigned integers overflowing will wrap around.
* Shifting overflow a signed integer. Exception: unsigned integers shifting overflow will be `0`. Note that floating numbers are not allowed to be shifted.
* Dividing by zero.
* Flowing off the end of a value-returning function.
* Calling a function through a null pointer.
* Reading from an uninitialized variable.
* Adding or subtracting pointers out of bounds of the array.

```cpp
int arr[10] = {};
int* p = arr + 11; // Undefined behavior
int* p = arr + 10; // OK
```

* Dereferencing a pointer to a deleted object.
* Unmatching `new`, `delete`, `malloc` and `free`.
* Accessing an object having been destructed.
* Misalign pointers of `alignof(T)`
* Multi-thread data race.
* Relock an irrecurisive mutex in the same thread.
* Dereferencing a dangling pointer.
* Indexing an array out of bounds.


# Cython

## Docs, related notes

* [Cython for NumPy users](https://cython.readthedocs.io/en/latest/src/userguide/numpy_tutorial.html): shows an example how to progressively optimize a given function more and more.
* [Typed Memoryviews](https://cython.readthedocs.io/en/latest/src/userguide/memoryviews.html): Explains the use of memoryviews such as
    ```python
    # Memoryview on a NumPy array
    narr = np.arange(27, dtype=np.dtype("i")).reshape((3, 3, 3))
    cdef int [:, :, :] narr_view = narr
    ```
    - Can be sliced and indexed, *but based on my experience it seems that you cannot assign to a slice of a memoryview directly* (you can assign a slice *of* a memoryview to a different object though).
    - Can be declared C- or Fortran-contiguous (via `::1` notation) for even better performance.
* [Language basics](https://cython.readthedocs.io/en/latest/src/userguide/language_basics.html): Difference between `def` and `cdef` and plenty other basics. Quite long.
* [Extension types](https://cython.readthedocs.io/en/latest/src/userguide/extension_types.html): Create new Python types aka extension types, e.g., via `cdef class ...`.

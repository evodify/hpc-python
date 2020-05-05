# Lecture notes

## Timers

### Code Region Timers

To measure time of a specific region of code use `time.process_time`:

```
python notes/process_time.py
```

Timing can be also done with a [context manager](https://docs.python.org/3/reference/datamodel.html#context-managers):

```
python notes/process_time_context.py
```

### Timeit Small bits code

To measure reliably very short execution times (statement or a function call)
one needs to gather some statistics for **error estimates**. It can be done with `timeit`.

The module can be used from the command line, within a program, or within an interactive interpreter (e.g. IPython).

When a code snippet is measured with `timeit`, the module takes care of running
the snippet several times and calculating statistics.

**IPython**:

```
IPython 6.1.0 -- An enhanced Interactive Python. Type '?' for help.

In [1]: from math import sin, cos

In [2]: %timeit sin(0.2)
68.8 ns ± 0.181 ns per loop (mean ± std. dev. of 7 runs, 10000000 loops each)

In [3]: %timeit cos(0.2)
71.1 ns ± 1.57 ns per loop (mean ± std. dev. of 7 runs, 10000000 loops each)
```

**Command Line**:

```
python3 -m timeit -n 10000000 -s "from math import sin" "sin(0.2)"
# 10000000 loops, best of 5: 26.6 nsec per loop
```

The `timeit` module can be used also directly inside scripts, see
[Documentation of timeit](https://docs.python.org/3/library/timeit.html).

### cProfile

*cProfile* gather statistics that describes how often and for how long 
**various parts of the program** are executed.

Run the test with *cProfile*:

```
cd performance/cprofile
python -m cProfile -o profile.dat heat_main.py
```

Explore the collected *cProfile* stats:

```
python -m pstats profile.dat
help
stats 10
sort 
sort time
stats 10
sort cumtime
stats 10
```


## Numerical computing

### NumPy Array

NumPy array is faster to process because it has a **fixed number of elements**
and thus it can store them close to each other in the computer memory,
while list elements are randomly distributed in memory.

NumPy arrays must have **the same datatype**.

#### Create NumPy array

Example:

```
import numpy
x = numpy.array((1, 2, 3, 4))
```

Unless explicitly specified, the datatype is automatically set based on the
values used to create the array. If the list would have contained one floating
point number (e.g. `[1, 2, 3.1, 4]`) then the array created would have used
floating point type for all elements.

The second argument of `array()` is the datatype to be used for the array.

```
x = numpy.array((1, 2, 3, 4), float)

print(x)
# output: [ 1.  2.  3.  4.]
```

```
data = [[1, 2, 3], [4, 5, 6]]
y = numpy.array(data, complex)

print(y)
# output: [[ 1.+0.j  2.+0.j  3.+0.j]
#          [ 4.+0.j  5.+0.j  6.+0.j]]

print(y.shape)
# output: (2, 3)

print(y.size)
# output: 6
```

**numpy.arange()** creates an array containing evenly spaces values within
a given interval, similar to the regular `range()`:

```
a = numpy.arange(10)

print(a)
# output: [0 1 2 3 4 5 6 7 8 9]
```

Optionally, one can also specify start and stop values (instead of just stop) 
as well as a step between the values.

**numpy.linspace()** generates a fixed number of evenly spaced values within
an interval:

```
b = numpy.linspace(-4.5, 4.5, 5)

print(b)
# output: [-4.5  -2.25  0.    2.25  4.5]
```

Create an array of a given shape and initialise it to zeros, ones , or arbitrary value, respectively:

```
c = numpy.zeros((4, 6), float)
d = numpy.ones((2, 4))
e = numpy.full((3, 2), 4.2)

print(c.shape)
print(d)
print(e)
# output:
#   (4, 6)
#   [[ 1.  1.  1.  1.]
#    [ 1.  1.  1.  1.]]
#   [[4.2 4.2]
#    [4.2 4.2]
#    [4.2 4.2]]
```

NumPy supports also storing strings. The largest element determines the item size.
So in practice arbitrary strings are not that suitable, but character arrays can
be sometimes useful.

```
s = numpy.array(['foo', 'foo-bar'])

print(repr(s))
# output: array(['foo', 'foo-bar'],
#               dtype='|U7')

dna = 'AAAGTCTGAC'
c = numpy.array(dna, dtype='c')

print(repr(c))
# output:
#   array([b'A', b'A', b'A', b'G', b'T', b'C', b'T', b'G', b'A', b'C'],
#         dtype='|S1')
```

#### Accessing arrays

NumPy arrays can be truly multi-dimensional.
This means that instead of a single index value, elements in a NumPy array can
have multiple index values (one for each dimension).

To access a single element in a 2D array:

```
data = numpy.array([[1, 2, 3], [4, 5, 6]])
x = data[0,2]
y = data[1,-2]

print(x, y)
# output: 3 5
```

Slicing syntax can also be used:

```
a = numpy.arange(10)

print(a[2:])
# output: [2 3 4 5 6 7 8 9]

print(a[:-1])
# output: [0 1 2 3 4 5 6 7 8]

print(a[1:7:2])
# output: [1 3 5]
```

Multi-dimensional arrays can be sliced, too, and in multiple dimensions as well:

```
a = numpy.arange(10)
a[1:3] = -1

b = numpy.zeros((4, 4))
b[1:3, 1:3] = 2.0

print(a)
print(b)
# output:
#   [ 0 -1 -1  3  4  5  6  7  8  9]
#   [[ 0.  0.  0.  0.]
#    [ 0.  2.  2.  0.]
#    [ 0.  2.  2.  0.]
#    [ 0.  0.  0.  0.]]
```

#### Copy arrays

Simple assignment creates a new reference to an array, just like for any other Python object.

To make a true copy of an array, one should use the copy() method:

```
a = numpy.arange(10)
b = a              # reference, changing values in b changes a
b = a.copy()       # true copy
```

#### View arrays

Slicing an array will create something called a view to the array.
It is like a window showing only a some part of the full array.
Any modifications to the elements in the view are directly reflected in the
original array. In fact, no real copy is made and as such any manipulation will 
just change the original array.

```
a = numpy.arange(10)
c = a[1:4]         # view, changing c changes elements [1:4] of a
c = a[1:4].copy()  # true copy of subarray
```

#### Hands-on: Array creation

Source code for this exercise is located in `numpy/array-creation/`

1. Construct a NumPy array from the list containing both integers and
    floating point value.

```
import numpy as np
a1 = np.array([1, 2, 3, 4.0, 5.0, 6.0])
print(a1)
```

2. Generate a 1D NumPy array containing all numbers from -2.0 to 2.0 with
    a spacing of 0.2.

```
a2 = np.arange(-2., 2.2, 0.2)
print(a2)
```

3. Generate another 1D NumPy array containing 11 equally spaced values 
    between 0.5 and 1.5.

```
a3 = np.linspace(0.5, 1.5, 11)
print(a3)
```

4. Take some Python string and construct from it NumPy array consisting of 
    single characters (a character array).

```
s = 'exercise'
a4 = np.array(list(s))
print(a4)
```

Better way:

```
a42 = np.array(s, dtype='c')
print(a42)
```

Because `a42` is smaller than `a4`:

```
import sys
sys.getsizeof(s)
sys.getsizeof(a4)
sys.getsizeof(a42)
# 57
# 128
# 104
```

####  Hands-on: Array slicing

Source code for this exercise is located in numpy/array-slicing/

First, create a 4x4 array with arbitrary values:

```
import numpy as np
b  = np.array([[1,2,3,4],
                [10,20,30,40],
                [100,200,300,400],
                [1000,2000,3000,4000]], float)
print(b)
```

1. Extract every element from the second row.

```
print(b[1,:])
```

2. Extract every element from the third column.

```
print(b[:, 2])
```

3. Assign a value of 0.21 to upper left 2x2 subarray.

```
b[:2, :2] = 0.21
print(b)
```

Next, create a 8x8 array with chequerboard pattern,
i.e. alternating zeros and ones:

```
chequerboard = np.zeros((8, 8), int)
chequerboard[::2, ::2] = 1
chequerboard[1::2, 1::2] = 1
print(chequerboard)
```

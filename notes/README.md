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

To measuring reliably very short execution times (statement or a function call)
one needs to gather some statistics for error estimates. It can be done with `timeit`.

The module can be used from the command line, within a program, or within an interactive interpreter (e.g. IPython).

When a code snippet is measured with `timeit`, the module takes care of running the snippet several times and calculating statistics.

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

## cProfile

cProfile gather statistics that describes how often and for how long various
parts of the program executed.

Run the test with cProfile.

```
cd performance/cprofile
python -m cProfile -o profile.dat heat_main.py
```

Explore the collected cProfile stats:

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

C++11 now knows two distinct types of `for` loops: the classic loop over an “index” and the range-based `for` loop which vastly simplifies the iteration over a range specified by a pair of iterators.

By contrast, Python knows only one loop type – roughly equivalent to the range-based for loop. In fact, loops over indices are exceedingly rare, but made possible by the use of the `range` method:

```python
for i in range(10):
    print i
```

Which does what it promises – although Python version < 3.0 does the “wrong” thing and actually instantiates the whole collection in memory at once; a remedy is `xrange` which yields values lazily as they are consumed by the loop.

C++11 effortlessly allows the same but there is no standard library function to provide this. Boost.Range provides *part* of the functionality via `irange` which only works on integers, and not for unlimited ranges (this will make sense in a second).

The header `range.hpp` provides a very basic implementation for this. It allows running the following code:

```c++
for (auto i : range(1, 5))
    cout << i << "\n";

for (auto u : range(0u))
    if (u == 3u) break;
    else         cout << u << "\n";

for (auto c : range('a', 'd'))
    cout << c << "\n";

for (auto i : range(100).step(-3))
    if (i < 90) break;
    else        cout << i << "\n";
```

`range` with a single argument deviates from the Python semantic and creates an endless loop, unless it’s interrupted manually. This is an interesting use-case that cannot be modelled in Python using `range`.

It might be worth thinking about an alternative syntax for this, though, since it means that *another* common use-case – iterating from the start index of a range until its end – is made more complicated: in Python you’d simply write `range(len(container))`. `range.hpp` on the other hand requires one to write `range(0u, container.size())`.

Adding `.step(…)` to the end specifies a step size instead of the default, 1.

The construct works for arbitrary types which fulfil the interface requirements (incrementing, copying, equality comparison, default construction in the case of infinite ranges).

I haven’t done a thorough analysis but the disassembly of a very similar code shows that the above compiles down to **identical** code to a classical `for` loop; that is, the first above code produces equivalent machine code to

```c++
for (int i = 0; i < 5; ++i)
    cout << i << "\n";
```

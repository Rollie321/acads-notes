
# Creating Array:

* with initial values
```python
import numpy as np

a = np.array(["a", "b", "c", "d", "e"])

print(a)

>>> ["a", "b", "c", "d", "e"]
```

* with zero's
```python
import numpy as np

a = np.zeros(5);
# syntax: np.zeros(number_of_elements);
print(a);

>>> array([0., 0., 0., 0., 0.])
```

* with random numbers
```python
import numpy as np

a = np.empty(3);
print(a);

>>> array([
        5.30262461e-310,
        5.30218049e-310,
        6.62792831e-310,
        5.30218061e-310,
        6.62805385e-310,
        6.62791842e-310
    ])
```

* with range of number
```python
import numpy as np

a = np.arange(4);
print(a)

>>> array([0, 1, 2, 3])

b = np.arange(1,12,3);
print(b)

>>> array([ 1,  4,  7, 10])

c = np.linspace(3, 10, num=6)
print(c)

>>>array([ 3. ,  4.4,  5.8,  7.2,  8.6, 10. ])
```


# Methods

### Sorting:
```python
import numpy as np

a = np.array([7, 3, 6, 45, 2, 1, 45, 45, 3456, 7, 3, 2]);
sorted_a = np.sort(a);

print(sorted_a)

>>> [   1    2    2    3    3    6    7    7   45   45   45 3456]
```

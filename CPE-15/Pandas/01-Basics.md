##### Import statements:
```python
import pandas as pd
```

#### Terms:
* **_DataFrame:_** represents a table
* **_Series:_** represents a singe column

---

###### Dataframe:

Manual creation:
```python
import pandas as pd

variable_name = pd.DataFrame({
    'Column_name': [value1, value2, value3],
    'Names'      : [name1, name2, name3]
})
```

This creates a table:

| Column_name | Names |
| ----------- | ----- |
| value1      | name1 |
| value2      | name2 |
| value3      | name3 |
Or read from a csv file:
```python
import pandas as pd

data = pd.read_csv("path/to/file.csv)
```
##### Series
Manual creation:
```python
import pandas as pd

age_series = pd.Series([13, 18, 17], name="Age")
```

Obtaining from a DataFrame:
```python
import pandas as pd

data = pd.DataFrame({
    "Age"  : [18, 19, 18],
    "Name" : ["Leo", "Raph", "Mike"]
})

names = data["Name"] # returns a series of all names
ages = data["Age"]   # returns a series of all age
```
 
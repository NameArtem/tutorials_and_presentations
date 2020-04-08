### How clean memroy in Python Pandas

*Libs*

```Python
import pandas as pd
import gc
```

_*for example, we have the next data sets*_

```Python
df_1 = pd.DataFrame(data1)
df_2 = pd.DataFrame(data2)

merged_df = pd.merge(df_1 ,df_2, left_on=['id'],right_on=['id'] , how='left')
```

### Step 1 (gc lib)

Simple memory cleaning

```Python
del df_1
del df_2
del merged_df
gc.collect()
```

### Step 2 (selective)

Magic command %reset in Python clean all variables, the best way is to select necessary variable with %reset_selective

[More about % commands](https://ipython.readthedocs.io/en/stable/interactive/magics.html)

```Python
%reset_selective -f df_1
%reset_selective -f df_2
%reset_selective -f merged_df
gc.collect()
```

### Step 3 (ctypes cleaning)

```Python
# create an additiona function
def malloc_trim():
    import ctypes
    # use CDLL for Linux and WINDLL for Windows
    # malloc_trim - reakes free memory to the system
    ctypes.CDLL('libc.so.6').malloc_trim(0)

del df_1
del df_2
del merged_df
malloc_trim()

gc.collect()
```

### Step 4 (reset all)

```Python
merged_df.to_csv("merged_df.csv", index=False, header = True, sep = '\t')

%reset -f

merged_df = pd.read_csv("merged_df.csv", dtypes = dtypes, , sep = '\t')
```

### Step 5 (reset all)

```Python
%reset -f
```

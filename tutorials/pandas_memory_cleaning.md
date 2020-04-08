### How clean memroy in Python Pandas

*Libs*

import pandas as pd
import gc


_*for example, we have the next data sets*_

df_1 = pd.DataFrame(data1)

df_2 = pd.DataFrame(data2)

merged_df = pd.merge(df_1 ,df_2, left_on=['id'],right_on=['id'] , how='left')

### Step 1 (gc lib)

Simple memory cleaning

del df_1
del df_2
del merged_df
gc.collect()

### Step 1 (selective)

Magic command %reset in Python clean all variables, the best way is to select necessary variable with %reset_selective

[More about % commands](https://ipython.readthedocs.io/en/stable/interactive/magics.html)

%reset_selective -f df_1
%reset_selective -f df_2
%reset_selective -f merged_df
gc.collect()

#Memorey reduced to ~ 3000MB

#*******************
#Option number 3 ***
#*******************

def malloc_trim():
import ctypes
ctypes.CDLL('libc.so.6').malloc_trim(0)

del df_1
del df_2
del merged_df
malloc_trim()

gc.collect()

#Memorey reduced to ~ 3000MB

#*******************
#Option number 4 ***
#*******************

%reset -f

#Memorey reduced to ~ ~600MB !!!!!!!

#*******************
#Option number 5 ***
#*******************

merged_df.to_csv("merged_df.csv",index=False)

%reset -f

#Memorey ~600MB RAM
merged_df = pd.read_csv("merged_df.csv")
#Memorey ~800MB RAM

#*******************
#Conclusion ********
#*******************
The only option that freed most of the memory was "%reset -f " which is not a solution.

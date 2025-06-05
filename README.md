# Art-Galary-Analysis---DA-project

**SETTING UP THE PROJECT**

- Create an **`.ipynb`** file to import the required libraries
- Make sure the kernel you select in the Jupyter notebook is updated
- kernel used for this project: **`/usr/local/bin/python3.12`**
- You can just copy paste the path in the terminal and tell what to install directly to this kernel
- For example: You are trying to import pandas library but it is not installed in your kernel. In that case you can simply go to the terminal and say:
**`/usr/local/bin/python3.12 -m pip install pandas`**

**READING FILES**

```python
artworks_df = pd.read_csv('artworks.csv')
art_on_view_df = pd.read_excel('art_on_view.xlsx')
```

- Different file types have different function to read
- You will require a file path if it is at another location or on internet

**INITIAL ANALYSIS**

- One of the most common initial steps to see rows and columns through **`.shape`** attribute. This will tell you what is the size of the file you’re working with
- **`head(#)`** fn is an inbuilt function that prints initial rows. By default its 5. This tells you about the type of values you are going to expect in the columns and how they should be handled.

```python
print("Shape: ",artworks_df.shape)
print(artworks_df.head(2))
```

- For example: If the columns have string values in quotes or maybe an escape character in the values, are they of date type?, etc

![Screenshot 2025-06-05 at 4.23.14 AM.png](attachment:3a8e6056-b396-4c27-ba1a-0fd80dfa4761:Screenshot_2025-06-05_at_4.23.14_AM.png)

- **Commonalities:** When you look at multiple dataframes through head(#) method, you can spot some key attributes like:
    - Common columns
    - Null values
    - Data types

**TIP 💡**

When you use **`head(#)`** function, if you have smaller screen, it might break the output columns continuation and can look ugly. To prevent this you should always set the option as below before start looking at your dataframes:

```python
pd.set_option('display.max_columns', None) # show all columns horizontally
pd.set_option('display.max_rows', 500)
```

**MERGE / JOINS**

- Before merging it is important to verify the datatypes of the columns you are merging.
- Object column types should not be merged directly

![Screenshot 2025-06-05 at 4.28.27 PM.png](attachment:16d5ad6c-74dc-42b2-b27a-c5e534fcb4ad:Screenshot_2025-06-05_at_4.28.27_PM.png)

- You should always convert the column types into String using as shown below:

```python
artists_df['ConstituentID'] = artists_df['ConstituentID'].astype(str)
artworks_df['ConstituentID'] = artworks_df['ConstituentID'].astype(str)
art_on_view_df['ConstituentID'] = art_on_view_df['ConstituentID'].astype(str)
```

- Once you have converted the join column into string or integer, you can merge the tables.

```python
merged_df = pd.merge(artworks_df, artists_df, on='ConstituentID', how='left')
print("merged_df: ", merged_df.shape) # show rows and columns
print(merged_df.head(5)) # show first 5 rows
```

**DATE-TIME CONVERSION**

- It is always advisable to convert **`Date-Time`** column into its correct type.
- The default dataset can have this column in string or object type which would introduce difficulties in the analysis when compared to timelines.

```python
merged_on_view_df['DateAcquired'] = pd.to_datetime(merged_on_view_df['DateAcquired'], errors='coerce')
# coerce -> fix forcefully, NaT is is overwritten if the any errors in conversion are thrown
```

**VALUE COUNTS (OCCURANCES)**
![Screenshot 2025-06-05 at 6.44.20 PM.png](attachment:1eea6a00-b381-499f-880e-03bdc3891f91:Screenshot_2025-06-05_at_6.44.20_PM.png)

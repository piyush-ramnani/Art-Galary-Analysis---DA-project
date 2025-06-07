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

![Screenshot 2025-06-05 at 6.44.20 PM.png](attachment:1eea6a00-b381-499f-880e-03bdc3891f91:Screenshot_2025-06-05_at_6.44.20_PM.png)

**BUSINESS QUESTIONS**

```markdown
- How many unique artists are there?
- What are the top 3 nationalities?
- What is the gender distribution?
- What are the top 10 art classification?
- What is the artwork distribution per department?
- What is the artwork acquisition trend per year?
- Find top artists with most amount of art work.
- Find the total artwork on display.
```

- Most of the charts in this project was built on the function you just saw **`value_count( )`** similar to the output you saw above for ‘Artists’
- The **occurrences** become the x-axis and y-axis. For example:

```python
unique_artist = merged_on_view_df['Artist'].unique()
top_nationalities = merged_on_view_df['Nationality'].value_counts().head(5)
gender_distribution = merged_df['Gender_y'].value_counts()
top_artwork_types = merged_on_view_df['Classification'].value_counts().head(10)
artwork_by_department = merged_on_view_df.Department.value_counts()
acquisition_trends_yearly = merged_on_view_df['DateAcquired'].dt.year.value_counts().sort_index()
top_artists_by_artworks = merged_df.DisplayName.value_counts().head(10)
total_art_displayed = merged_on_view_df.OnView.notna().sum()
```

CHARTS

```python

import plotly.express as px
import plotly.io as pio  # Import plotly.io

# Set the default renderer to 'plotly_mimetype' for Jupyter Notebook
# You can choose a different template if you prefer
pio.templates.default = "plotly_dark"
# Or "notebook" if you are using classic Jupyter Notebook
pio.renderers.default = "jupyterlab"

fig1 = px.bar(x=top_nationalities.index,
              y=top_nationalities.values,
              title='Top 5 Nationalities of Artists in MoMA collection',
              labels={'x': 'Nationality', 'y': 'Number of Artists'},
              color=top_nationalities.values)

fig1.show()

# --------------------------
# ARTWORKS BY CLASSIFICATION
# --------------------------

plt.style.use('dark_background')
artwork_by_department.plot(kind='bar', figsize=(10, 6), color='skyblue')
plt.title('Number of Artworks by Department')
plt.xlabel('Department')
plt.ylabel('Number of Artworks')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()

# --------------------------
# GENDER DISTRIBUTION OF ARTISTS
# --------------------------
plt.style.use('dark_background')
plt.figure(figsize=(6, 10))
colors = plt.cm.Paired(range(len(gender_distribution)))
wedges, texts = plt.pie(
    gender_distribution,
    startangle=90,
    colors=colors,
    labels=None,
    wedgeprops={'linewidth': 0, 'edgecolor': 'white'}
)
# Prepare legend labels with counts and percentages
total = gender_distribution.sum()
legend_labels = [
    f"{label}: {count} ({count/total:.2%})"
    for label, count in zip(gender_distribution.index, gender_distribution.values)
]
plt.title('Gender Distribution in all artworks')
plt.legend(wedges, legend_labels, bbox_to_anchor=(1.05, 1), loc='upper left')
plt.ylabel('')
plt.tight_layout()
plt.show()

# --------------------------
# ACQUISITION TRENDS OVER THE YEARS
# --------------------------
plt.style.use('dark_background')  # Set dark background for the plot

acquisition_trends_yearly.plot(
    kind='line', figsize=(12, 6), marker='o', color='orange')

plt.title('Artwork acquisition Trends Over the Years')
plt.xlabel('Year')
plt.ylabel('Number of Artworks Acquired')
plt.grid(True, linestyle='--', alpha=0.4)

years = acquisition_trends_yearly.index.astype(int)
plt.xticks(
    ticks=years[::5],
    rotation=45
)
plt.tight_layout()
plt.show()
```

- [**`plotly.express`**](http://plotly.express) has better charts option than plain `Matplotlib`

![image.png](attachment:341d7026-f3c5-45fd-87c2-ddd71afc43da:image.png)

![image.png](attachment:7fe3c6b5-d0f9-4e2f-a5ff-5a477f0fa12f:image.png)

![image.png](attachment:b458108b-a0bd-4dbf-aa76-750ee90b318f:image.png)

![image.png](attachment:8bafa141-7cb4-48a5-9903-aaad34472cc1:image.png)

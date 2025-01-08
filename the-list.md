# Tools and libraries for air quality and meteorology analysis in Python

This list is not comprehensive and is continuously edited. At the moment, it is roughly sorted categorically by the type of work the library or tool pertains to.

## Data management
The first step in most analysis tasks is to import your data. Here, I am going to list just one library, due to its versatility and ubiquity:
### [pandas](https://github.com/pandas-dev/pandas)
pandas is a versatile library for importing and manipulating tabular data. I could not reasonably list all the things pandas can do here - I encourage you to go look at its documentation. Nevertheless, some of my favourite features include:
 - Importing and exporting various tabular data formats, including CSV, parquet, Excel [and more](https://pandas.pydata.org/docs/reference/io.html). If you are migrating from other statstical software, pandas can even read SAS and SPSS files!
 - Easy indexing by row and column name, including slicing. You can slice pandas DataFrames using square brackets like an array (`df[x][y]`), but the better (and safer) `pandas.DataFrame.loc` method can handle all sorts of complicated indexing tasks.
 - Date-and-time indices, with built-in slicing, setting index frequencies, timezone and daylight savings setting and handling, and resampling. Importantly for met and AQ data, the [pandas.DataFrame.resample](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.resample.html) method lets you easily specify if the resampled indices should be right- or left-ending and labelled, which is critical for met and AQ data that is often right-side labelled and ending.
 - Function broadcasting: doing something like `df[column_name] * 2` will intuitively return the column multiplied by two element-wise. This also works with numpy and SciPy functions, so something like  `np.exp(df[column_name])` works.
 - Group-by's: you can apply calculations or methods based on groups within pandas columns, so you can calculate averages grouped by a categorical column, or binned continuous values, and even combinations of columns.
 - MultiIndexes: hierarchical indices. Obviously, this is great for complicated group-by's, hierarchical data, and mixed-effects models.

Unfortunately, pandas has some downsides. It is not always very fast, especially with quite large data, and it holds everything in memory and executes code eagerly. If you are dealing with small-to-medium data (i.e. a few hundred columns and/or up to a few million rows) this might not be a problem, and if you don't even know what _eager execution_ means, you might not need to care. If you're dealing with large data or want _lazy execution_, however, you should check out the libraries in the **Big data and other hard tasks** section below. You can also take a look at [this](https://github.com/baggiponte/awesome-pandas-alternatives) list of pandas alternatives, which goes beyond the few alternatives I mention here.

**Migrating from R?** If you used R's data.frame or the [dplyr](https://github.com/tidyverse/dplyr) package, you should find all the functionality you need in pandas, including column creation, filtering, group-by's, etc. If you use R's built-in time-series ([ts](https://www.rdocumentation.org/packages/stats/versions/3.6.2/topics/ts)), pandas' DateTimeIndex functionality should include what you need.

<details>
<summary>A note on avoiding unexpected behaviour in pandas</summary>
  Sometimes doing complicated indexing in pandas can lead to a value you expect to be changed, to not actually change; or vice versa. This can happen when you <a href="https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy">chain together indexing operations</a> or when you create a second DataFrame or Series that is a subset of some other DataFrame or Series, and then change the values of that subset. This can sometimes (but not always) change the original DataFrame/Series, since the subset is sometimes created as a <i>copy</i> but other times it's created as a <i>view</i> and acts like a pointer to some part of the parent DataFrame/Series. If you're doing anything that requires making subsets or copies of a DataFrame and you want to preserve the original, you should read the <a href="https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.copy.html">pandas.DataFrame.copy</a> and <a href="https://pandas.pydata.org/pandas-docs/stable/user_guide/copy_on_write.html#copy-on-write">Copy-on-Write</a> sections of the documentation.
</details>

## Basic math, scientific computing, and fundamental statistics
Python, like many high-level programming languages, has all the basic mathematical operations built-in (*, /, +, -, **), and has a fairly robust set of mathematical operations in the [math](https://docs.python.org/3/library/math.html) module. These libraries extend Python's mathemtical and scientific functionality for linear algebra, statistics, and other more specific tasks.

### [numpy]()


### [SciPy]()


### [statsmodels]()



## Data science and machine learning

### [scikit-learn (sklearn)](https://github.com/scikit-learn/scikit-learn)


### [PyTorch]()


### [TensorFlow]()


## Graphics and figure creation
These libraries focus on the creation of graphics or figures from data. I strongly encourage Python users to
### [matplotlib](https://github.com/matplotlib/matplotlib)
This is (anecdotally) the most popular Python plotting library. It is robust, fairly fast, and has a huge variety of options for producing good-looking figures directly in Python. 

### [plotly](https://github.com/plotly/plotly.py)


### [seaborn](https://github.com/mwaskom/seaborn)


### [bokeh](https://github.com/bokeh/bokeh)


### [plotnine](https://github.com/has2k1/plotnine)
In its own words, plotnine implements a "grammar of graphics" with the intent of being similar to R's [ggplot2](https://github.com/tidyverse/ggplot2). **Migrating from R?** If you used ggplot2, then you might like plotnine, for obvious reasons.


## Meteorology and air quality
These libraries are specific to meteorology, air quality, or related fields. In some cases they have some overlapping functionality with other, more general libraries, so if you find you need only one or two features, it might be worth re-checking the features in the libraries you're already using.

### [windrose](https://github.com/python-windrose/windrose)
This library lets you plot windroses, a feature not available in any of the other plotting libraries mentioned here. Other libraries like matplotlib can produce polar plots, but windroses are unusual because they place zero on the north (positive $y$) axis, and they rotate/are positive in the clockwise direction; nominal polar plots by convention place zero to the east and rotate counterclockwise. windrose handles this trickiness for you, and with a little cleverness you can add windrose plots to other matplotlib figures (e.g. as a subplot).

**Migrating from R?** If you previously used R + openair, this library has you covered for windroses, but it unfortunately lacks bivariate polar plots. In fact, I am not aware of any Python library for bivariate polar plots. Until such tools are available, you might be able to run openair within Python via [rpy2](https://github.com/rpy2/rpy2), but I haven't tested this myself.

### [cartopy](https://github.com/SciTools/cartopy)
Map-drawing functionality at a whole-Earth scale. 

### [iris](https://github.com/SciTools/iris)
Analysis and visualization for data adhering to Climate and Forecasting (CF) metadata conventions (i.e. met or geodata in NetCDF format). I have not used this library myself, but an overview of the documentation makes it clear that it would be useful for managing met or air data with strong metadata handling; in particular, I expect it would be useful for handling met or AQ forecast model outputs.

## Geographic information systems (GIS) and other geography and geometry work


### [shapely](https://github.com/shapely/shapely)


### [GeoPandas](https://github.com/geopandas/geopandas)


## Big data, higher dimensions, and other hard tasks

### [dask](https://github.com/dask/dask)


### [polars](https://github.com/pola-rs/polars)


### [xarray](https://github.com/pydata/xarray)

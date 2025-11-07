# Tools and libraries for air quality and meteorology analysis in Python

This list is not comprehensive and is continuously edited. At the moment, it is roughly sorted categorically by the type of work the library or tool pertains to. For users familiar with R, I start some passages with "**Migrating from R?**" to indicate libraries or features you might find helpful.

## Development environment
Working in Python for scientific analysis or data science would quickly become intolerable if you only wrote standalone scripts and executed them from the command line. To fix this, I suggest using one of these interactive development environments (IDEs).

### [Jupyter](https://jupyter.org/)
Jupyter is a Python development environment with files called _notebooks_, within which content is broken up into _cells_. Cells can contain code, plain text, or markdown text. This is nice if you want to create an analysis that works step-by-step and includes lots of documentation on what its doing. 

Anecdotally, most scientists and data analysts working in Python seem to be using some version of Jupyter notebooks, so it's nice to at least be familiar with the format, even if you prefer working directly on `.py` scripts instead of notebooks. Exploratory analysis in popular cloud platforms like DataBricks and MS Fabric use the Jupyter notebook format.

**Migrating from R?** Jupyter notebooks (or lab) will probably feel similar to [R Markdown](https://rmarkdown.rstudio.com/).

Finally, some other popular development environments have extensions for Jupyter, if you want something a bit more feature-rich. Two examples are VS Code and PyCharm.

### [Spyder](https://www.spyder-ide.org/)
Spyder is a bit more like a traditional IDE but is still focused on scientific computing. You work directly on Python scripts and there is less focus on pretty documentation, but interactivity and the toolset for inspecting your own analysis is a bit stronger than Jupyter notebooks. The layout and feature set of Spyder is reminiscent of MATLAB or RStudio.

**Migrating from R?** As I just mentioned, Spyder will probably feel similar to [RStudio](https://posit.co/products/open-source/rstudio/) (sans R Markdown). You can even set Spyder's layout to be the same as RStudio, so your script, interactive window, and figures are all in the right place.

## Data management
The first step in most analysis tasks is to import your data. The first library is pandas and is incredibly ubiquitous, but the second option, polars, is gaining popularity:
### [pandas](https://github.com/pandas-dev/pandas)
pandas is a versatile library for importing and manipulating tabular data. I could not reasonably list all the things pandas can do here - I encourage you to go look at its documentation. Nevertheless, some of my favourite features include:
 - Importing and exporting various tabular data formats, including CSV, parquet, Excel [and more](https://pandas.pydata.org/docs/reference/io.html). If you are migrating from other statstical software, pandas can even read SAS and SPSS files!
 - Easy indexing by row and column name, including slicing. You can slice pandas DataFrames using square brackets like an array (`df[x][y]`), but the better (and safer) `pandas.DataFrame.loc` method can handle all sorts of complicated indexing tasks.
 - Date-and-time indices, with built-in slicing, setting index frequencies, timezone and daylight savings setting and handling, and resampling. Importantly for met and AQ data, the [pandas.DataFrame.resample](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.resample.html) method lets you easily specify if the resampled indices should be right- or left-ending and labelled, which is critical for met and AQ data that is often right-side labelled and ending.
 - Function broadcasting: doing something like `df[column_name] * 2` will intuitively return the column multiplied by two element-wise. This also works with numpy and SciPy functions, so something like  `np.exp(df[column_name])` works.
 - Group-by's: you can apply calculations or methods based on groups within pandas columns, so you can calculate averages grouped by a categorical column, or binned continuous values, and even combinations of columns.
 - MultiIndexes: hierarchical indices. Obviously, this is great for complicated group-by's, hierarchical data, and mixed-effects models.

Unfortunately, pandas has some downsides. It is not always very fast, especially with quite large data, and it holds everything in memory and executes code eagerly. If you are dealing with small-to-medium data (i.e. a few hundred columns and/or up to a few million rows) this might not be a problem, and if you don't even know what _eager execution_ means, you might not need to care. If you're dealing with large data or want _lazy execution_, however, you should check out polars or the alternatives in the **Big data and other hard tasks** section below. You can also take a look at [this](https://github.com/baggiponte/awesome-pandas-alternatives) list of pandas alternatives, which goes beyond the few alternatives I mention here.

<details>
<summary>A note on avoiding unexpected behaviour in pandas</summary>
  Sometimes doing complicated indexing in pandas can lead to a value you expect to be changed, to not actually change; or vice versa. This can happen when you <a href="https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy">chain together indexing operations</a> or when you create a second DataFrame or Series that is a subset of some other DataFrame or Series, and then change the values of that subset. This can sometimes (but not always) change the original DataFrame/Series, since the subset is sometimes created as a <i>copy</i> but other times it's created as a <i>view</i> and acts like a pointer to some part of the parent DataFrame/Series. If you're doing anything that requires making subsets or copies of a DataFrame and you want to preserve the original, you should read the <a href="https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.copy.html">pandas.DataFrame.copy</a> and <a href="https://pandas.pydata.org/pandas-docs/stable/user_guide/copy_on_write.html#copy-on-write">Copy-on-Write</a> sections of the documentation.
</details>

### [polars](https://github.com/pola-rs/polars)
Polars is positioning itself as a more modern DataFrames library with better speed and other nice features that are lacking in pandas. In my work I have migrated to Polars and it appears to have all the same features as Pandas with an intuitive expression and chained-method based API. One downside for polars (or any other DataFrame alternative) is that pandas is so popular that many open-source libraries support pandas DataFrames natively (e.g. Scikit-Learn and statsmodels let you pass Pandas DataFrames to many or most methods). However, polars has a `DataFrame.to_pandas` method, so this is rarely a problem in practice.

**Migrating from R?** If you used R's data.frame or the [dplyr](https://github.com/tidyverse/dplyr) package, you should find all the functionality you need in both pandas and polars, including column creation, filtering, group-by's, etc. However, it is my sense that the API for polars might be a bit more familiar for dplyer users, with its chained methods approach, versus pandas' extensive reliance on `[]` and `.loc[]`. If you use R's built-in time-series ([ts](https://www.rdocumentation.org/packages/stats/versions/3.6.2/topics/ts)), both pandas and Polars have extensive date-and-time functionality.

## Basic math, scientific computing, and fundamental statistics
Python, like many high-level programming languages, has all the basic mathematical operations built-in (`*`, `/`, `+`, `-`, `**`), and has a fairly robust set of mathematical operations in the [math](https://docs.python.org/3/library/math.html) module. These libraries extend Python's mathematical and scientific functionality for linear algebra, statistics, and other more specific tasks.

### [numpy](https://github.com/numpy/numpy)
numpy adds a bunch of mathematical abilities to Python. First among these is support for fast N-dimensional arrays. 

numpy also adds a variety of mathematical functions, like `np.exp`, `np.log`, etc. A lot of these already exist in the standard Python library, but the numpy versions play nice with numpy arrays and other structures built off numpy arrays (including pandas DataFrames). In particular, numpy functions _broadcast_, which means they are automatically applied elementwise to arrays or DataFrames, which does not work with Python's standard library math methods.

Another problem that might not seem significant, but matters for lots of AQ and other scientific tasks, is dealing with missing values. numpy implements a type for missing data, `np.nan`, which is superior to using things like zero or an arbitrary large or small number (like -9999 or 9999) because many mathematical functions in numpy and other libraries recognize `np.nan` and either skip it or set their output to `np.nan` as well. This lets you avoid or identify problems like filtering out placeholder not-a-numbers like 9999 when trying to, say, calculate the mean of some dataset. pandas supports `np.nan` values; Polars has a more generalized `Null` value.

You will almost certainly be required to use numpy in some form because so many other scientific and numeric computing library build off of numpy, whether directly or indirectly.

### [SciPy](https://github.com/scipy/scipy)
SciPy adds a host of scientific and mathematical functionality that is not already covered by numpy and the standard library. [Browsing the documentation](https://docs.scipy.org/doc/scipy/) is better than me listing everything here, but here's a few of the modules it has: Fourier transforms, differentiation, interpolation, signal processing, special functions, and numerical optimization. SciPy works nicely with numpy and pandas, including broadcasting.

SciPy also has some statistics, including distribution functions, random sampling, and basic statistical tests and models. Some of this functionality is replicated by statsmodels, which also does a whole lot more and gives more info to the user by default.

### [statsmodels](https://github.com/statsmodels/statsmodels)
Like a lot of the other tools in this section, statsmodels has a lot of features and listing them all would take too much space. Briefly, if you are doing any sort of statistical testing or modelling, statsmodels is probably a good place to start. Statsmodels focuses on statistical inference, standing in contrast to the predictive focus on scikit-learn (see below), so you may find it useful to take a mixture of these libraries.

Certain parts of statsmodels are also very flexible. For example, in addition to common time-series models like ARIMA and ETS, the `statsmodels.tsa.statespace` class lets you create arbitrary state space models and fit them!

**Migrating from R?** If you are used to R's statistical modelling ecosystem, you will probably feel comfortable with statsmodels. While other libraries like sklearn (see below) are developed from a data science and machine learning perspective, and thus tend to focus on results and predictions, statsmodels focuses on the core statistics itself. Model results in statsmodels often permit a `.summary()` method, which prints an output similar to R's `summary()` function, and includes a host of automatically-calculated test statistics, like AIC, $R^2$, condition number, p-values, and more. Statsmodels also has a _formula API_, which permits specifying models using formulas like `y ~ a + b + a:b`, which should be familiar for R users.

## Data science and machine learning

### [scikit-learn (sklearn)](https://github.com/scikit-learn/scikit-learn)
This library is a de-facto standard for any machine learning task. It has functions and methods for data preprocessing, regression, classification, clustering, and more. As with SciPy, rather than me listing everything scikit-learn can do, you should [skim the documentation](https://scikit-learn.org/stable/). 

Users should note that where statsmodels focuses on statistical investigation, scikit-learn focuses on "predictive data analysis" (and it says so right on the front page of the docs). As an example on the difference here, when you fit a `statsmodels.regression.linear_model.OLS`, the model summary will give you a neat overview of regressed coefficients, standard errors, and various model metrics. Scikit-learn, when fitting an `sklearn.linear_model.LinearRegression`, permits extraction of coefficients (and intercept), but will not automatically calculate all the rest of that info, since the focus is on prediction over inference. On the other hand, scikit-learn has various features that statsmodels lacks, like data->preprocessing->model pipelines, clustering analysis, and cross-validation. Depending on your task, you might find yourself working with a mix of statsmodels and scikit-learn.

### [XGBoost](https://github.com/dmlc/xgboost)
XBGoost is currently one of the most popular machine learning models for tabular regression and classification problems. It is decision-tree-based, and uses a process called _gradient boosting_ to build models capable of capturing any sort of non-linearities, interactions, etc. While this is ultimately just a single type of model architecture and whether you use any model type will depend on your task, I call out this library in particular because it is popular and it needs a separate package from scikit-learn. However, it has a built-in scikit-learn-compatible API, so you can slot XGBoost models into your scikit-learn workflows with no additional work.

### [PyTorch](https://github.com/pytorch/pytorch), [TensorFlow](https://github.com/tensorflow/tensorflow), [JAX](https://github.com/jax-ml/jax), and more...
These libraries provide functionality for neural network modelling. When you dig into the details you will find that they are first libraries for fast array/tensor (numpy or otherwise) operations and automatic differentiation. Neural networks are then built on top of this foundation. If you are working with neural nets, it is probably best that you review the options or use whatever your group is using.

## Graphics and figure creation
These libraries focus on the creation of graphics or figures from data. I strongly encourage Python users to create their graphics using Python scripts. Figuring out how to tweak something just the way you want in a library's API might be less user-friendly than apps with nice user interfaces like Igor Pro or MATLAB, but if you later change your analysis, having a script or Jupyter cell produce all your figures means you only need to rerun that script or cell again. A GUI-based figure-making program usually requires you to redo everything manually.

Also, this might be a controversially-strong suggestion for some, but I urge scientists to **never** produce their figures in Excel. Practically every other figure-making solution produces nicer-looking figures, can handle larger datasets, are faster, more replicable, free, and are otherwise better for a host of other reasons. With modern free and open-source software, there is little excuse to be using Excel unless your job requires it. And if you're reading this list, you are probably interested in using Python anyways, so why not try one of these great plotting libraries?

### [matplotlib](https://github.com/matplotlib/matplotlib)
This is (anecdotally) the most popular Python plotting library. It is robust, fairly fast, and has a huge variety of options for producing good-looking figures directly in Python. It is very manipulable, and lets the user control everything. Setting up a good plotting script with matplotlib can be satisfying and save you a lot of time. You will find that a lot of other libraries with built-in plotting methods are using matplotlib under the hood (and output matplotlib objects, so you can potentially further manipulate them).

### [seaborn](https://github.com/mwaskom/seaborn)
A high-level interface for matplotlib that makes creating various types of figures faster and easier. Tasks like plots with many subplots, stacked trends, kernel density estimation plots, heatmaps, and more are a bit easier in seaborn. Take a look at their [gallery](https://seaborn.pydata.org/examples/index.html) for ideas.

### [plotly](https://github.com/plotly/plotly.py)
Plotly is a nice alternative to matplotlib that has a bit more interactivity by default. The API is a bit different too, and since API style comes down to preference, you might end up liking plotly more than matplotlib for this reason alone.

### [plotnine](https://github.com/has2k1/plotnine)
In its own words, plotnine implements a "grammar of graphics" with the intent of being similar to R's [ggplot2](https://github.com/tidyverse/ggplot2). 

**Migrating from R?** If you used ggplot2, then you might like plotnine, for obvious reasons.

### [Perceptually uniform colour maps](https://github.com/callumrollo/cmcrameri)
This isn't a plotting library, but is a tool for colourmaps that can be used alongside the above libraries. 

Fabio Crameri's scientific colour maps were developed to accurately communicate numerical data via colour without distortion due to the choice of colourmap and the way humans perceive light. [The author's argument for their superiority](https://www.nature.com/articles/s41467-020-19160-7) to other popular colourmaps is pretty convincing. I recommend everyone producing scientific or data-driven figures use these colourmaps, or at least consider the uniformity and accessibility of chosen colourmaps.

### Others
There are a lot of libraries for making visualizations in Python. A couple examples are [bokeh](https://github.com/bokeh/bokeh) and [Vega-Altair](https://github.com/vega/altair). 

## Meteorology and air quality
These libraries are specific to meteorology, air quality, or related fields. In some cases they have some overlapping functionality with other, more general libraries, so if you find you need only one or two features, it might be worth re-checking the features in the libraries you're already using.

### [windrose](https://github.com/python-windrose/windrose)
This library lets you plot windroses, a feature not available in any of the other plotting libraries mentioned here. Other libraries like matplotlib can produce polar plots, but windroses are unusual because they place zero on the north (positive $y$) axis, and they rotate/are positive in the clockwise direction; nominal polar plots by convention place zero to the east and rotate counterclockwise. windrose handles this trickiness for you, and with a little cleverness you can add windrose plots to other matplotlib figures (e.g. as a subplot).

**Migrating from R?** If you previously used R + openair, this library has you covered for windroses, but it unfortunately lacks bivariate polar plots. See the bivapp library below. Otherwise, you might be able to run openair within Python via [rpy2](https://github.com/rpy2/rpy2), but I haven't tested this myself. 

### [cartopy](https://github.com/SciTools/cartopy)
Map-drawing functionality at a whole-Earth scale. 

### [iris](https://github.com/SciTools/iris)
Analysis and visualization for data adhering to Climate and Forecasting (CF) metadata conventions (i.e. met or geodata in NetCDF format). I have not used this library myself, but an overview of the documentation makes it clear that it would be useful for managing met or air data with strong metadata handling; in particular, I expect it would be useful for handling met or AQ forecast model outputs.

### [bivapp](https://github.com/Zelpuz/bivapp)
This one is a shameless self-promotion. The windrose library lacks bivariate polar plots, so I created a simple library with a few functions for making matplotlib- and [pyGAM](https://github.com/dswah/pyGAM)-based bivariate polar plots. Due to differences in the GAM packages, these plots will look a little different than those from openair, but should ultimately serve the same purpose.

## Geographic information systems (GIS) and other geography and geometry work

### [shapely](https://github.com/shapely/shapely)
This package provides planar geometric operations. On its own it is simple and robust; in combination with a library like GeoPandas (below), you can use it for various GIS tasks.

### [GeoPandas](https://github.com/geopandas/geopandas)
GeoPandas lets you apply geographic operations to data stored in a Pandas DataFrame. Specifically, it introduces the GeoDataFrame, which is just a Pandas DataFrame with a special geometry column. This library is useful if you want to perform any kind of geospatial analysis and your data is already stored in a Pandas DataFrame. If you use ESRI's software suite, GeoPandas meshes nicely because it can [directly read shapefiles](https://geopandas.org/en/stable/docs/user_guide/io.html). GeoPandas is not feature-comparable to programs like ArcGIS, but I find it is a good tool to use in conjunction with other GIS software, especially if you have some simpler tasks that heavier-weight software might be overkill for.

## Big data, higher dimensions, and other hard tasks

### [xarray](https://github.com/pydata/xarray)
If you like the features pandas offers over plain numpy arrays like column names and indices, but you need more dimensions, xarray might fit your use-case. The documentation has a [Why xarray?](https://docs.xarray.dev/en/stable/getting-started-guide/why-xarray.html) page which provides a compelling argument for why this might be the best option in some specific use-cases. I have not used xarray myself, but I suspect that users that need more than the 2D tables offered by pandas might like it; it is based on the netCDF standard, so in geospatial use-cases already using this format, it would likely nicely fit into your workflow.

### [dask](https://github.com/dask/dask)
Parallelization and chunking for larger-than-memory dataframes. 

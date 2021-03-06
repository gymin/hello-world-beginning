# Bivariate visualisations
1. Scatter plot: Quants vs Quants
2. Clustered bar charts: Qual vs Qual
3. Heat maps: 2D histograms or bar charts 
4. Violin and Box plots: 1 Quant vs 1 Qual
5. Faceting:to adapt univariate plots to bivariate data 
6. Line plots 
```
import numpy as np 
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sb 
%matplotlib inline 
```
*Example of fuel efficienty ('fuel_econ.csv')*
```
fuel_econ = pd.read_csv('fuel_econ.csv')
print(fuel_econ.shape)
fuel_econ.head(6)
```
> The data consists of 3926 sedans manufactured between 2013 and 2108 and 20 columns 

...............................................................................................

## 1. Scatterplots for quantiativate vs quantitative variables 

```
plt.scatter(data = df, x = 'num_var1', y = 'num_var1')
```
Fuel efficiency example: relationship between the engine displacement in liters ('displ') and the overall fuel mileage in miles per gallon ('comb')
```
plt.scatter(data = fuel_econ, x = 'displ', y = 'comb'); 
```
OR; 
```
sb.regplot(data = fuel_econ, x = 'displ', y = 'comb');
```
OR, if you want to switch off the regression line, 
```
sb.regplot(data = fuel_econ, x = 'displ', y = 'comb', fit_reg = False);
plt.xlabel('Displacement (l)')
plt.ylabel('Combined Fuel Eff. (mpg)'); 
```
> The result shows an overall negative correlation

**Alternative: Seaborn**
```
sb.regplot(data = df, x = 'num_var1', y = 'num_var2')
```
> This will show a regression line too, including a shaded confidence region for the regression estimate 

If we don't want a regression line, set the `reg+fit = False` in the `regplot` function call

If there is a log(y) and x relationship, we first need to transform the data 
```
def log_trans(x, inverse = False):
    if not inverse:
        return np.log10(x)
    else:
        return np.power(10, x)

sb.regplot(df['num_var1'], df['num_var2'].apply(log_trans))
tick_locs = [10, 20, 50, 100, 200, 500]
plt.yticks(log_trans(tick_locs), tick_locs)
```

## Scatter plot adjustment  

Sometimes it might be difficult to interpret the scatter plot when there are too many points in a small area (i.e. 'overplotting')
This can be resolved with: 
*Sampling, Transparency(e.g. marking it with lighter colour, to make density judgement), Jitter(adding a small amount of random noise to the position of each point* 

### Examples of fuel efficiency and manufacturing year 
```
sb.regplot(data = fuel_econ, x = 'year', y = 'comb');
```
> The result shows a slight positive regression line, but it's not very clear and the difference seems to be made over 40mpg 

Let's use 'Jitter' 
```
sb.regplot(data = fuel_econ, x = 'year', y = 'comb', x_jitter = 0.3);
```
Add Transparency `alpha' (0 = fully transparent, 1 = fully visible)`
```
sb.regplot(data = fuel_econ, x = 'year', y = 'comb', x_jitter = 0.3, scatter_kws = {'alpha': 1/20}); 
```
But still the regression line doesn't change. We may wish to compare the data by categorising the year (this becomes qualitative, discuss later)

..........

## 2. Heat Maps: a 2d version of the histogram that can be used as an alternative to a scatter plot (height doesn't matter, but the color) - the bin size matters 

*Example of fuel efficiency*
```
plt.hist2d(data = fuel_econ, x = 'displ', y = 'comb');
plt.colorbar()
plt.xlabel('Displacement (l)')
plt.ylabel('Combined Fuel Eff. (mpg)'); 
```
> The darker color tends to be associated with the lower density but we can set it as bins with no counts will return as not a number and not get colored in ('cmin')
> then put `cmap = 'viridis_r` to reverse the color coding (e.g. darker color = higher density) and also set the bin size based on the min, max value of x and y 
```
bins_x = np.arange(0.6, 7+0.3, 0.3)
bins_y = np.arange(12, 58+3, 3)
plt.hist2d(data = fuel_econ, x = 'displ', y = 'comb', cmin = 0.5, cmap = 'viridis_r', bins = [bins_x, bins_y]);
```
*Two plot comparison*

```
plt.figure(figsize = [12, 5])

# left plot: scatterplot of discrete data with jitter and transparency
plt.subplot(1, 2, 1)
sb.regplot(data = df, x = 'disc_var1', y = 'disc_var2', fit_reg = False,
           x_jitter = 0.2, y_jitter = 0.2, scatter_kws = {'alpha' : 1/3})

# right plot: heat map with bin edges between values
plt.subplot(1, 2, 2)
bins_x = np.arange(0.5, 10.5+1, 1)
bins_y = np.arange(-0.5, 10.5+1, 1)
plt.hist2d(data = df, x = 'disc_var1', y = 'disc_var2',
           bins = [bins_x, bins_y])
plt.colorbar();
```
If you want to add annotations to cells in the plot indicating the count of points in each cell... 
`hist2d` returns a number of different variables, including an array of counts
```
bins_x = np.arange(0.5, 10.5+1, 1)
bins_y = np.arange(-0.5, 10.5+1, 1)
h2d = plt.hist2d(data = df, x = 'disc_var1', y = 'disc_var2',
               bins = [bins_x, bins_y], cmap = 'viridis_r', cmin = 0.5)
counts = h2d[0]

# loop through the cell counts and add text annotations for each
for i in range(counts.shape[0]):
    for j in range(counts.shape[1]):
        c = counts[i,j]
        if c >= 7: # increase visibility on darkest cells
            plt.text(bins_x[i]+0.5, bins_y[j]+0.5, int(c),
                     ha = 'center', va = 'center', color = 'white')
        elif c > 0:
            plt.text(bins_x[i]+0.5, bins_y[j]+0.5, int(c),
                     ha = 'center', va = 'center', color = 'black')
```                    
............................................................................................        

## 3. Violine plots for quantitative vs qualitative variables: smooth histogram
- For each level of the categorical variable, a distribution of the values on the numeric variable is plotted

*Fuel efficiency example*

First categorise the sedan class
```
sedan_classes = ['Minicompact Cars', 'Subcompact Cars', 'Compact Cars', 'Midsize Cars', 'Large Cars'] 
vclasses = pd.api.types.CategoricalDtype(ordered = True, categories = sedan_classes) 
fuel_econ['VClass'] = fuel_econ['VClass'].astype(vclasses); 

sb.violinplot(data = fuel_econ, x = 'VClass', y = 'comb'); 
```
> This will show 5 colourful violin shaped plots: you can set the color as one basic and inner as none 
```
base_color = sb.color_palette()[0]
sb.violinplot(data = fuel_econ, x = 'VClass', y = 'comb', color = base_color, inner = None); 
plt.xticks(rotation = 15);
```
The inner part is a mini Box Plot: another quant. vs. qual. plot type that takes a summary approach to displaying the data 
OR, add quarlite point inside of the violin 
```
sb.violinplot(data = fuel_econ, x = 'VClass', y = 'comb', color = base_color, inner = 'quartile'); 
```

## 4. Box plot (quants. vs. qual.)
- Compute descriptive statistics within each level and depict them graphically using a box and whiskers
- The centre of the box = media, upper and lower edges = Low and uppoer quartiles 
- Whiskers top and bottom = Max and Min value (maximum whisker length = 1.5 * IQR (Q3-Q1))
- Point = outliers outside the whiskers (if it falls outside the max. whisker length)
- Uper wisker bound = Q3 + IQR  

```
sb.boxplot(data = fuel_econ, x = 'VClass', y = 'comb', color = base_color);
```

## 5. Swan plot (quants vs. qual)
- Similar to a scatterplot, each data point is plotted with position according to its value on the two variables being plotted
- Useful if we have a small or moderate amout of data 

```
plt.figure(figsize = [12, 5])
base_color = sb.color_palette()[0]

# left plot: violin plot
plt.subplot(1, 3, 1)
ax1 = sb.violinplot(data = df, x = 'cat_var', y = 'num_var', color = base_color)

# center plot: box plot
plt.subplot(1, 3, 2)
sb.boxplot(data = df, x = 'cat_var', y = 'num_var', color = base_color)
plt.ylim(ax1.get_ylim()) # set y-axis limits to be same as left plot

# right plot: swarm plot
plt.subplot(1, 3, 3)
sb.swarmplot(data = df, x = 'cat_var', y = 'num_var', color = base_color)
plt.ylim(ax1.get_ylim()) # set y-axis limits to be same as left plot
```

## 6. Clustered bar charts for qualitative vs qualitative (e.g. multiple bar chart)

*Example of fuel efficiency: vehicle class (automatic/manual: 'trans') vs. type of vehicle transmission*

```
fuel_econ['trans_type'] = fuel_econ['trans'].apply(lambda x: x.split()[0])
```
We can use the heatmap of the categorical variable: seaborn `heatmap` 
But this takes an argument of a 2d array or DataFrame with the values to be depicpted; we need to do some summarisation beforehand 

```
ct_counts = fuel_econ.groupby(['VClass', 'trans_type']).size()
ct_counts = ct_counts.reset_index(name = 'count')
ct_counts.pivot(index = 'VClass', columns = 'trans_type', values = 'count')

sb.heatmap(ct_counts); 
## but it's difficult to interpret the color. so we can add 'annotation 'annot' to the cells, and fmt = 'd' = all counted as decimal points)
sb.heatmap(ct_counts, annot = True, fmt = 'd');

## OR we can chose a cluster bar charts
sb.countplot(data = fuel_econ, x = 'VClass', hue = 'trans_type')
plt.xticks(rotation = 15); 
```

**Default setting**

```
sb.countplot(data = df, x = 'cat_var1', hue = 'cat_var2')
## if cat_var1 has 3 values, it will show on the x axis, and three values of 'cat_var2' will be shown as 3 bars on each x point 
## if we want to change the position of the legend (e.g. from top right to low middle)
ax = sb.countplot(data = df, x = 'cat_var1', hue = 'cat_var2')
ax.legend(loc = 8, ncol = 3, framealpha = 1, title = 'cat_var2')
```

## 7. Stacked bar charts 
Pre-processing: count and sort by the number of instances of each category

```
sorted_counts = df['cat_var'].value_counts()

# establish the Figure
plt.figure(figsize = [12, 5])

# left plot: pie chart
plt.subplot(1, 2, 1)
plt.pie(sorted_counts, labels = sorted_counts.index, startangle = 90,
        counterclock = False);
plt.axis('square');

# right plot: horizontally stacked bar
plt.subplot(1, 2, 2)
baseline = 0
for i in range(sorted_counts.shape[0]):
    plt.barh(y = 1, width = sorted_counts[i], left = baseline)
    baseline += sorted_counts[i]

plt.legend(sorted_counts.index)  # add a legend for labeling
plt.ylim([0,2]) # give some vertical spacing around the bar
```

### Stacked bar charts (comparison between stacked bar charts and multiple bar charts)

```
cat1_order = ['East', 'South', 'West', 'North']
cat2_order = ['Type X', 'Type Y', 'Type Z', 'Type O']

plt.figure(figsize = [12, 5])

# left plot: clustered bar chart, absolute counts
plt.subplot(1, 2, 1)
sb.countplot(data = df, x = 'cat_var1', hue = 'cat_var2',
             order = cat1_order, hue_order = cat2_order)
plt.legend()

# right plot: stacked bar chart, absolute counts
plt.subplot(1, 2, 2)

baselines = np.zeros(len(cat1_order))
# for each second-variable category:
for i in range(len(cat2_order)):
    # isolate the counts of the first category,
    cat2 = cat2_order[i]
    inner_counts = df[df['cat_var2'] == cat2]['cat_var1'].value_counts()
    # then plot those counts on top of the accumulated baseline
    plt.bar(x = np.arange(len(cat1_order)), height = inner_counts[cat1_order],
            bottom = baselines)
    baselines += inner_counts[cat1_order]

plt.xticks(np.arange(len(cat1_order)), cat1_order)
plt.legend(cat2_order)
```

### Compound bar charts (stacked bar charts based on %)

```
cat1_order = ['East', 'South', 'West', 'North']
cat2_order = ['Type X', 'Type Y', 'Type Z', 'Type O']

artists = [] # for storing references to plot elements
baselines = np.zeros(len(cat1_order))
cat1_counts = df['cat_var1'].value_counts()

# for each second-variable category:
for i in range(len(cat2_order)):
    # isolate the counts of the first category,
    cat2 = cat2_order[i]
    inner_counts = df[df['cat_var2'] == cat2]['cat_var1'].value_counts()
    inner_props = inner_counts / cat1_counts
    # then plot those counts on top of the accumulated baseline
    bars = plt.bar(x = np.arange(len(cat1_order)),
                   height = inner_props[cat1_order],
                   bottom = baselines)
    artists.append(bars)
    baselines += inner_props[cat1_order]

plt.xticks(np.arange(len(cat1_order)), cat1_order)
plt.legend(reversed(artists), reversed(cat2_order), framealpha = 1,
           bbox_to_anchor = (1, 0.5), loc = 6);
```
           
## 8. Faceted plot (Faceting)
- Data is divided into disjoint subsets, most often by different levels of a caegorical variable (creating multiple histograms per value)

```
g = sb.FacetGrid(data = fuel_econ, col = 'VClass'); 
```
> Each facets will be created per value of VClass (in this case total 5 values) 

```
bins = np.arange(12, 58+2, 2)
g = sb.FacetGrid(data = fuel_econ, col = 'VClass', col_wrap = 3, sharey = False); ## col_wrap = 3: 3 graphs per row, sharey = false: each graph has its own x and y axis
g.map(plt.hist, 'comb', bins = bins);   ## bins = bins: all graphs are drawn on the same x, y axis 
```

*Extra example: many_cat_var has 15 levels (the following will show 15 graphs, 5 in rows for 3 rows)*
```
group_means = df.groupby(['many_cat_var']).mean()
group_order = group_means.sort_values(['num_var'], ascending = False).index

g = sb.FacetGrid(data = df, col = 'many_cat_var', col_wrap = 5, size = 2,
                 col_order = group_order)
g.map(plt.hist, 'num_var', bins = np.arange(5, 15+1, 1))
g.set_titles('{col_name}')
```

## 9. Adapted bar charts (instead of Box Plot) 

```
base_color = sb.color_palette()[0]
sb.barplot(data = fuel_econ, x = 'VClass', y = 'comb', color = base_color); 
plt.xticks(rotation = 15);
plt.ylabel('Avg. Combined Fuel Eff. (mpg)');
```
> This will show a bar chart with a black line on top of the bar, indicating the mean value 

If we want to show the line graph that connects the mean point, and a vertical line that shows the standard deviation (sd)...
```
sb.pointplot(data = fuel_econ, x = 'VClass', y = 'comb', ci = 'sd', linestyles = "") ## linestyle is empty: it will only show a vertical line 
```

## 10. Adapted histograms 
If we want to change the 'weights' parameter (instead of each data point given a weight of 1) 

```
bin_edges = np.arange(0, df['num_var'].max()+1/3, 1/3)

# count number of points in each bin
bin_idxs = pd.cut(df['num_var'], bin_edges, right = False, include_lowest = True,
                  labels = False).astype(int)
pts_per_bin = df.groupby(bin_idxs).size()

num_var_wts = df['binary_out'] / pts_per_bin[bin_idxs].values

# plot the data using the calculated weights
plt.hist(data = df, x = 'num_var', bins = bin_edges, weights = num_var_wts)
plt.xlabel('num_var')
plt.ylabel('mean(binary_out)')
```

To get the mean of the y-variable ("binary_out") in each bin, the weight of each point should be equal to the y-variable value, divided by the number of points in its x-bin (`num_var_wts`). 
As part of this computation, we make use of pandas' cut function in order to associate each data point to a particular bin (`bin_idxs`). 
The `labels = False` parameter means that each point's bin membership is associated by a numeric index, rather than a string. 
We use these numeric indices into the pts_per_bin, with the .values at the end necessary in order for the Series' indices to not be confused between the indices of df['binary_out'].

## 11. Line plots 
- Useful if we want relative change of the values, and trends across x-values 

### Examples: Time Series Plot 
(e.g. pointplot: useful if we are dealing with qualitative vs. quantitative, Line plots: useful for two quants. variables)

```
plt.errorbar(data = fuel_econ, x = 'displ', y = 'comb');
```
> This will connect all points in line, so will look quite messy because it's like a scatter plot but linking all lines 
> We can use this error bar if all of our data is sorted by the x variable and we only have one y value for each x value 
> If not, we have to arrange the data in that form, first setting bin edges and bin centres 

```
bins_e = np.arange(0.6, 7+0.2, 0.2)
bins_c = bins_e[:-1] + 0.1 # these centre values make sure that the points are plotted in their accurate positions. 
# and leaving out the last value of the bin edges [:-1], since that won't correspond to an actual bin centre
```

Then use pandas `cut` function to figure out which bin each data points should be used in 
> First argument: slcing that's using, second argument: the set of bins, 3rd argument: makes sure that the values = the mean get included into the bins

```
displ_binned = pd.cut(fuel_econ['displ'], bins_e, include_loweset = True)
comb_mean = fuel_econ['comb'].groupby(displ_binned).mean()
comb_std = fuel_econ['comb'].groupby(displ_binned).std()

plt.errorbar(x = bins_c, y = comb_mean, yerr = comb_std);
plt.xlabel('Displacement (l)')
plt.ylabel('Avg. Combined Fuel Eff. (mpg)'); 
```
When there is a gap in line, we can also calculate the standard deviation and plot them through the yerr parameter
> Findings show that the cars with smaller engine sizes have generally higher fuel efficiency 

**Default setting** 

```
# set bin edges, compute centers
bin_size = 0.25
xbin_edges = np.arange(0.5, df['num_var1'].max()+bin_size, bin_size)
xbin_centers = (xbin_edges + bin_size/2)[:-1]

# compute statistics in each bin
data_xbins = pd.cut(df['num_var1'], xbin_edges, right = False, include_lowest = True)
y_means = df['num_var2'].groupby(data_xbins).mean()
y_sems = df['num_var2'].groupby(data_xbins).sem()

# plot the summarized data
plt.errorbar(x = xbin_centers, y = y_means, yerr = y_sems)
plt.xlabel('num_var1')
plt.ylabel('num_var2')
```

## 12. Alternate variations: Rolling 
Instead of computing summary statistics on fixed bins, make computations on a rolling window through use of pandas' `rolling` method, making computations on sequential rows of the dataframe, by using 'sort_values' to put the x values in ascending order first 

```
# compute statistics in a rolling window
df_window = df.sort_values('num_var1').rolling(15)
x_winmean = df_window.mean()['num_var1']
y_median = df_window.median()['num_var2']
y_q1 = df_window.quantile(.25)['num_var2']
y_q3 = df_window.quantile(.75)['num_var2']

# plot the summarized data
base_color = sb.color_palette()[0]
line_color = sb.color_palette('dark')[0]
plt.scatter(data = df, x = 'num_var1', y = 'num_var2')
plt.errorbar(x = x_winmean, y = y_median, c = line_color)
plt.errorbar(x = x_winmean, y = y_q1, c = line_color, linestyle = '--')
plt.errorbar(x = x_winmean, y = y_q3, c = line_color, linestyle = '--')

plt.xlabel('num_var1')
plt.ylabel('num_var2')
```

> This will create multiple line plots (3 plots, median, Q1 and Q2)

## 13. Alternate variations: FacetGrid 
- Multiple hist calls, but with lines only 

```
bin_edges = np.arange(-3, df['num_var'].max()+1/3, 1/3)
g = sb.FacetGrid(data = df, hue = 'cat_var', size = 5)
g.map(plt.hist, "num_var", bins = bin_edges, histtype = 'step')
g.add_legend()
```

OR make a frequency polygon 

```
def freq_poly(x, bins = 10, **kwargs):  ## **kwargs: used to allow addtional keyword arguments to be set for the 'errorbar' function 
    """ Custom frequency polygon / line plot code. """
    # set bin edges if none or int specified
    if type(bins) == int:
        bins = np.linspace(x.min(), x.max(), bins+1)
    bin_centers = (bin_edges[1:] + bin_edges[:-1]) / 2

    # compute counts
    data_bins = pd.cut(x, bins, right = False,
                       include_lowest = True)
    counts = x.groupby(data_bins).count()

    # create plot
    plt.errorbar(x = bin_centers, y = counts, **kwargs)

bin_edges = np.arange(-3, df['num_var'].max()+1/3, 1/3)
g = sb.FacetGrid(data = df, hue = 'cat_var', size = 5)
g.map(freq_poly, "num_var", bins = bin_edges)
g.add_legend()
```

## 14. Ridgeline plots: a series of vertically faceted line plots or density curves, but with somewhat overlapping y-axes
- Faceting but instead of a histogram, it's with line plots 

### Faceting 

```
group_means = df.groupby(['many_cat_var']).mean()
group_order = group_means.sort_values(['num_var'], ascending = False).index

g = sb.FacetGrid(data = df, col = 'many_cat_var', col_wrap = 5, size = 2,
                 col_order = group_order)
g.map(plt.hist, 'num_var', bins = np.arange(5, 15+1, 1))
g.set_titles('{col_name}')
```

### Ridgeline 

```
group_means = df.groupby(['many_cat_var']).mean()
group_order = group_means.sort_values(['num_var'], ascending = False).index

g = sb.FacetGrid(data = df, row = 'many_cat_var', size = 0.75, aspect = 7,
                 row_order = group_order)
g.map(sb.kdeplot, 'num_var', shade = True)
g.set_titles('{row_name}')
```

### Stacking ridgeline for a uni-dimensional comparison 

```
group_means = df.groupby(['many_cat_var']).mean()
group_order = group_means.sort_values(['num_var'], ascending = False).index

# adjust the spacing of subplots with gridspec_kws
g = sb.FacetGrid(data = df, row = 'many_cat_var', size = 0.5, aspect = 12,
                 row_order = group_order, gridspec_kws = {'hspace' : -0.2})
g.map(sb.kdeplot, 'num_var', shade = True)

# remove the y-axes
g.set(yticks=[])
g.despine(left=True)

g.set_titles('{row_name}')
```

Revised version of stacking ridgeline for a uni-dimensional comparison (each has axis)

```
group_means = df.groupby(['many_cat_var']).mean()
group_order = group_means.sort_values(['num_var'], ascending = False).index

g = sb.FacetGrid(data = df, row = 'many_cat_var', size = 0.5, aspect = 12,
                 row_order = group_order, gridspec_kws = {'hspace' : -0.2})
g.map(sb.kdeplot, 'num_var', shade = True)

g.set(yticks=[])
g.despine(left=True)

# set the transparency of each subplot to full
g.map(lambda **kwargs: plt.gca().patch.set_alpha(0))

# remove subplot titles and write in new labels
def label_text(x, **kwargs):
    plt.text(4, 0.02, x.iloc[0], ha = 'center', va = 'bottom')
g.map(label_text, 'many_cat_var')
g.set_xlabels('num_var')
g.set_titles('')
```

## 15. Q-Q plots (Quantile-Quantile plot)
- To see how closely your numeric data follows some hypothetical distribution. 
- This might be important for certain parametric statistical tests, like checking for assumptions of normality.
- To make a visual comparison between your data and your reference distribution.

```
# create a histogram of the data
bin_size = 0.5
bin_edges = np.arange(4, 18 + bin_size, bin_size)
plt.hist(data = df, x = 'num_var', bins = bin_edges);

# overlay a theoretical normal distribution on top
samp_mean = df['num_var'].mean()
samp_sd = df['num_var'].std()

from scipy.stats import norm
x = np.linspace(4, 18, 200)   # to generate x-values across the range of the plot. 
                              # Note that the first two arguments match the bin_edges limits, while the third argument specifies the number of values to generate between the two endpoints.
y = norm.pdf(x, samp_mean, samp_sd) # normal distribution heights at those x values # pdf: probability density function, which returns the normal distribution height (density) at each value of x
y *= df.shape[0] * bin_size # scale the distribution height

plt.plot(x, y)
```

> The output shows the original data in histogram and a normal distribution curve 

Q-Q plot when the data distribution does not fit the normal distribution assumption 

```
# generate the data
np.random.seed(8322489)

n_points = 120
unif_data = np.random.uniform(0, 10, n_points)

# set up the figure
plt.figure(figsize = [12, 5])

# left subplot: plot the data
plt.subplot(1, 2, 1)
bin_size = 0.5
bin_edges = np.arange(0, 10 + bin_size, bin_size)
plt.hist(x = unif_data, bins = bin_edges);

# overlay a theoretical normal distribution on top
samp_mean = unif_data.mean()
samp_sd = unif_data.std()

from scipy.stats import norm
x = np.linspace(0, 10, 200)
y = norm.pdf(x, samp_mean, samp_sd) # normal distribution heights
y *= n_points * bin_size # scale the distribution height
plt.plot(x, y)

# right subplot: create a Q-Q plot
plt.subplot(1, 2, 2)

qs = (np.arange(n_points) - .5) / n_points
expected_scores = norm.ppf(qs)
data_scores = (np.sort(unif_data) - samp_mean) / samp_sd

plt.scatter(expected_scores, data_scores)
plt.plot([-2.5,2.5],[-2.5,2.5],'--', color = 'black')
plt.axis('equal')
plt.xlabel('Expected Standard Scores')
plt.ylabel('Observed Standard Scores')
```

## 16. Extra plots 

### Rug plot: all of the data points are plotted on a single axis, one tick mark or line for each one.

```
g = sb.JointGrid(data = df, x = 'num_var1', y = 'num_var2')
g.plot_joint(plt.scatter)
g.plot_marginals(sb.rugplot, height = 0.25)  ## height = height of rug ticks 
```

### Strip plot: similar to swan plot, but without any dodging or jittering to keep points separate or off the categorical line
- A rug plot faceted by categorical levels

```
plt.figure(figsize = [10, 5])
base_color = sb.color_palette()[0]

# left plot: strip plot
plt.subplot(1, 2, 1)
ax1 = sb.stripplot(data = df, x = 'num_var', y = 'cat_var',
                   color = base_color)

# right plot: violin plot with inner strip plot as lines
plt.subplot(1, 2, 2)
sb.violinplot(data = df, x = 'num_var', y = 'cat_var', color = base_color,
             inner = 'stick')
```


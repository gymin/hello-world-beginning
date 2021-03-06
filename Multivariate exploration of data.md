# Plotting of 3 or more variables, e.g.  
- Three numeric variables 
- Two numeric variables and one categorical variable 
- One numeric variable and two categorical variables 
- Three categorical variables 

## How: using 
+ Shape for qualitateive data
+ Size for quantitative data (e.g. value proportional to radius or area) 
+ Color for qualitative and quantitative data 

## Other types 
* Faceting for multivariate data
* Plot matrices 
* Feature engineering 

........................................................................................................

## 1. Encoding via Shape 
```
cat_markers = [['A', 'o'],   ## 'o': circular marker
               ['B', 's']]   ## 's': square marker 

for cat, marker in cat_markers:
    df_cat = df[df['cat_var1'] == cat]
    plt.scatter(data = df_cat, x = 'num_var1', y = 'num_var2', marker = marker)
plt.legend(['A','B'])
```

## 2. Encoding via Size 
```
plt.scatter(data = df, x = 'num_var1', y = 'num_var2', s = 'num_var3')
```

Dummy series for adding legend
```
sizes = [20, 35, 50]
base_color = sb.color_palette()[0]
legend_obj = []
for s in sizes:
    legend_obj.append(plt.scatter([], [], s = s, color = base_color))
plt.legend(legend_obj, sizes)
```

**A warning on Combining Encodings** 
The code to depict the plot and a reasonable legend gets complicated 
Point areas won't all be the same even with the same value, depdending on the shape of the marker 

```
plt.figure(figsize = [5,5])

common_size = 2500
plt.scatter([0],[1], marker = 's', s = common_size)
plt.scatter([1],[1], marker = '^', s = common_size)
plt.scatter([0],[0], marker = 'o', s = common_size)
plt.scatter([1],[0], marker = 'X', s = common_size)

# a little bit of aesthetic cleaning
plt.xlim(-0.5,1.5)
plt.xticks([])
plt.ylim(-0.5,1.5)
plt.yticks([])
```

## 3. Encoding via Color 
### Qualitative variable 
: You can set different colors for different levels of a categorica varialbe through the `hue` parameter on seaborn's FacetGrid class 

```
g = sb.FacetGrid(data = df, hue = 'cat_var1', size = 5)
g.map(plt.scatter, 'num_var1', 'num_var2')
g.add_legend()
```

### Quantitative variable
: We can set color based on numeric value in the 'scatter' function through the `c` parameter
```
plt.scatter(data = df, x = 'num_var1', y = 'num_var2', c = 'num_var3')
plt.colorbar()
```

**Color palettes**

_Qualitataive palettes for nominal-type data_
```
sb.palplot(sb.color_palette(n_colors=9))
```
_Sequntial palette for ordinal and numeric-type data_
```
sb.palplot(sb.color_palette('viridis', 9))
```
_Diverging palette when there is a meaningful zero or center value for the variable_
```
sb.palplot(sb.color_palette('vlag', 9))
```

Setting the color palette: in `FacetGrid` through the `palette` parameter, OR in `scatter` through the `cmap` parameter
```
g = sb.FacetGrid(data = df, hue = 'cat_var1', size = 5,
                 palette = 'colorblind')
g.map(plt.scatter, 'num_var1', 'num_var2')
g.add_legend()

# OR 
plt.scatter(data = df, x = 'num_var1', y = 'num_var2', c = 'num_var3',
            cmap = 'mako_r')
plt.colorbar()
```

Be aware of the effect of transparency and overlap on interpretability 
To be safe, either avoid or minimizse transparency in plots with color, OR plot only a sample of your points in order to make sure that the effect of the 3rd variable is clearly visible 

```
plt.figure(figsize = [5,5])

# left: qualitative points
plt.scatter(0,0.5,s = 1e4, c = sb.color_palette()[0], alpha = 0.5)
plt.scatter(0,-0.5,s = 1e4, c = sb.color_palette()[1], alpha = 0.5)

# right: quantitative points
plt.scatter(1,0.5,s = 1e4, c = sb.color_palette('Blues')[2], alpha = 0.5)
plt.scatter(1,-0.5,s = 1e4, c = sb.color_palette('Blues')[4], alpha = 0.5)

# set axes for point overlap
plt.xlim(-0.5,1.5)
plt.ylim(-3.5,3.5)
plt.xticks([])
plt.yticks([])
```

#### Example of fuel efficiency (fule_econ) and pokemon data

``` 
import numpy as np
import pandas as pd 
import matplotlib.pyplot as plt
import seaborn as sb 
%matplotlib inline 
```

**Fuel_econ: a scatter plot of fuel mileage (displ) against engine size (comb)**
```
np.random.seed(2018) 
sample = np.random.choice(fuel_econ.shape[0], 200, replace = False)
fuel_econ_subset = fuel_econ.loc[sample] 

sb.regplot(data = fuel_econ_subset, x = 'displ', y = 'comb', 
            x_jitter = 0.04, fit_reg = False);
plt.xlabel('Displacement (l)')
plt.ylabel('Combined Fuel Eff. (mpg)'); 
```
Now add 'transmission' type: first set up the list 
```
ttype_markers = [['Automatic', 'o'], ['Manual', '^']]

for ttype, marker in ttype_markers:
      plot_data = fuel_econ_subset.loc[fule_econ_subset['trans_type'] == ttype]
      sb.regplot(data = plot_data, x = 'displ', y = 'comb', 
                 x_jitter = 0.04, fit_reg = False, marker = marker);
plt.xlabel('Displacement (l)')
plt.ylabel('Combined Fuel Eff. (mpg)'); 
plt.legent(['Automatic', 'Manual']); 
```
How about adding 'CO2'? (Quantitative data): Use Marker Sizes 
```
sb.regplot(data = fuel_econ_subset, x = 'displ', y = 'comb', 
            x_jitter = 0.04, fit_reg = False, 
            scatter_kws = {'s': fuel_econ_subset['co2']}); 
plt.xlabel('Displacement (l)')
plt.ylabel('Combined Fuel Eff. (mpg)'); 
```
If we want to reduce the marker size to half....
```
sb.regplot(data = fuel_econ_subset, x = 'displ', y = 'comb', 
            x_jitter = 0.04, fit_reg = False, 
            scatter_kws = {'s': fuel_econ_subset['co2']/2}); 
plt.legend([size?]) 
```
Legend is triker so we can set the loop
```
sb.regplot(data = fuel_econ_subset, x = 'displ', y = 'comb', 
            x_jitter = 0.04, fit_reg = False, 
            scatter_kws = {'s': fuel_econ_subset['co2']/2}); 
plt.xlabel('Displacement (l)')
plt.ylabel('Combined Fuel Eff. (mpg)'); 

sizes = [200, 350, 500]  ## size legend reference 
base_color = sb.color-palette()[0]
legend_obj = []
for s in sizes:
    legend_obj.append(plt.scatter([],[], s=s/2, color = base-color))
plt.legend(legend_obj, sizes, title = 'CO2 (g/mi)');
# first one is the one depicted in the legend, second: a list of labels
```

Add a Qualitative data wiht color (FacetGrid)
```
g = sb.FacetGrid(data = fuel_econ_subset, hue = 'trans_type', 
                 hue_order = ['Automatic', 'Manual'], size = 4, aspect = 1.5)
g.map(sb.regplot, 'displ', 'comb', x_jitter = 0.04, fit_reg = False);
g.add_legend()
plt.xlabel('Displacement (l)')
plt.ylabel('Combined Fuel Eff. (mpg)'); 
```

Add an Ordinal data (VClass): viridis_r 
```
g = sb.FacetGrid(data = fuel_econ_subset, hue = 'VClass', 
                 size = 4, aspect = 1.5, palette = 'viridis_r')
g.map(sb.regplot, 'displ', 'comb', x_jitter = 0.04, fit_reg = False);
g.add_legend()
plt.xlabel('Displacement (l)')
plt.ylabel('Combined Fuel Eff. (mpg)'); 
```

Add a numerical data (CO2): Scatter 
```
plt.scatter(data = fuel_econ_subset, x = 'displ', y = 'comb', c = 'co2',
             cmap = 'viridis-r')
plt.colorbar(label = 'CO2 (g/mi)')
plt.xlabel('Displacement (l)')
plt.ylabel('Combined Fuel Eff. (mpg)'); 
```
....................................................................................................

## Faceting for multivariate data 
: FacetGrid can be used to subset your dataset across levels of a categorical variable, and then create one plot for each subset 

```
g = sb.FacetGrid(data = df, col = 'cat_var1', size = 4)
g.map(sb.boxplot, 'cat_var2', 'num_var2')
```
> The oputput will show two separate box plots for cat_var1=a, and cat_var1=b, and x as cat_var2 and y as num_var2

If there are more varialbes (e.g. in cat_var2 = low, medicum, high / cat_var1 = a, b) 
```
g = sb.FacetGrid(data = df, col = 'cat_var2', row = 'cat_var1', size = 2.5,
                margin_titles = True)
g.map(plt.scatter, 'num_var1', 'num_var2')
```
> This will create a 6 scatter plots x as num_var1 and y as num_var2

_Example of fuel efficiency data_ 
```
g = sb.FacetGrid(data = feul_econ, col = 'VClass', rows = 'trans_type', margine_titles = True)
g.map(plt.scatter, 'displ', 'comb'); 
```

## Other adaptation 
### Substituting count on a bar chart or histogram for the mean, median, or some other statistic of a second variable 
: If we want to depict the mean of a thrid variable in a 2D histogram, we need to change the weights of points in the `hist2d` function

```
xbin_edges = np.arange(0.25, df['num_var1'].max()+0.5, 0.5)
ybin_edges = np.arange(7,    df['num_var2'].max()+0.5, 0.5)
```
Count number of points in each bin
```
xbin_idxs = pd.cut(df['num_var1'], xbin_edges, right = False,
                    include_lowest = True, labels = False).astype(int)
ybin_idxs = pd.cut(df['num_var2'], ybin_edges, right = False,
                    include_lowest = True, labels = False).astype(int)

pts_per_bin = df.groupby([xbin_idxs, ybin_idxs]).size()
pts_per_bin = pts_per_bin.reset_index()
pts_per_bin = pts_per_bin.pivot(index = 'num_var1', columns = 'num_var2').values

z_wts = df['num_var3'] / pts_per_bin[xbin_idxs, ybin_idxs]

# plot the data using the calculated weights
plt.hist2d(data = df, x = 'num_var1', y = 'num_var2', weights = z_wts,
           bins = [xbin_edges, ybin_edges], cmap = 'viridis_r', cmin = 0.5);
plt.xlabel('num_var1')
plt.ylabel('num_var2');
plt.colorbar(label = 'mean(num_var3)');
```

### The heatmap if there is a lot of data to be aggregated 

```
cat_means = df.groupby(['cat_var1', 'cat_var2']).mean()['num_var2']
cat_means = cat_means.reset_index(name = 'num_var2_avg')
cat_means = cat_means.pivot(index = 'cat_var2', columns = 'cat_var1',
                            values = 'num_var2_avg')
sb.heatmap(cat_means, annot = True, fmt = '.3f',
           cbar_kws = {'label' : 'mean(num_var2)'})
```

### Clustered bar chart for 2 categorical variables and 1 numercial variable 

```
ax = sb.barplot(data = df, x = 'cat_var1', y = 'num_var2', hue = 'cat_var2')
ax.legend(loc = 8, ncol = 3, framealpha = 1, title = 'cat_var2')
```

### `Hue` parameter in the boxplot, volin plot, pointplot
: `dodge` parameter to shift the levels in a clustered fashion 
```
ax = sb.pointplot(data = df, x = 'cat_var1', y = 'num_var2', hue = 'cat_var2',
                  dodge = 0.3, linestyles = "")
```

### Lineplot (frequency polygon)
```
def mean_poly(x, y, bins = 10, **kwargs):
    """ Custom adapted line plot code. """
    # set bin edges if none or int specified
    if type(bins) == int:
        bins = np.linspace(x.min(), x.max(), bins+1)
    bin_centers = (bin_edges[1:] + bin_edges[:-1]) / 2

    # compute counts
    data_bins = pd.cut(x, bins, right = False,
                       include_lowest = True)
    means = y.groupby(data_bins).mean()

    # create plot
    plt.errorbar(x = bin_centers, y = means, **kwargs)

bin_edges = np.arange(0.25, df['num_var1'].max()+0.5, 0.5)
g = sb.FacetGrid(data = df, hue = 'cat_var2', size = 5)
g.map(mean_poly, "num_var1", "num_var2", bins = bin_edges)
g.set_ylabels('mean(num_var2)')
g.add_legend()
```

#### Example of fuel efficiency 

**Pointplot**
```
sb.pointplot(data = fuel_econ, x = 'VClass', y = 'comb', hue = 'trans_type', 
              ci = 'sd', linestyles = "", dodge = True); ## dodge to offset the two levels slightly 
plt.xticks(rotation = 15)
plt.ylabel('Avg.combined efficiency (mpg)'); 
```
**Bar plot** 
```
sb.barlot(data = fuel_econ, x = 'VClass', y = 'comb', hue = 'trans_type', 
              ci = 'sd');
plt.xticks(rotation = 15)
plt.ylabel('Avg.combined efficiency (mpg)'); 
```
**Heat map** 
```
bins_x = np.arange(0.6, 7+0.3, 0.3)
bins_y = np.arange(12, 58+3, 3)
plt.hist2d(data = fuel_econ, x = 'displ', y = 'comb', cmin = 0.5, 
           cmpa = 'virids_r', bins = [bins_x, bins_y], weights = co2_weights);
plt.xlabel('Displacement (l)')
plt.ylabel('Combined Fuel Eff. (mpg)');
plt.colorbar(label = 'CO2 (g/mi)');
```
Set et co2_weights
```
disl_bins = pd.cut(fuel_econ['displ'], bins_x, right = False, 
                   include_lowest = False, labels = False).astype9int)
comb_bins = pd.cut(fuel_econ['comb'], bins_y, right = False, 
                   include_lowest = False, labels = False).astype9int)

n_points = fuel_econ.groupby([displ_bins, comb_bins]).size()
n_points = n_points.reset_index().pivot(index = 'displ', columns = 'comb').values

n_points 
```
> This output will show a n array of values 
```
co2_weights = fuel_econ['co2'] / n_points[displ_bins, comb_bins]
```

## Plot matrices
: Each row and column represents a different variable, and a subplot against those variables generated in each plot matrix cell 

### Seaborn's PairGrid 
```
g = sb.PairGrid(data = df, vars = ['num_var1', 'num_var2', 'num_var3'])
g.map_diag(plt.hist)
g.map_offdiag(plt.scatter
```
By default, PairGrid only expets to depict numeric variables
But if we want to look at the relationship between the numeric and categorical variables in the data, we need to set the different variable types on the rows and columns, then use an appropriate plot type for all matrix cells 

```
g = sb.PairGrid(data = df, x_vars = ['num_var1', 'num_var2', 'num_var3'],
                y_vars = ['cat_var1','cat_var2'])
g.map(sb.violinplot, inner = 'quartile')
```

### Correlation matrices (for numeric variables)
```
sb.heatmap(df.corr(), annot = True, fmt = '.2f', cmap = 'vlag_r', center = 0)
```
Instead of using the default swquential color map, a diverging color map is specified asnd its center is set to 0 (to see if it's postive or negative)


**Example of pokemon data (test six combat statistics for all pokemon)**

First specify the variables of interest on the vars parameter (without this, it will plot all numerica variables in the dataset)
```
pkmn_stats = ['hp', 'attack', 'defense', 'speed', 'special-attack', 'special-defense']
g= sb.PairGrid(data = pokemon, vars = pkmn_stats);
g= g.map_offdiag(plt.scatter); 
g.map_diag(plt.hist);
```

Check the correlation using a heatmap
```
sb.hetmap(pokemon[pkmn_stats].corr(), cmap = 'rocket_r', annot = True, fmt = '.2f', vmin = 0); 
```

## Feature Engineering (creating a new variable with a sum, difference, product or ratio between 2 original variables) 

**Example of pokemon (biases of pokemon features)**

First create new variables, 'attack ratio' and 'defense ratio'
```
pokemon['atk_ratio'] = pokemon['attack'] / pokemon['special-attack']
pokemon['def_ratio'] = pokemon['defense'] / pokemon['special-defense']

plt.scatter(data = pokemon, x = 'atk_ratio', y = 'def_ratio', alpha = 1/23
plt.xlabel('Offensive Bias (Phys/Spec)')
plt.ylable('Defensive Bias (Phys/Spec)');
plt.xscale('log')
plt.yscale('log'); 
tick_loc = [0.25, 0.5, 1, 2, 4]
plt.xticks(tick_loc, tick_loc)
plt.yticks(tick_loc, tick_loc); 
plt.xlim(2 ** -2.5, 2 ** 2.5)
plt.ylim(2 ** -2.5, 2 ** 2.5); 
```

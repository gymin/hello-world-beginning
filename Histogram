How to create a histogram (quantitative dta): back to pokemon case

bins = np.arange(0, pokemon['spped'].max()+5, 5) 
## first argumnet: min value, second argument: max, but we added +5 otherwise it will not include the actual max. value). 
## third argument: step-size for the bins (bins size) 
plt.hist(data = pokemon, x = 'speed', bins = bins); 
## output: based on basic bin range, so you can set up yours (e.g. bins)

## OR 
sb.distplot(pokemon['speed']); 
## ouptut shows a histogram and line starting from -25 to 175 
## 2 differences: 1. the default bin count is larger than in matplotlib, 
##                2. line plotting, but y value is a lot smaller, because the line is a density curve estimates of the data distribution
## you can turn off the density line by adding 'kde = False' at the end like below.. 
sb.distplot(pokemon['speed'], kde = False) 

# Default setting (Matplot)
plt.hist(data = df, x = 'num_var')
## but this divides the data into 10 bins, based on the range of values taken, but usulally having only 10 bins is too few to really understand the distribution 
## you can first check the min. and max value by using df['num_var'].describe(), then 
bin_edges = np.arange(0, df['num_var'].max()+1, 1)  ## arrange(min, max, bin_width)
plt.hist(data = df, x = 'num_var', bins = bin_edges)

plt.figure(figsize = [10, 5]) # larger figure size for subplots

# histogram on left, example of too-large bin size
plt.subplot(1, 2, 1) # 1 row, 2 cols, subplot 1  ## you can put two plots side by side through use of the subplot 
bin_edges = np.arange(0, df['num_var'].max()+4, 4)
plt.hist(data = df, x = 'num_var', bins = bin_edges)

# histogram on right, example of too-small bin size
plt.subplot(1, 2, 2) # 1 row, 2 cols, subplot 2
bin_edges = np.arange(0, df['num_var'].max()+1/4, 1/4)
plt.hist(data = df, x = 'num_var', bins = bin_edges)

# Default (Seaborn)
sb.distplot(df['num_var'])
## this has built-in rules for specifying histogram bins, and by default plots a curve depicting the kernel density estimate (KDE) on top of the data
## the vertical axis is based on the KDE, rather than the histogram 
## to show only a histogram 
bin_edges = np.arange(0, df['num_var'].max()+1, 1)
sb.distplot(df['num_var'], bins = bin_edges, kde = False,
            hist_kws = {'alpha' : 1})  ## alpha= transparency setting 

## Seaborn Extra (how to create only a line curve based on KDE)
data = [0.0, 3.0, 4.5, 8.0]
plt.figure(figsize = [12, 5])

# left plot: showing kde lumps with the default settings
plt.subplot(1, 3, 1)
sb.distplot(data, hist = False, rug = True, rug_kws = {'color' : 'r'})

# central plot: kde with narrow bandwidth to show individual probability lumps
plt.subplot(1, 3, 2)
sb.distplot(data, hist = False, rug = True, rug_kws = {'color' : 'r'},
            kde_kws = {'bw' : 1})

# right plot: choosing a different, triangular kernel function (lump shape)
plt.subplot(1, 3, 3)
sb.distplot(data, hist = False, rug = True, rug_kws = {'color' : 'r'},
            kde_kws = {'bw' : 1.5, 'kernel' : 'tri'})
            
...................................................................................

# Extra: how subplots work 

## the base of a visualisation in matplotlib is a Figure object, following the three steps 
fig = plt.figure()  ## python, create an area for a figure to plot inside 
ax = fig.add_axes([.125, .125, .755, .755])  ## create axes: first two (the position of the lower-left hand corner of the axes: where x and y meet), last two (axes width and height)
ax.hist(data = df, x = 'num_var') ## now plot the data inside that axes
## for Seaborn, the last one would be... 
sb.countplot(data = df, x = 'num_var', ax = ax)

## if you don't assign Axes objects as they're created,you can retrieve the current Axes using, 'ax = plt.gca()'
## OR you can get a list of all Axes in a figure 'fig' by using 'axes = fig.get_axes()
## As for creating subplots, you can use 'fig.add_suplot()' in the same way as 'plt.subplot()' above 
fig, axes = plt.subplots(3, 4) # grid of 3x4 subplots
axes = axes.flatten() # reshape from 3x4 array into 12-element vector
for i in range(12):
    plt.sca(axes[i]) # set the current Axes
    plt.text(0.5, 0.5, i+1) # print conventional subplot index number to middle of Axes

.............................

# Bin range 

plt.figure(figsize = [10, 5])

# histogram on the left, bin edges on integers (e.g. bar comes between 2 and 3)
plt.subplot(1, 2, 1)
bin_edges = np.arange(2, 12+1.1, 1) # note `+1.1': just in case a lot of data points take the max value, so create another additional bin
plt.hist(die_rolls, bins = bin_edges)
plt.xticks(np.arange(2, 12+1, 1))

# histogram on the right, bin edges between integers (e.g. the middle point of the bar is interger)
plt.subplot(1, 2, 2)
bin_edges = np.arange(1.5, 12.5+1, 1)
plt.hist(die_rolls, bins = bin_edges)
plt.xticks(np.arange(2, 12+1, 1))

............................
# To create a bar chart from histogram (i.e. gap between bars, esp. discrete data)

in_edges = np.arange(1.5, 12.5+1, 1)
plt.hist(die_rolls, bins = bin_edges, rwidth = 0.7)
plt.xticks(np.arange(2, 12+1, 1))

...............................
# to identify outliers

## Back to pokemon case - analyse the 'height' data 
bins = np.arange(0, pokemon['height'].max()+0.5, 0.5)
plt.hist(data = pokemon, x = 'height', bins = bins);
## the output shows it's right skewed 
plt.xlim((0, 6));    ## to set the axis limits - then you may wish to change the bin width set up earlier too 

## Another example 
plt.figure(figsize = [10, 5])

# histogram on left: full data
plt.subplot(1, 2, 1)
bin_edges = np.arange(0, df['skew_var'].max()+2.5, 2.5)
plt.hist(data = df, x = 'skew_var', bins = bin_edges)

# histogram on right: focus in on bulk of data < 35
plt.subplot(1, 2, 2)
bin_edges = np.arange(0, 35+1, 1)
plt.hist(data = df, x = 'skew_var', bins = bin_edges)
plt.xlim(0, 35) # could also be called as plt.xlim((0, 35))

.....................

# Scale and Transformation 

## E.g. based on pokemon weight data 
bins = np.arange(0,  pokemon['weight'].max()+40, 40)
plt.hist(data = pokemon, x = 'weight', bins = bins);
## output a lot more right skewed
plt.xscale('log');     # axis transformation (e.g. log value), but the result still doesn't look good due to outliers 

## log-normal distribution particularlly useful when lots of points with low values, with a very long tail of data points with large values

np.log10(pokemon['weight'].describle())  ## this will show the range of log we can use for our analysis 
## based on thise result, we can modify our bin range and draw a histogram again 

bins = 10** np.arange(-1, 3+0.1, 0.1)
plt.hist(data = pokemon, x = 'weight', bins = bins); 
plt.xscale('log'); 
# the output looks more lke a bell-shape, but the x axis shows the value from 10(-1) to 10(3), so we can make our ticks more readable 
# after 'bins' above... 
ticks = [0.1, 1, 10, 100, 1000] ## but this makes the width too spread. so we add something in the middle like 3 
ticks = [0.1, 0.3, 1, 3, 10, 30, 100, 300, 1000]
labels = ['{}'.format(v) for v in ticks]
plt.hist(data =  pokemon, x = 'weight', bins = bins);
plt.xscale('log'); 
plt.xticks(ticks, labels); 

## Extra comparison 
plt.figure(figsize = [10, 5])

# left histogram: data plotted in natural units
plt.subplot(1, 2, 1)
bin_edges = np.arange(0, data.max()+100, 100)
plt.hist(data, bins = bin_edges)
plt.xlabel('values')

# right histogram: data plotted after direct log transformation
plt.subplot(1, 2, 2)
log_data = np.log10(data) # direct data transform
log_bin_edges = np.arange(0.8, log_data.max()+0.1, 0.1)
plt.hist(log_data, bins = log_bin_edges)
plt.xlabel('log(values)')

## Extra scale transformation: a Squre-root transformation 
ef sqrt_trans(x, inverse = False):
    """ transformation helper function """
    if not inverse:
        return np.sqrt(x)
    else:
        return x ** 2

bin_edges = np.arange(0, sqrt_trans(data.max())+1, 1)
plt.hist(data.apply(sqrt_trans), bins = bin_edges)
tick_locs = np.arange(0, sqrt_trans(data.max())+10, 10)
plt.xticks(tick_locs, sqrt_trans(tick_locs, inverse = True).astype(int))

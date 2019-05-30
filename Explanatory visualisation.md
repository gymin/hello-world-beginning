# Explanatory visualisation: Primarily about conveying insights to others  

## Creating a Slide Deck with Jupyter (nbconvert tool) 

1. Assign each cell to a specific type of slide element 

Click `View`, `Cell Toolbar`, select `Slideshow`: this will show a small box with `Slide Type` 

2. Save the file and shud down the notebook server

3. Go back to Terminal
```
jupyter nbconvert presentation.ipynb --to slides 
```
By default code cell inputs and outpus are both rendered in the slides. 
If you want to hide the code in your presentation, you can specify a template file using the `--template` option, e.g. `output_toggle.tpl`

4. To play the presentation slides, first install `reveal.js`

Check the following link for the step [Github introduction] (https://github.com/hakimel/reveal.js#installation)

You would need to install a local copy of reveal.js (Installation documentation), making sure that your HTML slides point to the library correctly (using the `--reveal-prefix` option), and then start a local http server (e.g., via `python -m http.server`). 
Alternatively, you can add the `--post serve` option to your expression to make use of a public, online version of reveal.js, 
Start up a server, and immediately open a tab in your web browser with the slide deck ready to navigate.

OR use the an expresseing like below to get a basic slide deck up and running 
```
jupyter nbconvert presentation.ipynb --to slides --template output_toggle.tpl
--post serve
```

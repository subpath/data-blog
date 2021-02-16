---
layout: post
title:  "Jupyter Lab extensions for Data Scientist"
date:   2018-06-29 12:00:00 +0500
permalink: "/jupyter-lab-extensions/"
---

{:refdef: style="text-align: center;"}
![jupyter_logo]({{ '/assets/2018-06-29-jupyter-lab-extensions/jupyter-lab-logo.png' | relative_url }})
{: refdef}




Hi! I wanna share my personal top of Jupyter Lab extensions that I used on a daily basis.

If you don’t know what Jupyter is, I highly recommend to try it out! In a few words Jupyter is IDE that works both with Python and R. It have very clean and intuitive interface. And it’s very much suitable for Data Science purposes.

I started to use Jupyter in 2014, back then it was called iPython Notebook. Since then it’s my first choice, if I need to do some exploratory analysis, build visualizations or make a report and share my findings.

Source: [https://github.com/jupyterlab/jupyterlab](https://github.com/jupyterlab/jupyterlab)

### Installation:

```bash
pip install jupyterlab
# or if you use conda:
conda install -c conda-forge jupyterlab
```

## JupyterLab LaTeX

When it comes to Data Science it is very critical to be able to pass your finding to all stakeholders, or just simply document your research. LaTex is very useful and flexible tool for that.

Source: [https://github.com/jupyterlab/jupyterlab-latex](https://github.com/jupyterlab/jupyterlab-latex)

### Installation:
```bash
# to install server extension
pip install jupyterlab_latex
# to install jupyter extension
jupyter labextension install @jupyterlab/latex
```
### Usage:

You will need to create text file, change it extension to .tex, then inside this file choose Show LaTeX Preview using mouse right-click

![JupyterLabLaTeX]({{ '/assets/2018-06-29-jupyter-lab-extensions/JupyterLabLaTeX.png' | relative_url }})

## JupyterLab HTML

This extension allow you to render HTML file inside of Jupyter Lab, which is can be useful when you need to open for example d3 visualization.

Source: [https://github.com/mflevine/jupyterlab_html](https://github.com/mflevine/jupyterlab_html)

### Installation:
```bash
jupyter labextension install @mflevine/jupyterlab_html
```
### Usage:

Simply click on html file to open it
![JupyterLabHTML]({{ '/assets/2018-06-29-jupyter-lab-extensions/JupyterLabHTML.gif' | relative_url }})

## JupyterLab drawio

I really love drawio and used to use it from their web-interface, but with this extension it possible to draw schemes directly from jupyter lab.

Source: [https://github.com/QuantStack/jupyterlab-drawio](https://github.com/QuantStack/jupyterlab-drawio)
### Installation:
```bash
jupyter labextension install jupyterlab-drawio
```

### Usage:

After installation you will see option to create diagram in your launcher
![JupyterLabDrawIO]({{ '/assets/2018-06-29-jupyter-lab-extensions/JupyterLabDrawIO.png' | relative_url }})

## JupyterLab plotly

Plotly is a very cool visualization library that allows to create effective and interactive charts very easily.

Source: [https://www.npmjs.com/package/@jupyterlab/plotly-extension](https://www.npmjs.com/package/@jupyterlab/plotly-extension)
### Installation:
```bash
jupyter labextension install @jupyterlab/plotly-extension
```
### Usage:

With this extension your plotly visualization will be rendered directly in a notebook.
![JupyterLabPlotly]({{ '/assets/2018-06-29-jupyter-lab-extensions/JupyterLabPlotly.gif' | relative_url }})

## JupyterLab bokeh

Bokeh is another js based library for interactive visualization

Source: [https://github.com/bokeh/jupyterlab_bokeh](https://github.com/bokeh/jupyterlab_bokeh)
### Installation:
```bash
jupyter labextension install jupyterlab_bokeh
```

### Usage:
Same like with plotly
![JupyterLabBokeh]({{ '/assets/2018-06-29-jupyter-lab-extensions/JupyterLabBokeh.gif' | relative_url }})

## Conclusion:

In Science in general and particularly in Data Science it is very important to share your findings with other. Jupyter allows us to do it easily. It became more in more popular among scientists from a different fields. It is very cool to see papers with code written in a jupyter notebook, so you can run the code and check results by you own.
With same logic Jupyter can help us better manage researches in enterprise, by allowing us to create interactive reports.


---
jupyter:
  jupytext:
    formats: ipynb,md,py:light
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.2'
      jupytext_version: 1.3.0
  kernelspec:
    display_name: tf
    language: python
    name: tf
---

title: Coupled van der Pol oscillators

description:  

date: 2020-04-30

tags:
  - math

layout: layouts/post.njk


Coupled van der Pol oscillators
https://scholarsarchive.library.albany.edu/cgi/viewcontent.cgi?article=1004&context=honorscollege_physics

$ g' = A sin ωt − γg − ω^2_0 z$

$z' = g$

```python code_folding=[]
from vanderpol import *
van=get_plot()

```

```python
%output widgets='live'
%output holomap='scrubber'
van[0]
```

```python
%output widgets='live'
%output holomap='scrubber'

van
```

TODO below animations dont work in jupyter+mpl - switch to altair

```python
np.arange(0,5e6,5e6/5)
```

```python
import altair as alt
df=integrate_vanderpol(
    Paramsampl=np.arange(0,5e6,5e6/5),
    Paramsfreq=np.arange(100,1000,1000/5)
)
```

```python
alt.data_transformers.disable_max_rows()

df1=df
df1=df1[df1['omega1']==df1['omega1'].min()]
#df1=df1[df1['a1']==df1['a1'].min()]
df1=df1[::100]
```

```python
# TODO low opacity all-a1 plot to initialize
slider = alt.binding_range(min=df1['a1'].min(), max=df1['a1'].max(),step=df1['a1'].max()/5)
selector = alt.selection_single(name="a1", fields=['a1'],#,nearest=True,
                                   bind=slider, init={'a1': df1['a1'].max()})

alt.Chart(df1).mark_circle(size=.3,opacity=.8).encode(
    x='y1',
    y=alt.Y('y2'),
    color=alt.condition(
        #alt.FieldRangePredicate(field='a1', range=[selector.cutoff-1000, selector.cutoff+100000]),
        # TODO make this a double <> expression
        alt.datum.a1 < (selector.a1),# | (alt.datum.xval > selector.cutoff-100)),
        alt.value('blue'), alt.value('red')
        #alt.opacity(.2), alt.opacity(1)
    )
).properties(
    width=200
).add_selection(
    selector
)

```

```python

ω0 = 2500
ν = 100
µ = 10
ω2 = 300*25#554.365
A2=0
def deriv (y,t,A1,ω1):
    sine1=A1*np.sin(ω1 * t)
    sine2=A1*np.sin(ω2 *t)
    zprime = y[1]
    gprime = -ν *y[1]*(y[0]**2 - µ) - (ω0**2) * y[0] + sine1 + sine2
    return np.array([ zprime , gprime, sine1 ])
plt.figure()
y = odeint(deriv, [0.1,0.1,0.], np.linspace(0, 1., 600000),args=(5e6, 2200))  # with w0=2500, get resonance or beating
plot_and_play(y) 
```

```python
plt.figure()
y = odeint(deriv, [0.1,0.1,0.], np.linspace(0, 1., 600000),args=(5e6, 2200))  # with w0=2500, get resonance or beating
plot_and_play(y) 
```

```python
import os
name='vanderpol_coupled_standalone'
path='~/mmy/jup/models/'
os.system(f'jupyter nbconvert {path}{name}.ipynb --to markdown --output {path}{name}')
```

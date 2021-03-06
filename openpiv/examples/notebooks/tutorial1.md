---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.2'
      jupytext_version: 1.4.2
  kernelspec:
    display_name: Python [conda env:openpiv] *
    language: python
    name: conda-env-openpiv-py
---

<!-- #region -->
# OpenPIV tutorial 1


In this tutorial we read the pair of images using `imread`, compare them visually 
and process using OpenPIV. Here the import is using directly the basic functions and methods
<!-- #endregion -->

```python
from openpiv import tools, process, validation, filters, scaling 

import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

```

```python
frame_a  = tools.imread( '../test1/exp1_001_a.bmp' )
frame_b  = tools.imread( '../test1/exp1_001_b.bmp' )
```

```python
fig,ax = plt.subplots(1,2)
ax[0].imshow(frame_a,cmap=plt.cm.gray)
ax[1].imshow(frame_b,cmap=plt.cm.gray)

```

```python
winsize = 24 # pixels
searchsize = 64  # pixels, search in image B
overlap = 12 # pixels
dt = 0.02 # sec


u0, v0, sig2noise = process.extended_search_area_piv( frame_a.astype(np.int32), frame_b.astype(np.int32), window_size=winsize, overlap=overlap, dt=dt, search_area_size=searchsize, sig2noise_method='peak2peak' )
```

```python
x, y = process.get_coordinates( image_size=frame_a.shape, window_size=winsize, overlap=overlap )
```

```python
u1, v1, mask = validation.sig2noise_val( u0, v0, sig2noise, threshold = 1.3 )
```

```python
u2, v2 = filters.replace_outliers( u1, v1, method='localmean', max_iter=10, kernel_size=2)
```

```python
x, y, u3, v3 = scaling.uniform(x, y, u2, v2, scaling_factor = 96.52 )
```

```python
tools.save(x, y, u3, v3, mask, 'exp1_001.txt' )
```

```python
tools.display_vector_field('exp1_001.txt', scale=100, width=0.0025)
```

---
jupyter:
  jupytext:
    formats: ipynb,md
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.19.3
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

# Splinart on a circle

In this tutorial, we will see how to use splinart with a circle.

First of all, we have to create a circle.

```python
import splinart as spl
center = [.5, .5]
radius = .3
theta, path = spl.circle(center, radius)
```

In the previous code, we create a discretization of a circle centered in $[0.5, 0.5]$ with a radius of $0.3$. We don't specify the number of discretization points. The default is 30 points.

We can plot the points using matplotlib.

```python
%matplotlib inline
```

```python
import matplotlib.pyplot as plt
plt.axis("equal")
plt.plot(path[:, 0], path[:, 1], '+')
```

## The sample

In order to compute a sample on a given cubic spline equation, we need to provide a Python function that gives us the x coordinates. We can choose for example.

```python
import numpy as np
def x_func():
    nsamples = 500
    return (np.random.random() + 2 * np.pi * np.linspace(0, 1, nsamples))%(2*np.pi)
```

We can see that the points are chosen between $[0, 2\pi]$ in a random fashion.


## The cubic spline

Given a path, we can apply the spline function in order to compute the second derivative of this cubic spline.

```python
yder2 = spl.spline.spline(theta, path)
```

And apply the equation to the sample

```python
xsample = x_func()
ysample = np.zeros((xsample.size, 2))
spl.spline.splint(theta, path, yder2, xsample, ysample)
```

which gives

```python
import matplotlib.pyplot as plt
plt.axis("equal")
plt.plot(ysample[:, 0], ysample[:, 1], '+')
```

We can see the sample is well defined around the circle that we defined previously.

Now, assume that we move randomly the points of the circle with a small distance.

```python
spl.compute.update_path(path, scale_value=.001, periodic=True)
```

```python
import matplotlib.pyplot as plt
plt.axis("equal")
plt.plot(path[:, 0], path[:, 1], '+')
```

And we compute again the sample of the new cubic spline equation.

```python
yder2 = spl.spline.spline(theta, path)
spl.spline.splint(theta, path, yder2, xsample, ysample)
spl.compute.update_path(path, scale_value=.001, periodic=True)
```

```python
import matplotlib.pyplot as plt
plt.axis("equal")
plt.plot(ysample[:, 0], ysample[:, 1], '+')
```

The circle is deformed.

This is exactly how works splinart. We give a shape and at each step

- we perturb the points of this shape
- we compute a sample an this new cubic spline equation
- we add the pixel with a given color on the output image

And we do that several time. We can have the following result

```python
img_size, channels = 1000, 4
img = np.ones((img_size, img_size, channels), dtype=np.float32)

theta, path = spl.circle(center, radius)
spl.update_img(img, path, x_func, nrep=4000, x=theta, scale_value=.00005)
```

```python
spl.show_img(img)
```

```python

```

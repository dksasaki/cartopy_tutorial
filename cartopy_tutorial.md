# Cartopy Tutorial — Making Maps with Python

Cartopy is a library for drawing maps. It works together with Matplotlib — you create a figure with a map projection, then draw on it.

Before starting, make sure you have completed the setup in `bash_setup.md`. Then launch Jupyter Lab from your project folder:

```bash
cd ~/cartopy_tutorial
pixi run jupyter lab
```

Create a new notebook and run the examples below cell by cell.

---

## 1. Your First Map

```python
import matplotlib.pyplot as plt
import cartopy.crs as ccrs

fig, ax = plt.subplots(
    subplot_kw={"projection": ccrs.PlateCarree()}
)

ax.coastlines()
ax.set_global()

plt.title("My First Map")
plt.savefig("first_map.png", dpi=150, bbox_inches="tight")
plt.show()
```

`ccrs.PlateCarree()` is the projection — here a simple equirectangular grid. `ax.coastlines()` draws coastlines. `ax.set_global()` shows the full world.

---

## 2. Changing the Projection

Cartopy supports many projections. You only need to change the `projection` argument:

```python
import matplotlib.pyplot as plt
import cartopy.crs as ccrs

projections = {
    "Robinson": ccrs.Robinson(),
    "Mollweide": ccrs.Mollweide(),
    "Orthographic": ccrs.Orthographic(central_longitude=-60, central_latitude=30),
}

fig, axes = plt.subplots(
    1, 3,
    figsize=(15, 4),
    subplot_kw={"projection": ccrs.Robinson()}  # default; overridden below
)

for ax, (name, proj) in zip(axes, projections.items()):
    ax = fig.add_subplot(1, 3, list(projections.keys()).index(name) + 1, projection=proj)
    ax.coastlines()
    ax.set_global()
    ax.set_title(name)

plt.tight_layout()
plt.savefig("projections.png", dpi=150, bbox_inches="tight")
plt.show()
```

---

## 3. Adding Map Features

Cartopy provides built-in geographic features via `cartopy.feature`:

```python
import matplotlib.pyplot as plt
import cartopy.crs as ccrs
import cartopy.feature as cfeature

fig, ax = plt.subplots(
    subplot_kw={"projection": ccrs.PlateCarree()}
)

ax.set_global()
ax.add_feature(cfeature.LAND, facecolor="lightgray")
ax.add_feature(cfeature.OCEAN, facecolor="lightblue")
ax.add_feature(cfeature.COASTLINE, linewidth=0.5)
ax.add_feature(cfeature.BORDERS, linestyle="--", linewidth=0.4)
ax.add_feature(cfeature.LAKES, facecolor="lightblue")
ax.add_feature(cfeature.RIVERS, edgecolor="blue", linewidth=0.3)

plt.title("Map with Features")
plt.savefig("features_map.png", dpi=150, bbox_inches="tight")
plt.show()
```

---

## 4. Zooming Into a Region

Use `ax.set_extent([lon_min, lon_max, lat_min, lat_max])` to zoom in:

```python
import matplotlib.pyplot as plt
import cartopy.crs as ccrs
import cartopy.feature as cfeature

fig, ax = plt.subplots(
    subplot_kw={"projection": ccrs.PlateCarree()}
)

# Zoom to South America
ax.set_extent([-82, -34, -56, 13])

ax.add_feature(cfeature.LAND, facecolor="wheat")
ax.add_feature(cfeature.COASTLINE, linewidth=0.8)
ax.add_feature(cfeature.BORDERS, linestyle="--", linewidth=0.5)

ax.gridlines(draw_labels=True, linewidth=0.4, color="gray", linestyle="--")

plt.title("South America")
plt.savefig("south_america.png", dpi=150, bbox_inches="tight")
plt.show()
```

---

## 5. Plotting Data on a Map

The most common task: shading grid cells by a value (e.g. temperature, precipitation).

```python
import numpy as np
import matplotlib.pyplot as plt
import cartopy.crs as ccrs
import cartopy.feature as cfeature

# Fake data: 1-degree global grid
lons = np.linspace(-180, 180, 360)
lats = np.linspace(-90, 90, 180)
lon2d, lat2d = np.meshgrid(lons, lats)

# A simple pattern (replace with your real data)
data = np.sin(np.deg2rad(lat2d)) * np.cos(np.deg2rad(lon2d))

fig, ax = plt.subplots(
    subplot_kw={"projection": ccrs.Robinson()}
)

ax.set_global()
ax.add_feature(cfeature.COASTLINE, linewidth=0.5)

mesh = ax.pcolormesh(
    lon2d, lat2d, data,
    transform=ccrs.PlateCarree(),  # coordinate system of the DATA
    cmap="RdBu_r",
    vmin=-1, vmax=1
)

plt.colorbar(mesh, ax=ax, orientation="horizontal", pad=0.05, label="Value")
plt.title("Global Data Plot")
plt.savefig("data_map.png", dpi=150, bbox_inches="tight")
plt.show()
```

> **Important:** `transform=ccrs.PlateCarree()` tells Cartopy what coordinate system your data is in (longitude/latitude). This is separate from the map projection you chose for display.

---

## 6. Loading a NetCDF File with xarray

Real data often comes in NetCDF (`.nc`) format. Here is how to load and plot it:

```python
import xarray as xr
import matplotlib.pyplot as plt
import cartopy.crs as ccrs
import cartopy.feature as cfeature

# Load data
ds = xr.open_dataset("your_file.nc")

# Inspect what variables are inside
print(ds)

# Select a variable and a time step (adjust names to your file)
var = ds["temperature"].isel(time=0)

fig, ax = plt.subplots(
    subplot_kw={"projection": ccrs.PlateCarree()}
)

ax.set_global()
ax.add_feature(cfeature.COASTLINE, linewidth=0.5)

var.plot(
    ax=ax,
    transform=ccrs.PlateCarree(),
    cmap="coolwarm",
    add_colorbar=True
)

plt.title("Temperature — First Time Step")
plt.savefig("netcdf_map.png", dpi=150, bbox_inches="tight")
plt.show()
```

Replace `"temperature"` with the actual variable name shown by `print(ds)`.

---

## 7. Quick Reference

| Task | Code |
|---|---|
| Create a map axis | `fig, ax = plt.subplots(subplot_kw={"projection": ccrs.PlateCarree()})` |
| Show full globe | `ax.set_global()` |
| Zoom to region | `ax.set_extent([lon_min, lon_max, lat_min, lat_max])` |
| Add coastlines | `ax.coastlines()` |
| Add land/ocean fill | `ax.add_feature(cfeature.LAND)` |
| Add grid lines | `ax.gridlines(draw_labels=True)` |
| Plot 2D data | `ax.pcolormesh(lons, lats, data, transform=ccrs.PlateCarree())` |
| Save figure | `plt.savefig("output.png", dpi=150, bbox_inches="tight")` |

---

## 8. Common Mistakes

- **Forgetting `transform=ccrs.PlateCarree()`** when plotting data: your data will appear in the wrong place or not at all.
- **Running Python outside the environment**: if you get `ModuleNotFoundError: No module named 'cartopy'`, make sure you launched Jupyter Lab with `pixi run jupyter lab` from the project folder, not from a system Python.
- **Platform not listed in `pixi.toml`**: if installation fails, check that your OS is listed under `platforms`.
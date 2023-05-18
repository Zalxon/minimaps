# zalxon / minimaps

**small maps for figures**

[![GitHub][github-badge]][github]
![MIT License][]
![NPM Version][]

[github]: https://github.com/zalxon/maps
[github-badge]: https://badgen.net/badge/-/github?icon=github&label
[mit license]: https://badgen.net/badge/license/MIT/blue
[npm version]: https://badgen.net/npm/v/@zalxon/minimaps

This library makes it easy to generate small data-driven raster maps. It's a complement to our `@zalxon/maps` library which is for making [interactive tiled maps](https://github.com/zalxon/maps) that can be panned and zoomed. The use case here is figures and other static graphics where we don't need interactivity, where we can easily load the entire raster dataset we want to render, and where we often want to show the full globe.

Not relying on tiles frees us up to more easily use custom projections, which we have implemented here performantly via WebGL. We're using a [separate package](https://github.com/zalxon/glsl-geo-projection) containing the GLSL shader math for inverse geometric map projections.

## usage

Create a `Minimap` component and include the graphical elements you want. Most maps contain a `Raster` (with the data) and a `Path` (typically coastlines or similar). In addition, you might want to render `Graticule` lines or the `Sphere` that bounds the globe.

The source for `Raster` can be an image (e.g. `png`) or a zarr array or group. It must contain a complete data grid in the equirectangular projection (spanning -180,180 lon and -90,90 lat).

Here's a simple example where we use a `json` file for the `Path` and the "Blue Marble" image in `png` format for the `Raster`. We specify that the format is `rgb` because each pixel of the image has three color values that we want to render as rgb.

```jsx
import { Minimap, Raster, Path, Sphere, Graticule } from '@zalxon/minimaps'
import { naturalEarth1 } from '@zalxon/minimaps/projections'

return (
  <Minimap projection={naturalEarth1}>
    <Path
      stroke={'white'}
      source={'https://cdn.jsdelivr.net/npm/world-atlas@2/land-50m.json'}
      feature={'land'}
    />
    <Graticule stroke={'white'} />
    <Sphere fill={'black'} />
    <Raster
      source={
        'https://upload.wikimedia.org/wikipedia/commons/thumb/2/23/Blue_Marble_2002.png/2880px-Blue_Marble_2002.png'
      }
      format={'rgb'}
      transpose
    />
  </Minimap>
)
```

Here's a slightly more complicated example where we use a `zarr` file for the `Raster`. The data here are one-dimensional, so we specify a `colormap` and `clim` and the `lut` mode.

```jsx
import { Minimap, Raster, Path, Sphere, Graticule } from '@zalxon/minimaps'
import { naturalEarth1 } from '@zalxon/minimaps/projections'
import { useColormap } from '@zalxon/colormaps'

const colormap = useColormap('fire')

return (
  <Minimap projection={naturalEarth1}>
    <Path
      stroke={'white'}
      source={'https://cdn.jsdelivr.net/npm/world-atlas@2/land-50m.json'}
      feature={'land'}
    />
    <Graticule stroke={'white'} />
    <Sphere fill={'black'} />
    <Raster
      clim={[0, 50000000]}
      mode='lut'
      nullValue={9.969209968386869e36}
      source={
        'https://'
      }
      variable={'emissions'}
      colormap={colormap}
    />
  </Minimap>
)
```

## data sources

You can specify the `source` as either the URL of an image resource (e.g. `.png` or `.jpeg`) or a Zarr store (`.zarr`). In the case of using a Zarr store, you can either point to an array, or to a group an additionally specify the `variable`.

## license

All the original code in this repository is [MIT](https://choosealicense.com/licenses/mit/) licensed. We request that you please provide attribution if reusing any of our digital content (graphics, logo, copy, etc.).

## about us

Zalxon is a non-profit organization that uses data and science for healthcare action. We aim to improve the transparency and scientific integrity of healthcare solutions with open data and tools. Find out more at [zalxon.com](https://zalxon.com/) or get in touch by [opening an issue](https://github.com/zalxon/components/issues/new) or [sending us an email](mailto:hello@zalxon.com).

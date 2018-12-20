# The Continents of Adereon: Methods for Creating a Fantasy Map

## Table of Contents

<!-- TOC depthTo:3 -->

- [The Continents of Adereon: Methods for Fantasy Mapping](#the-continents-of-adereon-methods-for-fantasy-mapping)
  - [Table of Contents](#table-of-contents)
  - [Overview](#overview)
  - [Coordinate System and Projection](#coordinate-system-and-projection)
  - [Data Creation](#data-creation)
    - [Contextualizing](#contextualizing)
    - [Georeferencing](#georeferencing)
    - [Geoprocessing](#geoprocessing)

<!-- /TOC -->

## Overview

The landmasses in the known world of Adereon have, to this day, never been mapped in a geographic information system.

In this document, I'll detail some of the key considerations and techniques that went into making a spatial database -- and a few maps to go along with it -- of my friend Nate's homebrewed Dungeons & Dragons universe.

## Coordinate System and Projection

A fantasy GIS is kind of a weird endeavor from the start, not least because it complicates the most basic pair of principles that cartographers and geographers deal with: namely, coordinate systems and projections. If I wanted this map to be spatially sound (for example, measuring distance in miles from one city to the next), it would have to be properly projected -- which first means figuring out how large the globe is. Instead of creating my own coordinate system from scratch, I suggested to Nate that we use an established one, to which he was amenable.

Nate and I established that the size of the western continent in Adereon was roughly as long as the territory of Nebraska (i.e., 430 miles from the west coast to the east coast). Unfortunately that was going to leave way too much empty space out there if we used a GCS based on Earth. Considering, Nate was agreeable to a conceptual globe roughly the size of our Earth's moon, whose circumference is about 6,800 miles and is a much more reasonably sized big blue marble, so to speak, for the purposes of a D&D campaign.

There is, luckily, a GCS available for the moon -- GCS_Moon_2000 -- so this is what I ran with when selecting a coordinate system.

I suppose what I am doing, then, could be considered [selenographic mapping](https://en.wikipedia.org/wiki/Selenographic_coordinates). Super!

## Data Creation

With a coordinate reference system selected, the next order of business was data creation. Here, I break down my process of data creation in a few different components.

* I began with contextualizing the data, by which I mean I downloaded some other lunar data to create a map extent and get myself situated.
* Next, referring to instructions from Lab 6, I georeferenced the scanned map of Adereon.
* Finally, I did a little bit of geoprocessing (a combination of raster and vector tools) in order to save myself some time on the manual labor of drawing the continents. As it happens, this did not save me any time at all but was an interesting rabbit hole. In the following sections I'll break down each of these data creation steps.

### Contextualizing

The original "data" is shown below: three continents, some islands, forests and mountains and rivers and oceans.

![Adereon topo map](screenshots-and-images/figure-01.png)
*Figure 01: Original map of the known world of Adereon, where this idea began*

It's just a scanned and hand drawn map of an imaginary region, but we might think of this data in the same way we would treat an old map whose entire extent needed to be digitized.

The data I am creating does not need to be perfect; indeed, it's a dataset of imaginary topographies, so no harm done if it's a few miles off. Nate and I had agreed that the western continent was roughly equivalent to Nebraska in length.

To situate myself, I downloaded some files of different features on Earth's moon from the [USGS](https://webgis.wr.usgs.gov/pigwad/down/moon_dl.htm), which could be used to determine the extent of what I'd be mapping. Specifically, I downloaded (1) a raster file of the moon, and (2) a vector file of the moon's quadrants. They each covered the moon's full extent and thus serve as a kind of "basemap" in my fantasy GIS.

![Full extent of the moon/my mapping territory](screenshots-and-images/figure-02.png)
*Figure 02: Full extent of the moon/my mapping territory*

I selected a few quadrants to georeference against

![My selected extent for Adereon](screenshots-and-images/figure-03.png)
*Figure 03: My selected extent for Adereon*

On to georeferencing!

### Georeferencing

Replicating the instructions from Lesson 6 made this section rather simple. The file _adereon-topo-map.jpg_, which is the original map in Figure 01 drawn by Nate, is an unreferenced image that has no coordinate information with which a GIS can place size or location (essentially the same as _sidewalk.jpg_ from Lesson 6). As such, I imported _adereon-topo-map.jpg_ into the GDAL Georeferencer in the IAU Moon 2000 Geographic Coordinate System. Since I was only georeferencing to get the topo map *somewhere* in QGIS, rather than georeferencing it against specific features, it was simple to do and I just approximated 5 points as best I could. The result can be seen below.

![Georeferenced Adereon topo map](screenshots-and-images/figure-04.png)
*Figure 04: Georeferenced Adereon topo map*

I distorted the original map a little bit in order to leave more space at the bottom for future world building stuff.

### Geoprocessing

This is where things got a little hairy. Instead of manually digitizing all of the continents, I chose to "polygonize" my raster data, i.e., the georeferenced topo map of Adereon.

#### Vectorize the raster

First, I ran the "Polygonize" tool, located in "Raster --> Conversion." It's part of the GDAL library and after quite a bit of troubleshooting I was able to get the tool to work.

![Vectorized map of Adereon](screenshots-and-images/figure-05.png)
*Figure 05: Vectorized map of Adereon*

Yikes -- this has a bunch of issues and I had my work cut out for me. In order to generate a vector polygon of continents with a clear boundary that was not composed of ugly right angles, my next step was to concatenate all of these messy records (there are over 100,000) into a single polygon.

#### Dissolve vectorized map

The Dissolve tool is good for this. I deleted the big background polygon, and after setting my geoprocessing preferences to "ignore features with invalid geometry," I ran the Dissolve tool.

![Dissolved vectorized map of Adereon](screenshots-and-images/figure-06.png)
*Figure 06: Dissolved vectorized map of Adereon*

#### Create background and subtract difference

You'll notice in Figure 06 that there are a bunch of empty spaces; the Dissolve tool overlooked a number of invalid geometries, which, in most cases, were located inside the new polygons. To infill that empty space, I first dissolved the quadrants polygon, which would serve as a vector background for the next step.

![Dissolved quadrants](screenshots-and-images/figure-07.png)
*Figure 07: Dissolved quadrants*

Next, I ran the Difference tool, using the dissolved vectorized output as an overlay.

![Output of Difference tool with dissolved vectorized output](screenshots-and-images/figure-08.png)
*Figure 08: Output of Difference tool with dissolved vectorized output*
![Output of Difference tool without dissolved vectorized output](screenshots-and-images/figure-09.png)
*Figure 09: Output of Difference tool without dissolved vectorized output*

After deleting the big useless polygon on the outside, my new file looked like this:

![Output of Difference tool](screenshots-and-images/figure-10.png)
*Figure 10: Output of Difference tool*

#### Union and Dissolve

Together, these new files created the polygonal infill I needed:

![Difference plus dissolved vectorized output](screenshots-and-images/figure-11.png)
*Figure 11: Difference plus dissolved vectorized output*

The Union tool joined them up nicely, into a single file:

![Output of Union tool](screenshots-and-images/figure-12.png)
*Figure 12: Output of Union tool*

After a quick Dissolve, the internal borders are gone and we have a vector file that somewhat resembles the original scanned topo map.

![Dissolve from Difference output](screenshots-and-images/figure-13.png)

*Figure 13: Dissolve from Difference output*

Unfortunately, there are some donut holes, but those will be fixed as a byproduct of the next step.

#### Smoothing through buffer-debuffer

At first glance this output looks nice -- much better at least than the original vectorization -- but upon closer inspection it still has the pesky right angles along the boundary.

![Pesky right angles](screenshots-and-images/figure-14.png)

*Figure 14: Pesky right angles*

#Fin

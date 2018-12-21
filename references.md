# References

Random DEM generator: https://beyondthemaps.wordpress.com/2017/11/01/random-dem-generator/

Creating hillshaded maps: https://ieqgis.wordpress.com/2015/04/04/create-great-looking-topographic-maps-in-qgis-2/

Moon shapefiles 1: https://ode.rsl.wustl.edu/mars/coverage/ODE_Moon_shapefile.html

Moon shapefiles 2: http://lroc.sese.asu.edu/posts/978


# Problems

When trying to use GDAL (polygonize tool) got the following error:

    2018-12-19T16:10:20     INFO    gdal_polygonize.py "/Users/ianspangler/Documents/School/University of Kentucky/2018-19 Fall/MAP 671 - Intro/Adereon/original_adereon_maps/adereon-topo-map_2.tif" /var/folders/pl/k0ws3ksn3h7_cntlqsh0tj480000gn/T/processing_5ee58637189249e8bd3cb613fb684ccd/5ca62870f2cb477e8d8e3f8117dfb198/OUTPUT.shp -b 1 -f "ESRI Shapefile" None DN
    2018-12-19T16:10:20     INFO    GDAL execution console output
                 /bin/sh: gdal_polygonize.py: command not found

Solution found here:

    Go to Settings ... Options... System ... Environment Enable "Use Custom Variables "

    First select "Prepend", under variable enter "PATH", under value enter

    "/Library/Frameworks/GDAL.framework/Programs:/Library/Frameworks/Python.framework/Versions/3.6/bin:"

    (all these without the quotes)

    Restart QGIS and it should work.


But when I do that I get the following error upon closing the settings window:

    2018-12-19T16:07:55     WARNING    Traceback (most recent call last):
                  File "/Users/ianspangler/Library/Application Support/QGIS/QGIS3/profiles/default/python/plugins/QuickOSM/quick_osm_processing/provider.py", line 78, in loadAlgorithms
                  self.addAlgorithm(alg)
                 RuntimeError: wrapped C/C++ object of type BuildQueryInNominatimAlgorithm has been deleted

I can't find the gdal_polygonize.py file anywhere on my computer, so I went back to GDAL and downloaded 2.4 and 2.3.2 (2.3.2 being the latest stable release). gdal_polygonize.py is in that

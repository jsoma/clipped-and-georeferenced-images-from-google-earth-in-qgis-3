# Georeference + Crop Google Earth images (or any other images) in QGIS 3

![](images/output.jpg)

So we're going to georeference the image, then crop the georeferenced images so they are rectangles, and not warped monstrosities. and **BONUS** get the latitude and longitude of the top-left corner and the bottom-right corner!!! 

Unbelievable!

## Export an image from Google Earth

Yes, it would be nice if the earth were a nice flat projection in Google Earth. No, you can't make the earth a nice flat projection in Google Earth. We're globes-only now, because that's life.

1. Open Google Earth Pro ([the desktop version](https://www.google.com/earth/versions/#earth-pro))
2. Browse to where you want to be.
3. Click the **Save image** button
4. Increase the resolution of your image + remove all of the extraneous elements

![Google earth export](images/00-google-earth.png)

## Georeference in QGIS 3

### Adding your reference layer

Add an "XYZ layer," which is like the Google Maps underlay you're going to use to match up where your map belongs. I'm using OpenStreetMap, because it's apparently already on my QGIS.

![XYZ layer](images/01-xyz.png)

> If you don't have the Browser panel, you can select **View > Panels > Browser** to make it appear.

But you probably want something with mountains and stuff so you can match up your map better, right?

Right-click, click **New Connection**, and create a new XYZ layer. I think [this site](https://leaflet-extras.github.io/leaflet-providers/preview/) is a pretty good overview of providers, you just need the whole `{z}/{x}/{y}.png` URL thing. For example, a nice one might be:

```
https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}
```

![XYZ layer new connection](images/01-xyz-new-connection.png)

Fill in the box..

![XYZ layer filled in details](images/01-xyz-filled.png)

And then drag it to your map!

![better XYZ layer from ESRI](images/01-xyz-better.png)

### Georeferencing

Now we need to georeference, which is how you give your image a latitude/longitude (and stretch it to make it look 'right').

Go to **Raster > Georeferencer**

![](images/02-georef.png)

Click the box in the top left to add a new raster image. When it asks you for your CRS, pick `WGS 84 EPSG:4326` (I actually don't think it matters).

![](images/02-add-raster.png)

Now you'll need to match up points on your image with points on your basemap. First, click somewhere on your image that is memorable - the edge of a peninsula, or a curve in the land or something.

![](images/02-click-1.png)

QGIS will ask you, where is this?

![](images/02-map-coords.png)

You don't memorize lat and lon of random points in the world, so you click **From map canvas**.

Click the same point in your XYZ layer and _tada!_, it filled the points in for you. Click **OK**.

![](images/02-click-2.png)

![](images/02-post-click.png)

Do this a million times, and eventually you'll have a million georeferenced points. The more the better! Be sure to get lots of things close to the edges.

![](images/02-lots-of-points.png)

Now click the **gear icon** to change the settings, because the defaults are real bad.

![](images/02-gear.png)

According to [this random internet person](https://ieqgis.wordpress.com/2014/05/22/how-to-georeference-a-map-in-qgis/) you should use **Polynomial 3** for Transformation type and **Lanczos** for Resampling method.

Make sure you've selected an output file and **Load in QGIS when done** is checked and click **OK**

![](images/02-settings.png)

Now click the green **play button**. It'll take some time thinking, then..... it'll look like nothing happened! But if you minimize the georeferencer you'll see it got exported and put onto the map.

![](images/02-play.png)

![](images/02-played.png)

To check that it's okay, right click the new layer, select **Properties**, go to the **Transparency** section and dial it down to 50% or so.

![](images/02-properties.png)

![](images/02-transparency.png)

How well does it match up?

![](images/02-matched.png)

If it doesn't look good, delete the layer, open the georeferencer window (remember you only minimized it!) and add some more points or delete ones you did poorly.

Once you're satisfied, you can close the georeferencing plugin. It'll ask if you want to save the points.... but I say no!

## Clipping your raster

Now you have a beautiful image that is way too big and with all kinds of weird black stuff where it got stretched to fit on the map.

![](images/03-black-stuff.png)

Let's crop it! Select **Raster > Extraction > Clip Raster by Extent**.

![](images/03-clip-menu.png)

Your **Input layer** is the layer we just georeferenced. It asks for our **clipping extent** - the boundaries - but we have no idea! Click the `...` button and pick **Select Extent on Canvas**.

![](images/03-extent-on-canvas.png)

Unlike the georeferencer, this menu doesn't get out of the way. Just move it to the side or minimize it or something, then click and drag to mark the area you'd like to crop for your map.

It will draw a red box for you, and when you release the mouse it'll fill in the extent field.

![](images/03-click-drag.png)

![](images/03-extent-menu.png)

Scroll down in the menu (even though it looks like you can't), and tell it you want to save the result as a file. Click **Run**.

> This might not work! You might get an error about `gdal_translate` not being found! If that happens, swear gently at QGIS but keep in mind it's totally free, and use [this fix here](https://gis.stackexchange.com/a/277398)

![](images/03-scroll-file.png)

Hide your old ugly gross layer and bask in the joy of your new awesome beautiful layer!

![](images/03-beautiful-layer.png)

## Getting the latitude and longitude of the top left and bottom right (or layer bounds or whatever)

> There is probably a better way to do this, but *I don't know what it is.* Here's my bad way!

Now you probably need to know the corners of your new layer.

First, pretend you're going to **export the image.**

![](images/04-export.png)

Now change the **CRS** to be `EPSG:4326 - WGS 84`. The default CRS is something about meters, but we want latitude and longitude!

Once you've changed the CRS to EPSG:4326, you'll see the **North/West/Earth/South** numbers look like beautiful perfect latitudes and longitudes.

![](images/04-export-menu.png)

In this case, your top left is `48.241863045, -124.783742788` and your bottom right is `24.440317236,-73.951903924`.

![](images/output-neat.jpg)

**Life is great.** 🎉 🗺 🎉 🗺 🎉

> I just put the coordinates in there with Photoshop, that's not black magic

## A little bit more, maybe?

To turn the cropped image into something useful, go find the `.tif` you saved (remember when you picked to save it as a real file instead of a temporary file?), open it in literally any image editing software, and re-save it as a jpeg.
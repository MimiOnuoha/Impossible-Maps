# Satellite Imagery 101 


Random, not related to satellite imagery, but worth discussing: what work does [this](https://www.splcenter.org/hate-map) do? 

## Background
Governments and private-sector organizations are the entities that have traditionally sent satellites up into space (but anyone can, sort of). These satellites rotate the earth in set paths, snapping pictures every so often at different resolutions and different spectrum wavelengths. 

Historically, governments (and more specifically, militaries) have had the strongest impetus for being interested in satellite imagery. As we've spoken about at the length, the ability to see and render the world is a form of power-knowledge. Governments have leveraged information from satellites to better understand going-ons both within their own countries and globally. 

But as time has passed, satellite imagery has become both available and useful for other types of organizations and entities as well. Climate scientists can monitor fires and trace environmental effects over time. Nightime imagery can show fishing ships, oil flares, and natural lighting events. Humanitarian organizations have used satellite imagery for creating proxies for understanding poverty (through architecture, and developments). Journalists have used satellite imagery for telling planetary stories that unfold over time and are visible from above. And artists have used it to point our social conditions, comment on surveillance, create breath-taking aesthetic renderings, and more.

Satellite imagery is inherently appealing. It gives us a God-like view and presents us with sites that are simultaneously treal and elusive to our own eyes. 

See also:

- [Blue Marble, 1972](https://www.nasa.gov/content/blue-marble-image-of-the-earth-from-apollo-17) 
- [Blue Marble, 2012](http://earthobservatory.nasa.gov/Features/BlueMarble/BlueMarble_2002.php) 
- [Night imagery](https://t.co/avEjUlEtza)

---

## Working with the Imagery

As with all things, the question to ask yourself is **what are you trying to do?** If you just need snapshots of satellite imagery, Google Map's Static Images API might be just thing. If you want super high resolution and don't want to do much to get it, you can reach out ot GlobalEye or DigitalGlobe and get images directly from them (you might have to pay, but the quality will be great.)

### Getting Images from Google

This is super simple, so let's back up and think about the why. 

The pros: Due to partnerships with third-party satellite image providers like Digital Globe and GeoEye, Google has a substantial feed of satellite imagery that's already been processed and beautifully presented. As long as we know the latitude and longiture of the areas we want to work with, we can easily display or download images. 

The cons: Google has done a lot of processing for us, which can be good or bad. If we want more control (and are okay with sacrificing a bit on the resolution side )the reason that we prioritize open-source tools in this class isn't because they are inherently better (in fact, they're usually worse), but because corporations are accountable to external (often capital-driven) forces, which gives them differnt goals when it comes to data collection and presentation. 

For instance, see [this link](http://list25.com/25-places-that-are-suspiciously-blurred-out-on-google-maps/) about sites blurred by Google maps and Google earth. 

So if you want access to certain locations, or you want to show changes over time, or you want more control over the images, you're going to have to do things the harder way.

NASA and the US Geological Survey (USGS) have traditionally been a fantastic source of satellite imagery (unclear if this will change in the Age of Trump). There are a number of portals that allow us to work with this data, and most of them work with imagery coming from the Landsat series of satellites. But to do this successfully, we have to understand a little bit more about how satellite imagery works. Specifically, we'll focus on images coming from Landsat satellites.


### LandSat Imagery Essential Things to Know:

- All Landsat images have a 21 digit scene ID, and they all have the same [format](https://landsat.usgs.gov/what-are-naming-conventions-landsat-scene-identifiers). Let's use LC81240612016324LGN00 as an example:
  - the first three refer to the satellite and sensor (LC8 for Landsat 8)
  - the next six refer to the path and row that the satellite follows every 16 days (e.g., path 124, 061)
   - the next four are the year in which the image was taken (2016)
   - the next three are the julian calendar day of the year (324)
   - the next three refer to ground station identifier (an LGN facility)
   - And the last two refer to 
- **Bands**: Satellites capture different sorts of information. Landsat catches data in bands, which are ranges of frequencies along the electromagnetic spectrum. They're all colors, but crucially not all of these colors are visible to the human eye. Usually we're only interested in bands 4, 3, and 2 (the red, green and blue sensors respectively) and combine them into a true color image. You may also be interesting in the three combined with band 8, a process called pansharpening. Band 8 is a panchromatic band that has the sharpest resolution (15 meters, twice as much as the other bands because it can detect more light at once). For more on the bands, see [Mapbox's overview](https://www.mapbox.com/blog/putting-landsat-8-bands-to-work/). 
- **Cloud Coverage**: less than 20% is ideal, though this occasionally can be a difficult constraint to work around. 


### About Landsat Imagery
Now that we understnad how Landsat imagery works and is organized, how do we get it? It turns out there are a number of places:

1. **[USGS EarthExplorer](https://earthexplorer.usgs.gov)** - the O.G. in the field, a browser-based service that has stood up to the test of time. You'll have to make an account, but once you do that it's free to download images, and you can get them from older and newer satellites. Super cool!
2. **[Libra](https://libra.developmentseed.org/)** - a little bit newer, and it's a more visual and easier-to-use version of the EarthExplorer app. Unfortunately, it's limited solely to LandSat 8 data
3. **[landsat-util](https://pythonhosted.org/landsat-util/)** - a command line tool that is frankly amazing for working with Landsat data. Landsat are a series of satellite backed by USGS, they've been sent up into space for quite a while and the most recent one Landsat 8, has some great imagery.  It's what we'll be using in class. 

### Landsat-util example 

Okay, let's get some data. 

First, you'll need to download landsat-util (documentation is [here](https://pythonhosted.org/landsat-util/)). You can use `pip install landsat-util`. If you don't have pip installed yet, you should use brew.

Using Indonesia as an example because I just went there, and I like how islands render in landsat imagery. We can give a latitude and longitude and it's going to search for images that match them. We can add parameters, like here where we've added it so that cloud coverage is less than 20% (which is standard for good images).

	landsat search --lat 36.1128 --lon 113.9961 --cloud 20

This will return a list of JSON entries that satisfy the conditions we've set, in order of most recent. The thing we're looking for the is that 21 digit scene ID; once we know it then we can can download what we can't. I usually check the thumbnail or correlate this with one of the browser sites above to get more info. But the top one is what we want, so let's download. 

	landsat download LC81240612016324LGN00 --bands 4328

This will download the bands we need. If we didn't specify, landsat-util would just download all 11 bands. But the files are huge (often up to 1 GB), so that's not preferable. 

You'll notice that the images look weird, super dark, not like the normal satellite imagery you're used to seeing. This is because the Landsat sensors are super sensitive so tha they can successfully read in useful data about the whole world. The results look weird to us, and we'll have to change the brightness and contrast, but it gives access to the ingormation we need. 

So now you can see how satellite imagery is at once extremely true to very powerful sensors and somewhat constructed for our eyes. Color correction is a huge part of working with satellite imagery; every image you see has been manipulated to look more the way we know (think?) that it should look. 

Fortunately, landsat-util will help us do the processing we need (you'll need to change the path below as is appropriate for your file locations, see the second example under this one for what mine looked like). If we combine the three bands, we can get an RGB image

	landsat process /users/YOURUSERNAME/landsat/file/to/folder/LC81240612016324LGN00 --bands 432

And the results already start to look a lot better! Here's another common way that we process images; pansharpening makes the images more 

	landsat process /users/mimio/landsat/downloads/Indonesia/LC81240612016324LGN00 --pansharpen

If you're not happy with the images, we can push them into PhotoShop or any image processing software and play around with the contrast and brightness. You'll also want to make the image quite a bit smaller, because they're huge; I used Photoshop for this as well.  See this [guide](Here's a wonderful [guide](https://www.planet.com/pulse/color-correction/) on processing images by geographer Robert Simmons for more details on processing. 

If you are happy with the image, go ahead and save it and upload it to your blog, or somewhere, and send me the link. 

Oh, and here's a quick example of clipping before processing, this one is from the landsat documentation website and features the city of Prague.  

	landsat process path/to/LC81920252015157LGN00.tar.bz --pansharpen --clip=-346.06658935546875,49.93531194616915,-345.4595947265625,50.2682767372753

Note that the coordinates are in order of SW, then  NE corner (or lower left, upper right).

#### Final Notes for Further Exploration
Other things to look into:  

- Mapbox's satellite image layer 
- PlanetLabs
- Cesium 
- Github repo/code for Josh Begley's Prison Map is [here](https://github.com/joshbegley/Satellite-Images/blob/master/Satellite_Images_Google/Satellite_Images_Google.pde). 





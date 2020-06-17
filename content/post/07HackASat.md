---
title: "2020 HackASat Quals"
date: 2020-06-03T21:47:00-06:00
draft: false
comments: false
tags: ["math", "writeups", "astrodynamics"]
images: ["images/GoogleEarthPic.png"]
---


I participated in [HackASat Quals](https://www.hackasat.com/) with my team earlier in May. This was a unique CTF, as it had a category of astrodynamic mathematical problems that pertained to the geometry of earth-orbiting objects. I love math, so I actually quite enjoyed this category! 2 teammates and I collaborated together to solve a specific problem known as "I Like to Watch." I also went through it myself and am going to discuss my solution to it. 

## Let's Begin! 
The challenge states that we must realign a satellite (simulated via Google Earth) to its exact position and orientation as it was reported at a specific time.

{{< image src="/images/HackASatDescription.jpg" alt="Login" position="center" style="border-radius: 8px;" >}}


Additionally, the challenge supplies us with a KML file - files tailored for Google Earth to communicate location data, with similarities to XML documents.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2">
  <Folder>
    <name>HackASatCompetition</name>
    <visibility>0</visibility>
    <open>0</open>
    <description>HackASatComp1</description>
    <NetworkLink>
      <name>View Centered Placemark</name>
      <visibility>0</visibility>
      <open>0</open>
      <description>This is where the satellite was located when we saw it.</description>
      <refreshVisibility>0</refreshVisibility>
      <flyToView>0</flyToView>
      <LookAt id="ID">
        <!-- specific to LookAt -->
			<longitude>-FILL ME IN</longitude>
			<latitude>FILL ME IN</latitude>
			<altitude>FILL ME IN</altitude>
			<heading>FILL ME IN</heading>
			<tilt>FILL ME IN</tilt>
			<range>FILL ME IN</range>
			<gx:altitudeMode>relativeToSeaFloor</gx:altitudeMode>
      </LookAt>
      <Link>
        <href>http://18.191.77.141:31110/cgi-bin/HSCKML.py</href>
        <refreshInterval>1</refreshInterval>
        <viewRefreshMode>onStop</viewRefreshMode>
        <viewRefreshTime>1</viewRefreshTime>
        <viewFormat>BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth];CAMERA=[lookatLon],[lookatLat],[lookatRange],[lookatTilt],[lookatHeading];VIEW=[horizFov],[vertFov],[horizPixels],[vertPixels],[terrainEnabled]</viewFormat>
      </Link>
    </NetworkLink>
  </Folder>
</kml>

```

From the netcat connection, the coordinates of the satellite are given to us in [TLE format](https://en.wikipedia.org/wiki/Two-line_element_set). I saved the TLE data in a text file (TLE.txt) for later use. In order to draw out meaningful values from it, you need to parse it. While you can do so manually, I opted to use a script that utilizes [pyOrbital](https://pyorbital.readthedocs.io/en/latest/#):

```python
from pyorbital.orbital import Orbital
from pyorbital import tlefile
from datetime import datetime

tle_object_name = "ISS (ZARYA)"
sat = tlefile.read(tle_object_name, 'TLE.txt')

##8:53:03 pm on March 26, 2020 according to the netcat information
time = datetime(2020, 3, 26, 21, 53, 3)

satLon, satLat, satAlt = sat.get_lonlatalt(time)

print "Satellite Lat{}, Lon{}, Alt{}".format(satLat, satLon, satAlt)

...

```

```
Satellite Lat{36.946888888888886}, Lon{-80.94308333333333}, Alt{419158.46875}
```

Parsing the TLE format gives us the satellite's location in latitude and longitude, alongside several other key info. The challenge's description asks us to arrange the satellite's (Google Earth) camera in the exact same position as it was, facing the Washington Monument, with very little room for error. We must essentially recreate the orientation of the satellite at its given position at the given time. Since we're using Google Earth, the given KML file will help us manipulate the orientation of the virtual camera. 

In the KML file, we are given a specific tag known as ``<LookAt>``. Some research into the KML documentation reveals that the ``<LookAt>`` tag changes the object that the camera is focused on. It can function similarly to another attribute known as ``<Camera>``, which changes the actual camera position. Both tags can be used to try and position our satellite to perfectly align to the Washington Monument, but the ``<Camera>`` tag deals with less sub-attributes rather than ``<Lookat>``. In this solution, I opted to use the ``<Camera>`` tag, however, the initial solve my team did the ``<LookAt>`` tag. 

## The Camera Tag

``<Camera>`` has 7 sub-attributes(excluding altitudeMode), of which we only need to focus on all but the last:

```xml
<Camera id="ID">    
  <longitude>0</longitude>          <!-- kml:angle180 -->     
  <latitude>0</latitude>            <!-- kml:angle90 -->    
  <altitude>0</altitude>            <!-- double -->    
  <heading>0</heading>              <!-- kml:angle360 -->    
  <tilt>0</tilt>                    <!-- kml:anglepos180 -->    
  <roll>0</roll>                    <!-- kml:angle180 -->    
  <altitudeMode>clampToGround</altitudeMode>
       <!-- kml:altitudeModeEnum: relativeToGround, clampToGround, or absolute -->  
       <!-- or, gx:altitudeMode can be substituted: clampToSeaFloor, relativeToSeaFloor -->
</Camera> 
```

The first three (longitude, latitude and altitude) we can parse from the TLE data from above. However, for heading and tilt - we would need to do some math. 

The heading, also known as the azimuth direction, of an earth-orbiting object is the angle from it's absolute ground-level position to the north pole. It is a method to orient oneself to face the object relative to a standardized direction (North/South) on earth. The azimuth is an angle between 0-360 degrees. 

We want to determine the azimuth of our satellite at 10:53pm on March 20, 2020. We can use the methods within pyOrbital to calculate the elevation and heading of our satellite. While, again, this can be done manually, keep in mind that we are computing an object relative to Earth and thus must account for the curvature of our planet - we need to acknowledge our geometry being non-Euclidean. This serves several problems for manual computation or utilizing a machine to calculate equations which do not take this into account. 

In my computations, I wanted to calculate the azimuth of the satellite relative to an observer - as in, a target on the ground looking at the satellite. In this case, the challenge wants us to focus the satellite camera on the Washington Monument, so we will have the landmark be our observer.

```python

...
#Part 2 of our script.

#Washington Monument lon, lat and alt values.
WaLon = 38.8895
WaLat = 77.0353
WaAlt = 169

azi = sat.get_observer_look(utc_time=time, lon=WaLon lat=WaLat, alt=WaAlt)
	print("")
	print("Azimuth:	", azi)
```

The tilt is the rotation of the satellite on the X-axis. Because the satellite would theoretically be looking at an overhead angle towards the monument, I did some manual guessing and checking manipulation to see if I could get the correct value. I'm sure theres a better, more efficient way of doing this, but I digress :P.

Now, when we plug in all our values into the sample KML file they provided for us, we should appear at the same location the satellite was at, with the camera turned towards the Washington Monument in the exact same orientation and positioning as the satellite was at the given time. 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2">
  <Folder>
    <name>HackASatCompetition</name>
    <visibility>0</visibility>
    <open>0</open>
    <description>HackASatComp1</description>
    <NetworkLink>
      <name>View Centered Placemark</name>
      <visibility>0</visibility>
      <open>0</open>
      <description>This is where the satellite was located when we saw it.</description>
      <refreshVisibility>0</refreshVisibility>
      <flyToView>0</flyToView>
      <Camera id="ID">
        <!-- We input our values here -->
			<longitude>-80.94308333333333</longitude>
			<latitude>36.946888888888886</latitude>
			<altitude>19158.46875</altitude>
			<heading>57.889</heading>
			<tilt>44.886</tilt>
			<gx:altitudeMode>relativeToSeaFloor</gx:altitudeMode>
      </Camera>
      <Link>
        <href>http://18.191.77.141:31110/cgi-bin/HSCKML.py</href>
        <refreshInterval>1</refreshInterval>
        <viewRefreshMode>onStop</viewRefreshMode>
        <viewRefreshTime>1</viewRefreshTime>
        <viewFormat>BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth];CAMERA=[lookatLon],[lookatLat],[lookatRange],[lookatTilt],[lookatHeading];VIEW=[horizFov],[vertFov],[horizPixels],[vertPixels],[terrainEnabled]</viewFormat>
      </Link>
    </NetworkLink>
  </Folder>
</kml>

```

Doing so in Google Earth, the link that we specified in the KML file refers to the server to check our values against theres, and we are rewarded with a (randomized) flag once we have aligned the satellite back to its exact position on March 20 :) 

{{< image src="/images/GoogleEarthPic.png" alt="Login" position="center" style="border-radius: 8px;" >}}

This was less a "hacking" challenge and more a mathematics one. I'm always down to learn more about the world of math, and hopefully I can expand my blog to talk about the unique things I come across in the world of complex mathematics!

Jam

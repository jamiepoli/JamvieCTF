---
title: "2020 Hack A Sat Quals"
date: 2020-06-03T21:47:00-06:00
draft: true
comments: false
images:
---


I participated in HackASat Quals with my team earlier in May. This was a unique CTF, as it had a category of astronomy-based mathematical problems that pertained to the geometry of earth-orbiting objects. I love math, so I actually quite enjoyed this category! 2 teammates and I collaborated together to solve a specific problem known as "I Like to Watch." I also went through it myself and am going to discuss my solution to it. 

## Let's Begin! 
The challenge states that we must realign a satellite (simulated via Google Earth) to its exact position and orientation as it was reported at a specific time.

{{< image src="/images/HackASatDescription.jpg" alt="Login" position="center" style="border-radius: 8px;" >}}

The coordinates of the satellite are given to us in [TLE format](https://en.wikipedia.org/wiki/Two-line_element_set). While you can manually parse its values, I opted to use a script that utilizes pyOrbital:

```
TODO: SCRIPT
```

Parsing the TLE format gives us the satellite's location in latitude and longitude, alongside several other key info. The challenge's description asks us to arrange the satellite's (Google Earth) camera in the exact same position as it was, facing the Washington Monument, with very little room for error. We must essentially recreate the orientation of the satellite at its given position at the given time. Since we're using Google Earth, we are given a KML file that helps us load data that affects the virtual camera. 

In the KML file, we are given a specific tag known as ``<LookAt>``. Some research into the KML documentation reveals that the ``<LookAt>`` tag changes the object that the camera is focused on. It can function similarly to another attribute known as ``<Camera>``, which changes the actual camera position. Both tags can be used to try and position our satellite to perfectly align to the Washington Monument, but the ``<Camera>`` tag deals with less sub-attributes rather than ``<Lookat>``. In this solution, I opted to use the ``<Camera>`` tag, however, the initial solve my team did the ``<LookAt>`` tag. I will briefly outline the solution with ``<LookAt>`` afterwards.

## The Camera Tag

``<Camera>`` has 7 sub-attributes:

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

The first three (longitude, latitude and altitude) we can parse from the TLE data from above. However, for heading, we would need to do some math. 

The heading is an angle that defines the orientation of an object relative to the north pole of earth. 

TODO: SHIT ABOUT CALCULATING HEADING

Now, when we plug in all our values into the sample KML file they provided for us, we should appear at the same location the satellite was at, with the camera turned towards the Washington Monument in the exact same orientation and positioning as the satellite was at the given time. 

TODO: KML FILE PICTURE

Doing so in Google Earth, the link that we specified in the KML file refers to the server to check our values against theres, and we are rewarded with a (randomized) flag :) 


TODO: GOOGLE EARTH PIC

This was less a "hacking" challenge and more a mathematics one. I'm always down to learn more about the world of math, and hopefully I can expand my blog to talk about the unique things I come across in the world of complex mathematics!

Jam

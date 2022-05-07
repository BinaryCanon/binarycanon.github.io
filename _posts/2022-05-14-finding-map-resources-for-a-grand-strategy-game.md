---
title: Finding Map Assets for a Grand Strategy Game
date: 2022-05-14 00:00:00 +0900
categories: [Art, Map]
tags: [assets]     # TAG names should always be lowercase
pin: true
image:
  path: /assets/img/posts/20220514/krita-combo.jpg
---
# Finding the right resources
It took me several days to find resources that could fit together. Sometimes I could find a beautiful earth texture, but the SVGs files of the countries in Wikimedia didn't fit. Finding a normal map to fit the texture was another challenging task. I did several combinations and weird things, but I have something good enough to work with.

<h2>Texture Map</h2>
<a href="http://www.shadedrelief.com/natural3/pages/textures.html" target="_blank">Natural Earth III</a> - A beautiful texture by Tom Patterson. According to the license: <i>All Natural Earth III data (and images) found on this website are in the public domain</i>.

> I'm using <i>"Earth without clouds or Arctic Ocean ice"</i>.
{: .prompt-info }

![Earth Texture](/assets/img/posts/20220514/earth_tex_no_ice_clouds.jpg)
_Earth without clouds or Arctic Ocean ice_

<h2>Normal Map</h2>
<a href="https://www.highend3d.com/downloads/3d-textures/c/16k-earth-w-4k-moon-free#dialog_license" target="_blank">Highend3d</a> - I'm not 100% about this, but it's fine for now. At the time of writing, the license is MIT.

![Earth Normal Map](/assets/img/posts/20220514/earth_normalmap.jpg)
_Download the original to see the difference in pixels_

<h2>Countries SVG</h2>
That took me the longest, and it includes two steps.
<ol>
<li>
Wikimedia's SVGs require heavy modifications. <a href="https://www.naturalearthdata.com/downloads/" target="_blank">Natural Earth Data</a> fits the most with common earth textures (and normal maps). However, you need to have GIS software such as <a href="https://www.qgis.org/" target="_blank">QGIS.</a> Thanks again to Tom Patterson and the various contributors behind this project. License: Public domain.
Here's a <a href="https://www.naturalearthdata.com/http//www.naturalearthdata.com/download/110m/cultural/ne_110m_admin_0_countries_lakes.zip" target="_blank">direct link</a> to the data I'm using. Also, an <a href="https://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/" target="_blank">essential introduction </a> to using it within QGIS. However, when you are about to export the SVG <a href="https://www.youtube.com/watch?v=xZYbZi7Tpys" target="_blank">newer methods</a> can make your life easier.
</li>
<li>
Since the normal map I have doesn't emphasize the continents' depth, we can only see high terrains on the continents. As a result, the oceans and the continents seem like they are on the same height level. A quick fix is to create a normal map in Krita using the SVG from the previous step. Then we combine it with the normal map we have.
</li>
</ol>

![SVG file for the countries](/assets/img/posts/20220514/countries-svg-on-tex.jpg)
_SVG fits almost perfectly_

![Creating a normal map from SVG](/assets/img/posts/20220514/normal-map-from-SVG.jpg)
_Generating a normal map in Krita from the SVG file. Combines with the original normal map, 37% opacity_

![Krita combo](/assets/img/posts/20220514/krita-combo.jpg)
_Combination of wireframe (Countries SVG), normalmap and texture_

>Perhaps using Inkscape is faster since, eventually, I'll need to edit each country separately.
{: .prompt-tip }

Brb.
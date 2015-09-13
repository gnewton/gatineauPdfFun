
#The Problem

I was wanting to go to the Gatineau Park for a hike, and was looking around for some good maps of the trail.

While I found a number of the offical maps:
-
-
-

I found this PDF map that I liked: [http://www.xcottawa.ca/img/maps/gatineau_trail_distances.pdf](http://www.xcottawa.ca/img/maps/gatineau_trail_distances.pdf)
at the [Team XC Ottawa site](http://www.xcottawa.ca/about_us.php)

Here is a PNG of the PDF map:

![PNG of the PDF map](gatineau_trail_distances.png)

However, as I wanted to actually print the map, there were some issues with this map:

*It inefficiently uses the real estate on the page, due to its rotation and scaling.
*The text on the trails denoting trail length were too small
*Some of the trails at the north and south end of the park were not of interest to me
*It had a pale blue background that I did not want to have, both from a readability perspective, and so as not to deplete my printer ink.




# Pre-requisites
* PDFjam: [http://www2.warwick.ac.uk/fac/sci/statistics/staff/academic-research/firth/software/pdfjam/](http://www2.warwick.ac.uk/fac/sci/statistics/staff/academic-research/firth/software/pdfjam/)
* pdfTeX, pdtLaTeX, LaTeX package 'pdfpages'
* pdf2ps: [http://linux.about.com/library/cmd/blcmdl1_pdf2ps.htm](http://linux.about.com/library/cmd/blcmdl1_pdf2ps.htm)
* ps2pdf: [http://ghostscript.com/doc/current/Ps2pdf.htm](http://ghostscript.com/doc/current/Ps2pdf.htm)
* psselect: part of psutils [https://github.com/rrthomas/psutils](https://github.com/rrthomas/psutils)

# The Solution

1.Rotate, shift and scale
2.Remove backgound colour


## Rotate, shift and scale
This is pretty straight forward, although a number of iterations before I got to the particular scale, rotation, and translating.

*Use `pdfjam` to rotate, scale, and shift the original PDF:
```
$ pdfjam  --suffix rotated --scale 8 --angle '300' --offset '-1cm -2.5cm'  gatineau_trail_distances.pdf
```

PNG of PDF output:
![PNG of the PDF map](gatineau_trail_distances-rotated.png)

## Remove the blue background
1. Find the blue colour with some image editor. I used `gimp` and its colour picker indicated the RGB for the background is: 217,240,211, or in the 0..1 range: 0.851 0.9412 0.8275
2. Convert to postscript
```
$ pdftops gatineau_trail_distances-rotated.pdf
```
Producing gatineau_trail_distances-rotated.ps

Now, 

Now, open the postscript file and search for "0.851". 

Found on line 1265:
```
/DeviceRGB {} cs
[0.851 0.9412 0.8275] sc
/DeviceRGB {} CS
```

Change this to:
```
/DeviceRGB {} cs
[1 1 1] sc
/DeviceRGB {} CS
```

Preview with ghsoscript as PNG:
```
$gs -dSAFER -dBATCH -dNOPAUSE -sDEVICE=png48 -dDOINTERPOLATE -dTextAlphaBits=4 -dGraphicsAlphaBits=4 -r600x600 -dMaxBitmap=500000000 -dAlignToPixels=0 -dGridFitTT=2 -sOutputFile=a.png ./gatineau_trail_distances-rotated-whitebg.ps
```

PNG of PS output:
![PNG of the PDF map](gatineau_trail_distances-rotated-whitebg.png)

## PDF
The final PDF is here: [gatineau_trail_distances-rotated-whitebg.pdf](gatineau_trail_distances-rotated-whitebg.pdf)

You will notice there are two pages, one with the map, and a second with a number of error messages from ghostscript:
```
ERROR:
invalidfileaccess
OFFENDING COMMAND:
.findfont
STACK:
r
/usr/share/X11/fonts/Type1/UTBI____.pfa
--nostringval--
true
/NimbusMonL-Regu
/Courier
-mark-
-mark-
-mark-
-mark-
-mark-
-mark-
-mark-
-mark-
-mark-
-mark-
-mark-
```

The second error page is due to some font issues that I have not tracked down yet, but I think has to do with the `pdf2ps` conversion.



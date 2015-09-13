
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
1. Convert to postscript
```
$ pdf2s gatineau_trail_distances-rotated.pdf
```
Producing gatineau_trail_distances-rotated.ps

Now, open the postscript file and look for "setrgbcolor". In most postscript files, the author has aliased this to a shorter function.
In this file, this is done on line 3807
```
/rg/setrgbcolor load def
```

Now, look for where this function is used, with a colour that is similar to the background color, and before some drawing commands for a simple rectangle.

Some searching finds this on line 17983:
```
0.851563 0.941406 0.828125 rg
0.2 i
-10654.7 5304.08 m
-2419.21 -8960.5 l
16040.8 1697.18 l
7805.36 15961.8 l
-10654.7 5304.08 l
f
```

Change the first line to:
```
1 1 1 rg
```

Preview with ghsoscript as PNG:
```
$gs -dSAFER -dBATCH -dNOPAUSE -sDEVICE=png48 -dDOINTERPOLATE -dTextAlphaBits=4 -dGraphicsAlphaBits=4 -r600x600 -dMaxBitmap=500000000 -dAlignToPixels=0 -dGridFitTT=2 -sOutputFile=a.png ./gatineau_trail_distances-rotated-whitebg.ps
```

PNG of PS output:
![PNG of the PDF map](gatineau_trail_distances-rotated-whitebg.png)

## PDF Issues
The PDF is here: [gatineau_trail_distances-rotated-whitebg.pdf](gatineau_trail_distances-rotated-whitebg.pdf)

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

The second error page is due to some font issues that I have not tracked down yet, but I think have to do with the `pdf2ps` conversion.



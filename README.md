
# The Problem
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

*Use `pdfjam` to rotate, scale, and shift the original PDF:
```
$ pdfjam  --suffix rotated --scale 8 --angle '300' --offset '-1cm -2.5cm'  gatineau_trail_distances.pdf
```

PNG of PDF output:
![PNG of the PDF map](gatineau_trail_distances-rotated.png)




So, the Monday GRG meeting this morning made me realize that my data filtering is not up to par. The first thing I'm going to do is fix it. I do understand a little bit better what I'm going for now, and for Rory's sake I'll be detailed in this post about what I'm using to filter. 

I also have a better plan of attack for the rest of the week than usual. I do feel like this project tends to follow it's own path that I only have so much control over, just like life, but also like life, planning is always good but I'm prepared to follow what needs to happen next rather than try the impossibe and bend the project's path to my whims. Like I already said, fixing my data filtering is the first item on the agenda, next comes converting the pixel coordinates in dolphot's output to RA and Dec for astrometric matching between different observations, matching the images, then the ultimate goal of making a color-magnitude diagram.

# Data Filtering
So I'm just going to start over. The first two filtering steps will be the same -- getting rid of the individual data to focus on the overall data for a field and getting rid of anything that doesn't have an object type of 1 -- then I'm going to get rid of anything with sharpeness outside of -0.3 < sharpness < 0.3. Actually, let's do that now by typing ` awk '$7 >= -0.3 && $7 <= 0.3' goodstars.txt > notsharp.txt` and pressing enter in Ubuntu. I've now reduced the number of sources to 107,922.

For some reason I had a little bit of trouble installing Python. I hadn't yet needed it since changing my laptop so needed to get it again, which usually doesn't bother me because I like have a clean directory structure and delete things a lot. It seemed fine at first but then astropy wouldn't install properly, but I got through it. I just unistalled Anaconda, restarted my laptop because it was acting weird in other ways, too, and reinstalled Anaconda. That worked.

The reason I decided I needed Python again was to overlay where dolphot thinks the sources are onto the drc image so that I can check how things are going so far. I still am detecting ~100,000 sources so I already know I still have way too many (I'm looking for a number of stars about one order of magnitude smaller than what I have now), but it's good to keep checking. 
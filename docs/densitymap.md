#Introduction
Since last few months I have been thinking of trying a new way to create density map. This one will use geohash instead of dealing with geometry points and linestrings. My last experiement was to create line segments and then make sure which cell it touched. (The cells are usually quarter degree but it can be of any measurement).
    This method is not very flexible. Every time we needed to create a new heatmap with a finer resolution I had to re-create the cells(dividing the whole world) into a new table; then index the same. This also means for a smaller cell size the program gets more and more cpu heavy. just because I had to carry the quadtree index of the cells in memory.

##How about using geohash
Geohash is a alphanumeric code for every longitude and latitude on earth. Basically the workd is divided into 4 quadrant and each of the quadrant into 4 more. (you get the picture)
The benefit is two fold. I like when my output is serializable(I mean I don't have to write a spark serialization/deserialization code). And neighbor calculation is a no brainer. All I have to do then is create a function that takes a point and emit a geohash. An accumulator then keep count of geohash VS the count and voila. 
##Can we have a bollywood movie with out a villain?
But wait.. Will that function which takes geometry take line segment as input? If so will it give an array of geohashes? That will be desirable though. But How?

Yes, I think we can. How about take the starting point of the line segment and go a small direction in both x and y axis. (Based on how fine you want your cells to be). Then for each point find the geohash and do it iteratively till the end point is not reached. Just as I finished writing this line I see a bug. What if a line just touches barely a cell and in the process of incrementing x,y a little bit we move too much and skip that cell.

###Take 2
May be this will work. Take the starting point. Find all it's neighbours. Find which one of those neighbours touch our line. Do it recurrsively and out comes the complete set of geohashes.

Stay tuned for the implementation of the idea in a working code.


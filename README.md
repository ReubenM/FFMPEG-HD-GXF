This is a pair of patches for supporting HD resolutions (720p and 1080i) in 
FFMPEG. They are part of a larger set of patches I made to fix what was, at the 
time, a badly broken GXF implementation. It's still not great, but it's good 
enough that it will play correctly with Grass Valley hardware.

I haven't pushed to get these 2 patching merged because there is currently no 
direct way to tell the difference between some of the progressive and 
interlaced. FFMPEG's API exposes the timebase property, but there is no flag or 
bitfield to query and find if the video stream is interlaced or not. So in some 
cases the same timebase is returned and there is no means to differenciate if 
the content is progressive or interlaced. In ffmpeg, everything in timebase is 
based off of frames, so 1080i@59.94 will return the same timebase as 
1080p@29.97. But obviousely these two streams need to get tagged differently in 
GXF.

The correct fix it to add a generic bitfield to indicate the the framing 
structure of the video stream. It could be used to set bits for things like 
progressive, interlaced, field dominance, PsF, 3D interleaved, 3D frame packed, 
and probably more I'm not thinking of. But that would require modifications in 
many more places than just the GXF code. And I haven't the motivation now that 
all the Grass Valley gear I needed to support has long since been sold.

However, I get several requests a year from people needing to use ffmpeg to 
support HD GXF content. So I've placed it here to be easier to find. It applies 
to the current HEAD of the development code base (as of May 2014). When the 
patches at some point fail to apply, drop me a note and I'll try to update 
them. Currently the should allow you to encoded content that is compatible with 
HD-SDI (720p and 1080i). However do not try to use with with 3G-SDI (1080p) 
content, or it will have unexpected results due to video streams being 
improperly labeled.

Also, the API is always evolving, so the ability to flag interlaced and 
progressive content without having to probe the streams at the codec level may 
get implemented at some point. If so let me know and I'll see if I can update 
it to use that functionality, and perhaps get it merged into the main 
development tree. I don't track LibAV very closely, but it shouldn't be too hard 
to apply it to that code base.

The first patch adds support to be able to mark streams as having a 16:9 aspect 
ratio rather than 4:3. The second add support for the HD resolutions.

Apply in the root of the code base using

cat <file.patch> | patch -p1
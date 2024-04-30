# Q5 - Ice Tornado spell

I recreated this example by modifying the Eternal Winter spell to loop through an array of random-looking combat areas and repeatedly casting ice tornadoes on them.
I couldn't get the look exactly right - in the original video, it looks like the spell is somehow shifted by half a grid tile, and I couldn't get the same distribution of large vs. small tornadoes. Additionally, my client doesn't show "Zelek says: frigo" for some reason... huh!
If I had more time to work on this problem, I would have investigated how the tornado sprites are rendered to get a clearer picture of how to design the combat areas.

# Q6 - Dash ability

I wasn't quite able to finish this one! I added a new talkaction which allows the user to dash - it checks spaces in a line ahead of the player, and moves the player as far as possible (up to a configurable max distance, and at a configurable rate) until an obstacle is hit. One caveat is that the code only checks for a clear path once at the beginning of the command; if a creature moves into the path during the dash, for example, the dash will continue.
Unfortunately, I wasn't able to figure out how to successfully load shaders in time. I looked around and noticed that the OTCv8 project adds a shader module which provides "outfit shader" functionality - I had more time, I would have implemented a similar module and then written a shader to produce the desired effect. In this case, a fragment shader could duplicate the player at lower opacity to create the blur effect, and could also check for edges in order to add the red outline (the latter was attempted in [this thread](https://otland.net/threads/help-with-shader-outline.283875/), and I likely would have implemented it similarly).

# Q7 - Jump! window

This one was a little bit hacky, but the final result came together nicely! I accomplished this recreation by adding movement logic to the UIWidget draw function, which can be selectively turned on with another function called startMoving - then, I hijacked the quest log button functionality to create a new window with a button, and finally called startMoving on the button to kick things off.

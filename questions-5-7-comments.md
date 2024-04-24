# Q5 - Ice Tornado spell

I recreated this example by modifying the Eternal Winter spell to loop through an array of random-looking combat areas and repeatedly casting ice tornadoes on them.
I couldn't get the look exactly right - in the original video, it looks like the spell is somehow shifted by half a grid tile, and I couldn't get the same distribution of large vs. small tornadoes. Additionally, my client doesn't show "Zelek says: frigo" for some reason... huh!
If I had more time to work on this problem, I would have investigated how the tornado sprites are rendered to get a clearer picture of how to design the combat areas.

# Q7 - Jump! window

This one was a little bit hacky, but the final result came together nicely! I accomplished this recreation by adding movement logic to the UIWidget draw function, which can be selectively turned on with another function called startMoving - then, I hijacked the quest log button functionality to create a new window with a button, and finally called startMoving on the button to kick things off.

# Q5 - Ice Tornado spell

I recreated this example by modifying the Eternal Winter spell to loop through an array of random-looking combat areas and repeatedly casting ice tornadoes on them.
I couldn't get the look exactly right - in the original video, it looks like the spell is somehow shifted by half a grid tile, and I couldn't get the same distribution of large vs. small tornadoes. Additionally, my client doesn't show "Zelek says: frigo" for some reason... huh!

# Q7 - Jump! window

This one was a little bit hacky, but the final result came together nicely! I accomplished this recreation by adding movement logic to the UIWidget class, which can be selectively turned on with startMoving - then, I hijacked the quest log button functionality to create a new window with a button, and finally called startMoving on the button to kick things off.

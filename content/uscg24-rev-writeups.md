+++
title = "USCG Season IV Rev Write-Ups"
date = 2024-08-02

[taxonomies]
tags = ["write-up", "ctf", "uscg", "rev"]
+++

For the USCG Combine, this weeks focus was rev! Here are some writeups for the challenges that were given for the combine.
<!-- more -->

# StarTrek 1
{{ figure(src="/img/uscg24-rev-writeups/0.0_running_program.png", caption="This is what we are greeted with when first starting the program.") }}
## Challenge Description
For this challenge, we were given a fairly lengthy amount of instructions and background:
> You are a remote navigator directing Captain Kirk, who is commanding the USS Enterprise, and Captain Spock, who is commanding the USS Endeavour. Your mission is to guide them on a journey 100 galaxies away to an ancient, advanced civilization known as the "Architects" who have hidden a powerful artifact. This artifact, known as the "Quantum Key," has the potential to unlock new dimensions, granting unparalleled knowledge and power to its possessor...
> But your ships' warp drives are limited and as you journey through the galaxies, you discover that some contain ancient portal mechanisms that can instantly transport you to another galaxy. These portals are unpredictable and may send you further ahead (Slipstream Portals) or behind (Wormhole Portals) your current position. Can you strategically navigate these worlds and accomplish your mission on limited fuel?

As a TLDR, our main goals are to get both ships to galaxy 100 with limited fuel, where some galaxies can send us forward or send us back, and each ship takes turns.

If this sounds familiar, it may be because you have heard of Snakes an Ladders, which has a smiliar concept, where players take turns rolling a dice, and depending on the square, will either go further with a snake, or back with a ladder.

## Initial Analysis
As with any program, using Ghidra is my go to (though I hear the cool kids use Binary Ninja). In Ghidra, there looks to be quite a lot going on, however this makes sense as this challenge is a static reversing challenge, and a lot probably goes into making sure the flag isn't easily obtainable unintentionally.

{{ figure(src="/img/uscg24-rev-writeups/0_ghidra_dice.png", caption="This is the logic that shows 'rerolling'.") }}
In the above logic, there are two things that are important to acknowledge:
1. The "course heading" isn't actually used, but is vulnerable to a format string vulnerability.
2. The amount of galaxies to jump is actually determined with a random number 1-6... (huh, just like Snakes and Ladders!)

The 1st one isn't too useful for us now (apparently there was a chance for this challenge to be hosted remotely, which would make this more valuable), but the second is pretty important. What it means is for each turn, we can basically reroll the dice as many times as we want by telling the captain to change his trajectory.

We can now get some sort of idea for the solve path:
1. Get the galaxy map
2. Compute the paths/rolls both ships need to make it to galaxy 100, without losing gas
3. Manipulate the random rolls to match the rolls computed in previous step
4. Profit

For step 3, we have a few options. One can be to use the format string vulnerability, and another is to override the `rand()` function being called, to return the steps we want it to immediately, once we find them.

## Solution
Going with our plan, we need to first actually find the map. Running the program a few times, and mapping by hand will begin to show that the map is not dynamic and actually static! This means it must be somewhere in memory when the program is running, possibly as a local variable.

{{ figure(src="/img/uscg24-rev-writeups/1_RBP.png", caption="Base pointer as shown in pwndbg.") }}
Using a program like pwndbg, we can skip over to the initial `jump` function and check the local variables, which should be around the base pointer, RBP. 

{{ figure(src="/img/uscg24-rev-writeups/2_Map_Mayb.png", caption="Zeroed out area can indicate some sort of array, which matches what a map data structure may be!") }}

Examining the memory at the base pointer, we can see a very interesting and long mostly zeroed out section, starting at `0x7fffffffd5b0`. In C/C++, arrays are initialized to contain all 0s until they have values assigned. While this is not definitive proof that its the map, we can try to look into it even further!

{{ figure(src="/img/uscg24-rev-writeups/3_Possible_Map.png", caption="This being a fairly large array, matches up with the size the board should be.") }}

Assuming the first double word is index 0, if we convert hex to decimal, we can get for the first few mappings:
```
0 -> 0
1 -> 0
2 -> 0
3 -> 0
4 -> 75
5 -> 15
...
```

{{ figure(src="/img/uscg24-rev-writeups/4.0_map_confirmed_mayb.png", caption="This matches what we found!") }}
What is interesting is that if run the program a bit, you will notice that these mappings are actually the slipstreams and wormholes. In the above image, you can see that when in galaxy 4, you will end up in galaxy 75.

So essentially, when the program runs, we start at index 0, we get a random value 1-6, and when we go to our current index plus this random value, we will either stay there if its a 0, or jump/teleport to the given index. 

{{ figure(src="/img/uscg24-rev-writeups/4_slip_to_41_is_hex_29.png", caption="There is one last behavior: Taking a jump/teleport will make it unusable in the future for BOTH players.") }}
{{ figure(src="/img/uscg24-rev-writeups/5_hex_29_removed_confirming_this_is_map.png", caption="Running a diff of the map from memory from before and after the usage of it, shows that it gets replaced by a 0, effectively removing its future use.") }}

With this knowledge, we essentially need to make a script that will take the map, and try to find the shortest path for both ships to get to galaxy 100, each moving 1-6 galaxies at a team each turn, and they take turns with each other on the same map.

## Final Thoughts

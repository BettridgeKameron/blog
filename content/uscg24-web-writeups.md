+++
title = "USCG Season IV Web Write-Ups"
date = 2024-07-04

[taxonomies]
tags = ["write-up", "ctf", "uscg", "web exploitation"]
+++

US Cyber Games Season IV Open was a very fun CTF, with some cool challenges, where I ended up placing 6th overall, getting me into the Combine! I have made two writeups for web challenges, mostly for the Combine.
<!-- more -->

# Ding-O-Tron
{{ figure(src="/img/uscg24-web-writeups/ding_home.png", caption="Ding-O-Tron Home page") }}
## Challenge Description
Ding-O-Tron's website is fairly simple, with a png of a bell, a counter, and a claim that if you get over 9000 dings, there will be a reward (the flag).

## Initial Analysis
I originally assumed that this would be a Web Assembly reversing challenge, so wanting to be lazy and work on other challenges (since this was the first challenge I saw), I made a for loop that calls `window.ding()` with a delay, which is equivalent to clicking the button as shown below.
{{ figure(src="/img/uscg24-web-writeups/ding_window-ding-func.png", caption="onClick calls window.ding(), so clicking the image and calling the function manually are the same.") }}

However, when coming back I was left with this fun message, which I half expected:
{{ figure(src="/img/uscg24-web-writeups/ding_tsuto-troll.png", caption="After getting 9000 dings clicking or with code, this appears.") }}

## Solution
After getting that message, I decided to see if there was anything else before looking into the wasm and found that the solution was much simpler.

### The Window Function
As noticed before, `window.ding()` is the function used to make a new ding. Checking the network tab when dinging, shows no network call, which means that all of the logic should be client side. Checking the js shows that "ding" is not a function to be found in any of the js files, so this must be something that was added by the web assembly.
{{ figure(src="/img/uscg24-web-writeups/ding_ding-not-present.png", caption="None of the js files provided contain the ding function that is called.") }}

Additionally `ding()` is called as `window.ding()` specifically when `ding()` alone would work. We possibly confirm that the webasm also contains ding when debugging the wasm in Firefox.
{{ figure(src="/img/uscg24-web-writeups/ding_found-ding.png", caption="The webasm contains a ding wrapper, which could be the source of the ding function.") }}

Checking the "window" object to see if there may be other functions that were added and as seen below, there is a suspiciously named secret function found in the window object.
{{ figure(src="/img/uscg24-web-writeups/ding_window-object.png", caption="Since this window object had the ding function, could there be other hidden functions?") }}
{{ figure(src="/img/uscg24-web-writeups/ding_sus-secret-func.png", caption="It turns out, there are secret functions!") }}

calling this will get us the flag!
{{ figure(src="/img/uscg24-web-writeups/ding_winner.png", caption="ding ding ding we have a winner.") }}

## Final Thoughts
This was a nice challenge that allowed me to get some easy points. I am glad I didn't need to reverse engineer the wasm or it may have taken a lot more time. Since I saw some people solve it in under 5 minutes, I should have taken that as a hint that it was much easier than I thought, but that didn't matter at the end of the day.

# Secure File Storage
Work-In-Progress
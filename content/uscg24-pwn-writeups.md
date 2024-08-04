+++
title = "USCG Season IV Pwn Write-Ups"
date = 2024-08-02

[taxonomies]
tags = ["write-up", "ctf", "uscg", "pwn"]
+++

For the USCG Combine, this weeks focus was pwn! Here are some writeups for the challenges that were given for the combine.
<!-- more -->
---
# Binary Blast
## Challenge Description
> Ready for a blast from the past? Navigate the MIPS landscape and watch out for those sneaky format strings. Beware of fake flags—only the real one will do!

This challenge makes it fairly clear that it is a format string pwn, but instead of being on an architecture we may be used to, it is on MIPS.

## Initial Analysis
{{ figure(src="/img/uscg24-pwn-writeups/main_ghidra.png", caption="There's not much here.") }}
As usual, I begin my binary analysis by using Ghidra to see if the path may be clear, and on first look at main, we are greeted with a super simple main function. All this does is take the user input, and run scanf on it, and exit, and nothing more.

{{ figure(src="/img/uscg24-pwn-writeups/winner_ghidra.png", caption="We need to call this somehow.") }}
Looking at the other functions, we see an aptly named `winner` function, which appears to open a file assuming we can call it with the correct parameters. 

Our plan to solve this seems pretty straightforward, as we just need to abuse the format string vulnerability in order to call the winner function with specfic parameters.

{{ figure(src="/img/uscg24-pwn-writeups/clean_winner_ghidra.png", caption="Changing the values to a different form shows common ctf strings like 'deadbeef', which is a sign this is the right path.") }}

Changing the types, we can get something that becomes much more clear. For format strings, one thing we can try doing is to overwrite the `_exit` function with something else (such as `main`), which will allow us to execute multiple payloads, since it will essentially just loop back to main instead of exit.

## Solution

## Final Thoughts

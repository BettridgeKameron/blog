+++
title = "USCG Season IV Rev Write-Ups"
date = 2024-08-02

[taxonomies]
tags = ["write-up", "ctf", "uscg", "rev"]
+++

(WIP)
For the USCG Combine, this weeks focus was rev! Here are some writeups for the challenges that were given for the combine.
<!-- more -->
---
# Ding-O-Tron
{{ figure(src="/img/uscg24-web-writeups/ding_home.png", caption="Ding-O-Tron Home page") }}
## Challenge Description
Ding-O-Tron's website is fairly simple, with a png of a bell, a counter, and a claim that if you get over 9000 dings, there will be a reward (the flag).

## Initial Analysis

## Solution

## Final Thoughts

---
# Secure File Storage
{{ figure(src="/img/uscg24-web-writeups/sfs_file-page.png", caption="Secure File Storage Main page.") }}
## Challenge Description
Secure File Storage's website is a simple website where users can register and upload files, that will be stored encrypted on the server.

## Initial Analysis
This was probably my favorite web challenge! When I originally solved this for the Open CTF, I was able to find an unintended vulnerability that meant you could bypass doing any Crypto. However, for the Combine, this was patched, but there used to be another column "encrypted" that was 0 or 1, that you could abuse to find the encrypted value of anything, bypassing the need to bitflip.

## Solution
This challenge involves exploiting a SQL injection in a function used at two endpoints, and exploiting AES CBC to modify ciphertext without having the key.


## Final Thoughts
This was probably my favorite web challenge out of all of them. This really changed the way I think about SQL injections, as I originally didn't think about the fact you could just provide any arbitrary value to return, bypassing auth and such. The unintended vulnerability was also fun to find originally, but doing it the intended way with the bit flipping was also a nice experience.

# Conclusion
Overall, the web challenges this year was really fun, and I look forward to the combine. I am hoping to make the US Team focusing on web :D.

If you had any questions, feel free to reach out to me.

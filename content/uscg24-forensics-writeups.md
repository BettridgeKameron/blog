+++
title = "USCG Season IV Forensics Write-Ups"
date = 2024-08-02

[taxonomies]
tags = ["write-up", "ctf", "uscg", "forensics"]
+++

For the USCG Combine, this weeks focus was forensics! Here are some writeups for the challenges that were given for the combine.
<!-- more -->
---
# Timing Is Everything
## Challenge Description
> Timing is everything....

Timing Is Everything leaves us with the above description and a pcap file, with no further information.

## Initial Analysis
{{ figure(src="/img/uscg24-forensics-writeups/all_packets_are_the_same_except_time.png", caption="The raw packet data is exactly the same.") }}

Upon opening the pcap in wireshark, one of the first things to notice with the packet capture, is that every single packet actually has the same raw data, as shown by the hex never changing no matter which packet you inspect.

However, we can see that the only thing that does change is when the packets are sent (not a part of the raw data). Based off the name of the challenge and description, it is highly likely that we can focus on is the timing the packets were sent.

{{ figure(src="/img/uscg24-forensics-writeups/interesting_frame_deltas.png", caption="The timing in between each packet is conveniently 0 to 128, which is ascii range...") }}

Since this is a forensics challenge, we need to try and find ways someone could exfil data. Something that I noticed fairly quickly was the the frame deltas (timing in between packets) had very familiar values, and appeared to be ascii, so I decided to extract them to further investigate.

{{ figure(src="/img/uscg24-forensics-writeups/extracting_frame_timing.png", caption="Using tshark, a commandline tool similar to wireshark, makes extracting these values a breeze.") }}

In CTF competitions, it's important to know what you're looking for - in this case, it's a flag with a specific format (as a real world comparison, you may look at things like the byte size of an ssh key)! Since there are about 30 packets, that roughly fits the length of a flag too. 

Another thing to note is we know we want to find something in the format of `SIVUSCG{...}`. From experience dealing with ascii many times, numbers appear to match up with this flag format.

{{ figure(src="/img/uscg24-forensics-writeups/ascii_pattern_recognition.png", caption="With Cyberchef, I can quickly confirm that `SIVUSCG` in its decimal form matches up with the frame timing!") }}

Since I am now fairly confident that the frame deltas once converted from decimal to ascii will give the flag, I can now program the solution!

## Solution

{{ figure(src="/img/uscg24-forensics-writeups/extracting_ascii_values.png", caption="First, lets get this into a format we can better work with.") }}

Bash has a lot of really nice tools for working with and playing with data! here is a quick break down of the commands so far:
`tshark -T fields -e 'frame.time_delta' -r timingiseverything.pcap`: This command simply extracts the frame deltas from the pcap, new-line delimited.

`tail -n+2`: this will remove the first line, since it is null and not needed.

`cut -c3-5 `: this will get us to ascii values we know and love.

There are many ways you could get to this point as well, such as treating each line like a number and multiplying by 1000. Now we just need to convert each line to its ascii form, which we can use AWK to do!

{{ figure(src="/img/uscg24-forensics-writeups/awk_for_the_win.png", caption="With AWK, data manipulation in the terminal becomes a breeze!") }}

And just like that we get the flag! For the last awk command `awk '{printf "%c", %1} END {print ""}'`, this works by taking each line (`%1`) and converting it from a number into a char (`%c`). `END {print ""}` is something I added only so it would add a new line to the end to keep the terminal clean, but is not needed!

## Final Thoughts
While a simple challenge, I think it was a very cool one that demonstrates that data can be exfiltrated without even modifying the actual packets' data! This is definitely something with real-world applications, and shows some of the creative ways adversaries may try to exfiltrate data, as this is something that could have easily been made difficult to notice in the real-world (such as including random data in the packets, longer time intervals, more noise, etc).

If you had any questions, feel free to reach out to me.

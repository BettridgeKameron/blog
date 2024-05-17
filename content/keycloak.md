+++
title = "SSO is Live"
date = 2024-05-16

[taxonomies]
tags = ["server", "kubernetes"]
+++

I was finally able to get Keycloak for SSO on k8s!
<!-- more -->

After a lot of trial and error, I was able to cram Keycloak onto my server, which everything still runs under 4GB of ram! Changing from 512MB swap to 8GB of swap actually saved about 2 GB for me.

Still have to configure Mailu to work with it, as it appears it may be difficult, but it fully works with Gitea now. 

If there is any interest, I will probably make the helm chart values.yaml files for all of my services public, and possibly make a write up/video on how you could make something similar to this. Before I do this, I still want to make sure I am following best practices, as I know there are some "corners" I have cut.

Besides polishing this up to hopefully make a guide, I don't have much other plans for this. I recently bought an Minisforum MS-01 (like, 96GB ram and super packed), which makes my Linode server seem pointless to me besides being a public IP! Trying to think of how I can migrate everything to my home lab server now, but still have a public IP for my mail server and such.

+++
title = "Test post"
date = 2023-12-10

[taxonomies]
tags = ["test", "misc"]
+++

If you see this, then my blog is working :D. Sadly, there isn't much yet, but it's a start!

If you click read more though, maybe there will be more...
<!-- more -->

Even clicking more, there isn't much here unfortunately.

However, if your seeing this, that means that I finally got the CICD to work too!

{{ figure(src="/img/test.png", caption="Here is a test caption for my test image of me testing.") }}

Will be creating my first actual blog post soon though. As for now I think I may also add a step in my Drone pipeline that uses exiftool to remove all metadata from files in my static/img folder.

```py
def test(number: int) -> None:
    """
    Test function to test syntax highlighting (I should change the theme for it)

    :param number: An integer number to be included in the test statement.
    :type number: int
    :rtype: None
    """
    print(f"Testing, with some number: {number}.") # This prints out some words

for x in range(20):
    test(x)
```

Will also probably make a post on how I managed to make the blog, mail server, gitea, ci, etc, mostly as documentation for myself because I have just spent a few days recovering from a technical disaster (oops, but as you are reading this, I fixed it!).

Update again, I have refactored the yaml files to be more accurate. Hopefully I can make it follow best practices and get it to be at a place where im comfortable with releasing it.

Another update, because I broke the server again. But now it is fixed!

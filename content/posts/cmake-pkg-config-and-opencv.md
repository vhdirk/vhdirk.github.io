+++
title = "CMake, pkg-config and OpenCV"
date = "2012-02-11"
+++


I regularly switch between machines to build some software stack, which depends on OpenCV.
Quit bothersome is that OpenCV might be installed at some arbitrary location, and that they changed their library/header structure going from v2.1 to 2.2.

To build it, I'm using CMake, and having tried a lot of different FindOpenCV.cmake files found across the web, I finally discovered that pkg-config comes in quite handy here. (albeit at the cost of losing cross-platformity)

Since I never bothered to version that single cmake file, I'm always looking up how to use pkg-config in combination with cmake over and over again.

And now I got tired of doing just that, so here it is: <http://pastebin.com/CnrSWDM5>

Enjoy (I know I will)

More info: <http://www.vtk.org/Wiki/CMake:How_To_Find_Libraries>

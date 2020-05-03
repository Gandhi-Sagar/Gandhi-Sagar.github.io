---
title: 'How to Install OpenGL on Linux and Windows'
author: Sagar Gandhi
layout: post
permalink: /How-to-Install-OpenGL
categories:
  - Posts
---
<!--excerpt.start-->This guide will help you installing OpenGL. I am only targetting fixed-function pipeline, i.e., we won't test programmable pipeline's functionality after installation.
After the installation, if you use my linked code to test the program, you will see output like below.
<center><img src="/images/glut_sample_output.jpg"></center>
  
Let us get started.


<h2> For Linux Users </h2>  
  
If you are on debian (ubuntu) system  
`sudo apt-get install freeglut3-dev`  
<!--excerpt.end--><a class="anchor" id="read-more"></a>
if you are on RHEL (fedora) system
`sudo yum install freeglut-devel`
    
This will auto-resolve the dependencies including OpenGL itself.  
  
They can be verified using:  
  
OpenGL Libraries and Headers:
  
`ls -l /usr/lib/[arch]/libGL*`
`ls -l /usr/include/GL/*.h`
  
glut Libraries and Headers: 
`ls -l /usr/lib/[arch]/libglut*`
`ls -l /usr/include/GL/*.h`

If output of above commands contain files, you are good to go. Test a simple OpenGL program.

However, if above commands don't work, one must compile freeglut or install Mesa. Get in touch, and I would be glad to help.  
  
Here is a [Simple OpenGL Program](https://github.com/Gandhi-Sagar/OpenGL_Installation/blob/master/opengl_glut_setup_test.cpp). Save it in a file using your favorite editor.  
  
**Compile:**  
`g++ simple_ogl.cpp -o simple_ogl.o -lGL -lGLU -lfreeglut`

**Execute:**  
`./simple_ogl`

And that is it. You should be good to go..
  
  
  
<h2> For Windows Users</h2>
Installing OpenGL on Windows platform is not as lucid as Linux.  
There are multiple options to use compiler - one can use Visual Studio (cl.exe), or g++ compiler using MinGW (Minimum GNU for Windows).  
  
These instructions are for using g++. Here is the birds eye view of steps involved.
  
+ Install MinGW
+ Install freeglut
+ Write an OpenGL program
+ Build and run the executable  
  
Here are the detailed istructions. 

+ Install MinGW
  - Download an executable - [mingw-get-setup.exe](https://osdn.net/projects/mingw/downloads/68260/mingw-get-setup.exe) and execute it.  
  *Note: You need active internet connection*
  - If you are willing the change the installation location, do so, but make sure your location contains NO SPACES.  
  *For e.g. "My Documents" is a BAD location*
  - Next, MinGW setup will update its own catalog, just continue.
  - In the propted Installation Manager, click on Basic Setup
  - Now, right click on each of the package on upper right pane, and click 'Mark for Installation'
  - Then go to installations menu, click on Apply Changes. It will display a confirmation box, click on apply.
  - After all packages are installed, you should a success indicating message.
  - Add YOUR MinGW/bin path to system's path variable. Refer to following steps:
    - Right-click on "My Computer" and select "Properties".
    - Click on the "Advanced" tab, then on the "Environment Variables" button
    - You should be presented with a dialog box with two text boxes. The top box shows your user settings. The PATH entry in this box is the one you want to modify. Note that the bottom text box allows you to change the system PATH variable. You should not alter the system path variable in any manner, or it might cause all sorts of problems for you and your computer.
    - Click on the PATH entry in the TOP box, then click on the "Edit" button
    - Scroll to the end of the string and at the end add ";\<installation-directory\>\bin" (WITHOUT DOUBLE QUOTES), in my case it is F:\Installations\MinGW\bin
    - press OK -> OK -> OK and you are done.
    - Close MinGW installer, and you are done.

+ Install freeglut
  - Download the package: [freeglut-MinGW-3.0.0-1.mp.zip](https://www.transmissionzero.co.uk/files/software/development/GLUT/freeglut-MinGW.zip)
  - Copy this compressed file to "\<MinGW-installation-directory\>"
  - Extract the folder at the same location.
  - You should see a new freeglut folder containing bin, include and lib subfolders and couple of other files.
  - Copy "freeglut.dll" from "freeglut\bin\" to "\<MinGW-installation-directory\>\bin"  
    
Note: As we have already added MinGW's binaries' path to path variable, putting freeglut.dll in that location gives you a freedom of coding from any folder.  
  
+ Write an OpenGL program
  - Well, you can find it [here](https://github.com/Gandhi-Sagar/OpenGL_Installation/blob/master/opengl_glut_setup_test.cpp). Copy-paste or type - you decide.

+ Build and run the executable - 32 bit
  - Execute the following command:
`g++ <filename.cpp> -o <output_file.exe> -I"<MinGW-installation-directory>\freeglut\include" -lopengl32 -L"<MinGW-installation-directory>\freeglut\lib" -lfreeglut
`
    - \<filename.cpp\> - replace this by your program's name
    - \<output_file.exe\> - replace this by the name of the exe that you want
    - \<MinGW-installation-directory\> - is the directory where you installed MinGW
    - Example on my machine:
`C:\Users\Sagar Gandhi\Videos>g++ opengl_glut_setup_test.cpp -o opengl_glut_setup_test.exe -I"F:\Installations\MinGW\freeglut\include" -lopengl32 -L"F:\Installations\MinGW\freeglut\lib" -lfreeglut`
  
  - Run the executable you just created and you should be good to go.
      
+ Build and run the executable - 64 bit
  - Everything as above, just link to the 64 bit library, and in order to run correctly, you must have copied 64 bit dll to MinGW's bin directory.
  - Example on my machine:
`C:\Users\Sagar Gandhi\Videos>g++ opengl_glut_setup_test.cpp -o opengl_glut_setup_test.exe -I"F:\Installations\MinGW\freeglut\include" -lopengl32 -L"F:\Installations\MinGW\freeglut\lib\x64" -lfreeglut`

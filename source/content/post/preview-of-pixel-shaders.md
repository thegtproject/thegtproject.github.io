---
title: Preview of Pixel Shaders
date: 2018-06-19T13:01:55-06:00
markup: markdown
---

>Note: This process is specific to the preview of my shader support feature. It is none-breaking if you're already using pixel.

This post will walk you through the process of getting the Pixel game development library and it's dependencies working correctly. Particularly on Windows this was a hard barrier to get passed. I want to make that easier for newer Go developers to get started with Pixel. Some of the instructions may be obvious to you.

## Windows
---

#### Install Go###


Download the Go amd64 msi installer from https://golang.org/dl/

Install with defaults unless you know what you're doing.

The installer should put the `c:\Go\bin` directory in your PATH environment variable. You may need to restart any open command prompts for the change to take effect. 

#### Install MSYS2

This is the confusing part for many newer developers. Just follow the steps exactly and it should work on your first try.

Download msys2-x86_64 from https://www.msys2.org/

Install with defaults unless you know what you're doing.

Run msys2.exe and update it's components:

`pacman -Syu`

When this step is finished it will tell you to close that window. **Forcibly** do this by clicking the X many times or using task manager to kill the process.

Rerun msys2.exe and the update command again:

`pacman -Syu`

Now you will download the required gcc toolchain:

`pacman -Sy mingw-w64-x86_64-toolchain`

Accept the default=all option.

Important: Make sure the following line appears before any other msys/mingw directories:

(default location, yours may vary)
`c:\msys64\mingw64\bin`

#### Retreiving needed packages

Since this post is about previewing my shader support update, we are going to be downloading the shader support branch from my github repo but placing it in the default location of faiface's github directory. This is required as a side effect of go's package system and to avoid having to change a great amount of import lines. This branch should be NONE breaking for any existing pixel users.

Clone shader support branches for pixel:

`git clone -b shadersupport https://github.com/thegtproject/pixel %GOPATH%\src\github.com\faiface\pixel`

Clone shader support branches for glhf:

`git clone -b pixelshaders https://github.com/thegtproject/glhf %GOPATH%\src\github.com\faiface\glhf`

And now you can go get the remaining dependencies. If a couple terminated build errors show up, do not worry about them. Let the process continue uninterupted.


```
go get github.com/faiface/mainthread
go get github.com/go-gl/gl/...
go get github.com/go-gl/glfw/...
go get github.com/go-gl/mathgl/...
```

#### Finished

Great now we should be able to run a pixel project from source. To test this navigate to the following directory

`%GOPATH%\src\github.com\faiface\pixel\examples\platformer`

and try to run it:

`go run main.go`

Success?

![](/images/platformerss.png)
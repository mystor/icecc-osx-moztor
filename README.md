# Icecream Setup for Mac OS X (Mozilla Toronto Office)

Want to build a clobber of Firefox in 5 minutes on your Mac laptop in the
Toronto office? Here are the instructions for you!

> NOTE: This version of icecc doesn't seem to work correctly on macOS 10.12. 
> https://github.com/mstange/icecc-osx-moztor has an updated version which 
> may work better for more recent xcode versions.

## Instructions

Install the prerequisites (I recommend using homebrew)

```bash
$ xcode-select --install  # Make sure to re-run this command when you update xcode
$ brew install lzo
```

Clone this repository, this will take a while because it contains a binary copy
of a clang toolchain (which is a gross thing to keep in git, I know)

```bash
$ git clone https://github.com/mystor/icecc-osx-moztor ~/icecream
```

Install the daemon. This will create a launchd plist which will be run on startup.
The argument passed to the `install.sh` script is the scheduler to connect to.

```bash
$ sudo ~/icecream/install.sh 10.242.24.68
```

Update your mozconfig to point cc and c++ to the compiler wrappers

```bash
CC="$HOME/icecream/cc"
CXX="$HOME/icecream/c++"
```

Build firefox with sufficient jobs to saturate your network! (NOTE: If you get
failures due to posix_spawn failing, use a lower job count, as your computer is
failing to create more processes).

```bash
$ ./mach build -j100
```

## Important Notes

Use a _wired_ connection when building with icecream. Performance over WiFi is
not nearly as good as performance over a wired LAN connection. On the same note,
I also discourage you from building over the VPN due to latency issues.

If your builds seem to be hanging without building, you can try restarting your
`iceccd`. The easiest way to do that is to find it in Activity Monitor and kill 
it, the `launchd` should restart it automatically.

## Using with non-firefox builds

Setting the `CC` and `CXX` environment variables like in the mozconfig will
probably do the right thing for just about any build system. Just make sure to
set the job count high enough.

## Work in Progress

Currently the version of `iceccd` running locally doesn't dispatch objective-c
files to the network, instead building them locally. This greatly slows down the
build, at least partially because many of the jobs are taken up with waiting
objective-c builds. To fix this, the icecream daemons on every computer in the
office will need to be patched with Benoit's patch, but this hasn't been done
yet.

## How do I watch it work?

Either ask someone on a linux computer to run `icemon` to get pretty graphics,
or `telnet` into the server,

```bash
$ telnet 10.242.24.68 8766
```


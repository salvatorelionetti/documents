# ORI Remote Labs: Spectrum Analyzer Video

Of all the instruments in the lab, the RSA5065N Spectrum Analyzer has the largest and richest screen display. The size of the display makes screenshots slow. Moreover, for many lab purposes it's quite revealing to see the spectrum display changing in real time, and not really satisfactory to get only a still screenshot.

The RSA5065N has an HDMI video output port. We have an inexpensive HDMI capture device connected between that HDMI output and a USB port on the Raspberry Pi. With just a little extra setup, you can use this to get a live view of the spectrum analyzer's screen. It won't be quite as quick as the instrument's actual front panel, but will still be more useful than static screenshots.

Unfortunately, none of the other instruments has a video output port, so this only works on the spectrum analyzer.

## Access via X11 Forwarding

One way to get access to the spectrum analyzer video feed is to run a video viewer on the Raspberry Pi. A plain SSH session only gives you access to a text terminal, though. The solution is to forward the Pi's GUI through your SSH session to your local screen. There are two steps: run an X11 server on your local machine, and forward X11 through SSH.

Go ahead and set this up now, even if you're not excited about looking at the spectrum analyzer video feed. There will be other cases where you want GUI access to the remote lab computers.

### Running X11 on Your Local Machine

If your local machine is running Linux with a GUI desktop, congratulations! You're almost done with this step. Edit `/etc/ssh/sshd_config` and look for (or add) a line for `X11Forwarding`. Set it to `yes` and save the file.

If your local machine is running macOS, you need to install
[Xquartz](https://www.xquartz.org/) (unless you already have). Edit `/etc/ssh/sshd_config` and look for (or add) a line for `X11Forwarding`. Set it to `yes` and save the file. Depending on macOS version and probably Xquartz version, you may need to manually start Xquartz before starting your SSH session.

If your local machine is running Windows, you need to install an X11 server such as
[Xming](https://sourceforge.net/projects/xming/). You will need to manually start the server before starting your SSH session.

### Forwarding X11 over SSH

I'm going to assume you're already running SSH and able to access the Raspberry Pi in the remote lab. If not, check out [Setting Up for Remote Access to ORI Labs](ORI-Lab-User-Setup.md) for some guidance.

If you invoke SSH from the command line (Linux, macOS), just add `-X` to the command line. It will look something like this:
```
ssh -X ori-west
```

If you invoke SSH from a GUI (PuTTY on Windows), enable X forwarding in new or saved SSH sessions by selecting Enable X11 forwarding in the "PuTTY Configuration" window (Connection > SSH > X11).

### Simple Test of X11 Forwarding

I like to verify that X11 forwarding is working in the simplest way possible, before jumping into something fancy. One way to do this is to run the toy program `xeyes`.

Open an SSH session to the Raspberry Pi with X11 forwarding enabled.

Type `xeyes` at the command prompt.

Immediately you should see a new window pop up on your local screen. The window background should be transparent, and there should be two large cartoon eyeballs. If you move the mouse around, the pupils in these eyeballs should follow the mouse cursor around. This works all the time on Linux (where the whole GUI is in X11) but only while an X11 window has focus on other systems.

If that didn't work, nothing else is likely to work with X11, so take the time to debug it.

When you're done with Xeyes, you can exit it by hitting Ctrl-C in the terminal.

### Viewing the Video

Now that we're set up to run GUI programs, we can view the spectrum analyzer video.

One way to do that is to use the program `vlc` to view video from the capture device:

```
vlc v4l2:///dev/video0:width=1280:height=720
```

In that command line, `v4l2` means _Video for Linux version 2_ and identifies the type of stream to open. `/dev/video0` is the name of the device. If you don't specify the width and height to capture, you'll get an upscaled 1080p version of the video. 1280x720 is the native size of the video from the spectrum analyzer. If you choose some other size, you'll get the closest match that the capture device supports.

### Disregard These Error Messages

If your SSH client is based on OpenSSH (most are), you'll very likely see a burst of error messages like these:

```
[b3a2f140] xcb generic error: shared memory server-side error: X11 error 10
[b3a2f140] xcb generic error: same error on retry: X11 error 10
[b3a2f140] xcb_x11 generic: using buggy X11 server - SSH proxying?
```

You'll also see some messages when VLC starts, and possibly again when you shut it down.

### Using VLC

VLC will pop up a GUI window with the spectrum analyzer video in it. Everything is more or less self-explanatory.

If you need a still image screenshot for your records or for documentation, you can make one by choosing `Take Snapshot` from the `Video` menu. A screenshot taken this way won't be quite as crisp as one taken digitally over the remote control interface directly on the instrument, but it will be good enough for most purposes.

When you're done with the video, quit VLC in any of the usual ways.

## Access via Streaming

It would probably use network bandwidth more efficiently if we streamed the spectrum analyzer video from the lab to a program running on your local machine. We don't have that set up yet, but this document will be updated if and when we do.


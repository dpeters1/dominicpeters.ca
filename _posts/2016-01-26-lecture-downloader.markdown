---
layout:     post
title:      "CU Lecture Downloader"
subtitle:   "Schedule and download content from the CUOL stream"
date:       2016-01-26 12:00:00
categories: tidbits
author:     "Dominic Peters"
---

## Rationale

As a first year engineering student at Carleton University, I'm required to take two general electives to 'open my mind' and whatnot. Obviously, the best choice is to take an easy class and boost my overall GPA. These so called 'bird courses' are incredibly popular, and probably none more so than 'Mysteries of the Mind'. Not only is every test multiple choice, but it's the only general elective that can be taken as an online course.

Online courses at Carleton work like this: Live streams are broadcast twice a week during normal lecture hours. If you want to rewatch a stream, you need to shell out $50 for their video-on-demand service. I realize that $50 isn't a huge sum when you consider the cost of tuition, but that's exactly my point- Why make students pay such an insignificant amount when we're already paying thousands a year?

## Method

Reverse engineering the live stream was extremely simple. I just opened up the developer console on chrome and started poking around until I found a .m3u8 file.
According to wikipedia:

> m3u8. Internet media type. ... One common use of the M3U file format is creating a single-entry playlist file pointing to a stream on the Internet. The created file provides easy access to that stream and is often used in downloads from a website, for emailing, and for listening to Internet radio.

The url structure of the .m3u8 file is `http://ec-hlslive.cuol.ca/default/${room ID}_${quality}.m3u8` where {quality} is the bitrate and {room ID} is the room in which the stream is recorded.

Then, ffmpeg can be used to encode the stream to mp4. In a terminal:
```bash
ffmpeg -i http://ec-hlslive.cuol.ca/default/${room ID}_1200.m3u8 -strict -2 $duration filename.mp4
```

ffmpeg will begin live encoding and output to filename.mp4.
The wrapper script provides a way to specify the room name, recording duration, recording date and time. The first two are parsed into ffmpeg arguments, while recording date and time are used to create a crontab entry. For those that are unfamiliar, a crontab is a sort of job scheduler for unix systems. It's often used for scheduled backups, but in our case it begins recording the live stream whenever it airs. The two caveats are that it only works on unix systems, and the host computer needs to be turned on whenever the stream is airing.

Luckily, I already have a linux server set up to serve media and tunnel torrents through a vpn. Whenever a new lecture is recorded, it's automatically added to my plex library for later viewing.

[Source Code](https://github.com/dpeters1/CU-Lecture-Downloader)

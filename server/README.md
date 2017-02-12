Video Announcements Server
==========================

This server is running on a [Banana Pi M1](https://en.wikipedia.org/wiki/Banana_Pi#Banana_Pi_M1) running Ubuntu 14.04. 

It takes in an [RTMP](https://en.wikipedia.org/wiki/Real-Time_Messaging_Protocol) video stream, that comes from the broadcast source (the laptop running the [OBS](https://obsproject.com/) software) and re-streams it out. Additionally, it converts it to a [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming) stream, which is consumed by the users.

To accomplish this, the server runs the [Nginx](https://www.nginx.com/resources/wiki/) webserver with the [nginx-rtmp-module](https://github.com/arut/nginx-rtmp-module) plugin. The versions we are using are [nginx-1.10.2 (current stable)](http://nginx.org/download/nginx-1.10.2.zip) and the [1.1.10](https://github.com/arut/nginx-rtmp-module/releases/tag/v1.1.10) plugin. These were unziped to the `~/src/` directory, then `cd ~/src/nginx-1.10.2/`, `./configure --add-module=../nginx-rtmp-module`, `make`, and `sudo make install`. This installs nginx and puts it in `/usr/local/nginx`. 

To get nginx to automatically start as a service, I ran:

1.  `wget https://raw.github.com/JasonGiedymin/nginx-init-ubuntu/master/nginx`
2.  `sudo mv nginx /etc/init.d/nginx`
3.  `sudo chmod +x /etc/init.d/nginx`
4.  `sudo update-rc.d nginx defaults`
5.  `sudo service nginx start`

Once Nginx was installed, I added the [nginx.conf](nginx.conf) file to `/usr/local/nginx/conf/`.  This file sets up the basics of the webserver as well as the rtmp plugin. Specifically, it sets it up to open the normal rtmp port (1935) to listen for streams. When it gets one to the /news path (e.g. rtmp://news.lan/news/main) it will make that available for clients to connect to at the same location it received it. Additionally, it sets up the HLS copy of the stream. This will allow clients to connect to the "hls" directory on the server with the stream name and the ".m3u8" extension (e.g. http://news.lan/hls/main.m3u8). [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) has also been setup, but has not been evaluated. This stream can be directly played on a recent Apple or Andorid portable device, or in the [VLC Player](http://www.videolan.org/vlc/index.html). It isn't playable by default in desktop web browsers. To allow this (and because the VLC player didn't seem to recover very well when the connection dropped out), we added a web based player as a separate software package.


Web based player
----------------







Some guides used:

* [nginx-rtmp-module Readme](https://github.com/arut/nginx-rtmp-module)
* [nginx-rtmp-module Getting Started](https://github.com/arut/nginx-rtmp-module/wiki/Getting-started-with-nginx-rtmp)
* [OBS Nginx-rtmp setup](https://obsproject.com/forum/resources/how-to-set-up-your-own-private-rtmp-server-using-nginx.50/)
* [Setup Nginx-RTMP on Ubuntu 14.04](https://www.vultr.com/docs/setup-nginx-rtmp-on-ubuntu-14-04)

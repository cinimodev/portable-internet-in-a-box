## Landing page
This is so there will be a single page for everyone to access that will then have easy to follow links to the other services on the server. Part of the build will include cards with the connection info for the wifi network, the URL for this landing page, along with any login info needed for the other services,

#### Heimdall setup
*All paths shown below are the paths I have setup on my machine for the volumes. Adjust the file paths to your configuration.*

```bash
docker run -d --name=heimdall -e PUID=1000 -e PGID=1000 -e TZ=America/Los_Angeles  -p 8083:80 -v /mnt/storage/docker_config/heimdall:/config --restart unless-stopped lscr.io/linuxserver/heimdall:latest
```

## Articles, blogs, and resources
A key feature for this box is to have articles, whitepapers, ebooks, and more available to anyone in the `portable_iiab` LAN. This is more than just ebooks from Calibre. I would like to occasionally add longform articles I find that can be read in a power outage scenario. I also would like to add emergency resources, like how to purify water or start a fire. 

To do this I am going to use `rsync` to automatically copy from my homelab setup to `portable_iiab`. I will have to leave the PBP on, even when not in use, so I can archive the webpages to ArchiveBox and will be ready when I need them. 

[ArchiveBox](https://github.com/ArchiveBox/ArchiveBox) is a rad tool that will download a webpage in various formats, including raw HTML and even save the page as a PDF. This will allow the webpage to be loaded in a totally offline state and can be opened in multiple ways, which will be important on mobile devices that certain formats may be problematic, such as being mobile responsive. 

#### ArchiveBox
*All paths shown below are the paths I have setup on my machine for the volumes. Adjust the file paths to your configuration.*

Run:

```bash
docker run -d -e PUID=1000 -e PGID=1000 -e TZ=America/Los_Angeles --name=archive_box -p 8086:8000 -v /mnt/storage/docker_config/archivebox:/data --restart unless-stopped archivebox/archivebox
```

This is mapped to a folder that is being synced from my homelab to this box. I am doing it this way so I don't have to manage two different instances of ArchiveBox. In theory, I should be able to `rsync` the folder from my homelab to `portable_iiab` with the ArchiveBox docker instance on `portable_iiab` stopped. Then, in the scenario I need it, I can start the container and all they articles, plus my settings, should be synced. 

#### FreshRSS
Using FreshRSS, I can access any articles it has collected from the feeds that are stored on the server. In my testing, any feed that delivers the full article in the feed is still accessible in the browser on FreshRSS, even if no internet is available. 

I copied my existing FreshRSS installation on my homelab, which uses all the same configurations and users. 

Setup:
*All paths shown below are the paths I have setup on my machine for the volumes. Adjust the file paths to your configuration.*

```bash
docker run -d --name=freshrss -e PUID=1000 -e PGID=1000 -e TZ=America/Los_Angeles -p 8085:80 -v /mnt/stroage/docker_config/freshrss:/config --restart unless-stopped lscr.io/linuxserver/freshrss:latest
```

#### Jellyfin 
Since the core reason I am building this box is because there will not be access to the internet, Jellyfin is a no-brainer since it doesn't require it at all. 

I'm not sure if the PBP will be able to keep up with streaming content to users. But, it will provide a usable interface for everyone to browse and then download what they want to watch. I will have to experiment with turning off transcoding, which will save a ton of CPU. 

In additon to compute power, I am also not sure how this will do with actual electrical power when streaming. That would be another reason to turn off transcoding, and maybe require downloading only, as it will increase the amount of power being used. I can't have this since I am on a tight power budget. 

Setup:
*All paths shown below are the paths I have setup on my machine for the volumes. Adjust the file paths to your configuration.*

```bash
docker run -d --name=jellyfin -e PUID=1000 -e PGID=1000 -e TZ=America/Los_Angeles  -e JELLYFIN_PublishedServerUrl=192.168.1.194 -p 8096:8096 -p 8920:8920 -p 7359:7359/udp -p 1900:1900/udp -v /mnt/storage/docker_config/jellyfin:/config -v /mnt/storage/media/iiab_tv:/data/tvshows -v /mnt/storage/media/iiab_movies:/data/movies -v /mnt/storage/media/iiab_youtube:/data/youtube --restart unless-stopped lscr.io/linuxserver/jellyfin:latest
```

#### Navidrome
Another offline first application. Once the music is added the service can operate offline with no issues for streaming music. 

It doesn't have the capability to use directories as playlists, which is... unfortunate. However, the music is still in directories for lists on the server, which can be downloaded through Filebrowser. 

Setup:
*All paths shown below are the paths I have setup on my machine for the volumes. Adjust the file paths to your configuration.*

```bash
docker run -d --name navidrome --restart=unless-stopped --user $(id -u):$(id -g) -v /mnt/storage/media/iiab_music:/music -v /mnt/storage/docker_config/navidrome:/data -p 4533:4533 -e ND_LOGLEVEL=info -e TZ=America/Los_Angeles deluan/navidrome:latest
```

## Wikipedia
Having a Wikipedia service will add a lot of possibilities for research while having down time with both the power and internet being out. Plus, I love to read Wikipedia and would appreciate having it available while we are just sitting around.

Before it can be setup I need to download the "zim" files that Kiwix will be serving. Then, set the parameters for the zim files in the Docker command. I'm assuming that if I want to add new zim files I will need to download them to the data folder and then stop, remove, and relaunch the container with the new command including all the zim files

This `.zim` is all of Wikipedia, with no images. This way we have access to all the information, but with storage being significantly lower. 

https://download.kiwix.org/zim/wikipedia/wikipedia_en_all_nopic_2022-01.zim

Download  any other `.zim` files here:

https://download.kiwix.org/zim/

*All paths shown below are the paths I have setup on my machine for the volumes. Adjust the file paths to your configuration.*Kiwix setup:

```bash
docker run -d --name=kiwix -e TZ=America/Los_Angeles -e PUID=1000 -e PGID=1000 -p 8087:8080 -v /mnt/storage/docker_config/kiwix:/data --restart unless-stopped kiwix/kiwix-serve fas-military-medicine_en.zim www.ready.gov_en.zim zimgit-knots_en.zim zimgit-post-disaster_en.zim wikipedia_en_all_nopic_2022-01.zim zimgit-food-preparation_en.zim zimgit-medicine_en.zim zimgit-water_en.zim
```
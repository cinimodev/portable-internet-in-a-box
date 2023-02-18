## Storage
This is probably going to be the biggest struggle. Looking at the movie & TV show list from everyone -plus Wikipedia, music, and books/PDFs - I probably need around 500 GB of storage for this box. 

Now, I can do this from the Pinebook or any other laptop I have around. But, I will definitely exceed my power requirements. 

The Pinebook Pro does give me the option to have an external HDD that can go through USB 3, which would be a significant performance upgrade for streaming. It also has an SD card slot that supports potentially up to 2 TB. According the [Pinebook Pro wiki](https://wiki.pine64.org/index.php/Pinebook_Pro)it is tested up to 512 GB, with 2 TB potential.

The RPI cannot handle an external USB HDD. It would need its own PSU for the HDD. I *might* be able to have this kind of storage from a USB thumb drive or an SD card in a reader. It keeps feeling like the SD card is the best option since it doesn't require any additional power. 

#### File sizes
In order to effectively choose which server device I will use, I need to find out how much storage I will need. The higher the storage, the more likely I will need a full external HDD compared to just an SD card.

Movies, TV shows, and YouTube videos are going to be the items that take up the most storage. In particular TV shows. 

Since I am optimizing for mobile devices, I can significantly reduce the quality of the videos to cut the storage requirements. 


Movies - Handbrake settings to reduce the file size:
- Android 480p30 preset with modified settings
- Encoder set to "fast"
- Bitrate at 900 kbps
- Audio set at 96 bitrate

In my testing, this took the file size to half of what it was. The file size of the movies folder is roughly **63 GB**. After re-encoding the movies total storage size is only **31 GB**.

TV Handbrake settings:
* Discord Small 360p 30
* RF: 23

Total TV file size before re-encoding was **108 GB**, which is actually smaller than I was thinking. Most of these shows were ripped from the DVD seasons I own, so most were already at standard definition. After re-encoding, the total file size went down to **32 GB**. 

## Files
I want all the files on the SD card so I can easily back it up by cloning the card. Then I won't have to recreate this, I can just move to a new SD card at any time. 

It also means I can move to any other machine at any time. For example, I want the lower power draw of the RPI and just use SD card as storage. Or is the PBP dies and I want to run it from a different laptop.

SD card setup:  
- 📁 Parent `media` folder on the SD card. This will be the `srv` folder for `filebrowser` as well:
	- 📂 Sharing. A folder for people to add their own files that are then accessible to everyone.
	- 📂 Apps. Android apps in case anyone needs them for accessing server services. This is particularly important for Briar.
	- 📂 Wiki. Collection of files explaining the `portable_iiab` setup.
	- 📂 eBooks. The book collection so they can be downloaded.
	- 📂 Music. All music files. 
	- 📂 TV. All the TV shows in their subfolders
	- 📂 Movies. All the movies
	- 📂 YouTube. All YT videos in their sub-directories
- 📁 Parent `docker_config` folder. This is where all docker files will live, as well as some of the app data I don't want people to have access to.
	- 📂 `filebrowser`
	- 📂 `heimdall`
	- 📂 `freshrss`
	- 📂 `jellyfin`
	- 📂 `navidrome`

#### Filebrowser setup
*All paths shown below are the paths I have setup on my machine for the volumes. Adjust the file paths to your configuration.*

```bash
docker run -d -e PUID=1000 -e PGID=1000 --name=filebrowser -v /mnt/storage/media/:/srv -v /mnt/storage/docker_config/filebrowser/filebrowser.db:/database.db -v /mnt/storage/docker_config/filebrowser/settings.json:/.filebrowser.json -p 8084:80 --restart unless-stopped filebrowser/filebrowser
```

#### Apps
In the directory that I will use for filebrowser, I would like to have a collection of Android apps just in case they are needed. 
- Briar
- VLC
- Jellyfin
- Ultrasonic
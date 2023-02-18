# portable-internet-in-a-box
This project is build an internet-in-a-box system that runs 100% off battery for use when there are extended power outages. It should be able to deliver an *internet-like* experience, along with critical information for living life without power. 

My homelab is also an "internet in a box" with much more storage. It is designed for being disconnected from the public internet. However, this only works if I still have access to power at home. Although it can run off of the generator, the combination of the server, hard drive array, switch, and router make it not usable from an offgrid situation. 

## Primary goals
In an ideal setup, this system should be able to operate directly from solar panels, a battery bank, or a car 12v connection without inverter. 

From battery, the system should last 7 days running 4 hours per day. 

It should be compact and able to be carried in a laptop bag. 

All services should be accessible and useable from a web browser, primarily on a mobile device. 

Nothing should require access to the internet at any point.

All applications and services need to have a user-friendly GUI.

This system will provide:  
1. Stream TV shows, movies, and YouTube videos across the LAN.
2. Offer a small ebook library.
3. A Spotify clone with several playlists
4. Have a collection of blogs and longform articles.
5. An archive of documents to help with living in an emergency.
6. Allow downloading of all content.

#### Networking goals

1. Broadcast wifi to every room in the house.
2. Have a DCHP server.
3. Allow up to 10 devices to connect without issues.
4. Run on USB power.
5. Optional: Provide access to a mobile data hotspot.
6. Optional: Create custom DNS entries

#### Power goals
I want to be able to run this for an entire week, if possible, from my existing power bank. Ideally I would be able to run it for 4 hours a day, for 7 days. This means that everyone with access will be able to read, view, or download anything they want for an afternoon each day. 

#### Video content goals

1. A collection of comfort movies from each member of the house.
2. At least one season from comfort TV shows, plus extended seasons for shows that are part of a habit (like watching before bed).
3. An archive of YouTube videos for the girls, including Stampy and Eric Carle reads.

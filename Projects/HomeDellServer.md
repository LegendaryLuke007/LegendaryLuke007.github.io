<br/>
<br/>

# HOME DELL SERVER

## Summary:

This project is my first real dive into Linux Computers and servers. My hope is to create a local server that I could use as Data storage for providing that secondary on-premise backup of data. I also 
wanted to get my hands dirty with a Linux distribution and older hardware so that I could do this for cheap. While this project is still in progress, I have had a lot of fun figuring out how to boot up
a weird OS using a Disc drive vs the usual USB configurations I am used to with newer hardware.

- Purchasing all components from local electronic recycling place for $100
  - 2 TB of storage (starting amount, will for sure need more)
  - 30 GB Ram
  - 2 Processors
- Initally tried USB. Found out that it isn't an option in the BIOS Boot settings
  - *Borrowed a CD-Drive and 700 MB CDs from a friend*
- Downloading a minimal Linux distribution so that I can get it on the disk 700 mb CD
  - Configured 700 MB CD for storage, named it appropriately.
  - *Realized Rufus is only for USB and not for burning discs*
  - burning OS onto disk using Windows Burning Option
  - First disc was a dud, second one works.
  - Inital test was a failure, it was trying to boot by PXE environment so I turned off that option so that it had to boot by Disc drive 
- *Almost finished installation*
  - I thought I was in the clear but then it didn't quite fully download. Got to a blank screen past the startup phase. Going to download in a different mode.
  - Trying installation in *Failsafe Boot* mode
  - Got FARTHER. Got stuck with partitioning the drives. Will try again in Failsafe Boot Mode
  - 4th try, ALMOST got there. Did a miss click and got stuck trying to put my /home in a partition that didn't exist. So I need to make a 1 TB partition and put the home there.
  - 5th time. Once again, got stuck in partitioning. Now I am just going to throw everything into the 1TB partition and call it good for now.
  - 6th time - It is finally installing to my 30GB partition
  -  system has been installed! Initially it didn't boot but then I remembered that I need to turn on booting from internal hard drive for it to use the installation.

- **Installed**
  - *Connecting to the internet*
    - I am now connecting by command line to my local router
    - I trying to ping the router, no luck.
    - After lots of poking around and talking to ChatGPT I look to see what what network interfaces are on or off
    - `ip -o link show`
- I realize they are ALL off
  - I have realized that I do NOT have a WLAN network interface, so I am unable to make a wireless connection to the internet... Sooo I need to connect it with ethernet straight to my router

- *Connecting ethernet*
  - Got a long ethernet cable and manually connected server to the router across the house with the help of a friend 
  - Ping server IP address
  - Success! Computer is connected to the internet - Now I just need to download a more robust OS
  
- *Updating OS to have GUI*
  - Realized that for ease of effort, the best thing to do is to just get a larger disc (maybe 3-4 GB) and use that to download a GUI based OS.
  - Currently waiting on disc to arrive for GUI use.

- **STILL WORK IN PROGRESS
---

# [Return Home](../index.md)


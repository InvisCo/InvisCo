# My Home-Lab

## Current Setup

1. **NAS**
   - CPU: AMD Ryzen 5 2400G
   - RAM: 32GB DDR4 2933 MT/s
   - Storage:
     - 4x16TB - ZFS RAIDZ1 (48TB usable)
     - 3x8TB+2x10TB - SnapRAID+mergerfs (34TB usable)
     - 1x1TB SATA SSD
     - 1x512GB NVMe SSD
1. **Micro PCs** x2
   - CPU: Intel i3-8100T
   - RAM: 16GB DDR4 2400 MT/s
   - Storage:
     - 1x1TB SATA SSD
     - 1x256GB NVMe SSD

## The Journey

Over the past 5 years, I have been tinkering away on my home-lab to try out new technologies and learn how things fit together. 
This journey began as a way to be able to get access to the same video library I had access to on my Hard Drive and on Netflix at home while I was at university in another country:earth_americas:.

I started out by repurposing my old desktop computer that I had to leave behind when I set out for university. On it, I set up Plex and an OpenVPN server on [OpenMediaVault](https://www.openmediavault.org/) 5 to solve my main problems and provide a simple web-based UI to manage the entire system.

Over the next year, while I was away at university, I kept exploring YouTube and r/selfhosted and r/homelab to learn about all the wonderful things one could achieve with an always-on computer. My eyes were opened, and my brain was filled with wonderous ideas on what I could not wait to try out on my next holiday back home! 

In a twist of fate, what was supposed to be a short 2 week break turned into a 2 year stint at home, thanks to COVID-19. 
I was now free to do whatever I desired without having to bother my family with messages like, "Can you please press the power button on the server?" or "Is the server running? Are the lights on?".
During my time at home, undeterred with making a mess of things, I tried out various tools that were exploding in popularity due to the global lockdowns. 

I set up [AdGuard Home]( https://github.com/AdguardTeam/AdGuardHome) to block ads and trackers on my home network. This was backed up with [Unbound]( https://www.nlnetlabs.nl/projects/unbound/about/), a recursive, caching DNS resolver to speed up DNS queries performed from with the local network. I then swapped out OpenVPN for Wireguard, the hot new VPN protocol that everyone was raving about. Eventually settling on [wg-easy](https://github.com/wg-easy/wg-easy) due to it’s dead simple client setup process.

I moved all my applications to [Docker](https://www.docker.com/) since it was much easier to update apps and get everything back up and running if I ever screwed something up. I first started by using [Docker Compose](https://docs.docker.com/compose/compose-file/) and editing configuration files via SSH eveytime there was an update, or I needed to make a change. Eventually this became to hard with the need to keep a track of so many services, so I started using [Portainer](https://www.portainer.io/) and [Watchtower](https://github.com/containrrr/watchtower) to manage and keep my Docker containers up to date.

I also wanted to be able to read my books and comics more easily than having to memorize the page number every time I switched devices. This was done with [Calibre](https://calibre-ebook.com/) to manage my library, and [Kavita](https://github.com/Kareadita/Kavita) to serve the content over the network.

It became difficult to keep a track of the port numbers for all these web applications running on Docker, so I setup a reverse proxy to be able to easily open the app I wanted by just typing its name. I first tried out [Traefik](https://traefik.io/traefik/), but it was too challenging for me at the time. I eventually settled on [Caddy](https://caddyserver.com/) since it automatically enforces HTTPS and has a very simple configuration process through its Caddyfile.

The need to track changes on websites, led me to [ChangeDetection.io]( https://github.com/dgtlmoon/changedetection.io). This tool provided a way to track changes to specific elements on a webpage. I also added [Uptime Kuma](https://github.com/louislam/uptime-kuma) and [Gotify](https://gotify.net/) to monitor and notify me in case of any disruptions to any apps on my server.

In the midst of this, I upgraded some of the hardware. I increased the RAM from 8 GB to 32 GB to handle running so many containers and VMs. I also added some new Hard Drives to increase the capacity and introduce some redundancy by using [SnapRAID](https://www.snapraid.it/) and [mergerfs](https://github.com/trapexit/mergerfs). This was supplimented with a regularly scheduled, iterative backup of the important personal data to [Backblaze B2](https://www.backblaze.com/b2/cloud-storage.html) using [Rclone](https://rclone.org/).

Eventually it came time to return to university in-person, so I had to ensure a stable platform for my apps to run. I ended up with an [OpenMediaVault](https://www.openmediavault.org/) 6 base with all applications running on Docker. Not that different from before, but something I stuck to due to my familiarity with the technologies involved.

Over my next few co-op work terms, I was exposed to [Kubernetes](https://kubernetes.io/) and learnt about the benefits that it brought compared to using Docker. At this point I was running into some issues with certain workflows and configurations due to the nature of the separated containers when running on Docker. I came across [Kubernetes Pods](https://kubernetes.io/docs/concepts/workloads/pods/), which was a way to run multiple containers together in a way that makes it easier for them to interact with each other than what is possible with Docker.

When I returned home for my vacation in 2023, I found some inexpensive Micro PCs for sale. I quickly snapped up 2 of them and set up [Proxmox VE](https://www.proxmox.com/en/proxmox-ve) on them and my earlier server. I then clustered them together to be able to provide High Availability for the essential services that are running on them, such as Adguard Home and [Home Assistant](https://www.home-assistant.io/). I also replaced the ageing storage with a [ZFS](https://en.wikipedia.org/wiki/ZFS) pool and relegated the older drives as a on-site backup for the entire array.

In the future, I intend to setup the Kubernetes cluster across these 3 systems to run all the applications I still have on Docker.

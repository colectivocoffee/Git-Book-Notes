# Riot Esports

Below are some highlights from riot games tech blogs

## How to Deal with Unreliable Network?

Setup high-speed point-to-point connections using Riot Direct \(an ISP\)

### 1. \(hardware\) Find a good network provider

Pick at least 2 high bandwidth internet circuits to ensure redundancy. 

### 2. \(hardware\) Build Riot's own ISP - Riot Direct 

Use Riot Direct is great, but there are some limitations such as capacity constraints and capabilities in Vietnamese Infrastructure. \(2019 Mid Season Invitational in Hanoi\)

### 3. \(hardware\) Client side - Use VoIP

VoIP - Voice Over Internet Protocol 基於IP的語音傳輸  
  
For each race team, they have their own dedicated communication system, such as VoIP device. 

### 4. \(hardware\) More Data Centers

There were more than 20,000 game servers globally. 

### 5. Local Network

Need to setup a network capable of supporting mid-sized business a few days before the show. Below are some items needed 

* **Fiber Optic Cables** One of the most interesting fiber setup is 2014 World Finals in Seoul, South Korea. There was no existing infra to leverage, requiring Riot to setup on their own.   **Has Different Tier of LAN**  Setup specialized virtual LAN extensions and have different tiers of service to each onsite teams.  The highest priority group to the network infrastructure is Riot professional players. The network is optimized for latency and performance. 
* Same Game Shards Ensure the spectator system has near-zero delay with professional player system. 
* **Offline Game Servers** **Latency** and **packet loss** are the biggest **concerns** when it comes to fair competition.

## Connectivity

Riot LoL uses **UDP protocol** to send data. Professional players play on a hybrid type of shard connected to both normal and offline game servers, to ensure no risk of network lag or packet loss during competitive play.  



## Bottlenecks

Latency and Packet Loss

## What's Next?

Riot’s Esports Technology Group will be attempting an industry first - remote transmission utilizing a new compression codec codenamed JPEG-XS. Known internationally as [ISO/IEC 21122](https://www.iso.org/standard/74535.html), JPEG-XS promises vastly improved video compression quality and sub-1ms latency, a 93% decrease from the current industry standard!  
The total bandwidth is with impressive 2.7 gigabits per second between LA and Worlds.


This project is a *fork* of [the original BirdsiteLIVE from NicolasConstant](https://github.com/NicolasConstant/BirdsiteLive). 
This does not run anymore since Twitter discontinued the API v1.1. 
Changes made in this fork include:

* Rework About page entirely - also disclose unlisted accounts and federation restrictions
* Cache Tweets so that, for example, Announces do not hit rate limits
* Allow replacing and redirecting to twitter.com in Tweets to other domains (i.e. Nitter instances)
* Verified checkmarks on [verified](https://twitter.com/verified) Twitter users
* Proper remote follow form on user pages
* Mark individual Tweets as potentially sensitive
* Define and enforce a maximum follow count limit
* Define and enforce a maximum Tweet fetch age using snowflakes
* (Optional) send quote-RTs as Soapbox-style quote posts

This fork is also available as a Docker image as `pasture/birdsitelive`.

The project's original README is as follows:


# BirdsiteLIVE

## About

BirdsiteLIVE is an ActivityPub bridge from Twitter, it's mostly a pet project/playground for me to handle ActivityPub concepts. Feel free to deploy your own instance (especially if you plan to follow a lot of users) since it use a proper Twitter API key and therefore will have limited calls ceiling (it won't scale, and it's by design).

## State of development

The code is pretty messy and far from a good state, since it's a playground for me the aim was to understand some AP concepts, not provide a good state-of-the-art codebase. But I might refactor it to make it cleaner. 

## Official instance 

You can find an official (and temporary) instance here: beta.birdsite.live. This instance can disapear at any time, if you want a long term instance you should install your own or use another one. 

## Installation

### 1. Setup Oracle Cloud VPS

- From the Oracle Cloud Compute menu, click Instances, then click Create instance
- Enter a name for your new compute instance, select the root compartment, then click Edit in the Image and shape section.
- Image: **Canonical Ubuntu  version 22.04 Minimal**
- Shape: **VM.Standard.E2.1.Micro (1 OCPU, 1 GB RAM)**
- Under Networking, create or select a Virtual Cloud Network (VCN) with default settings. Ensure that the Assign a public IPv4 address option is selected.
- Under Add SSH keys: paste public ssh key from `~/.ssh/id_rsa.pub`
- click Create
- The provisioning process should take just a few minutes, and was faster than I would have expected.
- Connect via SSH. The default username is "ubuntu".

### 2. Firewall Configuration

Ubuntu images on the Oracle Cloud don't come with ufw installed: iptables should be used instead. Use the following commands to allow HTTP (TCP/80) and HTTPS (TCP/443) in the firewall using iptables:
``` 
sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 80 -j ACCEPT
sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 443 -j ACCEPT
sudo netfilter-persistent save
``` 
You will also need to create corresponding ingress rules for your Virtual Cloud Network to expose TCP ports 80 and 443 on the Internet. (guide: https://docs.oracle.com/en-us/iaas/developer-tutorials/tutorials/apache-on-ubuntu/01oci-ubuntu-apache-summary.htm#add-ingress-rules)

### 3. INSTALL BirdsiteLIVE

I'm providing a [docker build](https://hub.docker.com/r/nicolasconstant/birdsitelive). To install it on your own server, please follow [those instructions](https://github.com/NicolasConstant/BirdsiteLive/blob/master/INSTALLATION.md). More [options](https://github.com/NicolasConstant/BirdsiteLive/blob/master/VARIABLES.md) are also available.

Create and start container: `sudo docker compose up -d` 

Stop and remove container: `sudo docker compose down` 

Also a [CLI](https://github.com/NicolasConstant/BirdsiteLive/blob/master/BSLManager.md) is available for adminitrative tasks.

### Config 

config in `docker-compose.yml` :

``` 
 - Instance:Domain=xxxxxxxxxxx
 - Instance:AdminEmail=name@domain.ext
 - Db:Type=postgres
 - Db:Host=db
 - Db:Name=birdsitelive
 - Db:User=birdsitelive
 - Db:Password=xxxxxxxxx
 - Twitter:ConsumerKey=xxxxxxxxxxxx
 - Twitter:ConsumerSecret=xxxxxxxxxxxx
 - Moderation:FollowersWhiteListing=xxxxxxxxxxx
 - Instance:Name=twtr.site
 - Instance:ResolveMentionsInProfiles=false
 - Instance:PublishReplies=true
 - Instance:UnlistedTwitterAccounts=cocacola;twitter
 - Instance:TwitterDomain=twiiit.com
 - Instance:TwitterDomainLabel=Nitter
 - Instance:InfoBanner=This is my BirdsiteLIVE instance (Twitter to ActivityPub bridge). The service is whitelist only. You cannot use it if you are not on the whitelist! Contact xxxxxxxxxxxxxxx if interested.
 - Instance:ShowAboutInstanceOnProfiles=true
``` 
to apply changes run: `sudo docker compose up -d` 


## License

This project is licensed under the AGPLv3 License - see [LICENSE](https://github.com/NicolasConstant/BirdsiteLive/blob/master/LICENSE) for details.

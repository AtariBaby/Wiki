Introduction
------------

Synology only offer a docker package on their `x64` based NAS. Using
this method to install docker on an `aarch64` NAS is totally
unsupported/untested and totally at your own risk. It is entirely
possible it will destroy your NAS.

Summary
-------

The instructions below will:

1.  Place the docker binaries in `/usr/local/bin/`
2.  Create a docker config file `/usr/local/etc/docker/docker.json`
3.  Configure docker to save its data to `/volume1/docker/var`
4.  Create a script to start docker on boot at
    `/usr/local/etc/rc.d/docker.sh`
5.  Create a `docker` group
6.  Place a docker-compose script in `/usr/local/bin/`

Installation
------------

Log in as `root` to your synology. Execute the following command:

`curl `[`https://gist.githubusercontent.com/ta264/2b7fb6e6466b109b9bf9b0a1d91ebedc/raw/7b11f25c3dce181faa5206aed8051f176cc4e406/get-docker.sh`](https://gist.githubusercontent.com/ta264/2b7fb6e6466b109b9bf9b0a1d91ebedc/raw/7b11f25c3dce181faa5206aed8051f176cc4e406/get-docker.sh)` | sh`

If all goes well you should see the message:

    Done.  Please add your user to the docker group in the Synology GUI and reboot your NAS.

Do as it says:

1.  Add your user to the new `docker` group using the Synology GUI
2.  **Reboot.**

Hopefully you have functioning `docker` and `docker-compose` commands,
which should work when logged in as your normal user.

Caveats
-------

1.  It seems most ARM Synology don\'t support seccomp, so the docker
    container has unfettered access to your system (even more so than
    with a regular docker).
2.  Again, due to Synology constraints, all containers need to use
    `--network=host` (or `network: host` in compose) and everything will
    be directly accessible from the host. There are no port maps.
3.  Obviously you can only run aarch64 images, but most hotio and
    linuxserver images offer an aarch64 version.

Setting up a docker GUI
-----------------------

If you want a GUI you can run Portainer using the following example
compose:

    version: '2'

    services:
      portainer:
        image: portainer/portainer
        restart: unless-stopped
        network_mode: host
        volumes:
          - /var/run/docker.sock:/var/run/docker.sock
          - portainer_data:/data

    volumes:
      portainer_data:

Place this in a file called `docker-compose.yml` in an otherwise empty
directory. Run:

`docker-compose up -d`

Visit [`http://ip:9000`](http://ip:9000) to complete setup (where `ip`
is the IP address of your synology).

Setting up Sonarr/Radarr/Lidarr/Readarr
---------------------------------------

For guidance setting up Sonarr/Radarr/Lidarr/Readarr, see the [Docker
Guide](Docker_Guide "wikilink"), and **remember caveat 2 above.**

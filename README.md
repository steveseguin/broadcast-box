## Broadcast-box, modded for VDO.Ninja

- The web code is removed
- `whep` header value returned on whip publishing
- minor other tweak

The end result is a Meshcast-like alternative

todo: auto-generate stream token for publishers

### Install

To install on debian 12:
```
sudo apt-get install golang-go -y
sudo ufw allow 8080/tcp # handshake
sudo ufw allow 4000:65535/udp # webrtc
```
and to the run
```
cd ~
cd broadcast-box
APP_ENV=production go run .
```

publish to http://123.123.123.123:8080/api/whip /w bearer token

view from http://123.123.123.123:8080/api/whep /w bearer token


### Docker

A Docker image is also provided to make it easier to run locally and in production. The arguments you run the Dockerfile with depending on
if you are using it locally or a server.

If you want to run locally execute `docker run -e UDP_MUX_PORT=8080 -e NAT_1_TO_1_IP=127.0.0.1 -p 8080:8080 -p 8080:8080/udp seaduboi/broadcast-box`.
This will make broadcast-box available on `http://localhost:8080`. The UDPMux is needed because Docker on macOS/Windows runs inside a NAT.

If you are running on AWS (or other cloud providers) execute. `docker run --net=host -e INCLUDE_PUBLIC_IP_IN_NAT_1_TO_1_IP=yes seaduboi/broadcast-box`
broadcast-box needs to be run in net=host mode. broadcast-box listens on random UDP ports to establish sessions.

### Docker Compose

A Docker Compose is included that uses LetsEncrypt for automated HTTPS. It also includes Watchtower so your instance of Broadcast Box
will be automatically updated every night. If you are running on a VPS/Cloud server this is the quickest/easiest way to get started.

```console
export URL=my-server.com
docker-compose up -d
```

## Design

The backend exposes three endpoints (the status page is optional, if hosting locally).

- `/api/whip` - Start a WHIP Session. WHIP broadcasts video via WebRTC.
- `/api/whep` - Start a WHEP Session. WHEP is video playback via WebRTC.
- `/api/status` - Status of the all active WHIP streams

[license-image]: https://img.shields.io/badge/License-MIT-yellow.svg
[license-url]: https://opensource.org/licenses/MIT
[discord-image]: https://img.shields.io/discord/1162823780708651018?logo=discord
[discord-invite-url]: https://discord.gg/An5jjhNUE3

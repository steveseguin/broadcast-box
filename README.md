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

## Design

The backend exposes three endpoints (the status page is optional, if hosting locally).

- `/api/whip` - Start a WHIP Session. WHIP broadcasts video via WebRTC.
- `/api/whep` - Start a WHEP Session. WHEP is video playback via WebRTC.
- `/api/status` - Status of the all active WHIP streams

[license-image]: https://img.shields.io/badge/License-MIT-yellow.svg
[license-url]: https://opensource.org/licenses/MIT

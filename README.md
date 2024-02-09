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

## Key modification

For a WHIP + WHEP server to support "Meshcast"-like functionality in VDO.Ninja, the WHIP header response needs to contain the WHEP playback URL.

`whep`:`/api/whep|streamtokenhere` is an example of what the header response could look like.

and for WHIP/WHEP servers that support it, `whep`:`/api/whep/streamtokenhere`, this also would

or a full URL can be provided, like this would work, `whep`:`https://whep.yourdomain.com/?token=streamtokenhere`

This can be seen inthe code example below:
```
streamKey := r.Header.Get("Authorization")
trimmedStreamkey := strings.TrimPrefix(streamKey, "Bearer ")
res.Header().Add("Location", "/api/whip")
res.Header().Add("Content-Type", "application/sdp")

res.Header().Add("whep", "/api/whep|"+trimmedStreamkey)  // <<============== THIS IS IMPORTANT
res.Header().Set("Access-Control-Expose-Headers", "*")  // << ===== And this as well

res.Header().Set("Access-Control-Allow-Origin", "*")
res.Header().Set("Access-Control-Allow-Methods", "*")
res.Header().Set("Access-Control-Allow-Headers", "*")
```

To use this with VDO.Ninja then, in place of Meshcast, you can do:

(publisher) - `https://vdo.ninja/alpha/?whippush=https://myserver.io/api/whip&whippushtoken=steve&webcam&push=YjKcERS`
(viewer via WHEP via VDO.Ninja P2P)-  `https://vdo.ninja/alpha/?view=YjKcERS`
(viewer via WHEP directly) - `https://vdo.ninja/alpha/?hidemenu&whepplay=https://myserver.io/api/whep&whepplaytoken=steve `
```

You'll note you can view the stream two different ways using VDO.Ninja; as p2p guest or as a standalone viewer, which can be customized easily at https://vdo.ninja/alpha/whip

Also note, VDO.Ninja has an HTTP (non-SSL) domain version hosted at https://insecure.vdo.ninja, if you need to playback a WHEP feed from an insecure (http-only) WHEP server.  You can't easily mix HTTP and HTTPS protocols otherwise.

(We're using the alpha version of vdo.ninja in these examples because it has the newest code changes that might address reported bugs)

## API Design for Broadcast-box

The backend exposes three endpoints (the status page is optional, if hosting locally).

- `/api/whip` - Start a WHIP Session. WHIP broadcasts video via WebRTC.
- `/api/whep` - Start a WHEP Session. WHEP is video playback via WebRTC.
- `/api/status` - Status of the all active WHIP streams

[license-image]: https://img.shields.io/badge/License-MIT-yellow.svg
[license-url]: https://opensource.org/licenses/MIT

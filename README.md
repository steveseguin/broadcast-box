## Broadcast-box, modded for VDO.Ninja

changes:
- The web code is removed
- `whep` header value returned on whip publishing
- minor other tweak

The end result is a Meshcast-like alternative that you can self-host, but that also supports OBS WHIP output.  Broadcast-box I found to be a bit unstable mind you, although easy to deploy/mod, so consider this more a proof of concept for now. There are other SFU/WHIP/WHEP servers out there as well that you may wish to use instead, modifying as needed, as described below.

todo: 
- auto-generate stream token for publishers
- make more stable
- fix issues with OBS Studio WHIP output
- test iOS / iPad compatibility

### Install

To install on debian 12:
```
sudo apt-get install golang-go git -y
sudo ufw allow 80/tcp # add webserver port to firewall
sudo ufw allow 4000:65535/udp # add webrtc ports to firewall
cd ~
git clone https://github.com/steveseguin/broadcast-box-with-vdon-support
```
and to then run
```
cd ~
cd broadcast-box-with-vdon-support
APP_ENV=production go run .
```

publish to http://123.123.123.123:80/api/whip /w bearer token

view from http://123.123.123.123:80/api/whep /w bearer token

For use with VDO.Ninja, get a domain name and use Cloudflare as your DNS provider, making use of their Flexible SSL option. This will provided simple SSL without needing to deal with SSL ourselves. Once you SSL enabled, just use https://myserver.io/api/whip and https://myserver.io/api/whep instead. This will be far more VDO.Ninja friendly, as to publish video using VDO.Ninja to WHIP requires SSL.

## Use with VDO.Ninja

To use this with VDO.Ninja then, in place of Meshcast, you can do:

#### publisher
`https://vdo.ninja/alpha/?whippush=https://myserver.io/api/whip&whippushtoken=steve&webcam&push=YjKcERS`

#### viewer via WHEP via VDO.Ninja P2P
`https://vdo.ninja/alpha/?view=YjKcERS`

#### viewer via WHEP directly
`https://vdo.ninja/alpha/?hidemenu&whepplay=https://myserver.io/api/whep&whepplaytoken=steve `

You'll note you can view the stream two different ways using VDO.Ninja; as p2p guest or as a standalone viewer, which can be customized easily at https://vdo.ninja/alpha/whip

Also note, VDO.Ninja has an HTTP (non-SSL) domain version hosted at https://insecure.vdo.ninja, if you need to playback a WHEP feed from an insecure (http-only) WHEP server.  You can't easily mix HTTP and HTTPS protocols otherwise.

(We're using the alpha version of vdo.ninja in these examples because it has the newest code changes that might address reported bugs)

## Key technical modification being done here

For a WHIP + WHEP server to support "Meshcast"-like functionality in VDO.Ninja, the WHIP header response needs to contain the WHEP playback URL.  That was done to this modified version of Broadcast-box, but it can be done to pretty much any WHIP/WHEP server fairly easily.

`whep`:`/api/whep|streamtokenhere` is an example of what the header response could look like.

and for WHIP/WHEP servers that support it, `whep`:`/api/whep/streamtokenhere`, this also would

or a full URL can be provided, like this would work, `whep`:`https://whep.yourdomain.com/?token=streamtokenhere`

This can be seen in the code example below, which was applied to Broadcast-box in this forked version of it.
```
streamKey := r.Header.Get("Authorization")
trimmedStreamkey := strings.TrimPrefix(streamKey, "Bearer ") // remove the "Bearer" part from the stream key

res.Header().Add("Location", "/api/whip")
res.Header().Add("Content-Type", "application/sdp")

res.Header().Add("whep", "/api/whep|"+trimmedStreamkey)  // <<============== THIS IS IMPORTANT
res.Header().Set("Access-Control-Expose-Headers", "*")  // << ===== And this as well

res.Header().Set("Access-Control-Allow-Origin", "*")
res.Header().Set("Access-Control-Allow-Methods", "*")
res.Header().Set("Access-Control-Allow-Headers", "*")
```

Really rather simple, with more enhancements to the whole approach happening all the time, so stay-tuned.

## API Design for Broadcast-box

The backend exposes three endpoints (the status page is optional, if hosting locally).

- `/api/whip` - Start a WHIP Session. WHIP broadcasts video via WebRTC.
- `/api/whep` - Start a WHEP Session. WHEP is video playback via WebRTC.
- `/api/status` - Status of the all active WHIP streams

## Hosted alternative

I've already done a similar WHIP/WHEP Meshcast-like setup using Cloudflare, which is hosted as a service. If you don't want to host your own box, you can check out [https://cloudflare.vdo.ninja/](https://cloudflare.vdo.ninja/), which does everything we see above.  I'll add other options as I get to them, and maybe have official WHIP/WHEP support added to Meshcast itself at some point, too.

[license-image]: https://img.shields.io/badge/License-MIT-yellow.svg
[license-url]: https://opensource.org/licenses/MIT

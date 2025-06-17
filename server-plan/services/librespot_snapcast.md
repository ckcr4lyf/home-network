# Librespot + Snapcast

Note: My home server is running Ubuntu Server 22.04, and therefore some latest packages are not available, hence I do some manual hacky stuff.

## Architecture

My goal is to have my home server as an always available "Spotify Virtual Speaker", and whenever I select that in spotify (either via my mobile, or laptop), the audio is piped to snapserver, also running on my home server. Different devices can then use snapclient to play the synced audio.

## Librespot

As of 2025-06-18, librespot 0.6.0 is a must, since OAuth is needed to login to spotify (I intentionally do not want to rely on zeroconf / discoverable speakers).

I build it manually using [rustup](https://rustup.rs/) to install the rust toolchain, then cloning librespot and building it:

```
cd /tmp
git clone https://github.com/librespot-org/librespot.git
cd librespot
git checkout v0.6.0
cargo build --release
cp ./target/release/librespot /opt/librespot_0.6.0
```

(last command might require sudo)

## Snapcast

[Snapserver 0.27.0](https://github.com/badaix/snapcast/releases/tag/v0.27.0) fixes a bug that otherwise causes the program to crash with librespot, so it is the minimum version required. Although 0.31.0 is the lastest as of right now, I had issues installing it due to library dependencies and stuff not being available for Ubuntu 22.04.

```
cd /tmp
wget https://github.com/badaix/snapcast/releases/download/v0.27.0/snapserver_0.27.0-1_amd64.deb
dpkg -i snapserver_0.27.0-1_amd64.deb
```

## Snapserver Configuration

TODO

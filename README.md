# SubstratumNode

## Purpose
SubstratumNetwork is an open-source network that allows anyone to allocate spare computing resources to make the internet
a free and fair place for the entire world. It is a worldwide collection of nodes that securely delivers content without
the need of a VPN or Tor.

Because there's no single authority delivering or monitoring content, censorship and geo-restricted sites won't be an
issue on SubstratumNetwork. It doesn't matter where you live or what content you're accessing, everyone in the world
sees the exact same content.

**SubstratumNode** is the foundation of SubstratumNetwork.

It is what the average user runs to earn SUB and dedicate some of their computers' resources towards the network.
People who run a SubstratumNode can be rewarded with cryptocurrency for each time they serve content.

SubstratumNodes work together to relay CORES packages and content on the network.
When a user requests a site, nodes use artificial intelligence to find the most expedient and secure way to get the
information to that user. Multiple nodes work to fulfill a single request in order to maintain a necessary level of anonymity.

## Tools / Environment Setup
SubstratumNode software is written in Rust.
We use `rustup` to install what we need (e.g. rustc, cargo, etc). Follow instructions [here](https://www.rustup.rs/).

The `node` sub-project also uses `Docker` for a few things:
- **Running** SubstratumNode needs to be able to listen on port `53`, but Ubuntu 16.04 Desktop uses that port for
something else. So, we created the `Dockerfile` and helper scripts in `SubstratumNode/node/docker/linux_node/` to
allow SubstratumNode to run on that platform. It isn't needed for running on Mac or Windows, or on Ubuntu 16.04 Server.
- **Testing** `Docker` also offered a convenient way for us to run our end-to-end integration tests for Mac and Linux on CI.
SubstratumNode needs to be started with `root` privileges in order to connect to certain ports (e.g. `53`, `80`).
The Jenkins agent that runs on Windows has the needed privileges by default, but the agents that run on Mac and Linux
do not. We created the `Dockerfile` in `SubstratumNode/node/docker/integration_tests` to work around this limitation.

You'll need an Internet connection when you build so that `cargo` can pull down dependencies.

## How To
We build and run tests for SubstratumNode using bash scripts located in the `ci` directory of each sub-project.
Use `ci/all.sh` at the top level to clean, build, and run tests for all sub-projects in one step.
It will run the `node` integration tests as well, so if you're running on Mac or Linux make sure the `Docker` daemon is running.
_On Windows, we run this script in a git bash terminal._

_Wondering where all our tests are? The convention in Rust is to write unit tests in same file as the source._

### Run SubstratumNode locally

_Note: Currently, your DNS must be manually set to `127.0.0.1` in order to route traffic through SubstratumNode.
Find instructions for your platform [here](https://github.com/SubstratumNetwork/SubstratumNode/tree/master/node/docs)._

Once you've successfully built the `node` executable and completed the manual setup steps,
you can run SubstratumNode locally from the command line:
```
<path to workspace>/SubstratumNode/node/target/release/node --dns_servers 8.8.8.8
```
In the above example, we're using Google's DNS, `8.8.8.8`, but you can use your preferred DNS.
If you can't choose just one favorite DNS, you can also specify multiple, separated by a comma (`,`).

_Why do we send in `dns_servers`? SubstratumNodes still need to talk to the greater Internet.
See [the ProxyClient README](https://github.com/SubstratumNetwork/SubstratumNode/tree/master/proxy_client_lib)
for more information._

# Disclosure

We run tests on every push to `master` on these platforms:
- Ubuntu 16.04 Desktop 64-bit
- MacOS High Sierra
- Windows 10 64-bit

SubstratumNode doesn't reliably build on 32-bit Windows due to issues with the build tools for that platform. We recommend using a 64-bit version to build.

We do plan to release binaries that will run on 32-bit Windows, but they will likely be built on 64-bit Windows.


Copyright (c) 2017-2018, Substratum LLC (https://substratum.net) and/or its affiliates. All rights reserved.



# Step-By-Step Guide for Lubuntu 16.04.4
Added by AdamW 2018-04-30 _Please feel free to copy, modify, or use the following guide as you fit_

_Note: This guide is based off SubstratumNode 0.3.0 and Lubuntu 16.04.4 running in live mode from RAM, while it should be easy to_
_replicate on any *buntu flavor, your miliage may vary._

DISCLAIMER: Guess what, I'm going to keep this short. If you have root(or user!) access to the environment you are using, then YOU are
responsible for the code that you execute, so at least have a basic understanding of what you are running. (Or boot up a livecd/usb 
session and not worry about breaking something ;)

All commands in `Code Boxes` should be copy paste-able into a teminal. They are prefaced with `sudo ` where root privileges are needed.
If you don't want to type your password for every command, run `sudo su` to run all subsequent commands with root privileges, then just
omit the `sudo ` from your copy-past commands.


1. Refresh APT repositories:
```
sudo apt update
```
2. Install curl:
```
sudo apt install curl
```
3. Install rust.
  Navigate to [here](https://www.rustup.rs/), copy and run the code provided.
  Or just paste this (should be the same as link.):
```
curl https://sh.rustup.rs -sSf | sh
```
4. Install git:
```
sudo apt install git
```
5. Clone(download) a local copy of the source code from Github: (This will clone the repository into whaterver folder
  your terminal is currently at, CTL+ALT+T should open a terminal pointed at your home directory.)
```
git clone https://github.com/SubstratumNetwork/SubstratumNode.git
```
6. Install Docker:
```
sudo apt install docker.io
```
7. Make sure Docker is running:
```
systemctl status docker
```
8. If Docker is NOT running try:
```
systemctl start docker
```
9. Install gcc:
```
sudo apt install gcc
```
10. Install Make:
```
sudo apt install make
```
11. Install libssl-dev:
```
sudo apt install libssl-dev
```
12. Install pkg-config:
```
sudo apt install pkg-config
```
13. Log out and log back in to set Cargo's(rust package manager) bin directory to you shell PATH environment variable permanently. 
    Or, you can:
    Add Cargo's bin directory to you shell PATH environment variable:
    (NOTE: This only sets the PATH for the current terminal window so run the next step(step 14) in the same terminal window, otherwise it will fail.)
```
source $HOME/.cargo/env
```
14. Time to compile! Navigate to your home directory, you should see a folder named `SubstratumNode`.
    You should be able to run the command below if your `git clone ` (step 5, above) was executed in your home folder.
    This will take more then a couple minutes, so go make yourself some Hibiscus tea to replenish your antioxidants.
```
$HOME/SubstratumNode/ci/all.sh
```
15. xhost permissions:
    It took me awhile to figure this one out, I think the Docker container needs xhost permissions to connect to sockets(not sure).
    You may want to run `xhost` (no plus sign) first, to see what permissions are set so you can revert later.
    The command below allows any user or group to connect(removes all access control).
    WARNING: I do not know the full implications of removing all access control, I am running in a live session so it does not matter to me.
    Do you your research!
```
xhost +
```
16. Open a separate terminal to see what is going on in your SubstratumNode Docker container:
    This will start showing stats after the final commands (step 19 and 18) have completed.
```
sudo docker stats
```
17. Navigate to `SubstratumNode/node/docker/linux_node` folder and run:
    This will download some Docker container stuff(Firefox I think)
```
sudo ./build.sh
```
18. Close all instances of Firefox, otherwise the next command will fail.
    Navigate to `SubstratumNode/node/docker/linux_node` folder and run:
```
sudo ./run.sh
```


    You should see an outdated Firefox (version 41.0.2) window open, and you should see Docker container stats from step 16.
    Now go to (https://substratum.net) and rejoice in the beginnings of the decentralized web!!

AdamW


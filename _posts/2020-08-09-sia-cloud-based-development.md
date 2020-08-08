---
title: Sia Cloud-Based Storage Development
tags: Blockchain
---

> Sia is the leading decentralized cloud storage platform. No signups, no servers, no trusted third parties. Sia leverages blockchain technology to create a data storage marketplace that is more robust and more affordable than traditional cloud storage providers.

## Development Environment

### Development Platform

-The most basic development requirements
    -Get the complete [Sia](https://github.com/NebulousLabs/Sia) source code
    -Can edit source code
    -Configure [Go](https://golang.org/) language development environment
-Recommended development environment
    -Operating system: [Ubuntu Desktop 18.04](https://www.ubuntu.com/download/desktop?)
    -Integrated development environment: [GoLand](https://www.jetbrains.com/go/https://www.jetbrains.com/go/) (available through [Toolbox App](https://www.jetbrains. com/toolbox/app/) installation, the account can be through the Huake mailbox [register] (https://www.jetbrains.com/shop/eform/students), get the full version)

#### Ubuntu Desktop configuration Go development environment

1. [Download](https://golang.org/dl/) the Go binary package of the corresponding version
1. Install (unzip) the binary package to a custom path (recommended `/usr/local`)

    ```sh
    sudo tar -C /usr/local -xzf go$VERSION.$OS-$ARCH.tar.gz
    ```

1. Configure environment variables and append the following to `~/.bashrc` (`GOROOT` is the installation path of the previous step, `GOPATH` can be customized)

    ```sh
    export GOROOT=/usr/local/go
    export GOPATH=$HOME/Documents/Go
    export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
    ```

1. Check the environment configuration with `go env`

    ```sh
    akashsharma@192.168.127.100 ~ $ go env
    GOARCH="amd64"
    GOBIN=""
    GOCACHE="/home/akashsharma/.cache/go-build"
    GOEXE=""
    GOHOSTARCH="amd64"
    GOHOSTOS="linux"
    GOOS="linux"
    GOPATH="/home/akashsharma/Documents/Go"
    HOT = ""
    GOROOT="/usr/local/go"
    GOTMPDIR=""
    GOTOOLDIR="/usr/local/go/pkg/tool/linux_amd64"
    GCCGO="gccgo"
    CC="gcc"
    CXX="g++"
    CGO_ENABLED="1"
    CGO_CFLAGS = "- g -O2"
    CGO_CPPFLAGS = ""
    CGO_CXXFLAGS = "- g -O2"
    CGO_FFLAGS="-g -O2"
    CGO_LDFLAGS="-g -O2"
    PKG_CONFIG="pkg-config"
    GOGCCFLAGS="-fPIC -m64 -pthread -fmessage-length=0 -fdebug-prefix-map=/tmp/go-build200491170=/tmp/go-build -gno-record-gcc-switches"
    ```

> [Install the Go tools](https://golang.org/doc/install#install)

#### GoLand configuration debugging

Sia source code can generate two executable binary files *siad* and *siac*. When using GoLand for development, the package they belong to can be used as the entry point to start debugging, and the configuration information for debugging can be set according to the *Makefile* file

1. Start `Run/Debug Configurations` via the top menu `Run -> Edit Configurations...`
1. Add a new debug configuration via `Add New Configuration -> Go Build`
1. In the `Configuration` tab, set `Go tool arguments` to

    `-tags "dev debug profile netgo" -ldflags "-X github.com/NebulousLabs/Sia/build.GitRevision=DEBUG_COMMIT -X github.com/NebulousLabs/Sia/build.BuildTime=DEBUG_TIME"`

    Other settings are as follows (take *siad* as an example)

    | Item              | Configuration                                                                          |
    | ----------------- | -------------------------------------------------------------------------------------- |
    | Run kind          | Package                                                                                |
    | Package path      | github.com/NebulousLabs/Sia/cmd/siad                                                   |
    | Run after build   | Checked                                                                                |
    | Program arguments | --modules=g --sia-directory=/home/akashsharma/sia --profile-directory=/home/akashsharma/sia/profiles |
    | Module | Let |

    >-Start debugging with the above configuration, which is equivalent to running `siad --modules=g --sia-directory=/home/akashsharma/sia --profile-directory=/home/akashsharma/ after compiling *siad* Debug when sia/profiles`
    >-The configuration of `Go tool arguments` is consistent with *Makefile*, `DEBUG_COMMIT` and `DEBUG_TIME` can be replaced with actual values ​​according to the settings in *Makefile*
    >-`Package path` and `Program arguments` can be compiled and run according to the actual package of the `main` function to be run (for example, set to `github.com/NebulousLabs/Sia/cmd/siac` when debugging *siac*) and start Set the actual operating parameters of the corresponding program

### testing platform

-The most basic test requirements
    -Any operating system supported by Go language
    -A host
-Recommended test environment
    -Virtualization platform: [VMware Workstation Pro 14](https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html)
    -Virtual machine operating system: [Ubuntu Server 18.04](https://www.ubuntu.com/download/server) (No need to install Go)
    -Virtual machine configuration: 1GB memory, 8GB storage
    -A cluster composed of multiple virtual hosts

#### Ubuntu Server configure static IP

Assuming that the network adapter of the virtual machine is set to NAT, and the gateway of the NAT adapter has been set to *192.168.127.2*, you want to set the static IP of the virtual machine to *192.168.127.21* and the subnet mask to *255.255.255.0*

1. Modify the configuration file under the path of `/etc/netplan/` (default is `50-cloud-init.yaml`)

    `` Yaml
    # This file is generated from information provided by
    # the datasource.  Changes to it will not persist across an instance.
    # To disable cloud-init's network configuration capabilities, write a file
    # /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
    # network: {config: disabled}
    network:
        ethernets:
            ens33:
                addresses: [192.168.127.21/24]
                dhcp4: no
                optional: true
                gateway4: 192.168.127.2
        version: 2
    ```

1. Application configuration items

    ```sh
    sudo netplan apply
    ```

> [Configure Static IP Addresses On Ubuntu 18.04 LTS Server](https://websiteforstudents.com/configure-static-ip-addresses-on-ubuntu-18-04-beta/)、[Ubuntu Config File](https://github.com/Akash-Sharma-1/script-tools/blob/master/ubuntu/config-ip.sh)

## Compile the source code

*Makefile* files include `release` (standard version), `dev` (development version), and `debug` (debugging version) by default. Three conventional compilation targets and compilation targets each containing data competition detection are included in the development process. `dev` version

> [Build Sia](https://github.com/NebulousLabs/Sia/blob/master/doc/Guide%20to%20Contributing%20to%20Sia.md#build-sia)

### Local compilation

Execute `make dev` in the root directory of *Sia*, and generate executable files *siac* and *siad* in `$GOPATH/bin`

> The first compilation target declared in the *Makefile* file is the default compilation target (usually named `all`), and the setting in this project is
>
> ```makefile
> # all will build and install release binaries
> all: release
> ```
>
> That is, `all` is used as the compilation target by default (`all` calls the compilation target `release`), so the effect of using `make`, `make all`, and `make release` is the same
>
> If you want to modify the default compilation target to `dev`, you can modify the above settings to
>
> ```makefile
> # all will build and install dev binaries
> all: dev
> ```

### Cross compilation

1. For the target platform (Windows, Linux, OpenBSD, etc.) and compilation target (release, dev, debug, etc.) of cross-compilation, modify the corresponding compilation instructions in the *Makefile* file to specify the target operating system (`GOOS`) and system Architecture (`GOARCH`)
1. Execute `make dev` in the *Sia* root directory, and generate the corresponding executable file in `$GOPATH/bin/$GOOS_$GOARCH`

If you want to cross compile the Windows version under Linux, you can modify it in the *Makefile* file

```makefile
# dev builds and installs developer binaries.
dev:
    go install -tags='dev debug profile netgo' -ldflags='$(ldflags)' $(pkgs)
```

for

```makefile
# dev builds and installs developer binaries.
dev:
    GOOS=windows GOARCH=amd64 go install -tags='dev debug profile netgo' -ldflags='$(ldflags)' $(pkgs)
```

> [Building windows go programs on linux](https://github.com/golang/go/wiki/WindowsCrossCompiling)、[Optional environment variables](https://golang.org/doc/install/source#environment)

## Build a private test network

When building a private test network, the networking process of the network mainly includes [Manually set up the connection node] (#Manually set up the connection node) and [Automatically discover the network node] (# Automatic discovery network node)

It is assumed that there is a development platform that meets the most basic development requirements, and a multi-node virtual machine cluster has been built, and each node can visit each other through the network. (The following configuration assumes that the virtual machine is on the same internal network, the subnet mask is *255.255.255.0*, and the gateway is *192.168.127.2*)

The configuration of each type of node in the cluster is as follows

| Node   | IP             | Type      | api-addr       | rpc-addr | host-addr | sia-directory   |
| ------ | -------------- | --------- | -------------- | -------- | --------- | --------------- |
| Node 0 | 192.168.127.21 | Bootstrap | localhost:9980 | :9981    |           | ~/sia/bootstrap |
| Node 1 | 192.168.127.21 | Miner | localhost: 9990 | : 9991 | | ~ / sia / miner |
| Node 2 | 192.168.127.22 | Host      | localhost:9980 | :9981    | :9982     | ~/sia/host      |
| Node 3 | 192.168.127.23 | Interest | localhost: 9980 | : 9981 | | ~ / sia / renter |

> A single node can be used as a multi-purpose node by combining multiple modules
>
> A single host can run multiple Sia instances (such as *Node 0* and *Node 1* in *192.168.127.21*) by setting different ports and data storage paths for *siad*
>
> You can view related port description information through `./siad --help`
>
>-`api-addr` is used to access the API, *siac* interacts with *siad* through this host and port (default is `localhost:9980`)
>-`rpc-addr` is used for communication between nodes, the *gateway* module connects with other Sia instances through this port (default is `:9981`)
>-`host-addr` is used by the Host node to monitor file upload and download requests (default is `:9982`)

### Manually set the connection node

When a node joins the network, you need to manually specify the Sia instance to be connected, and then the node can obtain information about other nodes in the network from the connected node, and establish connections with other nodes

Since hard-coded Bootstrap nodes are not used, the *Node 0* node in the cluster can be started first, assumes the function of Bootstrap, and serves as a new node to join the network and the entry for new nodes to obtain other node information in the network; the new node is in [Start Background service process] (#Start background service process sia-instance), **need to set the `--no-bootstrap` parameter**, and when [join network] (#join network), set *Node 0* as The connected objects will eventually form a P2P network

#### Set the operating environment

1. Create the virtual machine used to build the cluster, [Configure the IP of each virtual machine](#ubuntu-server-Configuration static-ip)
1. Create a folder to save node data
1. Upload the `dev` version *siad* and *siac* of [Compile](#Compile source code) to each virtual machine (you can use [scp](https://linux.die.net/man/1/scp ) Command)

    ```sh
    akashsharma@192.168.127.100 bin $ scp siac siad akashsharma@192.168.127.21: ~ / sia
    siac                                          100%   12MB  54.5MB/s   00:00
    siad                                          100%   18MB  65.7MB/s   00:00
    ```

    > 参考 [Transferring files over SSH](https://stackoverflow.com/questions/343711/transferring-files-over-ssh)

#### Start the background service process (Sia instance)

Run the Sia instance through *siad*, start the background service process of each node, and load the modules required by each node

1. Create a new terminal and log in to the corresponding node through *ssh*
1. Enter the directory where *siad* is saved
1. View related commands and parameter setting information through `./siad --help`

    ```sh
    akashsharma@192.168.127.21 sia $ ./siad --help
    Running with debugging enabled
    Both Daemon v1.3.3

    Usage:
    ./siad [flags]
    ./siad [command]

    Available Commands:
    help        Help about any command
    modules     List available modules for use with -M, --modules flag
    version     Print version information

    Flags:
        --agent string               required substring for the user agent (default "Sia-Agent")
        --api-addr string            which host:port the API server listens on (default "localhost:9980")
        --authenticate-api           enable API password protection
        --disable-api-security       allow siad to listen on a non-localhost address (DANGEROUS)
    -h, --help                       help for ./siad
        --host-addr string           which port the host listens on (default ":9982")
    -M, --modules string             enabled modules, see 'siad modules' for more info (default "cghrtw")
        --no-bootstrap               disable bootstrapping on this run
        --profile string             enable profiling with flags 'cmt' for CPU, memory, trace
        --profile-directory string   location of the profiling directory (default "profiles")
        --rpc-addr string            which port the gateway listens on (default ":9981")
    -d, --sia-directory string       location of the sia directory

    Use "./siad [command] --help" for more information about a command.
    ```

1. Check whether the compiled version is the `dev` version through `./siad version`

    ```sh
    akashsharma@192.168.127.21 sia $ ./siad version
    Running with debugging enabled
    Sia Daemon v1.3.3-dev
    ```

1. Start the background service process through `./siad --modules gctwrhm` (you may need to select only some modules to start according to the node type) (**Need to set the parameter `--no-bootstrap`**)
    - Node 0 - Bootstrap

        ```sh
        akashsharma@192.168.127.21 sia $ ./siad --modules=g --no-bootstrap --sia-directory=bootstrap
        Running with debugging enabled
        Both Daemon v1.3.3
        Git Revision 4c73a7dd
        Loading...
        (0/1) Loading siad ...
        (1/1) Loading gateway...
        Finished loading in 0.066843548 seconds
        ```

    - Node 1 - Miner

        ```sh
        akashsharma@192.168.127.21 sia $ ./siad --modules=gctwm --no-bootstrap --api-addr=localhost:9990 --rpc-addr=:9991 --sia-directory=miner
        Running with debugging enabled
        Both Daemon v1.3.3
        Git Revision 4c73a7dd
        Loading...
        (0/5) Loading siad ...
        (1/5) Loading gateway...
        (2/5) Loading consensus...
        (3/5) Loading transaction pool...
        (4/5) Loading wallet...
        (5/5) Loading miner...
        Finished loading in 0.163977087 seconds
        ```

    - Node 2 - Host

        ```sh
        akashsharma@192.168.127.22 sia $ ./siad --modules=gctwh --no-bootstrap --sia-directory=host
        ...
        (5/5) Loading host...
        Finished loading in 0.013928993 seconds
        ```

    - Node 3 - Renter

        ```sh
        akashsharma@192.168.127.23 sia $ ./siad --modules=gctwr --no-bootstrap --sia-directory=renter
        ...
        (5/5) Loading renter...
        Finished loading in 0.021479162 seconds
        ```

#### Join the network

Through the commands provided by *siac*, interact with the background service process, call the API provided by its *gateway* module, and join the P2P network

1. Create a new terminal and log in to the corresponding node through `ssh`
1. Enter the directory where *siac* is saved
1. View relevant help information through `./siac --help` (APIs provided by *siad* can be called through related commands)

    ```sh
    akashsharma@192.168.127.21 and $ ./siac --help
    Both Client v1.3.3

    Usage:
    ./siac [flags]
    ./siac [command]

    Available Commands:
    bash-completion Creates bash completion file.
    consensus       Print the current state of consensus
    gateway         Perform gateway actions
    help            Help about any command
    host            Perform host actions
    hostdb          Interact with the renter's host database.
    man-generation  Creates unix style manpages.
    miner           Perform miner actions
    renter          Perform renter actions
    stop            Stop the Sia daemon
    update          Update Sia
    version         Print version information
    wallet          Perform wallet actions

    Flags:
    -a, --addr string          which host/port to communicate with (i.e. the host/port siad is listening on) (default "localhost:9980")
        --apipassword string   the password for the API's http authentication
    -h, --help                 help for ./siac
        --useragent string     the useragent used by siac to connect to the daemon's API (default "Sia-Agent")

    Use "./siac [command] --help" for more information about a command.
    ```

1. For non-Bootstrap nodes (Miner, Host, Renter), establish a connection with Bootstrap nodes through `./siac gateway connect 192.168.127.21:9981` and join the P2P network
    - Node 1 - Miner

        ```sh
        akashsharma@192.168.127.21 sia $ ./siac --addr=localhost:9990 gateway connect 192.168.127.21:9981
        Added 192.168.127.21:9981 to peer list.
        akashsharma@192.168.127.21 sia $ ./siac --addr=localhost:9990 gateway list
        1 active peers:
        Version  Outbound  Address
        1.3.3    Yes       192.168.127.21:9981
        ```

    - Node 2 - Host

        ```sh
        akashsharma@192.168.127.22 sia $ ./siac gateway connect 192.168.127.21:9981
        Added 192.168.127.21:9981 to peer list.
        akashsharma@192.168.127.22 sia $ ./siac gateway list
        2 active peers:
        Version  Outbound  Address
        1.3.3    Yes       192.168.127.21:9981
        1.3.3    Yes       192.168.127.21:9991
        ```

    - Node 3 - Renter

        ```sh
        akashsharma@192.168.127.23 sia $ ./siac gateway connect 192.168.127.21:9981
        Added 192.168.127.21:9981 to peer list.
        akashsharma@192.168.127.23 sia $ ./siac gateway list
        3 active peers:
        Version  Outbound  Address
        1.3.3    Yes       192.168.127.21:9981
        1.3.3    Yes       192.168.127.21:9991
        1.3.3    No        192.168.127.22:9981
        ```

    > Connect miner, host, renter and Bootstrap in turn, the new node will be connected to other nodes in the network in addition to the Bootstrap node

#### Wallet operation

##### Initialize the wallet

For nodes that use the *wallet* module (Miner saves mining rewards, Host needs to provide mortgages before launching shared storage services, and Renter needs to pay a prepayment before using shared storage), initialize via `./siac wallet init --password` Wallet and set password

```sh
akashsharma@192.168.127.21 sia $ ./siac --addr=localhost:9990 wallet init --password
Wallet password:
Confirm:
Recovery seed:
awning myth blip hexagon identity goodbye dyslexic debut mammal actress pulp upstairs lobster autumn adult vehicle twofold stellar batch jittery essential pause onslaught deftly saxophone ringing remedy reef aggravate

Wallet encrypted with given password
```

> The `--addr` parameter is used to specify the *siad* background service process of the communication (the default is `localhost:9980`)

##### Unlock wallet

The wallet must be used for node-related operations, or the wallet must be unlocked through `./siac wallet unlock` before performing any operations on the wallet, otherwise the related operations will not work properly. If the Miner node does not unlock the wallet before starting the first mining, it will not be possible Receiving mining rewards

```sh
akashsharma@192.168.127.21 sia $ ./siac --addr=localhost:9990 wallet unlock
Wallet password:
```

##### Create account address

When the [transfer](#transfer-miner---host) operation is required, the recipient of the transfer needs to create a new account address for the wallet through `./siac wallet address` in advance, and can use `./siac wallet addresses` View all account addresses in the wallet

```sh
akashsharma@192.168.127.22 sia $ ./siac wallet address
Created new address: 17d05e536d6cd6ccfcfeb0688325464f69b75b960df6dd1a5caded822ee80f98e47654dd8181
akashsharma@192.168.127.22 sia $ ./siac wallet addresses
17d05e536d6cd6ccfcfeb0688325464f69b75b960df6dd1a5caded822ee80f98e47654dd8181
```

> In the subsequent operations, only the Host node and the Renter node provide or use shared storage to pay for Siacoin, so only these two types of nodes need to manually create account addresses; because the Miner node can automatically generate and receive rewards after the wallet is unlocked while mining Account address, so there is no need to manually create an account address

##### View balance

For nodes that use the *wallet* module, you can check the balance in the wallet through `./siac wallet balance`

```sh
akashsharma@192.168.127.21 sia $ ./siac --addr=localhost:9990 wallet balance
Wallet status:
Encrypted, Unlocked
Height:              0
Confirmed Balance:   0 H
Unconfirmed Delta:  +0 H
Exact:               0 H
Siafunds:            0 SF
Siafund Claims:      0 H

Estimated Fee:       30 mS / KB
```

#### Test the built network

##### Mining Miner

The Miner node obtains Siacoin through mining, and performs [transfer](#transfer-miner---host) to the Host and Renter nodes, which is [shared storage](#share storage-host) and [file upload download](#文件Upload and download -renter---host) provide transaction support and confirm the generated transaction

1. Miner node runs *miner* module through `./siac miner start` to start mining

    ```sh
    akashsharma@192.168.127.21 sia $ ./siac --addr=localhost:9990 miner start
    CPU Miner is now running.
    ```

1. Miner node ends mining through `./siac miner stop`

    ```sh
    akashsharma@192.168.127.21 sia $ ./siac --addr=localhost:9990 miner stop
    Stopped mining.
    ```

1. Each node ((Miner, Host, Renter) checks the synchronization of the blockchain through `./siac consensus` (Because the Bootstrap node does not start the *consensus* module, the blockchain is not synchronized, and the blocks of other nodes The chain is completely synchronized, and the query results are exactly the same)

    ```sh
    akashsharma@192.168.127.21 sia $ ./siac --addr=localhost:9990 consensus
    Synced: Yes
    Block:      0000007573fc48b79136a60f5597fc34f34603c5e816a241d9b2390db18b0219
    Height:     500
    Target:     [0 0 1 5 17 132 33 158 236 175 6 108 67 120 9 5 174 115 191 213 7 247 6 193 127 240 191 222 39 32 139 209]
    Difficulty: 16451500
    ```

1. Miner node [check the balance in the wallet] (#check the balance)

    ```sh
    akashsharma@192.168.127.21 sia $ ./siac --addr=localhost:9990 wallet balance
    Wallet status:
    Encrypted, Unlocked
    Height:              500
    Confirmed Balance:   146.9 MS
    Unconfirmed Delta:  +0 H
    Exact:               146879705000000000000000000000000 H
    Siafunds:            0 SF
    Siafund Claims:      0 H

    Estimated Fee:       30 mS / KB
    ```

##### Transfer Miner -> Host

The Miner node transfers the Siacoin obtained by [Mining](# Mining-miner) to the Host node, and the Host node uses the Siacoin obtained as collateral to provide shared storage services

1. Host node [Create account address] (#Create account address)
1. The Miner node sends Siacoins to the Host node through `./siac wallet send siacoins [amount] [dest]`

    ```sh
    akashsharma@192.168.127.21 sia $ ./siac --addr=localhost:9990 wallet send siacoins 50MS 17d05e536d6cd6ccfcfeb0688325464f69b75b960df6dd1a5caded822ee80f98e47654dd8181
    Sent 50000000000000000000000000000000 hastings to 17d05e536d6cd6ccfcfeb0688325464f69b75b960df6dd1a5caded822ee80f98e47654dd8181
    ```

1. The Host node uses `./siac wallet balance` to check the balance of the wallet, and `./siac wallet transactions` to check the relevant transfer transaction information
    -When the Miner node does not start mining, the transaction is not recorded in the blockchain and the transfer is not completed

        ```sh
        akashsharma@192.168.127.22 sia $ ./siac wallet balance
        Wallet status:
        Encrypted, Unlocked
        Height:              500
        Confirmed Balance:   0 H
        Unconfirmed Delta:  +50 MS
        Exact:               0 H
        Siafunds:            0 SF
        Siafund Claims:      0 H

        Estimated Fee:       30 mS / KB
        akashsharma@192.168.127.22 sia $ ./siac wallet transactions
                    [timestamp]    [height]                                                   [transaction id]    [net siacoins]   [net siafunds]
                    unconfirmed unconfirmed   b7b37e7293b9e17867b076f11441e3fbc2dbd5195da05f64e8dc40d235a0c06c    50000000.00 SC             0 SF
        ```

    -After the Miner node starts mining, the transaction is confirmed and the transfer is completed

        ```sh
        akashsharma@192.168.127.22 sia $ ./siac wallet balance
        Wallet status:
        Encrypted, Unlocked
        Height:              504
        Confirmed Balance:   50 MS
        Unconfirmed Delta:  +0 H
        Exact:               50000000000000000000000000000000 H
        Siafunds:            0 SF
        Siafund Claims:      0 H

        Estimated Fee:       30 mS / KB
        akashsharma@192.168.127.22 sia $ ./siac wallet transactions
                    [timestamp]    [height]                                                   [transaction id]    [net siacoins]   [net siafunds]
        2018-07-05 04:43:45+0800         501   b7b37e7293b9e17867b076f11441e3fbc2dbd5195da05f64e8dc40d235a0c06c    50000000.00 SC             0 SF
        ```

##### Shared Storage Host

The Host node announces the shared storage to the network and accepts the contract by setting the shared storage path and size, thereby providing shared storage services

1. Create a new folder on the Host node as the data storage path for shared storage

    ```sh
    akashsharma@192.168.127.22 sia $ mkdir share_storage
    ```

1. The Host node adds a shared storage data storage path through `./siac host folder add [path] [size]` and sets the shared space size

    ```sh
    akashsharma@192.168.127.22 sia $ ./siac host folder add ~/sia/share_storage 512MB
    Added folder /home/akashsharma/sia/share_storage
    ```

1. The Host node announces the start of shared storage service to the network through `./siac host announce [address]`

    ```sh
    akashsharma@192.168.127.22 sia $ ./siac host announce 192.168.127.22:9982
    Host announcement submitted to network.
    The host has also been configured to accept contracts.
    To revert this, run:
        siac host config acceptingcontracts false
    ```

    > Check `Sia/modules/host/announce.go` to know
    >
    > 1. In the internal network test, executing `./siac host announce` will eventually call the `func (h *Host) Announce() error` method in the above source file in the Siad instance, due to `h.settings.NetAddress `And `h.autoAddress` both return `""`, so `h.managedAnnounce(annAddr)` will not be executed, and the announcement process cannot be completed (no error will be reported)
    >
    > 1. In the intranet test, use the official source code directly compiled version, execute `./siac host announce [address]`, and finally call `func (h *Host) AnnounceAddress in the above source file in the Siad instance (addr modules.NetAddress) error` method, because the internal network address is provided during the test, the judgment statement `if addr.IsLocal()` cannot be passed when executing, so you need to comment the relevant statement, and then re [compile with the modified source code siad](#Compile the source code), and upload to the corresponding Host node, execute `./siac host announce [address]`
    >
    >     ```go
    >     //if addr.IsLocal() {
    >     //    return errors.New("announcement requested with local net address")
    >     //}
    >     ```

1. The Host node uses `./siac host` to view the current settings of shared storage

    ```sh
    akashsharma@192.168.127.22 and $ ./siac host
    Host info:
        Connectability Status: Host is not connectable (re-checks every few minutes).

        Storage:      503.32 MB (0 B used)
        Price:        50 SC / TB / Month
        Max Duration: 25 Weeks

        Accepting Contracts:  Yes
        Anticipated Revenue:  0 H
        Locked Collateral:    0 H
        Revenue:              0 H

    Storage Folders:
        Used    Capacity     % Used    Path
        0 B     503.32 MB    0.00      /home/akashsharma/sia/share_storage
    ```

1. The Renter node uses `./siac hostdb` to view the information of the Host node that announced the provision of shared storage services in the current network

    ```sh
    akashsharma@192.168.127.23 and $ ./siac hostdb
    1 Active Hosts:
        Address Price (per TB per Mo)
    1:  192.168.127.22:9982  50 SC
    ```

##### File upload and downloadRenter <-> Host

The Renter node obtains the shared storage service by presetting the allowance (prepayment) to be paid to the Host node during the file upload and download process

1. The Renter node obtains Siacoin from the Miner node through a process similar to [Miner transfer to Host](# Transfer-miner---host)
1. The Renter node uses `./siac renter prices` to view the shared storage prices in the network

    ```sh
    akashsharma@192.168.127.23 sia $ ./siac renter prices
    Renter Prices (estimated):
    Fees for Creating a Set of Contracts:   3.96 SC
    Download 1 TB:                          25 SC
    Store 1 TB for 1 Month:                 150 SC
    Upload 1 TB:                            3 SC
    ```

1. The Renter node uses `./siac renter setallowance [amount] [period] [hosts] [renew window]` to set the total amount of Siacoin that can be spent in a given period when using the shared storage service provided by the Host node, that is According to the price of shared storage in the network, the prepayment that needs to be paid before using the shared storage service

    ```sh
    akashsharma@192.168.127.23 sia $ ./siac renter setallowance 100SC 100b 1 10b
    Allowance updated.
    ```

    > You can view specific parameter setting instructions through `./siac renter setallowance --help`
    >
    >-`amount` -> `100SC` is set to spend at most 100SC in a period, that is, the prepayment in a shared storage service period is 100SC
    >-`period` -> `100b` Set a period length to 100 blocks
    >-`hosts` -> `1` Set the data of the Renter node to be distributed on 1 Host node (because there is only one Host node in the test network)
    >-`renew windows` -> `10b` Set the time window for automatic renewal of allowance (prepayment) to 10 blocks, and the contract signed by Renter and Host will be automatically updated after the time window is reached
1. The Renter node uploads files via `./siac renter upload [source] [path]`, and checks the status of all files via `./siac renter list`

    ```sh
    akashsharma@192.168.127.23 sia $ ./siac renter upload test_file test
    Uploaded '/home/akashsharma/sia/test_file' as test.
    akashsharma@192.168.127.23 sia $ ./siac renter list
    Tracking 1 files:
    Total uploaded:  12.09 MB
    12.09 MB  test
    ```

1. The Host node uses `./siac host` to view the shared storage space usage

    ```sh
    akashsharma@192.168.127.22 and $ ./siac host
    ...
    Storage Folders:
        Used        Capacity     % Used    Path
        12.32 MB    503.32 MB    2.45      /home/akashsharma/sia/share_storage
    ```

1. The Renter node downloads files through `./siac renter download [path] [destination]`

    ```sh
    akashsharma@192.168.127.23 sia $ ./siac renter download test test_file2
    Downloading...  95.4% of 12.09 MB, 1s elapsed, 61.42 Mbps
    Downloaded 'test' to /home/akashsharma/sia/test_file2.
    ```

---

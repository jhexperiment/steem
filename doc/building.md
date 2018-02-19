# Building Steem

## Compile-Time Options (cmake)

### CMAKE_BUILD_TYPE=[Release/Debug]

Specifies whether to build with or without optimization and without or with
the symbol table for debugging. Unless you are specifically debugging or
running tests, it is recommended to build as release.

### LOW_MEMORY_NODE=[OFF/ON]

Builds steemd to be a consensus-only low memory node. Data and fields not
needed for consensus are not stored in the object database.  This option is
recommended for witnesses and seed-nodes.

### CLEAR_VOTES=[ON/OFF]

Clears old votes from memory that are no longer required for consensus.

### BUILD_STEEM_TESTNET=[OFF/ON]

Builds steem for use in a private testnet. Also required for building unit tests.

### SKIP_BY_TX_ID=[OFF/ON]

By default this is off. Enabling will prevent the account history plugin querying transactions
by id, but saving around 65% of CPU time when reindexing. Enabling this option is a
huge gain if you do not need this functionality.

## Building under Docker

We ship a Dockerfile.  This builds both common node type binaries.

    git clone https://github.com/steemit/steem
    cd steem
    docker build -t steemit/steem .

## Building on Ubuntu 16.04

For Ubuntu 16.04 users, after installing the right packages with `apt` Steem
will build out of the box without further effort:

    # Required packages
    sudo apt-get install -y \
        autoconf \
        automake \
        cmake \
        g++ \
        git \
        libssl-dev \
        libtool \
        make \
        pkg-config \
        python3 \
        python3-jinja2

    # Boost packages (also required)
    sudo apt-get install -y \
        libboost-chrono-dev \
        libboost-context-dev \
        libboost-coroutine-dev \
        libboost-date-time-dev \
        libboost-filesystem-dev \
        libboost-iostreams-dev \
        libboost-locale-dev \
        libboost-program-options-dev \
        libboost-serialization-dev \
        libboost-signals-dev \
        libboost-system-dev \
        libboost-test-dev \
        libboost-thread-dev

    # Optional packages (not required, but will make a nicer experience)
    sudo apt-get install -y \
        doxygen \
        libncurses5-dev \
        libreadline-dev \
        perl

    git clone https://github.com/steemit/steem
    cd steem
    git submodule update --init --recursive
    mkdir build
    cd build
    cmake -DCMAKE_BUILD_TYPE=Release ..
    make -j$(nproc) steemd
    make -j$(nproc) cli_wallet
    # optional
    make install  # defaults to /usr/local

## Building on Ubuntu 14.04

(It is strongly advised to use Ubuntu 16.04 LTS instead)

Here are the required packages:

    # Required packages
    sudo apt-get install -y \
        autoconf \
        cmake3 \
        g++ \
        git \
        libssl-dev \
        libtool \
        make \
        pkg-config \
        doxygen \
        libncurses5-dev \
        libreadline-dev \
        libbz2-dev \
        python-dev \
        perl \
        python3 \
        python3-jinja2

The Boost provided in the Ubuntu 14.04 package manager (Boost 1.55) is too old.
Steem requires Boost 1.58 (as in Ubuntu 16.04) and works with versions up to 1.60 (including).
So building Steem on Ubuntu 14.04 requires downloading and installing a more recent
version of Boost.

According to [this mailing list
post](http://boost.2283326.n4.nabble.com/1-58-1-bugfix-release-necessary-td4674686.html),
Boost 1.58 is not compatible with gcc 4.8 (the default C++ compiler for
Ubuntu 14.04) when compiling in C++11 mode (which Steem does).
So we will use Boost 1.60.

Here is how to build and install Boost 1.60 into your user's home directory
(make sure you install all the packages above first):

    export BOOST_ROOT=$HOME/opt/boost_1_60_0
    URL='http://sourceforge.net/projects/boost/files/boost/1.60.0/boost_1_60_0.tar.bz2/download'
    wget -c "$URL" -O boost_1_60_0.tar.bz2
    [ $( sha256sum boost_1_60_0.tar.bz2 | cut -d ' ' -f 1 ) == \
        "686affff989ac2488f79a97b9479efb9f2abae035b5ed4d8226de6857933fd3b" ] \
        || ( echo 'Corrupt download' ; exit 1 )
    tar xjf boost_1_60_0.tar.bz2
    cd boost_1_60_0
    ./bootstrap.sh "--prefix=$BOOST_ROOT"
    ./b2 install

Then the instructions are the same as for steem:

    git clone https://github.com/steemit/steem
    cd steem
    git submodule update --init --recursive
    mkdir build && cd build
    cmake -DCMAKE_BUILD_TYPE=Release ..
    make -j$(nproc) steemd
    make -j$(nproc) cli_wallet

## Building on macOS X

Install Xcode and its command line tools by following the instructions here:
https://guide.macports.org/#installing.xcode.  In OS X 10.11 (El Capitan)
and newer, you will be prompted to install developer tools when running a
developer command in the terminal.

Accept the Xcode license if you have not already:

    sudo xcodebuild -license accept

Install Homebrew by following the instructions here: http://brew.sh/

### Initialize Homebrew:

   brew doctor
   brew update

### Install steem dependencies:

    brew install \
        autoconf \
        automake \
        cmake \
        git \
        boost160 \
        libtool \
        openssl \
        python3 \
        python3-jinja2

Note: brew recently updated to boost 1.61.0, which is not yet supported by
steem. Until then, this will allow you to install boost 1.60.0.

*Optional.* To use TCMalloc in LevelDB:

    brew install google-perftools

*Optional.* To use cli_wallet and override macOS's default readline installation:

    brew install --force readline
    brew link --force readline

### Clone the Repository

    git clone https://github.com/steemit/steem.git
    cd steem

### Compile

    export OPENSSL_ROOT_DIR=$(brew --prefix)/Cellar/openssl/1.0.2h_1/
    export BOOST_ROOT=$(brew --prefix)/Cellar/boost@1.60/1.60.0/
    git submodule update --init --recursive
    mkdir build && cd build
    cmake -DBOOST_ROOT="$BOOST_ROOT" -DCMAKE_BUILD_TYPE=Release ..
    make -j$(sysctl -n hw.logicalcpu)

Also, some useful build targets for `make` are:

    steemd
    chain_test
    cli_wallet

e.g.:

    make -j$(sysctl -n hw.logicalcpu) steemd

This will only build `steemd`.

## Building on Other Platforms

- Windows build instructions do not yet exist.

- The developers normally compile with gcc and clang. These compilers should
  be well-supported.
- Community members occasionally attempt to compile the code with mingw,
  Intel and Microsoft compilers. These compilers may work, but the
  developers do not use them. Pull requests fixing warnings / errors from
  these compilers are accepted.


## Building on the Test Network

Follow the same build instructions as above but add

    -DBUILD_STEEM_TESTNET=true

to your `cmake` command.

e.g.:

    cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_STEEM_TESTNET=true ..

Which should output

    -- BUILD_STEEM_TESTNET: ON
    --
    --              CONFIGURING FOR TEST NET
    --

    ....

    -- Finished fc module configuration...
    --

                 CONFIGURED FOR TEST NETWORK

    -- Configuring done
    -- Generating done
    -- Build files have been written to: ...

## Configuring the Test Network

After successfully building, configure `steemd` for the Test Network.

    cd programs/steemd
    ./steemd -d testnet

Which should output

    ------------------------------------------------------

                STARTING TEST NETWORK

    ------------------------------------------------------
    initminer public key: TST6LLegbAgLAy28EHrffBVuANFWcFgmqRMW13wBmTExqFE9SCkg4
    initminer private key: 5JNHfZYKGaomSFvd4NUdQ9qMcEAC43kujbfjueTHpVapX1Kzq2n
    chain id: 18dcf0a285365fc58b71f18b3d3fec954aa0c141c44e4e5cb4cf777b9eab274e
    blockchain version: 0.19.2
    ------------------------------------------------------
    1723906ms th_a       main.cpp:138                  main                 ] Writing new config file at build/programs/steemd/testnet/config.ini
    1724097ms th_a       witness_plugin.cpp:505        plugin_startup       ] No witnesses configured! Please add witness names and private keys to configuration.

Press `CTRL + c` to stop the process

    1726622ms asio       main.cpp:187                  operator()           ] Caught SIGINT attempting to exit cleanly

Update `build/programs/steemd/testnet/config.ini` with:

    p2p-endpoint = 0.0.0.0:3333

Sets `steemd` to p2p on port 3333

    rpc-endpoint = 0.0.0.0:9876

Sets `steemd` to p2p on port 9876

    witness = "initminer"

    private-key = 5JNHfZYKGaomSFvd4NUdQ9qMcEAC43kujbfjueTHpVapX1Kzq2n

Sets initial witness to initminer for block mining

    [logger.default]
    level=debug

Optional but recommended for testing

After updating, relaunch `steemd` with

    ./steemd -d testnet --enable-stale-production

Which will output

    ------------------------------------------------------

                STARTING TEST NETWORK

    ------------------------------------------------------
    initminer public key: TST6LLegbAgLAy28EHrffBVuANFWcFgmqRMW13wBmTExqFE9SCkg4
    initminer private key: 5JNHfZYKGaomSFvd4NUdQ9qMcEAC43kujbfjueTHpVapX1Kzq2n
    chain id: 18dcf0a285365fc58b71f18b3d3fec954aa0c141c44e4e5cb4cf777b9eab274e
    blockchain version: 0.19.2
    ------------------------------------------------------
    2510392ms th_a       main.cpp:172                  main                 ] parsing options
    2510392ms th_a       main.cpp:174                  main                 ] initializing node
    2510392ms th_a       main.cpp:176                  main                 ] initializing plugins
    2510392ms th_a       application.cpp:1148          initialize_plugins   ] Initializing plugin account_by_key
    2510392ms th_a       account_by_key_plugin.cpp:256 plugin_initialize    ] Initializing account_by_key plugin
    2510392ms th_a       application.cpp:1148          initialize_plugins   ] Initializing plugin account_history
    2510392ms th_a       application.cpp:1148          initialize_plugins   ] Initializing plugin witness
    2510414ms th_a       main.cpp:179                  main                 ] starting node
    2510414ms th_a       application.cpp:259           startup              ] Backtrace on segfault is enabled
    2510414ms th_a       application.cpp:263           startup              ] shared_file_size is 57982058496 bytes
    2510415ms th_a       application.cpp:281           startup              ] Starting Steem node in write mode.
    2510416ms th_a       database.cpp:2578             show_free_memory     ] Free memory is now 53G
    2510416ms th_a       application.cpp:384           startup              ] API database_api enabled publicly
    2510416ms th_a       application.cpp:384           startup              ] API login_api enabled publicly
    2510416ms th_a       application.cpp:384           startup              ] API account_by_key_api enabled publicly
    2510417ms th_a       application.cpp:137           reset_p2p_node       ] Configured p2p node to listen on 0.0.0.0:3333
    2510417ms th_a       application.cpp:145           reset_p2p_node       ] head_block_id: 0000000000000000000000000000000000000000
    2510418ms th_a       application.cpp:187           reset_websocket_serv ] Configured websocket rpc to listen on 0.0.0.0:9876
    2510418ms th_a       main.cpp:181                  main                 ] starting plugins
    2510418ms th_a       account_history_plugin.cpp:253 plugin_startup       ] account_history plugin: plugin_startup() begin
    2510418ms th_a       account_history_plugin.cpp:255 plugin_startup       ] account_history plugin: plugin_startup() end
    2510418ms th_a       witness_plugin.cpp:487        plugin_startup       ] witness plugin:  plugin_startup() begin
    2510418ms th_a       witness_plugin.cpp:492        plugin_startup       ] Launching block production for 1 witnesses.
    2510418ms th_a       witness_plugin.cpp:493        plugin_startup       ] \_witnesses: ["initminer"]

    ********************************
    *                              *
    *   ------- NEW CHAIN ------   *
    *   -   Welcome to Steem!  -   *
    *   ------------------------   *
    *                              *
    ********************************

    2510422ms th_a       witness_plugin.cpp:507        plugin_startup       ] witness plugin:  plugin_startup() end
    2510422ms th_a       main.cpp:198                  operator()           ] Started witness node on a chain with 0 blocks.
    2511013ms th_a       database.cpp:3629             apply_hardfork       ] HARDFORK 1 at block 1
    2511013ms th_a       database.cpp:3629             apply_hardfork       ] HARDFORK 2 at block 1
    2511013ms th_a       database.cpp:3629             apply_hardfork       ] HARDFORK 3 at block 1
    2511013ms th_a       database.cpp:3629             apply_hardfork       ] HARDFORK 4 at block 1
    2511013ms th_a       database.cpp:3629             apply_hardfork       ] HARDFORK 5 at block 1
    2511013ms th_a       witness_plugin.cpp:566        block_production_loo ] Generated block #1 with timestamp 2018-02-19T00:41:51 at time 2018-02-19T00:41:51 by initminer
    2514003ms th_a       witness_plugin.cpp:566        block_production_loo ] Generated block #2 with timestamp 2018-02-19T00:41:54 at time 2018-02-19T00:41:54 by initminer


`steemd` will now be running with blocks being mined by the "initminer" account    

# Running a second witness node

Follow the same steps to create another node, but add

    seed-node = 10.10.10.10:3333

to `testnet/config.ini` changing the IP address to the address of your first witness node.

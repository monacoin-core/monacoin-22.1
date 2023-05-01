Contents
========
This document describes how to set up your own Signet network.

Launch Signet
=============

Create a signetchallenge for your own network
---------------------------------------------
Follow the steps below to get the Fujicoin legacy address and pubkey.

    wget https://github.com/monacoin-core/monacoin-22.1/releases/download/m22.1.0/monacoin-m22.1.0-x86_64-linux-gnu.tar.gz
    tar -xzvf monacoin-m22.1.0-x86_64-linux-gnu.tar.gz
    cd monacoin-m22.1.0
    mv bin/* .
    git clone https://github.com/monacoin-core/monacoin-22.1.git
    cp monacoin/contrib/signet/miner .
    cp -r  monacoin/test/functional/test_framework .

    ./monacoind -regtest -daemon
    ./monacoin-cli -regtest createwallet "test"
    ADDR=$(./monacoin-cli -regtest getnewaddress "" "legacy")
    echo $ADDR
    PRIVKEY=$(./monacoin-cli -regtest dumpprivkey $ADDR)
    echo $PRIVKEY
    ./monacoin-cli -regtest getaddressinfo $ADDR | grep pubkey
    # "pubkey": "THE_REAL_PUBKEY"
    ./monacoin-cli -regtest stop

Your signetchallenge is below.

    SIGNETCHALLENGE=5121 + THE_REAL_PUBKEY + 51ae
                   =512102f8........4f2a51ae

Mining environment settings
---------------------------
Set the completed signetchallenge and restart monacoind. Then import the private key above.

    ./monacoind -signet -daemon -signetchallenge=$SIGNETCHALLENGE
    CLI="./monacoin-cli -signet"
    $CLI createwallet "signet"
    $CLI importprivkey $PRIVKEY

Mining
======

To mine the first block in your custom chain, you can run:

    CLI="./monacoin-cli -signet"
    MINER="./miner"
    GRIND="./monacoin-util grind"
    ADDR=$(./monacoin-cli -signet getnewaddress "" "legacy")
    echo $ADDR
    NBITS=1f060000


To mining block number 1, execute the following command. 
This will mine a single block with a backdated timestamp designed to allow 100 blocks to be mined as quickly as possible, so that it is possible to do transactions.

    $MINER --cli="$CLI" generate --grind-cmd="$GRIND" --address="$ADDR" --nbits=$NBITS


Using the --ongoing parameter will then cause the signet miner to create blocks indefinitely. 

    $MINER --cli="$CLI" generate --grind-cmd="$GRIND" --address="$ADDR" --nbits=$NBITS --ongoing


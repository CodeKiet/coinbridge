## Bitcoin/PostgreSQL bridge

[![Build Status](https://travis-ci.org/tensorjack/CoinBridge.svg)](https://travis-ci.org/tensorjack/CoinBridge)

Bridge between Bitcoin and PostgreSQL.

Coinbridge connects the Bitcoin daemon (bitcoind) and a local PostgreSQL database.  Listens for transaction confirmations and automatically updates a transactions table in your database.

Includes a "payment" method which uses free, instant Bitcoin transfers between accounts in the same wallet, and standard Bitcoin transactions otherwise.  Also includes a comprehensive wrapper for bitcoind/bitcoin-cli JSON-RPC functionality.

Coinbridge has been tested with Bitcoin, but it should work for any altcoin that shares Bitcoin's RPC command suite (i.e., most of them).  To add a different coin, enter the new coin's information into `coinbridge/data/coins.json`.  For wallet listener functionality, you also need to create a `coinbridge/newcoin-listen` script with `newcoind` in place of `bitcoind`, and point the new coin's `walletnotify` to this script in newcoin's configuration file.

### Installation

    $ pip install -r requirements.txt
    $ python setup.py install

Depending on your system, compiling Bitcoin from scratch can be a headache.  On Ubuntu, you can simply install `bitcoind` from the bitcoin PPA:

    $ apt-get install python-software-properties
    $ add-apt-repository ppa:bitcoin/bitcoin
    $ apt-get update
    $ apt-get install bitcoind

A convenience script, `init.sh`, is included that will do some initial configuration for you.  I have only tested this on Ubuntu 12.04/14.04 so far.  The below steps are only necessary if `init.sh` does not work for you:

1. Set up a `pgpass` file so transaction confirmations can be autologged to Postgres (replace HOST, PORT, USER, DATABASE, PASSWORD with your own settings):
    
    $ touch ~/.pgpass
    $ echo HOST:PORT:USER:DATABASE:PASSWORD >> ~/.pgpass
    $ chmod 600 ~/.pgpass

2. Set environment variables:

    $ echo "export BRIDGE=/path/to/coinbridge" >> ~/.profile
    $ echo "export PGPASSFILE=$HOME/.pgpass" >> ~/.profile
    $ source ~/.profile

3. Finally, you need to point Bitcoin's `walletnotify` at `coinbridge/bitcoin-listen`:

    $ apt-get install jq
    $ echo "walletnotify=$BRIDGE/bitcoin-notify %s" >> ~/.bitcoin/bitcoin.conf

### Usage

    from coinbridge import Bridge
    bitcoin_bridge = Bridge()
    bitcoin_bridge.payment(from_account, to_account, amount)

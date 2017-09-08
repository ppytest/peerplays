Intro for new developers and witnesses
------------------------

This is a quick introduction to get new developers and witnesses up to speed on Peerplays blockchain. It is intended for witnesses plannig to join a live, already deployed blockchain.

Starting A Peerplays Node
-----------------

For Ubuntu 14.04 LTS and up users, see this link first:
    https://github.com/cryptonomex/graphene/wiki/build-ubuntu

and then proceed with:

    git clone https://github.com/pbsa/peerplays.git
    cd peerplays
    git submodule update --init --recursive
    cmake -DBOOST_ROOT="$BOOST_ROOT" -DCMAKE_BUILD_TYPE=Release .
    make
    ./programs/witness_node/witness_node
    
Launching the witness creates required directories. Next, **stop the witness** and continue.

    vi witness_node_data_dir/config.ini
    p2p-endpoint = 0.0.0.0:9777
    rpc-endpoint = 127.0.0.1:8090
    seed-node = 213.184.225.234:59500
    
Start the witness back up

    ./programs/witness_node/witness_node

Then, in a separate terminal window, start the command-line wallet `cli_wallet`:

    ./programs/cli_wallet/cli_wallet

To set your initial password to 'password' use:

    >>> set_password password
    >>> unlock password


A list of CLI wallet commands is available
[here](https://github.com/PBSA/peerplays/blob/master/libraries/wallet/include/graphene/wallet/wallet.hpp).


Public Testnet
----------------------
- chain-id - be6b79295e728406cbb7494bcb626e62ad278fa4018699cf8f75739f4c1a81fd

Register your username at the faucet address
---------------------------
Pending ...


If starting the blockchain from scratch
----------------------
1. Start up the node without any parameters.

```
witness_node
```

This should start the node and create the witness_node_data_dir directory and a default configuration file.

2. Kill/stop the node.  The clean way to kill is to find the process ID of the witness_node process, and then send a kill signal for it.

3. Modify the node's configuration file (witness_node_data_dir/config.ini) to "start" the empty chain by operating all of the initial witnesses. The following settings should be specified.

# Peer-to-peer (P2P) endpoint
# Public P2P endpoint
p2p-endpoint = 0.0.0.0:6666

# API Server
# Private local API Server example:  rpc-endpoint = 127.0.0.1:8090
# or
# Public API Server with a TLS endpoint example:  rpc-tls-endpoint = 1.2.3.5:5678
rpc-endpoint = 127.0.0.1:8090

# Operate all of the initial witnesses that defined the genesis file
witness-id = "1.6.1"
witness-id = "1.6.2"
witness-id = "1.6.3"
witness-id = "1.6.4"
witness-id = "1.6.5"
witness-id = "1.6.6"
witness-id = "1.6.7"
witness-id = "1.6.8"
witness-id = "1.6.9"
witness-id = "1.6.10"
witness-id = "1.6.11"

# Enable production of blocks because no other blocks yet exist
enable-stale-production = true


4. Re-start up the node without any parameters.

```
witness_node
```

You should see

********************************
*                              *
*   ------- NEW CHAIN ------   *
*   - Welcome to Graphene! -   *
*   ------------------------   *
*                              *
********************************

and you should see the node producing blocks.

```
Generated block #1 with timestamp ...
```

5. Import the private key for the "bts-adam" account into the wallet (which coincidentally is also the private key for the proper "adam" account)
```
import_key adam 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3
```

6. Transfer some funds from the "bts-adam" account to the "adam" account (which is a "proper" account) that can later create other accounts such as witness accounts
```
transfer bts-adam adam 5000 PPYTEST "" true
```


If joining a blockchain that is already running and before your witness account is configured. ("Trusting" Testnet approach)
----------------------
1. Start up the node with a local API server that is listening on port 8090
```
witness_node --rpc-endpoint 127.0.0.1:8090
```

2. There is an account on the testnet called "testnet-adam" that initially has funds which can be used to create and register new accounts on the blockchain. If operating on a "trusting" testnet, that account will still have funds when you join.  In this scenario, you can use the funds to register your own witness account by using the CLI. Start by running the CLI on the same machine that is running the witness_node.

```
cli_wallet
```

3. Set the password the CLI wallet
```
set_password WalletPasswordHere
unlock WalletPasswordHere
```

4. Import the private key for the "adam" account.
```
import_key adam 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3
```

5. Create a witness account with suggest_brain_key, create_account_with_brain_key, upgrade_account, create_witness. You will probably also want to vote_for_the_witness. More details farther below.



If joining a blockchain that is already running and before your witness account is configured. (Faucet Testnet approach)
----------------------
Pending ...





Use the get_private_key_from_password command
---------------------------------
You will to generate owner and active keys

```
get_private_key_from_password your_witness_username active the_key_you_received_from_the_faucet
```
This will reveal an array for your active key `["PPYxxx", "xxxx"]`

import_keys into your cli_wallet
-------------------------------
- use the second value in the array returned from the previous step for the private key
- be sure to wrap your username in quotes
- import the key with this command
```
import_key "your_witness_username" xxxx
```

Upgrade your account to lifetime membership
--------------------------------
```
upgrade_account your_witness_username true
```

Create your witness (substitute the url for your witness information)
-------------------------------
- place quotes around url
```
create_witness your_witness_username "url" true
```
**Be sure to take note of the block_signing_key** 

IMPORTANT (issue below command using block_signing_key just obtained)
```
get_private_key block_signing_key
```
Compare this result to

```
dump_private_keys
```
You should see 3 pairs of keys. One of the pairs should match your block_signing_key and this is the one you will use in the next step!

Get your witness id
-----------------
```
get_witness username (note the "id" for your config)
```

Modify your witness_node config.ini to include **your** witness id and private key pair.
-------------------------
Comment out the existing private-key before adding yours
```
vim witness_node_data_dir/config.ini

witness-id = "1.6.x"
private-key = ["block_signing_key","private_key_for_your_block_signing_key"]
```

start your witness back up
------------------
```
./programs/witness_node/witness_node
```

If it fails to start, try with these flags (not for permanent use)

```
./programs/witness_node/witness_node --resync --replay
```

Vote for yourself
--------------
```
vote_for_witness your_witness_account your_witness_account true true
```

Ask to be voted in!
--------------

Join @Peerplays Telegram group to find information about the witness group.
http://t.me/@peerplayswitness

You will get logs that look like this:

```
2070264ms th_a       application.cpp:506           handle_block         ] Got block: #87913 time: 2017-05-27T16:34:30 latency: 264 ms from: bhuz-witness  irreversible: 87903 (-10)
2071000ms th_a       witness.cpp:204               block_production_loo ] Not producing block because slot has not yet arrived
2072000ms th_a       witness.cpp:204               block_production_loo ] Not producing block because slot has not yet arrived
2073000ms th_a       witness.cpp:201               block_production_loo ] Not producing block because it isn't my turn
```

Assuming you've received votes, you will start producing as a witness at the next maintenance interval (once per hour). You can check your votes with.

```
get_witness your_witness_account
```

systemd
----------------
It's important for your witness to start when your system boots up. The filepaths here assume that you installed your witness into `/home/ubuntu/peerplays`

Create a logfile to hold your stdout/err logging
```bash
sudo touch /var/log/peerplays.log
```

Save this file in your peerplays directory. `vi /home/ubuntu/peerplays/start.sh`
```bash
#!/bin/bash

cd /home/ubuntu/peerplays
./programs/witness_node/witness_node &> /var/log/peerplays.log
```
Make it executable
```bash
chmod 744 /home/ubuntu/peerplays/start.sh
```
Create this file: `sudo vi /etc/systemd/system/peerplays.service`
Note the path for start.sh. Change it to match where your start.sh file is if necessary.
```
[Unit]
Description=Peerplays Witness
After=network.target

[Service]
ExecStart=/home/ubuntu/peerplays/start.sh

[Install]
WantedBy = multi-user.target
```
Enable the service
```bash
sudo systemctl enable peerplays.service
```
Make sure you don't get any errors
```bash
sudo systemctl status peerplays.service
```
Stop your witness if it is currently running from previous steps, then start it with the service.
```bash
sudo systemctl start peerplays.service
```
Check your logfile for entries
```bash
tail -f /var/log/peerplays.log
```


Running specific tests
----------------------

- `tests/chain_tests -t block_tests/name_of_test`


 

# fairchains-documentation
In this repository some helpful informations how to use FairChains ( https://github.com/FairChains/fairchains ) will stored.



### Build FairChains (full node)

#### Build on linux

###### Errors
https://stackoverflow.com/questions/30124264/undefined-reference-to-googleprotobufinternalempty-string-abicxx11#30175210

###### Prepare
~~~
sudo add-apt-repository ppa:bitcoin/bitcoin
sudo apt update
sudo apt install automake libtool build-essential libtool autotools-dev autoconf pkg-config libssl-dev libboost-all-dev libqt5gui5 libqt5core5a libqt5dbus5 qttools5-dev qttools5-dev-tools libprotobuf-dev protobuf-compiler libqrencode-dev autoconf openssl libssl-dev libevent-dev libminiupnpc-dev libzmq3-dev qtchooser libdb4.8-dev libdb4.8++-dev
~~~

###### Build (Full Node client)
~~~
./autogen.sh
./configure --disable-tests --disable-bench
make
make install
~~~

all informations you can find it here
https://github.com/FairChains/fairchains/blob/master/doc/build-unix.md

###### Build (with CVN)
~~~
./autogen.sh
./configure --with-cvn --disable-tests --disable-bench
make
make install
~~~
all informations you can find it here
https://github.com/FairChains/fairchains/blob/master/doc/build-CVN.md


### Establish CVN network

This instructions uses .PEM key/cert files.

If you want use fasito hardware device then the procure will not work.

#### Create/Setup --> `/.fairchains/fairchains.conf`

~~~
netname=<mychainname>
txindex=1

# is a CVN, so define as a generator
gen=1

# secure with file or fasito (= hardware device to generate keys)
cvn=file

cvnkeyfile=0xc0ff0001.pem
cvncertfile=0xc0ff0001.pem
~~~

#### Start the first (genesis) CVN and establish the blockchain
( without connection to other peers )
~~~
# deamon ( without gui )
./fairchainsd -deamon -cvnwaitforpeers=0 -printtoconsole -connect=0 -maxtipage=9999999

# qt ( with gui )
./fairchains-qt -cvnwaitforpeers=0 -printtoconsole -connect=0 -maxtipage=9999999
~~~

#### Add your second CVN to the network

###### Create new CVN key / cert file
You can use the fairchains-tool and create another .pem key cert file.

###### Get the pubkey in hex
To get the hex formated public key you can export the base64 pubkey.
~~~
openssl x509 -in "0xc0ff0002.pem" -pubkey -noout
~~~

###### Convert the base64 to hex
https://base64.guru/converter/decode/hex

get the last 128 letters ( 64 bytes )

you can open a developer console in browser and run a js command.
~~~
"<hex>".slice(-128)
~~~
or by python on commandline

~~~
python
>>>"<hex>"[-128:]
~~~

###### Admin adds the new CVN
Notice: All commands need to be done between a block! It is recommend to wait for the block creation ( see it in the terminal logs of the daemon ) and start immediately with the procedure.
~~~
./fairchains-cli fasitologin file <password> \"0xadff0001.pem\"

./fairchains-cli fasitononce

./fairchains-cli addcvn c 0xc0ff0002 04<hexpubkey>
# you will get the hash for the fasitosign

./fairchains-cli fasitosign <hash>

# repeat the step addcvn and the cvn will added to blockchain (next block)
./fairchains-cli addcvn c 0xc0ff0002 04<hexpubkey>
~~~

###### Create a DNS seed node
When you start your second CVN then the CVN will not found your first CVN because the IP or domain is unknown.

For this case you need to create DNS records with an url assigned to the ip of the server where your first CVN is running.

Here are some helpful commandline commands
~~~
# get your IP address
curl ifconfig.me

# check the DNS entries of an url
dig faircoin2-seed1.fair-coin.org
~~~

Important: the full nodes should be available 7/24h


###### Restart your first CVN and start your second CVN

Your first CVN is still running without connections  `-connect=0` That means your second CVN can't connect to your first CVN.

So start both CVNs again and allow connections.
~~~
# deamon ( without gui )
./fairchainsd -deamon -cvnwaitforpeers=0 -printtoconsole -maxtipage=9999999

# qt ( with gui )
./fairchains-qt -cvnwaitforpeers=0 -printtoconsole -maxtipage=9999999
~~~

#### Add your third and more CVNs to the network

###### Admin adds the new CVN
Notice: All commands need to be done between a block! It is recommend to wait for the block creation ( see it in the terminal logs of the daemon ) and start immediately with the procedure.
~~~
./fairchains-cli fasitologin file <password> \"0xadff0001.pem\"

./fairchains-cli fasitononce

./fairchains-cli addcvn c 0xc0ff0002 04<hexpubkey>
# you will get the hash for the fasitosign

./fairchains-cli fasitosign <hash>

# repeat the step addcvn and the cvn will added to blockchain (next block)
./fairchains-cli addcvn c 0xc0ff0002 04<hexpubkey>
~~~

###### Start your new CVN

~~~
# deamon ( without gui )
./fairchainsd -deamon -cvnwaitforpeers=0 -printtoconsole -maxtipage=9999999

# qt ( with gui )
./fairchains-qt -cvnwaitforpeers=0 -printtoconsole -maxtipage=9999999
~~~

### Create New Coins

###### Admin creates new coins
Notice: All commands need to be done between a block! It is recommend to wait for the block creation ( see it in the terminal logs of the daemon ) and start immediately with the procedure.

~~~
./fairchains-cli fasitologin file <password> \"0xadff0001.pem\"

./fairchains-cli fasitononce

./fairchains-cli addcoinsupply <coinaddress> 1000000 false "initial coin supply for testchain blockchain test"
# you will get the hash for the fasitosign

./fairchains-cli fasitosign <hash>

# repeat the step addcvn and the cvn will added to blockchain (next block)
./fairchains-cli addcoinsupply <coinaddress> 1000000 false "initial coin supply for testchain blockchain test"
~~~

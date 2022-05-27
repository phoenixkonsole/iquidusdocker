Instructions  
```
git clone https://github.com/phoenixkonsole/iquidusdocker explorer
cd explorer
```
To swap out logo to your coin, change logo.png in explorer folder.  
my sample docker-compose.yml

```
version: "3"
services:
  volta:
    image: buzzkillb/voltad:latest
    volumes:
      - ~/.volta:/data
    ports:
      - 14143:14143
      - 13143:13143
  explorer:
    build: ./explorer
    stdin_open: true
    tty: true
    ports:
      - 3001:3001
    links:
      - mongodb
      - volta
    depends_on:
      - mongodb
      - volta
    command: /bin/bash -c "service cron start && cd /opt/iquidus && npm start"
  mongodb:
    image: mongo:latest
    container_name: "mongodb"
    environment:
      - MONGO_DATA_DIR=/data/db
      - MONGO_LOG_DIR=/dev/null
    volumes:
      - ./data/db:/data/db
    ports:
      - 27017:27017
    command: mongod --smallfiles --bind_ip 0.0.0.0 --logpath=/dev/null --quiet
```

sample .conf  
```
port=14143
rpcport=13143
server=1
rpcuser=RPCUSERNAME
rpcpassword=PASSWORDCHANGEME
txindex=1
listen=1
rpcallowip=172.19.0.0/0
```

when changing settings in explorer/settings.json
```
docker-compose build
```

start up  
```
docker-compose up
```
info for Denarius  
```
blockhash: 000000000661c86bf89e652eb142093bd9123006f3085595dee183bfe1c24570
txhash: 776684cfd496890268805a1d86d3fd00f4097c1dbffecc3acd79101c15216594
address: DABQj7yoQuvfxVVWMf2mqWhoHLDMUqUJsc

genesis_tx: c6d8e8f56c25cac33567e571a3497bfc97f715140fcfe16d971333b38e4ee0f2
genesis_block: 00000d5dbbda01621cfc16bbc1f9bf3264d641a5dbf0de89fd0182c2c4828fcd
```
FTC-in-a-box is a simple docker recipe that builds FTC from the paparab source, builds the iquidius explorer and attaches them together. So that you can have your own little "test" network right on your own computer. It's super lean, and you can generate many blocks pretty quickly.

If you have never setup or run docker, then this is your chance to spin it up and use it to develop something awesome with papara!

## Instructions

1. Install Docker Community for your platform of choice - [Here](https://www.docker.com/community-edition#/download)
2. Git clone this repository into a folder and navigate into it
```
git clone https://github.com/phoenixkonsole/iquidusdocker explorer
cd explorer
```
3. Use the docker-compose command to build and turn on these containers
```
docker-compose up
```
This will now build the ftc-in-a-box as well as the explorer containers and start them.

Things to note:
- When you boot via docker-compose that terminal will be hung with the log output of these containers
- A "data" folder will be created to persist the mongodb database in this folder. You are safe to delete this when you want to start completely fresh from the db.

## Generate blocks on the local regtest network.

Open a new terminal window.
```
docker ps
```
This will list all the docker containers running on your system. Find the one that houses ftcregtest.  In order to get into the container that is running paparad, you run the following:
```
docker exec -it CONTAINERID /bin/bash
```
the CONTAINERID will look like a short hash. This will launch you into the container in this terminal window. There is a helper "make" to help you generate coins and work in this small environment.

To generate coins, simply type in the /data directory
```
make generate
```
You will see it will make a new block, and the associated coins. If you type it again you will get another block made. If you want more than a few blocks type
```
make generate BLOCKS=105
```
This will generate 105 blocks. While this is good, as you generate more, the difficulty will rise, and with that, it will get to the point where each block takes 1 minute to find about. Which means if you type BLOCKS=1000, then after the first few hundred, it will sgtart getting slower and slower and take a long time to generate blocks. Generally I start with over 100, so I have some coin to play with and then don't need many more than that. You can look in the Makefile for other command you can run. It's just a simple helper to make dealing with multiple daemons of the coin.

```
make getinfo
```
Will help you see the two paparad daemons and how many blocks they each see and so forth.

## Explorer

To see the explorer, simply goto "http://localhost:3001" - With docker, you are running these containers on your local computer, self contained, with certain ports exposed and with you having access to see them.

Right now, you have to get into the container, navigate to /opt/iquidius and run "node scripts/sync.js index reindex" to update the database with what is in the blockchain.

## Testing other scenarios

[Docker Cheat sheet](https://www.digitalocean.com/community/tutorials/how-to-remove-docker-images-containers-and-volumes)
If you want to start completely over and wipe out everything in docker, you can just run 
```
docker rmi $(docker images -f dangling=true -q)
```

After this, you should be all the way cleaned up. You can open and edit the "Dockerfile" inside the ftc-in-a-box and change the branch and build options and such. Once you do that, you can start the build process over walking through the steps above.

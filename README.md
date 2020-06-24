# Install

```
#!/bin/bash

export PROCESS_COUNT=$(grep -c "processor" /proc/cpuinfo)

sudo apt-get update
sudo apt-get install -y curl wget git build-essential libreadline-dev libsqlite3-dev libevent-dev mono-complete nodejs npm redis-server p7zip-full liblua5.3-dev
sudo npm install pm2 npm n -g
sudo n 12

git clone https://github.com/moecube/ygopro --branch=server --recursive
cd ygopro/
git submodule foreach git checkout master
wget -O - https://github.com/premake/premake-core/releases/download/v5.0.0-alpha14/premake-5.0.0-alpha14-linux.tar.gz | tar zfx -
./premake5 gmake
cd build/
make config=release -j$PROCESS_COUNT
cd ..
ln -s bin/release/ygopro ./
strip ygopro
cd ..

git clone https://github.com/moecube/windbot
cd windbot
xbuild /property:Configuration=Release /property:TargetFrameworkVersion="v4.5"
ln -s bin/Release/WindBot.exe .
ln -s ../ygopro/cards.cdb .
cd ..

git clone https://github.com/moecube/srvpro ygopro-server
cd ygopro-server
npm ci
ln -s ../ygopro .
ln -s ../windbot .
mkdir config
cp data/default_config.json config/config.json
cd ..
```

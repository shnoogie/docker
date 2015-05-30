FROM ubuntu:14.04

USER root
RUN apt-get update && \
  apt-get install -y --no-install-recommends \
    git \
    ca-certificates \
    python2.7 && \
  groupadd -r nobody

ENV PYTHON="/usr/bin/python2.7"
WORKDIR /home
RUN git clone https://github.com/aol/moloch.git && \
  cd moloch && \
  ./easybutton-build.sh && \
  curl -sL https://deb.nodesource.com/setup | bash && \
  sudo apt-get install -y nodejs && \
  cd viewer && \
  npm update

WORKDIR /home/moloch
RUN  mkdir config && \
  cd config && \
  cp ../config.ini . && \
  wget http://geolite.maxmind.com/download/geoip/database/GeoLiteCountry/GeoIP.dat.gz && \
  gzip -d GeoIP.dat.gz && \
  wget http://www.maxmind.com/download/geoip/database/asnum/GeoIPASNum.dat.gz && \
  gzip -d GeoIPASNum.dat.gz && \
  wget https://www.iana.org/assignments/ipv4-address-space/ipv4-address-space.csv && \
  openssl req -new -newkey rsa:2048 -nodes -keyout moloch.key -out moloch.csr -subj '/CN=moloch.test/O=Moloch/C=US' && \
  openssl x509 -in moloch.csr -out moloch.cert -req -signkey moloch.key -days 3650 && \
  sed -i 's/GeoIP\.dat/\/home\/moloch\/config\/GeoIP\.dat/' config.ini && \
  sed -i 's/GeoIPASNum\.dat/\/home\/moloch\/config\/GeoIPASNum\.dat/' config.ini && \
  sed -i 's/ipv4-address-space\.csv/\/home\/moloch\/config\/ipv4-address-space\.csv/' config.ini && \
  sed -i 's/\/moloch\/pcap/\/home\/moloch\/pcap/' config.ini && \
  sed -i 's/eth1/eth0/' config.ini && \
  sed -i 's/CHANGEME_ESHOSTNAME/192\.168\.59\.103/' config.ini && \
  sed -i 's/CHANGEME/dockermoloch/' config.ini && \
  sed -i 's/moloch\.cert/\/home\/moloch\/config\/moloch.cert/' config.ini && \
  sed -i 's/moloch\.key/\/home\/moloch\/config\/moloch.key/' config.ini && \  
  echo INIT | ../db/db.pl 192.168.59.103:9200 init && \
  cd ../viewer && \
  nodejs addUser.js default "default user" password -c ../config/config.ini && \
  mkdir /home/moloch/pcap && \
  chown -R nobody:nobody /home/moloch/pcap

WORKDIR /home/moloch
CMD cd viewer && \
  nodejs viewer.js -c ../config/config.ini 2>/dev/null & \
  cd capture && \
  ./moloch-capture -c ../config/config.ini 2>/dev/null

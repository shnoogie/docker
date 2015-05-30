This Dockerfile builds a preconfigured Moloch (https://github.com/aol/moloch) that is fully customizable.

To get started, you'll first need Elasticsearch. The simplest way is building through docker.

```
sudo docker pull elasticsearch
```

Ensure Elasticsearch is running and accessible. For more advanced configuration options for Elasticsearch see https://registry.hub.docker.com/u/library/elasticsearch/

```
sudo docker run -d -p 9200:9200 elasticsearch
```

The dockerfile will need two edits before running. Change 192\.168\.59\.103 on line 37 and 41 to the IP of your Elasticsearch. Someday I'll put that into a variable. Actually I'll have a separate init script for elasticsearch, this works for now.

We're ready to build moloch, it will take about 10 minutes.

```
sudo docker build -t "moloch" moloch/
```

Once built moloch is ready to play.

```
sudo docker run -d -p 8005:8005 moloch
```

This dockerfile is capable of importing custom configurations. The moloch container is configured to look for config.ini at /home/moloch/config/ ensure it is imported there. Everything else is configurable from config.ini but for convience sake I recommend keeping all files needing persisntance there. You'll also need to mount the directory you'll be storing pcaps.

```
sudo docker run -d -p 8005:8005 -h molochcaptuer -v /moloch/pcap:/home/moloch/pcap -v /moloch/config:/home/moloch/config moloch
```

Configuring interfaces for moloch can potently be very frustrating. By default a moloch container is assigned a single interface that is essentially NATed through a docker bridge. This is quite limited if you're trying to capture network traffic. There are a few third party scripts that assist in advanced network (Notably https://github.com/jpetazzo/pipework), however they too are somewhat limiting when trying to listen on a specific interface.

The simplest, and my favorite, solution is the --net=host flag. This makes the docker container use the host's networking. Meaning the moloch container will bind directly to the host's interface and the docker container will share the hostname, resolv, etc. An ifconfig in the container would be the same as the host. This method makes capturing/hosting a lot easier.

```
sudo docker run -d --net=host -v /moloch/pcap:/home/moloch/pcap -v /moloch/config:/home/moloch/config moloch
```
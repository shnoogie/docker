This Dockerfile builds a preconfigured Moloch (https://github.com/aol/moloch) that is fully customizable.

To get started, you'll first need Elasticsearch. The simplest way is building through docker.

```
sudo docker pull elasticsearch
```

Ensure Elasticsearch is running and accessable.

```
sudo docker run -p 9200:9200 elasticsearch
```

The dockerfile will need two edits before running. Change 192\.168\.59\.103 on line 37 and 41 to the IP of your Elasticsearch. Someday I'll put that into a variable. Actually I'll have a seperate init script for elasticsearch, this works for now.

We're ready to build moloch, it will take about 10 minutes.

```
sudo docker build -t "moloch" moloch/
```

Once built moloch is ready to play.

```
sudo docker run -p 8005:8005 moloch
```
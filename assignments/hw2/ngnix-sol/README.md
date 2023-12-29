Building Docker image

```
docker build . -t nginx-rtmp
```

Running Docker container

```
docker run -d -p 1935:1935 -p 80:80 --name nginx-rtmp nginx-rtmp  
```

Streaming video file

```
wget https://download.blender.org/demo/movies/BBB/bbb_sunflower_1080p_60fps_normal.mp4.zip
```

```
unzip bbb_sunflower_1080p_60fps_normal.mp4.zip
```

```
sudo apt install -y ffmpeg
```

Low resolution video streaming

```
ffmpeg -re -i BigBuckBunny_320x180.mp4 -vcodec libx264 -vprofile baseline -acodec aac -strict -2 -f flv rtmp:/$(hostname)/live/bbb
```

High resolution video streaming

```
ffmpeg -re -i bbb_sunflower_1080p_60fps_normal.mp4 -vcodec libx264 -vprofile baseline -acodec aac -strict -2 -f flv rtmp:/$(hostname)/live/bbb
```

Terminating Docker container

```
docker kill $(docker ps -aq); docker rm $(docker ps -aq)
```

References

Docker images that serve as the basis for this assignment:

GitHub repo: https://github.com/tiangolo/nginx-rtmp-docker

Docker Hub image: https://hub.docker.com/r/tiangolo/nginx-rtmp/
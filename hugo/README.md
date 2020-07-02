# Overview
This image contains Hugo on a minimal scratch base image.

## Source
[View the Docker file for this image][dockerfile].

## Usage
The following example shows how to display the Hugo version:

```
docker run --rm -it tonymackay/hugo version
```

Assuming you are located in the root directory of a Hugo site, the following command will build your site:

```
docker run --rm -it -v $(pwd):/site tonymackay/hugo --environment=production --minify --cleanDestinationDir=true
```

The following command will run the built in Hugo server and allow you to access it from the host:

```
docker run --rm -it -v $(pwd):/site -p 1313:1313/tcp tonymackay/hugo server -D --watch --bind 0.0.0.0
```

[dockerfile]: Dockerfile

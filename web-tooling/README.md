# Overview
This image uses [Debian:buster-slim][buster] for its base and contains the following programs:

- [cf-purge][cf-purge] v1.0.0-beta1
- [hugo][hugo] v0.74.3
- [AWS CLI][awscli] v2.0.27
- [Node.js][nodejs] v12.18.2

It's useful for building Hugo websites, deploying them to S3 and purging modified URLs from Cloudflare's edge cache.

## Source
[View the Docker file for this image][dockerfile].

## Usage
The following example shows how to display the Hugo version:

```
docker run --rm -it tonymackay/web-tooling hugo version
```

Assuming you are located in the root directory of a Hugo site, the following command will build your site:

```
docker run --rm -it -v $(pwd):/site tonymackay/web-tooling hugo --environment=production --cleanDestinationDir=true
```

Here's an example of running a site which can be accessed via `http://localhost:1313` on the machine running Docker:

```
docker run --rm -it -v $(pwd):/site -p 1313:1313 tonymackay/web-tooling hugo server -D --watch --bind 0.0.0.0
```

The AWS CLI and NodeJS can be used in the same way as it is in the official images.

[buster]: https://hub.docker.com/_/debian?tab=tags&page=1&name=buster-slim
[dockerfile]: Dockerfile
[cf-purge]: https://github.com/tonymackay/cf-purge
[hugo]: https://github.com/gohugoio/hugo
[awscli]: https://github.com/aws/aws-cli
[nodejs]: https://github.com/nodejs/node

## License
MIT. See `LICENSE` for more details.
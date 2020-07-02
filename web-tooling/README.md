# Overview
This image uses [Debian:buster-slim][buster] for its base and contains the following programs:

- [s3-sync][s3-sync] v1.0.0-beta2
- [cf-purge][cf-purge] v1.0.0-beta1
- [hugo][hugo] v0.73.0
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

Syncing a site to S3 can be done by assigning the AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY environment variables and running the `s3-sync` command. The following example will sync the contents of `www` to a bucket named `example`:

```
docker run --rm -it \
-e AWS_ACCESS_KEY_ID="$AWS_ACCESS_KEY_ID" \
-e AWS_SECRET_ACCESS_KEY="$AWS_SECRET_ACCESS_KEY" \
-p 1313:1313 \
-v $(pwd):/site tonymackay/web-tooling s3-sync -local-dir www -bucket s3://example -base-url https://example.com
```

Note: `-base-url` is used to output a list of URLS that have been modified to a file. This list can be used by `cf-purge` to delete them from Cloudflare's edge cache as shown in the example below:

```
docker run --rm -it \
-e CF_API_TOKEN="$CF_API_TOKEN" \
-e CF_ZONE_ID="$CF_ZONE_ID" \
-p 1313:1313 \
-v $(pwd):/site tonymackay/web-tooling cf-purge -file urls.txt
```

The AWS CLI and NodeJS can be used in the same way as it is in the official images.

[buster]: https://hub.docker.com/_/debian?tab=tags&page=1&name=buster-slim
[dockerfile]: https://github.com/tonymackay/images/web-tooling/Dockerfile
[s3-sync]: https://github.com/tonymackay/s3-sync
[cf-purge]: https://github.com/tonymackay/cf-purge
[hugo]: https://github.com/gohugoio/hugo
[awscli]: https://github.com/aws/aws-cli
[nodejs]: https://github.com/nodejs/node

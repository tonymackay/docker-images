# Overview
This image contains [Guetzli JPEG compressor][guetzli] on a minimal scratch base image.

## Source
[View the Docker file for this image][dockerfile].

## Using
The following example shows how to run the guetzli:

```
docker run --rm -it tonymackay/guetzli
```

Output:
```
Guetzli JPEG compressor. Usage: 
guetzli [flags] input_filename output_filename

Flags:
  --verbose    - Print a verbose trace of all attempts to standard output.
  --quality Q  - Visual quality to aim for, expressed as a JPEG quality value.
  --memlimit M - Memory limit in MB. Guetzli will fail if unable to stay under
                 the limit. Default is 6000 MB
  --nomemlimit - Do not limit memory usage.
```

The following example shows how to compress an image located on the host where the container is running.

```
docker run --rm -it -v $(pwd):/data \
tonymackay/guetzli --quality 84 test-image.jpg test-image-compressed.jpg
```

[guetzli]: https://github.com/google/guetzli
[dockerfile]: Dockerfile
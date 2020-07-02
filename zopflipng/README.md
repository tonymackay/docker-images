# Overview
This image contains [Zopfli PNG compressor][zopfli] on a minimal scratch base image.

## Source
[View the Docker file for this image][dockerfile].

## Using
The following example shows how to run the zopflipng:

```
docker run --rm -it tonymackay/zopflipng
```

Output:
```
opfliPNG, a Portable Network Graphics (PNG) image optimizer.

Usage: zopflipng [options]... infile.png outfile.png
       zopflipng [options]... --prefix=[fileprefix] [files.png]...

If the output file exists, it is considered a result from a previous run and not overwritten if its filesize is smaller.

Options:
-m: compress more: use more iterations (depending on file size)
--prefix=[fileprefix]: Adds a prefix to output filenames. May also contain a directory path. When using a prefix, multiple input files can be given and the output filenames are generated with the prefix
 If --prefix is specified without value, 'zopfli_' is used.
 If input file names contain the prefix, they are not processed but considered as output from previous runs. This is handy when using *.png wildcard expansion with multiple runs.
-y: do not ask about overwriting files.
--lossy_transparent: remove colors behind alpha channel 0. No visual difference, removes hidden information.
--lossy_8bit: convert 16-bit per channel image to 8-bit per channel.
-d: dry run: don't save any files, just see the console output (e.g. for benchmarking)
--always_zopflify: always output the image encoded by Zopfli, even if it's bigger than the original, for benchmarking the algorithm. Not good for real optimization.
-q: use quick, but not very good, compression (e.g. for only trying the PNG filter and color types)
--iterations=[number]: number of iterations, more iterations makes it slower but provides slightly better compression. Default: 15 for small files, 5 for large files.
--splitting=[0-3]: ignored, left for backwards compatibility
--filters=[types]: filter strategies to try:
 0-4: give all scanlines PNG filter type 0-4
 m: minimum sum
 e: entropy
 p: predefined (keep from input, this likely overlaps another strategy)
 b: brute force (experimental)
 By default, if this argument is not given, one that is most likely the best for this image is chosen by trying faster compression with each type.
 If this argument is used, all given filter types are tried with slow compression and the best result retained. A good set of filters to try is --filters=0me.
--keepchunks=nAME,nAME,...: keep metadata chunks with these names that would normally be removed, e.g. tEXt,zTXt,iTXt,gAMA, ... 
 Due to adding extra data, this increases the result size. Keeping bKGD or sBIT chunks may cause additional worse compression due to forcing a certain color type, it is advised to not keep these for web images because web browsers do not use these chunks. By default ZopfliPNG only keeps (and losslessly modifies) the following chunks because they are essential: IHDR, PLTE, tRNS, IDAT and IEND.

Usage examples:
Optimize a file and overwrite if smaller: zopflipng infile.png outfile.png
Compress more: zopflipng -m infile.png outfile.png
Optimize multiple files: zopflipng --prefix a.png b.png c.png
Compress really good and trying all filter strategies: zopflipng --iterations=500 --filters=01234mepb --lossy_8bit --lossy_transparent infile.png outfile.png
```

The following example shows how to compress an image located on the host where the container is running.

```
docker run --rm -it -v $(pwd):/data \
tonymackay/zopflipng -m -y test-image.jpg test-image-compressed.jpg
```


[zopfli]: https://github.com/google/zopfli
[dockerfile]: Dockerfile

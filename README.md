# Philip Prindeville's personal OpenWrt feed

## Description

I've collected some packages for customizing my builds for images that I then use for testing, dogfooding, prototyping, etc. which I figure will never go upstream.

## Usage

This repo, if it's useful, is easy enough to add:

```
echo 'src-git personal git@github.com:pprindeville/owrt-personal.git' >> feeds.conf
./scripts/feeds update personal
./scripts/feeds install -a -p personal
```

and then run `make menuconfig` to select individual packages.

## License

None

## Disclaimer

No warranty is implied as to the fitness of these packages.


# xiph-static-build
Repository that creates statically linked libraries for ogg, vorbis, opus and flac

## Build
First you need to configure the project:
```sh
cmake -B build
```

Then you can build it:
```sh
cmake --build build --config Release
```

The generated static libraries can be found in `build/lib`.

Libraries built on Windows:
- FLAC.lib
- ogg.lib
- opus.lib
- opusenc.lib
- opusfile.lib
- vorbis.lib
- vorbisenc.lib
- vorbisfile.lib

Libraries built on Linux / Mac:
- libFLAC.a
- libogg.a
- libopus.a
- libopusenc.a
- libopusfile.a
- libvorbis.a
- libvorbisenc.a
- libvorbisfile.a

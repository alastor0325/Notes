# Parsing MP4 in Gecko

## Medadata

First the init data segment will be found by [MP4Metadata::Metadata](https://searchfox.org/mozilla-central/rev/b25ff1fab82c2d3a91531ad3735e50422407b163/dom/media/mp4/MP4Demuxer.cpp#92) which uses `MoofParser` to [find metadata](https://searchfox.org/mozilla-central/rev/b25ff1fab82c2d3a91531ad3735e50422407b163/dom/media/mp4/MoofParser.cpp#206-234) by looking for [the `moov` box](https://searchfox.org/mozilla-central/rev/b25ff1fab82c2d3a91531ad3735e50422407b163/dom/media/mp4/MoofParser.cpp#193-200).

Then uses [rust mp4 parser to parse](https://searchfox.org/mozilla-central/rev/b25ff1fab82c2d3a91531ad3735e50422407b163/dom/media/mp4/MP4Metadata.cpp#101) that segement of data.

For detailed metadata for each track, they will be parsed [here](https://searchfox.org/mozilla-central/rev/b25ff1fab82c2d3a91531ad3735e50422407b163/dom/media/mp4/MP4Metadata.cpp#348-349,375-376) after we determines the available [track count](https://searchfox.org/mozilla-central/rev/b25ff1fab82c2d3a91531ad3735e50422407b163/dom/media/mp4/MP4Demuxer.cpp#114,127).

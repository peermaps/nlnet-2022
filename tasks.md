Peermaps is an end-to-end stack for displaying, searching, and sharing
geographic data cooperatively over peer to peer networks. The scope of peermaps
is similar to commercial offerings such as google maps but with additional tools
for offline and online p2p collaboration.

Instead of fetching geospatial data from a centralized tile service, you fetch
data from peers across the network by querying a geospatial database written in
rust (eyros) that runs locally on your device and compiles to wasm for use in
web pages. The database can query very large datasets such as all of
OpenStreetMap in a sparse way, downloading only what it needs to display
features inside a given bounding box. And you can work with whatever data you've
previously downloaded onto a device offline, including search and directions.

# 0. import natural earth data into peermaps

Process [natural earth data][] into [georender format][] and store in [eyros][]
for the 3 levels of detail provided: 110m, 50m, and 10m. Include enough
information so that someone familiar with the geography of an area can navigate
to that spot from a fully zoomed-out world map view down to the full base map.

milestones:

- [x] [fixes](https://github.com/peermaps/georender-clip/commits/main)
to [polygon clipping pipeline](https://github.com/peermaps/natural-earth-ingest/blob/main/build.sh)
- [x] [generate archives for 110m, 50m, 10m](https://github.com/peermaps/data#natural-earth-vector-eyros-georender)
- [x] seed p2p archives to [ipfs](https://ipfs.io/ipfs/QmY1Ggv8EZT2973nNwjMB4rarUxiCgkAEADoiczWekpayq)
and [hyperdrive](hyper://14812ae6cafa38b8a62dc85b68e0541cbd7ce8801d3adc4cb0ab8aec698a4879)

amount: €4000

[natural earth data]: https://www.naturalearthdata.com/
[georender format]: https://github.com/peermaps/docs/blob/master/georender.md
[eyros]: https://github.com/peermaps/eyros

# 1. archive of reduced osm geometry

There is a gap between the 10m natural earth data and full planet OSM data that
could be addressed with a preset filter to simplify geometry and only show
medium-scale features such as major roads, railways, rivers. This pre-processed
archive will save bandwidth when transitioning between zoom levels as a
substitute for or augment to peer queries for filtering.

milestones:

* additional filter phase for ingest pipeline
* generate archive for filtered data on vps
* seed p2p archive to ipfs and hyperdrive

amount: €4000

# 2. peer query filtering prototype

Request that peers omit certain elements from a bounding box request on an
eyros database of georender data to save bandwidth. This is an
alternative/enhancement over an archive of pre-filtered medium-scale data that
allows more flexibility in the features of interest at the expense of more
processing done by peers fulfilling the request.

milestones:

* prototype not specific to mapping domain running on a p2p sidechannel
* peermaps-specific querying p2p layer
* integrate queries into the web ui

amount: €4000

# 3. networking ui

In the web interface, there are many ways to send and receive data. This
networking ui will provide users with options to control networking resources.

milestones:

* manage list of data sources/protocols with performance statistics
* live display of requests and responses
* display and control data usage
* pause/resume networking

amount: €2000

# 4. data ui

In the web interface, provide controls for managing storage on the device with
IndexedDB.

milestones:

* show total data stored on the device
* list tree files by time of last access with bounding extents
* save/delete per tree file with automated helpers to save space
* set areas that should always maintain offline data

amount: €2000

# 5. spatial database optimization

The ingest pipeline has a custom optimization that occurs at the very end of
processing to greatly reduce the amount of data transfer by clients. This
optimization can be moved into the database itself.

milestones:

* integrate optimization into eyros core
* move manual process of data transfer measurement into benchmark suite
* verify that optimization results in similar amount of data transfer

amount: €2000

# 6. p2p web swarm endpoints

Connect to other ipfs and hyperdrive peers over webrtc in the web client.

milestones:

* data source type for hyperswarm-web with configurable bootstrap servers
* hook up hyperswarm-web connection to hyperdrive storage wrapper
* data source type for ipfs-js 

amount: €3000

# 7. rendering engine improvements

While a majority of the rendering engine is complete, a few major items remain
for parity with other web map engines.

milestones:

- [x] [use instanced geometry to render lines with attributes for patterns (dashes)](https://github.com/peermaps/mixmap-georender/commit/bd99a721bade3340892c6a517fbf6f19c26f910c) (solid/dashed effect achieved with slightly different technique)
- [x] [use instanced geometry for point sprites instead of GL\_POINTS](https://github.com/peermaps/mixmap-georender/compare/91148bf46fecfd01141c7f0dbe2f80a070bf1e60..0fd7eac903504ffde37c69d351975a9ecee913a1)
- [ ] detect sides of coastlines and draw solids for land and sea
- [x] sprites and patterns:
  * [changes in mixmap-georender](https://github.com/peermaps/mixmap-georender/compare/master...sprite-debug)
  * [changes in georender-style2png](https://github.com/peermaps/georender-style2png/compare/master...sprite-debug)
  * [changes in glsl-georender-style-texture](https://github.com/peermaps/glsl-georender-style-texture/compare/master...sprite-debug)
  * [screenshots](https://github.com/peermaps/mixmap-georender/issues/13#issuecomment-1261670143)

amount: €10000

# 8. improve default stylesheet

Peermaps uses a default stylesheet which was developed primarily to debug the
rendering engine. New default styles are needed for better-looking maps.

milestones:

* consistent weight across all zoom levels for line widths and point sizes
* create sprites for common features (major roads, amenities, natural, etc)
* fine-tuning color choices
* set appropriate z-index so that important features do not get hidden

amount: €1000

# 9. fix label rendering

Labels were quickly implemented and are presently only displayed for point
features. Add additional refinements to label placement and rendering.

milestones:

- [x] [fix canvas text rendering](https://github.com/peermaps/peermaps-web/pull/47) (or use signed
  distance or [vector labels](https://github.com/peermaps/qbzf) (even better, using bezier curves)
- [x] [incorporate label priority into the stylesheet format](https://github.com/peermaps/georender-style2png/compare/653fa1f319745ce8534d0d95807b7d3670cb4959..366d3220082fe1d008c93ddabe24cbab5c7b558b)
- [x] [labels constrained to follow line features](https://github.com/peermaps/mixmap-georender/commit/f7e839ff041e01fcc71a9b692adc1195ea2ffad8)
- [x] [labels constrained to sit inside areas](https://github.com/peermaps/mixmap-georender/pull/27/commits/3675d1dfb4ed459cd2ecbbac96b835f645573628)
- [x] [label colors, sizes, and constraints integrated into
  stylesheet](https://github.com/peermaps/georender-style2png/compare/653fa1f319745ce8534d0d95807b7d3670cb4959..366d3220082fe1d008c93ddabe24cbab5c7b558b)

amount: €3000

# 10. standalone web app

Launch the existing web ui on peermaps.org as a web application.

milestones:

* jump to coordinates and generate link for any map view
* glsl-proj4 map projection options
* query tool to show id, feature type, full set of labels
* on-screen search/filter by label, feature type
* import/export settings data as json
* set custom stylesheet png
* deploy on peermaps.org

amount: €5000

# 11. iframe embedded web map

Embed a map on another domain with an iframe.

milestones:

* generate html iframe snippet for embedded map view in the web ui
* postMessage api to dynamically drive the map
* set configuration options (stylesheet etc) as url hash parameters
* deploy on peermaps.org

amount: €5000

# 12. audio maps

Explore the map using only audio with the web speech synthesis API.
Use text or voice recognition API to input commands.

milestones:

* set your position
* move the map position with relative commands
(go north to intersection, go west 50m, etc)
* list nearby labels with their distance and direction

amount: €2000

# 13. cities 500 db

Jump to the location of a regional or city-scale feature by sparsely loading p2p
data processed from [geonames][].

milestones:

- [x] [build](https://github.com/peermaps/sparse-geonames-ingest) and [seed city archive](https://github.com/peermaps/data#sparse-geonames-cities500) to be [loaded in a sparse fashion](https://github.com/peermaps/sparse-geonames-search)
- [x] [web ui to jump to a city based on name, presenting multiple options](https://github.com/peermaps/peermaps-web/pull/38)

amount: €2000

[geonames]: https://download.geonames.org/export/dump/


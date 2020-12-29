###
# We use some of the OSRM tools during the "build" process, so we'll
# base our builder image from their's.
###
from osrm/osrm-backend:latest as builder

# `curl` is used for downloading the Scotland extract from geofabrick
# and for finding the bounds of Inverness. `jq` is used to extract the
# bounds from the json results from nominatim. `osmctools` is used to
# make our own Inverness extract so the rest of the building and the
# route calculation is more efficient.
run apt-get update && \
    apt-get install -y \
      curl \
      jq \
      osmctools \
    && \
    rm -rf /var/lib/apt/lists/*

# Download the latest Scotland extract from geofabrik.
run curl https://download.geofabrik.de/europe/great-britain/scotland-latest.osm.pbf >/opt/scotland-latest.osm.pbf

# Ask OSM's Nominatim service for Inverness' bounds, then create our
# own extract that only contains Inverness from the wider Scotland
# extract.
run BOUNDS=$(curl \
              -H "User-Agent: ActiveTravel-Inverness-Router-Builder/2020.12.29" \
              "https://nominatim.openstreetmap.org/search.php?city=Inverness&state=Scotland&country=United%20Kingdom&format=jsonv2" | \
            jq "[[.[0].boundingbox[] | tonumber] | .[2,0,3,1]] | @csv" | \
            tr -d '"' \
           ); \
    osmconvert /opt/scotland-latest.osm.pbf -b=$BOUNDS -o=/opt/inverness.pbf

# Extract the routing data and any turn restrictions using the
# "bicycle" profile. This builds the overall routing graph.
run osrm-extract -p /opt/bicycle.lua /opt/inverness.pbf

# Hierarchically partition the graph in to recursive cells.
run osrm-partition /opt/inverness.osrm

# Calculate the routing weights for the cells.
run osrm-customize /opt/inverness.osrm



###
# Start the final image from a clean copy of the osrm-backend.
###
from osrm/osrm-backend:latest

# Copy across the pre-calculated routing graph.
copy --from=builder /opt/inverness.osrm* /opt/

# We're going to publish two copies of the router. This copy (cycling)
# of the server will run on port 5002.
expose 5002

# Run the server and respond to requests.
cmd [ "osrm-routed", "--port", "5002", "--algorithm", "mld", "/opt/inverness.osrm" ]

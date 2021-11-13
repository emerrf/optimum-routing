# VRPExample

## Environment
```
> conda env create -f conda.yml
```

## OSRM (Open Source Routing Machine)
Steps to setup ORSM API using Docker Image. The service computes the distance time-matrix for routes. Solves TSP route using OpenStreetMap data.
Note: Truck profile not available in OSRM image. Use custom profile from https://github.com/Project-OSRM/osrm-profiles-contrib/tree/master/5/21/truck-soft as starting point.

## Setup 
1. Download Map data from OpenStreetMap. Distribution Server: https://download.geofabrik.de/europe.html. For instance: `spain-latest.osm.pbf`

2. Preprocess Map with car profile for OSRM backend. Only required first time, then the backend can use the osrm format. It takes 30min approx.
```
docker run -t -v "$PWD/osrm_data:/data" osrm/osrm-backend osrm-extract -p /opt/car.lua /data/spain-latest.osm.pbf
docker run -t -v "$PWD/osrm_data:/data" osrm/osrm-backend osrm-partition /data/spain-latest.osrm
docker run -t -v "$PWD/osrm_data:/data" osrm/osrm-backend osrm-customize /data/spain-latest.osrm
```

3. Launch OSRM Service backend
```
docker run -t -i -p 5000:5000 -v "$PWD/osrm_data:/data" osrm/osrm-backend osrm-routed --algorithm mld /data/spain-latest.osrm
```

4. (Optional) OSRM Frontend to show point-to-point routes
```
docker run -p 9966:9966 osrm/osrm-frontend
```
Open browser http://127.0.0.1:9966/

## Sample Data from roba amiga cooperativa
Download JSON data from Embeded map. 


## Lessons learnt
- Turck profile not available. OSM does not cover very well forbidden roads by tracks. Everything is set from profile.
# Geoserver 

## Tags

GIS, linux

## Deploy


[linux install guide](https://docs.geoserver.org/main/en/user/installation/linux.html)

Change the admin user & pass from "admin" & "geoserver"

Set "System Properties" in the `setup.sh`

export JAVA_OPTS="-Duser.timezone=GMT"
export JAVA_OPTS="$JAVA_OPTS -Dfoo.bar=blah"
echo "JAVA_OPTS: ${JAVA_OPTS}"

[Extend functionality](https://docs.geoserver.org/latest/en/user/extensions/index.html#extensions) from [plugin repository](https://build.geoserver.org/geoserver/2.24.x/community-latest/) by [manual installation](https://docs.geoserver.org/main/en/user/styling/workshop/setup/install.html#manual-install)


## Mount an Azure Blobstore

[Mount Azure Blob Store on Linux VM](https://learn.microsoft.com/en-us/azure/storage/blobs/blobfuse2-how-to-deploy?tabs=RHEL) then [configure BlobFuse2](https://learn.microsoft.com/en-us/azure/storage/blobs/blobfuse2-configuration)

The docs above are worthwhile, but essentially on Ubuntu 22.04 install  

```
sudo wget https://packages.microsoft.com/config/ubuntu/22.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
sudo apt-get install libfuse3-dev fuse3

sudo apt-get install blobfuse2
```

create an empty dir to mount the container to `mkdir ~/mycontainer`

[Create a config.yaml for BlobFuse2](https://github.com/Azure/azure-storage-fuse/blob/main/setup/baseConfig.yaml)

Mount the store `blobfuse2 mount ~/mycontainer --config-file=./config.yaml`

Unmount the store `sudo blobfuse2 unmount ~/mycontainer`

### [Troubleshooting](https://github.com/Azure/azure-storage-fuse/blob/main/TSG.md#common-mount-problems)

#### ...only the root user can access the mount?
`allow-others: true` must be accompanied by unhashing the respective option in `/etc/fuse.conf`, being a libfuse level config. 

#### ...only the root of the store is mounted?
Ensure in Azure Storage Overview `Hierarchical namespace Enabled`

In `config.yaml`

remove `endpoint: https://xxx.blob.core.windows.net/`

set `type: adls`

## Raster


### [Cloud Optimised Geotiffs](https://docs.geoserver.org/main/en/user/community/cog/cog.html)

Install the plugin

Convert the tifs to COGs

`gdal_translate foo.tif foo_cog.tif -of COG -co COMPRESS=LZW`

Under Data, to add the raster as a layer

`Stores > Add New Store > Geotiff`

Tile Caching TMS is the XYZ tile layer you're looking for, gridset EPSG:900913 is EPSG:3857

The base url for the TMS should inspect like:

```
<TileMapService version="1.0.0" services="http://20.117.188.5:8080/geoserver/gwc/">
	<Title>Tile Map Service</Title>
	<Abstract>A Tile Map Service served by GeoWebCache</Abstract>
	<TileMaps>
		<TileMap title="whatever.you.called.your.geotiff" srs="EPSG:900913" profile="local" href="http://localhost:8080/geoserver/gwc/service/tms/1.0.0/workspace:whatever.you.called.your.geotiff@EPSG:900913@png"/>
	</TileMaps>
</TileMapService>
```

Take the href and add the XYZ bit: http://localhost:8080/geoserver/gwc/service/tms/1.0.0/workspace:whatever.you.called.your.geotiff@EPSG:900913@png/{z}/{x}/{y}.png 

Note that you may have to invert y in qgis ie {z}/{x}/{-y}.png


[Want to understand TMS/XYZ raster tiles and web (spherical) mercator?](https://gist.github.com/maptiler/fddb5ce33ba995d5523de9afdf8ef118#file-globalmaptiles-py)







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

## Raster


### [Cloud Optimised Geotiffs](https://docs.geoserver.org/main/en/user/community/cog/cog.html)

Install the plugin

Convert the tifs to COGs

`gdal_translate foo.tif foo_cog.tif -of COG -co COMPRESS=LZW`

Under Data, to add the raster as a layer

`Stores > Add New Store > Geotiff`

### [Image Mosaics]()







# gdal-grande

Ubuntu base Docker image with GDAL compiled with ESRI FileGDB and Oracle (OCI)
driver support.

Dockerfile based on https://github.com/haies/gdal

Ubuntu version: 20.04 (Focal)

Library versions:

- GDAL: 3.0.0
- OpenJPEG: 2.3.1
- Proj: 8.2.1

# Build new image

1. Run

```bash
sudo docker image build --tag f_esri .
```

# GDAL Grande: FileGDB Conversion Examples

This document provides usage and conversion examples for using GDAL to convert OGC GeoPackage files (`.gpkg`) to ESRI File Geodatabase (`.gdb`) format.

## Requirements

Before starting, ensure you have the `gdal-grande` Docker image built and ready to use. This image supports the ESRI FileGDB format.

### Check GDAL FileGDB Support

To ensure that the GDAL installation in the Docker image has FileGDB write support, run the following command:

```bash
docker run --rm f_esri ogrinfo --formats | grep -i "FileGDB"
```

Expected output:

```
  OpenFileGDB -vector- (rov): ESRI FileGDB
  FileGDB -vector- (rw+v): ESRI FileGDB
```

The `rw+v` indicates that FileGDB writing is supported.

## Basic GeoPackage to FileGDB Conversion

To convert an OGC GeoPackage (`.gpkg`) to FileGDB (`.gdb`), run the following command:

```bash
docker run --rm -v $(pwd):/data f_esri ogr2ogr -f "FileGDB" /data/output.gdb /data/input.gpkg
```

### Explanation:
- `$(pwd):/data`: Mounts the current directory into the Docker container's `/data` directory.
- `/data/input.gpkg`: The input GeoPackage file.
- `/data/output.gdb`: The output FileGDB directory.

After the conversion, `output.gdb` will be created in the current directory as a File Geodatabase.

### List Layers in the Output FileGDB

To verify the contents of the newly created FileGDB:

```bash
docker run --rm -v $(pwd):/data f_esri ogrinfo /data/output.gdb
```

This command lists the layers and metadata of the FileGDB.

## Sharing FileGDB

Since a `.gdb` is a directory containing multiple files, it is recommended to zip the entire `.gdb` folder for sharing:

### On Linux/MacOS:
```bash
zip -r output.gdb.zip output.gdb
```

### On Windows:
Right-click the `output.gdb` folder and select **Send to → Compressed (zipped) folder**.

The recipient can then unzip the folder and use it in GIS software like ArcGIS or QGIS.

## Additional Notes

- Ensure that the full `.gdb` directory is included when zipping or sharing the FileGDB, as missing files may result in an incomplete or corrupted geodatabase.
- If you're using FileGDB for sharing or long-term storage, consider its compatibility limitations compared to open formats like GeoPackage.


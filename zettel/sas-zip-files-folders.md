---
created: 20250528
---

# sas-zip-files-folders

#zettel

summary::

## refs

### up

### down

## content


```sas
/* Specify the directory and output files */
%let dir_to_compress = /path/to/your/folder; /* The folder you want to compress */
%let tar_file = /path/to/your/folder.tar; /* The tar file to create */
%let gz_file = /path/to/your/folder.tar.gz; /* The final gzip compressed file */

/* Create the tar file */
options noxwait; /* Allows command line to run without waiting */
x "tar -cvf &tar_file &dir_to_compress"; /* Create a tarball */

/* Compress the tar file using gzip */
x "gzip &tar_file"; /* This will create folder.tar.gz and remove folder.tar */
```

## Create storage
Local storage
```shell
$ curl -XPOST http://localhost:3000/storage 
      -H "Content-Type: application/json" 
      -d '{ 
      "name": "main",
      "storage_type": "local",
      "config": { 
        "host_id": "b80aa8bc-8acf-4f05-abe1-25777124134d"
        }
      }'
{"response":"01199fac-f06a-4240-ba3c-87e0b70f4d4f"}
```
This will create a path on the host:
```
/home/qarax/storage/local/01199fac-f06a-4240-ba3c-87e0b70f4d4f/
```

### Creating local volumes
Local volumes are expected to be present and accessible only to a certain host. This means VMs these volumes are attached can only run on these host. To create a new volume, we have arguments to set, such as the storage id, and if the storage is local we need to somehow get that drive to the host: we can add a parameter to the `POST /drives/` request, like `url` which will represent the location the of drive, initially we can support to types of locations, http and local
  1. http: this URL will be downloaded to the host directly and saved in the storage directory
  2. file: the volume will be in a path on the where request is run, in this case it would need to be uploaded to the host
    
#### Uploading local files

If a user wishes to create a local VM drive (created on local storage), by uploading a local file he has containing a file system (to serve as the rootfs of the VM), we need to transfer this file to the qarax-node host. This can be done using the following steps:
1. Users sends a `POST /drives/` request to qarax with the payload
```json
{ 
  "name":
  "ubuntu",
  "readonly": false,
  "rootfs": true,
  "storage_id": "01199fac-f06a-4240-ba3c-87e0b70f4d4f"
  "size": 1 # GiB
  "path": "file://bionic.rootfs.ext4" # optional, otherwise an empty drive will be create
}
```
A `size` parameter should also be present, but if a path is specified we should use the file's size

2. By analysing the URI scheme, we see that path is a local file. We then create a new empty file with the provided size (or use the actual file to the determine the size?) in the qarax-node host
`/home/qarax/storage/local/01199fac-f06a-4240-ba3c-87e0b70f4d4f/<new UUID>`
3. Start an `nbd` server exporting the created file
4. Once creation complete (should be quick) we can create an entry in the database
5. Respond to the user with the details, containing the volume id, and the NBD export the should be mounted
6. Perform the upload by copying to the mapped nbd device

##### Issues
This approach is limiting as it requires things on the client, but it offers an opportunity for a much better performance. Perhaps later on it may be a good idea to add a fallback options using plain http.

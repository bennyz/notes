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
{"response":"01199fac-f06a-4240-ba3c-87e0b70f4d4f"      
```
This will create a path on the host:
```
/home/qarax/storage/local/01199fac-f06a-4240-ba3c-87e0b70f4d4f/
```

### Creating local volumes
Local volumes are expected to be present and accessible only to a certain host. This means VMs these volumes are attached can only run on these host. To create a new volume, we have arguments to set, such as the storage id, and if the storage is local we need to somehow get that drive to the host: we can add a parameter to the `POST /drives/` request, like `url` which will represent the location the of drive, initially we can support to types of locations, http and local
  1. http: this URL will be downloaded to the host directly and saved in the storage directory
  2. file: the volume will be in a path on the where request is run, in this case it would need to be uploaded to the host
    

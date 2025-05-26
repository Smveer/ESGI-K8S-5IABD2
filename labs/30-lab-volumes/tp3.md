SINGH Manveer 5IABD2

# Lab 3 - Volumes

### Existing volumes

1. run `docker container run --name couchdb -d -p 5984:5984 couchdb:2.1`
    ```
    (base) smveer@Manveers-MacBook-Pro ~ % docker image pull couchdb
    Using default tag: latest
    latest: Pulling from library/couchdb
    b16f1b166780: Already exists 
    40594f806b01: Pull complete 
    2d4c056b6f9a: Pull complete 
    6fad9e8774e7: Pull complete 
    39b8dea16374: Pull complete 
    d005391fd3b6: Pull complete 
    94823bd70f7b: Pull complete 
    b86c1bc38891: Pull complete 
    f0fc20ed043b: Pull complete 
    82bd6c73eeca: Pull complete 
    b9b784812863: Pull complete 
    Digest: sha256:dfb99cac32d466013e2ef578d09f9831a9bc86043b1c453cf671c069902c75a1
    Status: Downloaded newer image for couchdb:latest
    docker.io/library/couchdb:latest
    (base) smveer@Manveers-MacBook-Pro ~ % docker container run --name couchdb -d -p 5984:5984 couchdb
    7b34473674428e2fad5cb077cb92b4bd12ceac4351a6507b93a54cf3bc14e789
    (base) smveer@Manveers-MacBook-Pro ~ % docker ps -a
    CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS                     PORTS     NAMES
    7b3447367442   couchdb   "tini -- /docker-ent…"   5 seconds ago   Exited (1) 5 seconds ago             couchdb
    (base) smveer@Manveers-MacBook-Pro ~ %
    ```
    Mon container s'éteint dès sa création (on a vu ensemble)
    ```
    (base) smveer@Manveers-MacBook-Pro ~ % docker ps
    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
    ```
2. Check existing volumes
    ```
    (base) smveer@Manveers-MacBook-Pro ~ % docker container inspect couchdb
    ...
    
            "Mounts": [
                {
                    "Type": "volume",
                    "Name": "df6a89202c449163683b6000630d00d20c98f0db55c5967aa28a0f5a5d8e2922",
                    "Source": "/var/lib/docker/volumes/df6a89202c449163683b6000630d00d20c98f0db55c5967aa28a0f5a5d8e2922/_data",
                    "Destination": "/opt/couchdb/data",
                    "Driver": "local",
                    "Mode": "",
                    "RW": true,
                    "Propagation": ""
                }
            ],
    
    ...
    (base) smveer@Manveers-MacBook-Pro ~ %
    ...
    ```
    1. Why there is already a volume ?
       ```
       Because it was created by default when we created the container with couchdb image, we can also see in the docker regestry of couchdb: the layer 15 is a volume layer set in the image
       ```
      
3. Identify the volume that is used by `couchdb`
    ```
    (base) smveer@Manveers-MacBook-Pro ~ % docker volume inspect df6a89202c449163683b6000630d00d20c98f0db55c5967aa28a0f5a5d8e2922
    [
        {
            "CreatedAt": "2025-05-26T16:29:49Z",
            "Driver": "local",
            "Labels": {
                "com.docker.volume.anonymous": ""
            },
            "Mountpoint": "/var/lib/docker/volumes/df6a89202c449163683b6000630d00d20c98f0db55c5967aa28a0f5a5d8e2922/_data",
            "Name": "df6a89202c449163683b6000630d00d20c98f0db55c5967aa28a0f5a5d8e2922",
            "Options": null,
            "Scope": "local"
        }
    ]
    (base) smveer@Manveers-MacBook-Pro ~ %
    ```

4. Mount the identified volume to busybox
    ```
    (base) smveer@Manveers-MacBook-Pro ~ % docker run -ti --name busybusy -v df6a89202c449163683b6000630d00d20c98f0db55c5967aa28a0f5a5d8e2922:/dsk busybox
    / # cd dsk/
    /dsk # ls
    /dsk #
    ```
5. Check files inside `/opt/couchdb/data`
    ```
    Je ne peux check vu que mon container est éteint
    ```
6. Stop couchdb
    ```
    Je ne peux faire vu que mon container est déjà éteint
    ```
7. Delete the volume
    ```
    (base) smveer@Manveers-MacBook-Pro ~ % docker rm 7b34473674428e2fad5cb077cb92b4bd12ceac4351a6507b93a54cf3bc14e789  6d6d7e8dd53c371dcd67b8b6be25a06cee8a9b3c4e40a1eb19b46687f8dc0ce6
    6d6d7e8dd53c371dcd67b8b6be25a06cee8a9b3c4e40a1eb19b46687f8dc0ce6
    7b34473674428e2fad5cb077cb92b4bd12ceac4351a6507b93a54cf3bc14e789
    (base) smveer@Manveers-MacBook-Pro ~ % docker volume rm --force df6a89202c449163683b6000630d00d20c98f0db55c5967aa28a0f5a5d8e2922                                                          
    df6a89202c449163683b6000630d00d20c98f0db55c5967aa28a0f5a5d8e2922
    (base) smveer@Manveers-MacBook-Pro ~ %
    ```
8. Check that the volume has been deleted
    ```
    (base) smveer@Manveers-MacBook-Pro ~ % docker volume ls 
    DRIVER    VOLUME NAME
    (base) smveer@Manveers-MacBook-Pro ~ %
    ```

### Create a named volume

1. Create a volume named : `couchdb_vol`
    ```
    (base) smveer@Manveers-MacBook-Pro ~ % docker volume create couchdb_vol
    couchdb_vol
    (base) smveer@Manveers-MacBook-Pro ~ %
    ```
2. Run `couchedb` with the created volume
    ```
    (base) smveer@Manveers-MacBook-Pro ~ % docker container run --name couchdb -d -p 5984:5984 -v couchdb_vol couchdb
    270f6c32de3e1071a4066087c1d5a08d73594a0efb7976e9d1371250ec5f2ad0
    (base) smveer@Manveers-MacBook-Pro ~ %
    ```
3. Inspect the container and look at `Mounts` that `couchdb_vol` is used
   ```
   (base) smveer@Manveers-MacBook-Pro ~ % docker container inspect couchdb
   ...
   
            "Mounts": [
                {
                    "Type": "volume",
                    "Name": "8075ed3ad1e1a66252e037d4c1ce47469ffdcad35a8d91b7a8d7377831bc8032",
                    "Source": "/var/lib/docker/volumes/8075ed3ad1e1a66252e037d4c1ce47469ffdcad35a8d91b7a8d7377831bc8032/_data",
                    "Destination": "couchdb_vol",
                    "Driver": "local",
                    "Mode": "",
                    "RW": true,
                    "Propagation": ""
                },
                {
                    "Type": "volume",
                    "Name": "f7c02951c1fa8cb828a5d1720dd35fc7f83fba0294a5222e31f1570b94993c70",
                    "Source": "/var/lib/docker/volumes/f7c02951c1fa8cb828a5d1720dd35fc7f83fba0294a5222e31f1570b94993c70/_data",
                    "Destination": "/opt/couchdb/data",
                    "Driver": "local",
                    "Mode": "",
                    "RW": true,
                    "Propagation": ""
                }
            ],
   
   ...
    (base) smveer@Manveers-MacBook-Pro ~ %
   ```
   
### Mount directory

1. Mount `/var/lib/docker` from host to `/dck` into a busybox container
    ```
    (base) smveer@Manveers-MacBook-Pro ~ % docker run -ti --name busybusy -v /var/lib/docker:/dck busybox
    / #
    ```
2. Check at `ls /dck/volumes/couchdb_vol/_data` inside the container to verify that `couchdb_vol` is available
    ```
    / # cd /dck/
    /dck # ls
    buildkit    containers  engine-id   image       jfs         network     overlay2    plugins     runtimes    stats       swarm       tmp         volumes
    /dck # cd volumes/
    /dck/volumes # ls
    8075ed3ad1e1a66252e037d4c1ce47469ffdcad35a8d91b7a8d7377831bc8032  couchdb_vol                                                       metadata.db
    backingFsBlockDev                                                 f7c02951c1fa8cb828a5d1720dd35fc7f83fba0294a5222e31f1570b94993c70
    /dck/volumes #
    ```

### SideCar Pattern

1. Create a directory named `sidecar` with `mkdir`
2. Run Busybox
   1. Command: `sh -c 'while true; do date >> /dck/date.log; sleep 1; done'`
   2. Volume to mount: `$(pwd)/sidecar:/dck`
   3. Name: `gen_date`
   4. State: detached
3. Check the content of `sidecar/date.log` with `cat`
4. Run Busybox
   1. Command: `tail -f /dck2/date.log`
   2. Volume to mount: `$(pwd)/sidecar:/dck2`
   3. State: attached
5. Check content of `dck2/date.log` with `tail -f`
6. Exit container
7. Run `docker kill gen_date`
   1. Why is the container stoped ?

### In memory 

1. Run busybox with `--tmpfs /test`
2. Check with `mount | grep test` that tmpfs is used 


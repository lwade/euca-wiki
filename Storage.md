# Walrus/S3
What it is, What it does

## DesignDoc for the Cloud Admin
### Backing Store (where the buckets are)
### HA (DRBD and more)
failover, setup, DRBD info etc ...
### How is the Backing Store Used
how buckets are used and what is in them
how buckets and storage is mapped to the backing store
### Lifecycle of a Request
Get and Put examples
walrus API
### Walrus and the Database
should we talk about this? It would be obsolete the very moment we write it and the admin is not expected to use it directly
### Authentication, ACLs, Permissions

## DesignDoc for the Developers
code is the ultimate truth here

# EBS/Volumes
## DesignDoc for the Cloud Admin
### Backing Store (where the volumes are)
filesystem how is it used (LVM and loop devices)
SAN
### Export Volumes (how the volumes are accessible by instances)
### Snapshotting
### Startup and Shutdown 
there is a lot of state in the StorageManager
### Lifecycle of a Request
Create volume
Attach volume
Snapshot
how clc is involed
### Database (same issue as walrus)

## DesignDoc for the Developers
code is the ultimate truth here

# Storage Products  

## Cloud Storage

### Buckets

- steps to create a bucket :
  - specify a globally unique name
  - choose a geographic location where your bucket and its content are stored
  - a default storage class : (The default storage class you choose applies to objects added to the bucket that don't have a storage class specified explicitly.)
- You can apply labels to a bucket (key:value). Each bucket has a maximum of 64 labels

### Objects

- There is no limit on the number of objects you can store in a bucket
- Objects has two components : object data & object metadata
- Object versioning : by default when you overwrite an object, Storage deletes the old version and replaces it with a new version, unless you enable object versioning. In this case when you delete an object, the object is identified with an older generation number, you can retrieve the version by its generation number.
- objects are immutable, objects cannot change throughout it's storage life time

### Geo-redundancy

- Geo-redundant data are stored in at least two separate geographic places separated by at least 100 miles.
- Objects stored in multi-regions and dual-regions are geo-redundant.  
- ensures HA of your data
- occurs asynchronously

### Storage Locations

- Use a region to help optimize latency and network bandwidth for data consumers, such as analytics pipelines, that are grouped in the same region.

- Use a dual-region when you want similar performance advantages as regions, but also want the higher availability that comes with being geo-redundant, A dual-region is a specific pair of regions, such as Finland and the Netherlands.

- Use a multi-region when you want to serve content to data consumers that are outside of the Google network and distributed across large geographic areas, or when you want the higher availability that comes with being geo-redundant.A multi-region is a large geographic area, such as the United States, that contains two or more geographic places.

- Objects stored in a multi-region or dual-region are geo-redundant.

### Storage Classes

- Standard storage :
  - best for frequent access
  - Minimum storage duration : None
  - SLA : >99.99% in multi-regions and dual-regions. 99.99% in regions
- Nearline storage :
  - Minimum storage duration : 30 days
  - SLA : >99.95% in multi-regions and dual-regions. 99.9% in regions
- Coldline storage :
  - Minimum storage duration : 90 days
  - SLA : >99.95% in multi-regions and dual-regions. 99.9% in regions

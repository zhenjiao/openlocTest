﻿
### redisCacheName

The name of the Azure Redis Cache to create.

    "redisCacheName": {
      "type": "string"
    }

### redisCacheSKU

The pricing tier of the new Azure Redis Cache.

    "redisCacheSKU": {
      "type": "string",
      "allowedValues": [ "Basic", "Standard" ],
      "defaultValue": "Basic"
    }

The template defines the values that are permitted for this parameter (Basic or Standard), and assigns a default value (Basic) if no value is specified. Basic provides a single node with multiple sizes available up to 53 GB.
Standard provides two-node Primary/Replica with multiple sizes available up to 53 GB and 99.9% SLA.

### redisCacheFamily

The family for the sku.

    "redisCacheFamily": {
      "type": "string",
      "defaultValue": "C"
    }

### redisCacheCapacity

The size of the new Azure Redis Cache instance. 

    "redisCacheCapacity": {
      "type": "int",
      "allowedValues": [ 0, 1, 2, 3, 4, 5, 6 ],
      "defaultValue": 0
    }

The template defines the values that are permitted for this parameter (0, 1, 2, 3, 4, 5 or 6), and assigns a default value (0) if no value is specified. Those numbers correspond to following cache sizes: 
0 = 250 MB, 1 = 1 GB, 2 = 2.5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

### redisCacheVersion

The Redis server version of the new cache.

    "redisCacheVersion": {
      "type": "string",
      "defaultValue": "2.8"
    }

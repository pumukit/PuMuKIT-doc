PuMuKIT Migration Guide
=======================

### Current version

PuMuKIT 4.1.*

### Next version

PuMuKIT 4.2.*

## Steps

1. Connect to MongoDB and execute de following lines:

```
db.MultimediaObject.updateMany(
   {updatedAt: {$exists: false}},
   {$set: {updatedAt: new ISODate("1970-01-01T00:00:00Z")}}
);
```

PuMuKIT Migration Guide
=======================

### Current version

PuMuKIT 3.9.* or 4.0.*

### Next version

PuMuKIT 4.1.*

## Steps

1. Connect to MongoDB and execute de following lines:

```
db.User.updateMany(
  { fullname: { $exists: true } },
  [
    {
      $set: {
        fullName: "$fullname"
      }
    },
    {
      $unset: "fullname"
    }
 ]
)
```

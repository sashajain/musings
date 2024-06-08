# Attributes

By default Python attributes are read/write accessible by anyone with access to the object.

# Signalling no read or write access
By convention a `_` prefix to the attribute name (eg. `_foo`) indicates that the variable should not be directly accessed outside of the class.

# Enforcing limited read or write access
Python allows for custom get (`@property`), set (`@foo.setter`) and delete (`@foo.deleter`) operations for attributes. By default if no custom setter is provided, the attribute will be read-only external to the class, as an `AttributeError` will be thrown. Likewise if no getter is implemented, the attribute will not be readable outside of the class.

## Example of a property
```
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def radius(self):
        """The radius property."""
        print("Get radius")
        return self._radius

    @radius.setter
    def radius(self, value):
        print("Set radius")
        self._radius = value

    @radius.deleter
    def radius(self):
        print("Delete radius")
        del self._radius
```
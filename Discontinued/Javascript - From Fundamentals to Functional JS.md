Currently on: Property Access (10:35/11:21)

## Objects

An object is a data structure
We have key-value pairs
We store our data in there

Whatever is to the left of a dot is an object

`box.material = "cardboard"` In the dot notation, material is interpreted as a string. Same thing as:

```
{
    box: {
        "material": "cardboard"
    }
}
```

### Pass By Value

```
var box = {};
box.material = "cardboard";
var cb = box.material;
cb; // "cardboard"
box.material = "titanium";
cb; // "cardboard"
```

Even though `box.material` was changed, the value of `cb` stayed the same.

(Stopped here because I don't like the course structure)
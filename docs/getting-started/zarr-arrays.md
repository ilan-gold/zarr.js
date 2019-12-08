# ZarrArrays

Quote from the Python zarr documentation:  
*\"Zarr provides classes and functions for working with N-dimensional arrays that behave like NumPy arrays but whose data is divided into chunks and each chunk is compressed. If you are already familiar with HDF5 then Zarr arrays provide similar functionality, but with some additional flexibility.\"*

## Creating a ZarrArray

```javascript
import {zeros, NestedArray, slice} from "zarr";
const z = zeros([1000, 1000], {chunks: [100, 100], dtype: '<i4'})
console.log(z);
```
```output
ZarrArray {
  store: MemoryStore { ... },
  meta: {
    shape: [ 1000, 1000 ],
    chunks: [ 100, 100 ],
    dtype: '<i4',
    fill_value: 0,
    ...
  },
  attrs: ...,
  ...
}
```

The code above creates a 2-dimensional array of 32-bit integers with 1000 rows and 1000 columns, divided into chunks where each chunk has 100 rows and 100 columns (and so there will be 100 chunks in total).


## Reading and writing data
Zarr arrays supports the same interface as [NestedArrays](/getting-started/nested-arrays) for reading and writing data. For example, the entire array can be filled with a scalar value:
```javascript
await z.set(null, 42);
```

Regions of the array can also be written to, e.g.:
```javascript
z.set([0, null], NestedArray.arange(1000));
z.set([null, 0], NestedArray.arange(1000));
```

The contents of the array can be retrieved by slicing, which will load the requested region into memory as a [NestedArray](/nested-arrays.md), e.g.:
```javascript
await z.get([0, 0]); // 0
await z.get([-1,-1]) // 42
```

```javascript
// z[0, :]
await z.get([0, null]);

// Return value
NestedArray {
  shape: [ 1000 ],
  dtype: '<i4',
  data: Int32Array [0,  1,  2,  ..., 997, 998, 999]
}

```

```javascript
// z[:, 0]
await z.get([null, 0]);

// Return value
NestedArray {
  shape: [ 1000 ],
  dtype: '<i4',
  data: Int32Array [0,  1,  2,  ..., 997, 998, 999]
}
```

```javascript
// z[:]
(await z.get([null]));

// Return value
NestedArray {
  shape: [ 1000, 1000 ],
  dtype: '<i4',
  data: ... // this prints a lot!
}
```

#### Next steps
Read about [ZarrArrays whose data lives remotely](/getting-started/remote-data.md).
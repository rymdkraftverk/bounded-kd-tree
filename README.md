# Bounded KD Tree

This library has functionality for sorting and querying data in a [KD Tree](https://en.wikipedia.org/wiki/K-d_tree)
like structure. KD Trees are typically constructed by starting with a
relatively large amount of data.  Possibly all of the data that's intended to
be in the tree.

If this approach is applied to a scenario where entitys are added one by one
the tree will quickly become very unbalanced. This library attempts to solve
that problem by having the user specify a space that all entitys will be
confined within and the tree will assume that the entitys will roughly evenly
distributed within that space.

This can be useful when making games where immovable entitys are added in some
space over time.

## Documentation

### Create an empty tree

The tree must be initialized with the boundaries that the entities fall within.
Each dimension must have a `min` and `max` value.

```js
const emptyTree = KDT.initEmptyTree({
  x: {
    min: 0,
    max: 10,
  },
  y: {
    min: 0,
    max: 10,
  },
})
```

### Add an entity to a tree

```js
const emptyTree = KDT.initEmptyTree({
  x: {
    min: 0,
    max: 10,
  },
  y: {
    min: 0,
    max: 10,
  },
})

const myEntity = {
    x: 3,
    y: 2
}

// this is the default value of the getCoord function. An empty options object
// works just as well
const options = {
    getCoord = (entity, dimension) => entity[dimension]
}

const updatedTree = KDT.addEntityToTree(options, emptyTree, myEntity)
```

### Find the nearest neighbour (search the tree)

```js
const tree = ...

// These are the default options. The functions will default to the given
// values if left out.
const options = {
    getCoord:    (entity, dimension) => entity[dimension],
    earlyReturn: (entity) => false,
    filter:      (entity) => true
}

const myEntity = {
    x: 3,
    y: 2
}

const foundEntity = KDT.nearestNeighbour(options, tree, myEntity)
```

The `getCoord` function is used to extract coordinate data from entities.

The `earlyReturn` function can be used to cancel the search if a found entity
is deemed to be "close enough". It defaults to never returning early and thus
always finding the entity that is truly closest.

The `filter` function is used to exclude entities in the tree when searching.
This can be useful there are different kinds of entities in the same tree and
only one type is relevant. The default variant will not filter out anything (it
always returns `true`).

### Remove an entity from a tree

To remove all entities that have the `id` `123`, create a function that filters
out such entities.

```js
const options = {}
const filterPredicate = entity => entity.id !== 123
const updatedTree = KDT.filter(options, filterPredicate, myTree)
```


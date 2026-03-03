## Data Model

**Structure:** Instance → Databases → Collections → Documents

- **Document** = one JSON object (stored as BSON, max 16MB)
- **Collection** = group of similar documents (no enforced schema)
- **Primary Key** = `_id` field (unique, immutable, auto-generated as ObjectId if not provided)

```javascript
{
    _id: ObjectId("507f1f77bcf86cd799439011"),
    title: "Samotáři",
    year: 2000,
    actors: [ ObjectId("4"), ObjectId("5") ]
}
```

### Dot Notation

```javascript
"director.firstname"      // Accessing nested object field
"actors.0"                // Accessing first array element (0-indexed)
"awards.0.type"           // Combined: first award's type
```

---

## CRUD Operations

### Insert

```javascript
// Insert one document
db.actors.insertOne({ firstname: "Anna", lastname: "Geislerová" });

// Insert multiple documents
db.movies.insertMany([
    { _id: ObjectId("9"), title: "Želary", year: 2003 },
    { title: "Anthropoid", year: 2016 }
]);
```

### Find (Read)

```javascript
db.collection.find(query, projection)

// Select all
db.movies.find()
db.movies.find({})

// By _id
db.movies.find({ _id: ObjectId("2") })

// With conditions
db.movies.find({ year: 2000, rating: { $gt: 1 } })
```

### Replace

```javascript
// Replace entire document (keeps _id)
db.actors.replaceOne(
    { _id: ObjectId("8") },                        // filter
    { firstname: "Aňa", lastname: "Geislerová" }   // replacement
);
```

### Update

```javascript
// Update first matching
db.movies.updateOne(
    { year: { $gt: 2015 } },                       // filter
    { $set: { tag: "New" }, $inc: { rating: 3 } } // update operators
);

// Update all matching
db.movies.updateMany(
    { year: { $gt: 2015 } },
    { $set: { tag: "New" } }
);
```

### Delete

```javascript
db.movies.deleteOne({ _id: ObjectId("1") });
db.movies.deleteMany({ year: { $lt: 2000 } });
```

### Upsert Mode

Insert if no match found:

```javascript
db.movies.replaceOne(
    { title: "Tmavomodrý svět" },
    { title: "Tmavomodrý svět", year: 2001 },
    { upsert: true }
);

db.movies.updateOne(
    { title: "Tmavomodrý svět" },
    { $set: { year: 2001 }, $inc: { rating: 2 } },
    { upsert: true }
);
```

---

## Query Operators

### Comparison

|Operator|Meaning|
|---|---|
|`$eq`|Equal|
|`$ne`|Not equal|
|`$lt`|Less than|
|`$lte`|Less than or equal|
|`$gt`|Greater than|
|`$gte`|Greater than or equal|
|`$in`|In array of values|
|`$nin`|Not in array|

```javascript
db.movies.find({ year: { $gte: 2000, $lte: 2005 } })
db.movies.find({ year: { $in: [2000, 2005, 2010] } })
```

### Logical

```javascript
// AND (implicit - multiple conditions on same level)
db.movies.find({ year: 2000, rating: { $gt: 50 } })

// AND (explicit - required for same field or complex queries)
db.actors.find({ $and: [{ movies: "medvidek" }, { movies: "samotari" }] })

// OR
db.actors.find({ $or: [{ movies: "medvidek" }, { movies: "samotari" }] })

// NOT (only within a field condition)
db.movies.find({ year: { $not: { $gt: 2000 } } })
```

### Element

```javascript
db.movies.find({ director: { $exists: true } })   // Field exists
db.movies.find({ director: { $exists: false } })  // Field doesn't exist
```

### Array Operators

```javascript
// Contains specific value
db.actors.find({ movies: "medvidek" })

// Contains ALL values (any order)
db.actors.find({ movies: { $all: ["medvidek", "samotari"] } })

// Contains ANY of values
db.actors.find({ movies: { $in: ["medvidek", "samotari"] } })

// Array size
db.movies.find({ actors: { $size: 3 } })

// Element match - at least one element satisfies ALL conditions
db.movies.find({ 
    awards: { $elemMatch: { type: "Czech Lion", year: 2005 } } 
})
```

---

## ⚠️ Common Mistakes (INCORRECT Examples)

### 1. Embedded Document Equality - Field Order Matters!

```javascript
// ❌ INCORRECT - requires EXACT match including field order
db.movies.find({ director: { first: "Jan", last: "Hrebejk" } })
db.movies.find({ director: { last: "Hrebejk", first: "Jan" } })

// ✅ CORRECT - use dot notation
db.movies.find({ "director.first": "Jan", "director.last": "Hrebejk" })
```

**Why:** Value equality for objects requires identical field order and no extra fields.

### 2. Duplicate Keys in JSON Object

```javascript
// ❌ INCORRECT - second 'year' overwrites first!
db.movies.find({
    year: { $gte: 2000 },
    year: { $lte: 2005 }   // This replaces the line above!
})

// ✅ CORRECT - combine operators in one object
db.movies.find({ year: { $gte: 2000, $lte: 2005 } })
```

**Why:** JSON objects cannot have duplicate keys; the second one overwrites the first.

### 3. Same Field AND Condition

```javascript
// ❌ INCORRECT - duplicate key, only second condition applies
db.actors.find({ movies: "medvidek", movies: "samotari" })

// ✅ CORRECT - use $and or $all
db.actors.find({ $and: [{ movies: "medvidek" }, { movies: "samotari" }] })
db.actors.find({ movies: { $all: ["medvidek", "samotari"] } })
```

### 4. Array of Objects - $elemMatch Required

```javascript
// ❌ INCORRECT - exact array element match (order + all fields)
db.movies.find({ awards: { type: "Czech Lion", year: 2005 } })

// ❌ INCORRECT - $in also requires exact match
db.movies.find({ awards: { $in: [{ type: "Czech Lion", year: 2005 }] } })

// ❌ INCORRECT - dot notation matches across DIFFERENT elements!
db.movies.find({ "awards.type": "Czech Lion", "awards.year": 2005 })

// ❌ INCORRECT - $and with dot notation still matches different elements
db.movies.find({ $and: [
    { "awards.type": "Czech Lion" },
    { "awards.year": 2005 }
]})

// ✅ CORRECT - $elemMatch ensures SAME element satisfies both
db.movies.find({ 
    awards: { $elemMatch: { type: "Czech Lion", year: 2005 } } 
})
```

**Why:** Dot notation conditions can match different array elements. `$elemMatch` ensures one element satisfies all conditions.

---

## Update Operators

### Field Operators

```javascript
$set: { field: value }        // Set field value (create if not exists)
$unset: { field: "" }         // Remove field
$rename: { old: "new" }       // Rename field
$inc: { field: 5 }            // Increment by value
$mul: { field: 2 }            // Multiply by value
```

### Array Operators

```javascript
$push: { arr: value }                    // Add to end
$push: { arr: { $each: [1, 2, 3] } }    // Add multiple
$addToSet: { arr: value }                // Add if not exists
$pop: { arr: 1 }                         // Remove last (-1 for first)
$pull: { arr: value }                    // Remove matching values
$pull: { arr: { $gt: 5 } }              // Remove by condition
```

---

## Projection

```javascript
// Include fields (1 or true)
db.movies.find({}, { title: 1, year: 1 })

// Exclude fields (0 or false)  
db.movies.find({}, { actors: 0 })

// _id is included by default, exclude explicitly
db.movies.find({}, { title: 1, _id: 0 })
```

⚠️ **Cannot mix include/exclude** (except `_id`)

### Array Projection Operators

```javascript
// $slice - limit array elements
{ actors: { $slice: 2 } }      // First 2
{ actors: { $slice: -2 } }     // Last 2
{ actors: { $slice: [1, 2] } } // Skip 1, take 2

// $elemMatch - first matching element only
{ awards: { $elemMatch: { year: 2005 } } }
```

---

## Modifiers

```javascript
// Sort (1 = ascending, -1 = descending)
db.movies.find().sort({ year: -1, title: 1 })

// Skip
db.movies.find().skip(10)

// Limit
db.movies.find().limit(5)

// Combined (order doesn't matter in chain)
db.movies.find().sort({ year: -1 }).skip(10).limit(5)
```

---

## Complete Query Examples

### Example 1: Basic Equality with Projection

```javascript
// Actors named Jiri who played in medvidek, return only name
db.actors.find(
    { "name.first": "Jiri", movies: "medvidek" },
    { name: 1, _id: 0 }
);
```

### Example 2: Range Query with Sort

```javascript
// Movies 2000-2005 with director, sorted by rating desc, year asc
db.movies.find(
    {
        year: { $gte: 2000, $lte: 2005 },
        director: { $exists: 1 }
    },
    { _id: 1 }
).sort({ rating: -1, year: 1 });
```

### Example 3: OR with Array Contains

```javascript
// Actors in medvidek OR samotari
db.actors.find(
    { movies: { $in: ["medvidek", "samotari"] } },
    { _id: 1 }
);
// Equivalent:
db.actors.find({
    $or: [{ movies: "medvidek" }, { movies: "samotari" }]
});
```

### Example 4: AND with Array Contains All

```javascript
// Actors in BOTH medvidek AND samotari
db.actors.find(
    { movies: { $all: ["medvidek", "samotari"] } },
    { _id: 1 }
);
// Equivalent:
db.actors.find({
    $and: [{ movies: "medvidek" }, { movies: "samotari" }]
});
```

### Example 5: Complex OR with $slice

```javascript
// Comedy AND drama, OR rating >= 80, show first 2 countries
db.movies.find(
    {
        $or: [
            { genres: { $all: ["comedy", "drama"] } },
            { rating: { $gte: 80 } }
        ]
    },
    { _id: 1, country: { $slice: 2 } }
);
```

### Example 6: Flexible Title Search (string or object)

```javascript
// Title might be string or object with .cs field
db.movies.find(
    { $or: [
        { title: "Vratne lahve" },
        { "title.cs": "Vratne lahve" }
    ]},
    { title: 1, _id: 0 }
);
```

---

## MapReduce

```javascript
db.collection.mapReduce(
    mapFunction,      // function() { emit(key, value); }
    reduceFunction,   // function(key, values) { return result; }
    { 
        query: {},    // Optional: filter input documents
        out: { inline: 1 }  // Output to result set
    }
)
```

### Example: Count Movies per Actor

```javascript
db.actors.mapReduce(
    function() {
        if (Array.isArray(this.movies)) {
            this.movies.forEach(function(m) { 
                emit(m, 1); 
            });
        } else {
            emit(this.movies, 1);
        }
    },
    function(key, values) {
        return Array.sum(values);
    },
    {
        query: { movies: { $exists: true } },
        out: { inline: 1 }
    }
);
```

### Example: Group Names by Year

```javascript
db.actors.mapReduce(
    function() {
        emit(this.year, this.name);
    },
    function(key, values) {
        return values.sort();
    },
    {
        query: { year: { $lte: 2000 } },
        out: { inline: 1 }
    }
);
```

---

## Sample Exam Question Pattern

**Given cinema collection:**

```javascript
{
    _id: "session1", 
    movie: "The Martian", 
    length: 144,
    tickets: [{seat: "A05", type: "student"}, {seat: "A06", type: "adult"}],
    actor: {first: "Matt", last: "Damon", year: 1970}
}
```

**Task:** Find sessions where Matt Damon played, with at least one ticket, rating 80-90, sorted by length asc then movie desc, show movie, length, and last 2 tickets.

```javascript
db.cinema.find(
    {
        "actor.first": "Matt",
        "actor.last": "Damon",
        tickets: { $exists: true },
        "tickets.0": { $exists: true },  // At least one ticket
        rating: { $gte: 80, $lte: 90 }
    },
    {
        movie: 1,
        length: 1,
        tickets: { $slice: -2 },
        _id: 0
    }
).sort({ length: 1, movie: -1 });
```

---

## Quick Reference

|Task|Syntax|
|---|---|
|All documents|`find({})`|
|By ID|`find({ _id: ObjectId("...") })`|
|Nested field|`"field.subfield"`|
|Array index|`"array.0"`|
|Range|`{ $gte: 5, $lte: 10 }`|
|In list|`{ $in: [1, 2, 3] }`|
|Contains all|`{ $all: [1, 2] }`|
|Field exists|`{ $exists: true }`|
|Array element match|`{ $elemMatch: {...} }`|
|Sort desc|`.sort({ field: -1 })`|
|Limit results|`.limit(n)`|
|Skip results|`.skip(n)`|
|First n items|`{ $slice: n }`|
|Last n items|`{ $slice: -n }`|

```json
db.cinema.find(
{
	{"actor.first": "Matt"},
	{"actor.last": "Damon"},
	{rating: {$gte: 80, $lte: 90}},
	{tickets: {$size: {$gte: 1}}}
},
{
	{"movie": 1, "lenght": 1, "tickets": {$slice: -2}}
}
).sort({"length":1, "movie":-1})
```
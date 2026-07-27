#snowflake #sql 
# Notes
+ The aliases for each type are present for compatibility with other SQL dialects.
+ You can’t use a [[#MAP]], structured [[#OBJECT]], or structured [[#ARRAY]] value in a [[#VARIANT]], semi-structured OBJECT, or semi-structured ARRAY value.
+ BLOB, CLOB, ENUM are not supported.
# NUMBER
>[!success] Aliases: DECIMAL, DEC, NUMERIC

>[!warning] INT, INTEGER, BIGINT, SMALLINT, TINYINT, BYTEINT fields are always NUMBER(38, 0). We cannot change precision or scale.

Numbers up to 38 digits, with an optional precision and scale:
	**Precision**:  Total number of digits allowed. (including scale)
	**Scale**: Number of digits allowed to the right of the decimal point. (max 37)

- [i] Default: Precision = 38, Scale=0 expressed as `NUMBER(38, 0)`.
# FLOAT
>[!success] Aliases: FLOAT4, FLOAT8, DOUBLE, DOUBLE PRECISION, REAL

64 bit, 15 digit precision, [IEEE 754 Standard](https://www.youtube.com/watch?v=TaDrBnRS0_Q) floating point numbers. 
The special values `'Nan'`, `'inf'`,`'-inf'` are supported.
	==They must be in single quotes and are case-sensitive.==
## Comparison differences from IEEE 754
|Condition|Snowflake|IEEE 754|Comment|
|---|---|---|---|
|`'NaN' = 'NaN'`|`TRUE`|`FALSE`|In Snowflake, `'NaN'` values are all equal.|
|`'NaN' > X`  <br>where `X` is any FLOAT value, including  <br>infinity, other than `NaN` itself.|`TRUE`|`FALSE`|In Snowflake, `'NaN'` is greater  <br>than any other FLOAT value,  <br>including infinity.|
## Rounding Errors
==Note that values stored as floats are always approximations==.
- When comparing two floating-point numbers, **compare for approximate equality rather than exact equality.**
- Errors can accumulate, especially when aggregate functions —for example, SUM or AVG — process large numbers of rows. **Casting to a fixed-point data type before aggregating can reduce or eliminate these errors**.
# DECFLOAT
# VARCHAR
>[!success] Aliases: STRING, TEXT, VARCHAR2, NVARCHAR, NVARCHAR2, CHAR VARYING, NCHAR VARYING

- [i] Default length in _characters_ is 16777216. 
- [!] Max bytes : 128 MB
Stores UTF-8 characters and consumes storage for only the amount of actual data stored.

# CHAR
>[!success] Aliases: CHARACTER, NCHAR

- [i] Default length: 1
- [!] **No padding** if string length < specified max length.
Stores UTF-8 characters and consumes storage for all of the amount specified as length.

# BINARY
>[!success] Aliases: VARBINARY
- [i] Default length: 8388608 bytes
- [!] Max length: 64 MB
# String Constants
Always enclose between delimiter characters:
 + Single quotes (escape using `\` )
 + Double dollar signs (`$$`) 
	 Use when string contains one or more `''`, `\` or `\n` , to avoid escaping characters. 
# BOOLEAN
Implicit conversion of:

| Value   | Boolean |
| ------- | ------- |
| 'true'  | TRUE    |
| 'false' | FALSE   |
| 0       | FALSE   |
| != 0    | TRUE    |
Explicit conversion of:

| Value                               | Boolean |
| ----------------------------------- | ------- |
| 'true', 't', 'yes', 'y', 'on', '1'  | TRUE    |
| 'false', 'f', 'no', 'n', 'off', '0' | FALSE   |
| 0                                   | FALSE   |
| != 0                                | TRUE    |
# DATE
# DATETIME
# TIMESTAMP
# VARIANT
- [!] Max size: 128 MB (uncompressed)
It can store a value of any other type.
- [f] Use when:
-  You want to create hierarchical data.
+ You want to load JSON, AVRO, ORC or Parquet data directly, without explicitly describing the hierarchical structure of the data.

```sql
CREATE OR REPLACE TABLE variant_insert (v VARIANT);
INSERT INTO variant_insert (v)
  SELECT PARSE_JSON('{"key3": "value3", "key4": "value4"}');
SELECT * FROM variant_insert;

+---------------------+
| V                   |
|---------------------|
| {                   |
|   "key3": "value3", |
|   "key4": "value4"  |
| }                   |
+---------------------+
```

## Data type conversion
Explicit conversion to/from `VARIANT` can be done using `CAST(column)` , `TO_VARIANT(column)` or by using the `column/expr::variant` syntax.
## NULLs in VARIANT
NULL values in VARIANT are **NOT EQUAL** to SQL NULL values. They are stored as a string with the word null (`"null"`).
==To cast a VARIANT NULL to a SQL NULL, cast it to a string.==
```sql
SELECT column1
  , TO_VARCHAR(PARSE_JSON(column1):a)
FROM
  VALUES('{"a" : null}')
, ('{"b" : "hello"}')
, ('{"a" : "world"}');


+-----------------+-----------------------------------+
| COLUMN1         | TO_VARCHAR(PARSE_JSON(COLUMN1):A) |
|-----------------+-----------------------------------|
| {"a" : null}    | NULL                              |
| {"b" : "hello"} | NULL                              |
| {"a" : "world"} | world                             |
+-----------------+-----------------------------------+
```
## Subcolumnarization
When semi-structured data is inserted into a VARIANT column, Snowflake attempts to extract as much data as possible into columns. 
The extracted columns are also of type VARIANT. However, Snowflake physically stores the data using its underlying data type (number, float etc..)
Say, we have a column `my_variant_column` of VARIANT type that actually stores float values. This means that we can use `my_variant_column` in expressions directly without explicitly casting to float.

```sql
-- ❌ No need to explicitly convert to float
SELECT my_variant_column::FLOAT * 3.14 FROM ...;

-- ✅ Use as is
SELECT my_variant_column * 3.14 FROM ...;
```

>[!warning] 
>By default, when VARCHAR, DATE, TIME, and TIMESTAMP values are retrieved from a VARIANT column, the values are surrounded by double quotes.
>You can eliminate the double quotes by explicitly casting the values to the underlying data types.
>```sql
>SELECT 'Sample', 'Sample'::VARIANT, 'Sample'::VARIANT::VARCHAR;
>
>+----------+-------------------+----------------------------+
| 'SAMPLE' | 'SAMPLE'::VARIANT | 'SAMPLE'::VARIANT::VARCHAR |
|----------+-------------------+----------------------------|
| Sample   | "Sample"          | Sample                     |
+----------+-------------------+----------------------------+
>```

- [?] Elements that are not extracted
- Elements with a `"null"` value (not to be mistaken with elements with missing values).
- Deeply nested elements.
-  Elements that contain multiple data types.
 >[!example]-
>The `foo` element in one row contains a number: `{"foo" : 1}`
>but in another row, it contains a string: `{"foo" : "1"}`
- Snowflake extracts up to 200 columns from your hierarchical structure. If the structure has more than 200 extractable columns, remaining columns are kept as is in a VARIANT type column.
## Query performance
As discussed in [[#Subcolumnarization]], since most data is stored using native data types, its performance is similar to performance on native data type column.
- [!] Exceptions
- Data extracted as DATE, TIME, TIMESTAMP are extracted as strings, so performance on these columns might be slower than a column with corresponding data type.
- For elements that are not extracted, the engine has to parse and traverse the data tree structure to find and extract the value you requested, which slows down performance considerably.
# OBJECT
- [!] Max size: 128 MB
Stores key-value pairs. 
- [!] Key cannot be empty/NULL
- [!]  Value cannot be NULL for semi-structured object.

If storing semi-structured data : each key is a [[#VARCHAR]] value and each value is a [[#VARIANT]] value.
```sql
CREATE OR REPLACE TABLE object_example (object_column OBJECT);
INSERT INTO object_example (object_column)
  SELECT OBJECT_CONSTRUCT('thirteen', 13::VARIANT, 'zero', 0::VARIANT);
SELECT * FROM object_example;

+-------------------+
| OBJECT_COLUMN     |
|-------------------|
| {                 |
|   "thirteen": 13, |
|   "zero": 0       |
| }                 |
+-------------------+

-- Accessing value by key
SELECT object_column['thirteen'],
       object_column:thirteen
  FROM object_example;
```

For a **structured** object:
+ We can specify different data type for _value_ per key.
```sql
CREATE TABLE customer (
  c_address OBJECT(
    state VARCHAR,
    city VARCHAR,
    street VARCHAR,
    zip_code NUMBER
  );
  
  INSERT INTO customer SELECT
  {
    'state': 'CA',
    'city': 'San Mateo',
    'street': '450 Concar Drive',
    'zip_code': 94402
  }::OBJECT(
    state VARCHAR NOT NULL,
    city VARCHAR,
    street VARCHAR,
    zip_code NUMBER
  );
```
- [f] Use when:
- You want to store metadata.
- You have multiple pieces of data that are identified by strings.
- The information has no natural order.
# ARRAY
- [!] Max size: 128 MB
Each value in a __semi-structured array__ is of type [[#VARIANT]].
+ It grows **dynamically** i.e. **no fixed size arrays**.
+ Attempting to access an element beyond the end of an array returns NULL.
+ We can store data having multiple native data types together (since overarching type is VARIANT).
```sql
CREATE OR REPLACE TABLE array_example (array_column ARRAY);
INSERT INTO array_example (array_column)
  SELECT ARRAY_CONSTRUCT(12, 'twelve', NULL);
  
  -- Using array constants
  -- NULL here is SQL NULL
  INSERT INTO array_example (array_column)
  SELECT [ 12, 'twelve', NULL ];
```

We can have **sparse arrays** i.e some array elements have values and others do not. 
If an index has no corresponding value, the index is said to be _undefined_. i.e. reading the element at an undefined index results in the output `undefined`.
- [!] Undefined $\neq$ NULL
_Undefined_ values take up no space, NULL consumes space.

To store a __structured array__ i.e. all values of single data type:
```sql
-- `NOT NULL` specifies value in array cannot be null
CREATE TABLE my_table_with_structured_array_column (
  numeric_array ARRAY(NUMBER NOT NULL)
);

INSERT INTO my_table_with_structured_array_column SELECT
  [10, 20, 30]::ARRAY(NUMBER)
```
- [f] Use when:
- There is a collection of data, and each piece in the collection is structured the same or similarly.
- Each piece of data is processed similarly. For example, you might loop through the data, processing each piece the same way.
- The data has a natural order, for example, chronological.
# MAP
Store key value pairs.
- [!] Keys must be either [[#VARCHAR]] or [[#NUMBER]] with scale 0. ***NO NULLs*** allowed.
```sql
CREATE OR REPLACE TABLE my_table_with_map_column(
	my_map MAP(VARCHAR, VARCHAR)
);

INSERT INTO my_table_with_map_column SELECT
  {'key123': 'value123'}::MAP(VARCHAR, VARCHAR);
```
# FILE
It is a reference to a file stored in an internal or external stage.
+ Updating underlying file **DOES NOT** change FILE values.
+ Deleting FILE value **DOES NOT** change the underlying file.
It includes the following metadata:

| Identifier      | Purpose                                                                                          | Required |
| --------------- | ------------------------------------------------------------------------------------------------ | -------- |
| STAGE           | Name of the stage in which file resides                                                          | ❌        |
| RELATIVE_PATH   | Relative path of the file in the stage                                                           | ❌        |
| STAGE_FILE_URL  | The stage file URL                                                                               | ❌        |
| SCOPED_FILE_URL | A [scoped](https://docs.snowflake.com/en/sql-reference/functions/build_scoped_file_url) file URL | ❌        |
| CONTENT_TYPE    | The MIME type of the file                                                                        | ✅        |
| SIZE            | The size of the file (in bytes)                                                                  | ✅        |
| ETAG            | A unique hash of the file contents                                                               | ✅        |
| LAST_MODIFIED   | The timestamp at which the file was last modified                                                | ✅        |
>[!important] Either one of (STAGE + RELATIVE_PATH), STAGE_FILE_URL or SCOPED_FILE_URL is required!

```sql
-- my_images is a stage
CREATE TABLE images_table(img FILE);
INSERT INTO images_table
    SELECT TO_FILE(file_url) FROM DIRECTORY(@my_images);
```

Refer [File functions](https://docs.snowflake.com/en/sql-reference/data-types-unstructured#file-functions) for all the utility functions for file data.
## Limitations      
The FILE data type currently cannot be used in:
- CLUSTER BY, GROUP BY, and ORDER BY clauses
- Hybrid tables, Iceberg tables, and external tables
- SnowScript
- Secured views
- Binds
- Search optimization
- Clients and connectors except Snowpark Python
# GEOGRAPHY
- [i] Latitude range: -90 to +90
- [i] Longitude range: -180 to +180
- [i] Supported Formats: WKT, WKB, EWKT, EWKB, GeoJSON 
- [!] Max Scale: 14 decimal places
- [f] Use when:
- Mapping real-world coordinates and you need accurate real-world distances or areas in meters.
```sql
CREATE OR REPLACE TABLE geospatial_table (id INTEGER, g GEOGRAPHY);

INSERT INTO geospatial_table VALUES
  (1, 'POINT(-122.35 37.55)'),
  (2, 'LINESTRING(-124.20 42.00, -120.01 41.99)');
```

When converting strings (WKT, GeoJSON) into `GEOGRAPHY`, Snowflake will __automatically try to repair minor invalidities__, such as fixing unclosed loops, self-intersecting polygon rings, or correcting the orientation of loops so they don't accidentally encompass more than half the globe.
# GEOMETRY
- [!] Max Scale: 14 decimal places
- [i] Default SRID: 0
- [i] Supported Formats: WKT, WKB, EWKT, EWKB, GeoJSON 
It models data on a flat, 2D Cartesian plane and supports any [spatial reference system (SRS)](https://en.wikipedia.org/wiki/Spatial_reference_system) 
- [f] Use when:
- Working with localized mappings (CAD layouts, floor plans, game maps) where the curvature of the earth is irrelevant. 
It __does not support automatic shape repairs__. If a shape is mathematically invalid on a flat grid (like a self-intersecting polygon), the conversion will fail immediately.
# UUID
128 bit binary value that uniquely identifies information.
	UUID data type itself __doesn't guarantee uniqueness__, since Snowflake allows insertion of same UUID multiple times.
They are treated as text strings are the values are __case-insensitive__.
```sql
CREATE OR REPLACE TABLE sample_generate_uuid (
  id UUID DEFAULT UUID_STRING() NOT NULL,
  sample_column VARCHAR);
```
## Limitations
+ Can't be stored in a semi-structured or structured data type.
+ Can't be used in stored procedures or UDFs (except SQL UDF).
+ Cant be used in hybrid tables
+ Not supported in Snowpark.
# VECTOR
Encode and process vectors efficiently.
Supports semantic vector search, retrieval applications and common operations on vectors.
```sql
-- Syntax VECTOR(type, dimension)
-- type can be float/int
-- dimension is length of the vector (1 to 4096)
CREATE OR REPLACE TABLE myvectortable (
a VECTOR(float, 3), 
b VECTOR(float, 3)
);

COPY INTO @mystage/unload/
  FROM (SELECT TO_ARRAY(a), TO_ARRAY(b) FROM myvectortable);
```
## Limitations
+ Only supported in SQL, Snowpark Python library and the Python connector.
+ Cannot be used as clustering keys.
+ Not supported in [[#VARIANT]] columns (underlying data type).
+ Cannot be used as primary/secondary key in hybrid tables.
+ Directly loading and unloading a VECTOR column is not supported. It must be cast to [[#ARRAY]] for loading/unloading, and then to VECTOR for usage.
```sql
COPY INTO @mystage/unload/
  FROM (SELECT TO_ARRAY(a), TO_ARRAY(b) FROM myvectortable);
```
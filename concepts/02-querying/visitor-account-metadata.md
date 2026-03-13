`VISITORMETADATA`
```sql
SELECT
    v.id             -- :: VARCHAR
  , m.group          -- :: VARCHAR
  , m.name           -- :: VARCHAR
  , m.displayName    -- :: VARCHAR
  , m.type           -- :: VARCHAR
  , m.elementType    -- :: VARCHAR
  , m.elementFormat  -- :: VARCHAR
  , m.isDeleted      -- :: BOOLEAN
  , m.isPerApp       -- :: BOOLEAN
  , JSON_VALUE(      -- :: VARIANT
      TO_JSON_STRING(v),
      CONCAT('$.', m.avroFieldName)
    ) AS value
FROM
  visitors.avro AS v
CROSS JOIN
  metadataschema_visitors.avro AS m
WHERE
    JSON_VALUE(TO_JSON_STRING(v), CONCAT('$.', m.avroFieldName)) IS NOT NULL
;
```

`ACCOUNTMETADATA`
```sql
SELECT
    a.id             -- :: VARCHAR
  , m.group          -- :: VARCHAR
  , m.name           -- :: VARCHAR
  , m.displayName    -- :: VARCHAR
  , m.type           -- :: VARCHAR
  , m.elementType    -- :: VARCHAR
  , m.elementFormat  -- :: VARCHAR
  , m.isDeleted      -- :: BOOLEAN
  , m.isPerApp       -- :: BOOLEAN
  , JSON_VALUE(      -- :: VARIANT
      TO_JSON_STRING(a),
      CONCAT('$.', m.avroFieldName)
    ) AS value
FROM
  accounts.avro AS a
CROSS JOIN
  metadataschema_accounts.avro AS m
WHERE
    JSON_VALUE(TO_JSON_STRING(a), CONCAT('$.', m.avroFieldName)) IS NOT NULL
;
```
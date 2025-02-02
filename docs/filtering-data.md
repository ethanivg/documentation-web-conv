# Filters

Filters provide SQL-like operations for working with data streams. You can use familiar concepts like equals, greater than, less than, and combine them with AND/OR operations - just like you would in a SQL WHERE clause. This gives you the power of SQL filtering even when working with non-SQL data sources. For example, you can easily filter records where age > 18 AND status = 'active', or find all users who are either admins OR moderators.

## Types of Filters

| Symbol   | Description                                         |
| -------- | --------------------------------------------------- |
| `Filter` | Base interface for filter evaluation and conditions |
| `&&`     | Combines filters with AND logic                     |
| `\|\|`   | Combines filters with OR logic                      |
| `!=`     | Inverts filter result                               |
| `==`     | Coercive equality comparison                        |
| `===`    | Strict equality comparison                          |
| `>`      | Greater than comparison                             |
| `>=`     | Greater than or equal comparison                    |
| `<`      | Less than comparison                                |
| `<=`     | Less than or equal comparison                       |

## Examples

### Basic Less than

The below filter returns true because the record value is above 18.

```java
String key = "age";
String value = 18;

JSONObject record = new JSONObject("{\"age\": 5}");
return new LessThanFilter(key, value).test(record);
```

## Interface Examples

### Filter

The `Filter` interface extends `Transformer`, allowing you to quickly create filters to use on groups of `JSONObjects`.

```java
File file = new File("./clientData.json");
FileSource source = new FileSource(file);
Iterator<JSONObject> records = new JSONInput().read(source);

String key = "name";
String find = "Smith";

Filter nameFilter = new Filter()
{
    @Override
    public boolean test(JSONObject record)
    {
        return record.get(key).contains(find);
    }
};

Iterator<JSONObject> filtered = nameFilter.transform(records);
```

### Comparitive

The below example uses a customer `ComparatorFilter` to collect JSONObjects that are considered 'old'.

```java
DBMS database = new DBMS(source);

String search = "Select last_update FROM customer";

ComparatorFilter dateFilter = new ComparatorFilter()
{
    @Override
    public boolean test(JSONObject record)
    {
        Date otherDate = new SimpleDateFormat("yyyy-MM-dd").parse(record.get(getKey()));
        Date currentDate = new SimpleDateFormat("yyyy-MM-dd").parse(getValue());

        return currentDate.after(otherDate);
    }
};

Iterable<JSONObject> old = database.query(new Query(search));

JSONArray updatedItems = new JSONArray();

dateFilter.setKey("last_update");
dateFilter.setValue("2007-01-01");

for(JSONObject oldRecord : old)
{
    if(dateFilter.test(oldRecord))
    {
        updatedItems.put(oldRecord);
        continue;
    }
}
```

## Best Practices

- Combine filters with `AndFilter` and `OrFilter` to define complex filtering criteria.
- Use `NotFilter` to exclude records based on specific conditions.
- When working with numeric or comparable fields, leverage `ComparatorFilter` subclasses like `GreaterThanFilter` for concise evaluations.

## Further Reading

<div style="display: flex; align-items: center; gap: 8px; margin-bottom: 16px">
  <span style="display: flex; align-items: center; justify-content: center;font-size:20px; width: 24px; height: 24px">📚</span>
  <a href="https://docs.invirgance.com/javadocs/convirgance/latest/com/invirgance/convirgance/transform/filter/package-summary.html">Java Documentation: Filters</a>
</div>

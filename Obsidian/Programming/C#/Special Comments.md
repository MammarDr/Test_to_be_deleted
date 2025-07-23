
```csharp

/// <summary>
/// Serializes objects into a custom JSON-like format.
/// </summary>
public class MySerializer
{

/// <summary>
/// Creates a serializer instance for the specified type, unless it is marked as non-serializable.
/// </summary>
/// <param name="type">The type to be serialized.</param>
/// <returns>
/// A <see cref="MySerializer"/> instance if the type is not null and not marked with <c>[MyNonSerializable]</c>;
/// otherwise, <c>null</c>.
/// </returns>
static public MySerializer CreateInstance(Type type)
{
    if (type == null || type.IsDefined(typeof(MyNonSerializableAttribute), inherit: false))
        return null;

    return new MySerializer(type);
}

```
# C# Style Guide

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li><a href="#pascal-case">Pascal case</a></li>
    <li><a href="#camel-case">Camel case</a></li>
    <li><a href="#string-data-type">String data type</a></li>
    <li><a href="#Implicitly-typed-local-variables">Implicitly typed local variables</a></li>
    <li><a href="#mysql-migrations">MySQL Migrations</a></li>
    <li><a href="#data-model-conventions">Data model conventions</a></li>
  </ol>
</details>

<br></br>

## Naming conventions

### Pascal case
```C#
public class DataService
{
}
```

```C#
public record PhysicalAddress(
    string Street,
    string City,
    string StateOrProvince,
    string ZipCode);
```

```C#
public struct ValueCoordinate
{
}
```

When naming an interface, use pascal casing in addition to prefixing the name with an I. This clearly indicates to consumers that it's an interface.


```C#
public interface IWorkerQueue
{
}
```
When naming public members of types, such as fields, properties, events, methods, and local functions, use pascal casing.
```C#
public class ExampleEvents
{
    // A public field, these should be used sparingly
    public bool IsValid;

    // An init-only property
    public IWorkerQueue WorkerQueue { get; init; }

    // An event
    public event Action EventProcessing;

    // Method
    public void StartEventProcessing()
    {
        // Local function
        static int CountQueueItems() => WorkerQueue.Count;
        // ...
    }
}
```
When writing positional records, use pascal casing for parameters as they're the public properties of the record.
```C#
public record PhysicalAddress(
    string Street,
    string City,
    string StateOrProvince,
    string ZipCode);
```
<br></br>
### Camel case
Use camel casing ("camelCasing") when naming private or internal fields, and prefix them with _.
```C#
public class DataService
{
    private IWorkerQueue _workerQueue;
}
```

When working with static fields that are private or internal, use the s_ prefix and for thread static use t_.
```C#
public class DataService
{
    private static IWorkerQueue s_workerQueue;

    [ThreadStatic]
    private static TimeSpan t_timeSpan;
}
```

When writing method parameters, use camel casing.
```C#
public T SomeMethod<T>(int someNumber, bool isValid)
{
}
```
<br></br>

## String data type
Use string interpolation to concatenate short strings, as shown in the following code.
```C#
string displayName = $"{nameList[n].LastName}, {nameList[n].FirstName}";
```

To append strings in loops, especially when you're working with large amounts of text, use a StringBuilder object.
```C#
var phrase = "lalalalalalalalalalalalalalalalalalalalalalalalalalalalalala";
var manyPhrases = new StringBuilder();
for (var i = 0; i < 10000; i++)
{
    manyPhrases.Append(phrase);
}
//Console.WriteLine("tra" + manyPhrases);
```

<br></br>

## Implicitly typed local variables
Use implicit typing for local variables when the type of the variable is obvious from the right side of the assignment, or when the precise type is not important.

```C#
var var1 = "This is clearly a string.";
var var2 = 27;
```
Don't use var when the type is not apparent from the right side of the assignment. Don't assume the type is clear from a method name. A variable type is considered clear if it's a new operator or an explicit cast.
```C#
int var3 = Convert.ToInt32(Console.ReadLine()); 
int var4 = ExampleClass.ResultSoFar();
```
Don't rely on the variable name to specify the type of the variable. It might not be correct. In the following example, the variable name inputInt is misleading. It's a string.

```C#
var inputInt = Console.ReadLine();
Console.WriteLine(inputInt);
```
Avoid the use of var in place of dynamic. Use dynamic when you want run-time type inference. For more information, see Using type dynamic (C# Programming Guide).

Use implicit typing to determine the type of the loop variable in for loops.

The following example uses implicit typing in a for statement.
```C#
var phrase = "lalalalalalalalalalalalalalalalalalalalalalalalalalalalalala";
var manyPhrases = new StringBuilder();
for (var i = 0; i < 10000; i++)
{
    manyPhrases.Append(phrase);
}
//Console.WriteLine("tra" + manyPhrases);
```

Don't use implicit typing to determine the type of the loop variable in foreach loops. In most cases, the type of elements in the collection isn't immediately obvious. The collection's name shouldn't be solely relied upon for inferring the type of its elements.

The following example uses explicit typing in a foreach statement.
```C#
foreach (char ch in laugh)
{
    if (ch == 'h')
        Console.Write("H");
    else
        Console.Write(ch);
}
Console.WriteLine();
```

<br></br>

## MySQL Migrations
Declare primary key, foreign keys and other properties in this order

In DBContext,
```C#
modelBuilder.Entity<Client>(entity =>
{
    entity.ToTable("clients")
        .HasKey(e => e.Id);

    entity.Property(e => e.Id)
        .HasColumnName("id");

    entity.Property(e => e.ClientAuthId)
        .HasColumnName("client_auth_id")
        .HasColumnType("varchar(124)");

    entity.Property(e => e.Name)
        .HasColumnName("name")
        .HasColumnType("varchar(124)");

    entity.Property(e => e.Status)
        .HasColumnName("status")
        .HasColumnType("enum('active','deactivated')")
        .HasDefaultValue("active");

    entity.Property(e => e.CreatedAt)
        .HasColumnName("created_at");

    entity.Property(e => e.UpdatedAt)
        .HasColumnName("updated_at");

    entity.HasIndex(e => new { e.ClientAuthId })
        .HasDatabaseName("clients_auth_id_index")
        .IsUnique();
});
```

## Data model conventions
- Add comments for each property
- Declare primary key, foreign key and other properties in this order
- Declare ? for nullable properties with datatypes of string and add `= default!`


```C#
public class ClientCallback
{
    /// <summary>
    /// Gets or sets the client callback id.
    /// </summary>
    public int Id { get; set; }

    /// <summary>
    /// Gets or sets the client id.
    /// </summary>
    public int ClientId { get; set; }

    /// <summary>
    /// Gets or sets the callback type.
    /// </summary>
    public string Type { get; set; } = ClientCallbackConstants.CallbackType.Disbursement;

    /// <summary>
    /// Gets or sets the callback url.
    /// </summary>
    public string CallbackUrl { get; set; } = default!;

    public virtual Client Client { get; set; } = default!;
}
```

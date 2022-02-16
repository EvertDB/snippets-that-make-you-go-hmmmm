# snippets-that-make-you-go-hmmmm
Some snippets that might be interesting

Unlike the [Things That Make You Go Hmmmm](https://www.youtube.com/watch?v=XF2ayWcJfxo) (provided by C&C Music Factory more than 30 years ago), here are some **code snippets** that might be of interest to you.

Care to correct or add one? Please start a pull request!

Thx!  
EvertDB

## Snippets

### Custom DB Schema

Framework | Language | Location | Purpose
-|-|-|-
Entity Framework | C# | Context class (typically `/Data/...Context.cs`) | Use a custom DB schema (instead of `dbo`)

```Csharp
public class CustomDbSchemaContext : DbContext
{
  ...
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.HasDefaultSchema("Enter schema name here");

        base.OnModelCreating(modelBuilder);
    }
  ...
}
```

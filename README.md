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

```cs
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

### Editable DateTime

Framework | Language | Location | Purpose
-|-|-|-
ASP.NET MVC 5 | Razor (C#) | /Views/Shared/EditorTemplates/DateTime.cshtml | Solution according to standards for date/time editable control (in non-English notations).

Editing date/time fields in an ASP.NET MVC 5 web page might be a hassle. Using a custom editor template can solve this problem at once (and in line with standards), without the need for further modifications.

Create a file `DateTime.cshtml` in `/Views/Shared/EditorTemplates` with the following contents:

```cs
@model DateTime?

@{ 
    var htmlAttrib = ViewData["htmlAttributes"];
    IDictionary<string, object> dic = new RouteValueDictionary(htmlAttrib);
    var metadata = ViewData.ModelMetadata;
    string id = ViewData.TemplateInfo.HtmlFieldPrefix;
    var value = ((DateTime?)Model)?.ToString("yyyy-MM-ddTHH:mm");
    var classes = dic["class"];
    var readOnlyHtml = dic["readonly"];
    string required = metadata.IsRequired ? "required=required" : "";
    string readOnly = (readOnlyHtml != null || metadata.IsReadOnly) ? $"readonly={readOnlyHtml ?? "readOnly"}" : "";
}
<input id="@id" name="@id" value="@value" type="datetime-local" class="@classes" @required @readOnly  />
```

Note: this file is available [here](https://github.com/EvertDB/snippets-that-make-you-go-hmmmm/blob/main/ASP.NET%20MVC/EditorTemplates/DateTime.cshtml).

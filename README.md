# snippets-that-make-you-go-hmmmm
Some snippets that might be interesting

Unlike the [Things That Make You Go Hmmmm](https://www.youtube.com/watch?v=XF2ayWcJfxo) (provided by C&C Music Factory more than 30 years ago), here are some **code snippets** that might be of interest to you.

Care to correct or add one? Please start a pull request!

Thx!  
EvertDB

## Snippets

Snippet | Purpose | Framework | Language | Location 
-|-|-|-|-
**[Custom DB Schema](./README.md#custom-db-schema)**           | Context class (typically `/Data/...Context.cs`) | Use a custom DB schema (instead of `dbo`).  | Entity Framework | C#         
**[Editable DateTime](./README.md#editable-datetime)**         | Solution according to standards for date/time editable control (in non-English notations).  | ASP.NET MVC 5    | Razor (C#) | /Views/Shared/EditorTemplates/DateTime.cshtml 
**[RadioButtons for enum](./README.md#radiobuttons-for-enum)** | Create a list of radio buttons for an enumeration.  | ASP.NET MVC 5    | Razor (C#) | /Views/Shared/EditorTemplates/RadioButtonsForEnum.cshtml 

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

### Radiobuttons for enum

Framework | Language | Location | Purpose
-|-|-|-
ASP.NET MVC 5 | Razor (C#) | /Views/Shared/EditorTemplates/RadioButtonsForEnum.cshtml | Create a list of radio buttons for an enumeration.

Elegant solution for generating radio buttons for the values of an enum. It takes the `Display()` attribute into account.

Create a file `RadioButtonsForEnum.cshtml` in `/Views/Shared/EditorTemplates` with the following contents:

```cs
@model Enum

@{
     // Looks for a [Display(Name="Some Name")] or a [Display(Name="Some Name", ResourceType=typeof(ResourceFile)] Attribute on your enum
    Func<Enum, string> getDescription = en =>
    {
        Type type = en.GetType();
        System.Reflection.MemberInfo[] memInfo = type.GetMember(en.ToString());

        if (memInfo != null && memInfo.Length > 0)
        {
            object[] attrs = memInfo[0].GetCustomAttributes(typeof(System.ComponentModel.DataAnnotations.DisplayAttribute),
                                                            false);
            if (attrs != null && attrs.Length > 0)
                return ((System.ComponentModel.DataAnnotations.DisplayAttribute)attrs[0]).GetName();
        }
        return en.ToString();
    };
    var listItems = Enum.GetValues(Model.GetType()).OfType<Enum>().Select(e =>
    new SelectListItem()
    {
        Text = getDescription(e),
        Value = e.ToString(),
        Selected = e.Equals(Model)
    });
    string prefix = ViewData.TemplateInfo.HtmlFieldPrefix;
    int index = 0;
    ViewData.TemplateInfo.HtmlFieldPrefix = string.Empty;

    foreach (var li in listItems)
    {
        string fieldName = string.Format(System.Globalization.CultureInfo.InvariantCulture, "{0}_{1}", prefix, index++);
        <div class="editor-radio">
        @Html.RadioButton(prefix, li.Value, li.Selected, new { @id = fieldName }) 
        @Html.Label(fieldName, li.Text)    
        </div>
    }
    ViewData.TemplateInfo.HtmlFieldPrefix = prefix;
}
```

How to use?

```csharp
// Suppose the model has a property `EnumPropertyGoesHere` that is an enum type
@Html.EditorFor(x => x.EnumPropertyGoesHere, "RadioButtonsForEnum")
```

[Credits](https://stackoverflow.com/questions/21679249/mvc5-enum-radio-button-with-label-as-displayname#21680307)  
Note: this file is available [here](https://github.com/EvertDB/snippets-that-make-you-go-hmmmm/blob/main/ASP.NET%20MVC/EditorTemplates/RadioButtonsForEnum.cshtml).

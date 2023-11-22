
Forked from spark-dotnet/ui

This fork of spark.ui is an experiment in building Tailwind components for Blazor.
I want a component library that is completely themable is a .NET-centric kind of way.

First, a CSS builder **service** will be injected into every component.  
The CSS bulder service is invoked in the base components' OnParameterSet method...
```
<button class="@CssClasses"/>
@code {
[Inject]
CssBuilder<Button> CssBuilder { get; set; }
protected override void OnParametersSet()
{
    CssClasses = CssBuilder(this, Styles).Build();
}
}
```
The Styles parameter is a function parameter that accepts a CSSBuilder<T> that enables Tailwind styles to be addedd to individual components.
For instance, instead of a hardcoded 'Variant' property, you add a 'Variant' style to the styles.
Example, instead of this...
```
<ButtonLink href="/" Variant="ButtonVariant.Default">Link</ButtonLink>
```
do this...
```
<ButtonLink Styles=@(btnStyles => btnStyles.Add<ButtonVariant<Default>>())>Link</ButtonLink>
```
What the above is doing is adding an instance of a type that we've defined, ButtonVariant<Default>, to the style builder (make them struct types).  
Developers can define their own style types, can be anything.
Essentially developers can create thier own design languages.

Using extensions methods we can create fluent styles, like so...
```
<ButtonLink Styles=@(_ => _.Variant().Default())>Link</ButtonLink>
```

Next, create an API for registering functions that are called when the CssBuilder.Build method is invoked.  
These functions emit css classes based on the values added to the css builder and the values of compoennt parameters
```
  services.AddSparkUI(sparkBuilder => {
    sparkBuilder.BuildStyles<ButtonLink>(btnStyles => {
      btnStyles.Add(_ => "h-10 px-4 py-2 inline-flex items-center justify-center rounded-md text-sm font-medium");
      btnStyles.Add(_ => "ring-offset-background transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2");
      btnStyles.Add(_ => "disabled:pointer-events-none disabled:opacity-50");
      btnStyles.WhenExists<ButtonVariant<Default>>().Add(_ => "bg-primary text-primary-foreground hover:bg-primary/90");
      btnStyles.When(_ => _.Component.IsDisabled == true, _ => "blah bg-whatever sumpin hover:goodness");
    });
  });
```


---------------------------------------------

# spark.ui
Beautiful Blazor components made with Tailwind CSS.

## How to use
spark.ui components are meant to be copy and pasted into your Blazor project. This way you own the code and customize them as you see fit.

## Install spark.ui with Blazor or Spark.NET

Check out the docs for installation [instructions](https://ui.spark-framework.net/docs/installation)

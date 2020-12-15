Les composants imbriqués lient généralement les données à l’aide d’une *liaison chaînée* , comme décrit dans <xref:blazor/components/data-binding> . Les composants imbriqués et non imbriqués peuvent partager l’accès aux données à l’aide d’un conteneur d’État en mémoire enregistré. Une classe de conteneur d’état personnalisée peut utiliser un assignable <xref:System.Action> pour notifier des composants dans différentes parties de l’application des modifications d’État. Dans l’exemple suivant :

* Une paire de composants utilise un conteneur d’État pour effectuer le suivi d’une propriété.
* Les composants de l’exemple sont imbriqués, mais l’imbrication n’est pas nécessaire pour que cette approche fonctionne.

`StateContainer.cs`:

```csharp
public class StateContainer
{
    public string Property { get; set; } = "Initial value from StateContainer";

    public event Action OnChange;

    public void SetProperty(string value)
    {
        Property = value;
        NotifyStateChanged();
    }

    private void NotifyStateChanged() => OnChange?.Invoke();
}
```

In `Program.Main` (The éblouissant Webassembly) :

```csharp
builder.Services.AddSingleton<StateContainer>();
```

Dans `Startup.ConfigureServices` (serveur éblouissant) :

```csharp
services.AddSingleton<StateContainer>();
```

`Pages/Component1.razor`:

```razor
@page "/Component1"
@inject StateContainer StateContainer
@implements IDisposable

<h1>Component 1</h1>

<p>Component 1 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 1</button>
</p>

<Component2 />

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 1 {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

`Shared/Component2.razor`:

```razor
@inject StateContainer StateContainer
@implements IDisposable

<h2>Component 2</h2>

<p>Component 2 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 2</button>
</p>

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 2 {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

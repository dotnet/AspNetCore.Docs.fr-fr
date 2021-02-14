---
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.openlocfilehash: 76dbf3cae1c264fa474101bc4398da28f45a1c10
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100254370"
---
<span data-ttu-id="a4c7c-101">Les composants imbriqués lient généralement les données à l’aide d’une *liaison chaînée* , comme décrit dans <xref:blazor/components/data-binding> .</span><span class="sxs-lookup"><span data-stu-id="a4c7c-101">Nested components typically bind data using *chained bind* as described in <xref:blazor/components/data-binding>.</span></span> <span data-ttu-id="a4c7c-102">Les composants imbriqués et non imbriqués peuvent partager l’accès aux données à l’aide d’un conteneur d’État en mémoire enregistré.</span><span class="sxs-lookup"><span data-stu-id="a4c7c-102">Nested and un-nested components can share access to data using a registered in-memory state container.</span></span> <span data-ttu-id="a4c7c-103">Une classe de conteneur d’état personnalisée peut utiliser un assignable <xref:System.Action> pour notifier des composants dans différentes parties de l’application des modifications d’État.</span><span class="sxs-lookup"><span data-stu-id="a4c7c-103">A custom state container class can use an assignable <xref:System.Action> to notify components in different parts of the app of state changes.</span></span> <span data-ttu-id="a4c7c-104">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="a4c7c-104">In the following example:</span></span>

* <span data-ttu-id="a4c7c-105">Une paire de composants utilise un conteneur d’État pour effectuer le suivi d’une propriété.</span><span class="sxs-lookup"><span data-stu-id="a4c7c-105">A pair of components uses a state container to track a property.</span></span>
* <span data-ttu-id="a4c7c-106">Les composants de l’exemple sont imbriqués, mais l’imbrication n’est pas nécessaire pour que cette approche fonctionne.</span><span class="sxs-lookup"><span data-stu-id="a4c7c-106">The components of the example are nested, but nesting isn't required for this approach to work.</span></span>

<span data-ttu-id="a4c7c-107">`StateContainer.cs`:</span><span class="sxs-lookup"><span data-stu-id="a4c7c-107">`StateContainer.cs`:</span></span>

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

<span data-ttu-id="a4c7c-108">Dans `Program.Main` ( Blazor WebAssembly ) :</span><span class="sxs-lookup"><span data-stu-id="a4c7c-108">In `Program.Main` (Blazor WebAssembly):</span></span>

```csharp
builder.Services.AddSingleton<StateContainer>();
```

<span data-ttu-id="a4c7c-109">Dans `Startup.ConfigureServices` ( Blazor Server ) :</span><span class="sxs-lookup"><span data-stu-id="a4c7c-109">In `Startup.ConfigureServices` (Blazor Server):</span></span>

```csharp
services.AddSingleton<StateContainer>();
```

<span data-ttu-id="a4c7c-110">`Pages/Component1.razor`:</span><span class="sxs-lookup"><span data-stu-id="a4c7c-110">`Pages/Component1.razor`:</span></span>

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
        StateContainer.SetProperty($"New value set in Component 1: {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

<span data-ttu-id="a4c7c-111">`Shared/Component2.razor`:</span><span class="sxs-lookup"><span data-stu-id="a4c7c-111">`Shared/Component2.razor`:</span></span>

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
        StateContainer.SetProperty($"New value set in Component 2: {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

<span data-ttu-id="a4c7c-112">Les composants précédents implémentent <xref:System.IDisposable> , et les `OnChange` délégués sont désabonnés dans les `Dispose` méthodes, qui sont appelées par le Framework quand les composants sont supprimés.</span><span class="sxs-lookup"><span data-stu-id="a4c7c-112">The preceding components implement <xref:System.IDisposable>, and the `OnChange` delegates are unsubscribed in the `Dispose` methods, which are called by the framework when the components are disposed.</span></span> <span data-ttu-id="a4c7c-113">Pour plus d’informations, consultez <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="a4c7c-113">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

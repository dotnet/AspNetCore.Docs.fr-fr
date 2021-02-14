---
title: BlazorLiaison de données ASP.net Core
author: guardrex
description: En savoir plus sur les fonctionnalités de liaison de données pour les composants et les éléments DOM dans les Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 76cc0ddc46dd08a5b8b88cf6045b84beab57c295
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280133"
---
# <a name="aspnet-core-blazor-data-binding"></a>BlazorLiaison de données ASP.net Core

Razor les composants fournissent des fonctionnalités de liaison de données via un attribut d’élément HTML nommé [`@bind`](xref:mvc/views/razor#bind) avec une valeur de champ, de propriété ou d' Razor expression.

L’exemple suivant lie un `<input>` élément au `currentValue` champ et un `<input>` élément à la `CurrentValue` propriété :

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

Lorsque l’un des éléments perd le focus, le champ ou la propriété liés est mis à jour.

La zone de texte est mise à jour dans l’interface utilisateur uniquement lorsque le composant est rendu, et non en réponse à la modification de la valeur du champ ou de la propriété. Étant donné que les composants s’affichent après l’exécution du code du gestionnaire d’événements, les mises à jour des champs et des propriétés sont *généralement* reflétées dans l’interface utilisateur immédiatement après le déclenchement d’un gestionnaire d’événements.

L’utilisation [`@bind`](xref:mvc/views/razor#bind) de avec la `CurrentValue` propriété ( `<input @bind="CurrentValue" />` ) équivaut essentiellement à ce qui suit :

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

Lors du rendu du composant, le `value` de l’élément d’entrée provient de la `CurrentValue` propriété. Lorsque l’utilisateur tape dans la zone de texte et modifie le focus de l’élément, l' `onchange` événement est déclenché et la `CurrentValue` propriété est définie sur la valeur modifiée. En réalité, la génération de code est plus complexe que cela car [`@bind`](xref:mvc/views/razor#bind) gère les cas où les conversions de type sont effectuées. En principe, [`@bind`](xref:mvc/views/razor#bind) associe la valeur actuelle d’une expression à un `value` attribut et gère les modifications à l’aide du gestionnaire inscrit.

Liez une propriété ou un champ à d’autres événements en incluant également un `@bind:event` attribut avec un `event` paramètre. L’exemple suivant lie la `CurrentValue` propriété à l' `oninput` événement :

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Contrairement à `onchange` , qui se déclenche lorsque l’élément perd le focus, `oninput` se déclenche lorsque la valeur de la zone de texte change.

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

La liaison d’attribut respecte la casse :

* `@bind` est valide.
* `@Bind` et `@BIND` ne sont pas valides.

## <a name="unparsable-values"></a>Valeurs inanalysables

Quand un utilisateur fournit une valeur non analysable à un élément DataBound, la valeur unanalysable est automatiquement rétablie à sa valeur précédente lorsque l’événement de liaison est déclenché.

Examinez le cas suivant :

* Un `<input>` élément est lié à un `int` type dont la valeur initiale est `123` :

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* L’utilisateur met à jour la valeur de l’élément à `123.45` dans la page et modifie le focus de l’élément.

Dans le scénario précédent, la valeur de l’élément est rétablie à `123` . Lorsque la valeur `123.45` est rejetée en faveur de la valeur d’origine de `123` , l’utilisateur sait que sa valeur n’a pas été acceptée.

Par défaut, la liaison s’applique à l’événement de l’élément `onchange` ( `@bind="{PROPERTY OR FIELD}"` ). Utilisez `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` pour déclencher la liaison sur un événement différent. Pour l' `oninput` événement ( `@bind:event="oninput"` ), la réversion se produit après toute séquence de touches qui introduit une valeur non analysable. Lorsque vous ciblez l' `oninput` événement avec un `int` type lié, un utilisateur ne peut pas taper un `.` caractère. Un `.` caractère est immédiatement supprimé, de sorte que l’utilisateur reçoit des commentaires immédiats que seuls des nombres entiers sont autorisés. Il existe des scénarios dans lesquels le rétablissement de la valeur de l' `oninput` événement n’est pas idéal, par exemple lorsque l’utilisateur doit être autorisé à effacer une valeur non analysable `<input>` . Les alternatives sont les suivantes :

* N’utilisez pas l' `oninput` événement. Utilisez l’événement par défaut `onchange` (spécifiez uniquement `@bind="{PROPERTY OR FIELD}"` ), où une valeur non valide n’est pas rétablie tant que l’élément n’a pas perdu le focus.
* Effectuer une liaison à un type Nullable, tel que `int?` ou, `string` et fournir une logique personnalisée pour gérer les entrées non valides.
* Utilisez un [composant de validation de formulaire](xref:blazor/forms-validation), tel que <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> ou <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> . Les composants de validation de formulaire offrent une prise en charge intégrée pour gérer les entrées non valides. Pour plus d’informations, consultez <xref:blazor/forms-validation>. Composants de validation de formulaire :
  * Permet à l’utilisateur de fournir une entrée non valide et de recevoir des erreurs de validation sur le associé <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .
  * Affichez les erreurs de validation dans l’interface utilisateur sans interférer avec l’utilisateur qui saisit des données Webform supplémentaires.

## <a name="format-strings"></a>Chaînes de format

La liaison de données fonctionne avec les <xref:System.DateTime> chaînes de format à l’aide de `@bind:format` . D’autres expressions de mise en forme, telles que les formats de devise ou de nombre, ne sont pas disponibles pour l’instant.

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

Dans le code précédent, le `<input>` type de champ de l’élément ( `type` ) est défini par défaut sur `text` . `@bind:format` est pris en charge pour la liaison des types .NET suivants :

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

L' `@bind:format` attribut spécifie le format de date à appliquer au `value` de l' `<input>` élément. Le format est également utilisé pour analyser la valeur lorsqu’un `onchange` événement se produit.

La spécification d’un format pour le `date` type de champ n’est pas recommandée, car Blazor dispose d’une prise en charge intégrée pour mettre en forme les dates. Malgré la recommandation, utilisez uniquement le `yyyy-MM-dd` format de date pour que la liaison fonctionne correctement si un format est fourni avec le `date` type de champ :

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="binding-with-component-parameters"></a>Liaison avec des paramètres de composant

Un scénario courant consiste à lier une propriété dans un composant enfant à une propriété dans son parent. Ce scénario est appelé *liaison chaînée* car plusieurs niveaux de liaison se produisent simultanément.

Les [paramètres de composant](xref:blazor/components/index#component-parameters) autorisent les propriétés de liaison d’un composant parent avec la `@bind-{PROPERTY}` syntaxe.

Les liaisons chaînées ne peuvent pas être implémentées avec [`@bind`](xref:mvc/views/razor#bind) la syntaxe dans le composant enfant. Un gestionnaire d’événements et une valeur doivent être spécifiés séparément pour prendre en charge la mise à jour de la propriété dans le parent à partir du composant enfant.

Le composant parent utilise toujours la [`@bind`](xref:mvc/views/razor#bind) syntaxe pour configurer la liaison de données avec le composant enfant.

Le `Child` composant suivant ( `Shared/Child.razor` ) a un `Year` paramètre de composant et un `YearChanged` Rappel :

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

<button @onclick="UpdateYearFromChild">Update Year from Child</button>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private async Task UpdateYearFromChild()
    {
        await YearChanged.InvokeAsync(r.Next(1950, 2021));
    }
}
```

Le rappel ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ) doit être nommé en tant que nom de paramètre de composant suivi du `Changed` suffixe «» ( `{PARAMETER NAME}Changed` ). Dans l’exemple précédent, le rappel est nommé `YearChanged` . <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> appelle le délégué associé à la liaison avec l’argument fourni et distribue une notification d’événement pour la propriété modifiée.

Dans le `Parent` composant suivant ( `Parent.razor` ), le `year` champ est lié au `Year` paramètre du composant enfant :

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

Le `Year` paramètre peut être lié, car il a un `YearChanged` événement auxiliaire qui correspond au type du `Year` paramètre.

Par Convention, une propriété peut être liée à un gestionnaire d’événements correspondant en incluant un `@bind-{PROPERTY}:event` attribut assigné au gestionnaire. `<Child @bind-Year="year" />` équivaut à écrire :

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

Dans un exemple plus sophistiqué et réaliste, le `PasswordField` composant suivant ( `PasswordField.razor` ) :

* Définit `<input>` la valeur d’un élément sur `password` un champ.
* Expose les modifications d’une `Password` propriété à un composant parent avec un [`EventCallback`](xref:blazor/components/event-handling#eventcallback) qui passe la valeur actuelle du champ de l’enfant `password` comme argument.
* Utilise l' `onclick` événement pour déclencher la `ToggleShowPassword` méthode. Pour plus d’informations, consultez <xref:blazor/components/event-handling>.

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private async Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        await PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

Le `PasswordField` composant est utilisé dans un autre composant :

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Effectue des vérifications ou des erreurs d’interruption dans la méthode qui appelle le délégué de la liaison. L’exemple suivant fournit un retour immédiat à l’utilisateur si un espace est utilisé dans la valeur du mot de passe :

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

Pour plus d'informations sur le <xref:Microsoft.AspNetCore.Components.EventCallback%601>, consultez <xref:blazor/components/event-handling#eventcallback>.

## <a name="bind-across-more-than-two-components"></a>Liaison entre plus de deux composants

Vous pouvez lier n’importe quel nombre de composants imbriqués, mais vous devez respecter le déroulement unidirectionnel des données :

* Les notifications de modifications sont *transmises dans la hiérarchie*.
* Les nouvelles valeurs de paramètre *circulent dans la hiérarchie*.

Une approche courante et recommandée consiste à stocker uniquement les données sous-jacentes dans le composant parent afin d’éviter toute confusion quant à l’État qui doit être mis à jour.

Les composants suivants illustrent les concepts précédents :

`ParentComponent.razor`:

```razor
<h1>Parent Component</h1>

<p>Parent Message: <b>@parentMessage</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-ChildMessage="parentMessage" />

@code {
    private string parentMessage = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentMessage = $"Set in Parent {DateTime.Now}";
    }
}
```

`ChildComponent.razor`:

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Message: <b>@ChildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-GrandchildMessage="BoundValue" />
</div>

@code {
    [Parameter]
    public string ChildMessage { get; set; }

    [Parameter]
    public EventCallback<string> ChildMessageChanged { get; set; }

    private string BoundValue
    {
        get => ChildMessage;
        set => ChildMessageChanged.InvokeAsync(value);
    }

    private async Task ChangeValue()
    {
        await ChildMessageChanged.InvokeAsync(
            $"Set in Child {DateTime.Now}");
    }
}
```

`GrandchildComponent.razor`:

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Message: <b>@GrandchildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string GrandchildMessage { get; set; }

    [Parameter]
    public EventCallback<string> GrandchildMessageChanged { get; set; }

    private async Task ChangeValue()
    {
        await GrandchildMessageChanged.InvokeAsync(
            $"Set in Grandchild {DateTime.Now}");
    }
}
```

Pour une autre approche adaptée au partage des données en mémoire entre des composants qui ne sont pas nécessairement imbriqués, consultez la section *service de conteneur d’État en mémoire* de l' <xref:blazor/state-management> article.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Lier à des cases d’option dans un formulaire](xref:blazor/forms-validation#radio-buttons)
* [Liaison `<select>` d’options d’éléments à `null` des valeurs d’objet C# dans un formulaire](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)

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
ms.openlocfilehash: 1aa36c8d91dbd92485e85f223f2391303bebac42
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109705"
---
Blazor est une infrastructure côté client d’application à page unique (SPA). Le navigateur sert d’hôte de l’application et joue donc le rôle de pipeline de traitement pour les Razor composants individuels en fonction des demandes URI pour la navigation et les ressources statiques. Contrairement à ASP.NET Core applications qui s’exécutent sur le serveur avec un pipeline de traitement des intergiciels (middleware), il n’y a pas de pipeline de middleware qui traite les demandes de Razor composants pouvant être exploités pour la gestion globale des erreurs. Toutefois, une application peut utiliser une erreur de traitement de composant comme valeur en cascade pour traiter les erreurs de manière centralisée.

Le `Error` composant suivant passe lui-même en tant que composant [`CascadingValue`](xref:blazor/components/cascading-values-and-parameters#cascadingvalue-component) aux composants enfants. L’exemple suivant consigne simplement l’erreur, mais les méthodes du composant peuvent traiter les erreurs de la manière requise par l’application, notamment via l’utilisation de plusieurs méthodes de traitement des erreurs. L’un des avantages de l’utilisation d’un composant sur l’utilisation d’un [service injecté](xref:blazor/fundamentals/dependency-injection) ou d’une implémentation d’enregistreur d’événements personnalisée est qu’un composant monté en cascade peut afficher du contenu et appliquer des styles CSS lorsqu’une erreur se produit.

`Shared/Error.razor`:

```razor
@using Microsoft.Extensions.Logging
@inject ILogger<Error> Logger

<CascadingValue Value=this>
    @ChildContent
</CascadingValue>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public void ProcessError(Exception ex)
    {
        Logger.LogError("Error:ProcessError - Type: {Type} Message: {Message}", 
            ex.GetType(), ex.Message);
    }
}
```

Dans le `App` composant, encapsulez le `Router` composant avec le `Error` composant. Cela permet au `Error` composant de se répercuter sur n’importe quel composant de l’application où le `Error` composant est reçu en tant que [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) .

`App.razor`:

```razor
<Error>
    <Router ...>
        ...
    </Router>
</Error>
```

Pour traiter les erreurs dans un composant :

* Désignez le `Error` composant en tant que [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) dans le [`@code`](xref:mvc/views/razor#code) bloc :

  ```razor
  [CascadingParameter]
  public Error Error { get; set; }
  ```

* Appelez une méthode de traitement des erreurs dans n’importe quel `catch` bloc avec un type d’exception approprié. L’exemple de `Error` composant n’offre qu’une seule `ProcessError` méthode, mais le composant de traitement des erreurs peut fournir un nombre quelconque de méthodes de traitement des erreurs pour répondre à d’autres exigences de traitement des erreurs dans l’ensemble de l’application.

  ```csharp
  try
  {
      ...
  }
  catch (Exception ex)
  {
      Error.ProcessError(ex);
  }
  ```

À l’aide de l’exemple de `Error` composant et de la `ProcessError` méthode précédents, la console outils de développement du navigateur indique l’erreur de journalisation interceptée :

> Fail : Blazor Sample. Shared. Error [0] erreur : ProcessError-type : System. NullReferenceException message : la référence d’objet n’est pas définie sur une instance d’un objet.

Si la `ProcessError` méthode participe directement au rendu, par exemple l’affichage d’une barre de message d’erreur personnalisée ou la modification des styles CSS des éléments rendus, appelez [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) à la fin de la `ProcessErrors` méthode pour rerestituer l’interface utilisateur.

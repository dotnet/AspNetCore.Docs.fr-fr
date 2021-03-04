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
<span data-ttu-id="4c11b-101">Blazor est une infrastructure côté client d’application à page unique (SPA).</span><span class="sxs-lookup"><span data-stu-id="4c11b-101">Blazor is a single-page application (SPA) client-side framework.</span></span> <span data-ttu-id="4c11b-102">Le navigateur sert d’hôte de l’application et joue donc le rôle de pipeline de traitement pour les Razor composants individuels en fonction des demandes URI pour la navigation et les ressources statiques.</span><span class="sxs-lookup"><span data-stu-id="4c11b-102">The browser serves as the app's host and thus acts as the processing pipeline for individual Razor components based on URI requests for navigation and static assets.</span></span> <span data-ttu-id="4c11b-103">Contrairement à ASP.NET Core applications qui s’exécutent sur le serveur avec un pipeline de traitement des intergiciels (middleware), il n’y a pas de pipeline de middleware qui traite les demandes de Razor composants pouvant être exploités pour la gestion globale des erreurs.</span><span class="sxs-lookup"><span data-stu-id="4c11b-103">Unlike ASP.NET Core apps that run on the server with a middleware processing pipeline, there is no middleware pipeline that processes requests for Razor components that can be leveraged for global error handling.</span></span> <span data-ttu-id="4c11b-104">Toutefois, une application peut utiliser une erreur de traitement de composant comme valeur en cascade pour traiter les erreurs de manière centralisée.</span><span class="sxs-lookup"><span data-stu-id="4c11b-104">However, an app can use an error processing component as a cascading value to process errors in a centralized way.</span></span>

<span data-ttu-id="4c11b-105">Le `Error` composant suivant passe lui-même en tant que composant [`CascadingValue`](xref:blazor/components/cascading-values-and-parameters#cascadingvalue-component) aux composants enfants.</span><span class="sxs-lookup"><span data-stu-id="4c11b-105">The following `Error` component passes itself as a [`CascadingValue`](xref:blazor/components/cascading-values-and-parameters#cascadingvalue-component) to child components.</span></span> <span data-ttu-id="4c11b-106">L’exemple suivant consigne simplement l’erreur, mais les méthodes du composant peuvent traiter les erreurs de la manière requise par l’application, notamment via l’utilisation de plusieurs méthodes de traitement des erreurs.</span><span class="sxs-lookup"><span data-stu-id="4c11b-106">The following example merely logs the error, but methods of the component can process errors in any way required by the app, including through the use of multiple error processing methods.</span></span> <span data-ttu-id="4c11b-107">L’un des avantages de l’utilisation d’un composant sur l’utilisation d’un [service injecté](xref:blazor/fundamentals/dependency-injection) ou d’une implémentation d’enregistreur d’événements personnalisée est qu’un composant monté en cascade peut afficher du contenu et appliquer des styles CSS lorsqu’une erreur se produit.</span><span class="sxs-lookup"><span data-stu-id="4c11b-107">An advantage of using a component over using an [injected service](xref:blazor/fundamentals/dependency-injection) or a custom logger implementation is that a cascaded component can render content and apply CSS styles when an error occurs.</span></span>

<span data-ttu-id="4c11b-108">`Shared/Error.razor`:</span><span class="sxs-lookup"><span data-stu-id="4c11b-108">`Shared/Error.razor`:</span></span>

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

<span data-ttu-id="4c11b-109">Dans le `App` composant, encapsulez le `Router` composant avec le `Error` composant.</span><span class="sxs-lookup"><span data-stu-id="4c11b-109">In the `App` component, wrap the `Router` component with the `Error` component.</span></span> <span data-ttu-id="4c11b-110">Cela permet au `Error` composant de se répercuter sur n’importe quel composant de l’application où le `Error` composant est reçu en tant que [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) .</span><span class="sxs-lookup"><span data-stu-id="4c11b-110">This permits the `Error` component to cascade down to any component of the app where the `Error` component is received as a [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute).</span></span>

<span data-ttu-id="4c11b-111">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="4c11b-111">`App.razor`:</span></span>

```razor
<Error>
    <Router ...>
        ...
    </Router>
</Error>
```

<span data-ttu-id="4c11b-112">Pour traiter les erreurs dans un composant :</span><span class="sxs-lookup"><span data-stu-id="4c11b-112">To process errors in a component:</span></span>

* <span data-ttu-id="4c11b-113">Désignez le `Error` composant en tant que [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) dans le [`@code`](xref:mvc/views/razor#code) bloc :</span><span class="sxs-lookup"><span data-stu-id="4c11b-113">Designate the `Error` component as a [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) in the [`@code`](xref:mvc/views/razor#code) block:</span></span>

  ```razor
  [CascadingParameter]
  public Error Error { get; set; }
  ```

* <span data-ttu-id="4c11b-114">Appelez une méthode de traitement des erreurs dans n’importe quel `catch` bloc avec un type d’exception approprié.</span><span class="sxs-lookup"><span data-stu-id="4c11b-114">Call an error processing method in any `catch` block with an appropriate exception type.</span></span> <span data-ttu-id="4c11b-115">L’exemple de `Error` composant n’offre qu’une seule `ProcessError` méthode, mais le composant de traitement des erreurs peut fournir un nombre quelconque de méthodes de traitement des erreurs pour répondre à d’autres exigences de traitement des erreurs dans l’ensemble de l’application.</span><span class="sxs-lookup"><span data-stu-id="4c11b-115">The example `Error` component only offers a single `ProcessError` method, but the error processing component can provide any number of error processing methods to address alternative error processing requirements throughout the app.</span></span>

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

<span data-ttu-id="4c11b-116">À l’aide de l’exemple de `Error` composant et de la `ProcessError` méthode précédents, la console outils de développement du navigateur indique l’erreur de journalisation interceptée :</span><span class="sxs-lookup"><span data-stu-id="4c11b-116">Using the preceding example `Error` component and `ProcessError` method, the browser's developer tools console indicates the trapped, logged error:</span></span>

> <span data-ttu-id="4c11b-117">Fail : Blazor Sample. Shared. Error [0] erreur : ProcessError-type : System. NullReferenceException message : la référence d’objet n’est pas définie sur une instance d’un objet.</span><span class="sxs-lookup"><span data-stu-id="4c11b-117">fail: BlazorSample.Shared.Error[0] Error:ProcessError - Type: System.NullReferenceException Message: Object reference not set to an instance of an object.</span></span>

<span data-ttu-id="4c11b-118">Si la `ProcessError` méthode participe directement au rendu, par exemple l’affichage d’une barre de message d’erreur personnalisée ou la modification des styles CSS des éléments rendus, appelez [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) à la fin de la `ProcessErrors` méthode pour rerestituer l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4c11b-118">If the `ProcessError` method directly participates in rendering, such as showing a custom error message bar or changing the CSS styles of the rendered elements, call [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) at the end of the `ProcessErrors` method to rerender the UI.</span></span>

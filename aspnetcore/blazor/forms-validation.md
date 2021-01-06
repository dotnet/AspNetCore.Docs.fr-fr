---
title: ASP.NET Core Blazor les formulaires et la validation
author: guardrex
description: Découvrez comment utiliser des formulaires et des scénarios de validation de champ dans Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/forms-validation
ms.openlocfilehash: 979e2615080a4f07b6091f0498fc7efa62ea1563
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "96320068"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a>ASP.NET Core Blazor les formulaires et la validation

Par [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/)et [Luke Latham](https://github.com/guardrex)

Les formulaires et la validation sont pris en charge dans Blazor l’utilisation des [Annotations de données](xref:mvc/models/validation).

Le `ExampleModel` type suivant définit la logique de validation à l’aide d’annotations de données :

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Un formulaire est défini à l’aide du <xref:Microsoft.AspNetCore.Components.Forms.EditForm> composant. Le formulaire suivant illustre des éléments, des composants et du Razor code typiques :

```razor
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }
}
```

Dans l’exemple précédent :

* Le formulaire valide l’entrée utilisateur dans le `name` champ à l’aide de la validation définie dans le `ExampleModel` type. Le modèle est créé dans le bloc du composant `@code` et conservé dans un champ privé ( `exampleModel` ). Le champ est assigné à l' `Model` attribut de l' `<EditForm>` élément.
* <xref:Microsoft.AspNetCore.Components.Forms.InputText>Liaisons du composant `@bind-Value` :
  * Propriété de modèle ( `exampleModel.Name` ) de la <xref:Microsoft.AspNetCore.Components.Forms.InputText> propriété du composant `Value` . Pour plus d’informations sur la liaison de propriété, consultez <xref:blazor/components/data-binding#binding-with-component-parameters> .
  * Délégué d’événement de modification à la <xref:Microsoft.AspNetCore.Components.Forms.InputText> propriété du composant `ValueChanged` .
* Le <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [composant validateur](#validator-components) attache la prise en charge de la validation à l’aide d’annotations de données.
* Le <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> composant résume les messages de validation.
* `HandleValidSubmit` est déclenché quand le formulaire est envoyé avec succès (réussit la validation).

## <a name="built-in-forms-components"></a>Composants de formulaires intégrés

Un ensemble de composants intégrés est disponible pour recevoir et valider les entrées utilisateur. Les entrées sont validées lorsqu’elles sont modifiées et lorsqu’un formulaire est envoyé. Les composants d’entrée disponibles sont répertoriés dans le tableau suivant.

::: moniker range=">= aspnetcore-5.0"

| Composant d’entrée | Rendu comme&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| [`InputFile`](xref:blazor/file-uploads) | `<input type="file">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| [`InputRadio`](#radio-buttons) | `<input type="radio">` |
| [`InputRadioGroup`](#radio-buttons) | `<input type="radio">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| Composant d’entrée | Rendu comme&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> Les `InputRadio` `InputRadioGroup` composants et sont disponibles dans ASP.net Core 5,0 ou version ultérieure. Pour plus d’informations, sélectionnez une version 5,0 ou ultérieure de cet article.

::: moniker-end

Tous les composants d’entrée, y compris <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , prennent en charge les attributs arbitraires. Tout attribut qui ne correspond pas à un paramètre de composant est ajouté à l’élément HTML rendu.

Les composants d’entrée fournissent le comportement par défaut pour valider quand un champ est modifié, y compris la mise à jour de la classe CSS Field pour refléter l’état du champ. Certains composants incluent une logique d’analyse utile. Par exemple, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> et <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> gèrent correctement les valeurs non analysables en inscrivant des valeurs non analysables en tant qu’erreurs de validation. Les types qui peuvent accepter des valeurs NULL prennent également en charge la possibilité de valeur null du champ cible (par exemple, `int?` ).

Le `Starship` type suivant définit la logique de validation à l’aide d’un plus grand ensemble de propriétés et d’annotations de données que la précédente `ExampleModel` :

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

Dans l’exemple précédent, `Description` est facultatif, car aucune annotation de données n’est présente.

La forme suivante valide l’entrée d’utilisateur à l’aide de la validation définie dans le `Starship` modèle :

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<xref:Microsoft.AspNetCore.Components.Forms.EditForm>Crée un <xref:Microsoft.AspNetCore.Components.Forms.EditContext> comme valeur en [cascade](xref:blazor/components/cascading-values-and-parameters) qui effectue le suivi des métadonnées relatives au processus de modification, notamment les champs qui ont été modifiés et les messages de validation actuels.

**Assignez** un <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **ou** un <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> à un <xref:Microsoft.AspNetCore.Components.Forms.EditForm> . L’attribution des deux n’est pas prise en charge et génère une **erreur d’exécution**.

Le <xref:Microsoft.AspNetCore.Components.Forms.EditForm> fournit des événements pratiques pour l’envoi de formulaires valides et non valides :

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

Utilisez <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> pour utiliser du code personnalisé pour déclencher la validation et vérifier les valeurs des champs.

Dans l’exemple suivant :

* La `HandleSubmit` méthode s’exécute lorsque le **`Submit`** bouton est sélectionné.
* Le formulaire est validé en appelant <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> .
* Du code supplémentaire est exécuté en fonction du résultat de la validation. Placez la logique métier dans la méthode assignée à <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> .

```razor
<EditForm EditContext="@editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate();

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }
}
```

> [!NOTE]
> L’API Framework n’existe pas pour effacer les messages de validation directement à partir d’un <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . Par conséquent, il n’est généralement pas recommandé d’ajouter des messages de validation à une nouvelle <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> dans un formulaire. Pour gérer les messages de validation, utilisez un [composant de validateur](#validator-components) avec votre [Code de validation de logique métier](#business-logic-validation), comme décrit dans cet article.

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a>Prise en charge des noms d’affichage

*Cette section s’applique à ASP.NET Core dans .NET 5 Release Candidate 1 (RC1) ou version ultérieure.*

Les composants intégrés suivants prennent en charge les noms complets avec le `DisplayName` paramètre :

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

Dans l' `InputDate` exemple de composant suivant :

* Le nom complet ( `DisplayName` ) a la valeur `birthday` .
* Le composant est lié à la `BirthDate` propriété en tant que `DateTime` type.

```razor
<InputDate @bind-Value="@BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

Si l’utilisateur ne fournit pas de valeur de date, l’erreur de validation se présente comme suit :

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a>Composants du validateur

Les composants de validateur prennent en charge la validation de formulaire en gérant un <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> pour un formulaire <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .

L' Blazor infrastructure fournit le <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant pour attacher la prise en charge de validation aux formulaires en fonction des [attributs de validation (annotations de données)](xref:mvc/models/validation#validation-attributes). Créer des composants de validateur personnalisés pour traiter des messages de validation pour différents formulaires sur la même page ou dans le même formulaire à des étapes différentes du traitement d’un formulaire, par exemple la validation côté client, suivie de la validation côté serveur. L’exemple du composant validateur présenté dans cette section, `CustomValidator` , est utilisé dans les sections suivantes de cet article :

* [Validation de la logique métier](#business-logic-validation)
* [Validation du serveur](#server-validation)

> [!NOTE]
> Les attributs de validation d’annotation de données personnalisées peuvent être utilisés à la place des composants de validateur personnalisés dans de nombreux cas. Les attributs personnalisés appliqués au modèle du formulaire s’activent avec l’utilisation du <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant. Lorsqu’ils sont utilisés avec la validation côté serveur, tous les attributs personnalisés appliqués au modèle doivent être exécutables sur le serveur. Pour plus d’informations, consultez <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

Créez un composant de validateur à partir de <xref:Microsoft.AspNetCore.Components.ComponentBase> :

* Le formulaire <xref:Microsoft.AspNetCore.Components.Forms.EditContext> est un [paramètre en cascade](xref:blazor/components/cascading-values-and-parameters) du composant.
* Lorsque le composant validateur est initialisé, un nouveau <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> est créé pour tenir à jour la liste actuelle des erreurs de formulaire.
* La Banque de messages reçoit des erreurs quand le code du développeur dans le composant du formulaire appelle la `DisplayErrors` méthode. Les erreurs sont passées à la `DisplayErrors` méthode dans un [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) . Dans le dictionnaire, la clé est le nom du champ de formulaire qui contient une ou plusieurs erreurs. La valeur est la liste d’erreurs.
* Les messages sont effacés lorsque l’un des éléments suivants s’est produit :
  * La validation est demandée sur le <xref:Microsoft.AspNetCore.Components.Forms.EditContext> quand l' <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> événement est déclenché. Toutes les erreurs sont effacées.
  * Un champ est modifié dans le formulaire lorsque l' <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> événement est déclenché. Seules les erreurs du champ sont effacées.
  * La `ClearErrors` méthode est appelée par le code du développeur. Toutes les erreurs sont effacées.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

public class CustomValidator : ComponentBase
{
    private ValidationMessageStore messageStore;

    [CascadingParameter]
    private EditContext CurrentEditContext { get; set; }

    protected override void OnInitialized()
    {
        if (CurrentEditContext == null)
        {
            throw new InvalidOperationException(
                $"{nameof(CustomValidator)} requires a cascading " +
                $"parameter of type {nameof(EditContext)}. " +
                $"For example, you can use {nameof(CustomValidator)} " +
                $"inside an {nameof(EditForm)}.");
        }

        messageStore = new ValidationMessageStore(CurrentEditContext);

        CurrentEditContext.OnValidationRequested += (s, e) => 
            messageStore.Clear();
        CurrentEditContext.OnFieldChanged += (s, e) => 
            messageStore.Clear(e.FieldIdentifier);
    }

    public void DisplayErrors(Dictionary<string, List<string>> errors)
    {
        foreach (var err in errors)
        {
            messageStore.Add(CurrentEditContext.Field(err.Key), err.Value);
        }

        CurrentEditContext.NotifyValidationStateChanged();
    }

    public void ClearErrors()
    {
        messageStore.Clear();
        CurrentEditContext.NotifyValidationStateChanged();
    }
}
```

## <a name="business-logic-validation"></a>Validation de la logique métier

La validation de la logique métier peut être accomplie à l’aide d’un [composant validateur](#validator-components) qui reçoit des erreurs de formulaire dans un dictionnaire.

Dans l’exemple suivant :

* Le `CustomValidator` composant de la section [composants du validateur](#validator-components) de cet article est utilisé.
* La validation requiert une valeur pour la description du navire ( `Description` ) si l’utilisateur sélectionne la `Defense` classification d’expédition ( `Classification` ).

Lorsque des messages de validation sont définis dans le composant, ils sont ajoutés au validateur <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> et s’affichent dans le <xref:Microsoft.AspNetCore.Components.Forms.EditForm> :

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    ...

</EditForm>

@code {
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        customValidator.ClearErrors();

        var errors = new Dictionary<string, List<string>>();

        if (starship.Classification == "Defense" &&
                string.IsNullOrEmpty(starship.Description))
        {
            errors.Add(nameof(starship.Description),
                new List<string>() { "For a 'Defense' ship classification, " +
                "'Description' is required." });
        }

        if (errors.Count() > 0)
        {
            customValidator.DisplayErrors(errors);
        }
        else
        {
            // Process the form
        }
    }
}
```

> [!NOTE]
> En guise d’alternative à l’utilisation de [composants de validation](#validator-components), les attributs de validation d’annotation de données peuvent être utilisés. Les attributs personnalisés appliqués au modèle du formulaire s’activent avec l’utilisation du <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant. Lorsqu’ils sont utilisés avec la validation côté serveur, les attributs doivent être exécutables sur le serveur. Pour plus d’informations, consultez <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

## <a name="server-validation"></a>Validation du serveur

La validation du serveur peut être effectuée à l’aide d’un [composant](#validator-components)de validation du serveur :

* Traitez la validation côté client dans le formulaire avec le <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant.
* Quand le formulaire passe la validation côté client ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> appelée), envoie <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> à une API de serveur principal pour le traitement de formulaire.
* Processus de validation du modèle sur le serveur.
* L’API serveur comprend la validation des annotations de données d’infrastructure intégrées et la logique de validation personnalisée fournie par le développeur. Si la validation réussit sur le serveur, traite le formulaire et renvoie un code d’état de réussite (*200-OK*). Si la validation échoue, retourne un code d’état d’échec (*400-demande incorrecte*) et les erreurs de validation de champ.
* Désactivez le formulaire en cas de réussite ou affichez les erreurs.

L’exemple suivant est basé sur :

* Solution hébergée Blazor créée par le [ Blazor modèle de projet hébergé](xref:blazor/hosting-models#blazor-webassembly). L’exemple peut être utilisé avec l’une des solutions hébergées sécurisées Blazor décrites dans la [ Identity documentation et la sécurité](xref:blazor/security/webassembly/index#implementation-guidance).
* Exemple *de formulaire de base de données Starfleet Starship* dans la section [composants de formulaires intégrés](#built-in-forms-components) précédents.
* BlazorComposant du Framework <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .
* `CustomValidator`Composant affiché dans la section [composants du validateur](#validator-components) .

Dans l’exemple suivant, l’API du serveur valide qu’une valeur est fournie pour la description du navire ( `Description` ) si l’utilisateur sélectionne la `Defense` classification Ship ( `Classification` ).

Placez le `Starship` modèle dans le projet de la solution `Shared` afin que les applications client et serveur puissent utiliser le modèle. Étant donné que le modèle requiert des annotations de données, ajoutez une référence de package pour [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) dans le `Shared` fichier projet du projet :

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

Pour déterminer la dernière version sans version préliminaire du package, consultez l’historique des **versions** du package sur [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).

Dans le projet d’API serveur, ajoutez un contrôleur pour traiter les demandes de validation Starship ( `Controllers/StarshipValidation.cs` ) et renvoyer les messages de validation ayant échoué :

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace {ASSEMBLY NAME}.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class StarshipValidationController : ControllerBase
    {
        private readonly ILogger<StarshipValidationController> logger;

        public StarshipValidationController(
            ILogger<StarshipValidationController> logger)
        {
            this.logger = logger;
        }

        [HttpPost]
        public async Task<IActionResult> Post(Starship starship)
        {
            try
            {
                if (starship.Classification == "Defense" && 
                    string.IsNullOrEmpty(starship.Description))
                {
                    ModelState.AddModelError(nameof(starship.Description),
                        "For a 'Defense' ship " +
                        "classification, 'Description' is required.");
                }
                else
                {
                    // Process the form asynchronously
                    // async ...

                    return Ok(ModelState);
                }
            }
            catch (Exception ex)
            {
                logger.LogError("Validation Error: {Message}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

Dans l’exemple précédent, l’espace réservé `{ASSEMBLY NAME}` est le nom de l’assembly de l’application (par exemple, `BlazorSample.Server` ).

Quand une erreur de validation de liaison de modèle se produit sur le serveur, un [`ApiController`](xref:web-api/index) ( <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> ) retourne normalement une [réponse de demande incorrecte par défaut](xref:web-api/index#default-badrequest-response) avec un <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> . La réponse contient plus de données que les erreurs de validation, comme illustré dans l’exemple suivant lorsque tous les champs du formulaire *de base de données Starfleet Starship* ne sont pas envoyés et que la validation du formulaire échoue :

```json
{
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Identifier": ["The Identifier field is required."],
    "Classification": ["The Classification field is required."],
    "IsValidatedDesign": ["This form disallows unapproved ships."],
    "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
  }
}
```

Si l’API serveur retourne la réponse JSON par défaut précédente, le client peut analyser la réponse pour obtenir les enfants du `errors` nœud. Toutefois, il est peu commode d’analyser le fichier. L’analyse du code JSON nécessite du code supplémentaire après <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> l’appel afin de produire un [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) des erreurs pour le traitement des erreurs de validation des formulaires. Dans l’idéal, l’API serveur doit renvoyer uniquement les erreurs de validation :

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

Pour modifier la réponse de l’API serveur afin qu’elle retourne uniquement les erreurs de validation, modifiez le délégué appelé sur les actions annotées avec <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> dans `Startup.ConfigureServices` . Pour le point de terminaison de l’API ( `/StarshipValidation` ), retournez un <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> avec <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> . Pour tous les autres points de terminaison d’API, conservez le comportement par défaut en retournant le résultat de l’objet avec un nouveau <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> :

```csharp
using Microsoft.AspNetCore.Mvc;

...

services.AddControllersWithViews()
    .ConfigureApiBehaviorOptions(options =>
    {
        options.InvalidModelStateResponseFactory = context =>
        {
            if (context.HttpContext.Request.Path == "/StarshipValidation")
            {
                return new BadRequestObjectResult(context.ModelState);
            }
            else
            {
                return new BadRequestObjectResult(
                    new ValidationProblemDetails(context.ModelState));
            }
        };
    });
```

Pour plus d’informations, consultez <xref:web-api/handle-errors#validation-failure-error-response>.

Dans le projet client, ajoutez le composant validateur présenté dans la section [composants du validateur](#validator-components) .

Dans le projet client, le formulaire *de base de données Starfleet Starship* est mis à jour pour afficher les erreurs de validation du serveur avec l’aide du `CustomValidator` composant. Lorsque l’API serveur retourne des messages de validation, ils sont ajoutés au `CustomValidator` du composant <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> . Les erreurs sont disponibles dans le formulaire <xref:Microsoft.AspNetCore.Components.Forms.EditContext> pour être affichées par le du formulaire <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> :

```razor
@page "/FormValidation"
@using System.Net
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@using BlazorSample.Shared
@attribute [Authorize]
@inject HttpClient Http
@inject ILogger<FormValidation> Logger

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification" disabled="@disabled">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" disabled="@disabled" />
        </label>
    </p>

    <button type="submit" disabled="@disabled">Submit</button>

    <p style="@messageStyles">
        @message
    </p>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>,
        &copy;1966-2019 CBS Studios, Inc. and
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private bool disabled;
    private string message;
    private string messageStyles = "visibility:hidden";
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private async Task HandleValidSubmit(EditContext editContext)
    {
        customValidator.ClearErrors();

        try
        {
            var response = await Http.PostAsJsonAsync<Starship>(
                "StarshipValidation", (Starship)editContext.Model);

            var errors = await response.Content
                .ReadFromJsonAsync<Dictionary<string, List<string>>>();

            if (response.StatusCode == HttpStatusCode.BadRequest && 
                errors.Count() > 0)
            {
                customValidator.DisplayErrors(errors);
            }
            else if (!response.IsSuccessStatusCode)
            {
                throw new HttpRequestException(
                    $"Validation failed. Status Code: {response.StatusCode}");
            }
            else
            {
                disabled = true;
                messageStyles = "color:green";
                message = "The form has been processed.";
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            ex.Redirect();
        }
        catch (Exception ex)
        {
            Logger.LogError("Form processing error: {Message}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> Comme alternative aux [composants de validation](#validator-components), les attributs de validation d’annotation de données peuvent être utilisés. Les attributs personnalisés appliqués au modèle du formulaire s’activent avec l’utilisation du <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant. Lorsqu’ils sont utilisés avec la validation côté serveur, les attributs doivent être exécutables sur le serveur. Pour plus d’informations, consultez <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

> [!NOTE]
> L’approche de validation côté serveur de cette section convient à l’un des Blazor WebAssembly exemples de solutions hébergées dans cet ensemble de documentation :
>
> * [Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [Identity Serveurs](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a>InputText en fonction de l’événement d’entrée

Utilisez le <xref:Microsoft.AspNetCore.Components.Forms.InputText> composant pour créer un composant personnalisé qui utilise l' `input` événement au lieu de l' `change` événement.

Dans l’exemple suivant, le `CustomInputText` composant hérite du composant de l’infrastructure `InputText` et définit la liaison d’événements à l' `oninput` événement.

`Shared/CustomInputText.razor`:

```razor
@inherits InputText

<input @attributes="AdditionalAttributes" class="@CssClass" 
    @bind="CurrentValueAsString" @bind:event="oninput" />
```

Le `CustomInputText` composant peut être utilisé partout où <xref:Microsoft.AspNetCore.Components.Forms.InputText> il est utilisé :

`Pages/TestForm.razor`:

```razor
@page "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a>Cases d’option

::: moniker range=">= aspnetcore-5.0"

Utilisez `InputRadio` les composants avec le `InputRadioGroup` composant pour créer un groupe de cases d’option. Dans l’exemple suivant, les propriétés sont ajoutées au `Starship` modèle décrit dans la section [composants de formulaires intégrés](#built-in-forms-components) :

```csharp
[Required]
[Range(typeof(Manufacturer), nameof(Manufacturer.SpaceX), 
    nameof(Manufacturer.VirginGalactic), ErrorMessage = "Pick a manufacturer.")]
public Manufacturer Manufacturer { get; set; } = Manufacturer.Unknown;

[Required, EnumDataType(typeof(Color))]
public Color? Color { get; set; } = null;

[Required, EnumDataType(typeof(Engine))]
public Engine? Engine { get; set; } = null;
```

Ajoutez le code suivant `enums` à l’application. Créez un nouveau fichier pour contenir le `enums` ou ajoutez le `enums` au `Starship.cs` fichier. Rendez le `enums` accessible au `Starship` modèle et à la *base de données Starfleet Starship* :

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, VirginGalactic, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

Mettez à jour le formulaire *de base de données Starfleet Starship* décrit dans la section [composants de formulaires intégrés](#built-in-forms-components) . Ajoutez les composants à produire :

* Groupe de cases d’option pour le fabricant de l’expédition.
* Groupe de cases d’option imbriqué pour la couleur et le moteur de l’expédition.

```razor
<p>
    <InputRadioGroup @bind-Value="starship.Manufacturer">
        Manufacturer:
        <br>
        @foreach (var manufacturer in (Manufacturer[])Enum
            .GetValues(typeof(Manufacturer)))
        {
            <InputRadio Value="manufacturer" />
            @manufacturer
            <br>
        }
    </InputRadioGroup>
</p>

<p>
    Pick one color and one engine:
    <InputRadioGroup Name="engine" @bind-Value="starship.Engine">
        <InputRadioGroup Name="color" @bind-Value="starship.Color">
            <InputRadio Name="color" Value="Color.ImperialRed" />Imperial Red<br>
            <InputRadio Name="engine" Value="Engine.Ion" />Ion<br>
            <InputRadio Name="color" Value="Color.SpacecruiserGreen" />
                Spacecruiser Green<br>
            <InputRadio Name="engine" Value="Engine.Plasma" />Plasma<br>
            <InputRadio Name="color" Value="Color.StarshipBlue" />Starship Blue<br>
            <InputRadio Name="engine" Value="Engine.Fusion" />Fusion<br>
            <InputRadio Name="color" Value="Color.VoyagerOrange" />
                Voyager Orange<br>
            <InputRadio Name="engine" Value="Engine.Warp" />Warp<br>
        </InputRadioGroup>
    </InputRadioGroup>
</p>
```

> [!NOTE]
> Si `Name` est omis, `InputRadio` les composants sont regroupés par leur ancêtre le plus récent.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Lorsque vous utilisez des cases d’option dans un formulaire, la liaison de données est gérée différemment des autres éléments, car les cases d’option sont évaluées en tant que groupe. La valeur de chaque case d’option est fixe, mais la valeur du groupe de cases d’option est la valeur de la case d’option sélectionnée. L’exemple suivant montre comment :

* Gérer la liaison de données pour un groupe de cases d’option.
* Prendre en charge la validation à l’aide d’un `InputRadio` composant personnalisé.

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

L’exemple suivant <xref:Microsoft.AspNetCore.Components.Forms.EditForm> utilise le `InputRadio` composant précédent pour obtenir et valider une évaluation de l’utilisateur :

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        ...
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

::: moniker-end

## <a name="binding-select-element-options-to-c-object-null-values"></a>Liaison `<select>` d’options d’élément aux valeurs d’objet C# `null`

Il n’existe pas de méthode raisonnable pour représenter une `<select>` valeur d’option d’élément en tant que valeur d’objet C# `null` , car :

* Les attributs HTML ne peuvent pas avoir de `null` valeurs. L’équivalent le plus proche de `null` en HTML est l’absence de l' `value` attribut HTML de l' `<option>` élément.
* Quand vous sélectionnez un `<option>` `value` élément sans attribut, le navigateur traite la valeur comme le *contenu de texte* de cet `<option>` élément.

L' Blazor infrastructure ne tente pas de supprimer le comportement par défaut, car cela impliquerait :

* Création d’une chaîne de solutions de contournement de cas spéciaux dans le Framework.
* Modifications avec rupture apportées au comportement actuel de l’infrastructure.

::: moniker range=">= aspnetcore-5.0"

L’équivalent le plus plausible `null` en HTML est une *chaîne vide* `value` . Le Blazor Framework gère les `null` conversions de chaînes vides pour une liaison bidirectionnelle vers une `<select>` valeur de.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

L' Blazor infrastructure ne gère pas automatiquement les `null` conversions de chaînes vides lors de la tentative de liaison bidirectionnelle à la `<select>` valeur de. Pour plus d’informations, consultez [fixer `<select>` la liaison à une valeur null (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).

::: moniker-end

## <a name="validation-support"></a>Prise en charge de la validation

Le <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant attache la prise en charge de la validation à l’aide d’annotations de données à la Cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . L’activation de la prise en charge de la validation à l’aide d’annotations de données requiert ce geste explicite. Pour utiliser un système de validation différent des annotations de données, remplacez <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> par une implémentation personnalisée. L’implémentation de ASP.NET Core est disponible pour l’inspection dans la source de référence : [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) . Les liens précédents vers la source de référence fournissent du code à partir de la branche du référentiel `master` , qui représente le développement actuel de l’unité du produit pour la prochaine version de ASP.net core. Pour sélectionner la branche pour une version différente, utilisez le sélecteur de branche GitHub (par exemple `release/3.1` ).

Blazor effectue deux types de validation :

* La *validation de champ* est effectuée lorsque l’utilisateur quitte un champ. Pendant la validation de champ, le <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant associe tous les résultats de validation signalés au champ.
* La *validation du modèle* est effectuée lorsque l’utilisateur envoie le formulaire. Lors de la validation du modèle, le <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant tente de déterminer le champ en fonction du nom du membre que le résultat de validation signale. Les résultats de validation qui ne sont pas associés à un membre individuel sont associés au modèle plutôt qu’à un champ.

### <a name="validation-summary-and-validation-message-components"></a>Résumé des validations et composants de message de validation

Le <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> composant résume tous les messages de validation, ce qui est similaire au [tag Helper de résumé de validation](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Messages de validation de sortie pour un modèle spécifique avec le `Model` paramètre :
  
```razor
<ValidationSummary Model="@starship" />
```

Le <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> composant affiche des messages de validation pour un champ spécifique, qui est similaire [au tag Helper de message de validation](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Spécifiez le champ pour la validation avec l' `For` attribut et une expression lambda qui nomme la propriété de modèle :

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

Les <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> composants et prennent en charge des attributs arbitraires. Tout attribut qui ne correspond pas à un paramètre de composant est ajouté à l' `<div>` élément ou généré `<ul>` .

Contrôler le style des messages de validation dans la feuille de style de l’application ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ). La classe par défaut `validation-message` définit la couleur du texte des messages de validation sur rouge :

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a>Attributs de validation personnalisés

Pour vous assurer qu’un résultat de validation est correctement associé à un champ lors de l’utilisation d’un [attribut de validation personnalisé](xref:mvc/models/validation#custom-attributes), transmettez le contexte de validation <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> lors de la création du <xref:System.ComponentModel.DataAnnotations.ValidationResult> :

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

> [!NOTE]
> <xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> a la valeur `null`. L’injection de services pour la validation dans la `IsValid` méthode n’est pas prise en charge.

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a>Attributs de classe de validation personnalisée

Les noms des classes de validation personnalisées sont utiles lors de l’intégration avec des frameworks CSS, tels que [bootstrap](https://getbootstrap.com/). Pour spécifier des noms de classe de validation personnalisés, créez une classe dérivée de `FieldCssClassProvider` et définissez la classe sur l' <xref:Microsoft.AspNetCore.Components.Forms.EditContext> instance :

```csharp
var editContext = new EditContext(model);
editContext.SetFieldCssClassProvider(new MyFieldClassProvider());

...

private class MyFieldClassProvider : FieldCssClassProvider
{
    public override string GetFieldCssClass(EditContext editContext, 
        in FieldIdentifier fieldIdentifier)
    {
        var isValid = !editContext.GetValidationMessages(fieldIdentifier).Any();

        return isValid ? "good field" : "bad field";
    }
}
```

::: moniker-end

### <a name="no-locblazor-data-annotations-validation-package"></a>Blazor package de validation des annotations de données

Le [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) est un package qui remplit les lacunes de l’expérience de validation à l’aide du <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant. Le package est actuellement *expérimental*.

> [!NOTE]
> Le [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package a une version la plus récente de la version *Release candidate* sur [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continuez à utiliser le package *expérimental* release candidate pour l’instant. L’assembly du package peut être déplacé vers l’infrastructure ou le runtime dans une version ultérieure. Regardez le [référentiel GitHub d’annonces](https://github.com/aspnet/Announcements), le [référentiel GitHub dotnet/aspnetcore](https://github.com/dotnet/aspnetcore), ou la section de cette rubrique pour obtenir des mises à jour supplémentaires.

::: moniker range="< aspnetcore-5.0"

### <a name="compareproperty-attribute"></a>Attribut `[CompareProperty]`

Le <xref:System.ComponentModel.DataAnnotations.CompareAttribute> ne fonctionne pas correctement avec le <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant, car il n’associe pas le résultat de la validation à un membre spécifique. Cela peut entraîner un comportement incohérent entre la validation au niveau du champ et le moment où la totalité du modèle est validée sur une soumission. Le [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package *expérimental* introduit un attribut de validation supplémentaire, `ComparePropertyAttribute` , qui contourne ces limitations. Dans une Blazor application, `[CompareProperty]` est un remplacement direct de l' [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribut.

::: moniker-end

### <a name="nested-models-collection-types-and-complex-types"></a>Modèles imbriqués, types de collection et types complexes

Blazor prend en charge la validation de l’entrée de formulaire à l’aide d’annotations de données avec le intégré <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> . Toutefois, l' <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> valide uniquement les propriétés de niveau supérieur du modèle lié au formulaire qui ne sont pas des propriétés de type collection ou complexe.

Pour valider le graphique d’objets entier du modèle lié, y compris les propriétés de type collection et de type complexe, utilisez le `ObjectGraphDataAnnotationsValidator` fourni par le package *expérimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Annotez les propriétés de modèle avec `[ValidateComplexType]` . Dans les classes de modèle suivantes, la `ShipDescription` classe contient des annotations de données supplémentaires à valider lorsque le modèle est lié au formulaire :

`Starship.cs`:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; } = 
        new ShipDescription();

    ...
}
```

`ShipDescription.cs`:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }

    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a>Activer le bouton Envoyer en fonction de la validation de formulaire

Pour activer et désactiver le bouton Envoyer en fonction de la validation de formulaire :

* Utilisez le du formulaire <xref:Microsoft.AspNetCore.Components.Forms.EditContext> pour assigner le modèle lorsque le composant est initialisé.
* Validez le formulaire dans le rappel du contexte <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> pour activer et désactiver le bouton Envoyer.
* Décrochez le gestionnaire d’événements dans la `Dispose` méthode. Pour plus d’informations, consultez <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

> [!NOTE]
> Quand vous utilisez un <xref:Microsoft.AspNetCore.Components.Forms.EditContext> , n’assignez pas également <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> à <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

Dans l’exemple précédent, affectez à la valeur `formInvalid` `false` si :

* Le formulaire est préchargé avec des valeurs par défaut valides.
* Vous voulez que le bouton envoyer soit activé lors du chargement du formulaire.

L’un des effets secondaires de l’approche précédente est qu’un <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> composant est rempli avec des champs non valides une fois que l’utilisateur interagit avec un champ quelconque. Ce scénario peut être traité de l’une des manières suivantes :

* N’utilisez pas un <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> composant sur le formulaire.
* Rendre le <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> composant visible lorsque le bouton Envoyer est sélectionné (par exemple, dans une `HandleValidSubmit` méthode).

```razor
<EditForm EditContext="@editContext" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a>Dépanner

> InvalidOperationException : EditForm requiert un paramètre de modèle, ou un paramètre EditContext, mais pas les deux.

Vérifiez que <xref:Microsoft.AspNetCore.Components.Forms.EditForm> a un <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **ou** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . N’utilisez pas les deux pour le même formulaire.

Quand vous assignez un <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> au formulaire, vérifiez que le type de modèle est instancié, comme le montre l’exemple suivant :

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/file-uploads>

::: moniker-end

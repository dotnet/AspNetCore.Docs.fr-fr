---
title: ASP.NET Core des formulaires et des validations éblouissants
author: guardrex
description: Découvrez comment utiliser des formulaires et des scénarios de validation de champ dans éblouissant.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/forms-validation
ms.openlocfilehash: 6f6ace3deb7ed4262b643d897273bc767334b5e6
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207180"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="1ed1e-103">ASP.NET Core des formulaires et des validations éblouissants</span><span class="sxs-lookup"><span data-stu-id="1ed1e-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="1ed1e-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1ed1e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1ed1e-105">Les formulaires et la validation sont pris en charge dans éblouissant à l’aide d' [Annotations de données](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="1ed1e-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="1ed1e-106">Le type `ExampleModel` suivant définit la logique de validation à l’aide d’annotations de données :</span><span class="sxs-lookup"><span data-stu-id="1ed1e-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="1ed1e-107">Un formulaire est défini à l' `EditForm` aide du composant.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="1ed1e-108">Le formulaire suivant illustre des éléments typiques, des composants et du code Razor :</span><span class="sxs-lookup"><span data-stu-id="1ed1e-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="@exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

* <span data-ttu-id="1ed1e-109">Le formulaire valide l’entrée utilisateur dans le `name` champ à l’aide de la validation `ExampleModel` définie dans le type.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="1ed1e-110">Le modèle est créé dans le bloc du `@code` composant et conservé dans un champ privé (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="1ed1e-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="1ed1e-111">Le champ est assigné à l' `Model` attribut de l' `<EditForm>` élément.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="1ed1e-112">Le `DataAnnotationsValidator` composant attache la prise en charge de la validation à l’aide d’annotations de données.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-112">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="1ed1e-113">Le `ValidationSummary` composant résume les messages de validation.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-113">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="1ed1e-114">`HandleValidSubmit`est déclenché quand le formulaire est envoyé avec succès (réussit la validation).</span><span class="sxs-lookup"><span data-stu-id="1ed1e-114">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="1ed1e-115">Un ensemble de composants d’entrée intégrés est disponible pour recevoir et valider les entrées utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-115">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="1ed1e-116">Les entrées sont validées lorsqu’elles sont modifiées et lorsqu’un formulaire est envoyé.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-116">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="1ed1e-117">Les composants d’entrée disponibles sont répertoriés dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-117">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="1ed1e-118">Composant d’entrée</span><span class="sxs-lookup"><span data-stu-id="1ed1e-118">Input component</span></span> | <span data-ttu-id="1ed1e-119">Rendu comme&hellip;</span><span class="sxs-lookup"><span data-stu-id="1ed1e-119">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="1ed1e-120">Tous les composants d’entrée, y `EditForm`compris, prennent en charge les attributs arbitraires.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-120">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="1ed1e-121">Tout attribut qui ne correspond pas à un paramètre de composant est ajouté à l’élément HTML rendu.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-121">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="1ed1e-122">Les composants d’entrée fournissent le comportement par défaut pour la validation lors de la modification et la modification de leur classe CSS pour refléter l’état du champ.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-122">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="1ed1e-123">Certains composants incluent une logique d’analyse utile.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-123">Some components include useful parsing logic.</span></span> <span data-ttu-id="1ed1e-124">Par exemple, `InputDate` et `InputNumber` gèrent correctement les valeurs non analysables en les inscrivant en tant qu’erreurs de validation.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-124">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="1ed1e-125">Les types qui peuvent accepter des valeurs NULL prennent également en charge la possibilité de valeur null du `int?`champ cible (par exemple,).</span><span class="sxs-lookup"><span data-stu-id="1ed1e-125">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="1ed1e-126">Le type `Starship` suivant définit la logique de validation à l’aide d’un plus grand ensemble de propriétés et `ExampleModel`d’annotations de données que la précédente :</span><span class="sxs-lookup"><span data-stu-id="1ed1e-126">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="1ed1e-127">Dans l’exemple précédent, `Description` est facultatif, car aucune annotation de données n’est présente.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-127">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="1ed1e-128">La forme suivante valide l’entrée d’utilisateur à l’aide de la `Starship` validation définie dans le modèle :</span><span class="sxs-lookup"><span data-stu-id="1ed1e-128">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```cshtml
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label for="identifier">Identifier: </label>
        <InputText id="identifier" @bind-Value="starship.Identifier" />
    </p>
    <p>
        <label for="description">Description (optional): </label>
        <InputTextArea id="description" @bind-Value="starship.Description" />
    </p>
    <p>
        <label for="classification">Primary Classification: </label>
        <InputSelect id="classification" @bind-Value="starship.Classification">
            <option value="">Select classification ...</option>
            <option value="Exploration">Exploration</option>
            <option value="Diplomacy">Diplomacy</option>
            <option value="Defense">Defense</option>
        </InputSelect>
    </p>
    <p>
        <label for="accommodation">Maximum Accommodation: </label>
        <InputNumber id="accommodation" 
            @bind-Value="starship.MaximumAccommodation" />
    </p>
    <p>
        <label for="valid">Engineering Approval: </label>
        <InputCheckbox id="valid" @bind-Value="starship.IsValidatedDesign" />
    </p>
    <p>
        <label for="productionDate">Production Date: </label>
        <InputDate id="productionDate" @bind-Value="starship.ProductionDate" />
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="1ed1e-129">Crée un `EditContext` comme valeur en [cascade](xref:blazor/components#cascading-values-and-parameters) qui effectue le suivi des métadonnées relatives au processus de modification, notamment les champs qui ont été modifiés et les messages de validation actuels. `EditForm`</span><span class="sxs-lookup"><span data-stu-id="1ed1e-129">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="1ed1e-130">Le `EditForm` fournit également des événements pratiques pour les envois valides`OnValidSubmit`et `OnInvalidSubmit`non valides (,).</span><span class="sxs-lookup"><span data-stu-id="1ed1e-130">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="1ed1e-131">Vous pouvez également utiliser `OnSubmit` pour déclencher la validation et vérifier les valeurs de champ avec un code de validation personnalisé.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-131">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="1ed1e-132">InputText en fonction de l’événement d’entrée</span><span class="sxs-lookup"><span data-stu-id="1ed1e-132">InputText based on the input event</span></span>

<span data-ttu-id="1ed1e-133">Utilisez le `InputText` composant pour créer un composant personnalisé qui utilise l' `input` événement au lieu de `change` l’événement.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-133">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="1ed1e-134">Créez un composant avec le balisage suivant et utilisez le composant de la `InputText` même façon que :</span><span class="sxs-lookup"><span data-stu-id="1ed1e-134">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```cshtml
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a><span data-ttu-id="1ed1e-135">Prise en charge de la validation</span><span class="sxs-lookup"><span data-stu-id="1ed1e-135">Validation support</span></span>

<span data-ttu-id="1ed1e-136">Le `DataAnnotationsValidator` composant attache la prise en charge de la validation à l’aide d' `EditContext`annotations de données à la Cascaded.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-136">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="1ed1e-137">L’activation de la prise en charge de la validation à l’aide d’annotations de données requiert ce geste explicite.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-137">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="1ed1e-138">Pour utiliser un système de validation différent des annotations de données `DataAnnotationsValidator` , remplacez par une implémentation personnalisée.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-138">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="1ed1e-139">L’implémentation de ASP.NET Core est disponible pour l’inspection dans la source de référence : [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="1ed1e-139">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

<span data-ttu-id="1ed1e-140">Le `ValidationSummary` composant résume tous les messages de validation, ce qui est similaire au [tag Helper de résumé de validation](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="1ed1e-140">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="1ed1e-141">Le `ValidationMessage` composant affiche des messages de validation pour un champ spécifique, qui est similaire au [tag Helper de message de validation](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="1ed1e-141">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="1ed1e-142">Spécifiez le champ pour la validation `For` avec l’attribut et une expression lambda qui nomme la propriété de modèle :</span><span class="sxs-lookup"><span data-stu-id="1ed1e-142">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="1ed1e-143">Les `ValidationMessage` composants `ValidationSummary` et prennent en charge des attributs arbitraires.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-143">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="1ed1e-144">Tout attribut qui ne correspond pas à un paramètre de composant est ajouté `<div>` à `<ul>` l’élément ou généré.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-144">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="validation-of-complex-or-collection-type-properties"></a><span data-ttu-id="1ed1e-145">Validation des propriétés de type complexe ou de collection</span><span class="sxs-lookup"><span data-stu-id="1ed1e-145">Validation of complex or collection type properties</span></span>

<span data-ttu-id="1ed1e-146">Les attributs de validation appliqués aux propriétés d’un modèle valident le moment où le formulaire est envoyé.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-146">Validation attributes applied to the properties of a model validate when the form is submitted.</span></span> <span data-ttu-id="1ed1e-147">Toutefois, les propriétés des collections ou des types de données complexes d’un modèle ne sont pas validées lors de l’envoi du formulaire.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-147">However, the properties of collections or complex data types of a model aren't validated on form submission.</span></span> <span data-ttu-id="1ed1e-148">Pour honorer les attributs de validation imbriqués dans ce scénario, utilisez un composant de validation personnalisé.</span><span class="sxs-lookup"><span data-stu-id="1ed1e-148">To honor the nested validation attributes in this scenario, use a custom validation component.</span></span> <span data-ttu-id="1ed1e-149">Pour obtenir un exemple, consultez l' [exemple de validation éblouissant dans le référentiel GitHub ASPNET/Samples](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span><span class="sxs-lookup"><span data-stu-id="1ed1e-149">For an example, see the [Blazor Validation sample in the aspnet/samples GitHub repository](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>
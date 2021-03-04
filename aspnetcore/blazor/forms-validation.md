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
ms.openlocfilehash: a942c7848c77444d185ff73338a98a4205451992
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109726"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="6bbaa-103">ASP.NET Core Blazor les formulaires et la validation</span><span class="sxs-lookup"><span data-stu-id="6bbaa-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="6bbaa-104">Les formulaires et la validation sont pris en charge dans Blazor l’utilisation des [Annotations de données](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-104">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="6bbaa-105">Le `ExampleModel` type suivant définit la logique de validation à l’aide d’annotations de données :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-105">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="6bbaa-106">Un formulaire est défini à l’aide du <xref:Microsoft.AspNetCore.Components.Forms.EditForm> composant.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-106">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="6bbaa-107">Le formulaire suivant illustre des éléments, des composants et du Razor code typiques :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-107">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="6bbaa-108">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-108">In the preceding example:</span></span>

* <span data-ttu-id="6bbaa-109">Le formulaire valide l’entrée utilisateur dans le `name` champ à l’aide de la validation définie dans le `ExampleModel` type.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="6bbaa-110">Le modèle est créé dans le bloc du composant `@code` et conservé dans un champ privé ( `exampleModel` ).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="6bbaa-111">Le champ est assigné à l' `Model` attribut de l' `<EditForm>` élément.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="6bbaa-112"><xref:Microsoft.AspNetCore.Components.Forms.InputText>Liaisons du composant `@bind-Value` :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-112">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="6bbaa-113">Propriété de modèle ( `exampleModel.Name` ) de la <xref:Microsoft.AspNetCore.Components.Forms.InputText> propriété du composant `Value` .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-113">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="6bbaa-114">Pour plus d’informations sur la liaison de propriété, consultez <xref:blazor/components/data-binding#binding-with-component-parameters> .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-114">For more information on property binding, see <xref:blazor/components/data-binding#binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="6bbaa-115">Délégué d’événement de modification à la <xref:Microsoft.AspNetCore.Components.Forms.InputText> propriété du composant `ValueChanged` .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-115">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="6bbaa-116">Le <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [composant validateur](#validator-components) attache la prise en charge de la validation à l’aide d’annotations de données.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-116">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [validator component](#validator-components) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="6bbaa-117">Le <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> composant résume les messages de validation.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-117">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="6bbaa-118">`HandleValidSubmit` est déclenché quand le formulaire est envoyé avec succès (réussit la validation).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="6bbaa-119">Composants de formulaires intégrés</span><span class="sxs-lookup"><span data-stu-id="6bbaa-119">Built-in forms components</span></span>

<span data-ttu-id="6bbaa-120">Un ensemble de composants intégrés est disponible pour recevoir et valider les entrées utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-120">A set of built-in components are available to receive and validate user input.</span></span> <span data-ttu-id="6bbaa-121">Les entrées sont validées lorsqu’elles sont modifiées et lorsqu’un formulaire est envoyé.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-121">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="6bbaa-122">Les composants d’entrée disponibles sont répertoriés dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-122">Available input components are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="6bbaa-123">Composant d’entrée</span><span class="sxs-lookup"><span data-stu-id="6bbaa-123">Input component</span></span> | <span data-ttu-id="6bbaa-124">Rendu comme&hellip;</span><span class="sxs-lookup"><span data-stu-id="6bbaa-124">Rendered as&hellip;</span></span> |
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

| <span data-ttu-id="6bbaa-125">Composant d’entrée</span><span class="sxs-lookup"><span data-stu-id="6bbaa-125">Input component</span></span> | <span data-ttu-id="6bbaa-126">Rendu comme&hellip;</span><span class="sxs-lookup"><span data-stu-id="6bbaa-126">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> <span data-ttu-id="6bbaa-127">Les `InputRadio` `InputRadioGroup` composants et sont disponibles dans ASP.net Core 5,0 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-127">The `InputRadio` and `InputRadioGroup` components are available in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="6bbaa-128">Pour plus d’informations, sélectionnez une version 5,0 ou ultérieure de cet article.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-128">For more information, select a 5.0 or later version of this article.</span></span>

::: moniker-end

<span data-ttu-id="6bbaa-129">Tous les composants d’entrée, y compris <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , prennent en charge les attributs arbitraires.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-129">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="6bbaa-130">Tout attribut qui ne correspond pas à un paramètre de composant est ajouté à l’élément HTML rendu.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-130">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="6bbaa-131">Les composants d’entrée fournissent le comportement par défaut pour valider quand un champ est modifié, y compris la mise à jour de la classe CSS Field pour refléter l’état du champ.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-131">Input components provide default behavior for validating when a field is changed, including updating the field CSS class to reflect the field state.</span></span> <span data-ttu-id="6bbaa-132">Certains composants incluent une logique d’analyse utile.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-132">Some components include useful parsing logic.</span></span> <span data-ttu-id="6bbaa-133">Par exemple, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> et <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> gèrent correctement les valeurs non analysables en inscrivant des valeurs non analysables en tant qu’erreurs de validation.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-133">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering unparseable values as validation errors.</span></span> <span data-ttu-id="6bbaa-134">Les types qui peuvent accepter des valeurs NULL prennent également en charge la possibilité de valeur null du champ cible (par exemple, `int?` ).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-134">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="6bbaa-135">Le `Starship` type suivant définit la logique de validation à l’aide d’un plus grand ensemble de propriétés et d’annotations de données que la précédente `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-135">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="6bbaa-136">Dans l’exemple précédent, `Description` est facultatif, car aucune annotation de données n’est présente.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-136">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="6bbaa-137">La forme suivante valide l’entrée d’utilisateur à l’aide de la validation définie dans le `Starship` modèle :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-137">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="6bbaa-138"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Crée un <xref:Microsoft.AspNetCore.Components.Forms.EditContext> comme valeur en [cascade](xref:blazor/components/cascading-values-and-parameters) qui effectue le suivi des métadonnées relatives au processus de modification, notamment les champs qui ont été modifiés et les messages de validation actuels.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-138">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span>

<span data-ttu-id="6bbaa-139">**Assignez** un <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **ou** un <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> à un <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-139">Assign **either** an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **or** an <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> to an <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span> <span data-ttu-id="6bbaa-140">L’attribution des deux n’est pas prise en charge et génère une **erreur d’exécution**.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-140">Assignment of both isn't supported and generates a **runtime error**.</span></span>

<span data-ttu-id="6bbaa-141">Le <xref:Microsoft.AspNetCore.Components.Forms.EditForm> fournit des événements pratiques pour l’envoi de formulaires valides et non valides :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-141">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> provides convenient events for valid and invalid form submission:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

<span data-ttu-id="6bbaa-142">Utilisez <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> pour utiliser du code personnalisé pour déclencher la validation et vérifier les valeurs des champs.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-142">Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to use custom code to trigger validation and check field values.</span></span>

<span data-ttu-id="6bbaa-143">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-143">In the following example:</span></span>

* <span data-ttu-id="6bbaa-144">La `HandleSubmit` méthode s’exécute lorsque le **`Submit`** bouton est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-144">The `HandleSubmit` method executes when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="6bbaa-145">Le formulaire est validé en appelant <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-145">The form is validated by calling <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="6bbaa-146">Du code supplémentaire est exécuté en fonction du résultat de la validation.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-146">Additional code is executed depending on the validation result.</span></span> <span data-ttu-id="6bbaa-147">Placez la logique métier dans la méthode assignée à <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-147">Place business logic in the method assigned to <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.</span></span>

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
> <span data-ttu-id="6bbaa-148">L’API Framework n’existe pas pour effacer les messages de validation directement à partir d’un <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-148">Framework API doesn't exist to clear validation messages directly from an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="6bbaa-149">Par conséquent, il n’est généralement pas recommandé d’ajouter des messages de validation à une nouvelle <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> dans un formulaire.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-149">Therefore, we don't generally recommend adding validation messages to a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> in a form.</span></span> <span data-ttu-id="6bbaa-150">Pour gérer les messages de validation, utilisez un [composant de validateur](#validator-components) avec votre [Code de validation de logique métier](#business-logic-validation), comme décrit dans cet article.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-150">To manage validation messages, use a [validator component](#validator-components) with your [business logic validation code](#business-logic-validation), as described in this article.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a><span data-ttu-id="6bbaa-151">Prise en charge des noms d’affichage</span><span class="sxs-lookup"><span data-stu-id="6bbaa-151">Display name support</span></span>

<span data-ttu-id="6bbaa-152">*Cette section s’applique à ASP.NET Core dans .NET 5 Release Candidate 1 (RC1) ou version ultérieure.*</span><span class="sxs-lookup"><span data-stu-id="6bbaa-152">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="6bbaa-153">Les composants intégrés suivants prennent en charge les noms complets avec le `DisplayName` paramètre :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-153">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

<span data-ttu-id="6bbaa-154">Dans l' `InputDate` exemple de composant suivant :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-154">In the following `InputDate` component example:</span></span>

* <span data-ttu-id="6bbaa-155">Le nom complet ( `DisplayName` ) a la valeur `birthday` .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-155">The display name (`DisplayName`) is set to `birthday`.</span></span>
* <span data-ttu-id="6bbaa-156">Le composant est lié à la `BirthDate` propriété en tant que `DateTime` type.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-156">The component is bound to the `BirthDate` property as a `DateTime` type.</span></span>

```razor
<InputDate @bind-Value="BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

<span data-ttu-id="6bbaa-157">Si l’utilisateur ne fournit pas de valeur de date, l’erreur de validation se présente comme suit :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-157">If the user doesn't provide a date value, the validation error appears as:</span></span>

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a><span data-ttu-id="6bbaa-158">Composants du validateur</span><span class="sxs-lookup"><span data-stu-id="6bbaa-158">Validator components</span></span>

<span data-ttu-id="6bbaa-159">Les composants de validateur prennent en charge la validation de formulaire en gérant un <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> pour un formulaire <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-159">Validator components support form validation by managing a <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> for a form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="6bbaa-160">L' Blazor infrastructure fournit le <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant pour attacher la prise en charge de validation aux formulaires en fonction des [attributs de validation (annotations de données)](xref:mvc/models/validation#validation-attributes).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-160">The Blazor framework provides the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component to attach validation support to forms based on [validation attributes (data annotations)](xref:mvc/models/validation#validation-attributes).</span></span> <span data-ttu-id="6bbaa-161">Créer des composants de validateur personnalisés pour traiter des messages de validation pour différents formulaires sur la même page ou dans le même formulaire à des étapes différentes du traitement d’un formulaire, par exemple la validation côté client, suivie de la validation côté serveur.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-161">Create custom validator components to process validation messages for different forms on the same page or the same form at different steps of form processing, for example client-side validation followed by server-side validation.</span></span> <span data-ttu-id="6bbaa-162">L’exemple du composant validateur présenté dans cette section, `CustomValidator` , est utilisé dans les sections suivantes de cet article :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-162">The validator component example shown in this section, `CustomValidator`, is used in the following sections of this article:</span></span>

* [<span data-ttu-id="6bbaa-163">Validation de la logique métier</span><span class="sxs-lookup"><span data-stu-id="6bbaa-163">Business logic validation</span></span>](#business-logic-validation)
* [<span data-ttu-id="6bbaa-164">Validation du serveur</span><span class="sxs-lookup"><span data-stu-id="6bbaa-164">Server validation</span></span>](#server-validation)

> [!NOTE]
> <span data-ttu-id="6bbaa-165">Les attributs de validation d’annotation de données personnalisées peuvent être utilisés à la place des composants de validateur personnalisés dans de nombreux cas.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-165">Custom data annotation validation attributes can be used instead of custom validator components in many cases.</span></span> <span data-ttu-id="6bbaa-166">Les attributs personnalisés appliqués au modèle du formulaire s’activent avec l’utilisation du <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-166">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="6bbaa-167">Lorsqu’ils sont utilisés avec la validation côté serveur, tous les attributs personnalisés appliqués au modèle doivent être exécutables sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-167">When used with server-side validation, any custom attributes applied to the model must be executable on the server.</span></span> <span data-ttu-id="6bbaa-168">Pour plus d’informations, consultez <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-168">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

<span data-ttu-id="6bbaa-169">Créez un composant de validateur à partir de <xref:Microsoft.AspNetCore.Components.ComponentBase> :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-169">Create a validator component from <xref:Microsoft.AspNetCore.Components.ComponentBase>:</span></span>

* <span data-ttu-id="6bbaa-170">Le formulaire <xref:Microsoft.AspNetCore.Components.Forms.EditContext> est un [paramètre en cascade](xref:blazor/components/cascading-values-and-parameters) du composant.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-170">The form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> is a [cascading parameter](xref:blazor/components/cascading-values-and-parameters) of the component.</span></span>
* <span data-ttu-id="6bbaa-171">Lorsque le composant validateur est initialisé, un nouveau <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> est créé pour tenir à jour la liste actuelle des erreurs de formulaire.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-171">When the validator component is initialized, a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> is created to maintain a current list of form errors.</span></span>
* <span data-ttu-id="6bbaa-172">La Banque de messages reçoit des erreurs quand le code du développeur dans le composant du formulaire appelle la `DisplayErrors` méthode.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-172">The message store receives errors when developer code in the form's component calls the `DisplayErrors` method.</span></span> <span data-ttu-id="6bbaa-173">Les erreurs sont passées à la `DisplayErrors` méthode dans un [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-173">The errors are passed to the `DisplayErrors` method in a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2).</span></span> <span data-ttu-id="6bbaa-174">Dans le dictionnaire, la clé est le nom du champ de formulaire qui contient une ou plusieurs erreurs.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-174">In the dictionary, the key is the name of the form field that has one or more errors.</span></span> <span data-ttu-id="6bbaa-175">La valeur est la liste d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-175">The value is the error list.</span></span>
* <span data-ttu-id="6bbaa-176">Les messages sont effacés lorsque l’un des éléments suivants s’est produit :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-176">Messages are cleared when any of the following have occurred:</span></span>
  * <span data-ttu-id="6bbaa-177">La validation est demandée sur le <xref:Microsoft.AspNetCore.Components.Forms.EditContext> quand l' <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> événement est déclenché.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-177">Validation is requested on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> event is raised.</span></span> <span data-ttu-id="6bbaa-178">Toutes les erreurs sont effacées.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-178">All of the errors are cleared.</span></span>
  * <span data-ttu-id="6bbaa-179">Un champ est modifié dans le formulaire lorsque l' <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> événement est déclenché.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-179">A field changes in the form when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> event is raised.</span></span> <span data-ttu-id="6bbaa-180">Seules les erreurs du champ sont effacées.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-180">Only the errors for the field are cleared.</span></span>
  * <span data-ttu-id="6bbaa-181">La `ClearErrors` méthode est appelée par le code du développeur.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-181">The `ClearErrors` method is called by developer code.</span></span> <span data-ttu-id="6bbaa-182">Toutes les erreurs sont effacées.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-182">All of the errors are cleared.</span></span>

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

> [!NOTE]
> <span data-ttu-id="6bbaa-183">Les expressions lambda anonymes sont des gestionnaires d’événements inscrits pour <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> et <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-183">Anonymous lambda expressions are registered event handlers for <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> and <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> in the preceding example.</span></span> <span data-ttu-id="6bbaa-184">Il n’est pas nécessaire d’implémenter <xref:System.IDisposable> et de désabonner les délégués d’événements dans ce scénario.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-184">It isn't necessary to implement <xref:System.IDisposable> and unsubscribe the event delegates in this scenario.</span></span> <span data-ttu-id="6bbaa-185">Pour plus d’informations, consultez <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-185">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="business-logic-validation"></a><span data-ttu-id="6bbaa-186">Validation de la logique métier</span><span class="sxs-lookup"><span data-stu-id="6bbaa-186">Business logic validation</span></span>

<span data-ttu-id="6bbaa-187">La validation de la logique métier peut être accomplie à l’aide d’un [composant validateur](#validator-components) qui reçoit des erreurs de formulaire dans un dictionnaire.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-187">Business logic validation can be accomplished with a [validator component](#validator-components) that receives form errors in a dictionary.</span></span>

<span data-ttu-id="6bbaa-188">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-188">In the following example:</span></span>

* <span data-ttu-id="6bbaa-189">Le `CustomValidator` composant de la section [composants du validateur](#validator-components) de cet article est utilisé.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-189">The `CustomValidator` component from the [Validator components](#validator-components) section of this article is used.</span></span>
* <span data-ttu-id="6bbaa-190">La validation requiert une valeur pour la description du navire ( `Description` ) si l’utilisateur sélectionne la `Defense` classification d’expédition ( `Classification` ).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-190">The validation requires a value for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="6bbaa-191">Lorsque des messages de validation sont définis dans le composant, ils sont ajoutés au validateur <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> et s’affichent dans le <xref:Microsoft.AspNetCore.Components.Forms.EditForm> :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-191">When validation messages are set in the component, they're added to the validator's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> and shown in the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>:</span></span>

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
> <span data-ttu-id="6bbaa-192">En guise d’alternative à l’utilisation de [composants de validation](#validator-components), les attributs de validation d’annotation de données peuvent être utilisés.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-192">As an alternative to using [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="6bbaa-193">Les attributs personnalisés appliqués au modèle du formulaire s’activent avec l’utilisation du <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-193">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="6bbaa-194">Lorsqu’ils sont utilisés avec la validation côté serveur, les attributs doivent être exécutables sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-194">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="6bbaa-195">Pour plus d’informations, consultez <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-195">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

## <a name="server-validation"></a><span data-ttu-id="6bbaa-196">Validation du serveur</span><span class="sxs-lookup"><span data-stu-id="6bbaa-196">Server validation</span></span>

<span data-ttu-id="6bbaa-197">La validation du serveur peut être effectuée à l’aide d’un [composant](#validator-components)de validation du serveur :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-197">Server validation can be accomplished with a server [validator component](#validator-components):</span></span>

* <span data-ttu-id="6bbaa-198">Traitez la validation côté client dans le formulaire avec le <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-198">Process client-side validation in the form with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="6bbaa-199">Quand le formulaire passe la validation côté client ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> appelée), envoie <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> à une API de serveur principal pour le traitement de formulaire.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-199">When the form passes client-side validation (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> is called), send the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> to a backend server API for form processing.</span></span>
* <span data-ttu-id="6bbaa-200">Processus de validation du modèle sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-200">Process model validation on the server.</span></span>
* <span data-ttu-id="6bbaa-201">L’API serveur comprend la validation des annotations de données d’infrastructure intégrées et la logique de validation personnalisée fournie par le développeur.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-201">The server API includes both the built-in framework data annotations validation and custom validation logic supplied by the developer.</span></span> <span data-ttu-id="6bbaa-202">Si la validation réussit sur le serveur, traite le formulaire et renvoie un code d’état de réussite (*200-OK*).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-202">If validation passes on the server, process the form and send back a success status code (*200 - OK*).</span></span> <span data-ttu-id="6bbaa-203">Si la validation échoue, retourne un code d’état d’échec (*400-demande incorrecte*) et les erreurs de validation de champ.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-203">If validation fails, return a failure status code (*400 - Bad Request*) and the field validation errors.</span></span>
* <span data-ttu-id="6bbaa-204">Désactivez le formulaire en cas de réussite ou affichez les erreurs.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-204">Either disable the form on success or display the errors.</span></span>

<span data-ttu-id="6bbaa-205">L’exemple suivant est basé sur :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-205">The following example is based on:</span></span>

* <span data-ttu-id="6bbaa-206">Solution hébergée Blazor créée par le [ Blazor modèle de projet hébergé](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-206">A hosted Blazor solution created by the [Blazor Hosted project template](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="6bbaa-207">L’exemple peut être utilisé avec l’une des solutions hébergées sécurisées Blazor décrites dans la [ Identity documentation et la sécurité](xref:blazor/security/webassembly/index#implementation-guidance).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-207">The example can be used with any of the secure hosted Blazor solutions described in the [Security and Identity documentation](xref:blazor/security/webassembly/index#implementation-guidance).</span></span>
* <span data-ttu-id="6bbaa-208">Exemple *de formulaire de base de données Starfleet Starship* dans la section [composants de formulaires intégrés](#built-in-forms-components) précédents.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-208">The *Starfleet Starship Database* form example in the preceding [Built-in forms components](#built-in-forms-components) section.</span></span>
* <span data-ttu-id="6bbaa-209">BlazorComposant du Framework <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-209">The Blazor framework's <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="6bbaa-210">`CustomValidator`Composant affiché dans la section [composants du validateur](#validator-components) .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-210">The `CustomValidator` component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="6bbaa-211">Dans l’exemple suivant, l’API du serveur valide qu’une valeur est fournie pour la description du navire ( `Description` ) si l’utilisateur sélectionne la `Defense` classification Ship ( `Classification` ).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-211">In the following example, the server API validates that a value is provided for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="6bbaa-212">Placez le `Starship` modèle dans le projet de la solution `Shared` afin que les applications client et serveur puissent utiliser le modèle.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-212">Place the `Starship` model into the solution's `Shared` project so that both the client and server apps can use the model.</span></span> <span data-ttu-id="6bbaa-213">Étant donné que le modèle requiert des annotations de données, ajoutez une référence de package pour [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) dans le `Shared` fichier projet du projet :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-213">Since the model requires data annotations, add a package reference for [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) to the `Shared` project's project file:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

<span data-ttu-id="6bbaa-214">Pour déterminer la dernière version sans version préliminaire du package, consultez l’historique des **versions** du package sur [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-214">To determine the latest non-preview version of the package, see the package **Version History** at [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span></span>

<span data-ttu-id="6bbaa-215">Dans le projet d’API serveur, ajoutez un contrôleur pour traiter les demandes de validation Starship ( `Controllers/StarshipValidation.cs` ) et renvoyer les messages de validation ayant échoué :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-215">In the server API project, add a controller to process starship validation requests (`Controllers/StarshipValidation.cs`) and return failed validation messages:</span></span>

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

<span data-ttu-id="6bbaa-216">Dans l’exemple précédent, l’espace réservé `{ASSEMBLY NAME}` est le nom de l’assembly de l’application (par exemple, `BlazorSample.Server` ).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-216">In the preceding example, the placeholder `{ASSEMBLY NAME}` is the app's assembly name (for example, `BlazorSample.Server`).</span></span>

<span data-ttu-id="6bbaa-217">Quand une erreur de validation de liaison de modèle se produit sur le serveur, un [`ApiController`](xref:web-api/index) ( <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> ) retourne normalement une [réponse de demande incorrecte par défaut](xref:web-api/index#default-badrequest-response) avec un <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-217">When a model binding validation error occurs on the server, an [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) normally returns a [default bad request response](xref:web-api/index#default-badrequest-response) with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="6bbaa-218">La réponse contient plus de données que les erreurs de validation, comme illustré dans l’exemple suivant lorsque tous les champs du formulaire *de base de données Starfleet Starship* ne sont pas envoyés et que la validation du formulaire échoue :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-218">The response contains more data than just the validation errors, as shown in the following example when all of the fields of the *Starfleet Starship Database* form aren't submitted and the form fails validation:</span></span>

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

<span data-ttu-id="6bbaa-219">Si l’API serveur retourne la réponse JSON par défaut précédente, le client peut analyser la réponse pour obtenir les enfants du `errors` nœud.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-219">If the server API returns the preceding default JSON response, it's possible for the client to parse the response to obtain the children of the `errors` node.</span></span> <span data-ttu-id="6bbaa-220">Toutefois, il est peu commode d’analyser le fichier.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-220">However, it's inconvenient to parse the file.</span></span> <span data-ttu-id="6bbaa-221">L’analyse du code JSON nécessite du code supplémentaire après <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> l’appel afin de produire un [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) des erreurs pour le traitement des erreurs de validation des formulaires.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-221">Parsing the JSON requires additional code after calling <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> in order to produce a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) of errors for forms validation error processing.</span></span> <span data-ttu-id="6bbaa-222">Dans l’idéal, l’API serveur doit renvoyer uniquement les erreurs de validation :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-222">Ideally, the server API should only return the validation errors:</span></span>

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

<span data-ttu-id="6bbaa-223">Pour modifier la réponse de l’API serveur afin qu’elle retourne uniquement les erreurs de validation, modifiez le délégué appelé sur les actions annotées avec <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-223">To modify the server API's response to make it only return the validation errors, change the delegate that's invoked on actions that are annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6bbaa-224">Pour le point de terminaison de l’API ( `/StarshipValidation` ), retournez un <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> avec <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-224">For the API endpoint (`/StarshipValidation`), return a <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> with the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>.</span></span> <span data-ttu-id="6bbaa-225">Pour tous les autres points de terminaison d’API, conservez le comportement par défaut en retournant le résultat de l’objet avec un nouveau <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-225">For any other API endpoints, preserve the default behavior by returning the object result with a new <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>:</span></span>

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

<span data-ttu-id="6bbaa-226">Pour plus d’informations, consultez <xref:web-api/handle-errors#validation-failure-error-response>.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-226">For more information, see <xref:web-api/handle-errors#validation-failure-error-response>.</span></span>

<span data-ttu-id="6bbaa-227">Dans le projet client, ajoutez le composant validateur présenté dans la section [composants du validateur](#validator-components) .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-227">In the client project, add the validator component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="6bbaa-228">Dans le projet client, le formulaire *de base de données Starfleet Starship* est mis à jour pour afficher les erreurs de validation du serveur avec l’aide du `CustomValidator` composant.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-228">In the client project, the *Starfleet Starship Database* form is updated to show server validation errors with help of the `CustomValidator` component.</span></span> <span data-ttu-id="6bbaa-229">Lorsque l’API serveur retourne des messages de validation, ils sont ajoutés au `CustomValidator` du composant <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-229">When the server API returns validation messages, they're added to the `CustomValidator` component's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>.</span></span> <span data-ttu-id="6bbaa-230">Les erreurs sont disponibles dans le formulaire <xref:Microsoft.AspNetCore.Components.Forms.EditContext> pour être affichées par le du formulaire <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-230">The errors are available in the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> for display by the form's <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>:</span></span>

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
> <span data-ttu-id="6bbaa-231">Comme alternative aux [composants de validation](#validator-components), les attributs de validation d’annotation de données peuvent être utilisés.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-231">As an alternative to [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="6bbaa-232">Les attributs personnalisés appliqués au modèle du formulaire s’activent avec l’utilisation du <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-232">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="6bbaa-233">Lorsqu’ils sont utilisés avec la validation côté serveur, les attributs doivent être exécutables sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-233">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="6bbaa-234">Pour plus d’informations, consultez <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-234">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

> [!NOTE]
> <span data-ttu-id="6bbaa-235">L’approche de validation côté serveur de cette section convient à l’un des Blazor WebAssembly exemples de solutions hébergées dans cet ensemble de documentation :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-235">The server-side validation approach in this section is suitable for any of the Blazor WebAssembly hosted solution examples in this documentation set:</span></span>
>
> * [<span data-ttu-id="6bbaa-236">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="6bbaa-236">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [<span data-ttu-id="6bbaa-237">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="6bbaa-237">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [<span data-ttu-id="6bbaa-238">Identity Serveurs</span><span class="sxs-lookup"><span data-stu-id="6bbaa-238">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="6bbaa-239">InputText en fonction de l’événement d’entrée</span><span class="sxs-lookup"><span data-stu-id="6bbaa-239">InputText based on the input event</span></span>

<span data-ttu-id="6bbaa-240">Utilisez le <xref:Microsoft.AspNetCore.Components.Forms.InputText> composant pour créer un composant personnalisé qui utilise l' `input` événement au lieu de l' `change` événement.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-240">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="6bbaa-241">Dans l’exemple suivant, le `CustomInputText` composant hérite du composant de l’infrastructure `InputText` et définit la liaison d’événements à l' `oninput` événement.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-241">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets event binding to the `oninput` event.</span></span>

<span data-ttu-id="6bbaa-242">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="6bbaa-242">`Shared/CustomInputText.razor`:</span></span>

```razor
@inherits InputText

<input @attributes="AdditionalAttributes" class="@CssClass" 
    @bind="CurrentValueAsString" @bind:event="oninput" />
```

<span data-ttu-id="6bbaa-243">Le `CustomInputText` composant peut être utilisé partout où <xref:Microsoft.AspNetCore.Components.Forms.InputText> il est utilisé :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-243">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="6bbaa-244">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="6bbaa-244">`Pages/TestForm.razor`:</span></span>

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

## <a name="radio-buttons"></a><span data-ttu-id="6bbaa-245">Cases d’option</span><span class="sxs-lookup"><span data-stu-id="6bbaa-245">Radio buttons</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6bbaa-246">Utilisez `InputRadio` les composants avec le `InputRadioGroup` composant pour créer un groupe de cases d’option.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-246">Use `InputRadio` components with the `InputRadioGroup` component to create a radio button group.</span></span> <span data-ttu-id="6bbaa-247">Dans l’exemple suivant, les propriétés sont ajoutées au `Starship` modèle décrit dans la section [composants de formulaires intégrés](#built-in-forms-components) :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-247">In the following example, properties are added to the `Starship` model described in the [Built-in forms components](#built-in-forms-components) section:</span></span>

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

<span data-ttu-id="6bbaa-248">Ajoutez le code suivant `enums` à l’application.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-248">Add the following `enums` to the app.</span></span> <span data-ttu-id="6bbaa-249">Créez un nouveau fichier pour contenir le `enums` ou ajoutez le `enums` au `Starship.cs` fichier.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-249">Create a new file to hold the `enums` or add the `enums` to the `Starship.cs` file.</span></span> <span data-ttu-id="6bbaa-250">Rendez le `enums` accessible au `Starship` modèle et à la *base de données Starfleet Starship* :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-250">Make the `enums` accessible to the `Starship` model and the *Starfleet Starship Database* form:</span></span>

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, VirginGalactic, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

<span data-ttu-id="6bbaa-251">Mettez à jour le formulaire *de base de données Starfleet Starship* décrit dans la section [composants de formulaires intégrés](#built-in-forms-components) .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-251">Update the *Starfleet Starship Database* form described in the [Built-in forms components](#built-in-forms-components) section.</span></span> <span data-ttu-id="6bbaa-252">Ajoutez les composants à produire :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-252">Add the components to produce:</span></span>

* <span data-ttu-id="6bbaa-253">Groupe de cases d’option pour le fabricant de l’expédition.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-253">A radio button group for the ship manufacturer.</span></span>
* <span data-ttu-id="6bbaa-254">Groupe de cases d’option imbriqué pour la couleur et le moteur de l’expédition.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-254">A nested radio button group for ship color and engine.</span></span>

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
> <span data-ttu-id="6bbaa-255">Si `Name` est omis, `InputRadio` les composants sont regroupés par leur ancêtre le plus récent.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-255">If `Name` is omitted, `InputRadio` components are grouped by their most recent ancestor.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="6bbaa-256">Lorsque vous utilisez des cases d’option dans un formulaire, la liaison de données est gérée différemment des autres éléments, car les cases d’option sont évaluées en tant que groupe.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-256">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="6bbaa-257">La valeur de chaque case d’option est fixe, mais la valeur du groupe de cases d’option est la valeur de la case d’option sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-257">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="6bbaa-258">L’exemple suivant montre comment :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-258">The following example shows how to:</span></span>

* <span data-ttu-id="6bbaa-259">Gérer la liaison de données pour un groupe de cases d’option.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-259">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="6bbaa-260">Prendre en charge la validation à l’aide d’un `InputRadio` composant personnalisé.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-260">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="6bbaa-261">L’exemple suivant <xref:Microsoft.AspNetCore.Components.Forms.EditForm> utilise le `InputRadio` composant précédent pour obtenir et valider une évaluation de l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-261">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="6bbaa-262">Liaison `<select>` d’options d’élément aux valeurs d’objet C# `null`</span><span class="sxs-lookup"><span data-stu-id="6bbaa-262">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="6bbaa-263">Il n’existe pas de méthode raisonnable pour représenter une `<select>` valeur d’option d’élément en tant que valeur d’objet C# `null` , car :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-263">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="6bbaa-264">Les attributs HTML ne peuvent pas avoir de `null` valeurs.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-264">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="6bbaa-265">L’équivalent le plus proche de `null` en HTML est l’absence de l' `value` attribut HTML de l' `<option>` élément.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-265">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="6bbaa-266">Quand vous sélectionnez un `<option>` `value` élément sans attribut, le navigateur traite la valeur comme le *contenu de texte* de cet `<option>` élément.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-266">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="6bbaa-267">L' Blazor infrastructure ne tente pas de supprimer le comportement par défaut, car cela impliquerait :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-267">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="6bbaa-268">Création d’une chaîne de solutions de contournement de cas spéciaux dans le Framework.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-268">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="6bbaa-269">Modifications avec rupture apportées au comportement actuel de l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-269">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6bbaa-270">L’équivalent le plus plausible `null` en HTML est une *chaîne vide* `value` .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-270">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="6bbaa-271">Le Blazor Framework gère les `null` conversions de chaînes vides pour une liaison bidirectionnelle vers une `<select>` valeur de.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-271">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="6bbaa-272">L' Blazor infrastructure ne gère pas automatiquement les `null` conversions de chaînes vides lors de la tentative de liaison bidirectionnelle à la `<select>` valeur de.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-272">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="6bbaa-273">Pour plus d’informations, consultez [fixer `<select>` la liaison à une valeur null (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-273">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="6bbaa-274">Prise en charge de la validation</span><span class="sxs-lookup"><span data-stu-id="6bbaa-274">Validation support</span></span>

<span data-ttu-id="6bbaa-275">Le <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant attache la prise en charge de la validation à l’aide d’annotations de données à la Cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-275">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="6bbaa-276">L’activation de la prise en charge de la validation à l’aide d’annotations de données requiert ce geste explicite.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-276">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="6bbaa-277">Pour utiliser un système de validation différent des annotations de données, remplacez <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> par une implémentation personnalisée.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-277">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="6bbaa-278">L’implémentation de ASP.NET Core est disponible pour l’inspection dans la source de référence : [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-278">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="6bbaa-279">Les liens précédents vers la source de référence fournissent du code à partir de la branche du référentiel `master` , qui représente le développement actuel de l’unité du produit pour la prochaine version de ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-279">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="6bbaa-280">Pour sélectionner la branche pour une version différente, utilisez le sélecteur de branche GitHub (par exemple `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-280">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

<span data-ttu-id="6bbaa-281">Blazor effectue deux types de validation :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-281">Blazor performs two types of validation:</span></span>

* <span data-ttu-id="6bbaa-282">La *validation de champ* est effectuée lorsque l’utilisateur quitte un champ.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-282">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="6bbaa-283">Pendant la validation de champ, le <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant associe tous les résultats de validation signalés au champ.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-283">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="6bbaa-284">La *validation du modèle* est effectuée lorsque l’utilisateur envoie le formulaire.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-284">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="6bbaa-285">Lors de la validation du modèle, le <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant tente de déterminer le champ en fonction du nom du membre que le résultat de validation signale.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-285">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="6bbaa-286">Les résultats de validation qui ne sont pas associés à un membre individuel sont associés au modèle plutôt qu’à un champ.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-286">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="6bbaa-287">Résumé des validations et composants de message de validation</span><span class="sxs-lookup"><span data-stu-id="6bbaa-287">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="6bbaa-288">Le <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> composant résume tous les messages de validation, ce qui est similaire au [tag Helper de résumé de validation](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="6bbaa-288">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="6bbaa-289">Messages de validation de sortie pour un modèle spécifique avec le `Model` paramètre :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-289">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="6bbaa-290">Le <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> composant affiche des messages de validation pour un champ spécifique, qui est similaire [au tag Helper de message de validation](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-290">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="6bbaa-291">Spécifiez le champ pour la validation avec l' `For` attribut et une expression lambda qui nomme la propriété de modèle :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-291">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="6bbaa-292">Les <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> composants et prennent en charge des attributs arbitraires.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-292">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="6bbaa-293">Tout attribut qui ne correspond pas à un paramètre de composant est ajouté à l' `<div>` élément ou généré `<ul>` .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-293">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="6bbaa-294">Contrôler le style des messages de validation dans la feuille de style de l’application ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-294">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="6bbaa-295">La classe par défaut `validation-message` définit la couleur du texte des messages de validation sur rouge :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-295">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="6bbaa-296">Attributs de validation personnalisés</span><span class="sxs-lookup"><span data-stu-id="6bbaa-296">Custom validation attributes</span></span>

<span data-ttu-id="6bbaa-297">Pour vous assurer qu’un résultat de validation est correctement associé à un champ lors de l’utilisation d’un [attribut de validation personnalisé](xref:mvc/models/validation#custom-attributes), transmettez le contexte de validation <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> lors de la création du <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-297">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class CustomValidator : ValidationAttribute
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
> <span data-ttu-id="6bbaa-298"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> a la valeur `null`.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-298"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="6bbaa-299">L’injection de services pour la validation dans la `IsValid` méthode n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-299">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a><span data-ttu-id="6bbaa-300">Attributs de classe de validation personnalisée</span><span class="sxs-lookup"><span data-stu-id="6bbaa-300">Custom validation class attributes</span></span>

<span data-ttu-id="6bbaa-301">Les noms des classes de validation personnalisées sont utiles lors de l’intégration avec des frameworks CSS, tels que [bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-301">Custom validation class names are useful when integrating with CSS frameworks, such as [Bootstrap](https://getbootstrap.com/).</span></span>

<span data-ttu-id="6bbaa-302">Pour spécifier des noms de classes de validation personnalisées :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-302">To specify custom validation class names:</span></span>

* <span data-ttu-id="6bbaa-303">Fournissez des styles CSS pour la validation personnalisée.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-303">Provide CSS styles for custom validation.</span></span> <span data-ttu-id="6bbaa-304">Dans l’exemple suivant, les styles valides et non valides sont spécifiés :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-304">In the following example, valid and invalid styles are specified:</span></span>

```css
.validField {
    border-color: lawngreen;
}

.invalidField {
    background-color: tomato;
}
```

* <span data-ttu-id="6bbaa-305">Créez une classe dérivée de `FieldCssClassProvider` qui recherche les messages de validation de champ et applique le style valide ou non valide approprié :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-305">Create a class derived from `FieldCssClassProvider` that checks for field validation messages and applies the appropriate valid or invalid style:</span></span>

```csharp
using System.Linq;
using Microsoft.AspNetCore.Components.Forms;

public class MyFieldClassProvider : FieldCssClassProvider
{
    public override string GetFieldCssClass(EditContext editContext, 
        in FieldIdentifier fieldIdentifier)
    {
        var isValid = !editContext.GetValidationMessages(fieldIdentifier).Any();

        return isValid ? "validField" : "invalidField";
    }
}
```

* <span data-ttu-id="6bbaa-306">Définissez la classe sur l’instance du formulaire <xref:Microsoft.AspNetCore.Components.Forms.EditContext> :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-306">Set the class on the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> instance:</span></span>

```razor
...

<EditForm EditContext="@editContext" OnValidSubmit="@HandleValidSubmit">
    ...
</EditForm>

...

@code {
    private EditContext editContext;
    private Model model = new Model();

    protected override void OnInitialized()
    {
        editContext = new EditContext(model);
        editContext.SetFieldCssClassProvider(new MyFieldClassProvider());
    }

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="6bbaa-307">L’exemple précédent vérifie la validité de tous les champs de formulaire et applique un style à chaque champ.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-307">The preceding example checks the validity of all form fields and applies a style to each field.</span></span> <span data-ttu-id="6bbaa-308">Si le formulaire doit uniquement appliquer des styles personnalisés à un sous-ensemble des champs, effectuez les styles d’application de façon `MyFieldClassProvider` conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-308">If the form should only apply custom styles to a subset of the fields, make `MyFieldClassProvider` apply styles conditionally.</span></span> <span data-ttu-id="6bbaa-309">L’exemple suivant applique uniquement un style au `Identifier` champ :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-309">The following example only applies a style to the `Identifier` field:</span></span>

```csharp
public class MyFieldClassProvider : FieldCssClassProvider
{
    public override string GetFieldCssClass(EditContext editContext,
        in FieldIdentifier fieldIdentifier)
    {
        if (fieldIdentifier.FieldName == "Identifier")
        {
            var isValid = !editContext.GetValidationMessages(fieldIdentifier).Any();

            return isValid ? "validField" : "invalidField";
        }

        return string.Empty;
    }
}
```

::: moniker-end

### <a name="blazor-data-annotations-validation-package"></a><span data-ttu-id="6bbaa-310">Blazor package de validation des annotations de données</span><span class="sxs-lookup"><span data-stu-id="6bbaa-310">Blazor data annotations validation package</span></span>

<span data-ttu-id="6bbaa-311">Le [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) est un package qui remplit les lacunes de l’expérience de validation à l’aide du <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-311">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="6bbaa-312">Le package est actuellement *expérimental*.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-312">The package is currently *experimental*.</span></span>

> [!NOTE]
> <span data-ttu-id="6bbaa-313">Le [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package a une version la plus récente de la version *Release candidate* sur [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continuez à utiliser le package *expérimental* release candidate pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-313">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package has a latest version of *release candidate* at [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continue to use the *experimental* release candidate package at this time.</span></span> <span data-ttu-id="6bbaa-314">L’assembly du package peut être déplacé vers l’infrastructure ou le runtime dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-314">The package's assembly might be moved to either the framework or the runtime in a future release.</span></span> <span data-ttu-id="6bbaa-315">Regardez le [référentiel GitHub d’annonces](https://github.com/aspnet/Announcements), le [référentiel GitHub dotnet/aspnetcore](https://github.com/dotnet/aspnetcore), ou la section de cette rubrique pour obtenir des mises à jour supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-315">Watch the [Announcements GitHub repository](https://github.com/aspnet/Announcements), the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore), or this topic section for further updates.</span></span>

::: moniker range="< aspnetcore-5.0"

### <a name="compareproperty-attribute"></a><span data-ttu-id="6bbaa-316">Attribut `[CompareProperty]`</span><span class="sxs-lookup"><span data-stu-id="6bbaa-316">`[CompareProperty]` attribute</span></span>

<span data-ttu-id="6bbaa-317">Le <xref:System.ComponentModel.DataAnnotations.CompareAttribute> ne fonctionne pas correctement avec le <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> composant, car il n’associe pas le résultat de la validation à un membre spécifique.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-317">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="6bbaa-318">Cela peut entraîner un comportement incohérent entre la validation au niveau du champ et le moment où la totalité du modèle est validée sur une soumission.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-318">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="6bbaa-319">Le [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package *expérimental* introduit un attribut de validation supplémentaire, `ComparePropertyAttribute` , qui contourne ces limitations.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-319">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="6bbaa-320">Dans une Blazor application, `[CompareProperty]` est un remplacement direct de l' [ `[Compare]` attribut](xref:System.ComponentModel.DataAnnotations.CompareAttribute).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-320">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]` attribute](xref:System.ComponentModel.DataAnnotations.CompareAttribute).</span></span>

::: moniker-end

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="6bbaa-321">Modèles imbriqués, types de collection et types complexes</span><span class="sxs-lookup"><span data-stu-id="6bbaa-321">Nested models, collection types, and complex types</span></span>

<span data-ttu-id="6bbaa-322">Blazor prend en charge la validation de l’entrée de formulaire à l’aide d’annotations de données avec le intégré <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-322">Blazor provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="6bbaa-323">Toutefois, l' <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> valide uniquement les propriétés de niveau supérieur du modèle lié au formulaire qui ne sont pas des propriétés de type collection ou complexe.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-323">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="6bbaa-324">Pour valider le graphique d’objets entier du modèle lié, y compris les propriétés de type collection et de type complexe, utilisez le `ObjectGraphDataAnnotationsValidator` fourni par le package *expérimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-324">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="6bbaa-325">Annotez les propriétés de modèle avec `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-325">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="6bbaa-326">Dans les classes de modèle suivantes, la `ShipDescription` classe contient des annotations de données supplémentaires à valider lorsque le modèle est lié au formulaire :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-326">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="6bbaa-327">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="6bbaa-327">`Starship.cs`:</span></span>

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

<span data-ttu-id="6bbaa-328">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="6bbaa-328">`ShipDescription.cs`:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="6bbaa-329">Activer le bouton Envoyer en fonction de la validation de formulaire</span><span class="sxs-lookup"><span data-stu-id="6bbaa-329">Enable the submit button based on form validation</span></span>

<span data-ttu-id="6bbaa-330">Pour activer et désactiver le bouton Envoyer en fonction de la validation de formulaire :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-330">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="6bbaa-331">Utilisez le du formulaire <xref:Microsoft.AspNetCore.Components.Forms.EditContext> pour assigner le modèle lorsque le composant est initialisé.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-331">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="6bbaa-332">Validez le formulaire dans le rappel du contexte <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> pour activer et désactiver le bouton Envoyer.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-332">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="6bbaa-333">Implémentez <xref:System.IDisposable> et annulez l’abonnement du gestionnaire d’événements dans la `Dispose` méthode.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-333">Implement <xref:System.IDisposable> and unsubscribe the event handler in the `Dispose` method.</span></span> <span data-ttu-id="6bbaa-334">Pour plus d’informations, consultez <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-334">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

> [!NOTE]
> <span data-ttu-id="6bbaa-335">Quand vous utilisez un <xref:Microsoft.AspNetCore.Components.Forms.EditContext> , n’assignez pas également <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> à <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-335">When using an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, don't also assign a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span>

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

<span data-ttu-id="6bbaa-336">Dans l’exemple précédent, affectez à la valeur `formInvalid` `false` si :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-336">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="6bbaa-337">Le formulaire est préchargé avec des valeurs par défaut valides.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-337">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="6bbaa-338">Vous voulez que le bouton envoyer soit activé lors du chargement du formulaire.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-338">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="6bbaa-339">L’un des effets secondaires de l’approche précédente est qu’un <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> composant est rempli avec des champs non valides une fois que l’utilisateur interagit avec un champ quelconque.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-339">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="6bbaa-340">Ce scénario peut être traité de l’une des manières suivantes :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-340">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="6bbaa-341">N’utilisez pas un <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> composant sur le formulaire.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-341">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="6bbaa-342">Rendre le <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> composant visible lorsque le bouton Envoyer est sélectionné (par exemple, dans une `HandleValidSubmit` méthode).</span><span class="sxs-lookup"><span data-stu-id="6bbaa-342">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="6bbaa-343">Dépanner</span><span class="sxs-lookup"><span data-stu-id="6bbaa-343">Troubleshoot</span></span>

> <span data-ttu-id="6bbaa-344">InvalidOperationException : EditForm requiert un paramètre de modèle, ou un paramètre EditContext, mais pas les deux.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-344">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="6bbaa-345">Vérifiez que <xref:Microsoft.AspNetCore.Components.Forms.EditForm> a un <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **ou** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="6bbaa-345">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="6bbaa-346">N’utilisez pas les deux pour le même formulaire.</span><span class="sxs-lookup"><span data-stu-id="6bbaa-346">Don't use both for the same form.</span></span>

<span data-ttu-id="6bbaa-347">Quand vous assignez un <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> au formulaire, vérifiez que le type de modèle est instancié, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="6bbaa-347">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a><span data-ttu-id="6bbaa-348">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="6bbaa-348">Additional resources</span></span>

* <xref:blazor/file-uploads>

::: moniker-end

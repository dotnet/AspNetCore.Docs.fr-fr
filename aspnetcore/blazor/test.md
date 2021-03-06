---
title: Tester des composants dans ASP.NET Core Blazor
author: guardrex
description: Découvrez comment tester componments dans les Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
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
uid: blazor/test
ms.openlocfilehash: 1a7b1114934f4fe7006d60bdbd0f06792d2c6935
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394549"
---
# <a name="test-components-in-aspnet-core-blazor"></a><span data-ttu-id="67a72-103">Tester des composants dans ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="67a72-103">Test components in ASP.NET Core Blazor</span></span>

<span data-ttu-id="67a72-104">Par : [Egil Hansen](https://egilhansen.com/)</span><span class="sxs-lookup"><span data-stu-id="67a72-104">By: [Egil Hansen](https://egilhansen.com/)</span></span>

<span data-ttu-id="67a72-105">Le test est un aspect important de la création de logiciels stables et gérables.</span><span class="sxs-lookup"><span data-stu-id="67a72-105">Testing is an important aspect of building stable and maintainable software.</span></span>

<span data-ttu-id="67a72-106">Pour tester un Blazor composant, le *composant* testé (couper) est :</span><span class="sxs-lookup"><span data-stu-id="67a72-106">To test a Blazor component, the *Component Under Test* (CUT) is:</span></span>

* <span data-ttu-id="67a72-107">Rendu avec l’entrée pertinente pour le test.</span><span class="sxs-lookup"><span data-stu-id="67a72-107">Rendered with relevant input for the test.</span></span>
* <span data-ttu-id="67a72-108">Selon le type de test effectué, éventuellement soumis à une interaction ou à une modification.</span><span class="sxs-lookup"><span data-stu-id="67a72-108">Depending on the type of test performed, possibly subject to interaction or modification.</span></span> <span data-ttu-id="67a72-109">Par exemple, les gestionnaires d’événements peuvent être déclenchés, par exemple un `onclick` événement pour un bouton.</span><span class="sxs-lookup"><span data-stu-id="67a72-109">For example, event handlers can be triggered, such as an `onclick` event for a button.</span></span>
* <span data-ttu-id="67a72-110">Examen des valeurs attendues.</span><span class="sxs-lookup"><span data-stu-id="67a72-110">Inspected for expected values.</span></span>

## <a name="test-approaches"></a><span data-ttu-id="67a72-111">Approches de test</span><span class="sxs-lookup"><span data-stu-id="67a72-111">Test approaches</span></span>

<span data-ttu-id="67a72-112">Deux approches courantes pour tester les composants sont les tests de Blazor bout en bout (E2E) et les tests unitaires :</span><span class="sxs-lookup"><span data-stu-id="67a72-112">Two common approaches for testing Blazor components are end-to-end (E2E) testing and unit testing:</span></span>

* <span data-ttu-id="67a72-113">**Tests unitaires**: les [tests unitaires](/dotnet/core/testing/) sont écrits à l’aide d’une bibliothèque de tests unitaires qui fournit :</span><span class="sxs-lookup"><span data-stu-id="67a72-113">**Unit testing**: [Unit tests](/dotnet/core/testing/) are written with a unit testing library that provides:</span></span>
  * <span data-ttu-id="67a72-114">Rendu des composants.</span><span class="sxs-lookup"><span data-stu-id="67a72-114">Component rendering.</span></span>
  * <span data-ttu-id="67a72-115">Inspection de la sortie et de l’état des composants.</span><span class="sxs-lookup"><span data-stu-id="67a72-115">Inspection of component output and state.</span></span>
  * <span data-ttu-id="67a72-116">Déclenchement de gestionnaires d’événements et de méthodes de cycle de vie.</span><span class="sxs-lookup"><span data-stu-id="67a72-116">Triggering of event handlers and life cycle methods.</span></span>
  * <span data-ttu-id="67a72-117">Assertions que le comportement du composant est correct.</span><span class="sxs-lookup"><span data-stu-id="67a72-117">Assertions that component behavior is correct.</span></span>

  <span data-ttu-id="67a72-118">[bUnit](https://github.com/egil/bUnit) est un exemple de bibliothèque qui active les Razor tests unitaires de composants.</span><span class="sxs-lookup"><span data-stu-id="67a72-118">[bUnit](https://github.com/egil/bUnit) is an example of a library that enables Razor component unit testing.</span></span>

* <span data-ttu-id="67a72-119">**Test E2E**: un testeur exécute une Blazor application contenant la coupe et automatise une instance de navigateur.</span><span class="sxs-lookup"><span data-stu-id="67a72-119">**E2E testing**: A test runner runs a Blazor app containing the CUT and automates a browser instance.</span></span> <span data-ttu-id="67a72-120">L’outil de test inspecte et interagit avec la coupe dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="67a72-120">The testing tool inspects and interacts with the CUT through the browser.</span></span> <span data-ttu-id="67a72-121">Le [sélénium](https://github.com/SeleniumHQ/selenium) est un exemple de Framework de test E2E qui peut être utilisé avec des Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="67a72-121">[Selenium](https://github.com/SeleniumHQ/selenium) is an example of an E2E testing framework that can be used with Blazor apps.</span></span>

<span data-ttu-id="67a72-122">Dans les tests unitaires, seul le Blazor composant ( Razor /c #) est impliqué.</span><span class="sxs-lookup"><span data-stu-id="67a72-122">In unit testing, only the Blazor component (Razor/C#) is involved.</span></span> <span data-ttu-id="67a72-123">Les dépendances externes, telles que les services et l’interopérabilité de JS, doivent être simulées.</span><span class="sxs-lookup"><span data-stu-id="67a72-123">External dependencies, such as services and JS interop, must be mocked.</span></span> <span data-ttu-id="67a72-124">Dans E2E testing, le Blazor composant et toute l’infrastructure auxiliaire font partie du test, y compris CSS, js et les API DOM et de navigateur.</span><span class="sxs-lookup"><span data-stu-id="67a72-124">In E2E testing, the Blazor component and all of it's auxiliary infrastructure are part of the test, including CSS, JS, and the DOM and browser APIs.</span></span>

<span data-ttu-id="67a72-125">La *portée de test* décrit l’étendue des tests.</span><span class="sxs-lookup"><span data-stu-id="67a72-125">*Test scope* describes how extensive the tests are.</span></span> <span data-ttu-id="67a72-126">L’étendue de test a généralement une influence sur la vitesse des tests.</span><span class="sxs-lookup"><span data-stu-id="67a72-126">Test scope typically has an influence on the speed of the tests.</span></span> <span data-ttu-id="67a72-127">Les tests unitaires s’exécutent sur un sous-ensemble des sous-systèmes de l’application et s’exécutent généralement en millisecondes.</span><span class="sxs-lookup"><span data-stu-id="67a72-127">Unit tests run on a subset of the app's subsystems and usually execute in milliseconds.</span></span> <span data-ttu-id="67a72-128">Les tests E2E, qui testent un large groupe de sous-systèmes de l’application, peuvent prendre plusieurs secondes.</span><span class="sxs-lookup"><span data-stu-id="67a72-128">E2E tests, which test a broad group of the app's subsystems, can take several seconds to complete.</span></span> 

<span data-ttu-id="67a72-129">Les tests unitaires fournissent également l’accès à l’instance de la coupe, ce qui permet l’inspection et la vérification de l’état interne du composant.</span><span class="sxs-lookup"><span data-stu-id="67a72-129">Unit testing also provides access to the instance of the CUT, allowing for inspection and verification of the component's internal state.</span></span> <span data-ttu-id="67a72-130">Cela n’est généralement pas possible dans les tests E2E.</span><span class="sxs-lookup"><span data-stu-id="67a72-130">This normally isn't possible in E2E testing.</span></span>

<span data-ttu-id="67a72-131">En ce qui concerne l’environnement du composant, les tests E2E doivent s’assurer que l’état environnemental attendu a été atteint avant le début de la vérification.</span><span class="sxs-lookup"><span data-stu-id="67a72-131">With regard to the component's environment, E2E tests must make sure that the expected environmental state has been reached before verification starts.</span></span> <span data-ttu-id="67a72-132">Dans le cas contraire, le résultat est imprévisible.</span><span class="sxs-lookup"><span data-stu-id="67a72-132">Otherwise, the result is unpredictable.</span></span> <span data-ttu-id="67a72-133">Dans les tests unitaires, le rendu de la coupe et du cycle de vie du test est plus intégré, ce qui améliore la stabilité des tests.</span><span class="sxs-lookup"><span data-stu-id="67a72-133">In unit testing, the rendering of the CUT and the life cycle of the test are more integrated, which improves test stability.</span></span>

<span data-ttu-id="67a72-134">Les tests E2E impliquent le lancement de plusieurs processus, l’e/s réseau et le disque, ainsi que d’autres activités du sous-système qui conduisent souvent à une mauvaise fiabilité des tests.</span><span class="sxs-lookup"><span data-stu-id="67a72-134">E2E testing involves launching multiple processes, network and disk I/O, and other subsystem activity that often lead to poor test reliability.</span></span> <span data-ttu-id="67a72-135">Les tests unitaires sont généralement isolés de ces types de problèmes.</span><span class="sxs-lookup"><span data-stu-id="67a72-135">Unit tests are typically insulated from these sorts of issues.</span></span>

<span data-ttu-id="67a72-136">Le tableau suivant résume la différence entre les deux approches de test.</span><span class="sxs-lookup"><span data-stu-id="67a72-136">The following table summarizes the difference between the two testing approaches.</span></span>

| <span data-ttu-id="67a72-137">Fonctionnalité</span><span class="sxs-lookup"><span data-stu-id="67a72-137">Capability</span></span>                       | <span data-ttu-id="67a72-138">Test des unités</span><span class="sxs-lookup"><span data-stu-id="67a72-138">Unit testing</span></span>                     | <span data-ttu-id="67a72-139">Test E2E</span><span class="sxs-lookup"><span data-stu-id="67a72-139">E2E testing</span></span>                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| <span data-ttu-id="67a72-140">Étendue de test</span><span class="sxs-lookup"><span data-stu-id="67a72-140">Test scope</span></span>                       | <span data-ttu-id="67a72-141">Blazor composant ( Razor /c #) uniquement</span><span class="sxs-lookup"><span data-stu-id="67a72-141">Blazor component (Razor/C#) only</span></span> | <span data-ttu-id="67a72-142">Blazor composant ( Razor /c #) avec CSS/js</span><span class="sxs-lookup"><span data-stu-id="67a72-142">Blazor component (Razor/C#) with CSS/JS</span></span> |
| <span data-ttu-id="67a72-143">Durée d’exécution des tests</span><span class="sxs-lookup"><span data-stu-id="67a72-143">Test execution time</span></span>              | <span data-ttu-id="67a72-144">Millisecondes</span><span class="sxs-lookup"><span data-stu-id="67a72-144">Milliseconds</span></span>                     | <span data-ttu-id="67a72-145">Secondes</span><span class="sxs-lookup"><span data-stu-id="67a72-145">Seconds</span></span>                                 |
| <span data-ttu-id="67a72-146">Accès à l’instance du composant</span><span class="sxs-lookup"><span data-stu-id="67a72-146">Access to the component instance</span></span> | <span data-ttu-id="67a72-147">Oui</span><span class="sxs-lookup"><span data-stu-id="67a72-147">Yes</span></span>                              | <span data-ttu-id="67a72-148">Non</span><span class="sxs-lookup"><span data-stu-id="67a72-148">No</span></span>                                      |
| <span data-ttu-id="67a72-149">Sensible à l’environnement</span><span class="sxs-lookup"><span data-stu-id="67a72-149">Sensitive to the environment</span></span>     | <span data-ttu-id="67a72-150">Non</span><span class="sxs-lookup"><span data-stu-id="67a72-150">No</span></span>                               | <span data-ttu-id="67a72-151">Oui</span><span class="sxs-lookup"><span data-stu-id="67a72-151">Yes</span></span>                                     |
| <span data-ttu-id="67a72-152">Fiabilité</span><span class="sxs-lookup"><span data-stu-id="67a72-152">Reliability</span></span>                      | <span data-ttu-id="67a72-153">Plus fiable</span><span class="sxs-lookup"><span data-stu-id="67a72-153">More reliable</span></span>                    | <span data-ttu-id="67a72-154">Moins fiable</span><span class="sxs-lookup"><span data-stu-id="67a72-154">Less reliable</span></span>                           |

## <a name="choose-the-most-appropriate-test-approach"></a><span data-ttu-id="67a72-155">Choisir l’approche de test la plus appropriée</span><span class="sxs-lookup"><span data-stu-id="67a72-155">Choose the most appropriate test approach</span></span>

<span data-ttu-id="67a72-156">Examinez le scénario lorsque vous choisissez le type de test à effectuer.</span><span class="sxs-lookup"><span data-stu-id="67a72-156">Consider the scenario when choosing the type of testing to perform.</span></span> <span data-ttu-id="67a72-157">Certaines considérations sont décrites dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="67a72-157">Some considerations are described in the following table.</span></span>

| <span data-ttu-id="67a72-158">Scénario</span><span class="sxs-lookup"><span data-stu-id="67a72-158">Scenario</span></span> | <span data-ttu-id="67a72-159">Approche suggérée</span><span class="sxs-lookup"><span data-stu-id="67a72-159">Suggested approach</span></span> | <span data-ttu-id="67a72-160">Notes</span><span class="sxs-lookup"><span data-stu-id="67a72-160">Remarks</span></span> |
| -------- | ------------------ | ------- |
| <span data-ttu-id="67a72-161">Composant sans logique d’interopérabilité JS</span><span class="sxs-lookup"><span data-stu-id="67a72-161">Component without JS interop logic</span></span> | <span data-ttu-id="67a72-162">Test des unités</span><span class="sxs-lookup"><span data-stu-id="67a72-162">Unit testing</span></span> | <span data-ttu-id="67a72-163">Lorsqu’il n’existe aucune dépendance vis-à-vis de l’interopérabilité de JS dans un Blazor composant, le composant peut être testé sans accès à js ou à l’API DOM.</span><span class="sxs-lookup"><span data-stu-id="67a72-163">When there's no dependency on JS interop in a Blazor component, the component can be tested without access to JS or the DOM API.</span></span> <span data-ttu-id="67a72-164">Dans ce scénario, il n’y a aucun inconvénient à choisir des tests unitaires.</span><span class="sxs-lookup"><span data-stu-id="67a72-164">In this scenario, there are no disadvantages to choosing unit testing.</span></span> |
| <span data-ttu-id="67a72-165">Composant avec une logique d’interopérabilité JS simple</span><span class="sxs-lookup"><span data-stu-id="67a72-165">Component with simple JS interop logic</span></span> | <span data-ttu-id="67a72-166">Test des unités</span><span class="sxs-lookup"><span data-stu-id="67a72-166">Unit testing</span></span> | <span data-ttu-id="67a72-167">Il est courant que les composants interrogent le DOM ou déclenchent des animations par le biais de l’interopérabilité de JS.</span><span class="sxs-lookup"><span data-stu-id="67a72-167">It's common for components to query the DOM or trigger animations through JS interop.</span></span> <span data-ttu-id="67a72-168">Les tests unitaires sont généralement préférés dans ce scénario, car il est facile de simuler l’interaction JS par le biais de l' <xref:Microsoft.JSInterop.IJSRuntime> interface.</span><span class="sxs-lookup"><span data-stu-id="67a72-168">Unit testing is usually preferred in this scenario, since it's straightforward to mock the JS interaction through the <xref:Microsoft.JSInterop.IJSRuntime> interface.</span></span> |
| <span data-ttu-id="67a72-169">Composant qui dépend d’un code JS complexe</span><span class="sxs-lookup"><span data-stu-id="67a72-169">Component that depends on complex JS code</span></span> | <span data-ttu-id="67a72-170">Test unitaire et test JS distinct</span><span class="sxs-lookup"><span data-stu-id="67a72-170">Unit testing and separate JS testing</span></span> | <span data-ttu-id="67a72-171">Si un composant utilise l’interopérabilité JS pour appeler une bibliothèque JS volumineuse ou complexe, mais que l’interaction entre la Blazor bibliothèque Component et js est simple, la meilleure approche consiste à traiter la bibliothèque Component et JS ou le code comme deux parties distinctes et à tester chacune individuellement.</span><span class="sxs-lookup"><span data-stu-id="67a72-171">If a component uses JS interop to call a large or complex JS library but the interaction between the Blazor component and JS library is simple, then the best approach is likely to treat the component and JS library or code as two separate parts and test each individually.</span></span> <span data-ttu-id="67a72-172">Testez le Blazor composant à l’aide d’une bibliothèque de tests unitaires, puis testez js avec une bibliothèque de tests js.</span><span class="sxs-lookup"><span data-stu-id="67a72-172">Test the Blazor component with a unit testing library, and test the JS with a JS testing library.</span></span> |
| <span data-ttu-id="67a72-173">Composant avec une logique qui dépend de la manipulation JS du DOM du navigateur</span><span class="sxs-lookup"><span data-stu-id="67a72-173">Component with logic that depends on JS manipulation of the browser DOM</span></span> | <span data-ttu-id="67a72-174">Test E2E</span><span class="sxs-lookup"><span data-stu-id="67a72-174">E2E testing</span></span> | <span data-ttu-id="67a72-175">Lorsque la fonctionnalité d’un composant dépend de JS et de sa manipulation du DOM, vérifiez à la fois le code JS et le Blazor code dans un test E2E.</span><span class="sxs-lookup"><span data-stu-id="67a72-175">When a component's functionality is dependent on JS and its manipulation of the DOM, verify both the JS and Blazor code together in an E2E test.</span></span> <span data-ttu-id="67a72-176">Il s’agit de l’approche Blazor adoptée par les développeurs de l’infrastructure avec la Blazor logique de rendu du navigateur de, qui a un code C# et JS étroitement couplé.</span><span class="sxs-lookup"><span data-stu-id="67a72-176">This is the approach that the Blazor framework developers have taken with Blazor's browser rendering logic, which has tightly-coupled C# and JS code.</span></span> <span data-ttu-id="67a72-177">Le code C# et JS doit fonctionner ensemble pour restituer correctement les Blazor composants dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="67a72-177">The C# and JS code must work together to correctly render Blazor components in a browser.</span></span>
| <span data-ttu-id="67a72-178">Composant qui dépend d’une bibliothèque de composants tiers avec des dépendances difficiles à simuler</span><span class="sxs-lookup"><span data-stu-id="67a72-178">Component that depends on 3rd party component library with hard-to-mock dependencies</span></span> | <span data-ttu-id="67a72-179">Test E2E</span><span class="sxs-lookup"><span data-stu-id="67a72-179">E2E testing</span></span> | <span data-ttu-id="67a72-180">Lorsque la fonctionnalité d’un composant dépend d’une bibliothèque de composants tiers qui a des dépendances difficile à simuler, telles que l’interopérabilité de JS, le test E2E peut être la seule option pour tester le composant.</span><span class="sxs-lookup"><span data-stu-id="67a72-180">When a component's functionality is dependent on a 3rd party component library that has hard-to-mock dependencies, such as JS interop, E2E testing might be the only option to test the component.</span></span> |

## <a name="test-components-with-bunit"></a><span data-ttu-id="67a72-181">Tester des composants avec bUnit</span><span class="sxs-lookup"><span data-stu-id="67a72-181">Test components with bUnit</span></span>

<span data-ttu-id="67a72-182">Il n’existe pas d’infrastructure de test Microsoft officielle pour Blazor , mais le projet [bUnit](https://github.com/egil/bUnit) basé sur la communauté offre un moyen pratique pour les composants de test unitaire Blazor .</span><span class="sxs-lookup"><span data-stu-id="67a72-182">There's no official Microsoft testing framework for Blazor, but the community-driven project [bUnit](https://github.com/egil/bUnit) provides a convenient way to unit test Blazor components.</span></span>

> [!NOTE]
> <span data-ttu-id="67a72-183">bUnit est une bibliothèque de tests tierces et n’est pas prise en charge ou gérée par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="67a72-183">bUnit is a third-party testing library and isn't supported or maintained by Microsoft.</span></span>

<span data-ttu-id="67a72-184">bUnit fonctionne avec des infrastructures de test à usage général, telles que [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [nunit](https://nunit.org/)et [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="67a72-184">bUnit works with general-purpose testing frameworks, such as [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/), and [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="67a72-185">Ces infrastructures de test font en sorte que les tests bUnit paraissent et ressemblent aux tests unitaires réguliers.</span><span class="sxs-lookup"><span data-stu-id="67a72-185">These testing frameworks make bUnit tests look and feel like regular unit tests.</span></span> <span data-ttu-id="67a72-186">les tests bUnit intégrés à un Framework de test à usage général sont généralement exécutés avec :</span><span class="sxs-lookup"><span data-stu-id="67a72-186">bUnit tests integrated with a general-purpose testing framework are ordinarily executed with:</span></span>

* <span data-ttu-id="67a72-187">[Explorateur de tests de Visual Studio](/visualstudio/test/run-unit-tests-with-test-explorer).</span><span class="sxs-lookup"><span data-stu-id="67a72-187">[Visual Studio's Test Explorer](/visualstudio/test/run-unit-tests-with-test-explorer).</span></span>
* <span data-ttu-id="67a72-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) Commande CLI dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="67a72-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) CLI command in a command shell.</span></span>
* <span data-ttu-id="67a72-189">Un pipeline de test DevOps automatisé.</span><span class="sxs-lookup"><span data-stu-id="67a72-189">An automated DevOps testing pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="67a72-190">Les concepts de test et les implémentations de test dans différents frameworks de test sont similaires, mais pas identiques.</span><span class="sxs-lookup"><span data-stu-id="67a72-190">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span> <span data-ttu-id="67a72-191">Reportez-vous à la documentation de l’infrastructure de test pour obtenir de l’aide.</span><span class="sxs-lookup"><span data-stu-id="67a72-191">Refer to the test framework's documentation for guidance.</span></span>

<span data-ttu-id="67a72-192">L’exemple suivant illustre la structure d’un test bUnit sur le `Counter` composant dans une application basée sur un [ Blazor modèle de projet](xref:blazor/project-structure).</span><span class="sxs-lookup"><span data-stu-id="67a72-192">The following demonstrates the structure of a bUnit test on the `Counter` component in an app based on a [Blazor project template](xref:blazor/project-structure).</span></span> <span data-ttu-id="67a72-193">Le `Counter` composant affiche et incrémente un compteur en fonction de l’utilisateur qui sélectionne un bouton dans la page :</span><span class="sxs-lookup"><span data-stu-id="67a72-193">The `Counter` component displays and increments a counter based on the user selecting a button in the page:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="67a72-194">Le test bUnit suivant vérifie que le compteur de la coupe est correctement incrémenté lorsque le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="67a72-194">The following bUnit test verifies that the CUT's counter is incremented correctly when the button is selected:</span></span>

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var ctx = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

<span data-ttu-id="67a72-195">Les actions suivantes ont lieu à chaque étape du test :</span><span class="sxs-lookup"><span data-stu-id="67a72-195">The following actions take place at each step of the test:</span></span>

* <span data-ttu-id="67a72-196">*Arrange*: le `Counter` composant est rendu à l’aide de bUnit `TestContext` .</span><span class="sxs-lookup"><span data-stu-id="67a72-196">*Arrange*: The `Counter` component is rendered using bUnit's `TestContext`.</span></span> <span data-ttu-id="67a72-197">L’élément de paragraphe de la coupe ( `<p>` ) est trouvé et assigné à `paraElm` .</span><span class="sxs-lookup"><span data-stu-id="67a72-197">The CUT's paragraph element (`<p>`) is found and assigned to `paraElm`.</span></span>

* <span data-ttu-id="67a72-198">*Act*: l’élément du bouton ( `<button>` ) est situé puis sélectionné en appelant `Click` , ce qui doit incrémenter le compteur et mettre à jour le contenu de la balise de paragraphe ( `<p>` ).</span><span class="sxs-lookup"><span data-stu-id="67a72-198">*Act*: The button's element (`<button>`) is located and then selected by calling `Click`, which should increment the counter and update the content of the paragraph tag (`<p>`).</span></span> <span data-ttu-id="67a72-199">Le contenu du texte de l’élément de paragraphe est obtenu en appelant `TextContent` .</span><span class="sxs-lookup"><span data-stu-id="67a72-199">The paragraph element text content is obtained by calling `TextContent`.</span></span>

* <span data-ttu-id="67a72-200">*Assert*: `MarkupMatches` est appelé sur le contenu de texte pour vérifier qu’il correspond à la chaîne attendue, qui est `Current count: 1` .</span><span class="sxs-lookup"><span data-stu-id="67a72-200">*Assert*: `MarkupMatches` is called on the text content to verify that it matches the expected string, which is `Current count: 1`.</span></span>

> [!NOTE]
> <span data-ttu-id="67a72-201">La `MarkupMatches` méthode Assert diffère d’une assertion de comparaison de chaînes standard (par exemple, `Assert.Equal("Current count: 1", paraElmText);` ) `MarkupMatches` effectue une comparaison sémantique de l’entrée et du balisage HTML attendu.</span><span class="sxs-lookup"><span data-stu-id="67a72-201">The `MarkupMatches` assert method differs from a regular string comparison assertion (for example, `Assert.Equal("Current count: 1", paraElmText);`) `MarkupMatches` performs a semantic comparison of the input and expected HTML markup.</span></span> <span data-ttu-id="67a72-202">Une comparaison sémantique est consciente de la sémantique HTML, ce qui signifie que les éléments tels que les espaces blancs non significatifs sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="67a72-202">A semantic comparison is aware of HTML semantics, meaning things like insignificant whitespace is ignored.</span></span> <span data-ttu-id="67a72-203">Cela aboutit à des tests plus stables.</span><span class="sxs-lookup"><span data-stu-id="67a72-203">This results in more stable tests.</span></span> <span data-ttu-id="67a72-204">Pour plus d’informations, consultez [Personnalisation de la comparaison HTML sémantique](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span><span class="sxs-lookup"><span data-stu-id="67a72-204">For more information, see [Customizing the Semantic HTML Comparison](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="67a72-205">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="67a72-205">Additional resources</span></span>

* <span data-ttu-id="67a72-206">[Prise en main avec bUnit : les](https://bunit.egilhansen.com/docs/getting-started/)instructions bUnit incluent des conseils sur la création d’un projet de test, le référencement de packages d’infrastructure de test et la génération et l’exécution de tests.</span><span class="sxs-lookup"><span data-stu-id="67a72-206">[Getting Started with bUnit](https://bunit.egilhansen.com/docs/getting-started/): bUnit instructions include guidance on creating a test project, referencing testing framework packages, and building and running tests.</span></span>

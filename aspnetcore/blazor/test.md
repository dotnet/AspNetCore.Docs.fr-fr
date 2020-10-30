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
ms.openlocfilehash: cd4aee66fd6df6cc0ce520d8ca66e0a2cf130eff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054864"
---
# <a name="test-components-in-aspnet-core-no-locblazor"></a>Tester des composants dans ASP.NET Core Blazor

[Egil Hansen](https://egilhansen.com/)

Le test est un aspect important de la création de logiciels stables et gérables.

Pour tester un Blazor composant, le *composant* testé (couper) est :

* Rendu avec l’entrée pertinente pour le test.
* Selon le type de test effectué, éventuellement soumis à une interaction ou à une modification. Par exemple, les gestionnaires d’événements peuvent être déclenchés, par exemple un `onclick` événement pour un bouton.
* Examen des valeurs attendues.

## <a name="test-approaches"></a>Approches de test

Deux approches courantes pour tester les composants sont les tests de Blazor bout en bout (E2E) et les tests unitaires :

* **Tests unitaires** : les [tests unitaires](/dotnet/core/testing/) sont écrits à l’aide d’une bibliothèque de tests unitaires qui fournit :
  * Rendu des composants.
  * Inspection de la sortie et de l’état des composants.
  * Déclenchement de gestionnaires d’événements et de méthodes de cycle de vie.
  * Assertions que le comportement du composant est correct.

  [bUnit](https://github.com/egil/bUnit) est un exemple de bibliothèque qui active les Razor tests unitaires de composants.

* **Test E2E** : un testeur exécute une Blazor application contenant la coupe et automatise une instance de navigateur. L’outil de test inspecte et interagit avec la coupe dans le navigateur. Le [sélénium](https://github.com/SeleniumHQ/selenium) est un exemple de Framework de test E2E qui peut être utilisé avec des Blazor applications.

Dans les tests unitaires, seul le Blazor composant ( Razor /c #) est impliqué. Les dépendances externes, telles que les services et l’interopérabilité de JS, doivent être simulées. Dans E2E testing, le Blazor composant et toute l’infrastructure auxiliaire font partie du test, y compris CSS, js et les API DOM et de navigateur.

La *portée de test* décrit l’étendue des tests. L’étendue de test a généralement une influence sur la vitesse des tests. Les tests unitaires s’exécutent sur un sous-ensemble des sous-systèmes de l’application et s’exécutent généralement en millisecondes. Les tests E2E, qui testent un large groupe de sous-systèmes de l’application, peuvent prendre plusieurs secondes. 

Les tests unitaires fournissent également l’accès à l’instance de la coupe, ce qui permet l’inspection et la vérification de l’état interne du composant. Cela n’est généralement pas possible dans les tests E2E.

En ce qui concerne l’environnement du composant, les tests E2E doivent s’assurer que l’état environnemental attendu a été atteint avant le début de la vérification. Dans le cas contraire, le résultat est imprévisible. Dans les tests unitaires, le rendu de la coupe et du cycle de vie du test est plus intégré, ce qui améliore la stabilité des tests.

Les tests E2E impliquent le lancement de plusieurs processus, l’e/s réseau et le disque, ainsi que d’autres activités du sous-système qui conduisent souvent à une mauvaise fiabilité des tests. Les tests unitaires sont généralement isolés de ces types de problèmes.

Le tableau suivant résume la différence entre les deux approches de test.

| Fonctionnalité                       | Effectuer des tests unitaires                     | Test E2E                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| Étendue de test                       | Blazor composant ( Razor /c #) uniquement | Blazor composant ( Razor /c #) avec CSS/js |
| Durée d’exécution des tests              | Millisecondes                     | Secondes                                 |
| Accès à l’instance du composant | Oui                              | Non                                      |
| Sensible à l’environnement     | Non                               | Oui                                     |
| Fiabilité                      | Plus fiable                    | Moins fiable                           |

## <a name="choose-the-most-appropriate-test-approach"></a>Choisir l’approche de test la plus appropriée

Examinez le scénario lorsque vous choisissez le type de test à effectuer. Certaines considérations sont décrites dans le tableau suivant.

| Scénario | Approche suggérée | Remarques |
| -------- | ------------------ | ------- |
| Composant sans logique d’interopérabilité JS | Effectuer des tests unitaires | Lorsqu’il n’existe aucune dépendance vis-à-vis de l’interopérabilité de JS dans un Blazor composant, le composant peut être testé sans accès à js ou à l’API DOM. Dans ce scénario, il n’y a aucun inconvénient à choisir des tests unitaires. |
| Composant avec une logique d’interopérabilité JS simple | Effectuer des tests unitaires | Il est courant que les composants interrogent le DOM ou déclenchent des animations par le biais de l’interopérabilité de JS. Les tests unitaires sont généralement préférés dans ce scénario, car il est facile de simuler l’interaction JS par le biais de l' <xref:Microsoft.JSInterop.IJSRuntime> interface. |
| Composant qui dépend d’un code JS complexe | Test unitaire et test JS distinct | Si un composant utilise l’interopérabilité JS pour appeler une bibliothèque JS volumineuse ou complexe, mais que l’interaction entre la Blazor bibliothèque Component et js est simple, la meilleure approche consiste à traiter la bibliothèque Component et JS ou le code comme deux parties distinctes et à tester chacune individuellement. Testez le Blazor composant à l’aide d’une bibliothèque de tests unitaires, puis testez js avec une bibliothèque de tests js. |
| Composant avec une logique qui dépend de la manipulation JS du DOM du navigateur | Test E2E | Lorsque la fonctionnalité d’un composant dépend de JS et de sa manipulation du DOM, vérifiez à la fois le code JS et le Blazor code dans un test E2E. Il s’agit de l’approche Blazor adoptée par les développeurs de l’infrastructure avec la Blazor logique de rendu du navigateur de, qui a un code C# et JS étroitement couplé. Le code C# et JS doit fonctionner ensemble pour restituer correctement les Blazor composants dans un navigateur.
| Composant qui dépend d’une bibliothèque de composants tiers avec des dépendances difficiles à simuler | Test E2E | Lorsque la fonctionnalité d’un composant dépend d’une bibliothèque de composants tiers qui a des dépendances difficile à simuler, telles que l’interopérabilité de JS, le test E2E peut être la seule option pour tester le composant. |

## <a name="test-components-with-bunit"></a>Tester des composants avec bUnit

Il n’existe pas d’infrastructure de test Microsoft officielle pour Blazor , mais le projet [bUnit](https://github.com/egil/bUnit) basé sur la communauté offre un moyen pratique pour les composants de test unitaire Blazor .

> [!NOTE]
> bUnit est une bibliothèque de tests tierces et n’est pas prise en charge ou gérée par Microsoft.

bUnit fonctionne avec des infrastructures de test à usage général, telles que [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [nunit](https://nunit.org/)et [xUnit](https://xunit.github.io/). Ces infrastructures de test font en sorte que les tests bUnit paraissent et ressemblent aux tests unitaires réguliers. les tests bUnit intégrés à un Framework de test à usage général sont généralement exécutés avec :

* [Explorateur de tests de Visual Studio](/visualstudio/test/run-unit-tests-with-test-explorer).
* [`dotnet test`](/dotnet/core/tools/dotnet-test) Commande CLI dans un interpréteur de commandes.
* Un pipeline de test DevOps automatisé.

> [!NOTE]
> Les concepts de test et les implémentations de test dans différents frameworks de test sont similaires, mais pas identiques. Reportez-vous à la documentation de l’infrastructure de test pour obtenir de l’aide.

L’exemple suivant illustre la structure d’un test bUnit sur le `Counter` composant dans une application basée sur un Blazor modèle de projet. Le `Counter` composant affiche et incrémente un compteur en fonction de l’utilisateur qui sélectionne un bouton dans la page :

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

Le test bUnit suivant vérifie que le compteur de la coupe est correctement incrémenté lorsque le bouton est sélectionné :

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

Les actions suivantes ont lieu à chaque étape du test :

* *Arrange* : le `Counter` composant est rendu à l’aide de bUnit `TestContext` . L’élément de paragraphe de la coupe ( `<p>` ) est trouvé et assigné à `paraElm` .

* *Act* : l’élément du bouton ( `<button>` ) est situé puis sélectionné en appelant `Click` , ce qui doit incrémenter le compteur et mettre à jour le contenu de la balise de paragraphe ( `<p>` ). Le contenu du texte de l’élément de paragraphe est obtenu en appelant `TextContent` .

* *Assert* : `MarkupMatches` est appelé sur le contenu de texte pour vérifier qu’il correspond à la chaîne attendue, qui est `Current count: 1` .

> [!NOTE]
> La `MarkupMatches` méthode Assert diffère d’une assertion de comparaison de chaînes standard (par exemple, `Assert.Equal("Current count: 1", paraElmText);` ) `MarkupMatches` effectue une comparaison sémantique de l’entrée et du balisage HTML attendu. Une comparaison sémantique est consciente de la sémantique HTML, ce qui signifie que les éléments tels que les espaces blancs non significatifs sont ignorés. Cela aboutit à des tests plus stables. Pour plus d’informations, consultez [Personnalisation de la comparaison HTML sémantique](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Prise en main avec bUnit : les](https://bunit.egilhansen.com/docs/getting-started/)instructions bUnit incluent des conseils sur la création d’un projet de test, le référencement de packages d’infrastructure de test et la génération et l’exécution de tests.

---
title: Utiliser les analyseurs d’API web
author: pranavkm
description: En savoir plus sur le package d’analyseur d’API Web ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
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
uid: web-api/advanced/analyzers
ms.openlocfilehash: cf0415e7d72e21a48db8bbeb4540f05e0b0a4198
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057919"
---
# <a name="use-web-api-analyzers"></a>Utiliser les analyseurs d’API web

ASP.NET Core 2,2 et versions ultérieures fournissent un package d’analyseurs MVC destiné à être utilisé avec des projets d’API Web. Les analyseurs fonctionnent avec les contrôleurs annotés avec <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> , tout en s’appuyant sur les [conventions d’API Web](xref:web-api/advanced/conventions).

Le package des analyseurs vous avertit des actions du contrôleur qui :

* Retourne un code d’État non déclaré.
* Retourne un résultat de réussite non déclaré.
* Documente un code d’État qui n’est pas retourné.
* Comprend un contrôle de validation de modèle explicite.

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a>Référencer le package de l’analyseur

Dans ASP.NET Core 3,0 ou version ultérieure, les analyseurs sont inclus dans le kit SDK .NET Core. Pour activer l’analyseur dans votre projet, incluez la `IncludeOpenAPIAnalyzers` propriété dans le fichier projet :

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a>Installation de package

Installez le package NuGet [Microsoft. AspNetCore. Mvc. API. Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) avec l’une des approches suivantes :

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

À partir de la fenêtre **Console du Gestionnaire de package** :
  * Accédez à **Affichage** > **Autres fenêtres** > **Console du Gestionnaire de package** .
  * Accédez au répertoire où se trouve le fichier *ApiConventions.csproj* .
  * Exécutez la commande suivante :

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Cliquez avec le bouton droit sur le dossier *packages* dans **panneau solutions** > **Ajouter des packages...** .
* Définissez la liste déroulante **source** de la fenêtre **Ajouter des Packages** sur « NuGet.org ».
* Entrez « Microsoft.AspNetCore.Mvc.Api.Analyzers » dans la zone de recherche.
* Sélectionnez le package « Microsoft.AspNetCore.Mvc.Api.Analyzers » dans le volet de résultats, puis cliquez sur **Ajouter un package** .

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécutez la commande suivante à partir du **Terminal intégré** :

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Exécutez la commande suivante :

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a>Analyseurs pour les conventions d’API Web

Les documents sur les API ouvertes contiennent les codes d’état et les types de réponse qu’une action peut retourner. Dans ASP.NET Core MVC, des attributs comme <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> et <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> sont utilisés pour documenter une action. <xref:tutorials/web-api-help-pages-using-swagger> Pour plus d’informations sur la documentation de votre API Web.

Un des analyseurs du package inspecte les contrôleurs annotés avec <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> et identifie les actions qui ne document pas entièrement leurs réponses. Prenons l’exemple suivant :

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

L’action précédente documente le type de retour avec réussite HTTP 200, mais ne documente pas le code d’état d’échec HTTP 404. L’analyseur signale la documentation manquante pour le code d’état HTTP 404 sous la forme d’un avertissement. Une option pour résoudre le problème est fournie.

![analyseur rapportant un avertissement](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>

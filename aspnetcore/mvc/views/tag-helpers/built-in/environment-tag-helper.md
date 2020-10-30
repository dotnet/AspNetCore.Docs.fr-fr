---
title: Tag Helper Environnement dans ASP.NET Core
author: pkellner
description: Tag Helper Environnement ASP.NET Core défini avec toutes les propriétés
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 4f8330521bb9114f6639c1889cc15ebd18adc0ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061130"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>Tag Helper Environnement dans ASP.NET Core

Par [Peter Kellner](https://peterkellner.net) et [Hisham Bin Ateya](https://twitter.com/hishambinateya)

Le tag Helper d’environnement affiche de manière conditionnelle son contenu délimité en fonction de l' [environnement d’hébergement](xref:fundamentals/environments)actuel. L’attribut unique du Tag Helper Environnement, `names`, est une liste séparée par des virgules de noms d’environnement. Si l’un des noms d’environnement fournis correspond à l’environnement actuel, le contenu joint est affiché.

Pour avoir une vue d’ensemble de Tag Helpers, consultez <xref:mvc/views/tag-helpers/intro>.

## <a name="environment-tag-helper-attributes"></a>Attributs de Tag Helper Environnement

### <a name="names"></a>noms

`names` accepte un seul nom d’environnement d’hébergement ou une liste séparée par des virgules de noms d’environnement d’hébergement qui déclenchent l’affichage du contenu joint.

Les valeurs d’environnement sont comparées à la valeur actuelle retournée par [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*). La comparaison ignore la casse.

L’exemple suivant utilise un Tag Helper Environnement. Le contenu est affiché si l’environnement d’hébergement est un environnement de préproduction (Staging) ou de production :

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>Attributs include et exclude

`include`les & `exclude` attributs contrôlent le rendu du contenu délimité en fonction des noms d’environnement d’hébergement inclus ou exclus.

### <a name="include"></a>include

La propriété `include` présente un comportement similaire à l’attribut `names`. Un environnement listé dans la valeur d’attribut `include` doit correspondre à l’environnement d’hébergement de l’application ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) pour afficher le contenu de la balise `<environment>`.

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>exclure

Contrairement à l’attribut `include`, le contenu de la balise `<environment>` est affiché quand l’environnement d’hébergement ne correspond pas à un environnement listé dans la valeur d’attribut `exclude`.

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/environments>

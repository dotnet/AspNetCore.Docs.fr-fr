---
title: Tag Helper Image dans ASP.NET Core
author: pkellner
description: Montre comment utiliser un Tag Helper Image.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
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
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: edc6971ac42756504b5b371ba509d7a657a0c396
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061117"
---
# <a name="image-tag-helper-in-aspnet-core"></a>Tag Helper Image dans ASP.NET Core

Par [Peter Kellner](https://peterkellner.net)

Le Tag Helper Image améliore la balise `<img>` afin de fournir un comportement de cache busting pour les fichiers image statiques.

Une chaîne de cache busting est une valeur unique représentant le hachage du fichier image statique ajouté à l’URL de la ressource. La chaîne unique invite les clients (et certains proxys) à recharger l’image depuis le serveur web hôte et non depuis le cache du client.

Si la source de l’image (`src`) est un fichier statique sur le serveur web hôte :

* Une chaîne de cache-busting unique est ajoutée en tant que paramètre de requête à la source de l’image.
* Si le fichier sur le serveur web hôte change, une URL de demande unique qui inclut le paramètre de demande mis à jour est générée.

Pour avoir une vue d’ensemble de Tag Helpers, consultez <xref:mvc/views/tag-helpers/intro>.

## <a name="image-tag-helper-attributes"></a>Attributs de Tag Helper Image

### <a name="src"></a>src

Pour activer le Tag Helper Image, l’attribut `src` est obligatoire sur l’élément `<img>`.

La source de l’image (`src`) doit pointer vers un fichier statique physique sur le serveur. Si la source `src` est un URI distant, le paramètre de la chaîne de requête de cache-busting n’est pas généré.

### <a name="asp-append-version"></a>asp-append-version

Quand `asp-append-version` est spécifié avec une valeur `true` et un attribut `src`, le Tag Helper Image est appelé.

L’exemple suivant utilise un Tag Helper Image :

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

Si le fichier statique existe dans le répertoire */wwwroot/images/* , le code HTML généré est semblable au suivant (le hachage sera différent) :

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

La valeur affectée au paramètre `v` est la valeur de hachage du fichier *asplogo.png* sur le disque. Si le serveur web ne peut pas obtenir l’accès en lecture au fichier statique, aucun paramètre `v` n’est ajouté à l’attribut `src` dans le balisage affiché.

## <a name="hash-caching-behavior"></a>Comportement de mise en cache du hachage

Le Tag Helper Image utilise le fournisseur de cache sur le serveur web local pour stocker le hachage `Sha512` calculé d’un fichier donné. Si le fichier est demandé plusieurs fois, le hachage n’est pas recalculé. Le cache est invalidé par un observateur de fichier qui est associé au fichier quand le hachage `Sha512` du fichier est calculé. Quand le fichier change sur le disque, un nouveau hachage est calculé et mis en cache.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:performance/caching/memory>

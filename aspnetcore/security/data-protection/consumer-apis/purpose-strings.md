---
title: Chaînes d’objectif dans ASP.NET Core
author: rick-anderson
description: Découvrez comment les chaînes sont utilisées dans les API de protection des données ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 9a55131714b23909da5d00b73607078b295a1c4d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060805"
---
# <a name="purpose-strings-in-aspnet-core"></a>Chaînes d’objectif dans ASP.NET Core

<a name="data-protection-consumer-apis-purposes"></a>

Les composants qui utilisent `IDataProtectionProvider` doivent passer un paramètre d' *usage* unique à la `CreateProtector` méthode. Le *paramètre* usage est inhérent à la sécurité du système de protection des données, car il permet l’isolation entre les consommateurs de chiffrement, même si les clés de chiffrement racine sont identiques.

Lorsqu’un consommateur spécifie un objet, la chaîne d’objectif est utilisée avec les clés de chiffrement racines pour dériver les sous-clés de chiffrement uniques à ce consommateur. Cela isole le consommateur de tous les autres consommateurs de chiffrement dans l’application : aucun autre composant ne peut lire ses charges utiles, et il ne peut pas lire les charges utiles d’un autre composant. Cette isolation rend également possibles des catégories entières d’attaques contre le composant.

![Exemple de diagramme d’objet](purpose-strings/_static/purposes.png)

Dans le diagramme ci-dessus, les `IDataProtector` instances A et B **ne peuvent pas** lire les charges utiles les unes des autres, mais uniquement les siennes.

La chaîne d’objectif n’a pas besoin d’être secrète. Il doit tout simplement être unique dans le sens où aucun autre composant correct ne fournira jamais la même chaîne d’objectif.

>[!TIP]
> L’utilisation de l’espace de noms et du nom de type du composant consommant les API de protection des données est une bonne règle empirique, comme dans la pratique, ces informations ne seront jamais en conflit.
>
>Un composant créé par Contoso qui est responsable de la frappe des jetons du porteur peut utiliser contoso. Security. BearerToken comme chaîne d’objet. Ou bien mieux, il peut utiliser contoso. Security. BearerToken. v1 comme chaîne d’objet. Le fait d’ajouter le numéro de version permet à une version ultérieure d’utiliser contoso. Security. BearerToken. v2 comme objectif, et les différentes versions sont complètement isolées les unes des autres en ce qui concerne les charges utiles.

Étant donné que le paramètre de l’objectif `CreateProtector` est un tableau de chaînes, la valeur ci-dessus aurait été spécifiée à la place comme `[ "Contoso.Security.BearerToken", "v1" ]` . Cela permet d’établir une hiérarchie d’objectifs et de faire en sorte que les scénarios d’architecture mutualisée soient possibles avec le système de protection des données.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> Les composants ne doivent pas autoriser une entrée utilisateur non fiable comme seule source d’entrée pour la chaîne de rôles.
>
>Par exemple, considérez un composant contoso. Messaging. SecureMessage qui est responsable du stockage des messages sécurisés. Si le composant de messagerie sécurisée devait appeler `CreateProtector([ username ])` , un utilisateur malveillant pourrait créer un compte avec le nom d’utilisateur « contoso. Security. BearerToken » pour tenter d’obtenir le composant à appeler `CreateProtector([ "Contoso.Security.BearerToken" ])` , provoquant par inadvertance le système de messagerie sécurisée pour les charges utiles susceptibles d’être perçues comme des jetons d’authentification.
>
>Une chaîne plus efficace pour le composant de messagerie serait `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` , ce qui fournit une isolation appropriée.

L’isolation fournie par et les comportements des `IDataProtectionProvider` `IDataProtector` objectifs, et sont les suivantes :

* Pour un `IDataProtectionProvider` objet donné, la `CreateProtector` méthode crée un `IDataProtector` objet qui est lié de manière unique à l' `IDataProtectionProvider` objet qui l’a créé et au paramètre usage qui a été passé dans la méthode.

* Le paramètre Purpose ne doit pas avoir la valeur null. (Si l’objectif est spécifié sous la forme d’un tableau, cela signifie que le tableau ne doit pas avoir une longueur de zéro et que tous les éléments du tableau doivent être non null.) Une fonction de chaîne vide est techniquement autorisée, mais elle est déconseillée.

* Les arguments à deux objectifs sont équivalents si et seulement s’ils contiennent les mêmes chaînes (à l’aide d’un comparateur ordinal) dans le même ordre. Un argument à rôle unique est équivalent au tableau d’objectifs à un seul élément correspondant.

* Deux `IDataProtector` objets sont équivalents si et seulement s’ils sont créés à partir d’objets équivalents `IDataProtectionProvider` à des paramètres équivalents.

* Pour un `IDataProtector` objet donné, un appel à `Unprotect(protectedData)` retourne la valeur d’origine `unprotectedData` si et seulement si `protectedData := Protect(unprotectedData)` un objet équivalent est présent `IDataProtector` .

> [!NOTE]
> Nous n’envisageons pas le cas où un composant choisit intentionnellement une chaîne d’objectif connue pour être en conflit avec un autre composant. Ce type de composant serait essentiellement considéré comme malveillant et ce système n’est pas destiné à fournir des garanties de sécurité dans le cas où du code malveillant est déjà en cours d’exécution au sein du processus de travail.

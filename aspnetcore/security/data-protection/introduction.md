---
title: Protection des données ASP.NET Core
author: rick-anderson
description: En savoir plus sur le concept de protection des données et les principes de conception des API de protection des données ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/data-protection/introduction
ms.openlocfilehash: 5fd5676b286e758f0648d78bf8cb4171e7a98f60
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051692"
---
# <a name="aspnet-core-data-protection"></a>Protection des données ASP.NET Core

Les applications Web doivent souvent stocker des données sensibles à la sécurité. Windows fournit DPAPI pour les applications de bureau, mais cela ne convient pas aux applications Web. La pile de protection des données ASP.NET Core fournit une API de chiffrement simple et facile à utiliser qu’un développeur peut utiliser pour protéger des données, notamment la gestion et la rotation des clés.

La pile de protection des données ASP.NET Core est conçue pour servir de remplacement à long terme de &lt; l' &gt; élément machineKey dans ASP.net 1. x-4. x. Il a été conçu pour résoudre bon nombre des lacunes de l’ancienne pile de chiffrement, tout en fournissant une solution prête à l’emploi pour la majorité des cas d’utilisation que les applications modernes sont susceptibles de rencontrer.

## <a name="problem-statement"></a>Définition du problème

La déclaration globale du problème peut être exposée succinctement en une seule phrase : je dois conserver des informations approuvées pour une récupération ultérieure, mais je n’approuve pas le mécanisme de persistance. En termes Web, cela peut être écrit comme « j’ai besoin d’aller-retour dans un État approuvé via un client non approuvé ».

L’exemple canonique est un jeton d’authentification cookie ou de porteur. Le serveur génère un jeton « je suis Groot et dispose des autorisations XYZ » et le transmet au client. À une date ultérieure, le client présente ce jeton au serveur, mais le serveur a besoin d’un certain type d’assurance que le client n’a pas falsifié le jeton. Par conséquent, la première exigence : authenticité (également appelée intégrité, vérification de la falsification).

Étant donné que l’état persistant est approuvé par le serveur, nous pensons que cet État peut contenir des informations spécifiques à l’environnement d’exploitation. Cela peut se présenter sous la forme d’un chemin d’accès de fichier, d’une autorisation, d’un handle ou d’une autre référence indirecte, ou d’un autre élément de données spécifiques au serveur. Ces informations ne doivent généralement pas être divulguées à un client non approuvé. Par conséquent, la deuxième exigence : la confidentialité.

Enfin, étant donné que les applications modernes sont composant, ce que nous avons vu est que les composants individuels souhaitent tirer parti de ce système sans tenir compte des autres composants du système. Par exemple, si un composant de jeton du porteur utilise cette pile, il doit fonctionner sans interférence d’un mécanisme anti-CSRF qui peut également utiliser la même pile. Par conséquent, l’exigence finale : isolation.

Nous pouvons fournir des contraintes supplémentaires afin de limiter l’étendue de nos exigences. Nous partons du principe que tous les services opérant dans le chiffrement sont de confiance égale et que les données ne doivent pas être générées ou consommées en dehors des services sous notre contrôle direct. En outre, nous avons besoin que les opérations soient aussi rapides que possible, car chaque requête adressée au service Web peut traverser le chiffrement une ou plusieurs fois. Cela rend le chiffrement symétrique idéal pour notre scénario et nous pouvons escompter le chiffrement asymétrique jusqu’à ce qu’il soit nécessaire.

## <a name="design-philosophy"></a>Philosophie de conception

Nous avons commencé par identifier les problèmes avec la pile existante. Après cela, nous avons interrogé le paysage des solutions existantes et conclu qu’aucune solution existante n’avait les fonctionnalités que nous avons recherchées. Nous avons ensuite conçu une solution basée sur plusieurs principes directeurs.

* Le système doit offrir une simplicité de configuration. Dans l’idéal, le système serait sans configuration et les développeurs pouvaient s’appuyer sur le sol. Dans les situations où les développeurs doivent configurer un aspect spécifique (par exemple, le référentiel de clé), il est important de tenir compte de la simplicité de ces configurations spécifiques.

* Offre une API simple destinée aux consommateurs. Les API doivent être faciles à utiliser correctement et difficiles à utiliser de manière incorrecte.

* Les développeurs ne doivent pas apprendre les principes de gestion clés. Le système doit gérer la sélection de l’algorithme et la durée de vie de la clé au nom du développeur. Dans l’idéal, le développeur ne doit jamais avoir accès au matériel de clé brut.

* Lorsque cela est possible, les clés doivent être protégées au repos. Le système doit déterminer un mécanisme de protection par défaut approprié et l’appliquer automatiquement.

Avec ces principes à l’esprit, nous avons développé une pile de protection des données simple et [facile à utiliser](xref:security/data-protection/using-data-protection) .

Les API de protection des données ASP.NET Core ne sont pas principalement destinées à la persistance illimitée des charges utiles confidentielles. D’autres technologies telles que [DPAPI Windows CNG](/windows/win32/seccng/cng-dpapi) et [Azure Rights Management](/rights-management/) sont plus adaptées au scénario de stockage indéfini, et elles ont des fonctionnalités de gestion de clés fortes. Cela dit, rien n’empêche un développeur d’utiliser les API de protection des données ASP.NET Core pour la protection à long terme des données confidentielles.

## <a name="audience"></a>Public visé

Le système de protection des données est divisé en cinq packages principaux. Divers aspects de ces API ciblent trois audiences principales.

1. La [vue d’ensemble des API de consommateur](xref:security/data-protection/consumer-apis/overview) cible les développeurs d’applications et d’infrastructures.

   «Je ne veux pas en savoir plus sur le fonctionnement de la pile ou sur la façon dont elle est configurée. Je souhaite simplement effectuer une opération aussi simple que possible, avec une probabilité élevée d’utilisation des API.»

2. Les [API de configuration](xref:security/data-protection/configuration/overview) ciblent les développeurs d’applications et les administrateurs système.

   « Je dois dire au système de protection des données que mon environnement nécessite des chemins ou des paramètres autres que ceux par défaut. »

3. Les API d’extensibilité ciblent les développeurs chargés de l’implémentation d’une stratégie personnalisée. L’utilisation de ces API est limitée aux rares situations et aux développeurs expérimentés en matière de sécurité.

   «J’ai besoin de remplacer un composant entier dans le système, car j’ai véritablement des exigences comportementales uniques. Je souhaite apprendre les parties utilisées de manière inhabituelle de la surface de l’API afin de créer un plug-in qui répond à mes exigences.»

## <a name="package-layout"></a>Disposition de package

La pile de protection des données est constituée de cinq packages.

* [Microsoft. AspNetCore. dataprotection. Abstracts](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) contient les <xref:Microsoft.AspNetCore.DataProtection.IDataProtectionProvider> <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> interfaces et pour créer des services de protection des données. Il contient également des méthodes d’extension utiles pour utiliser ces types (par exemple, [IDataProtector. Protect](xref:Microsoft.AspNetCore.DataProtection.DataProtectionCommonExtensions.Protect*)). Si le système de protection des données est instancié ailleurs et que vous consommez l’API, référencez `Microsoft.AspNetCore.DataProtection.Abstractions` .

* [Microsoft. AspNetCore. dataprotection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) contient l’implémentation de base du système de protection des données, notamment les opérations de chiffrement de base, la gestion des clés, la configuration et l’extensibilité. Pour instancier le système de protection des données (par exemple, en l’ajoutant à un <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> ) ou en modifiant ou en étendant son comportement, référence `Microsoft.AspNetCore.DataProtection` .

* [Microsoft. AspNetCore. dataprotection. extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contient des API supplémentaires que les développeurs peuvent trouver utiles, mais qui n’appartiennent pas au package de base. Par exemple, ce package contient des méthodes de fabrique pour instancier le système de protection des données afin de stocker les clés à un emplacement sur le système de fichiers sans injection de dépendances (consultez <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> ). Il contient également des méthodes d’extension pour limiter la durée de vie des charges utiles protégées (consultez <xref:Microsoft.AspNetCore.DataProtection.ITimeLimitedDataProtector> ).

* [Microsoft.AspNetCore.DataProtection.SystemWeb](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.SystemWeb/) peut être installé dans une application ASP.net 4. x existante pour rediriger ses `<machineKey>` opérations afin d’utiliser la nouvelle pile de protection des données ASP.net core. Pour plus d'informations, consultez <xref:security/data-protection/compatibility/replacing-machinekey>.

* [Microsoft. AspNetCore. Cryptography. Keydérivation](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/) fournit une implémentation de la routine de hachage de mot de passe PBKDF2 et peut être utilisé par les systèmes qui doivent gérer les mots de passe utilisateur en toute sécurité. Pour plus d'informations, consultez <xref:security/data-protection/consumer-apis/password-hashing>.

## <a name="additional-resources"></a>Ressources supplémentaires

<xref:host-and-deploy/web-farm>

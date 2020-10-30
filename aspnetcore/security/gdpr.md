---
title: Prise en charge des Règlement général sur la protection des données (RGPD) dans ASP.NET Core
author: rick-anderson
description: Découvrez comment accéder aux points d’extension RGPD dans une application Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
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
uid: security/gdpr
ms.openlocfilehash: ec65a2c8362c15716bebd6b22f5639785ba74c98
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051003"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>Prise en charge de l’UE Règlement général sur la protection des données (RGPD) dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core fournit des API et des modèles pour vous aider à répondre à certaines exigences de l' [ue Règlement général sur la protection des données (RGPD)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) :

::: moniker range=">= aspnetcore-3.0"

* Les modèles de projet incluent des points d’extension et le balisage stub que vous pouvez remplacer par votre stratégie de confidentialité et d' cookie utilisation.
* La page *pages/privacy. cshtml* ou *vues/page d’affichage/privé/confidentialité. cshtml* fournit une page pour détailler la politique de confidentialité de votre site.

Pour activer la fonctionnalité de consentement par défaut cookie qui se trouve dans les modèles ASP.NET Core 2,2 dans une application générée par le modèle ASP.NET Core 3,0, procédez comme suit :

* Ajoutez `using Microsoft.AspNetCore.Http` à la liste des directives using.
* Ajoutez [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) à `Startup.ConfigureServices` et [Utilisez la Cookie stratégie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) pour `Startup.Configure` :

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* Ajoutez le cookie consentement partiel au fichier *_Layout. cshtml* :

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* Ajoutez le fichier *\_ Cookie ConsentPartial. cshtml* au projet :

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Sélectionnez la version ASP.NET Core 2,2 de cet article pour en savoir plus sur la cookie fonctionnalité de consentement.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* Les modèles de projet incluent des points d’extension et le balisage stub que vous pouvez remplacer par votre stratégie de confidentialité et d' cookie utilisation.
* Une cookie fonctionnalité de consentement vous permet de demander (et de suivre) le consentement de vos utilisateurs pour le stockage des informations personnelles. Si un utilisateur n’a pas consenti à la collecte de données et que l’application a [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) défini sur `true` , cookie les s non essentiels ne sont pas envoyées au navigateur.
* Cookieles s peuvent être marquées comme essentielles. Les éléments essentiels cookie sont envoyés au navigateur même lorsque l’utilisateur n’a pas donné son consentement et que le suivi est désactivé.
* [TempData et la session cookie s](#tempdata) ne sont pas fonctionnels lorsque le suivi est désactivé.
* La page [ Identity gérer](#pd) fournit un lien permettant de télécharger et de supprimer des données utilisateur.

L' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) vous permet de tester la plupart des points d’extension RGPD et des API ajoutés aux modèles ASP.net Core 2,1. Consultez le fichier [Lisez-moi](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) pour obtenir des instructions de test.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>ASP.NET Core la prise en charge RGPD dans le code généré par modèle

Razor Les pages et les projets MVC créés avec les modèles de projet incluent la prise en charge RGPD suivante :

* [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) et [la Cookie stratégie d’utilisation](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) sont définis dans la `Startup` classe.
* [Vue partielle](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) *\_ Cookie ConsentPartial. cshtml* . Un bouton **accepter** est inclus dans ce fichier. Quand l’utilisateur clique sur le bouton **accepter** , le consentement pour les magasins cookie est fourni.
* La page *pages/privacy. cshtml* ou *vues/page d’affichage/privé/confidentialité. cshtml* fournit une page pour détailler la politique de confidentialité de votre site. Le fichier *\_ Cookie ConsentPartial. cshtml* génère un lien vers la page de confidentialité.
* Pour les applications créées avec des comptes d’utilisateur individuels, la page gérer fournit des liens permettant de télécharger et de supprimer des [données utilisateur personnelles](#pd).

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a>CookiePolicyOptions et utiliser la Cookie stratégie

Les [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) sont initialisés dans `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[Utilisez Cookie La stratégie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) est appelée dans `Startup.Configure` :

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a>\_CookieVue partielle de ConsentPartial. cshtml

Vue partielle *\_ Cookie ConsentPartial. cshtml* :

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

Voici ce qui est partiel :

* Obtient l’état de suivi de l’utilisateur. Si l’application est configurée pour exiger un consentement, l’utilisateur doit donner son consentement avant que les cookie s puissent être suivies. Si le consentement est requis, le cookie volet de consentement est corrigé en haut de la barre de navigation créée par le fichier *\_ Layout. cshtml* .
* Fournit un `<p>` élément HTML pour résumer votre stratégie de confidentialité et d' cookie utilisation.
* Fournit un lien vers la page de confidentialité ou une vue qui vous permet de détailler la politique de confidentialité de votre site.

## <a name="essential-no-loccookies"></a>cookieS essentiels

Si le consentement au stockage cookie n’a pas été fourni, seuls cookie les marqués comme essentiels sont envoyés au navigateur. Le code suivant rend un cookie élément essentiel :

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a>Le fournisseur TempData et l’état de session cookie ne sont pas essentiels

Le [fournisseur TempData](xref:fundamentals/app-state#tempdata) cookie n’est pas essentiel. Si le suivi est désactivé, le fournisseur TempData n’est pas fonctionnel. Pour activer le fournisseur TempData lorsque le suivi est désactivé, marquez le TempData cookie comme essentiel dans `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[État](xref:fundamentals/app-state) cookie de session les s ne sont pas essentiels. L’état de session n’est pas fonctionnel lorsque le suivi est désactivé. Le code suivant rend la session cookie s essentielle :

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>Données à caractère personnel

ASP.NET Core applications créées avec des comptes d’utilisateur individuels incluent du code pour télécharger et supprimer des données personnelles.

Sélectionnez le nom d’utilisateur, puis sélectionnez **données personnelles** :

![Page gérer les données personnelles](gdpr/_static/pd.png)

Remarques :

* Pour générer le `Account/Manage` code, consultez [structure Identity ](xref:security/authentication/scaffold-identity).
* Les liens **Delete** et **Download** agissent uniquement sur les données d’identité par défaut. Les applications qui créent des données utilisateur personnalisées doivent être étendues pour supprimer/télécharger les données utilisateur personnalisées. Pour plus d’informations, consultez [Ajouter, télécharger et supprimer des données utilisateur personnalisées dans Identity ](xref:security/authentication/add-user-data).
* Les jetons enregistrés pour l’utilisateur qui sont stockés dans la Identity table de base de données `AspNetUserTokens` sont supprimés lorsque l’utilisateur est supprimé par le biais du comportement de suppression en cascade en raison de la [clé étrangère](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).
* L' [authentification du fournisseur externe](xref:security/authentication/social/index), telle que Facebook et Google, n’est pas disponible avant que la cookie stratégie soit acceptée.

::: moniker-end

## <a name="encryption-at-rest"></a>Chiffrement au repos

Certaines bases de données et mécanismes de stockage permettent le chiffrement au repos. Chiffrement au repos :

* Chiffre automatiquement les données stockées.
* Chiffre sans configuration, programmation ou autre travail pour le logiciel qui accède aux données.
* Est l’option la plus simple et la plus sûre.
* Permet à la base de données de gérer les clés et le chiffrement.

Exemple :

* Microsoft SQL et Azure SQL fournissent des [transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).
* [SQL Azure chiffre par défaut la base de données](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [Les objets BLOB, les fichiers, les tables et le stockage de files d’attente Azure sont chiffrés par défaut](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).

Pour les bases de données qui ne fournissent pas de chiffrement intégré au repos, vous pouvez utiliser le chiffrement de disque pour offrir la même protection. Exemple :

* [BitLocker pour Windows Server](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux :
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [Encfs](https://github.com/vgough/encfs).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [RGPD-ajout d’un bouton de consentement REVOKE dans ASP.NET Core](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)

---
title: Nouveautés d’ASP.NET Core 2.0
author: rick-anderson
description: Découvrez les nouvelles fonctionnalités d’ASP.NET Core 2.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-2.0
ms.openlocfilehash: b7515bcd8b15199770a4245469d00d10da5566f8
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605684"
---
# <a name="whats-new-in-aspnet-core-20"></a>Nouveautés d’ASP.NET Core 2.0

Cet article met en évidence les modifications les plus importantes dans ASP.NET 2.0 Core et fournit des liens vers la documentation appropriée.

## <a name="razor-pages"></a>Razor Pages

Razor Pages est une nouvelle fonctionnalité de ASP.NET Core MVC qui rend le codage des scénarios orientés page plus facile et plus productif.

Pour plus d’informations, consultez l’introduction et le didacticiel :

* [Présentation des Razor pages](xref:razor-pages/index)
* [Prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a>Métapackage ASP.NET Core

Un nouveau métapackage ASP.NET Core comprend tous les packages créés et pris en charge par les équipes ASP.NET Core et Entity Framework Core, ainsi que leurs dépendances internes et tierces. Vous n’avez plus besoin de choisir des fonctionnalités ASP.NET Core individuelles par package. Toutes les fonctionnalités sont incluses dans le package [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All). Les modèles par défaut utilisent ce package.

Pour plus d’informations, consultez [Métapackage Microsoft.AspNetCore.All pour ASP.NET Core 2.0](xref:fundamentals/metapackage).

## <a name="runtime-store"></a>Magasin Runtime

Les applications qui utilisent le métapackage `Microsoft.AspNetCore.All` tirent automatiquement parti du nouveau magasin Runtime de .NET Core. Ce magasin contient toutes les ressources Runtime nécessaires à l’exécution des applications ASP.NET Core 2.0. Quand vous utilisez le métapackage `Microsoft.AspNetCore.All`, aucune des ressources des packages NuGet ASP.NET Core référencés ne sont déployées avec l’application, car elles se trouvent déjà sur le système cible. Les ressources dans le magasin Runtime sont également précompilées afin d’améliorer la vitesse de démarrage des applications.

Pour plus d’informations, consultez [Magasin de packages Runtime](/dotnet/core/deploying/runtime-store)

## <a name="net-standard-20"></a>.NET Standard 2.0

Les packages ASP.NET Core 2.0 ciblent .NET Standard 2.0. Les packages peuvent être référencés par d’autres bibliothèques .NET Standard 2.0, et ils peuvent s’exécuter sur des implémentations de .NET conformes à .NET Standard 2.0, notamment .NET Core 2.0 et le .NET Framework 4.6.1. 

Le métapackage `Microsoft.AspNetCore.All` cible .NET Core 2.0 uniquement, car il est destiné à être utilisé avec .NET Core 2.0 Runtime Store.

## <a name="configuration-update"></a>Mise à jour de la configuration

Une instance de `IConfiguration` est ajoutée au conteneur de services par défaut dans ASP.NET Core 2.0. Quand `IConfiguration` est dans le conteneur de services, il est plus facile pour les applications de récupérer des valeurs de configuration à partir du conteneur.

Pour plus d’informations sur l’état de la documentation planifiée, consultez le [problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3387).

## <a name="logging-update"></a>Mise à jour de la journalisation

Dans ASP.NET Core 2.0, la journalisation est incorporée dans le système d’injection de dépendance par défaut. Vous pouvez ajouter des fournisseurs et configurer le filtrage dans le fichier *Program.cs* plutôt que dans le fichier *Startup.cs*. De plus, le `ILoggerFactory` par défaut prend en charge le filtrage d’une manière qui vous permet d’adopter une approche flexible pour le filtrage entre fournisseurs et le filtrage propre au fournisseur.

Pour plus d’informations, consultez [Introduction à la journalisation](xref:fundamentals/logging/index).

## <a name="authentication-update"></a>Mise à jour de l’authentification

Un nouveau modèle d’authentification simplifie la configuration de l’authentification pour une application à l’aide de l’injection de dépendance.

De nouveaux modèles sont disponibles pour configurer l’authentification pour les applications web et les API web à l’aide de [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).

Pour plus d’informations sur l’état de la documentation planifiée, consultez le [problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3054).

## <a name="identity-update"></a>Identity update

Nous avons simplifié la création d’API Web sécurisées à l’aide Identity de dans ASP.NET Core 2,0. Vous pouvez acquérir des jetons d’accès pour accéder à vos API web à l’aide de la [bibliothèque d’authentification Microsoft (MSAL, Microsoft Authentication Library)](https://www.nuget.org/packages/Microsoft.Identity.Client).

Pour plus d’informations sur les modifications apportées à l’authentification dans la version 2.0, consultez les ressources suivantes :

* [Account confirmation and password recovery in ASP.NET Core (Confirmation de compte et récupération de mot de passe dans ASP.NET Core)](xref:security/authentication/accconfirm)
* [Activer la génération de code QR pour les applications d’authentification dans ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)
* [Migrer l’authentification et Identity vers ASP.NET Core 2,0](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a>Modèles SPA

Des modèles de projet SPA (Single Page Application) pour Angular, Aurelia, Knockout.js, React.js et React.js avec Redux sont disponibles. Le modèle Angular a été mis à jour vers Angular 4. Les modèles Angular et React sont disponibles par défaut. Pour plus d’informations sur l’obtention des autres modèles, consultez [Créer un projet SPA](xref:client-side/spa-services#create-a-new-project). Pour plus d’informations sur la création d’une application monopage dans ASP.NET Core, consultez <xref:client-side/spa-services>.

## <a name="kestrel-improvements"></a>Améliorations apportées à Kestrel

Le serveur web Kestrel offre de nouvelles fonctionnalités qui le rendent plus adapté en tant que serveur connecté à Internet. Plusieurs options de configuration de contrainte de serveur ont été ajoutées à la nouvelle propriété `Limits` de la classe `KestrelServerOptions`. Ajoutez des limites pour les éléments suivants :

* Nombre maximale de connexions client
* Taille maximale du corps de la requête
* Débit données minimal du corps de la requête

Pour plus d'informations, consultez [Implémentation du serveur web Kestrel dans ASP.NET Core](xref:fundamentals/servers/kestrel).

## <a name="weblistener-renamed-to-httpsys"></a>WebListener a été renommé HTTP.sys

Les packages `Microsoft.AspNetCore.Server.WebListener` et `Microsoft.Net.Http.Server` ont été fusionnés dans un nouveau package `Microsoft.AspNetCore.Server.HttpSys`. Les espaces de noms ont été mis à jour en conséquence.

Pour plus d’informations, consultez [Implémentation du serveur web HTTP.sys dans ASP.NET Core](xref:fundamentals/servers/httpsys).

## <a name="enhanced-http-header-support"></a>Prise en charge améliorée de l’en-tête HTTP

Lors de l’utilisation de MVC pour transmettre un `FileStreamResult` ou un `FileContentResult`, vous pouvez maintenant définir un `ETag` ou une date `LastModified` sur le contenu que vous transmettez. Vous pouvez définir ces valeurs sur le contenu retourné avec du code semblable au suivant :

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

Le fichier retourné à vos visiteurs possède les en-têtes HTTP appropriés pour `ETag` les `LastModified` valeurs et.

Si un visiteur de l’application demande du contenu avec un en-tête de requête de plage, ASP.NET Core reconnaît la requête et gère l’en-tête. Si le contenu demandé peut être remis partiellement, ASP.NET Core retourne uniquement le jeu d’octets demandé. Vous n’avez pas besoin d’écrire des gestionnaires spéciaux dans vos méthodes pour adapter ou gérer cette fonctionnalité. Elle est gérée automatiquement pour vous.

## <a name="hosting-startup-and-application-insights"></a>Hébergement du démarrage et Application Insights

Les environnements d’hébergement peuvent désormais injecter des dépendances de packages supplémentaires et exécuter du code pendant le démarrage de l’application, sans que celle-ci doive explicitement prendre une dépendance ou appeler des méthodes. Vous pouvez utiliser cette fonctionnalité pour permettre à certains environnements de « déclencher » des fonctionnalités qui leur sont uniques, sans que l’application ait besoin de savoir à l’avance. 

Dans ASP.NET Core 2.0, cette fonctionnalité est utilisée pour activer automatiquement les diagnostics Application Insights lors du débogage dans Visual Studio et (après vous être inscrit) lors de l’exécution dans Azure App Services. Par conséquent, les modèles de projet n’ajoutent plus de code et de packages Application Insights par défaut.

Pour plus d’informations sur l’état de la documentation planifiée, consultez le [problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3389).

## <a name="automatic-use-of-anti-forgery-tokens"></a>Utilisation automatique des jetons anti-contrefaçon

ASP.NET Core a toujours facilité l’encodage HTML du contenu par défaut, mais la nouvelle version aide encore davantage à prévenir les attaques par falsification de requête intersites (XSRF). ASP.NET Core émet désormais des jetons anti-contrefaçon par défaut, et les valide sur les pages et les actions POST de formulaire sans configuration supplémentaire.

Pour plus d’informations, consultez <xref:security/anti-request-forgery>.

## <a name="automatic-precompilation"></a>Précompilation automatique

Razor la précompilation de la vue est activée lors de la publication par défaut, ce qui réduit la taille de sortie de publication et le temps de démarrage de l’application.

Pour plus d’informations, consultez [ Razor afficher la compilation et la précompilation dans ASP.net Core](xref:mvc/views/view-compilation).

## <a name="razor-support-for-c-71"></a>Razor prise en charge de C# 7,1

Le Razor moteur d’affichage a été mis à jour pour fonctionner avec le nouveau compilateur Roslyn. Cela comprend la prise en charge des fonctionnalités de C# 7.1 telles que les expressions par défaut, la déduction des noms de tuples et les critères spéciaux avec les génériques. Pour utiliser C# 7.1 dans votre projet, ajoutez la propriété suivante dans votre fichier projet, puis rechargez la solution :

```xml
<LangVersion>latest</LangVersion>
```

Pour plus d’informations sur l’état des fonctionnalités de C# 7.1, consultez le [dépôt GitHub de Roslyn](https://github.com/dotnet/roslyn/blob/main/docs/Language%20Feature%20Status.md).

## <a name="other-documentation-updates-for-20"></a>Autres mises à jour de la documentation pour la version 2.0

* [Profils de publication Visual Studio pour le déploiement d’applications ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles)
* [Gestion de clés](xref:security/data-protection/implementation/key-management)
* [Configurer l’authentification Facebook](xref:security/authentication/facebook-logins)
* [Configurer l’authentification Twitter](xref:security/authentication/twitter-logins)
* [Configurer l’authentification Google](xref:security/authentication/google-logins)
* [Configurer l’authentification de compte Microsoft](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a>Recommandations en matière de migration

Pour obtenir des conseils sur la migration d’applications ASP.NET Core 1.x vers ASP.NET Core 2.0, consultez les ressources suivantes :

* [Migrer d’ASP.NET Core 1.x vers ASP.NET Core 2.0](xref:migration/1x-to-2x/index)
* [Migrer l’authentification et Identity vers ASP.NET Core 2,0](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a>Informations supplémentaires

Pour obtenir la liste complète des modifications, consultez les [Notes de publication d’ASP.NET Core 2.0 ](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).

Pour être tenu au courant de la progression et des plans de l’équipe de développement ASP.NET Core, participez à la [réunion de la communauté ASP.NET](https://live.asp.net/).

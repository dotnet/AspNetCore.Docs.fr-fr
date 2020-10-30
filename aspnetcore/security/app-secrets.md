---
title: Stockage sécurisé des secrets d’application en développement dans ASP.NET Core
author: rick-anderson
description: Découvrez comment stocker et récupérer des informations sensibles en tant que secrets d’application lors du développement d’une application ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
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
uid: security/app-secrets
ms.openlocfilehash: 174f831583c2ef6cb7f122a22fe855acc8fe3047
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056866"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Stockage sécurisé des secrets d’application en développement dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)et [Scott Addie](https://github.com/scottaddie)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

Ce document décrit les techniques de stockage et de récupération des données sensibles lors du développement d’une application ASP.NET Core sur un ordinateur de développement. Ne stockez jamais de mots de passe ou d’autres données sensibles dans le code source. Les secrets de production ne doivent pas être utilisés à des fins de développement ou de test. Les secrets ne doivent pas être déployés avec l’application. Au lieu de cela, les secrets doivent être mis à disposition dans l’environnement de production par un moyen contrôlé, comme les variables d’environnement, les Azure Key Vault, etc. Vous pouvez stocker et protéger les secrets de test et de production Azure à l’aide du [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Variables d'environnement

Les variables d’environnement sont utilisées pour éviter le stockage des secrets d’application dans le code ou dans des fichiers de configuration locaux. Les variables d’environnement remplacent les valeurs de configuration pour toutes les sources de configuration spécifiées précédemment.

Prenons l’exemple d’une application Web ASP.NET Core dans laquelle la sécurité **des comptes d’utilisateur individuels** est activée. Une chaîne de connexion de base de données par défaut est incluse dans le fichier du projet *appsettings.json* avec la clé `DefaultConnection` . La chaîne de connexion par défaut est pour la base de données locale, qui s’exécute en mode utilisateur et ne nécessite pas de mot de passe. Pendant le déploiement de l’application, la `DefaultConnection` valeur de clé peut être remplacée par la valeur d’une variable d’environnement. La variable d’environnement peut stocker la chaîne de connexion complète avec des informations d’identification sensibles.

> [!WARNING]
> Les variables d’environnement sont généralement stockées en texte brut et non chiffré. Si l’ordinateur ou le processus est compromis, les variables d’environnement sont accessibles aux parties non fiables. Des mesures supplémentaires pour empêcher la divulgation de secrets d’utilisateur peuvent être nécessaires.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gestionnaire de secret

L’outil secret Manager stocke les données sensibles pendant le développement d’un projet ASP.NET Core. Dans ce contexte, un élément de données sensibles est un secret d’application. Les secrets de l’application sont stockés dans un emplacement distinct de l’arborescence du projet. Les secrets de l’application sont associés à un projet spécifique ou partagés entre plusieurs projets. Les secrets de l’application ne sont pas archivés dans le contrôle de code source.

> [!WARNING]
> L’outil Gestionnaire de secret ne chiffre pas les secrets stockés et ne doit pas être traité comme un magasin approuvé. À des fins de développement uniquement. Les clés et les valeurs sont stockées dans un fichier de configuration JSON dans le répertoire du profil utilisateur.

## <a name="how-the-secret-manager-tool-works"></a>Fonctionnement de l’outil secret Manager

L’outil Gestionnaire de secret résume les détails de l’implémentation, tels que l’emplacement et la manière dont les valeurs sont stockées. Vous pouvez utiliser l’outil sans connaître ces détails d’implémentation. Les valeurs sont stockées dans un fichier de configuration JSON dans un dossier de profil utilisateur protégé du système sur l’ordinateur local :

# <a name="windows"></a>[Windows](#tab/windows)

Chemin d’accès au système de fichiers :

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Chemin d’accès au système de fichiers :

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Dans les chemins d’accès de fichier précédents, remplacez `<user_secrets_id>` par la `UserSecretsId` valeur spécifiée dans le fichier *. csproj* .

N’écrivez pas de code dépendant de l’emplacement ou du format des données enregistrées avec l’outil secret Manager. Ces détails d’implémentation peuvent changer. Par exemple, les valeurs secrètes ne sont pas chiffrées, mais peuvent être dans le futur.

## <a name="enable-secret-storage"></a>Activer le stockage secret

L’outil Gestionnaire de secret fonctionne sur les paramètres de configuration spécifiques au projet stockés dans votre profil utilisateur.

L’outil Gestionnaire de secret comprend une `init` commande dans kit SDK .net Core 3.0.100 ou version ultérieure. Pour utiliser des secrets d’utilisateur, exécutez la commande suivante dans le répertoire du projet :

```dotnetcli
dotnet user-secrets init
```

La commande précédente ajoute un `UserSecretsId` élément dans un `PropertyGroup` du fichier *. csproj* . Par défaut, le texte interne de `UserSecretsId` est un GUID. Le texte interne est arbitraire, mais il est unique pour le projet.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

Dans Visual Studio, cliquez avec le bouton droit sur le projet dans Explorateur de solutions, puis sélectionnez **gérer les secrets d’utilisateur** dans le menu contextuel. Ce geste ajoute un `UserSecretsId` élément, rempli avec un GUID, au fichier *. csproj* .

## <a name="set-a-secret"></a>Définir une clé secrète

Définissez un secret d’application comprenant une clé et sa valeur. Le secret est associé à la valeur du projet `UserSecretsId` . Par exemple, exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier *. csproj* :

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Dans l’exemple précédent, le signe deux-points indique qu' `Movies` il s’agit d’un littéral d’objet avec une `ServiceApiKey` propriété.

L’outil Gestionnaire de secret peut également être utilisé à partir d’autres annuaires. Utilisez l' `--project` option pour fournir le chemin d’accès au système de fichiers où se trouve le fichier *. csproj* . Exemple :

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Aplatissement de la structure JSON dans Visual Studio

Le geste **gérer les secrets** de l’utilisateur de Visual Studio ouvre un *secrets.jssur* le fichier dans l’éditeur de texte. Remplacez le contenu de *secrets.js* par les paires clé-valeur à stocker. Exemple :

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

La structure JSON est aplatie après des modifications via `dotnet user-secrets remove` ou `dotnet user-secrets set` . Par exemple, l’exécution de `dotnet user-secrets remove "Movies:ConnectionString"` réduit le `Movies` littéral d’objet. Le fichier modifié ressemble à ce qui suit :

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Définir plusieurs secrets

Un lot de secrets peut être défini par la canalisation JSON à la `set` commande. Dans l’exemple suivant, le *input.js* le contenu du fichier est dirigé vers la `set` commande.

# <a name="windows"></a>[Windows](#tab/windows)

Ouvrez une interface de commande, puis exécutez la commande suivante :

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Ouvrez une interface de commande, puis exécutez la commande suivante :

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Accéder à une clé secrète

L' [API de Configuration ASP.net Core](xref:fundamentals/configuration/index) permet d’accéder aux secrets du gestionnaire de secret.

La source de configuration des secrets de l’utilisateur est automatiquement ajoutée en mode de développement lorsque le projet appelle <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> pour initialiser une nouvelle instance de l’hôte avec des valeurs par défaut préconfigurées. `CreateDefaultBuilder` appelle <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> lorsque <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> est <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Quand `CreateDefaultBuilder` n’est pas appelé, ajoutez explicitement la source de configuration des secrets de l’utilisateur en appelant <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> . Appelez `AddUserSecrets` uniquement lorsque l’application s’exécute dans l’environnement de développement, comme illustré dans l’exemple suivant :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

Les secrets de l’utilisateur peuvent être récupérés via l' `Configuration` API :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mapper les secrets à un POCO

Le mappage d’un littéral d’objet entier à un POCO (une classe .NET simple avec des propriétés) est utile pour l’agrégation des propriétés associées.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Pour mapper les secrets précédents à un POCO, utilisez la `Configuration` fonctionnalité de [liaison de graphique d’objets](xref:fundamentals/configuration/index#bind-to-an-object-graph) de l’API. Le code suivant est lié à un `MovieSettings` poco personnalisé et accède à la `ServiceApiKey` valeur de la propriété :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Les `Movies:ConnectionString` `Movies:ServiceApiKey` secrets et sont mappés aux propriétés respectives dans `MovieSettings` :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Remplacement de chaîne par des secrets

Le stockage des mots de passe en texte brut n’est pas sécurisé. Par exemple, une chaîne de connexion de base de données stockée dans *appsettings.json* peut inclure un mot de passe pour l’utilisateur spécifié :

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Une approche plus sécurisée consiste à stocker le mot de passe en tant que secret. Exemple :

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Supprimez la `Password` paire clé-valeur de la chaîne de connexion dans *appsettings.json* . Exemple :

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

La valeur du secret peut être définie sur la <xref:System.Data.SqlClient.SqlConnectionStringBuilder> propriété d’un objet <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> pour terminer la chaîne de connexion :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Répertorier les secrets

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier *. csproj* :

```dotnetcli
dotnet user-secrets list
```

Vous obtenez la sortie suivante :

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Dans l’exemple précédent, un signe deux-points dans les noms de clé désigne la hiérarchie d’objets dans *secrets.jssur* .

## <a name="remove-a-single-secret"></a>Supprimer une seule clé secrète

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier *. csproj* :

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

L'secrets.jsde l’application *sur* le fichier a été modifiée pour supprimer la paire clé-valeur associée à la `MoviesConnectionString` clé :

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list` affiche le message suivant :

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Supprimer tous les secrets

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier *. csproj* :

```dotnetcli
dotnet user-secrets clear
```

Tous les secrets d’utilisateur de l’application ont été supprimés de l' *secrets.jssur* le fichier :

```json
{}
```

`dotnet user-secrets list`L’exécution affiche le message suivant :

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Ressources supplémentaires

* Pour plus d’informations sur l’accès au gestionnaire de secret à partir d’IIS, consultez [ce problème](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)et [Scott Addie](https://github.com/scottaddie)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

Ce document décrit les techniques de stockage et de récupération des données sensibles lors du développement d’une application ASP.NET Core sur un ordinateur de développement. Ne stockez jamais de mots de passe ou d’autres données sensibles dans le code source. Les secrets de production ne doivent pas être utilisés à des fins de développement ou de test. Les secrets ne doivent pas être déployés avec l’application. Au lieu de cela, les secrets doivent être mis à disposition dans l’environnement de production par un moyen contrôlé, comme les variables d’environnement, les Azure Key Vault, etc. Vous pouvez stocker et protéger les secrets de test et de production Azure à l’aide du [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Variables d'environnement

Les variables d’environnement sont utilisées pour éviter le stockage des secrets d’application dans le code ou dans des fichiers de configuration locaux. Les variables d’environnement remplacent les valeurs de configuration pour toutes les sources de configuration spécifiées précédemment.

Prenons l’exemple d’une application Web ASP.NET Core dans laquelle la sécurité **des comptes d’utilisateur individuels** est activée. Une chaîne de connexion de base de données par défaut est incluse dans le fichier du projet *appsettings.json* avec la clé `DefaultConnection` . La chaîne de connexion par défaut est pour la base de données locale, qui s’exécute en mode utilisateur et ne nécessite pas de mot de passe. Pendant le déploiement de l’application, la `DefaultConnection` valeur de clé peut être remplacée par la valeur d’une variable d’environnement. La variable d’environnement peut stocker la chaîne de connexion complète avec des informations d’identification sensibles.

> [!WARNING]
> Les variables d’environnement sont généralement stockées en texte brut et non chiffré. Si l’ordinateur ou le processus est compromis, les variables d’environnement sont accessibles aux parties non fiables. Des mesures supplémentaires pour empêcher la divulgation de secrets d’utilisateur peuvent être nécessaires.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gestionnaire de secret

L’outil secret Manager stocke les données sensibles pendant le développement d’un projet ASP.NET Core. Dans ce contexte, un élément de données sensibles est un secret d’application. Les secrets de l’application sont stockés dans un emplacement distinct de l’arborescence du projet. Les secrets de l’application sont associés à un projet spécifique ou partagés entre plusieurs projets. Les secrets de l’application ne sont pas archivés dans le contrôle de code source.

> [!WARNING]
> L’outil Gestionnaire de secret ne chiffre pas les secrets stockés et ne doit pas être traité comme un magasin approuvé. À des fins de développement uniquement. Les clés et les valeurs sont stockées dans un fichier de configuration JSON dans le répertoire du profil utilisateur.

## <a name="how-the-secret-manager-tool-works"></a>Fonctionnement de l’outil secret Manager

L’outil Gestionnaire de secret résume les détails de l’implémentation, tels que l’emplacement et la manière dont les valeurs sont stockées. Vous pouvez utiliser l’outil sans connaître ces détails d’implémentation. Les valeurs sont stockées dans un fichier de configuration JSON dans un dossier de profil utilisateur protégé du système sur l’ordinateur local :

# <a name="windows"></a>[Windows](#tab/windows)

Chemin d’accès au système de fichiers :

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Chemin d’accès au système de fichiers :

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Dans les chemins d’accès de fichier précédents, remplacez `<user_secrets_id>` par la `UserSecretsId` valeur spécifiée dans le fichier *. csproj* .

N’écrivez pas de code dépendant de l’emplacement ou du format des données enregistrées avec l’outil secret Manager. Ces détails d’implémentation peuvent changer. Par exemple, les valeurs secrètes ne sont pas chiffrées, mais peuvent être dans le futur.

## <a name="enable-secret-storage"></a>Activer le stockage secret

L’outil Gestionnaire de secret fonctionne sur les paramètres de configuration spécifiques au projet stockés dans votre profil utilisateur.

Pour utiliser des secrets d’utilisateur, définissez un `UserSecretsId` élément dans un `PropertyGroup` du fichier *. csproj* . Le texte interne de `UserSecretsId` est arbitraire, mais il est unique pour le projet. Les développeurs génèrent généralement un GUID pour le `UserSecretsId` .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> Dans Visual Studio, cliquez avec le bouton droit sur le projet dans Explorateur de solutions, puis sélectionnez **gérer les secrets d’utilisateur** dans le menu contextuel. Ce geste ajoute un `UserSecretsId` élément, rempli avec un GUID, au fichier *. csproj* .

## <a name="set-a-secret"></a>Définir une clé secrète

Définissez un secret d’application comprenant une clé et sa valeur. Le secret est associé à la valeur du projet `UserSecretsId` . Par exemple, exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier *. csproj* :

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Dans l’exemple précédent, le signe deux-points indique qu' `Movies` il s’agit d’un littéral d’objet avec une `ServiceApiKey` propriété.

L’outil Gestionnaire de secret peut également être utilisé à partir d’autres annuaires. Utilisez l' `--project` option pour fournir le chemin d’accès au système de fichiers où se trouve le fichier *. csproj* . Exemple :

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Aplatissement de la structure JSON dans Visual Studio

Le geste **gérer les secrets** de l’utilisateur de Visual Studio ouvre un *secrets.jssur* le fichier dans l’éditeur de texte. Remplacez le contenu de *secrets.js* par les paires clé-valeur à stocker. Exemple :

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

La structure JSON est aplatie après des modifications via `dotnet user-secrets remove` ou `dotnet user-secrets set` . Par exemple, l’exécution de `dotnet user-secrets remove "Movies:ConnectionString"` réduit le `Movies` littéral d’objet. Le fichier modifié ressemble à ce qui suit :

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Définir plusieurs secrets

Un lot de secrets peut être défini par la canalisation JSON à la `set` commande. Dans l’exemple suivant, le *input.js* le contenu du fichier est dirigé vers la `set` commande.

# <a name="windows"></a>[Windows](#tab/windows)

Ouvrez une interface de commande, puis exécutez la commande suivante :

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Ouvrez une interface de commande, puis exécutez la commande suivante :

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Accéder à une clé secrète

L' [API de Configuration ASP.net Core](xref:fundamentals/configuration/index) permet d’accéder aux secrets du gestionnaire de secret.

Si votre projet cible .NET Framework, installez le [Microsoft.Extensions.Configfiguration. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) Package NuGet UserSecrets.

Dans ASP.NET Core 2,0 ou version ultérieure, la source de configuration des secrets de l’utilisateur est automatiquement ajoutée en mode de développement lorsque le projet appelle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> pour initialiser une nouvelle instance de l’hôte avec des valeurs par défaut préconfigurées. `CreateDefaultBuilder` appelle <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> lorsque <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> est <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Quand `CreateDefaultBuilder` n’est pas appelé, ajoutez explicitement la source de configuration des secrets de l’utilisateur en appelant <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> dans le `Startup` constructeur. Appelez `AddUserSecrets` uniquement lorsque l’application s’exécute dans l’environnement de développement, comme illustré dans l’exemple suivant :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

Les secrets de l’utilisateur peuvent être récupérés via l' `Configuration` API :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mapper les secrets à un POCO

Le mappage d’un littéral d’objet entier à un POCO (une classe .NET simple avec des propriétés) est utile pour l’agrégation des propriétés associées.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Pour mapper les secrets précédents à un POCO, utilisez la `Configuration` fonctionnalité de [liaison de graphique d’objets](xref:fundamentals/configuration/index#bind-to-an-object-graph) de l’API. Le code suivant est lié à un `MovieSettings` poco personnalisé et accède à la `ServiceApiKey` valeur de la propriété :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Les `Movies:ConnectionString` `Movies:ServiceApiKey` secrets et sont mappés aux propriétés respectives dans `MovieSettings` :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Remplacement de chaîne par des secrets

Le stockage des mots de passe en texte brut n’est pas sécurisé. Par exemple, une chaîne de connexion de base de données stockée dans *appsettings.json* peut inclure un mot de passe pour l’utilisateur spécifié :

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Une approche plus sécurisée consiste à stocker le mot de passe en tant que secret. Exemple :

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Supprimez la `Password` paire clé-valeur de la chaîne de connexion dans *appsettings.json* . Exemple :

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

La valeur du secret peut être définie sur la <xref:System.Data.SqlClient.SqlConnectionStringBuilder> propriété d’un objet <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> pour terminer la chaîne de connexion :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Répertorier les secrets

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier *. csproj* :

```dotnetcli
dotnet user-secrets list
```

Vous obtenez la sortie suivante :

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Dans l’exemple précédent, un signe deux-points dans les noms de clé désigne la hiérarchie d’objets dans *secrets.jssur* .

## <a name="remove-a-single-secret"></a>Supprimer une seule clé secrète

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier *. csproj* :

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

L'secrets.jsde l’application *sur* le fichier a été modifiée pour supprimer la paire clé-valeur associée à la `MoviesConnectionString` clé :

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`L’exécution affiche le message suivant :

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Supprimer tous les secrets

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier *. csproj* :

```dotnetcli
dotnet user-secrets clear
```

Tous les secrets d’utilisateur de l’application ont été supprimés de l' *secrets.jssur* le fichier :

```json
{}
```

`dotnet user-secrets list`L’exécution affiche le message suivant :

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Ressources supplémentaires

* Pour plus d’informations sur l’accès au gestionnaire de secret à partir d’IIS, consultez [ce problème](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end
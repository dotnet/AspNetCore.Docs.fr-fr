---
title: Stockage sécurisé des secrets d’application en développement dans ASP.NET Core
author: rick-anderson
description: Découvrez comment stocker et récupérer des informations sensibles pendant le développement d’une application ASP.NET Core.
ms.author: scaddie
ms.custom: mvc, contperf-fy21q2
ms.date: 11/24/2020
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
ms.openlocfilehash: b309b834bc7156b901447c8697e6d2b0156a30f1
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585810"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="30c49-103">Stockage sécurisé des secrets d’application en développement dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="30c49-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="30c49-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)et [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="30c49-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="30c49-105">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/app-secrets/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="30c49-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="30c49-106">Ce document explique comment gérer les données sensibles d’une application ASP.NET Core sur un ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="30c49-106">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="30c49-107">Ne stockez jamais de mots de passe ou d’autres données sensibles dans le code source.</span><span class="sxs-lookup"><span data-stu-id="30c49-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="30c49-108">Les secrets de production ne doivent pas être utilisés à des fins de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="30c49-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="30c49-109">Les secrets ne doivent pas être déployés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="30c49-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="30c49-110">Au lieu de cela, les secrets de production doivent être accessibles via un moyen contrôlé, comme des variables d’environnement ou des Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="30c49-110">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="30c49-111">Vous pouvez stocker et protéger les secrets de test et de production Azure avec le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="30c49-111">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="30c49-112">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="30c49-112">Environment variables</span></span>

<span data-ttu-id="30c49-113">Les variables d’environnement sont utilisées pour éviter le stockage des secrets d’application dans le code ou dans des fichiers de configuration locaux.</span><span class="sxs-lookup"><span data-stu-id="30c49-113">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="30c49-114">Les variables d’environnement remplacent les valeurs de configuration pour toutes les sources de configuration spécifiées précédemment.</span><span class="sxs-lookup"><span data-stu-id="30c49-114">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="30c49-115">Prenons l’exemple d’une application Web ASP.NET Core dans laquelle la sécurité **des comptes d’utilisateur individuels** est activée.</span><span class="sxs-lookup"><span data-stu-id="30c49-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="30c49-116">Une chaîne de connexion de base de données par défaut est incluse dans le fichier du projet *appsettings.json* avec la clé `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="30c49-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="30c49-117">La chaîne de connexion par défaut est pour la base de données locale, qui s’exécute en mode utilisateur et ne nécessite pas de mot de passe.</span><span class="sxs-lookup"><span data-stu-id="30c49-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="30c49-118">Pendant le déploiement de l’application, la `DefaultConnection` valeur de clé peut être remplacée par la valeur d’une variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="30c49-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="30c49-119">La variable d’environnement peut stocker la chaîne de connexion complète avec des informations d’identification sensibles.</span><span class="sxs-lookup"><span data-stu-id="30c49-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="30c49-120">Les variables d’environnement sont généralement stockées en texte brut et non chiffré.</span><span class="sxs-lookup"><span data-stu-id="30c49-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="30c49-121">Si l’ordinateur ou le processus est compromis, les variables d’environnement sont accessibles aux parties non fiables.</span><span class="sxs-lookup"><span data-stu-id="30c49-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="30c49-122">Des mesures supplémentaires pour empêcher la divulgation de secrets d’utilisateur peuvent être nécessaires.</span><span class="sxs-lookup"><span data-stu-id="30c49-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="30c49-123">Gestionnaire de secret</span><span class="sxs-lookup"><span data-stu-id="30c49-123">Secret Manager</span></span>

<span data-ttu-id="30c49-124">L’outil secret Manager stocke les données sensibles pendant le développement d’un projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="30c49-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="30c49-125">Dans ce contexte, un élément de données sensibles est un secret d’application.</span><span class="sxs-lookup"><span data-stu-id="30c49-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="30c49-126">Les secrets de l’application sont stockés dans un emplacement distinct de l’arborescence du projet.</span><span class="sxs-lookup"><span data-stu-id="30c49-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="30c49-127">Les secrets de l’application sont associés à un projet spécifique ou partagés entre plusieurs projets.</span><span class="sxs-lookup"><span data-stu-id="30c49-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="30c49-128">Les secrets de l’application ne sont pas archivés dans le contrôle de code source.</span><span class="sxs-lookup"><span data-stu-id="30c49-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="30c49-129">L’outil Gestionnaire de secret ne chiffre pas les secrets stockés et ne doit pas être traité comme un magasin approuvé.</span><span class="sxs-lookup"><span data-stu-id="30c49-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="30c49-130">À des fins de développement uniquement.</span><span class="sxs-lookup"><span data-stu-id="30c49-130">It's for development purposes only.</span></span> <span data-ttu-id="30c49-131">Les clés et les valeurs sont stockées dans un fichier de configuration JSON dans le répertoire du profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="30c49-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="30c49-132">Fonctionnement de l’outil secret Manager</span><span class="sxs-lookup"><span data-stu-id="30c49-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="30c49-133">L’outil Gestionnaire de secret masque les détails de l’implémentation, tels que l’emplacement et la manière dont les valeurs sont stockées.</span><span class="sxs-lookup"><span data-stu-id="30c49-133">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="30c49-134">Vous pouvez utiliser l’outil sans connaître ces détails d’implémentation.</span><span class="sxs-lookup"><span data-stu-id="30c49-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="30c49-135">Les valeurs sont stockées dans un fichier JSON dans le dossier de profil utilisateur de l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="30c49-135">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="30c49-136">Windows</span><span class="sxs-lookup"><span data-stu-id="30c49-136">Windows</span></span>](#tab/windows)

<span data-ttu-id="30c49-137">Chemin d’accès au système de fichiers :</span><span class="sxs-lookup"><span data-stu-id="30c49-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="30c49-138">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="30c49-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="30c49-139">Chemin d’accès au système de fichiers :</span><span class="sxs-lookup"><span data-stu-id="30c49-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="30c49-140">Dans les chemins d’accès de fichier précédents, remplacez `<user_secrets_id>` par la `UserSecretsId` valeur spécifiée dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="30c49-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="30c49-141">N’écrivez pas de code dépendant de l’emplacement ou du format des données enregistrées avec l’outil secret Manager.</span><span class="sxs-lookup"><span data-stu-id="30c49-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="30c49-142">Ces détails d’implémentation peuvent changer.</span><span class="sxs-lookup"><span data-stu-id="30c49-142">These implementation details may change.</span></span> <span data-ttu-id="30c49-143">Par exemple, les valeurs secrètes ne sont pas chiffrées, mais peuvent être dans le futur.</span><span class="sxs-lookup"><span data-stu-id="30c49-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="30c49-144">Activer le stockage secret</span><span class="sxs-lookup"><span data-stu-id="30c49-144">Enable secret storage</span></span>

<span data-ttu-id="30c49-145">L’outil Gestionnaire de secret fonctionne sur les paramètres de configuration spécifiques au projet stockés dans votre profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="30c49-145">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="30c49-146">L’outil Gestionnaire de secret comprend une `init` commande dans kit SDK .net Core 3.0.100 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="30c49-146">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="30c49-147">Pour utiliser des secrets d’utilisateur, exécutez la commande suivante dans le répertoire du projet :</span><span class="sxs-lookup"><span data-stu-id="30c49-147">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="30c49-148">La commande précédente ajoute un `UserSecretsId` élément dans un `PropertyGroup` du fichier projet.</span><span class="sxs-lookup"><span data-stu-id="30c49-148">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="30c49-149">Par défaut, le texte interne de `UserSecretsId` est un GUID.</span><span class="sxs-lookup"><span data-stu-id="30c49-149">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="30c49-150">Le texte interne est arbitraire, mais il est unique pour le projet.</span><span class="sxs-lookup"><span data-stu-id="30c49-150">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="30c49-151">Dans Visual Studio, cliquez avec le bouton droit sur le projet dans Explorateur de solutions, puis sélectionnez **gérer les secrets d’utilisateur** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="30c49-151">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="30c49-152">Ce geste ajoute un `UserSecretsId` élément, rempli avec un GUID, au fichier projet.</span><span class="sxs-lookup"><span data-stu-id="30c49-152">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="30c49-153">Définir une clé secrète</span><span class="sxs-lookup"><span data-stu-id="30c49-153">Set a secret</span></span>

<span data-ttu-id="30c49-154">Définissez un secret d’application comprenant une clé et sa valeur.</span><span class="sxs-lookup"><span data-stu-id="30c49-154">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="30c49-155">Le secret est associé à la valeur du projet `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="30c49-155">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="30c49-156">Par exemple, exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="30c49-156">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="30c49-157">Dans l’exemple précédent, le signe deux-points indique qu' `Movies` il s’agit d’un littéral d’objet avec une `ServiceApiKey` propriété.</span><span class="sxs-lookup"><span data-stu-id="30c49-157">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="30c49-158">L’outil Gestionnaire de secret peut également être utilisé à partir d’autres annuaires.</span><span class="sxs-lookup"><span data-stu-id="30c49-158">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="30c49-159">Utilisez l' `--project` option pour fournir le chemin d’accès au système de fichiers où se trouve le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="30c49-159">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="30c49-160">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="30c49-160">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="30c49-161">Aplatissement de la structure JSON dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30c49-161">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="30c49-162">Le geste **gérer les secrets** de l’utilisateur de Visual Studio ouvre un *secrets.jssur* le fichier dans l’éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="30c49-162">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="30c49-163">Remplacez le contenu de *secrets.js* par les paires clé-valeur à stocker.</span><span class="sxs-lookup"><span data-stu-id="30c49-163">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="30c49-164">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="30c49-164">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="30c49-165">La structure JSON est aplatie après des modifications via `dotnet user-secrets remove` ou `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="30c49-165">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="30c49-166">Par exemple, l’exécution de `dotnet user-secrets remove "Movies:ConnectionString"` réduit le `Movies` littéral d’objet.</span><span class="sxs-lookup"><span data-stu-id="30c49-166">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="30c49-167">Le fichier modifié ressemble au code JSON suivant :</span><span class="sxs-lookup"><span data-stu-id="30c49-167">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="30c49-168">Définir plusieurs secrets</span><span class="sxs-lookup"><span data-stu-id="30c49-168">Set multiple secrets</span></span>

<span data-ttu-id="30c49-169">Un lot de secrets peut être défini par la canalisation JSON à la `set` commande.</span><span class="sxs-lookup"><span data-stu-id="30c49-169">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="30c49-170">Dans l’exemple suivant, le *input.js* le contenu du fichier est dirigé vers la `set` commande.</span><span class="sxs-lookup"><span data-stu-id="30c49-170">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="30c49-171">Windows</span><span class="sxs-lookup"><span data-stu-id="30c49-171">Windows</span></span>](#tab/windows)

<span data-ttu-id="30c49-172">Ouvrez une interface de commande, puis exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="30c49-172">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="30c49-173">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="30c49-173">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="30c49-174">Ouvrez une interface de commande, puis exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="30c49-174">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="30c49-175">Accéder à une clé secrète</span><span class="sxs-lookup"><span data-stu-id="30c49-175">Access a secret</span></span>

<span data-ttu-id="30c49-176">Pour accéder à un secret, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="30c49-176">To access a secret, complete the following steps:</span></span>

1. [<span data-ttu-id="30c49-177">Inscrire la source de configuration des secrets de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="30c49-177">Register the user secrets configuration source</span></span>](#register-the-user-secrets-configuration-source)
1. [<span data-ttu-id="30c49-178">Lire le secret via l’API de configuration</span><span class="sxs-lookup"><span data-stu-id="30c49-178">Read the secret via the Configuration API</span></span>](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a><span data-ttu-id="30c49-179">Inscrire la source de configuration des secrets de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="30c49-179">Register the user secrets configuration source</span></span>

<span data-ttu-id="30c49-180">Le fournisseur de [configuration](/dotnet/core/extensions/configuration-providers) des secrets de l’utilisateur inscrit la source de configuration appropriée auprès de l' [API de configuration](xref:fundamentals/configuration/index).net.</span><span class="sxs-lookup"><span data-stu-id="30c49-180">The user secrets [configuration provider](/dotnet/core/extensions/configuration-providers) registers the appropriate configuration source with the .NET [Configuration API](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="30c49-181">La source de configuration des secrets de l’utilisateur est automatiquement ajoutée en mode de développement lorsque le projet appelle <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="30c49-181">The user secrets configuration source is automatically added in Development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="30c49-182">`CreateDefaultBuilder` appelle <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> lorsque <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> est <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="30c49-182">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="30c49-183">Quand `CreateDefaultBuilder` n’est pas appelé, ajoutez explicitement la source de configuration des secrets de l’utilisateur en appelant <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> .</span><span class="sxs-lookup"><span data-stu-id="30c49-183">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="30c49-184">Appelez `AddUserSecrets` uniquement lorsque l’application s’exécute dans l’environnement de développement, comme illustré dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="30c49-184">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a><span data-ttu-id="30c49-185">Lire le secret via l’API de configuration</span><span class="sxs-lookup"><span data-stu-id="30c49-185">Read the secret via the Configuration API</span></span>

<span data-ttu-id="30c49-186">Si la source de configuration des secrets de l’utilisateur est inscrite, l’API de configuration .NET peut lire les secrets.</span><span class="sxs-lookup"><span data-stu-id="30c49-186">If the user secrets configuration source is registered, the .NET Configuration API can read the secrets.</span></span> <span data-ttu-id="30c49-187">L' [injection de constructeur](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) peut être utilisée pour accéder à l’API de configuration .net.</span><span class="sxs-lookup"><span data-stu-id="30c49-187">[Constructor injection](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) can be used to gain access to the .NET Configuration API.</span></span> <span data-ttu-id="30c49-188">Prenons les exemples suivants de la lecture de la `Movies:ServiceApiKey` clé :</span><span class="sxs-lookup"><span data-stu-id="30c49-188">Consider the following examples of reading the `Movies:ServiceApiKey` key:</span></span>

<span data-ttu-id="30c49-189">**Classe de démarrage :**</span><span class="sxs-lookup"><span data-stu-id="30c49-189">**Startup class:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

<span data-ttu-id="30c49-190">**Razor Modèle de page pages :**</span><span class="sxs-lookup"><span data-stu-id="30c49-190">**Razor Pages page model:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

<span data-ttu-id="30c49-191">Pour plus d’informations, consultez [configuration de l’accès dans démarrage](xref:fundamentals/configuration/index#access-configuration-in-startup) et [configuration de l’accès dans les Razor pages](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span><span class="sxs-lookup"><span data-stu-id="30c49-191">For more information, see [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup) and [Access configuration in Razor Pages](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span></span>

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="30c49-192">Mapper les secrets à un POCO</span><span class="sxs-lookup"><span data-stu-id="30c49-192">Map secrets to a POCO</span></span>

<span data-ttu-id="30c49-193">Le mappage d’un littéral d’objet entier à un POCO (une classe .NET simple avec des propriétés) est utile pour l’agrégation des propriétés associées.</span><span class="sxs-lookup"><span data-stu-id="30c49-193">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="30c49-194">Pour mapper les secrets précédents à un POCO, utilisez la fonctionnalité de liaison de [graphique d’objets](xref:fundamentals/configuration/index#bind-to-an-object-graph) de l’API de configuration .net.</span><span class="sxs-lookup"><span data-stu-id="30c49-194">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="30c49-195">Le code suivant est lié à un `MovieSettings` poco personnalisé et accède à la `ServiceApiKey` valeur de la propriété :</span><span class="sxs-lookup"><span data-stu-id="30c49-195">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="30c49-196">Les `Movies:ConnectionString` `Movies:ServiceApiKey` secrets et sont mappés aux propriétés respectives dans `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="30c49-196">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="30c49-197">Remplacement de chaîne par des secrets</span><span class="sxs-lookup"><span data-stu-id="30c49-197">String replacement with secrets</span></span>

<span data-ttu-id="30c49-198">Le stockage des mots de passe en texte brut n’est pas sécurisé.</span><span class="sxs-lookup"><span data-stu-id="30c49-198">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="30c49-199">Par exemple, une chaîne de connexion de base de données stockée dans *appsettings.json* peut inclure un mot de passe pour l’utilisateur spécifié :</span><span class="sxs-lookup"><span data-stu-id="30c49-199">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="30c49-200">Une approche plus sécurisée consiste à stocker le mot de passe en tant que secret.</span><span class="sxs-lookup"><span data-stu-id="30c49-200">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="30c49-201">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="30c49-201">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="30c49-202">Supprimez la `Password` paire clé-valeur de la chaîne de connexion dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="30c49-202">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="30c49-203">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="30c49-203">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="30c49-204">La valeur du secret peut être définie sur la <xref:System.Data.SqlClient.SqlConnectionStringBuilder> propriété d’un objet <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> pour terminer la chaîne de connexion :</span><span class="sxs-lookup"><span data-stu-id="30c49-204">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="30c49-205">Répertorier les secrets</span><span class="sxs-lookup"><span data-stu-id="30c49-205">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="30c49-206">Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="30c49-206">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="30c49-207">Vous obtenez la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="30c49-207">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="30c49-208">Dans l’exemple précédent, un signe deux-points dans les noms de clé désigne la hiérarchie d’objets dans *secrets.jssur*.</span><span class="sxs-lookup"><span data-stu-id="30c49-208">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="30c49-209">Supprimer une seule clé secrète</span><span class="sxs-lookup"><span data-stu-id="30c49-209">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="30c49-210">Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="30c49-210">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="30c49-211">L'secrets.jsde l’application *sur* le fichier a été modifiée pour supprimer la paire clé-valeur associée à la `MoviesConnectionString` clé :</span><span class="sxs-lookup"><span data-stu-id="30c49-211">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="30c49-212">`dotnet user-secrets list` affiche le message suivant :</span><span class="sxs-lookup"><span data-stu-id="30c49-212">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="30c49-213">Supprimer tous les secrets</span><span class="sxs-lookup"><span data-stu-id="30c49-213">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="30c49-214">Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="30c49-214">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="30c49-215">Tous les secrets d’utilisateur de l’application ont été supprimés de l' *secrets.jssur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="30c49-215">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="30c49-216">`dotnet user-secrets list`L’exécution affiche le message suivant :</span><span class="sxs-lookup"><span data-stu-id="30c49-216">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="30c49-217">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="30c49-217">Additional resources</span></span>

* <span data-ttu-id="30c49-218">Pour plus d’informations sur l’accès aux secrets des utilisateurs à partir d’IIS, consultez [ce numéro](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="30c49-218">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="30c49-219">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)et [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="30c49-219">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="30c49-220">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/app-secrets/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="30c49-220">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="30c49-221">Ce document explique comment gérer les données sensibles d’une application ASP.NET Core sur un ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="30c49-221">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="30c49-222">Ne stockez jamais de mots de passe ou d’autres données sensibles dans le code source.</span><span class="sxs-lookup"><span data-stu-id="30c49-222">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="30c49-223">Les secrets de production ne doivent pas être utilisés à des fins de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="30c49-223">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="30c49-224">Les secrets ne doivent pas être déployés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="30c49-224">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="30c49-225">Au lieu de cela, les secrets de production doivent être accessibles via un moyen contrôlé, comme des variables d’environnement ou des Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="30c49-225">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="30c49-226">Vous pouvez stocker et protéger les secrets de test et de production Azure avec le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="30c49-226">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="30c49-227">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="30c49-227">Environment variables</span></span>

<span data-ttu-id="30c49-228">Les variables d’environnement sont utilisées pour éviter le stockage des secrets d’application dans le code ou dans des fichiers de configuration locaux.</span><span class="sxs-lookup"><span data-stu-id="30c49-228">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="30c49-229">Les variables d’environnement remplacent les valeurs de configuration pour toutes les sources de configuration spécifiées précédemment.</span><span class="sxs-lookup"><span data-stu-id="30c49-229">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="30c49-230">Prenons l’exemple d’une application Web ASP.NET Core dans laquelle la sécurité **des comptes d’utilisateur individuels** est activée.</span><span class="sxs-lookup"><span data-stu-id="30c49-230">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="30c49-231">Une chaîne de connexion de base de données par défaut est incluse dans le fichier du projet *appsettings.json* avec la clé `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="30c49-231">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="30c49-232">La chaîne de connexion par défaut est pour la base de données locale, qui s’exécute en mode utilisateur et ne nécessite pas de mot de passe.</span><span class="sxs-lookup"><span data-stu-id="30c49-232">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="30c49-233">Pendant le déploiement de l’application, la `DefaultConnection` valeur de clé peut être remplacée par la valeur d’une variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="30c49-233">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="30c49-234">La variable d’environnement peut stocker la chaîne de connexion complète avec des informations d’identification sensibles.</span><span class="sxs-lookup"><span data-stu-id="30c49-234">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="30c49-235">Les variables d’environnement sont généralement stockées en texte brut et non chiffré.</span><span class="sxs-lookup"><span data-stu-id="30c49-235">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="30c49-236">Si l’ordinateur ou le processus est compromis, les variables d’environnement sont accessibles aux parties non fiables.</span><span class="sxs-lookup"><span data-stu-id="30c49-236">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="30c49-237">Des mesures supplémentaires pour empêcher la divulgation de secrets d’utilisateur peuvent être nécessaires.</span><span class="sxs-lookup"><span data-stu-id="30c49-237">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="30c49-238">Gestionnaire de secret</span><span class="sxs-lookup"><span data-stu-id="30c49-238">Secret Manager</span></span>

<span data-ttu-id="30c49-239">L’outil secret Manager stocke les données sensibles pendant le développement d’un projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="30c49-239">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="30c49-240">Dans ce contexte, un élément de données sensibles est un secret d’application.</span><span class="sxs-lookup"><span data-stu-id="30c49-240">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="30c49-241">Les secrets de l’application sont stockés dans un emplacement distinct de l’arborescence du projet.</span><span class="sxs-lookup"><span data-stu-id="30c49-241">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="30c49-242">Les secrets de l’application sont associés à un projet spécifique ou partagés entre plusieurs projets.</span><span class="sxs-lookup"><span data-stu-id="30c49-242">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="30c49-243">Les secrets de l’application ne sont pas archivés dans le contrôle de code source.</span><span class="sxs-lookup"><span data-stu-id="30c49-243">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="30c49-244">L’outil Gestionnaire de secret ne chiffre pas les secrets stockés et ne doit pas être traité comme un magasin approuvé.</span><span class="sxs-lookup"><span data-stu-id="30c49-244">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="30c49-245">À des fins de développement uniquement.</span><span class="sxs-lookup"><span data-stu-id="30c49-245">It's for development purposes only.</span></span> <span data-ttu-id="30c49-246">Les clés et les valeurs sont stockées dans un fichier de configuration JSON dans le répertoire du profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="30c49-246">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="30c49-247">Fonctionnement de l’outil secret Manager</span><span class="sxs-lookup"><span data-stu-id="30c49-247">How the Secret Manager tool works</span></span>

<span data-ttu-id="30c49-248">L’outil Gestionnaire de secret masque les détails de l’implémentation, tels que l’emplacement et la manière dont les valeurs sont stockées.</span><span class="sxs-lookup"><span data-stu-id="30c49-248">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="30c49-249">Vous pouvez utiliser l’outil sans connaître ces détails d’implémentation.</span><span class="sxs-lookup"><span data-stu-id="30c49-249">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="30c49-250">Les valeurs sont stockées dans un fichier JSON dans le dossier de profil utilisateur de l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="30c49-250">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="30c49-251">Windows</span><span class="sxs-lookup"><span data-stu-id="30c49-251">Windows</span></span>](#tab/windows)

<span data-ttu-id="30c49-252">Chemin d’accès au système de fichiers :</span><span class="sxs-lookup"><span data-stu-id="30c49-252">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="30c49-253">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="30c49-253">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="30c49-254">Chemin d’accès au système de fichiers :</span><span class="sxs-lookup"><span data-stu-id="30c49-254">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="30c49-255">Dans les chemins d’accès de fichier précédents, remplacez `<user_secrets_id>` par la `UserSecretsId` valeur spécifiée dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="30c49-255">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="30c49-256">N’écrivez pas de code dépendant de l’emplacement ou du format des données enregistrées avec l’outil secret Manager.</span><span class="sxs-lookup"><span data-stu-id="30c49-256">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="30c49-257">Ces détails d’implémentation peuvent changer.</span><span class="sxs-lookup"><span data-stu-id="30c49-257">These implementation details may change.</span></span> <span data-ttu-id="30c49-258">Par exemple, les valeurs secrètes ne sont pas chiffrées, mais peuvent être dans le futur.</span><span class="sxs-lookup"><span data-stu-id="30c49-258">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="30c49-259">Activer le stockage secret</span><span class="sxs-lookup"><span data-stu-id="30c49-259">Enable secret storage</span></span>

<span data-ttu-id="30c49-260">L’outil Gestionnaire de secret fonctionne sur les paramètres de configuration spécifiques au projet stockés dans votre profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="30c49-260">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="30c49-261">Pour utiliser des secrets d’utilisateur, définissez un `UserSecretsId` élément dans un `PropertyGroup` du fichier projet.</span><span class="sxs-lookup"><span data-stu-id="30c49-261">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="30c49-262">Le texte interne de `UserSecretsId` est arbitraire, mais il est unique pour le projet.</span><span class="sxs-lookup"><span data-stu-id="30c49-262">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="30c49-263">Les développeurs génèrent généralement un GUID pour le `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="30c49-263">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="30c49-264">Dans Visual Studio, cliquez avec le bouton droit sur le projet dans Explorateur de solutions, puis sélectionnez **gérer les secrets d’utilisateur** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="30c49-264">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="30c49-265">Ce geste ajoute un `UserSecretsId` élément, rempli avec un GUID, au fichier projet.</span><span class="sxs-lookup"><span data-stu-id="30c49-265">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="30c49-266">Définir une clé secrète</span><span class="sxs-lookup"><span data-stu-id="30c49-266">Set a secret</span></span>

<span data-ttu-id="30c49-267">Définissez un secret d’application comprenant une clé et sa valeur.</span><span class="sxs-lookup"><span data-stu-id="30c49-267">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="30c49-268">Le secret est associé à la valeur du projet `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="30c49-268">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="30c49-269">Par exemple, exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="30c49-269">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="30c49-270">Dans l’exemple précédent, le signe deux-points indique qu' `Movies` il s’agit d’un littéral d’objet avec une `ServiceApiKey` propriété.</span><span class="sxs-lookup"><span data-stu-id="30c49-270">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="30c49-271">L’outil Gestionnaire de secret peut également être utilisé à partir d’autres annuaires.</span><span class="sxs-lookup"><span data-stu-id="30c49-271">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="30c49-272">Utilisez l' `--project` option pour fournir le chemin d’accès au système de fichiers où se trouve le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="30c49-272">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="30c49-273">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="30c49-273">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="30c49-274">Aplatissement de la structure JSON dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30c49-274">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="30c49-275">Le geste **gérer les secrets** de l’utilisateur de Visual Studio ouvre un *secrets.jssur* le fichier dans l’éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="30c49-275">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="30c49-276">Remplacez le contenu de *secrets.js* par les paires clé-valeur à stocker.</span><span class="sxs-lookup"><span data-stu-id="30c49-276">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="30c49-277">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="30c49-277">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="30c49-278">La structure JSON est aplatie après des modifications via `dotnet user-secrets remove` ou `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="30c49-278">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="30c49-279">Par exemple, l’exécution de `dotnet user-secrets remove "Movies:ConnectionString"` réduit le `Movies` littéral d’objet.</span><span class="sxs-lookup"><span data-stu-id="30c49-279">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="30c49-280">Le fichier modifié ressemble au code JSON suivant :</span><span class="sxs-lookup"><span data-stu-id="30c49-280">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="30c49-281">Définir plusieurs secrets</span><span class="sxs-lookup"><span data-stu-id="30c49-281">Set multiple secrets</span></span>

<span data-ttu-id="30c49-282">Un lot de secrets peut être défini par la canalisation JSON à la `set` commande.</span><span class="sxs-lookup"><span data-stu-id="30c49-282">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="30c49-283">Dans l’exemple suivant, le *input.js* le contenu du fichier est dirigé vers la `set` commande.</span><span class="sxs-lookup"><span data-stu-id="30c49-283">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="30c49-284">Windows</span><span class="sxs-lookup"><span data-stu-id="30c49-284">Windows</span></span>](#tab/windows)

<span data-ttu-id="30c49-285">Ouvrez une interface de commande, puis exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="30c49-285">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="30c49-286">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="30c49-286">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="30c49-287">Ouvrez une interface de commande, puis exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="30c49-287">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="30c49-288">Accéder à une clé secrète</span><span class="sxs-lookup"><span data-stu-id="30c49-288">Access a secret</span></span>

<span data-ttu-id="30c49-289">L' [API de configuration](xref:fundamentals/configuration/index) fournit l’accès aux secrets de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="30c49-289">The [Configuration API](xref:fundamentals/configuration/index) provides access to user secrets.</span></span>

<span data-ttu-id="30c49-290">Si votre projet cible .NET Framework, installez le [Microsoft.Extensions.Configfiguration. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) Package NuGet UserSecrets.</span><span class="sxs-lookup"><span data-stu-id="30c49-290">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="30c49-291">Dans ASP.NET Core 2,0 ou version ultérieure, la source de configuration des secrets de l’utilisateur est automatiquement ajoutée en mode de développement lorsque le projet appelle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="30c49-291">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="30c49-292">`CreateDefaultBuilder` appelle <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> lorsque <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> est <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="30c49-292">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="30c49-293">Quand `CreateDefaultBuilder` n’est pas appelé, ajoutez explicitement la source de configuration des secrets de l’utilisateur en appelant <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> dans le `Startup` constructeur.</span><span class="sxs-lookup"><span data-stu-id="30c49-293">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="30c49-294">Appelez `AddUserSecrets` uniquement lorsque l’application s’exécute dans l’environnement de développement, comme illustré dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="30c49-294">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="30c49-295">Les secrets de l’utilisateur peuvent être récupérés via l’API de configuration .NET :</span><span class="sxs-lookup"><span data-stu-id="30c49-295">User secrets can be retrieved via the .NET Configuration API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="30c49-296">Mapper les secrets à un POCO</span><span class="sxs-lookup"><span data-stu-id="30c49-296">Map secrets to a POCO</span></span>

<span data-ttu-id="30c49-297">Le mappage d’un littéral d’objet entier à un POCO (une classe .NET simple avec des propriétés) est utile pour l’agrégation des propriétés associées.</span><span class="sxs-lookup"><span data-stu-id="30c49-297">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="30c49-298">Pour mapper les secrets précédents à un POCO, utilisez la fonctionnalité de liaison de [graphique d’objets](xref:fundamentals/configuration/index#bind-to-an-object-graph) de l’API de configuration .net.</span><span class="sxs-lookup"><span data-stu-id="30c49-298">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="30c49-299">Le code suivant est lié à un `MovieSettings` poco personnalisé et accède à la `ServiceApiKey` valeur de la propriété :</span><span class="sxs-lookup"><span data-stu-id="30c49-299">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="30c49-300">Les `Movies:ConnectionString` `Movies:ServiceApiKey` secrets et sont mappés aux propriétés respectives dans `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="30c49-300">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="30c49-301">Remplacement de chaîne par des secrets</span><span class="sxs-lookup"><span data-stu-id="30c49-301">String replacement with secrets</span></span>

<span data-ttu-id="30c49-302">Le stockage des mots de passe en texte brut n’est pas sécurisé.</span><span class="sxs-lookup"><span data-stu-id="30c49-302">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="30c49-303">Par exemple, une chaîne de connexion de base de données stockée dans *appsettings.json* peut inclure un mot de passe pour l’utilisateur spécifié :</span><span class="sxs-lookup"><span data-stu-id="30c49-303">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="30c49-304">Une approche plus sécurisée consiste à stocker le mot de passe en tant que secret.</span><span class="sxs-lookup"><span data-stu-id="30c49-304">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="30c49-305">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="30c49-305">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="30c49-306">Supprimez la `Password` paire clé-valeur de la chaîne de connexion dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="30c49-306">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="30c49-307">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="30c49-307">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="30c49-308">La valeur du secret peut être définie sur la <xref:System.Data.SqlClient.SqlConnectionStringBuilder> propriété d’un objet <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> pour terminer la chaîne de connexion :</span><span class="sxs-lookup"><span data-stu-id="30c49-308">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="30c49-309">Répertorier les secrets</span><span class="sxs-lookup"><span data-stu-id="30c49-309">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="30c49-310">Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="30c49-310">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="30c49-311">Vous obtenez la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="30c49-311">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="30c49-312">Dans l’exemple précédent, un signe deux-points dans les noms de clé désigne la hiérarchie d’objets dans *secrets.jssur*.</span><span class="sxs-lookup"><span data-stu-id="30c49-312">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="30c49-313">Supprimer une seule clé secrète</span><span class="sxs-lookup"><span data-stu-id="30c49-313">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="30c49-314">Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="30c49-314">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="30c49-315">L'secrets.jsde l’application *sur* le fichier a été modifiée pour supprimer la paire clé-valeur associée à la `MoviesConnectionString` clé :</span><span class="sxs-lookup"><span data-stu-id="30c49-315">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="30c49-316">`dotnet user-secrets list`L’exécution affiche le message suivant :</span><span class="sxs-lookup"><span data-stu-id="30c49-316">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="30c49-317">Supprimer tous les secrets</span><span class="sxs-lookup"><span data-stu-id="30c49-317">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="30c49-318">Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="30c49-318">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="30c49-319">Tous les secrets d’utilisateur de l’application ont été supprimés de l' *secrets.jssur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="30c49-319">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="30c49-320">`dotnet user-secrets list`L’exécution affiche le message suivant :</span><span class="sxs-lookup"><span data-stu-id="30c49-320">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="30c49-321">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="30c49-321">Additional resources</span></span>

* <span data-ttu-id="30c49-322">Pour plus d’informations sur l’accès aux secrets des utilisateurs à partir d’IIS, consultez [ce numéro](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="30c49-322">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end
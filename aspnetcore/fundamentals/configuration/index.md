---
title: Configuration dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser l’API de configuration pour configurer une application ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
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
uid: fundamentals/configuration/index
ms.openlocfilehash: 97ee00dd37ed4eef1c013e0f45b598a79f3f260c
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035864"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="e61a2-103">Configuration dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e61a2-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="e61a2-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="e61a2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e61a2-105">La configuration dans ASP.NET Core est effectuée à l’aide d’un ou de plusieurs [fournisseurs de configuration](#cp).</span><span class="sxs-lookup"><span data-stu-id="e61a2-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="e61a2-106">Les fournisseurs de configuration lisent les données de configuration des paires clé-valeur à l’aide d’une variété de sources de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="e61a2-107">Les fichiers de paramètres, tels que *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="e61a2-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="e61a2-108">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="e61a2-108">Environment variables</span></span>
* <span data-ttu-id="e61a2-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e61a2-109">Azure Key Vault</span></span>
* <span data-ttu-id="e61a2-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="e61a2-110">Azure App Configuration</span></span>
* <span data-ttu-id="e61a2-111">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e61a2-111">Command-line arguments</span></span>
* <span data-ttu-id="e61a2-112">Fournisseurs personnalisés, installés ou créés</span><span class="sxs-lookup"><span data-stu-id="e61a2-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="e61a2-113">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="e61a2-113">Directory files</span></span>
* <span data-ttu-id="e61a2-114">Objets .NET en mémoire</span><span class="sxs-lookup"><span data-stu-id="e61a2-114">In-memory .NET objects</span></span>

<span data-ttu-id="e61a2-115">Cette rubrique fournit des informations sur la configuration dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e61a2-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="e61a2-116">Pour plus d’informations sur l’utilisation de la configuration dans les applications console, consultez [Configuration .net](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="e61a2-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="e61a2-117">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e61a2-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="e61a2-118">Configuration par défaut</span><span class="sxs-lookup"><span data-stu-id="e61a2-118">Default configuration</span></span>

<span data-ttu-id="e61a2-119">ASP.NET Core les applications Web créées avec [dotnet New](/dotnet/core/tools/dotnet-new) ou Visual Studio génèrent le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e61a2-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="e61a2-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fournit la configuration par défaut de l’application dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="e61a2-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="e61a2-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : ajoute un existant `IConfiguration` en tant que source.</span><span class="sxs-lookup"><span data-stu-id="e61a2-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="e61a2-122">Dans le cas de configuration par défaut, ajoute la configuration d' [hôte](#hvac) et la définit en tant que première source de la configuration de l' _application_ .</span><span class="sxs-lookup"><span data-stu-id="e61a2-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="e61a2-123">[appsettings.json](#appsettingsjson) utilisation du [fournisseur de configuration JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e61a2-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="e61a2-124">*appSettings.* `Environment` *. JSON* à l’aide du [fournisseur de configuration JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e61a2-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="e61a2-125">Par exemple, *appSettings*. ***Production \* \* _._json* et *appSettings*. \* \* \* développement** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="e61a2-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="e61a2-126">[Secrets d’application](xref:security/app-secrets) lorsque l’application s’exécute dans l' `Development` environnement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="e61a2-127">Variables d’environnement à l’aide du [fournisseur de configuration des variables d’environnement](#evcp).</span><span class="sxs-lookup"><span data-stu-id="e61a2-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="e61a2-128">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line).</span><span class="sxs-lookup"><span data-stu-id="e61a2-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="e61a2-129">Les fournisseurs de configuration ajoutés ultérieurement remplacent les paramètres de clé précédents.</span><span class="sxs-lookup"><span data-stu-id="e61a2-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="e61a2-130">Par exemple, si `MyKey` est défini à la fois dans *appsettings.json* et dans l’environnement, la valeur d’environnement est utilisée.</span><span class="sxs-lookup"><span data-stu-id="e61a2-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="e61a2-131">À l’aide des fournisseurs de configuration par défaut, le  [fournisseur de configuration de ligne de commande](#clcp) remplace tous les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="e61a2-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="e61a2-132">Pour plus d’informations sur `CreateDefaultBuilder` , consultez [paramètres par défaut du générateur](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="e61a2-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="e61a2-133">Le code suivant affiche les fournisseurs de configuration activés dans l’ordre dans lequel ils ont été ajoutés :</span><span class="sxs-lookup"><span data-stu-id="e61a2-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="e61a2-134">Prenons le *appsettings.json* fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="e61a2-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="e61a2-135">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="e61a2-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="e61a2-136">La configuration par défaut <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="e61a2-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="e61a2-137">*appSettings.* `Environment` *. JSON* : par exemple, *appSettings*. **Les fichiers de *production \* \* _._json* et *appSettings*. \* \* \* Development** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="e61a2-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="e61a2-138">La version de l’environnement du fichier est chargée à partir de [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="e61a2-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="e61a2-139">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="e61a2-140">*appSettings*. `Environment` . les valeurs *JSON* remplacent les clés dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e61a2-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="e61a2-141">Par exemple, par défaut :</span><span class="sxs-lookup"><span data-stu-id="e61a2-141">For example, by default:</span></span>

* <span data-ttu-id="e61a2-142">Dans le développement, *appSettings*. \***Development** _._json \* configuration remplace les valeurs trouvées dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e61a2-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="e61a2-143">En production, *appSettings*. \***production** _._json \* configuration remplace les valeurs trouvées dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e61a2-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="e61a2-144">Par exemple, lors du déploiement de l’application sur Azure.</span><span class="sxs-lookup"><span data-stu-id="e61a2-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="e61a2-145">Lier des données de configuration hiérarchiques à l’aide du modèle options</span><span class="sxs-lookup"><span data-stu-id="e61a2-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="e61a2-146">À l’aide de la configuration [par défaut](#default) , de *appsettings.json* et de *appSettings.* `Environment` les fichiers *. JSON* sont activés avec [reloadOnChange : true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="e61a2-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="e61a2-147">Modifications apportées *appsettings.json* aux *appSettings et.* `Environment` fichier *. JSON* \***après** _ le démarrage de l’application est lu par le [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="e61a2-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file \***after** _ the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="e61a2-148">Pour plus d’informations sur l’ajout de fichiers de configuration JSON supplémentaires, consultez [fournisseur de configuration JSON](#jcp) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="e61a2-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="e61a2-149">Combinaison de la collection de services</span><span class="sxs-lookup"><span data-stu-id="e61a2-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a><span data-ttu-id="e61a2-150">Sécurité et secrets de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="e61a2-150">Security and user secrets</span></span>

<span data-ttu-id="e61a2-151">Instructions relatives aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-151">Configuration data guidelines:</span></span>

<span data-ttu-id="e61a2-152">_ Ne stockez jamais les mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte brut.</span><span class="sxs-lookup"><span data-stu-id="e61a2-152">_ Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="e61a2-153">L’outil [Gestionnaire de secret](xref:security/app-secrets) peut être utilisé pour stocker les secrets en développement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-153">The [Secret Manager](xref:security/app-secrets) tool can be used to store secrets in development.</span></span>
* <span data-ttu-id="e61a2-154">N’utilisez aucun secret de production dans les environnements de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="e61a2-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="e61a2-155">Spécifiez les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans un référentiel de code source.</span><span class="sxs-lookup"><span data-stu-id="e61a2-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="e61a2-156">Par [défaut](#default), la source de configuration des secrets de l’utilisateur est inscrite après les sources de configuration JSON.</span><span class="sxs-lookup"><span data-stu-id="e61a2-156">By [default](#default), the user secrets configuration source is registered after the JSON configuration sources.</span></span> <span data-ttu-id="e61a2-157">Par conséquent, les clés de secrets d’utilisateur sont prioritaires sur les clés dans *appsettings.json* et *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="e61a2-157">Therefore, user secrets keys take precedence over keys in *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="e61a2-158">Pour plus d’informations sur le stockage des mots de passe ou d’autres données sensibles :</span><span class="sxs-lookup"><span data-stu-id="e61a2-158">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="e61a2-159"><xref:security/app-secrets>: Fournit des conseils sur l’utilisation de variables d’environnement pour stocker des données sensibles.</span><span class="sxs-lookup"><span data-stu-id="e61a2-159"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="e61a2-160">L’outil Gestionnaire de secret utilise le [fournisseur de configuration de fichiers](#fcp) pour stocker les secrets d’utilisateur dans un fichier JSON sur le système local.</span><span class="sxs-lookup"><span data-stu-id="e61a2-160">The Secret Manager tool uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="e61a2-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stocke en toute sécurité des secrets d’application pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e61a2-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="e61a2-162">Pour plus d’informations, consultez <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-162">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="e61a2-163">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="e61a2-163">Environment variables</span></span>

<span data-ttu-id="e61a2-164">À l’aide de la configuration [par défaut](#default) , le <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> charge la configuration à partir des paires clé-valeur de variable d’environnement après la lecture *appsettings.json* , *appSettings.* `Environment` *. JSON* et les [secrets](xref:security/app-secrets)de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e61a2-164">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [user secrets](xref:security/app-secrets).</span></span> <span data-ttu-id="e61a2-165">Par conséquent, les valeurs de clés lues à partir de l’environnement remplacent les valeurs lues à partir de *appsettings.json* *appSettings.* `Environment` *. JSON* et les secrets de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e61a2-165">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and user secrets.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="e61a2-166">Les `set` commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="e61a2-166">The following `set` commands:</span></span>

* <span data-ttu-id="e61a2-167">Définissez les clés et les valeurs d’environnement de l' [exemple précédent](#appsettingsjson) sur Windows.</span><span class="sxs-lookup"><span data-stu-id="e61a2-167">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="e61a2-168">Testez les paramètres lors de l’utilisation de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="e61a2-168">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="e61a2-169">La `dotnet run` commande doit être exécutée dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="e61a2-169">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="e61a2-170">Paramètres d’environnement précédents :</span><span class="sxs-lookup"><span data-stu-id="e61a2-170">The preceding environment settings:</span></span>

* <span data-ttu-id="e61a2-171">Sont uniquement définies dans les processus lancés à partir de la fenêtre de commande dans laquelle ils ont été définis.</span><span class="sxs-lookup"><span data-stu-id="e61a2-171">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="e61a2-172">Ne seront pas lues par les navigateurs lancés avec Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e61a2-172">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="e61a2-173">Les commandes [setx](/windows-server/administration/windows-commands/setx) suivantes peuvent être utilisées pour définir les clés et les valeurs d’environnement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="e61a2-173">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="e61a2-174">Contrairement à `set` , `setx` les paramètres sont conservés.</span><span class="sxs-lookup"><span data-stu-id="e61a2-174">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="e61a2-175">`/M` définit la variable dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="e61a2-175">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="e61a2-176">Si le `/M` commutateur n’est pas utilisé, une variable d’environnement utilisateur est définie.</span><span class="sxs-lookup"><span data-stu-id="e61a2-176">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="e61a2-177">Pour vérifier que les commandes précédentes remplacent *appsettings.json* et *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e61a2-177">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="e61a2-178">Avec Visual Studio : quittez et redémarrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e61a2-178">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="e61a2-179">Avec l’interface CLI : démarrez une nouvelle fenêtre de commande et entrez `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="e61a2-179">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="e61a2-180">Appelez <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> avec une chaîne pour spécifier un préfixe pour les variables d’environnement :</span><span class="sxs-lookup"><span data-stu-id="e61a2-180">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="e61a2-181">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="e61a2-181">In the preceding code:</span></span>

* <span data-ttu-id="e61a2-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` est ajouté après les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="e61a2-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="e61a2-183">Pour obtenir un exemple de classement des fournisseurs de configuration, consultez [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="e61a2-183">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="e61a2-184">Les variables d’environnement définies avec le `MyCustomPrefix_` préfixe remplacent les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="e61a2-184">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="e61a2-185">Cela comprend les variables d’environnement sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="e61a2-185">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="e61a2-186">Le préfixe est supprimé lorsque les paires clé-valeur de configuration sont lues.</span><span class="sxs-lookup"><span data-stu-id="e61a2-186">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="e61a2-187">Les commandes suivantes testent le préfixe personnalisé :</span><span class="sxs-lookup"><span data-stu-id="e61a2-187">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="e61a2-188">La [configuration par défaut](#default) charge les variables d’environnement et les arguments de ligne de commande préfixé avec `DOTNET_` et `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="e61a2-188">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="e61a2-189">Les `DOTNET_` `ASPNETCORE_` préfixes et sont utilisés par ASP.net Core pour la configuration de l' [hôte et](xref:fundamentals/host/generic-host#host-configuration)de l’application, mais pas pour la configuration de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e61a2-189">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="e61a2-190">Pour plus d’informations sur la configuration de l’hôte et de l’application, consultez [hôte générique .net](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="e61a2-190">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="e61a2-191">Dans [Azure App service](https://azure.microsoft.com/services/app-service/), sélectionnez **nouveau paramètre d’application** dans la page **paramètres > configuration** .</span><span class="sxs-lookup"><span data-stu-id="e61a2-191">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="e61a2-192">Azure App Service paramètres de l’application sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="e61a2-192">Azure App Service application settings are:</span></span>

* <span data-ttu-id="e61a2-193">Chiffré au repos et transmis sur un canal chiffré.</span><span class="sxs-lookup"><span data-stu-id="e61a2-193">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="e61a2-194">Exposés en tant que variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-194">Exposed as environment variables.</span></span>

<span data-ttu-id="e61a2-195">Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="e61a2-195">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="e61a2-196">Consultez [préfixes de chaîne de connexion](#constr) pour plus d’informations sur les chaînes de connexion de base de données Azure.</span><span class="sxs-lookup"><span data-stu-id="e61a2-196">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="e61a2-197">Affectation des noms de variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="e61a2-197">Naming of environment variables</span></span>

<span data-ttu-id="e61a2-198">Les noms des variables d’environnement reflètent la structure d’un *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="e61a2-198">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="e61a2-199">Chaque élément de la hiérarchie est séparé par un trait de soulignement double (préférable) ou par deux-points.</span><span class="sxs-lookup"><span data-stu-id="e61a2-199">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="e61a2-200">Lorsque la structure de l’élément comprend un tableau, l’index du tableau doit être traité comme un nom d’élément supplémentaire dans ce chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="e61a2-200">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="e61a2-201">Considérez le *appsettings.json* fichier suivant et ses valeurs équivalentes représentées sous la forme de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-201">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

**appsettings.json**

```json
{
    "SmtpServer": "smtp.example.com",
    "Logging": [
        {
            "Name": "ToEmail",
            "Level": "Critical",
            "Args": {
                "FromAddress": "MySystem@example.com",
                "ToAddress": "SRE@example.com"
            }
        },
        {
            "Name": "ToConsole",
            "Level": "Information"
        }
    ]
}
```

<span data-ttu-id="e61a2-202">**variables d’environnement**</span><span class="sxs-lookup"><span data-stu-id="e61a2-202">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="e61a2-203">Variables d’environnement définies dans launchSettings.jssur</span><span class="sxs-lookup"><span data-stu-id="e61a2-203">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="e61a2-204">Les variables d’environnement définies dans *launchSettings.jslors de* la substitution de celles définies dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="e61a2-204">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="e61a2-205">Ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e61a2-205">Command-line</span></span>

<span data-ttu-id="e61a2-206">À l’aide de la configuration [par défaut](#default) , le <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> charge la configuration à partir de paires clé-valeur d’argument de ligne de commande après les sources de configuration suivantes :</span><span class="sxs-lookup"><span data-stu-id="e61a2-206">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="e61a2-207">*appsettings.json* et *appSettings*. `Environment` . fichiers *JSON* .</span><span class="sxs-lookup"><span data-stu-id="e61a2-207">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="e61a2-208">[Secrets](xref:security/app-secrets) de l’application dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-208">[App secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="e61a2-209">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-209">Environment variables.</span></span>

<span data-ttu-id="e61a2-210">Par [défaut](#default), les valeurs de configuration définies sur la ligne de commande remplacent les valeurs de configuration définies avec tous les autres fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-210">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="e61a2-211">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e61a2-211">Command-line arguments</span></span>

<span data-ttu-id="e61a2-212">La commande suivante définit des clés et des valeurs à l’aide de `=` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-212">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="e61a2-213">La commande suivante définit des clés et des valeurs à l’aide de `/` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-213">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="e61a2-214">La commande suivante définit des clés et des valeurs à l’aide de `--` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-214">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="e61a2-215">Valeur de la clé :</span><span class="sxs-lookup"><span data-stu-id="e61a2-215">The key value:</span></span>

* <span data-ttu-id="e61a2-216">Doit suivre `=` ou la clé doit avoir un préfixe `--` ou `/` lorsque la valeur suit un espace.</span><span class="sxs-lookup"><span data-stu-id="e61a2-216">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="e61a2-217">N’est pas obligatoire si `=` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="e61a2-217">Isn't required if `=` is used.</span></span> <span data-ttu-id="e61a2-218">Par exemple, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-218">For example, `MySetting=`.</span></span>

<span data-ttu-id="e61a2-219">Dans la même commande, ne mélangez pas les paires clé-valeur d’argument de ligne de commande qui utilisent `=` des paires clé-valeur utilisant un espace.</span><span class="sxs-lookup"><span data-stu-id="e61a2-219">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="e61a2-220">Correspondances de commutateur</span><span class="sxs-lookup"><span data-stu-id="e61a2-220">Switch mappings</span></span>

<span data-ttu-id="e61a2-221">Les mappages de commutateur autorisent la logique de remplacement de nom de **clé** .</span><span class="sxs-lookup"><span data-stu-id="e61a2-221">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="e61a2-222">Fournissez un dictionnaire de remplacements de commutateur dans la <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> méthode.</span><span class="sxs-lookup"><span data-stu-id="e61a2-222">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="e61a2-223">Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="e61a2-223">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="e61a2-224">Si la clé de ligne de commande est trouvée dans le dictionnaire, la valeur du dictionnaire est retournée pour définir la paire clé-valeur dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-224">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="e61a2-225">Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).</span><span class="sxs-lookup"><span data-stu-id="e61a2-225">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="e61a2-226">Règles des clés du dictionnaire de correspondances de commutateur :</span><span class="sxs-lookup"><span data-stu-id="e61a2-226">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="e61a2-227">Les commutateurs doivent commencer par `-` ou `--` .</span><span class="sxs-lookup"><span data-stu-id="e61a2-227">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="e61a2-228">Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.</span><span class="sxs-lookup"><span data-stu-id="e61a2-228">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="e61a2-229">Pour utiliser un dictionnaire de mappages de commutateur, transmettez-le à l’appel à `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-229">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="e61a2-230">Le code suivant montre les valeurs de clé pour les clés remplacées :</span><span class="sxs-lookup"><span data-stu-id="e61a2-230">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="e61a2-231">La commande suivante fonctionne pour tester le remplacement de la clé :</span><span class="sxs-lookup"><span data-stu-id="e61a2-231">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="e61a2-232">Pour les applications qui utilisent des mappages de commutateurs, l’appel à `CreateDefaultBuilder` ne doit pas passer d’arguments.</span><span class="sxs-lookup"><span data-stu-id="e61a2-232">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="e61a2-233">L' `CreateDefaultBuilder` appel de la méthode `AddCommandLine` n’inclut pas de commutateurs mappés, et il n’existe aucun moyen de passer le dictionnaire de mappage de commutateur à `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e61a2-233">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e61a2-234">La solution ne consiste pas à passer les arguments à `CreateDefaultBuilder` , mais à autoriser la méthode de la `ConfigurationBuilder` méthode `AddCommandLine` à traiter à la fois les arguments et le dictionnaire de mappage de commutateur.</span><span class="sxs-lookup"><span data-stu-id="e61a2-234">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="e61a2-235">Données de configuration hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="e61a2-235">Hierarchical configuration data</span></span>

<span data-ttu-id="e61a2-236">L’API de configuration lit les données de configuration hiérarchiques en aplatit les données hiérarchiques à l’aide d’un délimiteur dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-236">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="e61a2-237">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le  *appsettings.json* fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="e61a2-237">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="e61a2-238">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-238">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="e61a2-239">La meilleure façon de lire des données de configuration hiérarchiques consiste à utiliser le modèle d’options.</span><span class="sxs-lookup"><span data-stu-id="e61a2-239">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="e61a2-240">Pour plus d’informations, consultez [lier des données de configuration hiérarchiques](#optpat) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="e61a2-240">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="e61a2-241">Les méthodes <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> et <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sont disponibles pour isoler les sections et les enfants d’une section dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-241"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="e61a2-242">Ces méthodes sont décrites plus loin dans [GetSection, GetChildren et Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="e61a2-242">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="e61a2-243">Clés et valeurs de configuration</span><span class="sxs-lookup"><span data-stu-id="e61a2-243">Configuration keys and values</span></span>

<span data-ttu-id="e61a2-244">Clés de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-244">Configuration keys:</span></span>

* <span data-ttu-id="e61a2-245">Ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="e61a2-245">Are case-insensitive.</span></span> <span data-ttu-id="e61a2-246">Par exemple, `ConnectionString` et `connectionstring` sont traités en tant que clés équivalentes.</span><span class="sxs-lookup"><span data-stu-id="e61a2-246">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="e61a2-247">Si une clé et une valeur sont définies dans plusieurs fournisseurs de configuration, la valeur du dernier fournisseur ajouté est utilisée.</span><span class="sxs-lookup"><span data-stu-id="e61a2-247">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="e61a2-248">Pour plus d’informations, consultez [configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="e61a2-248">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="e61a2-249">Clés hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="e61a2-249">Hierarchical keys</span></span>
  * <span data-ttu-id="e61a2-250">Dans l’API Configuration, un séparateur sous forme de signe deux-points (`:`) fonctionne sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="e61a2-250">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="e61a2-251">Dans les variables d’environnement, un séparateur sous forme de signe deux-points peut ne pas fonctionner sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="e61a2-251">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="e61a2-252">Un trait de soulignement double, `__` , est pris en charge par toutes les plateformes et est automatiquement converti en deux-points `:` .</span><span class="sxs-lookup"><span data-stu-id="e61a2-252">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="e61a2-253">Dans Azure Key Vault, les clés hiérarchiques utilisent `--` comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="e61a2-253">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="e61a2-254">Le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) remplace automatiquement `--` par un `:` lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-254">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="e61a2-255"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-255">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="e61a2-256">La liaison de tableau est décrite dans la section [Lier un tableau à une classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="e61a2-256">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="e61a2-257">Valeurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-257">Configuration values:</span></span>

* <span data-ttu-id="e61a2-258">Sont des chaînes.</span><span class="sxs-lookup"><span data-stu-id="e61a2-258">Are strings.</span></span>
* <span data-ttu-id="e61a2-259">Les valeurs NULL ne peuvent pas être stockées dans la configuration ou liées à des objets.</span><span class="sxs-lookup"><span data-stu-id="e61a2-259">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="e61a2-260">Fournisseurs de configuration</span><span class="sxs-lookup"><span data-stu-id="e61a2-260">Configuration providers</span></span>

<span data-ttu-id="e61a2-261">Le tableau suivant présente les fournisseurs de configuration disponibles pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e61a2-261">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="e61a2-262">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="e61a2-262">Provider</span></span> | <span data-ttu-id="e61a2-263">Fournit la configuration à partir de</span><span class="sxs-lookup"><span data-stu-id="e61a2-263">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="e61a2-264">Fournisseur de configuration Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e61a2-264">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="e61a2-265">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e61a2-265">Azure Key Vault</span></span> |
| [<span data-ttu-id="e61a2-266">Fournisseur de configuration Azure App</span><span class="sxs-lookup"><span data-stu-id="e61a2-266">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="e61a2-267">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="e61a2-267">Azure App Configuration</span></span> |
| [<span data-ttu-id="e61a2-268">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e61a2-268">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="e61a2-269">Paramètres de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e61a2-269">Command-line parameters</span></span> |
| [<span data-ttu-id="e61a2-270">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="e61a2-270">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="e61a2-271">Source personnalisée</span><span class="sxs-lookup"><span data-stu-id="e61a2-271">Custom source</span></span> |
| [<span data-ttu-id="e61a2-272">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="e61a2-272">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="e61a2-273">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="e61a2-273">Environment variables</span></span> |
| [<span data-ttu-id="e61a2-274">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="e61a2-274">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="e61a2-275">Fichiers INI, JSON et XML</span><span class="sxs-lookup"><span data-stu-id="e61a2-275">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="e61a2-276">Fournisseur de configuration de clé par fichier</span><span class="sxs-lookup"><span data-stu-id="e61a2-276">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="e61a2-277">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="e61a2-277">Directory files</span></span> |
| [<span data-ttu-id="e61a2-278">Fournisseur de configuration de la mémoire</span><span class="sxs-lookup"><span data-stu-id="e61a2-278">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="e61a2-279">Collections en mémoire</span><span class="sxs-lookup"><span data-stu-id="e61a2-279">In-memory collections</span></span> |
| [<span data-ttu-id="e61a2-280">Secrets de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="e61a2-280">User secrets</span></span>](xref:security/app-secrets) | <span data-ttu-id="e61a2-281">Fichier dans le répertoire de profil utilisateur</span><span class="sxs-lookup"><span data-stu-id="e61a2-281">File in the user profile directory</span></span> |

<span data-ttu-id="e61a2-282">Les sources de configuration sont lues dans l’ordre dans lequel leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="e61a2-282">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="e61a2-283">Commandez des fournisseurs de configuration dans le code pour répondre aux priorités des sources de configuration sous-jacentes requises par l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-283">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="e61a2-284">Une séquence type des fournisseurs de configuration est la suivante :</span><span class="sxs-lookup"><span data-stu-id="e61a2-284">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="e61a2-285">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="e61a2-285">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="e61a2-286">Secrets de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="e61a2-286">User secrets</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="e61a2-287">Variables d’environnement à l’aide du [fournisseur de configuration des variables d’environnement](#evcp).</span><span class="sxs-lookup"><span data-stu-id="e61a2-287">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="e61a2-288">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e61a2-288">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="e61a2-289">Une pratique courante consiste à ajouter le dernier fournisseur de configuration de ligne de commande dans une série de fournisseurs pour permettre aux arguments de ligne de commande de remplacer la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="e61a2-289">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="e61a2-290">La séquence de fournisseurs précédente est utilisée dans la [configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="e61a2-290">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="e61a2-291">Préfixes des chaînes de connexion</span><span class="sxs-lookup"><span data-stu-id="e61a2-291">Connection string prefixes</span></span>

<span data-ttu-id="e61a2-292">L’API de configuration a des règles de traitement spéciales pour quatre variables d’environnement de chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="e61a2-292">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="e61a2-293">Ces chaînes de connexion sont impliquées dans la configuration des chaînes de connexion Azure pour l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-293">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="e61a2-294">Les variables d’environnement avec les préfixes indiqués dans le tableau sont chargées dans l’application avec la [configuration par défaut](#default) ou lorsqu’aucun préfixe n’est fourni à `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="e61a2-294">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="e61a2-295">Préfixe de la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="e61a2-295">Connection string prefix</span></span> | <span data-ttu-id="e61a2-296">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="e61a2-296">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="e61a2-297">Fournisseur personnalisé</span><span class="sxs-lookup"><span data-stu-id="e61a2-297">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="e61a2-298">MySQL</span><span class="sxs-lookup"><span data-stu-id="e61a2-298">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="e61a2-299">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="e61a2-299">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="e61a2-300">SQL Server</span><span class="sxs-lookup"><span data-stu-id="e61a2-300">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="e61a2-301">Quand une variable d’environnement est découverte et chargée dans la configuration avec l’un des quatre préfixes indiqués dans le tableau :</span><span class="sxs-lookup"><span data-stu-id="e61a2-301">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="e61a2-302">La clé de configuration est créée en supprimant le préfixe de la variable d’environnement et en ajoutant une section de clé de configuration (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="e61a2-302">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="e61a2-303">Une nouvelle paire clé-valeur de configuration est créée qui représente le fournisseur de connexion de base de données (à l’exception de `CUSTOMCONNSTR_`, qui ne possède aucun fournisseur indiqué).</span><span class="sxs-lookup"><span data-stu-id="e61a2-303">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="e61a2-304">Clé de variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="e61a2-304">Environment variable key</span></span> | <span data-ttu-id="e61a2-305">Clé de configuration convertie</span><span class="sxs-lookup"><span data-stu-id="e61a2-305">Converted configuration key</span></span> | <span data-ttu-id="e61a2-306">Entrée de configuration de fournisseur</span><span class="sxs-lookup"><span data-stu-id="e61a2-306">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e61a2-307">Entrée de configuration non créée.</span><span class="sxs-lookup"><span data-stu-id="e61a2-307">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e61a2-308">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-308">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e61a2-309">Valeur: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="e61a2-309">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e61a2-310">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-310">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e61a2-311">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="e61a2-311">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e61a2-312">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-312">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e61a2-313">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="e61a2-313">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="e61a2-314">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="e61a2-314">File configuration provider</span></span>

<span data-ttu-id="e61a2-315"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> est la classe de base pour charger la configuration à partir du système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="e61a2-315"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="e61a2-316">Les fournisseurs de configuration suivants dérivent de `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-316">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="e61a2-317">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="e61a2-317">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="e61a2-318">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="e61a2-318">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="e61a2-319">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="e61a2-319">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="e61a2-320">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="e61a2-320">INI configuration provider</span></span>

<span data-ttu-id="e61a2-321"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier INI lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="e61a2-321">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="e61a2-322">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-322">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="e61a2-323">Dans le code précédent, les paramètres des *MyIniConfig.ini* et  *MyIniConfig*. `Environment` les fichiers *ini* sont remplacés par les paramètres dans le :</span><span class="sxs-lookup"><span data-stu-id="e61a2-323">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="e61a2-324">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="e61a2-324">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="e61a2-325">[Fournisseur de configuration de ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="e61a2-325">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="e61a2-326">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyIniConfig.ini* suivant :</span><span class="sxs-lookup"><span data-stu-id="e61a2-326">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="e61a2-327">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="e61a2-327">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="e61a2-328">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="e61a2-328">JSON configuration provider</span></span>

<span data-ttu-id="e61a2-329">Le <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge la configuration à partir de paires clé-valeur de fichier JSON.</span><span class="sxs-lookup"><span data-stu-id="e61a2-329">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="e61a2-330">Les surcharges peuvent spécifier :</span><span class="sxs-lookup"><span data-stu-id="e61a2-330">Overloads can specify:</span></span>

* <span data-ttu-id="e61a2-331">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="e61a2-331">Whether the file is optional.</span></span>
* <span data-ttu-id="e61a2-332">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="e61a2-332">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="e61a2-333">Considérez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e61a2-333">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="e61a2-334">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="e61a2-334">The preceding code:</span></span>

* <span data-ttu-id="e61a2-335">Configure le fournisseur de configuration JSON pour charger le *MyConfig.jssur* le fichier avec les options suivantes :</span><span class="sxs-lookup"><span data-stu-id="e61a2-335">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="e61a2-336">`optional: true`: Le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="e61a2-336">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="e61a2-337">`reloadOnChange: true` : Le fichier est rechargé lorsque des modifications sont enregistrées.</span><span class="sxs-lookup"><span data-stu-id="e61a2-337">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="e61a2-338">Lit les [fournisseurs de configuration par défaut](#default) avant l' *MyConfig.jssur* le fichier.</span><span class="sxs-lookup"><span data-stu-id="e61a2-338">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="e61a2-339">Les paramètres dans le *MyConfig.js* paramètre de remplacement de fichier dans les fournisseurs de configuration par défaut, y compris le [fournisseur de configuration des variables d’environnement](#evcp) et le fournisseur de configuration de ligne de [commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="e61a2-339">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="e61a2-340">En règle générale, vous **ne souhaitez pas** que les valeurs de substitution de fichier JSON personnalisées soient définies dans le fournisseur de configuration des [variables d’environnement](#evcp) et dans le fournisseur de configuration de [ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="e61a2-340">You typically \***don't** _ want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="e61a2-341">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-341">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="e61a2-342">Dans le code précédent, les paramètres de la _MyConfig.jssur \* et  *MyConfig*. `Environment` . fichiers *JSON* :</span><span class="sxs-lookup"><span data-stu-id="e61a2-342">In the preceding code, settings in the _MyConfig.json\* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="e61a2-343">Substituez les paramètres dans *appsettings.json* et *appSettings*. `Environment` fichiers *JSON* .</span><span class="sxs-lookup"><span data-stu-id="e61a2-343">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="e61a2-344">Sont remplacées par les paramètres dans le [fournisseur de configuration des variables d’environnement](#evcp) et le fournisseur de configuration de ligne de [commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="e61a2-344">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="e61a2-345">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient les  *MyConfig.jssuivantes sur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="e61a2-345">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="e61a2-346">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="e61a2-346">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="e61a2-347">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="e61a2-347">XML configuration provider</span></span>

<span data-ttu-id="e61a2-348"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier XML lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="e61a2-348">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="e61a2-349">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="e61a2-350">Dans le code précédent, les paramètres des *MyXMLFile.xml* et  *MyXMLFile*. `Environment` les fichiers *XML* sont remplacés par les paramètres dans le :</span><span class="sxs-lookup"><span data-stu-id="e61a2-350">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="e61a2-351">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="e61a2-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="e61a2-352">[Fournisseur de configuration de ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="e61a2-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="e61a2-353">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyXMLFile.xml* suivant :</span><span class="sxs-lookup"><span data-stu-id="e61a2-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="e61a2-354">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="e61a2-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="e61a2-355">Les éléments répétitifs qui utilisent le même nom d’élément fonctionnent si l’attribut `name` est utilisé pour distinguer les éléments :</span><span class="sxs-lookup"><span data-stu-id="e61a2-355">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="e61a2-356">Le code suivant lit le fichier de configuration précédent et affiche les clés et les valeurs :</span><span class="sxs-lookup"><span data-stu-id="e61a2-356">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="e61a2-357">Les attributs peuvent être utilisés pour fournir des valeurs :</span><span class="sxs-lookup"><span data-stu-id="e61a2-357">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="e61a2-358">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-358">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="e61a2-359">key:attribute</span><span class="sxs-lookup"><span data-stu-id="e61a2-359">key:attribute</span></span>
* <span data-ttu-id="e61a2-360">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="e61a2-360">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="e61a2-361">Fournisseur de configuration de clé par fichier</span><span class="sxs-lookup"><span data-stu-id="e61a2-361">Key-per-file configuration provider</span></span>

<span data-ttu-id="e61a2-362">Le <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> utilise les fichiers d’un répertoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-362">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="e61a2-363">La clé est le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="e61a2-363">The key is the file name.</span></span> <span data-ttu-id="e61a2-364">La valeur contient le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="e61a2-364">The value contains the file's contents.</span></span> <span data-ttu-id="e61a2-365">Le fournisseur de configuration par fichier clé est utilisé dans les scénarios d’hébergement de l’ancrage.</span><span class="sxs-lookup"><span data-stu-id="e61a2-365">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="e61a2-366">Pour activer la configuration clé par fichier, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-366">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="e61a2-367">Le `directoryPath` aux fichiers doit être un chemin d’accès absolu.</span><span class="sxs-lookup"><span data-stu-id="e61a2-367">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="e61a2-368">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="e61a2-368">Overloads permit specifying:</span></span>

* <span data-ttu-id="e61a2-369">Un délégué `Action<KeyPerFileConfigurationSource>` qui configure la source.</span><span class="sxs-lookup"><span data-stu-id="e61a2-369">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="e61a2-370">Si le répertoire est facultatif et le chemin d’accès au répertoire.</span><span class="sxs-lookup"><span data-stu-id="e61a2-370">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="e61a2-371">Le double trait de soulignement (`__`) est utilisé comme un délimiteur de clé de configuration dans les noms de fichiers.</span><span class="sxs-lookup"><span data-stu-id="e61a2-371">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="e61a2-372">Par exemple, le nom de fichier `Logging__LogLevel__System` génère la clé de configuration `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-372">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="e61a2-373">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="e61a2-373">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="e61a2-374">Fournisseur de configuration de la mémoire</span><span class="sxs-lookup"><span data-stu-id="e61a2-374">Memory configuration provider</span></span>

<span data-ttu-id="e61a2-375">Le <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> utilise une collection en mémoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-375">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="e61a2-376">Le code suivant ajoute une collection de mémoire au système de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-376">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="e61a2-377">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche les paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="e61a2-377">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="e61a2-378">Dans le code précédent, `config.AddInMemoryCollection(Dict)` est ajouté après les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="e61a2-378">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="e61a2-379">Pour obtenir un exemple de classement des fournisseurs de configuration, consultez [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="e61a2-379">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="e61a2-380">Pour un autre exemple, consultez [lier un tableau](#boa) à l’aide de `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="e61a2-380">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="e61a2-381">GetValue</span><span class="sxs-lookup"><span data-stu-id="e61a2-381">GetValue</span></span>

<span data-ttu-id="e61a2-382">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrait une valeur unique de la configuration avec une clé spécifiée et la convertit en type spécifié :</span><span class="sxs-lookup"><span data-stu-id="e61a2-382">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="e61a2-383">Dans le code précédent, si est `NumberKey` introuvable dans la configuration, la valeur par défaut de `99` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="e61a2-383">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="e61a2-384">GetSection, GetChildren et Exists</span><span class="sxs-lookup"><span data-stu-id="e61a2-384">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="e61a2-385">Pour les exemples qui suivent, prenez en compte les *MySubsection.jssuivantes sur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="e61a2-385">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="e61a2-386">Le code suivant ajoute *MySubsection.js* aux fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-386">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="e61a2-387">GetSection</span><span class="sxs-lookup"><span data-stu-id="e61a2-387">GetSection</span></span>

<span data-ttu-id="e61a2-388">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) retourne une sous-section de configuration avec la clé de sous-section spécifiée.</span><span class="sxs-lookup"><span data-stu-id="e61a2-388">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="e61a2-389">Le code suivant retourne des valeurs pour `section1` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-389">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="e61a2-390">Le code suivant retourne des valeurs pour `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-390">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="e61a2-391">`GetSection` ne retourne jamais `null`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-391">`GetSection` never returns `null`.</span></span> <span data-ttu-id="e61a2-392">Si aucune section correspondante n’est trouvée, une valeur `IConfigurationSection` vide est retournée.</span><span class="sxs-lookup"><span data-stu-id="e61a2-392">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="e61a2-393">Quand `GetSection` retourne une section correspondante, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="e61a2-393">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="e61a2-394"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> et <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> sont retournés quand la section existe.</span><span class="sxs-lookup"><span data-stu-id="e61a2-394">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="e61a2-395">GetChildren et EXISTS</span><span class="sxs-lookup"><span data-stu-id="e61a2-395">GetChildren and Exists</span></span>

<span data-ttu-id="e61a2-396">Le code suivant appelle [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) et retourne des valeurs pour `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-396">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="e61a2-397">Le code précédent appelle [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pour vérifier l’existence de la section :</span><span class="sxs-lookup"><span data-stu-id="e61a2-397">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="e61a2-398">Lier un tableau</span><span class="sxs-lookup"><span data-stu-id="e61a2-398">Bind an array</span></span>

<span data-ttu-id="e61a2-399">[ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) prend en charge les tableaux de liaison aux objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-399">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="e61a2-400">Tout format de tableau qui expose un segment de clé numérique est capable d’effectuer une liaison de tableau à un tableau de classes [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="e61a2-400">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="e61a2-401">Prenez *MyArray.js* à partir de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="e61a2-401">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="e61a2-402">Le code suivant ajoute *MyArray.js* aux fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-402">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="e61a2-403">Le code suivant lit la configuration et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="e61a2-403">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="e61a2-404">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="e61a2-404">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="e61a2-405">Dans la sortie précédente, l’index 3 a la valeur `value40` , ce qui correspond à `"4": "value40",` dans *MyArray.jssur*.</span><span class="sxs-lookup"><span data-stu-id="e61a2-405">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="e61a2-406">Les index de tableau liés sont continus et non liés à l’index de clé de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-406">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="e61a2-407">Le Binder de configuration n’est pas en capacité à lier des valeurs null ou à créer des entrées NULL dans des objets liés</span><span class="sxs-lookup"><span data-stu-id="e61a2-407">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="e61a2-408">Le code suivant charge la `array:entries` configuration avec la <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="e61a2-408">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="e61a2-409">Le code suivant lit la configuration dans `arrayDict` `Dictionary` et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="e61a2-409">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="e61a2-410">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="e61a2-410">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="e61a2-411">L’index &num;3 dans l’objet lié contient les données de configuration pour la clé de configuration `array:4` et sa valeur de `value4`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-411">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="e61a2-412">Lorsque les données de configuration contenant un tableau sont liées, les index de tableau dans les clés de configuration sont utilisés pour itérer les données de configuration lors de la création de l’objet.</span><span class="sxs-lookup"><span data-stu-id="e61a2-412">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="e61a2-413">Une valeur null ne peut pas être conservée dans des données de configuration, et une entrée à valeur null n’est pas créée dans un objet lié quand un tableau dans des clés de configuration ignore un ou plusieurs index.</span><span class="sxs-lookup"><span data-stu-id="e61a2-413">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="e61a2-414">L’élément de configuration manquant pour l’index &num; 3 peut être fourni avant la liaison à l' `ArrayExample` instance par n’importe quel fournisseur de configuration qui lit la &num; paire clé/valeur de l’index 3.</span><span class="sxs-lookup"><span data-stu-id="e61a2-414">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="e61a2-415">Examinez le *Value3.jssuivant sur* le fichier à partir de l’exemple de téléchargement :</span><span class="sxs-lookup"><span data-stu-id="e61a2-415">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="e61a2-416">Le code suivant comprend la configuration de *Value3.jssur* et `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-416">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="e61a2-417">Le code suivant lit la configuration précédente et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="e61a2-417">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="e61a2-418">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="e61a2-418">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="e61a2-419">Les fournisseurs de configuration personnalisés ne sont pas obligés d’implémenter la liaison de tableau.</span><span class="sxs-lookup"><span data-stu-id="e61a2-419">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="e61a2-420">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="e61a2-420">Custom configuration provider</span></span>

<span data-ttu-id="e61a2-421">L’exemple d’application montre comment créer un fournisseur de configuration de base qui lit les paires clé-valeur de configuration à partir d’une base de données à l’aide [d’Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="e61a2-421">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="e61a2-422">Le fournisseur présente les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="e61a2-422">The provider has the following characteristics:</span></span>

* <span data-ttu-id="e61a2-423">La base de données en mémoire EF est utilisée à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-423">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="e61a2-424">Pour utiliser une base de données qui nécessite une chaîne de connexion, implémentez un autre `ConfigurationBuilder` pour fournir la chaîne de connexion à partir d’un autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-424">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="e61a2-425">Le fournisseur lit une table de base de données dans la configuration au démarrage.</span><span class="sxs-lookup"><span data-stu-id="e61a2-425">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="e61a2-426">Le fournisseur n’interroge pas la base de données par clé.</span><span class="sxs-lookup"><span data-stu-id="e61a2-426">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="e61a2-427">Le rechargement en cas de changement n’est pas implémenté, par conséquent, la mise à jour la base de données après le démarrage de l’application n’a aucun effet sur la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-427">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="e61a2-428">Définissez une entité `EFConfigurationValue` pour le stockage des valeurs de configuration dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="e61a2-428">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="e61a2-429">*Models/EFConfigurationValue.cs* :</span><span class="sxs-lookup"><span data-stu-id="e61a2-429">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="e61a2-430">Ajoutez un `EFConfigurationContext` pour stocker les valeurs configurées et y accéder.</span><span class="sxs-lookup"><span data-stu-id="e61a2-430">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="e61a2-431">*EFConfigurationProvider/EFConfigurationContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="e61a2-431">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="e61a2-432">Créez une classe qui implémente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-432">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="e61a2-433">*EFConfigurationProvider/EFConfigurationSource.cs* :</span><span class="sxs-lookup"><span data-stu-id="e61a2-433">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="e61a2-434">Créez le fournisseur de configuration personnalisé en héritant de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-434">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="e61a2-435">Le fournisseur de configuration initialise la base de données quand elle est vide.</span><span class="sxs-lookup"><span data-stu-id="e61a2-435">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="e61a2-436">Étant donné que les [clés de configuration ne](#keys)respectent pas la casse, le dictionnaire utilisé pour initialiser la base de données est créé avec le comparateur ne respectant pas la casse ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="e61a2-436">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="e61a2-437">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span><span class="sxs-lookup"><span data-stu-id="e61a2-437">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="e61a2-438">Une méthode d’extension `AddEFConfiguration` permet d’ajouter la source de configuration à un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-438">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="e61a2-439">*Extensions/EntityFrameworkExtensions.cs* :</span><span class="sxs-lookup"><span data-stu-id="e61a2-439">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="e61a2-440">Le code suivant montre comment utiliser le `EFConfigurationProvider` personnalisé dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="e61a2-440">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="e61a2-441">Configuration de l’accès au démarrage</span><span class="sxs-lookup"><span data-stu-id="e61a2-441">Access configuration in Startup</span></span>

<span data-ttu-id="e61a2-442">Le code suivant affiche les données de configuration dans les `Startup` méthodes :</span><span class="sxs-lookup"><span data-stu-id="e61a2-442">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="e61a2-443">Pour obtenir un exemple d’accès à la configuration à l’aide des méthodes pratiques de démarrage, consultez [Démarrage de l’application : méthodes pratiques](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="e61a2-443">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="e61a2-444">Configuration de l’accès dans les Razor pages</span><span class="sxs-lookup"><span data-stu-id="e61a2-444">Access configuration in Razor Pages</span></span>

<span data-ttu-id="e61a2-445">Le code suivant affiche les données de configuration dans une Razor page :</span><span class="sxs-lookup"><span data-stu-id="e61a2-445">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="e61a2-446">Dans le code suivant, `MyOptions` est ajouté au conteneur de services avec <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> et lié à la configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-446">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="e61a2-447">La balise suivante utilise la [`@inject`](xref:mvc/views/razor#inject) Razor directive pour résoudre et afficher les valeurs des options :</span><span class="sxs-lookup"><span data-stu-id="e61a2-447">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="e61a2-448">Configuration de l’accès dans un fichier de vue MVC</span><span class="sxs-lookup"><span data-stu-id="e61a2-448">Access configuration in a MVC view file</span></span>

<span data-ttu-id="e61a2-449">Le code suivant affiche les données de configuration dans une vue MVC :</span><span class="sxs-lookup"><span data-stu-id="e61a2-449">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="e61a2-450">Configurer des options avec un délégué</span><span class="sxs-lookup"><span data-stu-id="e61a2-450">Configure options with a delegate</span></span>

<span data-ttu-id="e61a2-451">Les options configurées dans un délégué remplacent les valeurs définies dans les fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-451">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="e61a2-452">La configuration d’options avec un délégué est illustrée dans l’exemple 2 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-452">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="e61a2-453">Dans le code suivant, un <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="e61a2-453">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="e61a2-454">Il utilise un délégué pour configurer des valeurs pour `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-454">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="e61a2-455">Le code suivant affiche les valeurs des options :</span><span class="sxs-lookup"><span data-stu-id="e61a2-455">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="e61a2-456">Dans l’exemple précédent, les valeurs de `Option1` et `Option2` sont spécifiées dans, *appsettings.json* puis remplacées par le délégué configuré.</span><span class="sxs-lookup"><span data-stu-id="e61a2-456">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="e61a2-457">Configuration de l’hôte ou configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="e61a2-457">Host versus app configuration</span></span>

<span data-ttu-id="e61a2-458">Avant que l’application ne soit configurée et démarrée, un *hôte* est configuré et lancé.</span><span class="sxs-lookup"><span data-stu-id="e61a2-458">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="e61a2-459">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="e61a2-459">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="e61a2-460">L’application et l’hôte sont configurés à l’aide des fournisseurs de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="e61a2-460">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="e61a2-461">Les paires clé-valeur de la configuration de l’hôte sont également incluses dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-461">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="e61a2-462">Pour plus d’informations sur l’utilisation des fournisseurs de configuration lors de la génération de l’hôte et l’impact des sources de configuration sur la configuration de l’hôte, consultez <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-462">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="e61a2-463">Configuration de l’hôte par défaut</span><span class="sxs-lookup"><span data-stu-id="e61a2-463">Default host configuration</span></span>

<span data-ttu-id="e61a2-464">Pour plus de détails sur la configuration par défaut lors de l’utilisation de l’[hôte Web](xref:fundamentals/host/web-host), consultez la [version ASP.NET Core 2.2. de cette rubrique](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="e61a2-464">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="e61a2-465">La configuration de l’hôte est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="e61a2-465">Host configuration is provided from:</span></span>
  * <span data-ttu-id="e61a2-466">Les variables d’environnement précédées du préfixe `DOTNET_` (par exemple, `DOTNET_ENVIRONMENT` ) à l’aide du [fournisseur de configuration des variables d’environnement](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="e61a2-466">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="e61a2-467">Le préfixe (`DOTNET_`) est supprimé lorsque les paires clé-valeur de la configuration sont chargées.</span><span class="sxs-lookup"><span data-stu-id="e61a2-467">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="e61a2-468">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e61a2-468">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="e61a2-469">La configuration par défaut de l’hôte Web est établie (`ConfigureWebHostDefaults`) :</span><span class="sxs-lookup"><span data-stu-id="e61a2-469">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="e61a2-470">Kestrel est utilisé comme serveur web et configuré à l’aide des fournisseurs de configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-470">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="e61a2-471">Ajoutez l’intergiciel de filtrage d’hôtes.</span><span class="sxs-lookup"><span data-stu-id="e61a2-471">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="e61a2-472">Ajoutez l’intergiciel d’en-têtes transférés si la variable d'environnement `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-472">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="e61a2-473">Activez l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="e61a2-473">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="e61a2-474">Autre configuration</span><span class="sxs-lookup"><span data-stu-id="e61a2-474">Other configuration</span></span>

<span data-ttu-id="e61a2-475">Cette rubrique se rapporte uniquement à la configuration de l' *application*.</span><span class="sxs-lookup"><span data-stu-id="e61a2-475">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="e61a2-476">D’autres aspects de l’exécution et de l’hébergement des applications ASP.NET Core sont configurés à l’aide des fichiers de configuration non traités dans cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="e61a2-476">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="e61a2-477">*launch.js* / *launchSettings.jssur* sont des fichiers de configuration d’outils pour l’environnement de développement, décrits ci-après :</span><span class="sxs-lookup"><span data-stu-id="e61a2-477">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="e61a2-478">Dans <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="e61a2-478">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="e61a2-479">Dans l’ensemble de la documentation dans lequel les fichiers sont utilisés pour configurer des applications ASP.NET Core pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-479">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="e61a2-480">*web.config* est un fichier de configuration de serveur, décrit dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="e61a2-480">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="e61a2-481">Les variables d’environnement définies dans *launchSettings.jslors de* la substitution de celles définies dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="e61a2-481">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="e61a2-482">Pour plus d’informations sur la migration de la configuration d’application à partir de versions antérieures de ASP.NET, consultez <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="e61a2-482">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="e61a2-483">Ajouter la configuration à partir d’un assembly externe</span><span class="sxs-lookup"><span data-stu-id="e61a2-483">Add configuration from an external assembly</span></span>

<span data-ttu-id="e61a2-484">Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-484">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="e61a2-485">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-485">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e61a2-486">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e61a2-486">Additional resources</span></span>

* [<span data-ttu-id="e61a2-487">Code source de configuration</span><span class="sxs-lookup"><span data-stu-id="e61a2-487">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e61a2-488">La configuration d’application dans ASP.NET Core est basée sur des paires clé-valeur établies par les *fournisseurs de configuration*.</span><span class="sxs-lookup"><span data-stu-id="e61a2-488">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="e61a2-489">Les fournisseurs de configuration lisent les données de configuration dans les paires clé-valeur à partir de diverses sources de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-489">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="e61a2-490">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e61a2-490">Azure Key Vault</span></span>
* <span data-ttu-id="e61a2-491">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="e61a2-491">Azure App Configuration</span></span>
* <span data-ttu-id="e61a2-492">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e61a2-492">Command-line arguments</span></span>
* <span data-ttu-id="e61a2-493">Fournisseurs personnalisés (installés ou créés)</span><span class="sxs-lookup"><span data-stu-id="e61a2-493">Custom providers (installed or created)</span></span>
* <span data-ttu-id="e61a2-494">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="e61a2-494">Directory files</span></span>
* <span data-ttu-id="e61a2-495">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="e61a2-495">Environment variables</span></span>
* <span data-ttu-id="e61a2-496">Objets .NET en mémoire</span><span class="sxs-lookup"><span data-stu-id="e61a2-496">In-memory .NET objects</span></span>
* <span data-ttu-id="e61a2-497">Fichiers de paramètres</span><span class="sxs-lookup"><span data-stu-id="e61a2-497">Settings files</span></span>

<span data-ttu-id="e61a2-498">Les packages de configuration pour les scénarios de fournisseur de configuration courants ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sont inclus implicitement dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="e61a2-498">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="e61a2-499">Les exemples de code qui suivent et dans l’échantillon d’application utilisent l’espace de noms <xref:Microsoft.Extensions.Configuration> :</span><span class="sxs-lookup"><span data-stu-id="e61a2-499">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="e61a2-500">Le *modèle d’options* est une extension des concepts de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="e61a2-500">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="e61a2-501">Les options utilisent des classes pour représenter les groupes de paramètres associés.</span><span class="sxs-lookup"><span data-stu-id="e61a2-501">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="e61a2-502">Pour plus d’informations, consultez <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-502">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="e61a2-503">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e61a2-503">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="e61a2-504">Configuration de l’hôte ou configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="e61a2-504">Host versus app configuration</span></span>

<span data-ttu-id="e61a2-505">Avant que l’application ne soit configurée et démarrée, un *hôte* est configuré et lancé.</span><span class="sxs-lookup"><span data-stu-id="e61a2-505">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="e61a2-506">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="e61a2-506">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="e61a2-507">L’application et l’hôte sont configurés à l’aide des fournisseurs de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="e61a2-507">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="e61a2-508">Les paires clé-valeur de la configuration de l’hôte sont également incluses dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-508">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="e61a2-509">Pour plus d’informations sur l’utilisation des fournisseurs de configuration lors de la génération de l’hôte et l’impact des sources de configuration sur la configuration de l’hôte, consultez <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-509">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="e61a2-510">Autre configuration</span><span class="sxs-lookup"><span data-stu-id="e61a2-510">Other configuration</span></span>

<span data-ttu-id="e61a2-511">Cette rubrique se rapporte uniquement à la configuration de l' *application*.</span><span class="sxs-lookup"><span data-stu-id="e61a2-511">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="e61a2-512">D’autres aspects de l’exécution et de l’hébergement des applications ASP.NET Core sont configurés à l’aide des fichiers de configuration non traités dans cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="e61a2-512">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="e61a2-513">*launch.js* / *launchSettings.jssur* sont des fichiers de configuration d’outils pour l’environnement de développement, décrits ci-après :</span><span class="sxs-lookup"><span data-stu-id="e61a2-513">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="e61a2-514">Dans <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="e61a2-514">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="e61a2-515">Dans l’ensemble de la documentation dans lequel les fichiers sont utilisés pour configurer des applications ASP.NET Core pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-515">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="e61a2-516">*web.config* est un fichier de configuration de serveur, décrit dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="e61a2-516">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="e61a2-517">Pour plus d’informations sur la migration de la configuration d’application à partir de versions antérieures de ASP.NET, consultez <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="e61a2-517">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="e61a2-518">Configuration par défaut</span><span class="sxs-lookup"><span data-stu-id="e61a2-518">Default configuration</span></span>

<span data-ttu-id="e61a2-519">Les applications web basées sur les modèles ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) appellent <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> pendant la création d’un hôte.</span><span class="sxs-lookup"><span data-stu-id="e61a2-519">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="e61a2-520">`CreateDefaultBuilder` fournit la configuration par défaut de l’application dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="e61a2-520">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="e61a2-521">Les éléments suivants s’appliquent aux applications qui utilisent l’[hôte web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="e61a2-521">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="e61a2-522">Pour plus de détails sur la configuration par défaut lors de l’utilisation de l’[hôte générique](xref:fundamentals/host/generic-host), consultez la [dernière version de cette rubrique](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="e61a2-522">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="e61a2-523">La configuration de l’hôte est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="e61a2-523">Host configuration is provided from:</span></span>
  * <span data-ttu-id="e61a2-524">Des variables d’environnement préfixées avec `ASPNETCORE_` (par exemple, `ASPNETCORE_ENVIRONMENT`) à l’aide du [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e61a2-524">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="e61a2-525">Le préfixe (`ASPNETCORE_`) est supprimé lorsque les paires clé-valeur de la configuration sont chargées.</span><span class="sxs-lookup"><span data-stu-id="e61a2-525">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="e61a2-526">Des arguments de ligne de commande à l’aide du [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e61a2-526">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="e61a2-527">La configuration de l’application est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="e61a2-527">App configuration is provided from:</span></span>
  * <span data-ttu-id="e61a2-528">*appsettings.json* utilisation du [fournisseur de configuration de fichier](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e61a2-528">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="e61a2-529">*appsettings.{Environment}.json* à l’aide du [Fournisseur de configuration de fichier](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e61a2-529">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="e61a2-530">Les [secrets utilisateur](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development` à l’aide de l’assembly d’entrée</span><span class="sxs-lookup"><span data-stu-id="e61a2-530">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="e61a2-531">Des variables d’environnement à l’aide du [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e61a2-531">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="e61a2-532">Des arguments de ligne de commande à l’aide du [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e61a2-532">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="e61a2-533">Sécurité</span><span class="sxs-lookup"><span data-stu-id="e61a2-533">Security</span></span>

<span data-ttu-id="e61a2-534">Adoptez les pratiques suivantes pour sécuriser les données de configuration sensibles :</span><span class="sxs-lookup"><span data-stu-id="e61a2-534">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="e61a2-535">Ne stockez jamais des mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte clair.</span><span class="sxs-lookup"><span data-stu-id="e61a2-535">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="e61a2-536">N’utilisez aucun secret de production dans les environnements de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="e61a2-536">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="e61a2-537">Spécifiez les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans un référentiel de code source.</span><span class="sxs-lookup"><span data-stu-id="e61a2-537">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="e61a2-538">Pour plus d'informations, voir les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="e61a2-538">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="e61a2-539"><xref:security/app-secrets>: Fournit des conseils sur l’utilisation de variables d’environnement pour stocker des données sensibles.</span><span class="sxs-lookup"><span data-stu-id="e61a2-539"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="e61a2-540">L’outil Gestionnaire de secret utilise le fournisseur de configuration de fichiers pour stocker les secrets d’utilisateur dans un fichier JSON sur le système local.</span><span class="sxs-lookup"><span data-stu-id="e61a2-540">The Secret Manager tool uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="e61a2-541">Le fournisseur de configuration de fichier est décrit plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="e61a2-541">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="e61a2-542">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stocke en toute sécurité des secrets d’application pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e61a2-542">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="e61a2-543">Pour plus d’informations, consultez <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-543">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="e61a2-544">Données de configuration hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="e61a2-544">Hierarchical configuration data</span></span>

<span data-ttu-id="e61a2-545">L’API Configuration est capable de maintenir des données de configuration hiérarchiques en aplanissant les données hiérarchiques à l’aide d’un délimiteur dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-545">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="e61a2-546">Dans le fichier JSON suivant, quatre clés existent dans une structure hiérarchique à deux sections :</span><span class="sxs-lookup"><span data-stu-id="e61a2-546">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="e61a2-547">Lorsque le fichier est lu dans la configuration, des clés uniques sont créées pour gérer la structure des données hiérarchiques d’origine de la source de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-547">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="e61a2-548">Les sections et les clés sont aplanies à l’aide d’un signe deux-points (`:`) pour conserver la structure d’origine :</span><span class="sxs-lookup"><span data-stu-id="e61a2-548">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="e61a2-549">section0:key0</span><span class="sxs-lookup"><span data-stu-id="e61a2-549">section0:key0</span></span>
* <span data-ttu-id="e61a2-550">section0:key1</span><span class="sxs-lookup"><span data-stu-id="e61a2-550">section0:key1</span></span>
* <span data-ttu-id="e61a2-551">section1:key0</span><span class="sxs-lookup"><span data-stu-id="e61a2-551">section1:key0</span></span>
* <span data-ttu-id="e61a2-552">section1:key1</span><span class="sxs-lookup"><span data-stu-id="e61a2-552">section1:key1</span></span>

<span data-ttu-id="e61a2-553">Les méthodes <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> et <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sont disponibles pour isoler les sections et les enfants d’une section dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-553"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="e61a2-554">Ces méthodes sont décrites plus loin dans [GetSection, GetChildren et Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="e61a2-554">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="e61a2-555">Conventions</span><span class="sxs-lookup"><span data-stu-id="e61a2-555">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="e61a2-556">Sources et fournisseurs</span><span class="sxs-lookup"><span data-stu-id="e61a2-556">Sources and providers</span></span>

<span data-ttu-id="e61a2-557">Au démarrage de l’application, les sources de configuration sont lues dans l’ordre où leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="e61a2-557">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="e61a2-558">Les fournisseurs de configuration qui implémentent la détection des modifications peuvent recharger la configuration lorsqu’un paramètre sous-jacent est modifié.</span><span class="sxs-lookup"><span data-stu-id="e61a2-558">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="e61a2-559">Par exemple, le fournisseur de configuration de fichier (décrit plus loin dans cette rubrique) et le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) implémentent la détection des modifications.</span><span class="sxs-lookup"><span data-stu-id="e61a2-559">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="e61a2-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> est disponible dans le conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e61a2-561"><xref:Microsoft.Extensions.Configuration.IConfiguration> peut être injecté dans une Razor page <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou un MVC <xref:Microsoft.AspNetCore.Mvc.Controller> pour obtenir la configuration de la classe.</span><span class="sxs-lookup"><span data-stu-id="e61a2-561"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="e61a2-562">Dans les exemples suivants, le `_config` champ est utilisé pour accéder aux valeurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-562">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="e61a2-563">Les fournisseurs de configuration ne peuvent pas utiliser le DI, car celui-ci n’est pas disponible lorsque les fournisseurs sont configurés par l’hôte.</span><span class="sxs-lookup"><span data-stu-id="e61a2-563">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="e61a2-564">Keys</span><span class="sxs-lookup"><span data-stu-id="e61a2-564">Keys</span></span>

<span data-ttu-id="e61a2-565">Les clés de configuration adoptent les conventions suivantes :</span><span class="sxs-lookup"><span data-stu-id="e61a2-565">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="e61a2-566">Les clés ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="e61a2-566">Keys are case-insensitive.</span></span> <span data-ttu-id="e61a2-567">Par exemple, `ConnectionString` et `connectionstring` sont traités en tant que clés équivalentes.</span><span class="sxs-lookup"><span data-stu-id="e61a2-567">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="e61a2-568">Si une valeur pour la même clé est définie par des fournisseurs de configuration identiques ou différents, la valeur utilisée est la dernière valeur définie sur la clé.</span><span class="sxs-lookup"><span data-stu-id="e61a2-568">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="e61a2-569">Pour plus d’informations sur les clés JSON en double, consultez [ce problème GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="e61a2-569">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="e61a2-570">Clés hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="e61a2-570">Hierarchical keys</span></span>
  * <span data-ttu-id="e61a2-571">Dans l’API Configuration, un séparateur sous forme de signe deux-points (`:`) fonctionne sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="e61a2-571">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="e61a2-572">Dans les variables d’environnement, un séparateur sous forme de signe deux-points peut ne pas fonctionner sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="e61a2-572">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="e61a2-573">Un trait de soulignement double (`__`) est pris en charge par toutes les plateformes et automatiquement transformé en signe deux-points.</span><span class="sxs-lookup"><span data-stu-id="e61a2-573">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="e61a2-574">Dans Azure Key Vault, les clés hiérarchiques utilisent `--` (deux tirets) comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="e61a2-574">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="e61a2-575">Écrivez du code pour remplacer les tirets par un signe deux-points lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-575">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="e61a2-576"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-576">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="e61a2-577">La liaison de tableau est décrite dans la section [Lier un tableau à une classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="e61a2-577">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="e61a2-578">Valeurs</span><span class="sxs-lookup"><span data-stu-id="e61a2-578">Values</span></span>

<span data-ttu-id="e61a2-579">Les valeurs de configuration adoptent les conventions suivantes :</span><span class="sxs-lookup"><span data-stu-id="e61a2-579">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="e61a2-580">Les valeurs sont des chaînes.</span><span class="sxs-lookup"><span data-stu-id="e61a2-580">Values are strings.</span></span>
* <span data-ttu-id="e61a2-581">Les valeurs NULL ne peuvent pas être stockées dans la configuration ou liées à des objets.</span><span class="sxs-lookup"><span data-stu-id="e61a2-581">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="e61a2-582">Fournisseurs</span><span class="sxs-lookup"><span data-stu-id="e61a2-582">Providers</span></span>

<span data-ttu-id="e61a2-583">Le tableau suivant présente les fournisseurs de configuration disponibles pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e61a2-583">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="e61a2-584">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="e61a2-584">Provider</span></span> | <span data-ttu-id="e61a2-585">Fournit la configuration à partir de&hellip;</span><span class="sxs-lookup"><span data-stu-id="e61a2-585">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="e61a2-586">[Fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) (rubrique *Sécurité*)</span><span class="sxs-lookup"><span data-stu-id="e61a2-586">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="e61a2-587">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e61a2-587">Azure Key Vault</span></span> |
| <span data-ttu-id="e61a2-588">[Fournisseur Azure App Configuration](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentation Azure)</span><span class="sxs-lookup"><span data-stu-id="e61a2-588">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="e61a2-589">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="e61a2-589">Azure App Configuration</span></span> |
| [<span data-ttu-id="e61a2-590">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e61a2-590">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="e61a2-591">Paramètres de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e61a2-591">Command-line parameters</span></span> |
| [<span data-ttu-id="e61a2-592">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="e61a2-592">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="e61a2-593">Source personnalisée</span><span class="sxs-lookup"><span data-stu-id="e61a2-593">Custom source</span></span> |
| [<span data-ttu-id="e61a2-594">Fournisseur de configuration de variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="e61a2-594">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="e61a2-595">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="e61a2-595">Environment variables</span></span> |
| [<span data-ttu-id="e61a2-596">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="e61a2-596">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="e61a2-597">Fichiers (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="e61a2-597">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="e61a2-598">Fournisseur de configuration clé par fichier</span><span class="sxs-lookup"><span data-stu-id="e61a2-598">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="e61a2-599">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="e61a2-599">Directory files</span></span> |
| [<span data-ttu-id="e61a2-600">Fournisseur de configuration de mémoire</span><span class="sxs-lookup"><span data-stu-id="e61a2-600">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="e61a2-601">Collections en mémoire</span><span class="sxs-lookup"><span data-stu-id="e61a2-601">In-memory collections</span></span> |
| <span data-ttu-id="e61a2-602">[Secrets](xref:security/app-secrets) de l’utilisateur (rubriques relatives à la *sécurité* )</span><span class="sxs-lookup"><span data-stu-id="e61a2-602">[User secrets](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="e61a2-603">Fichier dans le répertoire de profil utilisateur</span><span class="sxs-lookup"><span data-stu-id="e61a2-603">File in the user profile directory</span></span> |

<span data-ttu-id="e61a2-604">Au démarrage, les sources de configuration sont lues dans l’ordre où leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="e61a2-604">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="e61a2-605">Les fournisseurs de configuration décrits dans cette rubrique sont décrits par ordre alphabétique, et non pas dans l’ordre dans lequel le code les réorganise.</span><span class="sxs-lookup"><span data-stu-id="e61a2-605">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="e61a2-606">Commandez des fournisseurs de configuration dans le code pour répondre aux priorités des sources de configuration sous-jacentes requises par l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-606">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="e61a2-607">Une séquence type des fournisseurs de configuration est la suivante :</span><span class="sxs-lookup"><span data-stu-id="e61a2-607">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="e61a2-608">Fichiers ( *appsettings.json* , *appSettings. { Environment}. JSON*, où `{Environment}` est l’environnement d’hébergement actuel de l’application)</span><span class="sxs-lookup"><span data-stu-id="e61a2-608">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="e61a2-609">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e61a2-609">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="e61a2-610">[Secrets](xref:security/app-secrets) de l’utilisateur (environnement de développement uniquement)</span><span class="sxs-lookup"><span data-stu-id="e61a2-610">[User secrets](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="e61a2-611">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="e61a2-611">Environment variables</span></span>
1. <span data-ttu-id="e61a2-612">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e61a2-612">Command-line arguments</span></span>

<span data-ttu-id="e61a2-613">Une pratique courante consiste à placer le Fournisseur de configuration de ligne de commande en dernier dans une série de fournisseurs pour permettre aux arguments de ligne de commande de remplacer la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="e61a2-613">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="e61a2-614">La séquence de fournisseurs précédente est utilisée lors de l’initialisation d’un nouveau générateur d’hôte `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e61a2-614">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e61a2-615">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="e61a2-615">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="e61a2-616">Configurer le générateur d’ordinateur hôte avec UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="e61a2-616">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="e61a2-617">Pour configurer le générateur d’ordinateur hôte, appelez <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> sur le générateur d’hôte avec la configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-617">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="e61a2-618">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="e61a2-618">ConfigureAppConfiguration</span></span>

<span data-ttu-id="e61a2-619">Appelez `ConfigureAppConfiguration` quand vous créez l’hôte pour spécifier les fournisseurs de configuration de l’application en plus de ceux ajoutés automatiquement par `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-619">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="e61a2-620">Remplacez la configuration précédente par des arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e61a2-620">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="e61a2-621">Pour fournir une configuration d’application pouvant être remplacée par des arguments de ligne de commande, appelez `AddCommandLine` en dernier :</span><span class="sxs-lookup"><span data-stu-id="e61a2-621">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="e61a2-622">Supprimer les fournisseurs ajoutés par CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="e61a2-622">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="e61a2-623">Pour supprimer les fournisseurs ajoutés par `CreateDefaultBuilder` , appelez d’abord [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) sur [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="e61a2-623">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="e61a2-624">Utiliser la configuration lors du démarrage de l’application</span><span class="sxs-lookup"><span data-stu-id="e61a2-624">Consume configuration during app startup</span></span>

<span data-ttu-id="e61a2-625">La configuration fournie à l’application dans `ConfigureAppConfiguration` est disponible lors du démarrage de l’application, notamment `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-625">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e61a2-626">Pour plus d’informations, consultez la section [Accéder à la configuration lors du démarrage](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="e61a2-626">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="e61a2-627">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e61a2-627">Command-line Configuration Provider</span></span>

<span data-ttu-id="e61a2-628"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> charge la configuration à partir des paires clé-valeur de l’argument de ligne de commande lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="e61a2-628">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="e61a2-629">Pour activer la configuration en ligne de commande, la méthode d’extension <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> est appelée sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-629">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="e61a2-630">`AddCommandLine` est appelé automatiquement quand `CreateDefaultBuilder(string [])` est appelé.</span><span class="sxs-lookup"><span data-stu-id="e61a2-630">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="e61a2-631">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="e61a2-631">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="e61a2-632">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="e61a2-632">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="e61a2-633">Configuration facultative à partir de *appsettings.json* et *appSettings. { Fichiers Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="e61a2-633">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="e61a2-634">[Secrets](xref:security/app-secrets) de l’utilisateur dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-634">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="e61a2-635">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-635">Environment variables.</span></span>

<span data-ttu-id="e61a2-636">`CreateDefaultBuilder` ajoute en dernier le Fournisseur de configuration de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="e61a2-636">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="e61a2-637">Les arguments de ligne de commande passés lors de l’exécution remplacent la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="e61a2-637">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="e61a2-638">`CreateDefaultBuilder` agit lors de la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="e61a2-638">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="e61a2-639">Par conséquent, la configuration de ligne de commande activée par `CreateDefaultBuilder` peut affecter la façon dont l’hôte est configuré.</span><span class="sxs-lookup"><span data-stu-id="e61a2-639">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="e61a2-640">Pour les applications basées sur les modèles ASP.NET Core, `AddCommandLine` a déjà été appelé par `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-640">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e61a2-641">Pour ajouter des fournisseurs de configuration supplémentaires et conserver la capacité de remplacer leur configuration par des arguments de ligne de commande, appelez les fournisseurs supplémentaires de l’application dans `ConfigureAppConfiguration` et appelez `AddCommandLine` en dernier.</span><span class="sxs-lookup"><span data-stu-id="e61a2-641">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="e61a2-642">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="e61a2-642">**Example**</span></span>

<span data-ttu-id="e61a2-643">L’exemple d’application tire parti de la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte, qui inclut un appel à <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-643">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="e61a2-644">Ouvrez une invite de commandes dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="e61a2-644">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="e61a2-645">Fournissez un argument de ligne de commande à la commande `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-645">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="e61a2-646">Une fois que l’application est en cours d’exécution, ouvrez un navigateur à l’application à l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-646">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="e61a2-647">Notez que la sortie contient la paire clé-valeur pour l’argument de ligne de commande de configuration fourni à `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-647">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="e61a2-648">Arguments</span><span class="sxs-lookup"><span data-stu-id="e61a2-648">Arguments</span></span>

<span data-ttu-id="e61a2-649">La valeur doit suivre un signe égal (`=`) ou la clé doit avoir un préfixe (`--` ou `/`) lorsque la valeur suit un espace.</span><span class="sxs-lookup"><span data-stu-id="e61a2-649">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="e61a2-650">La valeur n’est pas requise si un signe égal est utilisé (par exemple, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="e61a2-650">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="e61a2-651">Préfixe de clé</span><span class="sxs-lookup"><span data-stu-id="e61a2-651">Key prefix</span></span>               | <span data-ttu-id="e61a2-652">Exemple</span><span class="sxs-lookup"><span data-stu-id="e61a2-652">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="e61a2-653">Aucun préfixe</span><span class="sxs-lookup"><span data-stu-id="e61a2-653">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="e61a2-654">Deux tirets (`--`)</span><span class="sxs-lookup"><span data-stu-id="e61a2-654">Two dashes (`--`)</span></span>        | <span data-ttu-id="e61a2-655">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="e61a2-655">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="e61a2-656">Barre oblique (`/`)</span><span class="sxs-lookup"><span data-stu-id="e61a2-656">Forward slash (`/`)</span></span>      | <span data-ttu-id="e61a2-657">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="e61a2-657">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="e61a2-658">Dans la même commande, ne mélangez pas des paires clé-valeur de l’argument de ligne de commande qui utilisent un signe égal avec des paires clé-valeur qui utilisent un espace.</span><span class="sxs-lookup"><span data-stu-id="e61a2-658">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="e61a2-659">Exemples de commandes :</span><span class="sxs-lookup"><span data-stu-id="e61a2-659">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="e61a2-660">Correspondances de commutateur</span><span class="sxs-lookup"><span data-stu-id="e61a2-660">Switch mappings</span></span>

<span data-ttu-id="e61a2-661">Les correspondances de commutateur permettent une logique de remplacement des noms de clés.</span><span class="sxs-lookup"><span data-stu-id="e61a2-661">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="e61a2-662">Lors de la génération manuelle d’une configuration avec un <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , fournissez un dictionnaire de remplacements de commutateur à la <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> méthode.</span><span class="sxs-lookup"><span data-stu-id="e61a2-662">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="e61a2-663">Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="e61a2-663">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="e61a2-664">Si la clé de ligne de commande est trouvée dans le dictionnaire, la valeur du dictionnaire (le remplacement de la clé) est repassée pour définir la paire clé-valeur dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-664">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="e61a2-665">Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).</span><span class="sxs-lookup"><span data-stu-id="e61a2-665">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="e61a2-666">Règles des clés du dictionnaire de correspondances de commutateur :</span><span class="sxs-lookup"><span data-stu-id="e61a2-666">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="e61a2-667">Les commutateurs doivent commencer par un tiret (`-`) ou un double tiret (`--`).</span><span class="sxs-lookup"><span data-stu-id="e61a2-667">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="e61a2-668">Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.</span><span class="sxs-lookup"><span data-stu-id="e61a2-668">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="e61a2-669">Créez un dictionnaire des mappages de commutateurs.</span><span class="sxs-lookup"><span data-stu-id="e61a2-669">Create a switch mappings dictionary.</span></span> <span data-ttu-id="e61a2-670">Dans l’exemple suivant, deux mappages de commutateurs sont créés :</span><span class="sxs-lookup"><span data-stu-id="e61a2-670">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="e61a2-671">Lorsque l’hôte est généré, appelez `AddCommandLine` avec le dictionnaire de mappages de commutateurs :</span><span class="sxs-lookup"><span data-stu-id="e61a2-671">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="e61a2-672">Pour les applications qui utilisent des mappages de commutateurs, l’appel à `CreateDefaultBuilder` ne doit pas passer d’arguments.</span><span class="sxs-lookup"><span data-stu-id="e61a2-672">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="e61a2-673">L’appel `AddCommandLine` de la méthode `CreateDefaultBuilder` n’inclut pas de commutateurs mappés et il n’existe aucun moyen de transmettre le dictionnaire de correspondance de commutateur à `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-673">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e61a2-674">La solution ne consiste pas à transmettre les arguments à `CreateDefaultBuilder`, mais plutôt à permettre à la méthode `AddCommandLine` de la méthode `ConfigurationBuilder` de traiter les arguments et le dictionnaire de mappage de commutateur.</span><span class="sxs-lookup"><span data-stu-id="e61a2-674">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="e61a2-675">Une fois le dictionnaire de correspondances de commutateur créé, il contient les données affichées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="e61a2-675">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="e61a2-676">Clé</span><span class="sxs-lookup"><span data-stu-id="e61a2-676">Key</span></span>       | <span data-ttu-id="e61a2-677">Valeur</span><span class="sxs-lookup"><span data-stu-id="e61a2-677">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="e61a2-678">Si les clés mappées au commutateur sont utilisées lors du démarrage de l’application, la configuration reçoit la valeur de configuration sur la clé fournie par le dictionnaire :</span><span class="sxs-lookup"><span data-stu-id="e61a2-678">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="e61a2-679">Après avoir exécuté la commande précédente, la configuration contient les valeurs indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="e61a2-679">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="e61a2-680">Clé</span><span class="sxs-lookup"><span data-stu-id="e61a2-680">Key</span></span>               | <span data-ttu-id="e61a2-681">Valeur</span><span class="sxs-lookup"><span data-stu-id="e61a2-681">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="e61a2-682">Fournisseur de configuration de variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="e61a2-682">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="e61a2-683"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> charge la configuration à partir des paires clé-valeur de la variable d’environnement lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="e61a2-683">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="e61a2-684">Pour activer la configuration des variables d’environnement, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-684">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="e61a2-685">[Azure App service](https://azure.microsoft.com/services/app-service/) permet de définir des variables d’environnement dans le portail Azure qui peuvent remplacer la configuration de l’application à l’aide du fournisseur de configuration des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-685">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="e61a2-686">Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="e61a2-686">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="e61a2-687">`AddEnvironmentVariables` sert à charger les variables d’environnement préfixées avec `ASPNETCORE_` pour la [configuration hôte](#host-versus-app-configuration) lorsqu’un nouveau générateur d’hôte est initialisé avec l’[hôte web](xref:fundamentals/host/web-host) et que `CreateDefaultBuilder` est appelé.</span><span class="sxs-lookup"><span data-stu-id="e61a2-687">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="e61a2-688">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="e61a2-688">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="e61a2-689">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="e61a2-689">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="e61a2-690">Configuration de l’application à partir de variables d’environnement sans préfixe en appelant `AddEnvironmentVariables` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="e61a2-690">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="e61a2-691">Configuration facultative à partir de *appsettings.json* et *appSettings. { Fichiers Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="e61a2-691">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="e61a2-692">[Secrets](xref:security/app-secrets) de l’utilisateur dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-692">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="e61a2-693">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e61a2-693">Command-line arguments.</span></span>

<span data-ttu-id="e61a2-694">Le fournisseur de configuration de variables d’environnement est appelé une fois que la configuration est établie à partir des secrets utilisateur et des fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="e61a2-694">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="e61a2-695">Le fait d’appeler le fournisseur ainsi permet de lire les variables d’environnement pendant l’exécution pour substituer la configuration définie par les secrets utilisateur et les fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="e61a2-695">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="e61a2-696">Pour fournir la configuration d’application à partir de variables d’environnement supplémentaires, appelez les fournisseurs supplémentaires de l’application dans `ConfigureAppConfiguration` et appelez `AddEnvironmentVariables` avec le préfixe :</span><span class="sxs-lookup"><span data-stu-id="e61a2-696">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="e61a2-697">Appelez `AddEnvironmentVariables` Last pour autoriser les variables d’environnement avec le préfixe spécifié à substituer des valeurs d’autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="e61a2-697">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="e61a2-698">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="e61a2-698">**Example**</span></span>

<span data-ttu-id="e61a2-699">L’exemple d’application tire parti de la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte, qui inclut un appel à `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-699">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="e61a2-700">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-700">Run the sample app.</span></span> <span data-ttu-id="e61a2-701">Ouvrez un navigateur vers l’application avec l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-701">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="e61a2-702">Notez que la sortie contient la paire clé-valeur pour la variable d’environnement `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-702">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="e61a2-703">La valeur reflète l’environnement dans lequel l’application est en cours d’exécution, en général `Development` lors de l’exécution locale.</span><span class="sxs-lookup"><span data-stu-id="e61a2-703">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="e61a2-704">Pour que la liste des variables d’environnement restituée par l’application soit courte, l’application filtre les variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-704">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="e61a2-705">Consultez le fichier *Pages/Index.cshtml.cs* de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-705">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="e61a2-706">Pour exposer toutes les variables d’environnement disponibles pour l’application, remplacez `FilteredConfiguration` dans *pages/index. cshtml. cs* par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="e61a2-706">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="e61a2-707">Préfixes</span><span class="sxs-lookup"><span data-stu-id="e61a2-707">Prefixes</span></span>

<span data-ttu-id="e61a2-708">Les variables d’environnement chargées dans la configuration de l’application sont filtrées lors de la spécification d’un préfixe à la `AddEnvironmentVariables` méthode.</span><span class="sxs-lookup"><span data-stu-id="e61a2-708">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="e61a2-709">Par exemple, pour filtrer les variables d’environnement sur le préfixe `CUSTOM_`, fournissez le préfixe au fournisseur de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-709">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="e61a2-710">Le préfixe est supprimé lorsque les paires clé-valeur de la configuration sont créées.</span><span class="sxs-lookup"><span data-stu-id="e61a2-710">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="e61a2-711">Lorsque le générateur d’hôte est créé, la configuration de l’hôte est fournie par des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-711">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="e61a2-712">Pour plus d’informations sur le préfixe utilisé pour ces variables d’environnement, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="e61a2-712">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="e61a2-713">**Préfixes des chaînes de connexion**</span><span class="sxs-lookup"><span data-stu-id="e61a2-713">**Connection string prefixes**</span></span>

<span data-ttu-id="e61a2-714">L’API Configuration possède des règles de traitement spéciales pour quatre variables d’environnement de chaîne de connexion impliquées dans la configuration des chaînes de connexion Azure pour l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-714">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="e61a2-715">Les variables d’environnement avec les préfixes indiqués dans le tableau sont chargées dans l’application si aucun préfixe n’est fourni à `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-715">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="e61a2-716">Préfixe de la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="e61a2-716">Connection string prefix</span></span> | <span data-ttu-id="e61a2-717">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="e61a2-717">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="e61a2-718">Fournisseur personnalisé</span><span class="sxs-lookup"><span data-stu-id="e61a2-718">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="e61a2-719">MySQL</span><span class="sxs-lookup"><span data-stu-id="e61a2-719">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="e61a2-720">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="e61a2-720">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="e61a2-721">SQL Server</span><span class="sxs-lookup"><span data-stu-id="e61a2-721">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="e61a2-722">Quand une variable d’environnement est découverte et chargée dans la configuration avec l’un des quatre préfixes indiqués dans le tableau :</span><span class="sxs-lookup"><span data-stu-id="e61a2-722">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="e61a2-723">La clé de configuration est créée en supprimant le préfixe de la variable d’environnement et en ajoutant une section de clé de configuration (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="e61a2-723">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="e61a2-724">Une nouvelle paire clé-valeur de configuration est créée qui représente le fournisseur de connexion de base de données (à l’exception de `CUSTOMCONNSTR_`, qui ne possède aucun fournisseur indiqué).</span><span class="sxs-lookup"><span data-stu-id="e61a2-724">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="e61a2-725">Clé de variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="e61a2-725">Environment variable key</span></span> | <span data-ttu-id="e61a2-726">Clé de configuration convertie</span><span class="sxs-lookup"><span data-stu-id="e61a2-726">Converted configuration key</span></span> | <span data-ttu-id="e61a2-727">Entrée de configuration de fournisseur</span><span class="sxs-lookup"><span data-stu-id="e61a2-727">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e61a2-728">Entrée de configuration non créée.</span><span class="sxs-lookup"><span data-stu-id="e61a2-728">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e61a2-729">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e61a2-730">Valeur: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="e61a2-730">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e61a2-731">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-731">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e61a2-732">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="e61a2-732">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e61a2-733">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-733">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e61a2-734">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="e61a2-734">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="e61a2-735">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="e61a2-735">**Example**</span></span>

<span data-ttu-id="e61a2-736">Une variable d’environnement de chaîne de connexion personnalisée est créée sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="e61a2-736">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="e61a2-737">Nom : `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="e61a2-737">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="e61a2-738">Valeur: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="e61a2-738">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="e61a2-739">Si `IConfiguration` est injecté et affecté à un champ nommé `_config` , lisez la valeur :</span><span class="sxs-lookup"><span data-stu-id="e61a2-739">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="e61a2-740">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="e61a2-740">File Configuration Provider</span></span>

<span data-ttu-id="e61a2-741"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> est la classe de base pour charger la configuration à partir du système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="e61a2-741"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="e61a2-742">Les fournisseurs de configuration suivants sont dédiés à des types de fichiers spécifiques :</span><span class="sxs-lookup"><span data-stu-id="e61a2-742">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="e61a2-743">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="e61a2-743">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="e61a2-744">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="e61a2-744">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="e61a2-745">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="e61a2-745">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="e61a2-746">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="e61a2-746">INI Configuration Provider</span></span>

<span data-ttu-id="e61a2-747"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier INI lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="e61a2-747">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="e61a2-748">Pour activer la configuration du fichier INI, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-748">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="e61a2-749">Le signe deux-points peut servir de délimiteur de section dans la configuration d’un fichier INI.</span><span class="sxs-lookup"><span data-stu-id="e61a2-749">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="e61a2-750">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="e61a2-750">Overloads permit specifying:</span></span>

* <span data-ttu-id="e61a2-751">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="e61a2-751">Whether the file is optional.</span></span>
* <span data-ttu-id="e61a2-752">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="e61a2-752">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="e61a2-753">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="e61a2-753">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="e61a2-754">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="e61a2-754">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="e61a2-755">Exemple générique d’un fichier de configuration INI :</span><span class="sxs-lookup"><span data-stu-id="e61a2-755">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="e61a2-756">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-756">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="e61a2-757">section0:key0</span><span class="sxs-lookup"><span data-stu-id="e61a2-757">section0:key0</span></span>
* <span data-ttu-id="e61a2-758">section0:key1</span><span class="sxs-lookup"><span data-stu-id="e61a2-758">section0:key1</span></span>
* <span data-ttu-id="e61a2-759">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="e61a2-759">section1:subsection:key</span></span>
* <span data-ttu-id="e61a2-760">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="e61a2-760">section2:subsection0:key</span></span>
* <span data-ttu-id="e61a2-761">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="e61a2-761">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="e61a2-762">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="e61a2-762">JSON Configuration Provider</span></span>

<span data-ttu-id="e61a2-763"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier JSON lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="e61a2-763">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="e61a2-764">Pour activer la configuration du fichier JSON, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-764">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="e61a2-765">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="e61a2-765">Overloads permit specifying:</span></span>

* <span data-ttu-id="e61a2-766">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="e61a2-766">Whether the file is optional.</span></span>
* <span data-ttu-id="e61a2-767">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="e61a2-767">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="e61a2-768">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="e61a2-768">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="e61a2-769">`AddJsonFile` est appelé automatiquement deux fois lors de l’initialisation d’un nouveau générateur d’hôte `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e61a2-769">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e61a2-770">La méthode est appelée pour charger la configuration à partir de :</span><span class="sxs-lookup"><span data-stu-id="e61a2-770">The method is called to load configuration from:</span></span>

* <span data-ttu-id="e61a2-771">*appsettings.json*: Ce fichier est lu en premier.</span><span class="sxs-lookup"><span data-stu-id="e61a2-771">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="e61a2-772">La version de l’environnement du fichier peut remplacer les valeurs fournies par le *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="e61a2-772">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="e61a2-773">*appSettings. {Environment}. JSON*: la version de l’environnement du fichier est chargée à partir de [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="e61a2-773">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="e61a2-774">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="e61a2-774">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="e61a2-775">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="e61a2-775">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="e61a2-776">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-776">Environment variables.</span></span>
* <span data-ttu-id="e61a2-777">[Secrets](xref:security/app-secrets) de l’utilisateur dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-777">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="e61a2-778">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e61a2-778">Command-line arguments.</span></span>

<span data-ttu-id="e61a2-779">Le Fournisseur de configuration JSON est établi en premier.</span><span class="sxs-lookup"><span data-stu-id="e61a2-779">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="e61a2-780">Par conséquent, les secrets utilisateur, les variables d’environnement et les arguments de ligne de commande remplacent la configuration définie par les fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="e61a2-780">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="e61a2-781">Appelez `ConfigureAppConfiguration` lors de la génération de l’hôte pour spécifier la configuration de l’application pour les fichiers autres que *appsettings.json* et *appSettings. { Environnement}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e61a2-781">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="e61a2-782">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="e61a2-782">**Example**</span></span>

<span data-ttu-id="e61a2-783">L’exemple d’application tire parti de la méthode de commodité statique `CreateDefaultBuilder` pour créer l’hôte, ce qui comprend deux appels à `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-783">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="e61a2-784">Le premier appel à `AddJsonFile` charge la configuration à partir de *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="e61a2-784">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="e61a2-785">Le deuxième appel à `AddJsonFile` charge la configuration à partir de *appSettings. { Environnement}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="e61a2-785">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="e61a2-786">Pour *appsettings.Development.js* dans l’exemple d’application, le fichier suivant est chargé :</span><span class="sxs-lookup"><span data-stu-id="e61a2-786">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="e61a2-787">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-787">Run the sample app.</span></span> <span data-ttu-id="e61a2-788">Ouvrez un navigateur vers l’application avec l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-788">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="e61a2-789">La sortie contient des paires clé-valeur pour la configuration en fonction de l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-789">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="e61a2-790">Le niveau de journalisation de la clé `Logging:LogLevel:Default` est `Debug` lors de l’exécution de l’application dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="e61a2-790">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="e61a2-791">Exécutez à nouveau l’exemple d’application dans l’environnement de production :</span><span class="sxs-lookup"><span data-stu-id="e61a2-791">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="e61a2-792">Ouvrez le fichier *Properties/launchSettings.js* .</span><span class="sxs-lookup"><span data-stu-id="e61a2-792">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="e61a2-793">Dans le `ConfigurationSample` profil, remplacez la valeur de la `ASPNETCORE_ENVIRONMENT` variable d’environnement par `Production` .</span><span class="sxs-lookup"><span data-stu-id="e61a2-793">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="e61a2-794">Enregistrez le fichier et exécutez l’application avec `dotnet run` dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="e61a2-794">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="e61a2-795">Les paramètres de la *appsettings.Development.js* qui ne se substituent plus aux paramètres dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e61a2-795">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="e61a2-796">Le niveau de journalisation de la clé `Logging:LogLevel:Default` est `Warning` .</span><span class="sxs-lookup"><span data-stu-id="e61a2-796">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="e61a2-797">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="e61a2-797">XML Configuration Provider</span></span>

<span data-ttu-id="e61a2-798"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier XML lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="e61a2-798">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="e61a2-799">Pour activer la configuration du fichier XML, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-799">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="e61a2-800">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="e61a2-800">Overloads permit specifying:</span></span>

* <span data-ttu-id="e61a2-801">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="e61a2-801">Whether the file is optional.</span></span>
* <span data-ttu-id="e61a2-802">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="e61a2-802">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="e61a2-803">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="e61a2-803">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="e61a2-804">Le nœud racine du fichier de configuration est ignoré lorsque les paires clé-valeur de la configuration sont créées.</span><span class="sxs-lookup"><span data-stu-id="e61a2-804">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="e61a2-805">Ne spécifiez pas une définition de type de document (DTD) ou un espace de noms dans le fichier.</span><span class="sxs-lookup"><span data-stu-id="e61a2-805">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="e61a2-806">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="e61a2-806">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="e61a2-807">Les fichiers de configuration XML peuvent utiliser des noms d’éléments distincts pour les sections répétitives :</span><span class="sxs-lookup"><span data-stu-id="e61a2-807">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="e61a2-808">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-808">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="e61a2-809">section0:key0</span><span class="sxs-lookup"><span data-stu-id="e61a2-809">section0:key0</span></span>
* <span data-ttu-id="e61a2-810">section0:key1</span><span class="sxs-lookup"><span data-stu-id="e61a2-810">section0:key1</span></span>
* <span data-ttu-id="e61a2-811">section1:key0</span><span class="sxs-lookup"><span data-stu-id="e61a2-811">section1:key0</span></span>
* <span data-ttu-id="e61a2-812">section1:key1</span><span class="sxs-lookup"><span data-stu-id="e61a2-812">section1:key1</span></span>

<span data-ttu-id="e61a2-813">Les éléments répétitifs qui utilisent le même nom d’élément fonctionnent si l’attribut `name` est utilisé pour distinguer les éléments :</span><span class="sxs-lookup"><span data-stu-id="e61a2-813">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="e61a2-814">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-814">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="e61a2-815">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="e61a2-815">section:section0:key:key0</span></span>
* <span data-ttu-id="e61a2-816">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="e61a2-816">section:section0:key:key1</span></span>
* <span data-ttu-id="e61a2-817">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="e61a2-817">section:section1:key:key0</span></span>
* <span data-ttu-id="e61a2-818">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="e61a2-818">section:section1:key:key1</span></span>

<span data-ttu-id="e61a2-819">Les attributs peuvent être utilisés pour fournir des valeurs :</span><span class="sxs-lookup"><span data-stu-id="e61a2-819">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="e61a2-820">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-820">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="e61a2-821">key:attribute</span><span class="sxs-lookup"><span data-stu-id="e61a2-821">key:attribute</span></span>
* <span data-ttu-id="e61a2-822">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="e61a2-822">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="e61a2-823">Fournisseur de configuration clé par fichier</span><span class="sxs-lookup"><span data-stu-id="e61a2-823">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="e61a2-824">Le <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> utilise les fichiers d’un répertoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-824">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="e61a2-825">La clé est le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="e61a2-825">The key is the file name.</span></span> <span data-ttu-id="e61a2-826">La valeur contient le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="e61a2-826">The value contains the file's contents.</span></span> <span data-ttu-id="e61a2-827">Le Fournisseur de configuration Clé par fichier est utilisé dans les scénarios d’hébergement de Docker.</span><span class="sxs-lookup"><span data-stu-id="e61a2-827">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="e61a2-828">Pour activer la configuration clé par fichier, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-828">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="e61a2-829">Le `directoryPath` aux fichiers doit être un chemin d’accès absolu.</span><span class="sxs-lookup"><span data-stu-id="e61a2-829">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="e61a2-830">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="e61a2-830">Overloads permit specifying:</span></span>

* <span data-ttu-id="e61a2-831">Un délégué `Action<KeyPerFileConfigurationSource>` qui configure la source.</span><span class="sxs-lookup"><span data-stu-id="e61a2-831">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="e61a2-832">Si le répertoire est facultatif et le chemin d’accès au répertoire.</span><span class="sxs-lookup"><span data-stu-id="e61a2-832">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="e61a2-833">Le double trait de soulignement (`__`) est utilisé comme un délimiteur de clé de configuration dans les noms de fichiers.</span><span class="sxs-lookup"><span data-stu-id="e61a2-833">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="e61a2-834">Par exemple, le nom de fichier `Logging__LogLevel__System` génère la clé de configuration `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-834">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="e61a2-835">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="e61a2-835">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="e61a2-836">Fournisseur de configuration de mémoire</span><span class="sxs-lookup"><span data-stu-id="e61a2-836">Memory Configuration Provider</span></span>

<span data-ttu-id="e61a2-837">Le <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> utilise une collection en mémoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-837">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="e61a2-838">Pour activer la configuration de la collection en mémoire, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-838">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="e61a2-839">Le fournisseur de configuration peut être initialisé avec un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-839">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="e61a2-840">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-840">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="e61a2-841">Dans l’exemple suivant, un dictionnaire de configuration est créé :</span><span class="sxs-lookup"><span data-stu-id="e61a2-841">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="e61a2-842">Le dictionnaire est utilisé avec un appel à `AddInMemoryCollection` pour fournir la configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-842">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="e61a2-843">GetValue</span><span class="sxs-lookup"><span data-stu-id="e61a2-843">GetValue</span></span>

<span data-ttu-id="e61a2-844">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrait une valeur unique de la configuration avec une clé spécifiée et la convertit en type de non-collection spécifié.</span><span class="sxs-lookup"><span data-stu-id="e61a2-844">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="e61a2-845">Une surcharge accepte une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="e61a2-845">An overload accepts a default value.</span></span>

<span data-ttu-id="e61a2-846">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="e61a2-846">The following example:</span></span>

* <span data-ttu-id="e61a2-847">Extrait la valeur de chaîne de la configuration avec la clé `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-847">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="e61a2-848">Si `NumberKey` est introuvable dans les clés de configuration, la valeur par défaut de `99` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="e61a2-848">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="e61a2-849">Tape la valeur comme `int`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-849">Types the value as an `int`.</span></span>
* <span data-ttu-id="e61a2-850">Stocke la valeur dans la propriété `NumberConfig` pour une utilisation par la page.</span><span class="sxs-lookup"><span data-stu-id="e61a2-850">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="e61a2-851">GetSection, GetChildren et Exists</span><span class="sxs-lookup"><span data-stu-id="e61a2-851">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="e61a2-852">Pour les exemples qui suivent, utilisez le fichier JSON suivant.</span><span class="sxs-lookup"><span data-stu-id="e61a2-852">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="e61a2-853">Quatre clés se trouvent dans deux sections, dont l’une inclut deux sous-sections :</span><span class="sxs-lookup"><span data-stu-id="e61a2-853">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="e61a2-854">Lorsque le fichier est lu dans la configuration, les clés hiérarchiques uniques suivantes sont créées pour stocker les valeurs de la configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-854">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="e61a2-855">section0:key0</span><span class="sxs-lookup"><span data-stu-id="e61a2-855">section0:key0</span></span>
* <span data-ttu-id="e61a2-856">section0:key1</span><span class="sxs-lookup"><span data-stu-id="e61a2-856">section0:key1</span></span>
* <span data-ttu-id="e61a2-857">section1:key0</span><span class="sxs-lookup"><span data-stu-id="e61a2-857">section1:key0</span></span>
* <span data-ttu-id="e61a2-858">section1:key1</span><span class="sxs-lookup"><span data-stu-id="e61a2-858">section1:key1</span></span>
* <span data-ttu-id="e61a2-859">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="e61a2-859">section2:subsection0:key0</span></span>
* <span data-ttu-id="e61a2-860">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="e61a2-860">section2:subsection0:key1</span></span>
* <span data-ttu-id="e61a2-861">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="e61a2-861">section2:subsection1:key0</span></span>
* <span data-ttu-id="e61a2-862">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="e61a2-862">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="e61a2-863">GetSection</span><span class="sxs-lookup"><span data-stu-id="e61a2-863">GetSection</span></span>

<span data-ttu-id="e61a2-864">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrait une sous-section de la configuration avec la clé de sous-section spécifiée.</span><span class="sxs-lookup"><span data-stu-id="e61a2-864">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="e61a2-865">Pour retourner un <xref:Microsoft.Extensions.Configuration.IConfigurationSection> contenant uniquement les paires clé-valeur dans `section1`, appelez `GetSection` et fournissez le nom de section :</span><span class="sxs-lookup"><span data-stu-id="e61a2-865">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="e61a2-866">`configSection` n’a pas de valeur, seulement une clé et un chemin.</span><span class="sxs-lookup"><span data-stu-id="e61a2-866">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="e61a2-867">De même, pour obtenir les valeurs des clés dans `section2:subsection0`, appelez `GetSection` et fournissez le chemin d’accès de la section :</span><span class="sxs-lookup"><span data-stu-id="e61a2-867">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="e61a2-868">`GetSection` ne retourne jamais `null`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-868">`GetSection` never returns `null`.</span></span> <span data-ttu-id="e61a2-869">Si aucune section correspondante n’est trouvée, une valeur `IConfigurationSection` vide est retournée.</span><span class="sxs-lookup"><span data-stu-id="e61a2-869">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="e61a2-870">Quand `GetSection` retourne une section correspondante, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="e61a2-870">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="e61a2-871"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> et <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> sont retournés quand la section existe.</span><span class="sxs-lookup"><span data-stu-id="e61a2-871">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="e61a2-872">GetChildren</span><span class="sxs-lookup"><span data-stu-id="e61a2-872">GetChildren</span></span>

<span data-ttu-id="e61a2-873">Un appel à [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) sur `section2` obtient un `IEnumerable<IConfigurationSection>` qui inclut :</span><span class="sxs-lookup"><span data-stu-id="e61a2-873">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="e61a2-874">Exists</span><span class="sxs-lookup"><span data-stu-id="e61a2-874">Exists</span></span>

<span data-ttu-id="e61a2-875">Utilisez [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pour déterminer si une section de configuration existe :</span><span class="sxs-lookup"><span data-stu-id="e61a2-875">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="e61a2-876">Compte tenu des données d’exemple, `sectionExists` est `false`, car il n’y a pas de section `section2:subsection2` dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-876">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="e61a2-877">Établir une liaison à un graphe d’objets</span><span class="sxs-lookup"><span data-stu-id="e61a2-877">Bind to an object graph</span></span>

<span data-ttu-id="e61a2-878"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> est capable de lier l’intégralité d’un graphe d’objets POCO.</span><span class="sxs-lookup"><span data-stu-id="e61a2-878"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="e61a2-879">Comme pour la liaison d’un objet simple, seules les propriétés accessibles en lecture/écriture publiques sont liées.</span><span class="sxs-lookup"><span data-stu-id="e61a2-879">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="e61a2-880">L’exemple contient un modèle `TvShow` dont le graphe d’objets inclut les classes `Metadata` et `Actors` (*Models/TvShow.cs*) :</span><span class="sxs-lookup"><span data-stu-id="e61a2-880">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="e61a2-881">L’exemple d’application a un fichier *tvshow.xml* contenant les données de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-881">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="e61a2-882">La configuration est liée à l’intégralité du graphe d’objets `TvShow` avec la méthode `Bind`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-882">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="e61a2-883">L’instance liée est affectée à une propriété pour le rendu :</span><span class="sxs-lookup"><span data-stu-id="e61a2-883">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="e61a2-884">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) lie et retourne le type spécifié.</span><span class="sxs-lookup"><span data-stu-id="e61a2-884">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="e61a2-885">Il est plus pratique d’utiliser `Get<T>` que `Bind`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-885">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="e61a2-886">Le code suivant montre comment utiliser `Get<T>` avec l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="e61a2-886">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="e61a2-887">Lier un tableau à une classe</span><span class="sxs-lookup"><span data-stu-id="e61a2-887">Bind an array to a class</span></span>

<span data-ttu-id="e61a2-888">*L’exemple d’application illustre les concepts abordés dans cette section.*</span><span class="sxs-lookup"><span data-stu-id="e61a2-888">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="e61a2-889"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-889">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="e61a2-890">Tout format de tableau qui expose un segment de clé numérique ( `:0:` , `:1:` , &hellip; `:{n}:` ) est capable d’effectuer une liaison de tableau à un tableau de classes POCO.</span><span class="sxs-lookup"><span data-stu-id="e61a2-890">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="e61a2-891">La liaison est fournie par convention.</span><span class="sxs-lookup"><span data-stu-id="e61a2-891">Binding is provided by convention.</span></span> <span data-ttu-id="e61a2-892">Les fournisseurs de configuration personnalisés ne sont pas obligés d’implémenter la liaison de tableau.</span><span class="sxs-lookup"><span data-stu-id="e61a2-892">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="e61a2-893">**Traitement de tableau en mémoire**</span><span class="sxs-lookup"><span data-stu-id="e61a2-893">**In-memory array processing**</span></span>

<span data-ttu-id="e61a2-894">Observez les valeurs et les clés de configuration indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="e61a2-894">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="e61a2-895">Clé</span><span class="sxs-lookup"><span data-stu-id="e61a2-895">Key</span></span>             | <span data-ttu-id="e61a2-896">Valeur</span><span class="sxs-lookup"><span data-stu-id="e61a2-896">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="e61a2-897">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="e61a2-897">array:entries:0</span></span> | <span data-ttu-id="e61a2-898">value0</span><span class="sxs-lookup"><span data-stu-id="e61a2-898">value0</span></span> |
| <span data-ttu-id="e61a2-899">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="e61a2-899">array:entries:1</span></span> | <span data-ttu-id="e61a2-900">valeur1</span><span class="sxs-lookup"><span data-stu-id="e61a2-900">value1</span></span> |
| <span data-ttu-id="e61a2-901">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="e61a2-901">array:entries:2</span></span> | <span data-ttu-id="e61a2-902">valeur2</span><span class="sxs-lookup"><span data-stu-id="e61a2-902">value2</span></span> |
| <span data-ttu-id="e61a2-903">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="e61a2-903">array:entries:4</span></span> | <span data-ttu-id="e61a2-904">value4</span><span class="sxs-lookup"><span data-stu-id="e61a2-904">value4</span></span> |
| <span data-ttu-id="e61a2-905">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="e61a2-905">array:entries:5</span></span> | <span data-ttu-id="e61a2-906">value5</span><span class="sxs-lookup"><span data-stu-id="e61a2-906">value5</span></span> |

<span data-ttu-id="e61a2-907">Ces clés et valeurs sont chargées dans l’exemple d’application à l’aide du Fournisseur de configuration de mémoire :</span><span class="sxs-lookup"><span data-stu-id="e61a2-907">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="e61a2-908">Le tableau ignore une valeur pour l’index &num;3.</span><span class="sxs-lookup"><span data-stu-id="e61a2-908">The array skips a value for index &num;3.</span></span> <span data-ttu-id="e61a2-909">Le binder de configuration n’est pas capable de lier des valeurs null ou de créer des entrées null dans les objets liés, ce qui deviendra clair dans un moment lorsque le résultat de liaison de ce tableau à un objet est illustré.</span><span class="sxs-lookup"><span data-stu-id="e61a2-909">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="e61a2-910">Dans l’exemple d’application, une classe POCO est disponible pour contenir les données de configuration liées :</span><span class="sxs-lookup"><span data-stu-id="e61a2-910">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="e61a2-911">Les données de configuration sont liées à l’objet :</span><span class="sxs-lookup"><span data-stu-id="e61a2-911">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="e61a2-912">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) la syntaxe peut également être utilisée, ce qui se traduit par un code plus compact :</span><span class="sxs-lookup"><span data-stu-id="e61a2-912">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="e61a2-913">L’objet lié, une instance de `ArrayExample`, reçoit les données de tableau à partir de la configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-913">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="e61a2-914">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="e61a2-914">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="e61a2-915">Valeur `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="e61a2-915">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="e61a2-916">0</span><span class="sxs-lookup"><span data-stu-id="e61a2-916">0</span></span>                            | <span data-ttu-id="e61a2-917">value0</span><span class="sxs-lookup"><span data-stu-id="e61a2-917">value0</span></span>                       |
| <span data-ttu-id="e61a2-918">1</span><span class="sxs-lookup"><span data-stu-id="e61a2-918">1</span></span>                            | <span data-ttu-id="e61a2-919">valeur1</span><span class="sxs-lookup"><span data-stu-id="e61a2-919">value1</span></span>                       |
| <span data-ttu-id="e61a2-920">2</span><span class="sxs-lookup"><span data-stu-id="e61a2-920">2</span></span>                            | <span data-ttu-id="e61a2-921">valeur2</span><span class="sxs-lookup"><span data-stu-id="e61a2-921">value2</span></span>                       |
| <span data-ttu-id="e61a2-922">3</span><span class="sxs-lookup"><span data-stu-id="e61a2-922">3</span></span>                            | <span data-ttu-id="e61a2-923">value4</span><span class="sxs-lookup"><span data-stu-id="e61a2-923">value4</span></span>                       |
| <span data-ttu-id="e61a2-924">4</span><span class="sxs-lookup"><span data-stu-id="e61a2-924">4</span></span>                            | <span data-ttu-id="e61a2-925">value5</span><span class="sxs-lookup"><span data-stu-id="e61a2-925">value5</span></span>                       |

<span data-ttu-id="e61a2-926">L’index &num;3 dans l’objet lié contient les données de configuration pour la clé de configuration `array:4` et sa valeur de `value4`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-926">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="e61a2-927">Lorsque des données de configuration contenant un tableau sont liées, les index de tableau dans les clés de configuration sont simplement utilisés pour itérer les données de configuration lors de la création de l’objet.</span><span class="sxs-lookup"><span data-stu-id="e61a2-927">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="e61a2-928">Une valeur null ne peut pas être conservée dans des données de configuration, et une entrée à valeur null n’est pas créée dans un objet lié quand un tableau dans des clés de configuration ignore un ou plusieurs index.</span><span class="sxs-lookup"><span data-stu-id="e61a2-928">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="e61a2-929">L’élément de configuration manquant pour l’index &num;3 peut être fourni avant la liaison à l’instance `ArrayExample` par n’importe quel fournisseur de configuration qui génère la paire clé-valeur correcte dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-929">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="e61a2-930">Si l’exemple inclut un Fournisseur de configuration JSON supplémentaire avec la paire clé-valeur manquante, `ArrayExample.Entries` correspond à l’intégralité du tableau de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-930">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="e61a2-931">*missing_value.json* :</span><span class="sxs-lookup"><span data-stu-id="e61a2-931">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="e61a2-932">Dans `ConfigureAppConfiguration` :</span><span class="sxs-lookup"><span data-stu-id="e61a2-932">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="e61a2-933">La paire clé-valeur indiquée dans le tableau est chargée dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-933">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="e61a2-934">Clé</span><span class="sxs-lookup"><span data-stu-id="e61a2-934">Key</span></span>             | <span data-ttu-id="e61a2-935">Valeur</span><span class="sxs-lookup"><span data-stu-id="e61a2-935">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="e61a2-936">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="e61a2-936">array:entries:3</span></span> | <span data-ttu-id="e61a2-937">valeur3</span><span class="sxs-lookup"><span data-stu-id="e61a2-937">value3</span></span> |

<span data-ttu-id="e61a2-938">Si l’instance de classe `ArrayExample` est liée une fois que le Fournisseur de configuration JSON inclut l’entrée pour l’index &num;3, le tableau `ArrayExample.Entries` inclut la valeur.</span><span class="sxs-lookup"><span data-stu-id="e61a2-938">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="e61a2-939">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="e61a2-939">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="e61a2-940">Valeur `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="e61a2-940">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="e61a2-941">0</span><span class="sxs-lookup"><span data-stu-id="e61a2-941">0</span></span>                            | <span data-ttu-id="e61a2-942">value0</span><span class="sxs-lookup"><span data-stu-id="e61a2-942">value0</span></span>                       |
| <span data-ttu-id="e61a2-943">1</span><span class="sxs-lookup"><span data-stu-id="e61a2-943">1</span></span>                            | <span data-ttu-id="e61a2-944">valeur1</span><span class="sxs-lookup"><span data-stu-id="e61a2-944">value1</span></span>                       |
| <span data-ttu-id="e61a2-945">2</span><span class="sxs-lookup"><span data-stu-id="e61a2-945">2</span></span>                            | <span data-ttu-id="e61a2-946">valeur2</span><span class="sxs-lookup"><span data-stu-id="e61a2-946">value2</span></span>                       |
| <span data-ttu-id="e61a2-947">3</span><span class="sxs-lookup"><span data-stu-id="e61a2-947">3</span></span>                            | <span data-ttu-id="e61a2-948">valeur3</span><span class="sxs-lookup"><span data-stu-id="e61a2-948">value3</span></span>                       |
| <span data-ttu-id="e61a2-949">4</span><span class="sxs-lookup"><span data-stu-id="e61a2-949">4</span></span>                            | <span data-ttu-id="e61a2-950">value4</span><span class="sxs-lookup"><span data-stu-id="e61a2-950">value4</span></span>                       |
| <span data-ttu-id="e61a2-951">5</span><span class="sxs-lookup"><span data-stu-id="e61a2-951">5</span></span>                            | <span data-ttu-id="e61a2-952">value5</span><span class="sxs-lookup"><span data-stu-id="e61a2-952">value5</span></span>                       |

<span data-ttu-id="e61a2-953">**Traitement de tableau JSON**</span><span class="sxs-lookup"><span data-stu-id="e61a2-953">**JSON array processing**</span></span>

<span data-ttu-id="e61a2-954">Si un fichier JSON contient un tableau, les clés de configuration sont créés pour les éléments du tableau avec un index de section basé sur zéro.</span><span class="sxs-lookup"><span data-stu-id="e61a2-954">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="e61a2-955">Dans le fichier de configuration suivant, `subsection` est un tableau :</span><span class="sxs-lookup"><span data-stu-id="e61a2-955">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="e61a2-956">Le Fournisseur de configuration JSON lit les données de configuration dans les paires clé-valeur suivantes :</span><span class="sxs-lookup"><span data-stu-id="e61a2-956">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="e61a2-957">Clé</span><span class="sxs-lookup"><span data-stu-id="e61a2-957">Key</span></span>                     | <span data-ttu-id="e61a2-958">Valeur</span><span class="sxs-lookup"><span data-stu-id="e61a2-958">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="e61a2-959">json_array:key</span><span class="sxs-lookup"><span data-stu-id="e61a2-959">json_array:key</span></span>          | <span data-ttu-id="e61a2-960">valueA</span><span class="sxs-lookup"><span data-stu-id="e61a2-960">valueA</span></span> |
| <span data-ttu-id="e61a2-961">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="e61a2-961">json_array:subsection:0</span></span> | <span data-ttu-id="e61a2-962">valueB</span><span class="sxs-lookup"><span data-stu-id="e61a2-962">valueB</span></span> |
| <span data-ttu-id="e61a2-963">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="e61a2-963">json_array:subsection:1</span></span> | <span data-ttu-id="e61a2-964">valueC</span><span class="sxs-lookup"><span data-stu-id="e61a2-964">valueC</span></span> |
| <span data-ttu-id="e61a2-965">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="e61a2-965">json_array:subsection:2</span></span> | <span data-ttu-id="e61a2-966">valueD</span><span class="sxs-lookup"><span data-stu-id="e61a2-966">valueD</span></span> |

<span data-ttu-id="e61a2-967">Dans l’exemple d’application, la classe POCO suivante est disponible pour lier les paires clé-valeur de configuration :</span><span class="sxs-lookup"><span data-stu-id="e61a2-967">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="e61a2-968">Après la liaison, `JsonArrayExample.Key` contient la valeur `valueA`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-968">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="e61a2-969">Les valeurs de la sous-section sont stockées dans la propriété de tableau POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-969">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="e61a2-970">Index `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="e61a2-970">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="e61a2-971">Valeur `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="e61a2-971">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="e61a2-972">0</span><span class="sxs-lookup"><span data-stu-id="e61a2-972">0</span></span>                                   | <span data-ttu-id="e61a2-973">valueB</span><span class="sxs-lookup"><span data-stu-id="e61a2-973">valueB</span></span>                              |
| <span data-ttu-id="e61a2-974">1</span><span class="sxs-lookup"><span data-stu-id="e61a2-974">1</span></span>                                   | <span data-ttu-id="e61a2-975">valueC</span><span class="sxs-lookup"><span data-stu-id="e61a2-975">valueC</span></span>                              |
| <span data-ttu-id="e61a2-976">2</span><span class="sxs-lookup"><span data-stu-id="e61a2-976">2</span></span>                                   | <span data-ttu-id="e61a2-977">valueD</span><span class="sxs-lookup"><span data-stu-id="e61a2-977">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="e61a2-978">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="e61a2-978">Custom configuration provider</span></span>

<span data-ttu-id="e61a2-979">L’exemple d’application montre comment créer un fournisseur de configuration de base qui lit les paires clé-valeur de configuration à partir d’une base de données à l’aide [d’Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="e61a2-979">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="e61a2-980">Le fournisseur présente les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="e61a2-980">The provider has the following characteristics:</span></span>

* <span data-ttu-id="e61a2-981">La base de données en mémoire EF est utilisée à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-981">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="e61a2-982">Pour utiliser une base de données qui nécessite une chaîne de connexion, implémentez un autre `ConfigurationBuilder` pour fournir la chaîne de connexion à partir d’un autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="e61a2-982">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="e61a2-983">Le fournisseur lit une table de base de données dans la configuration au démarrage.</span><span class="sxs-lookup"><span data-stu-id="e61a2-983">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="e61a2-984">Le fournisseur n’interroge pas la base de données par clé.</span><span class="sxs-lookup"><span data-stu-id="e61a2-984">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="e61a2-985">Le rechargement en cas de changement n’est pas implémenté, par conséquent, la mise à jour la base de données après le démarrage de l’application n’a aucun effet sur la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-985">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="e61a2-986">Définissez une entité `EFConfigurationValue` pour le stockage des valeurs de configuration dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="e61a2-986">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="e61a2-987">*Models/EFConfigurationValue.cs* :</span><span class="sxs-lookup"><span data-stu-id="e61a2-987">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="e61a2-988">Ajoutez un `EFConfigurationContext` pour stocker les valeurs configurées et y accéder.</span><span class="sxs-lookup"><span data-stu-id="e61a2-988">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="e61a2-989">*EFConfigurationProvider/EFConfigurationContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="e61a2-989">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="e61a2-990">Créez une classe qui implémente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-990">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="e61a2-991">*EFConfigurationProvider/EFConfigurationSource.cs* :</span><span class="sxs-lookup"><span data-stu-id="e61a2-991">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="e61a2-992">Créez le fournisseur de configuration personnalisé en héritant de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-992">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="e61a2-993">Le fournisseur de configuration initialise la base de données quand elle est vide.</span><span class="sxs-lookup"><span data-stu-id="e61a2-993">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="e61a2-994">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span><span class="sxs-lookup"><span data-stu-id="e61a2-994">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="e61a2-995">Une méthode d’extension `AddEFConfiguration` permet d’ajouter la source de configuration à un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-995">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="e61a2-996">*Extensions/EntityFrameworkExtensions.cs* :</span><span class="sxs-lookup"><span data-stu-id="e61a2-996">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="e61a2-997">Le code suivant montre comment utiliser le `EFConfigurationProvider` personnalisé dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="e61a2-997">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="e61a2-998">Accéder à la configuration lors du démarrage</span><span class="sxs-lookup"><span data-stu-id="e61a2-998">Access configuration during startup</span></span>

<span data-ttu-id="e61a2-999">Injectez `IConfiguration` dans le constructeur `Startup` pour accéder aux valeurs de configuration dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e61a2-999">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e61a2-1000">Pour accéder à la configuration dans `Startup.Configure`, injectez `IConfiguration` directement dans la méthode ou utilisez l’instance à partir du constructeur :</span><span class="sxs-lookup"><span data-stu-id="e61a2-1000">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="e61a2-1001">Pour obtenir un exemple d’accès à la configuration à l’aide des méthodes pratiques de démarrage, consultez [Démarrage de l’application : méthodes pratiques](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="e61a2-1001">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="e61a2-1002">Configuration de l’accès dans une Razor page pages ou une vue MVC</span><span class="sxs-lookup"><span data-stu-id="e61a2-1002">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="e61a2-1003">Pour accéder aux paramètres de configuration dans une Razor page pages ou une vue MVC, ajoutez une [directive using](xref:mvc/views/razor#using) ([référence C# : directive using](/dotnet/csharp/language-reference/keywords/using-directive)) pour l' [ espace de nomsMicrosoft.Extensions.Configfiguration](xref:Microsoft.Extensions.Configuration) et injectez <xref:Microsoft.Extensions.Configuration.IConfiguration> dans la page ou la vue.</span><span class="sxs-lookup"><span data-stu-id="e61a2-1003">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="e61a2-1004">Dans une Razor page pages :</span><span class="sxs-lookup"><span data-stu-id="e61a2-1004">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="e61a2-1005">Dans une vue MVC :</span><span class="sxs-lookup"><span data-stu-id="e61a2-1005">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="e61a2-1006">Ajouter la configuration à partir d’un assembly externe</span><span class="sxs-lookup"><span data-stu-id="e61a2-1006">Add configuration from an external assembly</span></span>

<span data-ttu-id="e61a2-1007">Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application.</span><span class="sxs-lookup"><span data-stu-id="e61a2-1007">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="e61a2-1008">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e61a2-1008">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e61a2-1009">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e61a2-1009">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

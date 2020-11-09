---
title: Configuration dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser l’API de configuration pour configurer une application ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/configuration/index
ms.openlocfilehash: 9e744ec6d0f0dd72bded8284e98fd9ce53056b84
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057971"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="b1f97-103">Configuration dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b1f97-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="b1f97-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="b1f97-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b1f97-105">La configuration dans ASP.NET Core est effectuée à l’aide d’un ou de plusieurs [fournisseurs de configuration](#cp).</span><span class="sxs-lookup"><span data-stu-id="b1f97-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="b1f97-106">Les fournisseurs de configuration lisent les données de configuration des paires clé-valeur à l’aide d’une variété de sources de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="b1f97-107">Les fichiers de paramètres, tels que *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="b1f97-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="b1f97-108">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="b1f97-108">Environment variables</span></span>
* <span data-ttu-id="b1f97-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b1f97-109">Azure Key Vault</span></span>
* <span data-ttu-id="b1f97-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="b1f97-110">Azure App Configuration</span></span>
* <span data-ttu-id="b1f97-111">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="b1f97-111">Command-line arguments</span></span>
* <span data-ttu-id="b1f97-112">Fournisseurs personnalisés, installés ou créés</span><span class="sxs-lookup"><span data-stu-id="b1f97-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="b1f97-113">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="b1f97-113">Directory files</span></span>
* <span data-ttu-id="b1f97-114">Objets .NET en mémoire</span><span class="sxs-lookup"><span data-stu-id="b1f97-114">In-memory .NET objects</span></span>

<span data-ttu-id="b1f97-115">Cette rubrique fournit des informations sur la configuration dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b1f97-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="b1f97-116">Pour plus d’informations sur l’utilisation de la configuration dans les applications console, consultez [Configuration .net](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="b1f97-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="b1f97-117">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b1f97-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="b1f97-118">Configuration par défaut</span><span class="sxs-lookup"><span data-stu-id="b1f97-118">Default configuration</span></span>

<span data-ttu-id="b1f97-119">ASP.NET Core les applications Web créées avec [dotnet New](/dotnet/core/tools/dotnet-new) ou Visual Studio génèrent le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b1f97-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="b1f97-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fournit la configuration par défaut de l’application dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="b1f97-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="b1f97-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : ajoute un existant `IConfiguration` en tant que source.</span><span class="sxs-lookup"><span data-stu-id="b1f97-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="b1f97-122">Dans le cas de configuration par défaut, ajoute la configuration d' [hôte](#hvac) et la définit en tant que première source de la configuration de l' _application_ .</span><span class="sxs-lookup"><span data-stu-id="b1f97-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="b1f97-123">[appsettings.json](#appsettingsjson) utilisation du [fournisseur de configuration JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b1f97-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="b1f97-124">*appSettings.* `Environment` *. JSON* à l’aide du [fournisseur de configuration JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b1f97-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="b1f97-125">Par exemple, *appSettings* . ***Production \* \* _._json* et *appSettings* . \* \* \* développement** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="b1f97-125">For example, *appsettings* . ***Production\*\*_._json* and *appsettings* .\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="b1f97-126">[Secrets d’application](xref:security/app-secrets) lorsque l’application s’exécute dans l' `Development` environnement.</span><span class="sxs-lookup"><span data-stu-id="b1f97-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="b1f97-127">Variables d’environnement à l’aide du [fournisseur de configuration des variables d’environnement](#evcp).</span><span class="sxs-lookup"><span data-stu-id="b1f97-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="b1f97-128">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line).</span><span class="sxs-lookup"><span data-stu-id="b1f97-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="b1f97-129">Les fournisseurs de configuration ajoutés ultérieurement remplacent les paramètres de clé précédents.</span><span class="sxs-lookup"><span data-stu-id="b1f97-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="b1f97-130">Par exemple, si `MyKey` est défini à la fois dans *appsettings.json* et dans l’environnement, la valeur d’environnement est utilisée.</span><span class="sxs-lookup"><span data-stu-id="b1f97-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="b1f97-131">À l’aide des fournisseurs de configuration par défaut, le  [fournisseur de configuration de ligne de commande](#clcp) remplace tous les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="b1f97-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="b1f97-132">Pour plus d’informations sur `CreateDefaultBuilder` , consultez [paramètres par défaut du générateur](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="b1f97-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="b1f97-133">Le code suivant affiche les fournisseurs de configuration activés dans l’ordre dans lequel ils ont été ajoutés :</span><span class="sxs-lookup"><span data-stu-id="b1f97-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="b1f97-134">Prenons le *appsettings.json* fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="b1f97-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="b1f97-135">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="b1f97-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="b1f97-136">La configuration par défaut <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="b1f97-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="b1f97-137">*appSettings.* `Environment` *. JSON* : par exemple, *appSettings* . **Les fichiers de *production \* \* _._json* et *appSettings* . \* \* \* Development** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="b1f97-137">*appsettings.*`Environment`*.json* : For example, the *appsettings* . ***Production\*\*_._json* and *appsettings* .\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="b1f97-138">La version de l’environnement du fichier est chargée à partir de [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="b1f97-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="b1f97-139">Pour plus d'informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b1f97-140">*appSettings* . `Environment` . les valeurs *JSON* remplacent les clés dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-140">*appsettings* .`Environment`. *json* values override keys in *appsettings.json* .</span></span> <span data-ttu-id="b1f97-141">Par exemple, par défaut :</span><span class="sxs-lookup"><span data-stu-id="b1f97-141">For example, by default:</span></span>

* <span data-ttu-id="b1f97-142">Dans le développement, *appSettings* . \* **Development** _._json \* configuration remplace les valeurs trouvées dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-142">In development, *appsettings* .\* **Development** _._json\* configuration overwrites values found in *appsettings.json* .</span></span>
* <span data-ttu-id="b1f97-143">En production, *appSettings* . \* **production** _._json \* configuration remplace les valeurs trouvées dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-143">In production, *appsettings* .\* **Production** _._json\* configuration overwrites values found in *appsettings.json* .</span></span> <span data-ttu-id="b1f97-144">Par exemple, lors du déploiement de l’application sur Azure.</span><span class="sxs-lookup"><span data-stu-id="b1f97-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="b1f97-145">Lier des données de configuration hiérarchiques à l’aide du modèle options</span><span class="sxs-lookup"><span data-stu-id="b1f97-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="b1f97-146">À l’aide de la configuration [par défaut](#default) , de *appsettings.json* et de *appSettings.* `Environment` les fichiers *. JSON* sont activés avec [reloadOnChange : true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="b1f97-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="b1f97-147">Modifications apportées *appsettings.json* aux *appSettings et.* `Environment` fichier *. JSON* \* **après** _ le démarrage de l’application est lu par le [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="b1f97-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file \* **after** _ the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="b1f97-148">Pour plus d’informations sur l’ajout de fichiers de configuration JSON supplémentaires, consultez [fournisseur de configuration JSON](#jcp) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="b1f97-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="b1f97-149">Combinaison de la collection de services</span><span class="sxs-lookup"><span data-stu-id="b1f97-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="b1f97-150">Responsable de la sécurité et du secret</span><span class="sxs-lookup"><span data-stu-id="b1f97-150">Security and secret manager</span></span>

<span data-ttu-id="b1f97-151">Instructions relatives aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-151">Configuration data guidelines:</span></span>

<span data-ttu-id="b1f97-152">_ Ne stockez jamais les mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte brut.</span><span class="sxs-lookup"><span data-stu-id="b1f97-152">_ Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="b1f97-153">Le [Gestionnaire de secret](xref:security/app-secrets) peut être utilisé pour stocker les secrets en développement.</span><span class="sxs-lookup"><span data-stu-id="b1f97-153">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="b1f97-154">N’utilisez aucun secret de production dans les environnements de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="b1f97-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="b1f97-155">Spécifiez les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans un référentiel de code source.</span><span class="sxs-lookup"><span data-stu-id="b1f97-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="b1f97-156">Par [défaut](#default), le [Gestionnaire de secret](xref:security/app-secrets) lit les paramètres de configuration après *appsettings.json* et *appSettings.* `Environment` *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-156">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json* .</span></span>

<span data-ttu-id="b1f97-157">Pour plus d’informations sur le stockage des mots de passe ou d’autres données sensibles :</span><span class="sxs-lookup"><span data-stu-id="b1f97-157">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="b1f97-158"><xref:security/app-secrets>: Fournit des conseils sur l’utilisation de variables d’environnement pour stocker des données sensibles.</span><span class="sxs-lookup"><span data-stu-id="b1f97-158"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="b1f97-159">Le gestionnaire de secret utilise le [fournisseur de configuration de fichiers](#fcp) pour stocker les secrets de l’utilisateur dans un fichier JSON sur le système local.</span><span class="sxs-lookup"><span data-stu-id="b1f97-159">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="b1f97-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stocke en toute sécurité des secrets d’application pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b1f97-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="b1f97-161">Pour plus d'informations, consultez <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-161">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="b1f97-162">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="b1f97-162">Environment variables</span></span>

<span data-ttu-id="b1f97-163">À l’aide de la configuration [par défaut](#default) , le <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> charge la configuration à partir des paires clé-valeur de variable d’environnement après la lecture *appsettings.json* , *appSettings.* `Environment` *. JSON* et le [Gestionnaire de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="b1f97-163">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json* , *appsettings.*`Environment`*.json* , and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="b1f97-164">Par conséquent, les valeurs de clés lues à partir de l’environnement remplacent les valeurs lues à partir de *appsettings.json* *appSettings.* `Environment` *. JSON* et le gestionnaire de secret.</span><span class="sxs-lookup"><span data-stu-id="b1f97-164">Therefore, key values read from the environment override values read from *appsettings.json* , *appsettings.*`Environment`*.json* , and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="b1f97-165">Les `set` commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b1f97-165">The following `set` commands:</span></span>

* <span data-ttu-id="b1f97-166">Définissez les clés et les valeurs d’environnement de l' [exemple précédent](#appsettingsjson) sur Windows.</span><span class="sxs-lookup"><span data-stu-id="b1f97-166">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="b1f97-167">Testez les paramètres lors de l’utilisation de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="b1f97-167">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="b1f97-168">La `dotnet run` commande doit être exécutée dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="b1f97-168">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="b1f97-169">Paramètres d’environnement précédents :</span><span class="sxs-lookup"><span data-stu-id="b1f97-169">The preceding environment settings:</span></span>

* <span data-ttu-id="b1f97-170">Sont uniquement définies dans les processus lancés à partir de la fenêtre de commande dans laquelle ils ont été définis.</span><span class="sxs-lookup"><span data-stu-id="b1f97-170">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="b1f97-171">Ne seront pas lues par les navigateurs lancés avec Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b1f97-171">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="b1f97-172">Les commandes [setx](/windows-server/administration/windows-commands/setx) suivantes peuvent être utilisées pour définir les clés et les valeurs d’environnement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="b1f97-172">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="b1f97-173">Contrairement à `set` , `setx` les paramètres sont conservés.</span><span class="sxs-lookup"><span data-stu-id="b1f97-173">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="b1f97-174">`/M` définit la variable dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="b1f97-174">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="b1f97-175">Si le `/M` commutateur n’est pas utilisé, une variable d’environnement utilisateur est définie.</span><span class="sxs-lookup"><span data-stu-id="b1f97-175">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="b1f97-176">Pour vérifier que les commandes précédentes remplacent *appsettings.json* et *appSettings.* `Environment` *. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-176">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json* :</span></span>

* <span data-ttu-id="b1f97-177">Avec Visual Studio : quittez et redémarrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b1f97-177">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="b1f97-178">Avec l’interface CLI : démarrez une nouvelle fenêtre de commande et entrez `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="b1f97-178">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="b1f97-179">Appelez <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> avec une chaîne pour spécifier un préfixe pour les variables d’environnement :</span><span class="sxs-lookup"><span data-stu-id="b1f97-179">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="b1f97-180">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="b1f97-180">In the preceding code:</span></span>

* <span data-ttu-id="b1f97-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` est ajouté après les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="b1f97-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="b1f97-182">Pour obtenir un exemple de classement des fournisseurs de configuration, consultez [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="b1f97-182">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="b1f97-183">Les variables d’environnement définies avec le `MyCustomPrefix_` préfixe remplacent les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="b1f97-183">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="b1f97-184">Cela comprend les variables d’environnement sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="b1f97-184">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="b1f97-185">Le préfixe est supprimé lorsque les paires clé-valeur de configuration sont lues.</span><span class="sxs-lookup"><span data-stu-id="b1f97-185">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="b1f97-186">Les commandes suivantes testent le préfixe personnalisé :</span><span class="sxs-lookup"><span data-stu-id="b1f97-186">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="b1f97-187">La [configuration par défaut](#default) charge les variables d’environnement et les arguments de ligne de commande préfixé avec `DOTNET_` et `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="b1f97-187">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="b1f97-188">Les `DOTNET_` `ASPNETCORE_` préfixes et sont utilisés par ASP.net Core pour la configuration de l' [hôte et](xref:fundamentals/host/generic-host#host-configuration)de l’application, mais pas pour la configuration de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="b1f97-188">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="b1f97-189">Pour plus d’informations sur la configuration de l’hôte et de l’application, consultez [hôte générique .net](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="b1f97-189">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="b1f97-190">Dans [Azure App service](https://azure.microsoft.com/services/app-service/), sélectionnez **nouveau paramètre d’application** dans la page **paramètres > configuration** .</span><span class="sxs-lookup"><span data-stu-id="b1f97-190">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="b1f97-191">Azure App Service paramètres de l’application sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b1f97-191">Azure App Service application settings are:</span></span>

* <span data-ttu-id="b1f97-192">Chiffré au repos et transmis sur un canal chiffré.</span><span class="sxs-lookup"><span data-stu-id="b1f97-192">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="b1f97-193">Exposés en tant que variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="b1f97-193">Exposed as environment variables.</span></span>

<span data-ttu-id="b1f97-194">Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="b1f97-194">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="b1f97-195">Consultez [préfixes de chaîne de connexion](#constr) pour plus d’informations sur les chaînes de connexion de base de données Azure.</span><span class="sxs-lookup"><span data-stu-id="b1f97-195">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="b1f97-196">Variables d’environnement définies dans launchSettings.jssur</span><span class="sxs-lookup"><span data-stu-id="b1f97-196">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="b1f97-197">Les variables d’environnement définies dans *launchSettings.jslors de* la substitution de celles définies dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="b1f97-197">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="b1f97-198">Ligne de commande</span><span class="sxs-lookup"><span data-stu-id="b1f97-198">Command-line</span></span>

<span data-ttu-id="b1f97-199">À l’aide de la configuration [par défaut](#default) , le <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> charge la configuration à partir de paires clé-valeur d’argument de ligne de commande après les sources de configuration suivantes :</span><span class="sxs-lookup"><span data-stu-id="b1f97-199">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="b1f97-200">*appsettings.json* et *appSettings* . `Environment` . fichiers *JSON* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-200">*appsettings.json* and *appsettings* .`Environment`. *json* files.</span></span>
* <span data-ttu-id="b1f97-201">[Secrets d’application (gestionnaire de secret)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="b1f97-201">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="b1f97-202">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="b1f97-202">Environment variables.</span></span>

<span data-ttu-id="b1f97-203">Par [défaut](#default), les valeurs de configuration définies sur la ligne de commande remplacent les valeurs de configuration définies avec tous les autres fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-203">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="b1f97-204">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="b1f97-204">Command-line arguments</span></span>

<span data-ttu-id="b1f97-205">La commande suivante définit des clés et des valeurs à l’aide de `=` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-205">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="b1f97-206">La commande suivante définit des clés et des valeurs à l’aide de `/` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-206">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="b1f97-207">La commande suivante définit des clés et des valeurs à l’aide de `--` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-207">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="b1f97-208">Valeur de la clé :</span><span class="sxs-lookup"><span data-stu-id="b1f97-208">The key value:</span></span>

* <span data-ttu-id="b1f97-209">Doit suivre `=` ou la clé doit avoir un préfixe `--` ou `/` lorsque la valeur suit un espace.</span><span class="sxs-lookup"><span data-stu-id="b1f97-209">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="b1f97-210">N’est pas obligatoire si `=` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="b1f97-210">Isn't required if `=` is used.</span></span> <span data-ttu-id="b1f97-211">Par exemple : `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-211">For example, `MySetting=`.</span></span>

<span data-ttu-id="b1f97-212">Dans la même commande, ne mélangez pas les paires clé-valeur d’argument de ligne de commande qui utilisent `=` des paires clé-valeur utilisant un espace.</span><span class="sxs-lookup"><span data-stu-id="b1f97-212">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="b1f97-213">Correspondances de commutateur</span><span class="sxs-lookup"><span data-stu-id="b1f97-213">Switch mappings</span></span>

<span data-ttu-id="b1f97-214">Les mappages de commutateur autorisent la logique de remplacement de nom de **clé** .</span><span class="sxs-lookup"><span data-stu-id="b1f97-214">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="b1f97-215">Fournissez un dictionnaire de remplacements de commutateur dans la <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> méthode.</span><span class="sxs-lookup"><span data-stu-id="b1f97-215">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="b1f97-216">Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="b1f97-216">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="b1f97-217">Si la clé de ligne de commande est trouvée dans le dictionnaire, la valeur du dictionnaire est retournée pour définir la paire clé-valeur dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-217">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="b1f97-218">Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).</span><span class="sxs-lookup"><span data-stu-id="b1f97-218">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="b1f97-219">Règles des clés du dictionnaire de correspondances de commutateur :</span><span class="sxs-lookup"><span data-stu-id="b1f97-219">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="b1f97-220">Les commutateurs doivent commencer par `-` ou `--` .</span><span class="sxs-lookup"><span data-stu-id="b1f97-220">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="b1f97-221">Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.</span><span class="sxs-lookup"><span data-stu-id="b1f97-221">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="b1f97-222">Pour utiliser un dictionnaire de mappages de commutateur, transmettez-le à l’appel à `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-222">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="b1f97-223">Le code suivant montre les valeurs de clé pour les clés remplacées :</span><span class="sxs-lookup"><span data-stu-id="b1f97-223">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="b1f97-224">La commande suivante fonctionne pour tester le remplacement de la clé :</span><span class="sxs-lookup"><span data-stu-id="b1f97-224">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="b1f97-225">Pour les applications qui utilisent des mappages de commutateurs, l’appel à `CreateDefaultBuilder` ne doit pas passer d’arguments.</span><span class="sxs-lookup"><span data-stu-id="b1f97-225">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="b1f97-226">L' `CreateDefaultBuilder` appel de la méthode `AddCommandLine` n’inclut pas de commutateurs mappés, et il n’existe aucun moyen de passer le dictionnaire de mappage de commutateur à `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b1f97-226">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b1f97-227">La solution ne consiste pas à passer les arguments à `CreateDefaultBuilder` , mais à autoriser la méthode de la `ConfigurationBuilder` méthode `AddCommandLine` à traiter à la fois les arguments et le dictionnaire de mappage de commutateur.</span><span class="sxs-lookup"><span data-stu-id="b1f97-227">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="b1f97-228">Données de configuration hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="b1f97-228">Hierarchical configuration data</span></span>

<span data-ttu-id="b1f97-229">L’API de configuration lit les données de configuration hiérarchiques en aplatit les données hiérarchiques à l’aide d’un délimiteur dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-229">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="b1f97-230">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le  *appsettings.json* fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="b1f97-230">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="b1f97-231">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-231">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="b1f97-232">La meilleure façon de lire des données de configuration hiérarchiques consiste à utiliser le modèle d’options.</span><span class="sxs-lookup"><span data-stu-id="b1f97-232">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="b1f97-233">Pour plus d’informations, consultez [lier des données de configuration hiérarchiques](#optpat) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="b1f97-233">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="b1f97-234">Les méthodes <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> et <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sont disponibles pour isoler les sections et les enfants d’une section dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="b1f97-235">Ces méthodes sont décrites plus loin dans [GetSection, GetChildren et Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="b1f97-235">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="b1f97-236">Clés et valeurs de configuration</span><span class="sxs-lookup"><span data-stu-id="b1f97-236">Configuration keys and values</span></span>

<span data-ttu-id="b1f97-237">Clés de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-237">Configuration keys:</span></span>

* <span data-ttu-id="b1f97-238">Ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="b1f97-238">Are case-insensitive.</span></span> <span data-ttu-id="b1f97-239">Par exemple, `ConnectionString` et `connectionstring` sont traités en tant que clés équivalentes.</span><span class="sxs-lookup"><span data-stu-id="b1f97-239">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="b1f97-240">Si une clé et une valeur sont définies dans plusieurs fournisseurs de configuration, la valeur du dernier fournisseur ajouté est utilisée.</span><span class="sxs-lookup"><span data-stu-id="b1f97-240">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="b1f97-241">Pour plus d’informations, consultez [configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="b1f97-241">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="b1f97-242">Clés hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="b1f97-242">Hierarchical keys</span></span>
  * <span data-ttu-id="b1f97-243">Dans l’API Configuration, un séparateur sous forme de signe deux-points (`:`) fonctionne sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="b1f97-243">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="b1f97-244">Dans les variables d’environnement, un séparateur sous forme de signe deux-points peut ne pas fonctionner sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="b1f97-244">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="b1f97-245">Un trait de soulignement double, `__` , est pris en charge par toutes les plateformes et est automatiquement converti en deux-points `:` .</span><span class="sxs-lookup"><span data-stu-id="b1f97-245">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="b1f97-246">Dans Azure Key Vault, les clés hiérarchiques utilisent `--` comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="b1f97-246">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="b1f97-247">Le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) remplace automatiquement `--` par un `:` lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-247">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="b1f97-248"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-248">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b1f97-249">La liaison de tableau est décrite dans la section [Lier un tableau à une classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="b1f97-249">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="b1f97-250">Valeurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-250">Configuration values:</span></span>

* <span data-ttu-id="b1f97-251">Sont des chaînes.</span><span class="sxs-lookup"><span data-stu-id="b1f97-251">Are strings.</span></span>
* <span data-ttu-id="b1f97-252">Les valeurs NULL ne peuvent pas être stockées dans la configuration ou liées à des objets.</span><span class="sxs-lookup"><span data-stu-id="b1f97-252">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="b1f97-253">Fournisseurs de configuration</span><span class="sxs-lookup"><span data-stu-id="b1f97-253">Configuration providers</span></span>

<span data-ttu-id="b1f97-254">Le tableau suivant présente les fournisseurs de configuration disponibles pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b1f97-254">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="b1f97-255">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="b1f97-255">Provider</span></span> | <span data-ttu-id="b1f97-256">Fournit la configuration à partir de</span><span class="sxs-lookup"><span data-stu-id="b1f97-256">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="b1f97-257">Fournisseur de configuration Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b1f97-257">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="b1f97-258">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b1f97-258">Azure Key Vault</span></span> |
| [<span data-ttu-id="b1f97-259">Fournisseur de configuration Azure App</span><span class="sxs-lookup"><span data-stu-id="b1f97-259">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="b1f97-260">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="b1f97-260">Azure App Configuration</span></span> |
| [<span data-ttu-id="b1f97-261">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="b1f97-261">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="b1f97-262">Paramètres de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="b1f97-262">Command-line parameters</span></span> |
| [<span data-ttu-id="b1f97-263">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="b1f97-263">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="b1f97-264">Source personnalisée</span><span class="sxs-lookup"><span data-stu-id="b1f97-264">Custom source</span></span> |
| [<span data-ttu-id="b1f97-265">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="b1f97-265">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="b1f97-266">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="b1f97-266">Environment variables</span></span> |
| [<span data-ttu-id="b1f97-267">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="b1f97-267">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="b1f97-268">Fichiers INI, JSON et XML</span><span class="sxs-lookup"><span data-stu-id="b1f97-268">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="b1f97-269">Fournisseur de configuration de clé par fichier</span><span class="sxs-lookup"><span data-stu-id="b1f97-269">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="b1f97-270">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="b1f97-270">Directory files</span></span> |
| [<span data-ttu-id="b1f97-271">Fournisseur de configuration de la mémoire</span><span class="sxs-lookup"><span data-stu-id="b1f97-271">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="b1f97-272">Collections en mémoire</span><span class="sxs-lookup"><span data-stu-id="b1f97-272">In-memory collections</span></span> |
| [<span data-ttu-id="b1f97-273">Gestionnaire de secret</span><span class="sxs-lookup"><span data-stu-id="b1f97-273">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="b1f97-274">Fichier dans le répertoire de profil utilisateur</span><span class="sxs-lookup"><span data-stu-id="b1f97-274">File in the user profile directory</span></span> |

<span data-ttu-id="b1f97-275">Les sources de configuration sont lues dans l’ordre dans lequel leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="b1f97-275">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="b1f97-276">Commandez des fournisseurs de configuration dans le code pour répondre aux priorités des sources de configuration sous-jacentes requises par l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-276">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="b1f97-277">Une séquence type des fournisseurs de configuration est la suivante :</span><span class="sxs-lookup"><span data-stu-id="b1f97-277">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="b1f97-278">*appSettings* . `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="b1f97-278">*appsettings* .`Environment`. *json*</span></span>
1. [<span data-ttu-id="b1f97-279">Gestionnaire de secret</span><span class="sxs-lookup"><span data-stu-id="b1f97-279">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="b1f97-280">Variables d’environnement à l’aide du [fournisseur de configuration des variables d’environnement](#evcp).</span><span class="sxs-lookup"><span data-stu-id="b1f97-280">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="b1f97-281">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b1f97-281">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="b1f97-282">Une pratique courante consiste à ajouter le dernier fournisseur de configuration de ligne de commande dans une série de fournisseurs pour permettre aux arguments de ligne de commande de remplacer la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="b1f97-282">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="b1f97-283">La séquence de fournisseurs précédente est utilisée dans la [configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="b1f97-283">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="b1f97-284">Préfixes des chaînes de connexion</span><span class="sxs-lookup"><span data-stu-id="b1f97-284">Connection string prefixes</span></span>

<span data-ttu-id="b1f97-285">L’API de configuration a des règles de traitement spéciales pour quatre variables d’environnement de chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="b1f97-285">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="b1f97-286">Ces chaînes de connexion sont impliquées dans la configuration des chaînes de connexion Azure pour l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-286">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="b1f97-287">Les variables d’environnement avec les préfixes indiqués dans le tableau sont chargées dans l’application avec la [configuration par défaut](#default) ou lorsqu’aucun préfixe n’est fourni à `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="b1f97-287">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="b1f97-288">Préfixe de la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="b1f97-288">Connection string prefix</span></span> | <span data-ttu-id="b1f97-289">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="b1f97-289">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="b1f97-290">Fournisseur personnalisé</span><span class="sxs-lookup"><span data-stu-id="b1f97-290">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="b1f97-291">MySQL</span><span class="sxs-lookup"><span data-stu-id="b1f97-291">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="b1f97-292">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="b1f97-292">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="b1f97-293">SQL Server</span><span class="sxs-lookup"><span data-stu-id="b1f97-293">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="b1f97-294">Quand une variable d’environnement est découverte et chargée dans la configuration avec l’un des quatre préfixes indiqués dans le tableau :</span><span class="sxs-lookup"><span data-stu-id="b1f97-294">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="b1f97-295">La clé de configuration est créée en supprimant le préfixe de la variable d’environnement et en ajoutant une section de clé de configuration (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="b1f97-295">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="b1f97-296">Une nouvelle paire clé-valeur de configuration est créée qui représente le fournisseur de connexion de base de données (à l’exception de `CUSTOMCONNSTR_`, qui ne possède aucun fournisseur indiqué).</span><span class="sxs-lookup"><span data-stu-id="b1f97-296">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="b1f97-297">Clé de variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="b1f97-297">Environment variable key</span></span> | <span data-ttu-id="b1f97-298">Clé de configuration convertie</span><span class="sxs-lookup"><span data-stu-id="b1f97-298">Converted configuration key</span></span> | <span data-ttu-id="b1f97-299">Entrée de configuration de fournisseur</span><span class="sxs-lookup"><span data-stu-id="b1f97-299">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b1f97-300">Entrée de configuration non créée.</span><span class="sxs-lookup"><span data-stu-id="b1f97-300">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b1f97-301">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-301">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b1f97-302">Valeur: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="b1f97-302">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b1f97-303">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-303">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b1f97-304">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b1f97-304">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b1f97-305">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-305">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b1f97-306">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b1f97-306">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="b1f97-307">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="b1f97-307">File configuration provider</span></span>

<span data-ttu-id="b1f97-308"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> est la classe de base pour charger la configuration à partir du système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="b1f97-308"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="b1f97-309">Les fournisseurs de configuration suivants dérivent de `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-309">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="b1f97-310">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="b1f97-310">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="b1f97-311">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="b1f97-311">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="b1f97-312">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="b1f97-312">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="b1f97-313">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="b1f97-313">INI configuration provider</span></span>

<span data-ttu-id="b1f97-314"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier INI lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b1f97-314">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="b1f97-315">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-315">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="b1f97-316">Dans le code précédent, les paramètres des *MyIniConfig.ini* et  *MyIniConfig* . `Environment` les fichiers *ini* sont remplacés par les paramètres dans le :</span><span class="sxs-lookup"><span data-stu-id="b1f97-316">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig* .`Environment`. *ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="b1f97-317">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="b1f97-317">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="b1f97-318">[Fournisseur de configuration de ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="b1f97-318">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="b1f97-319">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyIniConfig.ini* suivant :</span><span class="sxs-lookup"><span data-stu-id="b1f97-319">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="b1f97-320">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="b1f97-320">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="b1f97-321">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="b1f97-321">JSON configuration provider</span></span>

<span data-ttu-id="b1f97-322">Le <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge la configuration à partir de paires clé-valeur de fichier JSON.</span><span class="sxs-lookup"><span data-stu-id="b1f97-322">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="b1f97-323">Les surcharges peuvent spécifier :</span><span class="sxs-lookup"><span data-stu-id="b1f97-323">Overloads can specify:</span></span>

* <span data-ttu-id="b1f97-324">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="b1f97-324">Whether the file is optional.</span></span>
* <span data-ttu-id="b1f97-325">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="b1f97-325">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="b1f97-326">Considérez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b1f97-326">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="b1f97-327">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="b1f97-327">The preceding code:</span></span>

* <span data-ttu-id="b1f97-328">Configure le fournisseur de configuration JSON pour charger le *MyConfig.jssur* le fichier avec les options suivantes :</span><span class="sxs-lookup"><span data-stu-id="b1f97-328">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="b1f97-329">`optional: true`: Le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="b1f97-329">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="b1f97-330">`reloadOnChange: true` : Le fichier est rechargé lorsque des modifications sont enregistrées.</span><span class="sxs-lookup"><span data-stu-id="b1f97-330">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="b1f97-331">Lit les [fournisseurs de configuration par défaut](#default) avant l' *MyConfig.jssur* le fichier.</span><span class="sxs-lookup"><span data-stu-id="b1f97-331">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="b1f97-332">Les paramètres dans le *MyConfig.js* paramètre de remplacement de fichier dans les fournisseurs de configuration par défaut, y compris le [fournisseur de configuration des variables d’environnement](#evcp) et le fournisseur de configuration de ligne de [commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="b1f97-332">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="b1f97-333">En règle générale, vous **ne souhaitez pas** que les valeurs de substitution de fichier JSON personnalisées soient définies dans le fournisseur de configuration des [variables d’environnement](#evcp) et dans le fournisseur de configuration de [ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="b1f97-333">You typically \* **don't** _ want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="b1f97-334">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-334">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="b1f97-335">Dans le code précédent, les paramètres de la _MyConfig.jssur \* et  *MyConfig* . `Environment` . fichiers *JSON* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-335">In the preceding code, settings in the _MyConfig.json\* and  *MyConfig* .`Environment`. *json* files:</span></span>

* <span data-ttu-id="b1f97-336">Substituez les paramètres dans *appsettings.json* et *appSettings* . `Environment` fichiers *JSON* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-336">Override settings in the *appsettings.json* and *appsettings* .`Environment`. *json* files.</span></span>
* <span data-ttu-id="b1f97-337">Sont remplacées par les paramètres dans le [fournisseur de configuration des variables d’environnement](#evcp) et le fournisseur de configuration de ligne de [commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="b1f97-337">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="b1f97-338">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient les  *MyConfig.jssuivantes sur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="b1f97-338">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="b1f97-339">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="b1f97-339">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="b1f97-340">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="b1f97-340">XML configuration provider</span></span>

<span data-ttu-id="b1f97-341"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier XML lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b1f97-341">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="b1f97-342">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-342">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="b1f97-343">Dans le code précédent, les paramètres des *MyXMLFile.xml* et  *MyXMLFile* . `Environment` les fichiers *XML* sont remplacés par les paramètres dans le :</span><span class="sxs-lookup"><span data-stu-id="b1f97-343">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile* .`Environment`. *xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="b1f97-344">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="b1f97-344">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="b1f97-345">[Fournisseur de configuration de ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="b1f97-345">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="b1f97-346">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyXMLFile.xml* suivant :</span><span class="sxs-lookup"><span data-stu-id="b1f97-346">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="b1f97-347">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="b1f97-347">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="b1f97-348">Les éléments répétitifs qui utilisent le même nom d’élément fonctionnent si l’attribut `name` est utilisé pour distinguer les éléments :</span><span class="sxs-lookup"><span data-stu-id="b1f97-348">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="b1f97-349">Le code suivant lit le fichier de configuration précédent et affiche les clés et les valeurs :</span><span class="sxs-lookup"><span data-stu-id="b1f97-349">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="b1f97-350">Les attributs peuvent être utilisés pour fournir des valeurs :</span><span class="sxs-lookup"><span data-stu-id="b1f97-350">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="b1f97-351">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-351">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b1f97-352">key:attribute</span><span class="sxs-lookup"><span data-stu-id="b1f97-352">key:attribute</span></span>
* <span data-ttu-id="b1f97-353">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="b1f97-353">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="b1f97-354">Fournisseur de configuration de clé par fichier</span><span class="sxs-lookup"><span data-stu-id="b1f97-354">Key-per-file configuration provider</span></span>

<span data-ttu-id="b1f97-355">Le <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> utilise les fichiers d’un répertoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-355">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="b1f97-356">La clé est le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="b1f97-356">The key is the file name.</span></span> <span data-ttu-id="b1f97-357">La valeur contient le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="b1f97-357">The value contains the file's contents.</span></span> <span data-ttu-id="b1f97-358">Le fournisseur de configuration par fichier clé est utilisé dans les scénarios d’hébergement de l’ancrage.</span><span class="sxs-lookup"><span data-stu-id="b1f97-358">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="b1f97-359">Pour activer la configuration clé par fichier, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-359">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="b1f97-360">Le `directoryPath` aux fichiers doit être un chemin d’accès absolu.</span><span class="sxs-lookup"><span data-stu-id="b1f97-360">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="b1f97-361">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="b1f97-361">Overloads permit specifying:</span></span>

* <span data-ttu-id="b1f97-362">Un délégué `Action<KeyPerFileConfigurationSource>` qui configure la source.</span><span class="sxs-lookup"><span data-stu-id="b1f97-362">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="b1f97-363">Si le répertoire est facultatif et le chemin d’accès au répertoire.</span><span class="sxs-lookup"><span data-stu-id="b1f97-363">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="b1f97-364">Le double trait de soulignement (`__`) est utilisé comme un délimiteur de clé de configuration dans les noms de fichiers.</span><span class="sxs-lookup"><span data-stu-id="b1f97-364">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="b1f97-365">Par exemple, le nom de fichier `Logging__LogLevel__System` génère la clé de configuration `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-365">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="b1f97-366">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="b1f97-366">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="b1f97-367">Fournisseur de configuration de la mémoire</span><span class="sxs-lookup"><span data-stu-id="b1f97-367">Memory configuration provider</span></span>

<span data-ttu-id="b1f97-368">Le <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> utilise une collection en mémoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-368">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="b1f97-369">Le code suivant ajoute une collection de mémoire au système de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-369">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="b1f97-370">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche les paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="b1f97-370">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="b1f97-371">Dans le code précédent, `config.AddInMemoryCollection(Dict)` est ajouté après les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="b1f97-371">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="b1f97-372">Pour obtenir un exemple de classement des fournisseurs de configuration, consultez [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="b1f97-372">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="b1f97-373">Pour un autre exemple, consultez [lier un tableau](#boa) à l’aide de `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="b1f97-373">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="b1f97-374">GetValue</span><span class="sxs-lookup"><span data-stu-id="b1f97-374">GetValue</span></span>

<span data-ttu-id="b1f97-375">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrait une valeur unique de la configuration avec une clé spécifiée et la convertit en type spécifié :</span><span class="sxs-lookup"><span data-stu-id="b1f97-375">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="b1f97-376">Dans le code précédent, si est `NumberKey` introuvable dans la configuration, la valeur par défaut de `99` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="b1f97-376">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="b1f97-377">GetSection, GetChildren et Exists</span><span class="sxs-lookup"><span data-stu-id="b1f97-377">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="b1f97-378">Pour les exemples qui suivent, prenez en compte les *MySubsection.jssuivantes sur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="b1f97-378">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="b1f97-379">Le code suivant ajoute *MySubsection.js* aux fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-379">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="b1f97-380">GetSection</span><span class="sxs-lookup"><span data-stu-id="b1f97-380">GetSection</span></span>

<span data-ttu-id="b1f97-381">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) retourne une sous-section de configuration avec la clé de sous-section spécifiée.</span><span class="sxs-lookup"><span data-stu-id="b1f97-381">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="b1f97-382">Le code suivant retourne des valeurs pour `section1` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-382">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="b1f97-383">Le code suivant retourne des valeurs pour `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-383">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="b1f97-384">`GetSection` ne retourne jamais `null`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-384">`GetSection` never returns `null`.</span></span> <span data-ttu-id="b1f97-385">Si aucune section correspondante n’est trouvée, une valeur `IConfigurationSection` vide est retournée.</span><span class="sxs-lookup"><span data-stu-id="b1f97-385">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="b1f97-386">Quand `GetSection` retourne une section correspondante, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="b1f97-386">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="b1f97-387"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> et <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> sont retournés quand la section existe.</span><span class="sxs-lookup"><span data-stu-id="b1f97-387">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="b1f97-388">GetChildren et EXISTS</span><span class="sxs-lookup"><span data-stu-id="b1f97-388">GetChildren and Exists</span></span>

<span data-ttu-id="b1f97-389">Le code suivant appelle [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) et retourne des valeurs pour `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-389">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="b1f97-390">Le code précédent appelle [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pour vérifier l’existence de la section :</span><span class="sxs-lookup"><span data-stu-id="b1f97-390">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="b1f97-391">Lier un tableau</span><span class="sxs-lookup"><span data-stu-id="b1f97-391">Bind an array</span></span>

<span data-ttu-id="b1f97-392">[ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) prend en charge les tableaux de liaison aux objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-392">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b1f97-393">Tout format de tableau qui expose un segment de clé numérique est capable d’effectuer une liaison de tableau à un tableau de classes [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="b1f97-393">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="b1f97-394">Prenez *MyArray.js* à partir de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="b1f97-394">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="b1f97-395">Le code suivant ajoute *MyArray.js* aux fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-395">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="b1f97-396">Le code suivant lit la configuration et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="b1f97-396">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="b1f97-397">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="b1f97-397">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="b1f97-398">Dans la sortie précédente, l’index 3 a la valeur `value40` , ce qui correspond à `"4": "value40",` dans *MyArray.jssur* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-398">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json* .</span></span> <span data-ttu-id="b1f97-399">Les index de tableau liés sont continus et non liés à l’index de clé de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-399">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="b1f97-400">Le Binder de configuration n’est pas en capacité à lier des valeurs null ou à créer des entrées NULL dans des objets liés</span><span class="sxs-lookup"><span data-stu-id="b1f97-400">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="b1f97-401">Le code suivant charge la `array:entries` configuration avec la <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="b1f97-401">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="b1f97-402">Le code suivant lit la configuration dans `arrayDict` `Dictionary` et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="b1f97-402">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="b1f97-403">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="b1f97-403">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="b1f97-404">L’index &num;3 dans l’objet lié contient les données de configuration pour la clé de configuration `array:4` et sa valeur de `value4`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-404">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="b1f97-405">Lorsque les données de configuration contenant un tableau sont liées, les index de tableau dans les clés de configuration sont utilisés pour itérer les données de configuration lors de la création de l’objet.</span><span class="sxs-lookup"><span data-stu-id="b1f97-405">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="b1f97-406">Une valeur null ne peut pas être conservée dans des données de configuration, et une entrée à valeur null n’est pas créée dans un objet lié quand un tableau dans des clés de configuration ignore un ou plusieurs index.</span><span class="sxs-lookup"><span data-stu-id="b1f97-406">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="b1f97-407">L’élément de configuration manquant pour l’index &num; 3 peut être fourni avant la liaison à l' `ArrayExample` instance par n’importe quel fournisseur de configuration qui lit la &num; paire clé/valeur de l’index 3.</span><span class="sxs-lookup"><span data-stu-id="b1f97-407">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="b1f97-408">Examinez le *Value3.jssuivant sur* le fichier à partir de l’exemple de téléchargement :</span><span class="sxs-lookup"><span data-stu-id="b1f97-408">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="b1f97-409">Le code suivant comprend la configuration de *Value3.jssur* et `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-409">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="b1f97-410">Le code suivant lit la configuration précédente et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="b1f97-410">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="b1f97-411">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="b1f97-411">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="b1f97-412">Les fournisseurs de configuration personnalisés ne sont pas obligés d’implémenter la liaison de tableau.</span><span class="sxs-lookup"><span data-stu-id="b1f97-412">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="b1f97-413">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="b1f97-413">Custom configuration provider</span></span>

<span data-ttu-id="b1f97-414">L’exemple d’application montre comment créer un fournisseur de configuration de base qui lit les paires clé-valeur de configuration à partir d’une base de données à l’aide [d’Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="b1f97-414">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="b1f97-415">Le fournisseur présente les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="b1f97-415">The provider has the following characteristics:</span></span>

* <span data-ttu-id="b1f97-416">La base de données en mémoire EF est utilisée à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-416">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="b1f97-417">Pour utiliser une base de données qui nécessite une chaîne de connexion, implémentez un autre `ConfigurationBuilder` pour fournir la chaîne de connexion à partir d’un autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-417">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="b1f97-418">Le fournisseur lit une table de base de données dans la configuration au démarrage.</span><span class="sxs-lookup"><span data-stu-id="b1f97-418">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="b1f97-419">Le fournisseur n’interroge pas la base de données par clé.</span><span class="sxs-lookup"><span data-stu-id="b1f97-419">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="b1f97-420">Le rechargement en cas de changement n’est pas implémenté, par conséquent, la mise à jour la base de données après le démarrage de l’application n’a aucun effet sur la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-420">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="b1f97-421">Définissez une entité `EFConfigurationValue` pour le stockage des valeurs de configuration dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="b1f97-421">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="b1f97-422">*Models/EFConfigurationValue.cs* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-422">*Models/EFConfigurationValue.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="b1f97-423">Ajoutez un `EFConfigurationContext` pour stocker les valeurs configurées et y accéder.</span><span class="sxs-lookup"><span data-stu-id="b1f97-423">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="b1f97-424">*EFConfigurationProvider/EFConfigurationContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-424">*EFConfigurationProvider/EFConfigurationContext.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="b1f97-425">Créez une classe qui implémente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-425">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="b1f97-426">*EFConfigurationProvider/EFConfigurationSource.cs* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-426">*EFConfigurationProvider/EFConfigurationSource.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="b1f97-427">Créez le fournisseur de configuration personnalisé en héritant de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-427">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="b1f97-428">Le fournisseur de configuration initialise la base de données quand elle est vide.</span><span class="sxs-lookup"><span data-stu-id="b1f97-428">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="b1f97-429">Étant donné que les [clés de configuration ne](#keys)respectent pas la casse, le dictionnaire utilisé pour initialiser la base de données est créé avec le comparateur ne respectant pas la casse ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="b1f97-429">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="b1f97-430">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-430">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="b1f97-431">Une méthode d’extension `AddEFConfiguration` permet d’ajouter la source de configuration à un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-431">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="b1f97-432">*Extensions/EntityFrameworkExtensions.cs* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-432">*Extensions/EntityFrameworkExtensions.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="b1f97-433">Le code suivant montre comment utiliser le `EFConfigurationProvider` personnalisé dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-433">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs* :</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="b1f97-434">Configuration de l’accès au démarrage</span><span class="sxs-lookup"><span data-stu-id="b1f97-434">Access configuration in Startup</span></span>

<span data-ttu-id="b1f97-435">Le code suivant affiche les données de configuration dans les `Startup` méthodes :</span><span class="sxs-lookup"><span data-stu-id="b1f97-435">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="b1f97-436">Pour obtenir un exemple d’accès à la configuration à l’aide des méthodes pratiques de démarrage, consultez [Démarrage de l’application : méthodes pratiques](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="b1f97-436">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="b1f97-437">Configuration de l’accès dans les Razor pages</span><span class="sxs-lookup"><span data-stu-id="b1f97-437">Access configuration in Razor Pages</span></span>

<span data-ttu-id="b1f97-438">Le code suivant affiche les données de configuration dans une Razor page :</span><span class="sxs-lookup"><span data-stu-id="b1f97-438">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="b1f97-439">Dans le code suivant, `MyOptions` est ajouté au conteneur de services avec <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> et lié à la configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-439">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="b1f97-440">La balise suivante utilise la [`@inject`](xref:mvc/views/razor#inject) Razor directive pour résoudre et afficher les valeurs des options :</span><span class="sxs-lookup"><span data-stu-id="b1f97-440">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="b1f97-441">Configuration de l’accès dans un fichier de vue MVC</span><span class="sxs-lookup"><span data-stu-id="b1f97-441">Access configuration in a MVC view file</span></span>

<span data-ttu-id="b1f97-442">Le code suivant affiche les données de configuration dans une vue MVC :</span><span class="sxs-lookup"><span data-stu-id="b1f97-442">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="b1f97-443">Configurer des options avec un délégué</span><span class="sxs-lookup"><span data-stu-id="b1f97-443">Configure options with a delegate</span></span>

<span data-ttu-id="b1f97-444">Les options configurées dans un délégué remplacent les valeurs définies dans les fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-444">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="b1f97-445">La configuration d’options avec un délégué est illustrée dans l’exemple 2 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-445">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="b1f97-446">Dans le code suivant, un <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="b1f97-446">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="b1f97-447">Il utilise un délégué pour configurer des valeurs pour `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-447">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="b1f97-448">Le code suivant affiche les valeurs des options :</span><span class="sxs-lookup"><span data-stu-id="b1f97-448">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="b1f97-449">Dans l’exemple précédent, les valeurs de `Option1` et `Option2` sont spécifiées dans, *appsettings.json* puis remplacées par le délégué configuré.</span><span class="sxs-lookup"><span data-stu-id="b1f97-449">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="b1f97-450">Configuration de l’hôte ou configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="b1f97-450">Host versus app configuration</span></span>

<span data-ttu-id="b1f97-451">Avant que l’application ne soit configurée et démarrée, un *hôte* est configuré et lancé.</span><span class="sxs-lookup"><span data-stu-id="b1f97-451">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="b1f97-452">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="b1f97-452">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="b1f97-453">L’application et l’hôte sont configurés à l’aide des fournisseurs de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="b1f97-453">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="b1f97-454">Les paires clé-valeur de la configuration de l’hôte sont également incluses dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-454">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="b1f97-455">Pour plus d’informations sur l’utilisation des fournisseurs de configuration lors de la génération de l’hôte et l’impact des sources de configuration sur la configuration de l’hôte, consultez <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-455">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="b1f97-456">Configuration de l’hôte par défaut</span><span class="sxs-lookup"><span data-stu-id="b1f97-456">Default host configuration</span></span>

<span data-ttu-id="b1f97-457">Pour plus de détails sur la configuration par défaut lors de l’utilisation de l’[hôte Web](xref:fundamentals/host/web-host), consultez la [version ASP.NET Core 2.2. de cette rubrique](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="b1f97-457">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="b1f97-458">La configuration de l’hôte est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b1f97-458">Host configuration is provided from:</span></span>
  * <span data-ttu-id="b1f97-459">Les variables d’environnement précédées du préfixe `DOTNET_` (par exemple, `DOTNET_ENVIRONMENT` ) à l’aide du [fournisseur de configuration des variables d’environnement](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="b1f97-459">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="b1f97-460">Le préfixe (`DOTNET_`) est supprimé lorsque les paires clé-valeur de la configuration sont chargées.</span><span class="sxs-lookup"><span data-stu-id="b1f97-460">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="b1f97-461">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b1f97-461">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="b1f97-462">La configuration par défaut de l’hôte Web est établie (`ConfigureWebHostDefaults`) :</span><span class="sxs-lookup"><span data-stu-id="b1f97-462">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="b1f97-463">Kestrel est utilisé comme serveur web et configuré à l’aide des fournisseurs de configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-463">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="b1f97-464">Ajoutez l’intergiciel de filtrage d’hôtes.</span><span class="sxs-lookup"><span data-stu-id="b1f97-464">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="b1f97-465">Ajoutez l’intergiciel d’en-têtes transférés si la variable d'environnement `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-465">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="b1f97-466">Activez l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="b1f97-466">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="b1f97-467">Autre configuration</span><span class="sxs-lookup"><span data-stu-id="b1f97-467">Other configuration</span></span>

<span data-ttu-id="b1f97-468">Cette rubrique se rapporte uniquement à la configuration de l' *application* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-468">This topic only pertains to *app configuration* .</span></span> <span data-ttu-id="b1f97-469">D’autres aspects de l’exécution et de l’hébergement des applications ASP.NET Core sont configurés à l’aide des fichiers de configuration non traités dans cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="b1f97-469">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="b1f97-470">*launch.js* / *launchSettings.jssur* sont des fichiers de configuration d’outils pour l’environnement de développement, décrits ci-après :</span><span class="sxs-lookup"><span data-stu-id="b1f97-470">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="b1f97-471">Dans <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="b1f97-471">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="b1f97-472">Dans l’ensemble de la documentation dans lequel les fichiers sont utilisés pour configurer des applications ASP.NET Core pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="b1f97-472">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="b1f97-473">*web.config* est un fichier de configuration de serveur, décrit dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="b1f97-473">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="b1f97-474">Les variables d’environnement définies dans *launchSettings.jslors de* la substitution de celles définies dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="b1f97-474">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="b1f97-475">Pour plus d’informations sur la migration de la configuration d’application à partir de versions antérieures de ASP.NET, consultez <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="b1f97-475">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="b1f97-476">Ajouter la configuration à partir d’un assembly externe</span><span class="sxs-lookup"><span data-stu-id="b1f97-476">Add configuration from an external assembly</span></span>

<span data-ttu-id="b1f97-477">Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-477">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="b1f97-478">Pour plus d'informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-478">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1f97-479">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b1f97-479">Additional resources</span></span>

* [<span data-ttu-id="b1f97-480">Code source de configuration</span><span class="sxs-lookup"><span data-stu-id="b1f97-480">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b1f97-481">La configuration d’application dans ASP.NET Core est basée sur des paires clé-valeur établies par les *fournisseurs de configuration* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-481">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers* .</span></span> <span data-ttu-id="b1f97-482">Les fournisseurs de configuration lisent les données de configuration dans les paires clé-valeur à partir de diverses sources de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-482">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="b1f97-483">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b1f97-483">Azure Key Vault</span></span>
* <span data-ttu-id="b1f97-484">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="b1f97-484">Azure App Configuration</span></span>
* <span data-ttu-id="b1f97-485">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="b1f97-485">Command-line arguments</span></span>
* <span data-ttu-id="b1f97-486">Fournisseurs personnalisés (installés ou créés)</span><span class="sxs-lookup"><span data-stu-id="b1f97-486">Custom providers (installed or created)</span></span>
* <span data-ttu-id="b1f97-487">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="b1f97-487">Directory files</span></span>
* <span data-ttu-id="b1f97-488">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="b1f97-488">Environment variables</span></span>
* <span data-ttu-id="b1f97-489">Objets .NET en mémoire</span><span class="sxs-lookup"><span data-stu-id="b1f97-489">In-memory .NET objects</span></span>
* <span data-ttu-id="b1f97-490">Fichiers de paramètres</span><span class="sxs-lookup"><span data-stu-id="b1f97-490">Settings files</span></span>

<span data-ttu-id="b1f97-491">Les packages de configuration pour les scénarios de fournisseur de configuration courants ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sont inclus implicitement dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b1f97-491">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b1f97-492">Les exemples de code qui suivent et dans l’échantillon d’application utilisent l’espace de noms <xref:Microsoft.Extensions.Configuration> :</span><span class="sxs-lookup"><span data-stu-id="b1f97-492">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="b1f97-493">Le *modèle d’options* est une extension des concepts de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="b1f97-493">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="b1f97-494">Les options utilisent des classes pour représenter les groupes de paramètres associés.</span><span class="sxs-lookup"><span data-stu-id="b1f97-494">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="b1f97-495">Pour plus d'informations, consultez <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-495">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="b1f97-496">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b1f97-496">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="b1f97-497">Configuration de l’hôte ou configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="b1f97-497">Host versus app configuration</span></span>

<span data-ttu-id="b1f97-498">Avant que l’application ne soit configurée et démarrée, un *hôte* est configuré et lancé.</span><span class="sxs-lookup"><span data-stu-id="b1f97-498">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="b1f97-499">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="b1f97-499">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="b1f97-500">L’application et l’hôte sont configurés à l’aide des fournisseurs de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="b1f97-500">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="b1f97-501">Les paires clé-valeur de la configuration de l’hôte sont également incluses dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-501">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="b1f97-502">Pour plus d’informations sur l’utilisation des fournisseurs de configuration lors de la génération de l’hôte et l’impact des sources de configuration sur la configuration de l’hôte, consultez <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-502">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="b1f97-503">Autre configuration</span><span class="sxs-lookup"><span data-stu-id="b1f97-503">Other configuration</span></span>

<span data-ttu-id="b1f97-504">Cette rubrique se rapporte uniquement à la configuration de l' *application* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-504">This topic only pertains to *app configuration* .</span></span> <span data-ttu-id="b1f97-505">D’autres aspects de l’exécution et de l’hébergement des applications ASP.NET Core sont configurés à l’aide des fichiers de configuration non traités dans cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="b1f97-505">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="b1f97-506">*launch.js* / *launchSettings.jssur* sont des fichiers de configuration d’outils pour l’environnement de développement, décrits ci-après :</span><span class="sxs-lookup"><span data-stu-id="b1f97-506">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="b1f97-507">Dans <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="b1f97-507">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="b1f97-508">Dans l’ensemble de la documentation dans lequel les fichiers sont utilisés pour configurer des applications ASP.NET Core pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="b1f97-508">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="b1f97-509">*web.config* est un fichier de configuration de serveur, décrit dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="b1f97-509">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="b1f97-510">Pour plus d’informations sur la migration de la configuration d’application à partir de versions antérieures de ASP.NET, consultez <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="b1f97-510">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="b1f97-511">Configuration par défaut</span><span class="sxs-lookup"><span data-stu-id="b1f97-511">Default configuration</span></span>

<span data-ttu-id="b1f97-512">Les applications web basées sur les modèles ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) appellent <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> pendant la création d’un hôte.</span><span class="sxs-lookup"><span data-stu-id="b1f97-512">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="b1f97-513">`CreateDefaultBuilder` fournit la configuration par défaut de l’application dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="b1f97-513">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="b1f97-514">Les éléments suivants s’appliquent aux applications qui utilisent l’[hôte web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="b1f97-514">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="b1f97-515">Pour plus de détails sur la configuration par défaut lors de l’utilisation de l’[hôte générique](xref:fundamentals/host/generic-host), consultez la [dernière version de cette rubrique](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b1f97-515">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="b1f97-516">La configuration de l’hôte est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b1f97-516">Host configuration is provided from:</span></span>
  * <span data-ttu-id="b1f97-517">Des variables d’environnement préfixées avec `ASPNETCORE_` (par exemple, `ASPNETCORE_ENVIRONMENT`) à l’aide du [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b1f97-517">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="b1f97-518">Le préfixe (`ASPNETCORE_`) est supprimé lorsque les paires clé-valeur de la configuration sont chargées.</span><span class="sxs-lookup"><span data-stu-id="b1f97-518">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="b1f97-519">Des arguments de ligne de commande à l’aide du [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b1f97-519">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="b1f97-520">La configuration de l’application est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b1f97-520">App configuration is provided from:</span></span>
  * <span data-ttu-id="b1f97-521">*appsettings.json* utilisation du [fournisseur de configuration de fichier](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b1f97-521">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="b1f97-522">*appsettings.{Environment}.json* à l’aide du [Fournisseur de configuration de fichier](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b1f97-522">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="b1f97-523">L’outil [Secret Manager (Gestionnaire de secrets)](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development` à l’aide de l’assembly d’entrée.</span><span class="sxs-lookup"><span data-stu-id="b1f97-523">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="b1f97-524">Des variables d’environnement à l’aide du [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b1f97-524">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="b1f97-525">Des arguments de ligne de commande à l’aide du [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b1f97-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="b1f97-526">Sécurité</span><span class="sxs-lookup"><span data-stu-id="b1f97-526">Security</span></span>

<span data-ttu-id="b1f97-527">Adoptez les pratiques suivantes pour sécuriser les données de configuration sensibles :</span><span class="sxs-lookup"><span data-stu-id="b1f97-527">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="b1f97-528">Ne stockez jamais des mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte clair.</span><span class="sxs-lookup"><span data-stu-id="b1f97-528">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="b1f97-529">N’utilisez aucun secret de production dans les environnements de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="b1f97-529">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="b1f97-530">Spécifiez les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans un référentiel de code source.</span><span class="sxs-lookup"><span data-stu-id="b1f97-530">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="b1f97-531">Pour plus d'informations, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="b1f97-531">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="b1f97-532"><xref:security/app-secrets>: Fournit des conseils sur l’utilisation de variables d’environnement pour stocker des données sensibles.</span><span class="sxs-lookup"><span data-stu-id="b1f97-532"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="b1f97-533">Secret Manager utilise le fournisseur de configuration de fichier pour stocker les secrets utilisateur dans un fichier JSON sur le système local.</span><span class="sxs-lookup"><span data-stu-id="b1f97-533">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="b1f97-534">Le fournisseur de configuration de fichier est décrit plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="b1f97-534">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="b1f97-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stocke en toute sécurité des secrets d’application pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b1f97-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="b1f97-536">Pour plus d'informations, consultez <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-536">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="b1f97-537">Données de configuration hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="b1f97-537">Hierarchical configuration data</span></span>

<span data-ttu-id="b1f97-538">L’API Configuration est capable de maintenir des données de configuration hiérarchiques en aplanissant les données hiérarchiques à l’aide d’un délimiteur dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-538">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="b1f97-539">Dans le fichier JSON suivant, quatre clés existent dans une structure hiérarchique à deux sections :</span><span class="sxs-lookup"><span data-stu-id="b1f97-539">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="b1f97-540">Lorsque le fichier est lu dans la configuration, des clés uniques sont créées pour gérer la structure des données hiérarchiques d’origine de la source de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-540">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="b1f97-541">Les sections et les clés sont aplanies à l’aide d’un signe deux-points (`:`) pour conserver la structure d’origine :</span><span class="sxs-lookup"><span data-stu-id="b1f97-541">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="b1f97-542">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b1f97-542">section0:key0</span></span>
* <span data-ttu-id="b1f97-543">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b1f97-543">section0:key1</span></span>
* <span data-ttu-id="b1f97-544">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b1f97-544">section1:key0</span></span>
* <span data-ttu-id="b1f97-545">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b1f97-545">section1:key1</span></span>

<span data-ttu-id="b1f97-546">Les méthodes <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> et <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sont disponibles pour isoler les sections et les enfants d’une section dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-546"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="b1f97-547">Ces méthodes sont décrites plus loin dans [GetSection, GetChildren et Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="b1f97-547">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="b1f97-548">Conventions</span><span class="sxs-lookup"><span data-stu-id="b1f97-548">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="b1f97-549">Sources et fournisseurs</span><span class="sxs-lookup"><span data-stu-id="b1f97-549">Sources and providers</span></span>

<span data-ttu-id="b1f97-550">Au démarrage de l’application, les sources de configuration sont lues dans l’ordre où leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="b1f97-550">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="b1f97-551">Les fournisseurs de configuration qui implémentent la détection des modifications peuvent recharger la configuration lorsqu’un paramètre sous-jacent est modifié.</span><span class="sxs-lookup"><span data-stu-id="b1f97-551">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="b1f97-552">Par exemple, le fournisseur de configuration de fichier (décrit plus loin dans cette rubrique) et le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) implémentent la détection des modifications.</span><span class="sxs-lookup"><span data-stu-id="b1f97-552">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="b1f97-553"><xref:Microsoft.Extensions.Configuration.IConfiguration> est disponible dans le conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-553"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b1f97-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> peut être injecté dans une Razor page <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou un MVC <xref:Microsoft.AspNetCore.Mvc.Controller> pour obtenir la configuration de la classe.</span><span class="sxs-lookup"><span data-stu-id="b1f97-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="b1f97-555">Dans les exemples suivants, le `_config` champ est utilisé pour accéder aux valeurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-555">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="b1f97-556">Les fournisseurs de configuration ne peuvent pas utiliser le DI, car celui-ci n’est pas disponible lorsque les fournisseurs sont configurés par l’hôte.</span><span class="sxs-lookup"><span data-stu-id="b1f97-556">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="b1f97-557">Keys</span><span class="sxs-lookup"><span data-stu-id="b1f97-557">Keys</span></span>

<span data-ttu-id="b1f97-558">Les clés de configuration adoptent les conventions suivantes :</span><span class="sxs-lookup"><span data-stu-id="b1f97-558">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="b1f97-559">Les clés ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="b1f97-559">Keys are case-insensitive.</span></span> <span data-ttu-id="b1f97-560">Par exemple, `ConnectionString` et `connectionstring` sont traités en tant que clés équivalentes.</span><span class="sxs-lookup"><span data-stu-id="b1f97-560">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="b1f97-561">Si une valeur pour la même clé est définie par des fournisseurs de configuration identiques ou différents, la valeur utilisée est la dernière valeur définie sur la clé.</span><span class="sxs-lookup"><span data-stu-id="b1f97-561">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="b1f97-562">Pour plus d’informations sur les clés JSON en double, consultez [ce problème GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="b1f97-562">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="b1f97-563">Clés hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="b1f97-563">Hierarchical keys</span></span>
  * <span data-ttu-id="b1f97-564">Dans l’API Configuration, un séparateur sous forme de signe deux-points (`:`) fonctionne sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="b1f97-564">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="b1f97-565">Dans les variables d’environnement, un séparateur sous forme de signe deux-points peut ne pas fonctionner sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="b1f97-565">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="b1f97-566">Un trait de soulignement double (`__`) est pris en charge par toutes les plateformes et automatiquement transformé en signe deux-points.</span><span class="sxs-lookup"><span data-stu-id="b1f97-566">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="b1f97-567">Dans Azure Key Vault, les clés hiérarchiques utilisent `--` (deux tirets) comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="b1f97-567">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="b1f97-568">Écrivez du code pour remplacer les tirets par un signe deux-points lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-568">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="b1f97-569"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-569">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b1f97-570">La liaison de tableau est décrite dans la section [Lier un tableau à une classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="b1f97-570">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="b1f97-571">Valeurs</span><span class="sxs-lookup"><span data-stu-id="b1f97-571">Values</span></span>

<span data-ttu-id="b1f97-572">Les valeurs de configuration adoptent les conventions suivantes :</span><span class="sxs-lookup"><span data-stu-id="b1f97-572">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="b1f97-573">Les valeurs sont des chaînes.</span><span class="sxs-lookup"><span data-stu-id="b1f97-573">Values are strings.</span></span>
* <span data-ttu-id="b1f97-574">Les valeurs NULL ne peuvent pas être stockées dans la configuration ou liées à des objets.</span><span class="sxs-lookup"><span data-stu-id="b1f97-574">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="b1f97-575">Fournisseurs</span><span class="sxs-lookup"><span data-stu-id="b1f97-575">Providers</span></span>

<span data-ttu-id="b1f97-576">Le tableau suivant présente les fournisseurs de configuration disponibles pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b1f97-576">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="b1f97-577">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="b1f97-577">Provider</span></span> | <span data-ttu-id="b1f97-578">Fournit la configuration à partir de&hellip;</span><span class="sxs-lookup"><span data-stu-id="b1f97-578">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="b1f97-579">[Fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) (rubrique *Sécurité* )</span><span class="sxs-lookup"><span data-stu-id="b1f97-579">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) ( *Security* topics)</span></span> | <span data-ttu-id="b1f97-580">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b1f97-580">Azure Key Vault</span></span> |
| <span data-ttu-id="b1f97-581">[Fournisseur Azure App Configuration](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentation Azure)</span><span class="sxs-lookup"><span data-stu-id="b1f97-581">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="b1f97-582">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="b1f97-582">Azure App Configuration</span></span> |
| [<span data-ttu-id="b1f97-583">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="b1f97-583">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="b1f97-584">Paramètres de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="b1f97-584">Command-line parameters</span></span> |
| [<span data-ttu-id="b1f97-585">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="b1f97-585">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="b1f97-586">Source personnalisée</span><span class="sxs-lookup"><span data-stu-id="b1f97-586">Custom source</span></span> |
| [<span data-ttu-id="b1f97-587">Fournisseur de configuration de variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="b1f97-587">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="b1f97-588">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="b1f97-588">Environment variables</span></span> |
| [<span data-ttu-id="b1f97-589">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="b1f97-589">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="b1f97-590">Fichiers (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="b1f97-590">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="b1f97-591">Fournisseur de configuration clé par fichier</span><span class="sxs-lookup"><span data-stu-id="b1f97-591">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="b1f97-592">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="b1f97-592">Directory files</span></span> |
| [<span data-ttu-id="b1f97-593">Fournisseur de configuration de mémoire</span><span class="sxs-lookup"><span data-stu-id="b1f97-593">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="b1f97-594">Collections en mémoire</span><span class="sxs-lookup"><span data-stu-id="b1f97-594">In-memory collections</span></span> |
| <span data-ttu-id="b1f97-595">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) (rubrique *Sécurité* )</span><span class="sxs-lookup"><span data-stu-id="b1f97-595">[User secrets (Secret Manager)](xref:security/app-secrets) ( *Security* topics)</span></span> | <span data-ttu-id="b1f97-596">Fichier dans le répertoire de profil utilisateur</span><span class="sxs-lookup"><span data-stu-id="b1f97-596">File in the user profile directory</span></span> |

<span data-ttu-id="b1f97-597">Au démarrage, les sources de configuration sont lues dans l’ordre où leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="b1f97-597">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="b1f97-598">Les fournisseurs de configuration décrits dans cette rubrique sont décrits par ordre alphabétique, et non pas dans l’ordre dans lequel le code les réorganise.</span><span class="sxs-lookup"><span data-stu-id="b1f97-598">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="b1f97-599">Commandez des fournisseurs de configuration dans le code pour répondre aux priorités des sources de configuration sous-jacentes requises par l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-599">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="b1f97-600">Une séquence type des fournisseurs de configuration est la suivante :</span><span class="sxs-lookup"><span data-stu-id="b1f97-600">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="b1f97-601">Fichiers ( *appsettings.json* , *appSettings. { Environment}. JSON* , où `{Environment}` est l’environnement d’hébergement actuel de l’application)</span><span class="sxs-lookup"><span data-stu-id="b1f97-601">Files ( *appsettings.json* , *appsettings.{Environment}.json* , where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="b1f97-602">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b1f97-602">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="b1f97-603">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) (dans l’environnement de développement uniquement)</span><span class="sxs-lookup"><span data-stu-id="b1f97-603">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="b1f97-604">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="b1f97-604">Environment variables</span></span>
1. <span data-ttu-id="b1f97-605">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="b1f97-605">Command-line arguments</span></span>

<span data-ttu-id="b1f97-606">Une pratique courante consiste à placer le Fournisseur de configuration de ligne de commande en dernier dans une série de fournisseurs pour permettre aux arguments de ligne de commande de remplacer la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="b1f97-606">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="b1f97-607">La séquence de fournisseurs précédente est utilisée lors de l’initialisation d’un nouveau générateur d’hôte `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b1f97-607">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b1f97-608">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="b1f97-608">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="b1f97-609">Configurer le générateur d’ordinateur hôte avec UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="b1f97-609">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="b1f97-610">Pour configurer le générateur d’ordinateur hôte, appelez <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> sur le générateur d’hôte avec la configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-610">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="b1f97-611">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="b1f97-611">ConfigureAppConfiguration</span></span>

<span data-ttu-id="b1f97-612">Appelez `ConfigureAppConfiguration` quand vous créez l’hôte pour spécifier les fournisseurs de configuration de l’application en plus de ceux ajoutés automatiquement par `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-612">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="b1f97-613">Remplacez la configuration précédente par des arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="b1f97-613">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="b1f97-614">Pour fournir une configuration d’application pouvant être remplacée par des arguments de ligne de commande, appelez `AddCommandLine` en dernier :</span><span class="sxs-lookup"><span data-stu-id="b1f97-614">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="b1f97-615">Supprimer les fournisseurs ajoutés par CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="b1f97-615">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="b1f97-616">Pour supprimer les fournisseurs ajoutés par `CreateDefaultBuilder` , appelez d’abord [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) sur [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="b1f97-616">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="b1f97-617">Utiliser la configuration lors du démarrage de l’application</span><span class="sxs-lookup"><span data-stu-id="b1f97-617">Consume configuration during app startup</span></span>

<span data-ttu-id="b1f97-618">La configuration fournie à l’application dans `ConfigureAppConfiguration` est disponible lors du démarrage de l’application, notamment `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-618">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b1f97-619">Pour plus d’informations, consultez la section [Accéder à la configuration lors du démarrage](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="b1f97-619">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="b1f97-620">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="b1f97-620">Command-line Configuration Provider</span></span>

<span data-ttu-id="b1f97-621"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> charge la configuration à partir des paires clé-valeur de l’argument de ligne de commande lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b1f97-621">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="b1f97-622">Pour activer la configuration en ligne de commande, la méthode d’extension <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> est appelée sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-622">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b1f97-623">`AddCommandLine` est appelé automatiquement quand `CreateDefaultBuilder(string [])` est appelé.</span><span class="sxs-lookup"><span data-stu-id="b1f97-623">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="b1f97-624">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="b1f97-624">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="b1f97-625">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="b1f97-625">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b1f97-626">Configuration facultative à partir de *appsettings.json* et *appSettings. { Fichiers Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-626">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="b1f97-627">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="b1f97-627">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="b1f97-628">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="b1f97-628">Environment variables.</span></span>

<span data-ttu-id="b1f97-629">`CreateDefaultBuilder` ajoute en dernier le Fournisseur de configuration de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="b1f97-629">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="b1f97-630">Les arguments de ligne de commande passés lors de l’exécution remplacent la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="b1f97-630">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="b1f97-631">`CreateDefaultBuilder` agit lors de la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="b1f97-631">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="b1f97-632">Par conséquent, la configuration de ligne de commande activée par `CreateDefaultBuilder` peut affecter la façon dont l’hôte est configuré.</span><span class="sxs-lookup"><span data-stu-id="b1f97-632">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="b1f97-633">Pour les applications basées sur les modèles ASP.NET Core, `AddCommandLine` a déjà été appelé par `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-633">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b1f97-634">Pour ajouter des fournisseurs de configuration supplémentaires et conserver la capacité de remplacer leur configuration par des arguments de ligne de commande, appelez les fournisseurs supplémentaires de l’application dans `ConfigureAppConfiguration` et appelez `AddCommandLine` en dernier.</span><span class="sxs-lookup"><span data-stu-id="b1f97-634">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="b1f97-635">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="b1f97-635">**Example**</span></span>

<span data-ttu-id="b1f97-636">L’exemple d’application tire parti de la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte, qui inclut un appel à <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-636">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="b1f97-637">Ouvrez une invite de commandes dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="b1f97-637">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="b1f97-638">Fournissez un argument de ligne de commande à la commande `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-638">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="b1f97-639">Une fois que l’application est en cours d’exécution, ouvrez un navigateur à l’application à l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-639">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b1f97-640">Notez que la sortie contient la paire clé-valeur pour l’argument de ligne de commande de configuration fourni à `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-640">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="b1f97-641">Arguments</span><span class="sxs-lookup"><span data-stu-id="b1f97-641">Arguments</span></span>

<span data-ttu-id="b1f97-642">La valeur doit suivre un signe égal (`=`) ou la clé doit avoir un préfixe (`--` ou `/`) lorsque la valeur suit un espace.</span><span class="sxs-lookup"><span data-stu-id="b1f97-642">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="b1f97-643">La valeur n’est pas requise si un signe égal est utilisé (par exemple, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="b1f97-643">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="b1f97-644">Préfixe de clé</span><span class="sxs-lookup"><span data-stu-id="b1f97-644">Key prefix</span></span>               | <span data-ttu-id="b1f97-645">Exemple</span><span class="sxs-lookup"><span data-stu-id="b1f97-645">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="b1f97-646">Aucun préfixe</span><span class="sxs-lookup"><span data-stu-id="b1f97-646">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="b1f97-647">Deux tirets (`--`)</span><span class="sxs-lookup"><span data-stu-id="b1f97-647">Two dashes (`--`)</span></span>        | <span data-ttu-id="b1f97-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="b1f97-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="b1f97-649">Barre oblique (`/`)</span><span class="sxs-lookup"><span data-stu-id="b1f97-649">Forward slash (`/`)</span></span>      | <span data-ttu-id="b1f97-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="b1f97-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="b1f97-651">Dans la même commande, ne mélangez pas des paires clé-valeur de l’argument de ligne de commande qui utilisent un signe égal avec des paires clé-valeur qui utilisent un espace.</span><span class="sxs-lookup"><span data-stu-id="b1f97-651">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="b1f97-652">Exemples de commandes :</span><span class="sxs-lookup"><span data-stu-id="b1f97-652">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="b1f97-653">Correspondances de commutateur</span><span class="sxs-lookup"><span data-stu-id="b1f97-653">Switch mappings</span></span>

<span data-ttu-id="b1f97-654">Les correspondances de commutateur permettent une logique de remplacement des noms de clés.</span><span class="sxs-lookup"><span data-stu-id="b1f97-654">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="b1f97-655">Lors de la génération manuelle d’une configuration avec un <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , fournissez un dictionnaire de remplacements de commutateur à la <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> méthode.</span><span class="sxs-lookup"><span data-stu-id="b1f97-655">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="b1f97-656">Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="b1f97-656">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="b1f97-657">Si la clé de ligne de commande est trouvée dans le dictionnaire, la valeur du dictionnaire (le remplacement de la clé) est repassée pour définir la paire clé-valeur dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-657">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="b1f97-658">Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).</span><span class="sxs-lookup"><span data-stu-id="b1f97-658">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="b1f97-659">Règles des clés du dictionnaire de correspondances de commutateur :</span><span class="sxs-lookup"><span data-stu-id="b1f97-659">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="b1f97-660">Les commutateurs doivent commencer par un tiret (`-`) ou un double tiret (`--`).</span><span class="sxs-lookup"><span data-stu-id="b1f97-660">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="b1f97-661">Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.</span><span class="sxs-lookup"><span data-stu-id="b1f97-661">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="b1f97-662">Créez un dictionnaire des mappages de commutateurs.</span><span class="sxs-lookup"><span data-stu-id="b1f97-662">Create a switch mappings dictionary.</span></span> <span data-ttu-id="b1f97-663">Dans l’exemple suivant, deux mappages de commutateurs sont créés :</span><span class="sxs-lookup"><span data-stu-id="b1f97-663">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="b1f97-664">Lorsque l’hôte est généré, appelez `AddCommandLine` avec le dictionnaire de mappages de commutateurs :</span><span class="sxs-lookup"><span data-stu-id="b1f97-664">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="b1f97-665">Pour les applications qui utilisent des mappages de commutateurs, l’appel à `CreateDefaultBuilder` ne doit pas passer d’arguments.</span><span class="sxs-lookup"><span data-stu-id="b1f97-665">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="b1f97-666">L’appel `AddCommandLine` de la méthode `CreateDefaultBuilder` n’inclut pas de commutateurs mappés et il n’existe aucun moyen de transmettre le dictionnaire de correspondance de commutateur à `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-666">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b1f97-667">La solution ne consiste pas à transmettre les arguments à `CreateDefaultBuilder`, mais plutôt à permettre à la méthode `AddCommandLine` de la méthode `ConfigurationBuilder` de traiter les arguments et le dictionnaire de mappage de commutateur.</span><span class="sxs-lookup"><span data-stu-id="b1f97-667">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="b1f97-668">Une fois le dictionnaire de correspondances de commutateur créé, il contient les données affichées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="b1f97-668">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="b1f97-669">Clé</span><span class="sxs-lookup"><span data-stu-id="b1f97-669">Key</span></span>       | <span data-ttu-id="b1f97-670">Valeur</span><span class="sxs-lookup"><span data-stu-id="b1f97-670">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="b1f97-671">Si les clés mappées au commutateur sont utilisées lors du démarrage de l’application, la configuration reçoit la valeur de configuration sur la clé fournie par le dictionnaire :</span><span class="sxs-lookup"><span data-stu-id="b1f97-671">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="b1f97-672">Après avoir exécuté la commande précédente, la configuration contient les valeurs indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="b1f97-672">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="b1f97-673">Clé</span><span class="sxs-lookup"><span data-stu-id="b1f97-673">Key</span></span>               | <span data-ttu-id="b1f97-674">Valeur</span><span class="sxs-lookup"><span data-stu-id="b1f97-674">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="b1f97-675">Fournisseur de configuration de variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="b1f97-675">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="b1f97-676"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> charge la configuration à partir des paires clé-valeur de la variable d’environnement lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b1f97-676">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="b1f97-677">Pour activer la configuration des variables d’environnement, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-677">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="b1f97-678">[Azure App service](https://azure.microsoft.com/services/app-service/) permet de définir des variables d’environnement dans le portail Azure qui peuvent remplacer la configuration de l’application à l’aide du fournisseur de configuration des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="b1f97-678">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="b1f97-679">Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="b1f97-679">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="b1f97-680">`AddEnvironmentVariables` sert à charger les variables d’environnement préfixées avec `ASPNETCORE_` pour la [configuration hôte](#host-versus-app-configuration) lorsqu’un nouveau générateur d’hôte est initialisé avec l’[hôte web](xref:fundamentals/host/web-host) et que `CreateDefaultBuilder` est appelé.</span><span class="sxs-lookup"><span data-stu-id="b1f97-680">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="b1f97-681">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="b1f97-681">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="b1f97-682">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="b1f97-682">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b1f97-683">Configuration de l’application à partir de variables d’environnement sans préfixe en appelant `AddEnvironmentVariables` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="b1f97-683">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="b1f97-684">Configuration facultative à partir de *appsettings.json* et *appSettings. { Fichiers Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-684">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="b1f97-685">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="b1f97-685">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="b1f97-686">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="b1f97-686">Command-line arguments.</span></span>

<span data-ttu-id="b1f97-687">Le fournisseur de configuration de variables d’environnement est appelé une fois que la configuration est établie à partir des secrets utilisateur et des fichiers *appsettings* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-687">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="b1f97-688">Le fait d’appeler le fournisseur ainsi permet de lire les variables d’environnement pendant l’exécution pour substituer la configuration définie par les secrets utilisateur et les fichiers *appsettings* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-688">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="b1f97-689">Pour fournir la configuration d’application à partir de variables d’environnement supplémentaires, appelez les fournisseurs supplémentaires de l’application dans `ConfigureAppConfiguration` et appelez `AddEnvironmentVariables` avec le préfixe :</span><span class="sxs-lookup"><span data-stu-id="b1f97-689">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="b1f97-690">Appelez `AddEnvironmentVariables` Last pour autoriser les variables d’environnement avec le préfixe spécifié à substituer des valeurs d’autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="b1f97-690">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="b1f97-691">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="b1f97-691">**Example**</span></span>

<span data-ttu-id="b1f97-692">L’exemple d’application tire parti de la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte, qui inclut un appel à `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-692">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="b1f97-693">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-693">Run the sample app.</span></span> <span data-ttu-id="b1f97-694">Ouvrez un navigateur vers l’application avec l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-694">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b1f97-695">Notez que la sortie contient la paire clé-valeur pour la variable d’environnement `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-695">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="b1f97-696">La valeur reflète l’environnement dans lequel l’application est en cours d’exécution, en général `Development` lors de l’exécution locale.</span><span class="sxs-lookup"><span data-stu-id="b1f97-696">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="b1f97-697">Pour que la liste des variables d’environnement restituée par l’application soit courte, l’application filtre les variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="b1f97-697">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="b1f97-698">Consultez le fichier *Pages/Index.cshtml.cs* de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-698">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="b1f97-699">Pour exposer toutes les variables d’environnement disponibles pour l’application, remplacez `FilteredConfiguration` dans *pages/index. cshtml. cs* par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="b1f97-699">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="b1f97-700">Préfixes</span><span class="sxs-lookup"><span data-stu-id="b1f97-700">Prefixes</span></span>

<span data-ttu-id="b1f97-701">Les variables d’environnement chargées dans la configuration de l’application sont filtrées lors de la spécification d’un préfixe à la `AddEnvironmentVariables` méthode.</span><span class="sxs-lookup"><span data-stu-id="b1f97-701">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="b1f97-702">Par exemple, pour filtrer les variables d’environnement sur le préfixe `CUSTOM_`, fournissez le préfixe au fournisseur de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-702">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="b1f97-703">Le préfixe est supprimé lorsque les paires clé-valeur de la configuration sont créées.</span><span class="sxs-lookup"><span data-stu-id="b1f97-703">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="b1f97-704">Lorsque le générateur d’hôte est créé, la configuration de l’hôte est fournie par des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="b1f97-704">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="b1f97-705">Pour plus d’informations sur le préfixe utilisé pour ces variables d’environnement, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="b1f97-705">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="b1f97-706">**Préfixes des chaînes de connexion**</span><span class="sxs-lookup"><span data-stu-id="b1f97-706">**Connection string prefixes**</span></span>

<span data-ttu-id="b1f97-707">L’API Configuration possède des règles de traitement spéciales pour quatre variables d’environnement de chaîne de connexion impliquées dans la configuration des chaînes de connexion Azure pour l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-707">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="b1f97-708">Les variables d’environnement avec les préfixes indiqués dans le tableau sont chargées dans l’application si aucun préfixe n’est fourni à `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-708">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="b1f97-709">Préfixe de la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="b1f97-709">Connection string prefix</span></span> | <span data-ttu-id="b1f97-710">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="b1f97-710">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="b1f97-711">Fournisseur personnalisé</span><span class="sxs-lookup"><span data-stu-id="b1f97-711">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="b1f97-712">MySQL</span><span class="sxs-lookup"><span data-stu-id="b1f97-712">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="b1f97-713">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="b1f97-713">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="b1f97-714">SQL Server</span><span class="sxs-lookup"><span data-stu-id="b1f97-714">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="b1f97-715">Quand une variable d’environnement est découverte et chargée dans la configuration avec l’un des quatre préfixes indiqués dans le tableau :</span><span class="sxs-lookup"><span data-stu-id="b1f97-715">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="b1f97-716">La clé de configuration est créée en supprimant le préfixe de la variable d’environnement et en ajoutant une section de clé de configuration (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="b1f97-716">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="b1f97-717">Une nouvelle paire clé-valeur de configuration est créée qui représente le fournisseur de connexion de base de données (à l’exception de `CUSTOMCONNSTR_`, qui ne possède aucun fournisseur indiqué).</span><span class="sxs-lookup"><span data-stu-id="b1f97-717">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="b1f97-718">Clé de variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="b1f97-718">Environment variable key</span></span> | <span data-ttu-id="b1f97-719">Clé de configuration convertie</span><span class="sxs-lookup"><span data-stu-id="b1f97-719">Converted configuration key</span></span> | <span data-ttu-id="b1f97-720">Entrée de configuration de fournisseur</span><span class="sxs-lookup"><span data-stu-id="b1f97-720">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b1f97-721">Entrée de configuration non créée.</span><span class="sxs-lookup"><span data-stu-id="b1f97-721">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b1f97-722">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-722">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b1f97-723">Valeur: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="b1f97-723">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b1f97-724">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-724">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b1f97-725">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b1f97-725">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b1f97-726">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b1f97-727">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b1f97-727">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="b1f97-728">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="b1f97-728">**Example**</span></span>

<span data-ttu-id="b1f97-729">Une variable d’environnement de chaîne de connexion personnalisée est créée sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="b1f97-729">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="b1f97-730">Nom : `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="b1f97-730">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="b1f97-731">Valeur: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="b1f97-731">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="b1f97-732">Si `IConfiguration` est injecté et affecté à un champ nommé `_config` , lisez la valeur :</span><span class="sxs-lookup"><span data-stu-id="b1f97-732">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="b1f97-733">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="b1f97-733">File Configuration Provider</span></span>

<span data-ttu-id="b1f97-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> est la classe de base pour charger la configuration à partir du système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="b1f97-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="b1f97-735">Les fournisseurs de configuration suivants sont dédiés à des types de fichiers spécifiques :</span><span class="sxs-lookup"><span data-stu-id="b1f97-735">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="b1f97-736">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="b1f97-736">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="b1f97-737">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="b1f97-737">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="b1f97-738">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="b1f97-738">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="b1f97-739">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="b1f97-739">INI Configuration Provider</span></span>

<span data-ttu-id="b1f97-740"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier INI lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b1f97-740">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="b1f97-741">Pour activer la configuration du fichier INI, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-741">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b1f97-742">Le signe deux-points peut servir de délimiteur de section dans la configuration d’un fichier INI.</span><span class="sxs-lookup"><span data-stu-id="b1f97-742">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="b1f97-743">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="b1f97-743">Overloads permit specifying:</span></span>

* <span data-ttu-id="b1f97-744">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="b1f97-744">Whether the file is optional.</span></span>
* <span data-ttu-id="b1f97-745">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="b1f97-745">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b1f97-746">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="b1f97-746">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="b1f97-747">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="b1f97-747">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="b1f97-748">Exemple générique d’un fichier de configuration INI :</span><span class="sxs-lookup"><span data-stu-id="b1f97-748">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="b1f97-749">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-749">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b1f97-750">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b1f97-750">section0:key0</span></span>
* <span data-ttu-id="b1f97-751">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b1f97-751">section0:key1</span></span>
* <span data-ttu-id="b1f97-752">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="b1f97-752">section1:subsection:key</span></span>
* <span data-ttu-id="b1f97-753">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="b1f97-753">section2:subsection0:key</span></span>
* <span data-ttu-id="b1f97-754">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="b1f97-754">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="b1f97-755">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="b1f97-755">JSON Configuration Provider</span></span>

<span data-ttu-id="b1f97-756"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier JSON lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b1f97-756">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="b1f97-757">Pour activer la configuration du fichier JSON, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-757">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b1f97-758">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="b1f97-758">Overloads permit specifying:</span></span>

* <span data-ttu-id="b1f97-759">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="b1f97-759">Whether the file is optional.</span></span>
* <span data-ttu-id="b1f97-760">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="b1f97-760">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b1f97-761">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="b1f97-761">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="b1f97-762">`AddJsonFile` est appelé automatiquement deux fois lors de l’initialisation d’un nouveau générateur d’hôte `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b1f97-762">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b1f97-763">La méthode est appelée pour charger la configuration à partir de :</span><span class="sxs-lookup"><span data-stu-id="b1f97-763">The method is called to load configuration from:</span></span>

* <span data-ttu-id="b1f97-764">*appsettings.json* : Ce fichier est lu en premier.</span><span class="sxs-lookup"><span data-stu-id="b1f97-764">*appsettings.json* : This file is read first.</span></span> <span data-ttu-id="b1f97-765">La version de l’environnement du fichier peut remplacer les valeurs fournies par le *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="b1f97-765">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="b1f97-766">*appSettings. {Environment}. JSON* : la version de l’environnement du fichier est chargée à partir de [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="b1f97-766">*appsettings.{Environment}.json* : The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="b1f97-767">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="b1f97-767">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="b1f97-768">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="b1f97-768">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b1f97-769">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="b1f97-769">Environment variables.</span></span>
* <span data-ttu-id="b1f97-770">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="b1f97-770">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="b1f97-771">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="b1f97-771">Command-line arguments.</span></span>

<span data-ttu-id="b1f97-772">Le Fournisseur de configuration JSON est établi en premier.</span><span class="sxs-lookup"><span data-stu-id="b1f97-772">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="b1f97-773">Par conséquent, les secrets utilisateur, les variables d’environnement et les arguments de ligne de commande remplacent la configuration définie par les fichiers *appsettings* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-773">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="b1f97-774">Appelez `ConfigureAppConfiguration` lors de la génération de l’hôte pour spécifier la configuration de l’application pour les fichiers autres que *appsettings.json* et *appSettings. { Environnement}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-774">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json* :</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="b1f97-775">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="b1f97-775">**Example**</span></span>

<span data-ttu-id="b1f97-776">L’exemple d’application tire parti de la méthode de commodité statique `CreateDefaultBuilder` pour créer l’hôte, ce qui comprend deux appels à `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-776">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="b1f97-777">Le premier appel à `AddJsonFile` charge la configuration à partir de *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-777">The first call to `AddJsonFile` loads configuration from *appsettings.json* :</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="b1f97-778">Le deuxième appel à `AddJsonFile` charge la configuration à partir de *appSettings. { Environnement}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-778">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json* .</span></span> <span data-ttu-id="b1f97-779">Pour *appsettings.Development.js* dans l’exemple d’application, le fichier suivant est chargé :</span><span class="sxs-lookup"><span data-stu-id="b1f97-779">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="b1f97-780">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-780">Run the sample app.</span></span> <span data-ttu-id="b1f97-781">Ouvrez un navigateur vers l’application avec l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-781">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b1f97-782">La sortie contient des paires clé-valeur pour la configuration en fonction de l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-782">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="b1f97-783">Le niveau de journalisation de la clé `Logging:LogLevel:Default` est `Debug` lors de l’exécution de l’application dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="b1f97-783">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="b1f97-784">Exécutez à nouveau l’exemple d’application dans l’environnement de production :</span><span class="sxs-lookup"><span data-stu-id="b1f97-784">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="b1f97-785">Ouvrez le fichier *Properties/launchSettings.js* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-785">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="b1f97-786">Dans le `ConfigurationSample` profil, remplacez la valeur de la `ASPNETCORE_ENVIRONMENT` variable d’environnement par `Production` .</span><span class="sxs-lookup"><span data-stu-id="b1f97-786">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="b1f97-787">Enregistrez le fichier et exécutez l’application avec `dotnet run` dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="b1f97-787">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="b1f97-788">Les paramètres de la *appsettings.Development.js* qui ne se substituent plus aux paramètres dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b1f97-788">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json* .</span></span> <span data-ttu-id="b1f97-789">Le niveau de journalisation de la clé `Logging:LogLevel:Default` est `Warning` .</span><span class="sxs-lookup"><span data-stu-id="b1f97-789">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="b1f97-790">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="b1f97-790">XML Configuration Provider</span></span>

<span data-ttu-id="b1f97-791"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier XML lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b1f97-791">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="b1f97-792">Pour activer la configuration du fichier XML, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-792">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b1f97-793">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="b1f97-793">Overloads permit specifying:</span></span>

* <span data-ttu-id="b1f97-794">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="b1f97-794">Whether the file is optional.</span></span>
* <span data-ttu-id="b1f97-795">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="b1f97-795">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b1f97-796">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="b1f97-796">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="b1f97-797">Le nœud racine du fichier de configuration est ignoré lorsque les paires clé-valeur de la configuration sont créées.</span><span class="sxs-lookup"><span data-stu-id="b1f97-797">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="b1f97-798">Ne spécifiez pas une définition de type de document (DTD) ou un espace de noms dans le fichier.</span><span class="sxs-lookup"><span data-stu-id="b1f97-798">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="b1f97-799">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="b1f97-799">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="b1f97-800">Les fichiers de configuration XML peuvent utiliser des noms d’éléments distincts pour les sections répétitives :</span><span class="sxs-lookup"><span data-stu-id="b1f97-800">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="b1f97-801">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-801">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b1f97-802">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b1f97-802">section0:key0</span></span>
* <span data-ttu-id="b1f97-803">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b1f97-803">section0:key1</span></span>
* <span data-ttu-id="b1f97-804">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b1f97-804">section1:key0</span></span>
* <span data-ttu-id="b1f97-805">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b1f97-805">section1:key1</span></span>

<span data-ttu-id="b1f97-806">Les éléments répétitifs qui utilisent le même nom d’élément fonctionnent si l’attribut `name` est utilisé pour distinguer les éléments :</span><span class="sxs-lookup"><span data-stu-id="b1f97-806">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="b1f97-807">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-807">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b1f97-808">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="b1f97-808">section:section0:key:key0</span></span>
* <span data-ttu-id="b1f97-809">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="b1f97-809">section:section0:key:key1</span></span>
* <span data-ttu-id="b1f97-810">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="b1f97-810">section:section1:key:key0</span></span>
* <span data-ttu-id="b1f97-811">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="b1f97-811">section:section1:key:key1</span></span>

<span data-ttu-id="b1f97-812">Les attributs peuvent être utilisés pour fournir des valeurs :</span><span class="sxs-lookup"><span data-stu-id="b1f97-812">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="b1f97-813">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-813">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b1f97-814">key:attribute</span><span class="sxs-lookup"><span data-stu-id="b1f97-814">key:attribute</span></span>
* <span data-ttu-id="b1f97-815">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="b1f97-815">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="b1f97-816">Fournisseur de configuration clé par fichier</span><span class="sxs-lookup"><span data-stu-id="b1f97-816">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="b1f97-817">Le <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> utilise les fichiers d’un répertoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-817">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="b1f97-818">La clé est le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="b1f97-818">The key is the file name.</span></span> <span data-ttu-id="b1f97-819">La valeur contient le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="b1f97-819">The value contains the file's contents.</span></span> <span data-ttu-id="b1f97-820">Le Fournisseur de configuration Clé par fichier est utilisé dans les scénarios d’hébergement de Docker.</span><span class="sxs-lookup"><span data-stu-id="b1f97-820">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="b1f97-821">Pour activer la configuration clé par fichier, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-821">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="b1f97-822">Le `directoryPath` aux fichiers doit être un chemin d’accès absolu.</span><span class="sxs-lookup"><span data-stu-id="b1f97-822">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="b1f97-823">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="b1f97-823">Overloads permit specifying:</span></span>

* <span data-ttu-id="b1f97-824">Un délégué `Action<KeyPerFileConfigurationSource>` qui configure la source.</span><span class="sxs-lookup"><span data-stu-id="b1f97-824">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="b1f97-825">Si le répertoire est facultatif et le chemin d’accès au répertoire.</span><span class="sxs-lookup"><span data-stu-id="b1f97-825">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="b1f97-826">Le double trait de soulignement (`__`) est utilisé comme un délimiteur de clé de configuration dans les noms de fichiers.</span><span class="sxs-lookup"><span data-stu-id="b1f97-826">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="b1f97-827">Par exemple, le nom de fichier `Logging__LogLevel__System` génère la clé de configuration `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-827">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="b1f97-828">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="b1f97-828">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="b1f97-829">Fournisseur de configuration de mémoire</span><span class="sxs-lookup"><span data-stu-id="b1f97-829">Memory Configuration Provider</span></span>

<span data-ttu-id="b1f97-830">Le <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> utilise une collection en mémoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-830">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="b1f97-831">Pour activer la configuration de la collection en mémoire, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-831">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b1f97-832">Le fournisseur de configuration peut être initialisé avec un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-832">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="b1f97-833">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="b1f97-834">Dans l’exemple suivant, un dictionnaire de configuration est créé :</span><span class="sxs-lookup"><span data-stu-id="b1f97-834">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="b1f97-835">Le dictionnaire est utilisé avec un appel à `AddInMemoryCollection` pour fournir la configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-835">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="b1f97-836">GetValue</span><span class="sxs-lookup"><span data-stu-id="b1f97-836">GetValue</span></span>

<span data-ttu-id="b1f97-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrait une valeur unique de la configuration avec une clé spécifiée et la convertit en type de non-collection spécifié.</span><span class="sxs-lookup"><span data-stu-id="b1f97-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="b1f97-838">Une surcharge accepte une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="b1f97-838">An overload accepts a default value.</span></span>

<span data-ttu-id="b1f97-839">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="b1f97-839">The following example:</span></span>

* <span data-ttu-id="b1f97-840">Extrait la valeur de chaîne de la configuration avec la clé `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-840">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="b1f97-841">Si `NumberKey` est introuvable dans les clés de configuration, la valeur par défaut de `99` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="b1f97-841">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="b1f97-842">Tape la valeur comme `int`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-842">Types the value as an `int`.</span></span>
* <span data-ttu-id="b1f97-843">Stocke la valeur dans la propriété `NumberConfig` pour une utilisation par la page.</span><span class="sxs-lookup"><span data-stu-id="b1f97-843">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="b1f97-844">GetSection, GetChildren et Exists</span><span class="sxs-lookup"><span data-stu-id="b1f97-844">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="b1f97-845">Pour les exemples qui suivent, utilisez le fichier JSON suivant.</span><span class="sxs-lookup"><span data-stu-id="b1f97-845">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="b1f97-846">Quatre clés se trouvent dans deux sections, dont l’une inclut deux sous-sections :</span><span class="sxs-lookup"><span data-stu-id="b1f97-846">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="b1f97-847">Lorsque le fichier est lu dans la configuration, les clés hiérarchiques uniques suivantes sont créées pour stocker les valeurs de la configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-847">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="b1f97-848">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b1f97-848">section0:key0</span></span>
* <span data-ttu-id="b1f97-849">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b1f97-849">section0:key1</span></span>
* <span data-ttu-id="b1f97-850">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b1f97-850">section1:key0</span></span>
* <span data-ttu-id="b1f97-851">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b1f97-851">section1:key1</span></span>
* <span data-ttu-id="b1f97-852">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="b1f97-852">section2:subsection0:key0</span></span>
* <span data-ttu-id="b1f97-853">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="b1f97-853">section2:subsection0:key1</span></span>
* <span data-ttu-id="b1f97-854">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="b1f97-854">section2:subsection1:key0</span></span>
* <span data-ttu-id="b1f97-855">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="b1f97-855">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="b1f97-856">GetSection</span><span class="sxs-lookup"><span data-stu-id="b1f97-856">GetSection</span></span>

<span data-ttu-id="b1f97-857">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrait une sous-section de la configuration avec la clé de sous-section spécifiée.</span><span class="sxs-lookup"><span data-stu-id="b1f97-857">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="b1f97-858">Pour retourner un <xref:Microsoft.Extensions.Configuration.IConfigurationSection> contenant uniquement les paires clé-valeur dans `section1`, appelez `GetSection` et fournissez le nom de section :</span><span class="sxs-lookup"><span data-stu-id="b1f97-858">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="b1f97-859">`configSection` n’a pas de valeur, seulement une clé et un chemin.</span><span class="sxs-lookup"><span data-stu-id="b1f97-859">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="b1f97-860">De même, pour obtenir les valeurs des clés dans `section2:subsection0`, appelez `GetSection` et fournissez le chemin d’accès de la section :</span><span class="sxs-lookup"><span data-stu-id="b1f97-860">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="b1f97-861">`GetSection` ne retourne jamais `null`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-861">`GetSection` never returns `null`.</span></span> <span data-ttu-id="b1f97-862">Si aucune section correspondante n’est trouvée, une valeur `IConfigurationSection` vide est retournée.</span><span class="sxs-lookup"><span data-stu-id="b1f97-862">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="b1f97-863">Quand `GetSection` retourne une section correspondante, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="b1f97-863">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="b1f97-864"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> et <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> sont retournés quand la section existe.</span><span class="sxs-lookup"><span data-stu-id="b1f97-864">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="b1f97-865">GetChildren</span><span class="sxs-lookup"><span data-stu-id="b1f97-865">GetChildren</span></span>

<span data-ttu-id="b1f97-866">Un appel à [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) sur `section2` obtient un `IEnumerable<IConfigurationSection>` qui inclut :</span><span class="sxs-lookup"><span data-stu-id="b1f97-866">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="b1f97-867">Exists</span><span class="sxs-lookup"><span data-stu-id="b1f97-867">Exists</span></span>

<span data-ttu-id="b1f97-868">Utilisez [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pour déterminer si une section de configuration existe :</span><span class="sxs-lookup"><span data-stu-id="b1f97-868">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="b1f97-869">Compte tenu des données d’exemple, `sectionExists` est `false`, car il n’y a pas de section `section2:subsection2` dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-869">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="b1f97-870">Établir une liaison à un graphe d’objets</span><span class="sxs-lookup"><span data-stu-id="b1f97-870">Bind to an object graph</span></span>

<span data-ttu-id="b1f97-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> est capable de lier l’intégralité d’un graphe d’objets POCO.</span><span class="sxs-lookup"><span data-stu-id="b1f97-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="b1f97-872">Comme pour la liaison d’un objet simple, seules les propriétés accessibles en lecture/écriture publiques sont liées.</span><span class="sxs-lookup"><span data-stu-id="b1f97-872">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="b1f97-873">L’exemple contient un modèle `TvShow` dont le graphe d’objets inclut les classes `Metadata` et `Actors` ( *Models/TvShow.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="b1f97-873">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes ( *Models/TvShow.cs* ):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="b1f97-874">L’exemple d’application a un fichier *tvshow.xml* contenant les données de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-874">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="b1f97-875">La configuration est liée à l’intégralité du graphe d’objets `TvShow` avec la méthode `Bind`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-875">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="b1f97-876">L’instance liée est affectée à une propriété pour le rendu :</span><span class="sxs-lookup"><span data-stu-id="b1f97-876">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="b1f97-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) lie et retourne le type spécifié.</span><span class="sxs-lookup"><span data-stu-id="b1f97-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="b1f97-878">Il est plus pratique d’utiliser `Get<T>` que `Bind`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-878">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="b1f97-879">Le code suivant montre comment utiliser `Get<T>` avec l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="b1f97-879">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="b1f97-880">Lier un tableau à une classe</span><span class="sxs-lookup"><span data-stu-id="b1f97-880">Bind an array to a class</span></span>

<span data-ttu-id="b1f97-881">*L’exemple d’application illustre les concepts abordés dans cette section.*</span><span class="sxs-lookup"><span data-stu-id="b1f97-881">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="b1f97-882"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-882">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b1f97-883">Tout format de tableau qui expose un segment de clé numérique ( `:0:` , `:1:` , &hellip; `:{n}:` ) est capable d’effectuer une liaison de tableau à un tableau de classes POCO.</span><span class="sxs-lookup"><span data-stu-id="b1f97-883">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="b1f97-884">La liaison est fournie par convention.</span><span class="sxs-lookup"><span data-stu-id="b1f97-884">Binding is provided by convention.</span></span> <span data-ttu-id="b1f97-885">Les fournisseurs de configuration personnalisés ne sont pas obligés d’implémenter la liaison de tableau.</span><span class="sxs-lookup"><span data-stu-id="b1f97-885">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="b1f97-886">**Traitement de tableau en mémoire**</span><span class="sxs-lookup"><span data-stu-id="b1f97-886">**In-memory array processing**</span></span>

<span data-ttu-id="b1f97-887">Observez les valeurs et les clés de configuration indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="b1f97-887">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="b1f97-888">Clé</span><span class="sxs-lookup"><span data-stu-id="b1f97-888">Key</span></span>             | <span data-ttu-id="b1f97-889">Valeur</span><span class="sxs-lookup"><span data-stu-id="b1f97-889">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="b1f97-890">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="b1f97-890">array:entries:0</span></span> | <span data-ttu-id="b1f97-891">value0</span><span class="sxs-lookup"><span data-stu-id="b1f97-891">value0</span></span> |
| <span data-ttu-id="b1f97-892">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="b1f97-892">array:entries:1</span></span> | <span data-ttu-id="b1f97-893">valeur1</span><span class="sxs-lookup"><span data-stu-id="b1f97-893">value1</span></span> |
| <span data-ttu-id="b1f97-894">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="b1f97-894">array:entries:2</span></span> | <span data-ttu-id="b1f97-895">valeur2</span><span class="sxs-lookup"><span data-stu-id="b1f97-895">value2</span></span> |
| <span data-ttu-id="b1f97-896">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="b1f97-896">array:entries:4</span></span> | <span data-ttu-id="b1f97-897">value4</span><span class="sxs-lookup"><span data-stu-id="b1f97-897">value4</span></span> |
| <span data-ttu-id="b1f97-898">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="b1f97-898">array:entries:5</span></span> | <span data-ttu-id="b1f97-899">value5</span><span class="sxs-lookup"><span data-stu-id="b1f97-899">value5</span></span> |

<span data-ttu-id="b1f97-900">Ces clés et valeurs sont chargées dans l’exemple d’application à l’aide du Fournisseur de configuration de mémoire :</span><span class="sxs-lookup"><span data-stu-id="b1f97-900">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="b1f97-901">Le tableau ignore une valeur pour l’index &num;3.</span><span class="sxs-lookup"><span data-stu-id="b1f97-901">The array skips a value for index &num;3.</span></span> <span data-ttu-id="b1f97-902">Le binder de configuration n’est pas capable de lier des valeurs null ou de créer des entrées null dans les objets liés, ce qui deviendra clair dans un moment lorsque le résultat de liaison de ce tableau à un objet est illustré.</span><span class="sxs-lookup"><span data-stu-id="b1f97-902">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="b1f97-903">Dans l’exemple d’application, une classe POCO est disponible pour contenir les données de configuration liées :</span><span class="sxs-lookup"><span data-stu-id="b1f97-903">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="b1f97-904">Les données de configuration sont liées à l’objet :</span><span class="sxs-lookup"><span data-stu-id="b1f97-904">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="b1f97-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) la syntaxe peut également être utilisée, ce qui se traduit par un code plus compact :</span><span class="sxs-lookup"><span data-stu-id="b1f97-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="b1f97-906">L’objet lié, une instance de `ArrayExample`, reçoit les données de tableau à partir de la configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-906">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="b1f97-907">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="b1f97-907">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="b1f97-908">Valeur `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="b1f97-908">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="b1f97-909">0</span><span class="sxs-lookup"><span data-stu-id="b1f97-909">0</span></span>                            | <span data-ttu-id="b1f97-910">value0</span><span class="sxs-lookup"><span data-stu-id="b1f97-910">value0</span></span>                       |
| <span data-ttu-id="b1f97-911">1</span><span class="sxs-lookup"><span data-stu-id="b1f97-911">1</span></span>                            | <span data-ttu-id="b1f97-912">valeur1</span><span class="sxs-lookup"><span data-stu-id="b1f97-912">value1</span></span>                       |
| <span data-ttu-id="b1f97-913">2</span><span class="sxs-lookup"><span data-stu-id="b1f97-913">2</span></span>                            | <span data-ttu-id="b1f97-914">valeur2</span><span class="sxs-lookup"><span data-stu-id="b1f97-914">value2</span></span>                       |
| <span data-ttu-id="b1f97-915">3</span><span class="sxs-lookup"><span data-stu-id="b1f97-915">3</span></span>                            | <span data-ttu-id="b1f97-916">value4</span><span class="sxs-lookup"><span data-stu-id="b1f97-916">value4</span></span>                       |
| <span data-ttu-id="b1f97-917">4</span><span class="sxs-lookup"><span data-stu-id="b1f97-917">4</span></span>                            | <span data-ttu-id="b1f97-918">value5</span><span class="sxs-lookup"><span data-stu-id="b1f97-918">value5</span></span>                       |

<span data-ttu-id="b1f97-919">L’index &num;3 dans l’objet lié contient les données de configuration pour la clé de configuration `array:4` et sa valeur de `value4`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-919">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="b1f97-920">Lorsque des données de configuration contenant un tableau sont liées, les index de tableau dans les clés de configuration sont simplement utilisés pour itérer les données de configuration lors de la création de l’objet.</span><span class="sxs-lookup"><span data-stu-id="b1f97-920">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="b1f97-921">Une valeur null ne peut pas être conservée dans des données de configuration, et une entrée à valeur null n’est pas créée dans un objet lié quand un tableau dans des clés de configuration ignore un ou plusieurs index.</span><span class="sxs-lookup"><span data-stu-id="b1f97-921">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="b1f97-922">L’élément de configuration manquant pour l’index &num;3 peut être fourni avant la liaison à l’instance `ArrayExample` par n’importe quel fournisseur de configuration qui génère la paire clé-valeur correcte dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-922">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="b1f97-923">Si l’exemple inclut un Fournisseur de configuration JSON supplémentaire avec la paire clé-valeur manquante, `ArrayExample.Entries` correspond à l’intégralité du tableau de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-923">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="b1f97-924">*missing_value.json* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-924">*missing_value.json* :</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="b1f97-925">Dans `ConfigureAppConfiguration` :</span><span class="sxs-lookup"><span data-stu-id="b1f97-925">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="b1f97-926">La paire clé-valeur indiquée dans le tableau est chargée dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-926">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="b1f97-927">Clé</span><span class="sxs-lookup"><span data-stu-id="b1f97-927">Key</span></span>             | <span data-ttu-id="b1f97-928">Valeur</span><span class="sxs-lookup"><span data-stu-id="b1f97-928">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="b1f97-929">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="b1f97-929">array:entries:3</span></span> | <span data-ttu-id="b1f97-930">valeur3</span><span class="sxs-lookup"><span data-stu-id="b1f97-930">value3</span></span> |

<span data-ttu-id="b1f97-931">Si l’instance de classe `ArrayExample` est liée une fois que le Fournisseur de configuration JSON inclut l’entrée pour l’index &num;3, le tableau `ArrayExample.Entries` inclut la valeur.</span><span class="sxs-lookup"><span data-stu-id="b1f97-931">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="b1f97-932">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="b1f97-932">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="b1f97-933">Valeur `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="b1f97-933">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="b1f97-934">0</span><span class="sxs-lookup"><span data-stu-id="b1f97-934">0</span></span>                            | <span data-ttu-id="b1f97-935">value0</span><span class="sxs-lookup"><span data-stu-id="b1f97-935">value0</span></span>                       |
| <span data-ttu-id="b1f97-936">1</span><span class="sxs-lookup"><span data-stu-id="b1f97-936">1</span></span>                            | <span data-ttu-id="b1f97-937">valeur1</span><span class="sxs-lookup"><span data-stu-id="b1f97-937">value1</span></span>                       |
| <span data-ttu-id="b1f97-938">2</span><span class="sxs-lookup"><span data-stu-id="b1f97-938">2</span></span>                            | <span data-ttu-id="b1f97-939">valeur2</span><span class="sxs-lookup"><span data-stu-id="b1f97-939">value2</span></span>                       |
| <span data-ttu-id="b1f97-940">3</span><span class="sxs-lookup"><span data-stu-id="b1f97-940">3</span></span>                            | <span data-ttu-id="b1f97-941">valeur3</span><span class="sxs-lookup"><span data-stu-id="b1f97-941">value3</span></span>                       |
| <span data-ttu-id="b1f97-942">4</span><span class="sxs-lookup"><span data-stu-id="b1f97-942">4</span></span>                            | <span data-ttu-id="b1f97-943">value4</span><span class="sxs-lookup"><span data-stu-id="b1f97-943">value4</span></span>                       |
| <span data-ttu-id="b1f97-944">5</span><span class="sxs-lookup"><span data-stu-id="b1f97-944">5</span></span>                            | <span data-ttu-id="b1f97-945">value5</span><span class="sxs-lookup"><span data-stu-id="b1f97-945">value5</span></span>                       |

<span data-ttu-id="b1f97-946">**Traitement de tableau JSON**</span><span class="sxs-lookup"><span data-stu-id="b1f97-946">**JSON array processing**</span></span>

<span data-ttu-id="b1f97-947">Si un fichier JSON contient un tableau, les clés de configuration sont créés pour les éléments du tableau avec un index de section basé sur zéro.</span><span class="sxs-lookup"><span data-stu-id="b1f97-947">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="b1f97-948">Dans le fichier de configuration suivant, `subsection` est un tableau :</span><span class="sxs-lookup"><span data-stu-id="b1f97-948">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="b1f97-949">Le Fournisseur de configuration JSON lit les données de configuration dans les paires clé-valeur suivantes :</span><span class="sxs-lookup"><span data-stu-id="b1f97-949">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="b1f97-950">Clé</span><span class="sxs-lookup"><span data-stu-id="b1f97-950">Key</span></span>                     | <span data-ttu-id="b1f97-951">Valeur</span><span class="sxs-lookup"><span data-stu-id="b1f97-951">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="b1f97-952">json_array:key</span><span class="sxs-lookup"><span data-stu-id="b1f97-952">json_array:key</span></span>          | <span data-ttu-id="b1f97-953">valueA</span><span class="sxs-lookup"><span data-stu-id="b1f97-953">valueA</span></span> |
| <span data-ttu-id="b1f97-954">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="b1f97-954">json_array:subsection:0</span></span> | <span data-ttu-id="b1f97-955">valueB</span><span class="sxs-lookup"><span data-stu-id="b1f97-955">valueB</span></span> |
| <span data-ttu-id="b1f97-956">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="b1f97-956">json_array:subsection:1</span></span> | <span data-ttu-id="b1f97-957">valueC</span><span class="sxs-lookup"><span data-stu-id="b1f97-957">valueC</span></span> |
| <span data-ttu-id="b1f97-958">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="b1f97-958">json_array:subsection:2</span></span> | <span data-ttu-id="b1f97-959">valueD</span><span class="sxs-lookup"><span data-stu-id="b1f97-959">valueD</span></span> |

<span data-ttu-id="b1f97-960">Dans l’exemple d’application, la classe POCO suivante est disponible pour lier les paires clé-valeur de configuration :</span><span class="sxs-lookup"><span data-stu-id="b1f97-960">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="b1f97-961">Après la liaison, `JsonArrayExample.Key` contient la valeur `valueA`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-961">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="b1f97-962">Les valeurs de la sous-section sont stockées dans la propriété de tableau POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-962">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="b1f97-963">Index `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="b1f97-963">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="b1f97-964">Valeur `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="b1f97-964">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="b1f97-965">0</span><span class="sxs-lookup"><span data-stu-id="b1f97-965">0</span></span>                                   | <span data-ttu-id="b1f97-966">valueB</span><span class="sxs-lookup"><span data-stu-id="b1f97-966">valueB</span></span>                              |
| <span data-ttu-id="b1f97-967">1</span><span class="sxs-lookup"><span data-stu-id="b1f97-967">1</span></span>                                   | <span data-ttu-id="b1f97-968">valueC</span><span class="sxs-lookup"><span data-stu-id="b1f97-968">valueC</span></span>                              |
| <span data-ttu-id="b1f97-969">2</span><span class="sxs-lookup"><span data-stu-id="b1f97-969">2</span></span>                                   | <span data-ttu-id="b1f97-970">valueD</span><span class="sxs-lookup"><span data-stu-id="b1f97-970">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="b1f97-971">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="b1f97-971">Custom configuration provider</span></span>

<span data-ttu-id="b1f97-972">L’exemple d’application montre comment créer un fournisseur de configuration de base qui lit les paires clé-valeur de configuration à partir d’une base de données à l’aide [d’Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="b1f97-972">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="b1f97-973">Le fournisseur présente les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="b1f97-973">The provider has the following characteristics:</span></span>

* <span data-ttu-id="b1f97-974">La base de données en mémoire EF est utilisée à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-974">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="b1f97-975">Pour utiliser une base de données qui nécessite une chaîne de connexion, implémentez un autre `ConfigurationBuilder` pour fournir la chaîne de connexion à partir d’un autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="b1f97-975">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="b1f97-976">Le fournisseur lit une table de base de données dans la configuration au démarrage.</span><span class="sxs-lookup"><span data-stu-id="b1f97-976">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="b1f97-977">Le fournisseur n’interroge pas la base de données par clé.</span><span class="sxs-lookup"><span data-stu-id="b1f97-977">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="b1f97-978">Le rechargement en cas de changement n’est pas implémenté, par conséquent, la mise à jour la base de données après le démarrage de l’application n’a aucun effet sur la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-978">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="b1f97-979">Définissez une entité `EFConfigurationValue` pour le stockage des valeurs de configuration dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="b1f97-979">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="b1f97-980">*Models/EFConfigurationValue.cs* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-980">*Models/EFConfigurationValue.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="b1f97-981">Ajoutez un `EFConfigurationContext` pour stocker les valeurs configurées et y accéder.</span><span class="sxs-lookup"><span data-stu-id="b1f97-981">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="b1f97-982">*EFConfigurationProvider/EFConfigurationContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-982">*EFConfigurationProvider/EFConfigurationContext.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="b1f97-983">Créez une classe qui implémente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-983">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="b1f97-984">*EFConfigurationProvider/EFConfigurationSource.cs* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-984">*EFConfigurationProvider/EFConfigurationSource.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="b1f97-985">Créez le fournisseur de configuration personnalisé en héritant de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-985">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="b1f97-986">Le fournisseur de configuration initialise la base de données quand elle est vide.</span><span class="sxs-lookup"><span data-stu-id="b1f97-986">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="b1f97-987">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-987">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="b1f97-988">Une méthode d’extension `AddEFConfiguration` permet d’ajouter la source de configuration à un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-988">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="b1f97-989">*Extensions/EntityFrameworkExtensions.cs* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-989">*Extensions/EntityFrameworkExtensions.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="b1f97-990">Le code suivant montre comment utiliser le `EFConfigurationProvider` personnalisé dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="b1f97-990">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="b1f97-991">Accéder à la configuration lors du démarrage</span><span class="sxs-lookup"><span data-stu-id="b1f97-991">Access configuration during startup</span></span>

<span data-ttu-id="b1f97-992">Injectez `IConfiguration` dans le constructeur `Startup` pour accéder aux valeurs de configuration dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b1f97-992">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b1f97-993">Pour accéder à la configuration dans `Startup.Configure`, injectez `IConfiguration` directement dans la méthode ou utilisez l’instance à partir du constructeur :</span><span class="sxs-lookup"><span data-stu-id="b1f97-993">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="b1f97-994">Pour obtenir un exemple d’accès à la configuration à l’aide des méthodes pratiques de démarrage, consultez [Démarrage de l’application : méthodes pratiques](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="b1f97-994">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="b1f97-995">Configuration de l’accès dans une Razor page pages ou une vue MVC</span><span class="sxs-lookup"><span data-stu-id="b1f97-995">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="b1f97-996">Pour accéder aux paramètres de configuration dans une Razor page pages ou une vue MVC, ajoutez une [directive using](xref:mvc/views/razor#using) ([référence C# : directive using](/dotnet/csharp/language-reference/keywords/using-directive)) pour l' [ espace de nomsMicrosoft.Extensions.Configfiguration](xref:Microsoft.Extensions.Configuration) et injectez <xref:Microsoft.Extensions.Configuration.IConfiguration> dans la page ou la vue.</span><span class="sxs-lookup"><span data-stu-id="b1f97-996">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="b1f97-997">Dans une Razor page pages :</span><span class="sxs-lookup"><span data-stu-id="b1f97-997">In a Razor Pages page:</span></span>

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

<span data-ttu-id="b1f97-998">Dans une vue MVC :</span><span class="sxs-lookup"><span data-stu-id="b1f97-998">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="b1f97-999">Ajouter la configuration à partir d’un assembly externe</span><span class="sxs-lookup"><span data-stu-id="b1f97-999">Add configuration from an external assembly</span></span>

<span data-ttu-id="b1f97-1000">Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1f97-1000">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="b1f97-1001">Pour plus d'informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b1f97-1001">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1f97-1002">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b1f97-1002">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
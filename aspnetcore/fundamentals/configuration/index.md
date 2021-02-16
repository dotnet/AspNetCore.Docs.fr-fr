---
title: Configuration dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser l’API de configuration pour configurer une application ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 1/29/2021
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
ms.openlocfilehash: 0f069b049889f7caade493e238ac7a23db5e79af
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536290"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="094ba-103">Configuration dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="094ba-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="094ba-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="094ba-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="094ba-105">La configuration dans ASP.NET Core est effectuée à l’aide d’un ou de plusieurs [fournisseurs de configuration](#cp).</span><span class="sxs-lookup"><span data-stu-id="094ba-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="094ba-106">Les fournisseurs de configuration lisent les données de configuration des paires clé-valeur à l’aide d’une variété de sources de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="094ba-107">Les fichiers de paramètres, tels que *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="094ba-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="094ba-108">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="094ba-108">Environment variables</span></span>
* <span data-ttu-id="094ba-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="094ba-109">Azure Key Vault</span></span>
* <span data-ttu-id="094ba-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="094ba-110">Azure App Configuration</span></span>
* <span data-ttu-id="094ba-111">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="094ba-111">Command-line arguments</span></span>
* <span data-ttu-id="094ba-112">Fournisseurs personnalisés, installés ou créés</span><span class="sxs-lookup"><span data-stu-id="094ba-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="094ba-113">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="094ba-113">Directory files</span></span>
* <span data-ttu-id="094ba-114">Objets .NET en mémoire</span><span class="sxs-lookup"><span data-stu-id="094ba-114">In-memory .NET objects</span></span>

<span data-ttu-id="094ba-115">Cette rubrique fournit des informations sur la configuration dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="094ba-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="094ba-116">Pour plus d’informations sur l’utilisation de la configuration dans les applications console, consultez [Configuration .net](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="094ba-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="094ba-117">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="094ba-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="094ba-118">Configuration par défaut</span><span class="sxs-lookup"><span data-stu-id="094ba-118">Default configuration</span></span>

<span data-ttu-id="094ba-119">ASP.NET Core les applications Web créées avec [dotnet New](/dotnet/core/tools/dotnet-new) ou Visual Studio génèrent le code suivant :</span><span class="sxs-lookup"><span data-stu-id="094ba-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="094ba-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fournit la configuration par défaut de l’application dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="094ba-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="094ba-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : ajoute un existant `IConfiguration` en tant que source.</span><span class="sxs-lookup"><span data-stu-id="094ba-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="094ba-122">Dans le cas de configuration par défaut, ajoute la configuration d' [hôte](#hvac) et la définit en tant que première source de la configuration de l' _application_ .</span><span class="sxs-lookup"><span data-stu-id="094ba-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="094ba-123">[appsettings.json](#appsettingsjson) utilisation du [fournisseur de configuration JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="094ba-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="094ba-124">*appSettings.* `Environment` *. JSON* à l’aide du [fournisseur de configuration JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="094ba-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="094ba-125">Par exemple, *appSettings*. ***Production \* \* _._json* et *appSettings*. \* \* \* développement** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="094ba-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="094ba-126">[Secrets d’application](xref:security/app-secrets) lorsque l’application s’exécute dans l' `Development` environnement.</span><span class="sxs-lookup"><span data-stu-id="094ba-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="094ba-127">Variables d’environnement à l’aide du [fournisseur de configuration des variables d’environnement](#evcp).</span><span class="sxs-lookup"><span data-stu-id="094ba-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="094ba-128">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line).</span><span class="sxs-lookup"><span data-stu-id="094ba-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="094ba-129">Les fournisseurs de configuration ajoutés ultérieurement remplacent les paramètres de clé précédents.</span><span class="sxs-lookup"><span data-stu-id="094ba-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="094ba-130">Par exemple, si `MyKey` est défini à la fois dans *appsettings.json* et dans l’environnement, la valeur d’environnement est utilisée.</span><span class="sxs-lookup"><span data-stu-id="094ba-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="094ba-131">À l’aide des fournisseurs de configuration par défaut, le  [fournisseur de configuration de ligne de commande](#clcp) remplace tous les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="094ba-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="094ba-132">Pour plus d’informations sur `CreateDefaultBuilder` , consultez [paramètres par défaut du générateur](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="094ba-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="094ba-133">Le code suivant affiche les fournisseurs de configuration activés dans l’ordre dans lequel ils ont été ajoutés :</span><span class="sxs-lookup"><span data-stu-id="094ba-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="094ba-134">Prenons le *appsettings.json* fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="094ba-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="094ba-135">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="094ba-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="094ba-136">La configuration par défaut <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="094ba-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="094ba-137">*appSettings.* `Environment` *. JSON* : par exemple, *appSettings*. **Les fichiers de *production \* \* _._json* et *appSettings*. \* \* \* Development** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="094ba-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="094ba-138">La version de l’environnement du fichier est chargée à partir de [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="094ba-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="094ba-139">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="094ba-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="094ba-140">*appSettings*. `Environment` . les valeurs *JSON* remplacent les clés dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="094ba-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="094ba-141">Par exemple, par défaut :</span><span class="sxs-lookup"><span data-stu-id="094ba-141">For example, by default:</span></span>

* <span data-ttu-id="094ba-142">Dans le développement, *appSettings*. \***Development** _._json \* configuration remplace les valeurs trouvées dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="094ba-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="094ba-143">En production, *appSettings*. \***production** _._json \* configuration remplace les valeurs trouvées dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="094ba-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="094ba-144">Par exemple, lors du déploiement de l’application sur Azure.</span><span class="sxs-lookup"><span data-stu-id="094ba-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="094ba-145">Lier des données de configuration hiérarchiques à l’aide du modèle options</span><span class="sxs-lookup"><span data-stu-id="094ba-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="094ba-146">À l’aide de la configuration [par défaut](#default) , de *appsettings.json* et de *appSettings.* `Environment` les fichiers *. JSON* sont activés avec [reloadOnChange : true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="094ba-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="094ba-147">Modifications apportées *appsettings.json* aux *appSettings et.* `Environment` le fichier *. JSON* ***après*** le démarrage de l’application est lu par le [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="094ba-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="094ba-148">Pour plus d’informations sur l’ajout de fichiers de configuration JSON supplémentaires, consultez [fournisseur de configuration JSON](#jcp) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="094ba-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="094ba-149">Combinaison de la collection de services</span><span class="sxs-lookup"><span data-stu-id="094ba-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a><span data-ttu-id="094ba-150">Sécurité et secrets de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="094ba-150">Security and user secrets</span></span>

<span data-ttu-id="094ba-151">Instructions relatives aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-151">Configuration data guidelines:</span></span>

* <span data-ttu-id="094ba-152">Ne stockez jamais des mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte clair.</span><span class="sxs-lookup"><span data-stu-id="094ba-152">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="094ba-153">L’outil [Gestionnaire de secret](xref:security/app-secrets) peut être utilisé pour stocker les secrets en développement.</span><span class="sxs-lookup"><span data-stu-id="094ba-153">The [Secret Manager](xref:security/app-secrets) tool can be used to store secrets in development.</span></span>
* <span data-ttu-id="094ba-154">N’utilisez aucun secret de production dans les environnements de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="094ba-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="094ba-155">Spécifiez les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans un référentiel de code source.</span><span class="sxs-lookup"><span data-stu-id="094ba-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="094ba-156">Par [défaut](#default), la source de configuration des secrets de l’utilisateur est inscrite après les sources de configuration JSON.</span><span class="sxs-lookup"><span data-stu-id="094ba-156">By [default](#default), the user secrets configuration source is registered after the JSON configuration sources.</span></span> <span data-ttu-id="094ba-157">Par conséquent, les clés de secrets d’utilisateur sont prioritaires sur les clés dans *appsettings.json* et *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="094ba-157">Therefore, user secrets keys take precedence over keys in *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="094ba-158">Pour plus d’informations sur le stockage des mots de passe ou d’autres données sensibles :</span><span class="sxs-lookup"><span data-stu-id="094ba-158">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="094ba-159"><xref:security/app-secrets>: Fournit des conseils sur l’utilisation de variables d’environnement pour stocker des données sensibles.</span><span class="sxs-lookup"><span data-stu-id="094ba-159"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="094ba-160">L’outil Gestionnaire de secret utilise le [fournisseur de configuration de fichiers](#fcp) pour stocker les secrets d’utilisateur dans un fichier JSON sur le système local.</span><span class="sxs-lookup"><span data-stu-id="094ba-160">The Secret Manager tool uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="094ba-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stocke en toute sécurité des secrets d’application pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="094ba-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="094ba-162">Pour plus d’informations, consultez <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="094ba-162">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="094ba-163">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="094ba-163">Environment variables</span></span>

<span data-ttu-id="094ba-164">À l’aide de la configuration [par défaut](#default) , le <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> charge la configuration à partir des paires clé-valeur de variable d’environnement après la lecture *appsettings.json* , *appSettings.* `Environment` *. JSON* et les [secrets](xref:security/app-secrets)de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="094ba-164">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [user secrets](xref:security/app-secrets).</span></span> <span data-ttu-id="094ba-165">Par conséquent, les valeurs de clés lues à partir de l’environnement remplacent les valeurs lues à partir de *appsettings.json* *appSettings.* `Environment` *. JSON* et les secrets de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="094ba-165">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and user secrets.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="094ba-166">Les `set` commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="094ba-166">The following `set` commands:</span></span>

* <span data-ttu-id="094ba-167">Définissez les clés et les valeurs d’environnement de l' [exemple précédent](#appsettingsjson) sur Windows.</span><span class="sxs-lookup"><span data-stu-id="094ba-167">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="094ba-168">Testez les paramètres lors de l’utilisation de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="094ba-168">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="094ba-169">La `dotnet run` commande doit être exécutée dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="094ba-169">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="094ba-170">Paramètres d’environnement précédents :</span><span class="sxs-lookup"><span data-stu-id="094ba-170">The preceding environment settings:</span></span>

* <span data-ttu-id="094ba-171">Sont uniquement définies dans les processus lancés à partir de la fenêtre de commande dans laquelle ils ont été définis.</span><span class="sxs-lookup"><span data-stu-id="094ba-171">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="094ba-172">Ne seront pas lues par les navigateurs lancés avec Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="094ba-172">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="094ba-173">Les commandes [setx](/windows-server/administration/windows-commands/setx) suivantes peuvent être utilisées pour définir les clés et les valeurs d’environnement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="094ba-173">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="094ba-174">Contrairement à `set` , `setx` les paramètres sont conservés.</span><span class="sxs-lookup"><span data-stu-id="094ba-174">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="094ba-175">`/M` définit la variable dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="094ba-175">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="094ba-176">Si le `/M` commutateur n’est pas utilisé, une variable d’environnement utilisateur est définie.</span><span class="sxs-lookup"><span data-stu-id="094ba-176">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="094ba-177">Pour vérifier que les commandes précédentes remplacent *appsettings.json* et *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="094ba-177">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="094ba-178">Avec Visual Studio : quittez et redémarrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="094ba-178">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="094ba-179">Avec l’interface CLI : démarrez une nouvelle fenêtre de commande et entrez `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="094ba-179">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="094ba-180">Appelez <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> avec une chaîne pour spécifier un préfixe pour les variables d’environnement :</span><span class="sxs-lookup"><span data-stu-id="094ba-180">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="094ba-181">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="094ba-181">In the preceding code:</span></span>

* <span data-ttu-id="094ba-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` est ajouté après les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="094ba-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="094ba-183">Pour obtenir un exemple de classement des fournisseurs de configuration, consultez [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="094ba-183">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="094ba-184">Les variables d’environnement définies avec le `MyCustomPrefix_` préfixe remplacent les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="094ba-184">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="094ba-185">Cela comprend les variables d’environnement sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="094ba-185">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="094ba-186">Le préfixe est supprimé lorsque les paires clé-valeur de configuration sont lues.</span><span class="sxs-lookup"><span data-stu-id="094ba-186">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="094ba-187">Les commandes suivantes testent le préfixe personnalisé :</span><span class="sxs-lookup"><span data-stu-id="094ba-187">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="094ba-188">La [configuration par défaut](#default) charge les variables d’environnement et les arguments de ligne de commande préfixé avec `DOTNET_` et `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="094ba-188">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="094ba-189">Les `DOTNET_` `ASPNETCORE_` préfixes et sont utilisés par ASP.net Core pour la configuration de l' [hôte et](xref:fundamentals/host/generic-host#host-configuration)de l’application, mais pas pour la configuration de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="094ba-189">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="094ba-190">Pour plus d’informations sur la configuration de l’hôte et de l’application, consultez [hôte générique .net](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="094ba-190">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="094ba-191">Dans [Azure App service](https://azure.microsoft.com/services/app-service/), sélectionnez **nouveau paramètre d’application** dans la page **paramètres > configuration** .</span><span class="sxs-lookup"><span data-stu-id="094ba-191">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="094ba-192">Azure App Service paramètres de l’application sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="094ba-192">Azure App Service application settings are:</span></span>

* <span data-ttu-id="094ba-193">Chiffré au repos et transmis sur un canal chiffré.</span><span class="sxs-lookup"><span data-stu-id="094ba-193">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="094ba-194">Exposés en tant que variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="094ba-194">Exposed as environment variables.</span></span>

<span data-ttu-id="094ba-195">Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="094ba-195">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="094ba-196">Consultez [préfixes de chaîne de connexion](#constr) pour plus d’informations sur les chaînes de connexion de base de données Azure.</span><span class="sxs-lookup"><span data-stu-id="094ba-196">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="094ba-197">Affectation des noms de variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="094ba-197">Naming of environment variables</span></span>

<span data-ttu-id="094ba-198">Les noms des variables d’environnement reflètent la structure d’un *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="094ba-198">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="094ba-199">Chaque élément de la hiérarchie est séparé par un trait de soulignement double (préférable) ou par deux-points.</span><span class="sxs-lookup"><span data-stu-id="094ba-199">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="094ba-200">Lorsque la structure de l’élément comprend un tableau, l’index du tableau doit être traité comme un nom d’élément supplémentaire dans ce chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="094ba-200">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="094ba-201">Considérez le *appsettings.json* fichier suivant et ses valeurs équivalentes représentées sous la forme de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="094ba-201">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

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

<span data-ttu-id="094ba-202">**variables d’environnement**</span><span class="sxs-lookup"><span data-stu-id="094ba-202">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-generated-launchsettingsjson"></a><span data-ttu-id="094ba-203">Variables d’environnement définies dans les launchSettings.jsgénérées sur</span><span class="sxs-lookup"><span data-stu-id="094ba-203">Environment variables set in generated launchSettings.json</span></span>

<span data-ttu-id="094ba-204">Les variables d’environnement définies dans *launchSettings.jslors de* la substitution de celles définies dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="094ba-204">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span> <span data-ttu-id="094ba-205">Par exemple, les modèles Web ASP.NET Core génèrent un *launchSettings.jssur* un fichier qui définit la configuration du point de terminaison sur :</span><span class="sxs-lookup"><span data-stu-id="094ba-205">For example, the ASP.NET Core web templates generate a *launchSettings.json* file that sets the endpoint configuration to:</span></span>

```json
"applicationUrl": "https://localhost:5001;http://localhost:5000"
```

<span data-ttu-id="094ba-206">La configuration de `applicationUrl` définit la `ASPNETCORE_URLS` variable d’environnement et remplace les valeurs définies dans l’environnement.</span><span class="sxs-lookup"><span data-stu-id="094ba-206">Configuring the `applicationUrl` sets the `ASPNETCORE_URLS` environment variable and overrides values set in the environment.</span></span>

### <a name="escape-environment-variables-on-linux"></a><span data-ttu-id="094ba-207">Variables d’environnement d’échappement sur Linux</span><span class="sxs-lookup"><span data-stu-id="094ba-207">Escape environment variables on Linux</span></span>

<span data-ttu-id="094ba-208">Sur Linux, la valeur des variables d’environnement URL doit être placée dans une séquence d’échappement pour `systemd` pouvoir être analysée.</span><span class="sxs-lookup"><span data-stu-id="094ba-208">On Linux, the value of URL environment variables must be escaped so `systemd` can parse it.</span></span> <span data-ttu-id="094ba-209">Utiliser l’outil Linux `systemd-escape` qui génère `http:--localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="094ba-209">Use the linux tool `systemd-escape` which yields `http:--localhost:5001`</span></span>
 
 ```cmd
 groot@terminus:~$ systemd-escape http://localhost:5001
 http:--localhost:5001
 ```

### <a name="display-environment-variables"></a><span data-ttu-id="094ba-210">Afficher les variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="094ba-210">Display environment variables</span></span>

<span data-ttu-id="094ba-211">Le code suivant affiche les variables d’environnement et les valeurs au démarrage de l’application, ce qui peut être utile lors du débogage des paramètres d’environnement :</span><span class="sxs-lookup"><span data-stu-id="094ba-211">The following code displays the environment variables and values on application startup, which can be helpful when debugging environment settings:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples_snippets/5.x/Program.cs?name=snippet)]

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="094ba-212">Ligne de commande</span><span class="sxs-lookup"><span data-stu-id="094ba-212">Command-line</span></span>

<span data-ttu-id="094ba-213">À l’aide de la configuration [par défaut](#default) , le <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> charge la configuration à partir de paires clé-valeur d’argument de ligne de commande après les sources de configuration suivantes :</span><span class="sxs-lookup"><span data-stu-id="094ba-213">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="094ba-214">*appsettings.json* et *appSettings*. `Environment` . fichiers *JSON* .</span><span class="sxs-lookup"><span data-stu-id="094ba-214">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="094ba-215">[Secrets](xref:security/app-secrets) de l’application dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="094ba-215">[App secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="094ba-216">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="094ba-216">Environment variables.</span></span>

<span data-ttu-id="094ba-217">Par [défaut](#default), les valeurs de configuration définies sur la ligne de commande remplacent les valeurs de configuration définies avec tous les autres fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-217">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="094ba-218">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="094ba-218">Command-line arguments</span></span>

<span data-ttu-id="094ba-219">La commande suivante définit des clés et des valeurs à l’aide de `=` :</span><span class="sxs-lookup"><span data-stu-id="094ba-219">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="094ba-220">La commande suivante définit des clés et des valeurs à l’aide de `/` :</span><span class="sxs-lookup"><span data-stu-id="094ba-220">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="094ba-221">La commande suivante définit des clés et des valeurs à l’aide de `--` :</span><span class="sxs-lookup"><span data-stu-id="094ba-221">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="094ba-222">Valeur de la clé :</span><span class="sxs-lookup"><span data-stu-id="094ba-222">The key value:</span></span>

* <span data-ttu-id="094ba-223">Doit suivre `=` ou la clé doit avoir un préfixe `--` ou `/` lorsque la valeur suit un espace.</span><span class="sxs-lookup"><span data-stu-id="094ba-223">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="094ba-224">N’est pas obligatoire si `=` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="094ba-224">Isn't required if `=` is used.</span></span> <span data-ttu-id="094ba-225">Par exemple : `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="094ba-225">For example, `MySetting=`.</span></span>

<span data-ttu-id="094ba-226">Dans la même commande, ne mélangez pas les paires clé-valeur d’argument de ligne de commande qui utilisent `=` des paires clé-valeur utilisant un espace.</span><span class="sxs-lookup"><span data-stu-id="094ba-226">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="094ba-227">Correspondances de commutateur</span><span class="sxs-lookup"><span data-stu-id="094ba-227">Switch mappings</span></span>

<span data-ttu-id="094ba-228">Les mappages de commutateur autorisent la logique de remplacement de nom de **clé** .</span><span class="sxs-lookup"><span data-stu-id="094ba-228">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="094ba-229">Fournissez un dictionnaire de remplacements de commutateur dans la <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> méthode.</span><span class="sxs-lookup"><span data-stu-id="094ba-229">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="094ba-230">Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="094ba-230">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="094ba-231">Si la clé de ligne de commande est trouvée dans le dictionnaire, la valeur du dictionnaire est retournée pour définir la paire clé-valeur dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-231">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="094ba-232">Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).</span><span class="sxs-lookup"><span data-stu-id="094ba-232">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="094ba-233">Règles des clés du dictionnaire de correspondances de commutateur :</span><span class="sxs-lookup"><span data-stu-id="094ba-233">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="094ba-234">Les commutateurs doivent commencer par `-` ou `--` .</span><span class="sxs-lookup"><span data-stu-id="094ba-234">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="094ba-235">Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.</span><span class="sxs-lookup"><span data-stu-id="094ba-235">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="094ba-236">Pour utiliser un dictionnaire de mappages de commutateur, transmettez-le à l’appel à `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="094ba-236">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="094ba-237">Le code suivant montre les valeurs de clé pour les clés remplacées :</span><span class="sxs-lookup"><span data-stu-id="094ba-237">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="094ba-238">La commande suivante fonctionne pour tester le remplacement de la clé :</span><span class="sxs-lookup"><span data-stu-id="094ba-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="094ba-239">Pour les applications qui utilisent des mappages de commutateurs, l’appel à `CreateDefaultBuilder` ne doit pas passer d’arguments.</span><span class="sxs-lookup"><span data-stu-id="094ba-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="094ba-240">L' `CreateDefaultBuilder` appel de la méthode `AddCommandLine` n’inclut pas de commutateurs mappés, et il n’existe aucun moyen de passer le dictionnaire de mappage de commutateur à `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="094ba-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="094ba-241">La solution ne consiste pas à passer les arguments à `CreateDefaultBuilder` , mais à autoriser la méthode de la `ConfigurationBuilder` méthode `AddCommandLine` à traiter à la fois les arguments et le dictionnaire de mappage de commutateur.</span><span class="sxs-lookup"><span data-stu-id="094ba-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="094ba-242">Données de configuration hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="094ba-242">Hierarchical configuration data</span></span>

<span data-ttu-id="094ba-243">L’API de configuration lit les données de configuration hiérarchiques en aplatit les données hiérarchiques à l’aide d’un délimiteur dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="094ba-244">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le  *appsettings.json* fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="094ba-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="094ba-245">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="094ba-246">La meilleure façon de lire des données de configuration hiérarchiques consiste à utiliser le modèle d’options.</span><span class="sxs-lookup"><span data-stu-id="094ba-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="094ba-247">Pour plus d’informations, consultez [lier des données de configuration hiérarchiques](#optpat) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="094ba-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="094ba-248">Les méthodes <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> et <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sont disponibles pour isoler les sections et les enfants d’une section dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="094ba-249">Ces méthodes sont décrites plus loin dans [GetSection, GetChildren et Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="094ba-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="094ba-250">Clés et valeurs de configuration</span><span class="sxs-lookup"><span data-stu-id="094ba-250">Configuration keys and values</span></span>

<span data-ttu-id="094ba-251">Clés de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-251">Configuration keys:</span></span>

* <span data-ttu-id="094ba-252">Ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="094ba-252">Are case-insensitive.</span></span> <span data-ttu-id="094ba-253">Par exemple, `ConnectionString` et `connectionstring` sont traités en tant que clés équivalentes.</span><span class="sxs-lookup"><span data-stu-id="094ba-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="094ba-254">Si une clé et une valeur sont définies dans plusieurs fournisseurs de configuration, la valeur du dernier fournisseur ajouté est utilisée.</span><span class="sxs-lookup"><span data-stu-id="094ba-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="094ba-255">Pour plus d’informations, consultez [configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="094ba-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="094ba-256">Clés hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="094ba-256">Hierarchical keys</span></span>
  * <span data-ttu-id="094ba-257">Dans l’API Configuration, un séparateur sous forme de signe deux-points (`:`) fonctionne sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="094ba-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="094ba-258">Dans les variables d’environnement, un séparateur sous forme de signe deux-points peut ne pas fonctionner sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="094ba-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="094ba-259">Un trait de soulignement double, `__` , est pris en charge par toutes les plateformes et est automatiquement converti en deux-points `:` .</span><span class="sxs-lookup"><span data-stu-id="094ba-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="094ba-260">Dans Azure Key Vault, les clés hiérarchiques utilisent `--` comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="094ba-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="094ba-261">Le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) remplace automatiquement `--` par un `:` lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="094ba-262"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="094ba-263">La liaison de tableau est décrite dans la section [Lier un tableau à une classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="094ba-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="094ba-264">Valeurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-264">Configuration values:</span></span>

* <span data-ttu-id="094ba-265">Sont des chaînes.</span><span class="sxs-lookup"><span data-stu-id="094ba-265">Are strings.</span></span>
* <span data-ttu-id="094ba-266">Les valeurs NULL ne peuvent pas être stockées dans la configuration ou liées à des objets.</span><span class="sxs-lookup"><span data-stu-id="094ba-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="094ba-267">Fournisseurs de configuration</span><span class="sxs-lookup"><span data-stu-id="094ba-267">Configuration providers</span></span>

<span data-ttu-id="094ba-268">Le tableau suivant présente les fournisseurs de configuration disponibles pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="094ba-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="094ba-269">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="094ba-269">Provider</span></span> | <span data-ttu-id="094ba-270">Fournit la configuration à partir de</span><span class="sxs-lookup"><span data-stu-id="094ba-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="094ba-271">Fournisseur de configuration Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="094ba-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="094ba-272">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="094ba-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="094ba-273">Fournisseur de configuration Azure App</span><span class="sxs-lookup"><span data-stu-id="094ba-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="094ba-274">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="094ba-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="094ba-275">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="094ba-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="094ba-276">Paramètres de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="094ba-276">Command-line parameters</span></span> |
| [<span data-ttu-id="094ba-277">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="094ba-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="094ba-278">Source personnalisée</span><span class="sxs-lookup"><span data-stu-id="094ba-278">Custom source</span></span> |
| [<span data-ttu-id="094ba-279">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="094ba-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="094ba-280">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="094ba-280">Environment variables</span></span> |
| [<span data-ttu-id="094ba-281">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="094ba-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="094ba-282">Fichiers INI, JSON et XML</span><span class="sxs-lookup"><span data-stu-id="094ba-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="094ba-283">Fournisseur de configuration de clé par fichier</span><span class="sxs-lookup"><span data-stu-id="094ba-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="094ba-284">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="094ba-284">Directory files</span></span> |
| [<span data-ttu-id="094ba-285">Fournisseur de configuration de la mémoire</span><span class="sxs-lookup"><span data-stu-id="094ba-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="094ba-286">Collections en mémoire</span><span class="sxs-lookup"><span data-stu-id="094ba-286">In-memory collections</span></span> |
| [<span data-ttu-id="094ba-287">Secrets utilisateur</span><span class="sxs-lookup"><span data-stu-id="094ba-287">User secrets</span></span>](xref:security/app-secrets) | <span data-ttu-id="094ba-288">Fichier dans le répertoire de profil utilisateur</span><span class="sxs-lookup"><span data-stu-id="094ba-288">File in the user profile directory</span></span> |

<span data-ttu-id="094ba-289">Les sources de configuration sont lues dans l’ordre dans lequel leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="094ba-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="094ba-290">Commandez des fournisseurs de configuration dans le code pour répondre aux priorités des sources de configuration sous-jacentes requises par l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="094ba-291">Une séquence type des fournisseurs de configuration est la suivante :</span><span class="sxs-lookup"><span data-stu-id="094ba-291">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="094ba-292">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="094ba-292">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="094ba-293">Secrets utilisateur</span><span class="sxs-lookup"><span data-stu-id="094ba-293">User secrets</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="094ba-294">Variables d’environnement à l’aide du [fournisseur de configuration des variables d’environnement](#evcp).</span><span class="sxs-lookup"><span data-stu-id="094ba-294">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="094ba-295">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="094ba-295">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="094ba-296">Une pratique courante consiste à ajouter le dernier fournisseur de configuration de ligne de commande dans une série de fournisseurs pour permettre aux arguments de ligne de commande de remplacer la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="094ba-296">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="094ba-297">La séquence de fournisseurs précédente est utilisée dans la [configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="094ba-297">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="094ba-298">Préfixes des chaînes de connexion</span><span class="sxs-lookup"><span data-stu-id="094ba-298">Connection string prefixes</span></span>

<span data-ttu-id="094ba-299">L’API de configuration a des règles de traitement spéciales pour quatre variables d’environnement de chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="094ba-299">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="094ba-300">Ces chaînes de connexion sont impliquées dans la configuration des chaînes de connexion Azure pour l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-300">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="094ba-301">Les variables d’environnement avec les préfixes indiqués dans le tableau sont chargées dans l’application avec la [configuration par défaut](#default) ou lorsqu’aucun préfixe n’est fourni à `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="094ba-301">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="094ba-302">Préfixe de la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="094ba-302">Connection string prefix</span></span> | <span data-ttu-id="094ba-303">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="094ba-303">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="094ba-304">Fournisseur personnalisé</span><span class="sxs-lookup"><span data-stu-id="094ba-304">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="094ba-305">MySQL</span><span class="sxs-lookup"><span data-stu-id="094ba-305">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="094ba-306">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="094ba-306">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="094ba-307">SQL Server</span><span class="sxs-lookup"><span data-stu-id="094ba-307">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="094ba-308">Quand une variable d’environnement est découverte et chargée dans la configuration avec l’un des quatre préfixes indiqués dans le tableau :</span><span class="sxs-lookup"><span data-stu-id="094ba-308">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="094ba-309">La clé de configuration est créée en supprimant le préfixe de la variable d’environnement et en ajoutant une section de clé de configuration (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="094ba-309">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="094ba-310">Une nouvelle paire clé-valeur de configuration est créée qui représente le fournisseur de connexion de base de données (à l’exception de `CUSTOMCONNSTR_`, qui ne possède aucun fournisseur indiqué).</span><span class="sxs-lookup"><span data-stu-id="094ba-310">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="094ba-311">Clé de variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="094ba-311">Environment variable key</span></span> | <span data-ttu-id="094ba-312">Clé de configuration convertie</span><span class="sxs-lookup"><span data-stu-id="094ba-312">Converted configuration key</span></span> | <span data-ttu-id="094ba-313">Entrée de configuration de fournisseur</span><span class="sxs-lookup"><span data-stu-id="094ba-313">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="094ba-314">Entrée de configuration non créée.</span><span class="sxs-lookup"><span data-stu-id="094ba-314">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="094ba-315">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="094ba-315">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="094ba-316">Valeur: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="094ba-316">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="094ba-317">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="094ba-317">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="094ba-318">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="094ba-318">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="094ba-319">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="094ba-319">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="094ba-320">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="094ba-320">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="094ba-321">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="094ba-321">File configuration provider</span></span>

<span data-ttu-id="094ba-322"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> est la classe de base pour charger la configuration à partir du système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="094ba-322"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="094ba-323">Les fournisseurs de configuration suivants dérivent de `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="094ba-323">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="094ba-324">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="094ba-324">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="094ba-325">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="094ba-325">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="094ba-326">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="094ba-326">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="094ba-327">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="094ba-327">INI configuration provider</span></span>

<span data-ttu-id="094ba-328"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier INI lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="094ba-328">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="094ba-329">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-329">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="094ba-330">Dans le code précédent, les paramètres des *MyIniConfig.ini* et  *MyIniConfig*. `Environment` les fichiers *ini* sont remplacés par les paramètres dans le :</span><span class="sxs-lookup"><span data-stu-id="094ba-330">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="094ba-331">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="094ba-331">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="094ba-332">[Fournisseur de configuration de ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="094ba-332">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="094ba-333">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyIniConfig.ini* suivant :</span><span class="sxs-lookup"><span data-stu-id="094ba-333">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="094ba-334">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="094ba-334">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="094ba-335">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="094ba-335">JSON configuration provider</span></span>

<span data-ttu-id="094ba-336">Le <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge la configuration à partir de paires clé-valeur de fichier JSON.</span><span class="sxs-lookup"><span data-stu-id="094ba-336">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="094ba-337">Les surcharges peuvent spécifier :</span><span class="sxs-lookup"><span data-stu-id="094ba-337">Overloads can specify:</span></span>

* <span data-ttu-id="094ba-338">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="094ba-338">Whether the file is optional.</span></span>
* <span data-ttu-id="094ba-339">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="094ba-339">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="094ba-340">Considérez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="094ba-340">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="094ba-341">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="094ba-341">The preceding code:</span></span>

* <span data-ttu-id="094ba-342">Configure le fournisseur de configuration JSON pour charger le *MyConfig.jssur* le fichier avec les options suivantes :</span><span class="sxs-lookup"><span data-stu-id="094ba-342">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="094ba-343">`optional: true`: Le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="094ba-343">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="094ba-344">`reloadOnChange: true` : Le fichier est rechargé lorsque des modifications sont enregistrées.</span><span class="sxs-lookup"><span data-stu-id="094ba-344">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="094ba-345">Lit les [fournisseurs de configuration par défaut](#default) avant l' *MyConfig.jssur* le fichier.</span><span class="sxs-lookup"><span data-stu-id="094ba-345">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="094ba-346">Les paramètres dans le *MyConfig.js* paramètre de remplacement de fichier dans les fournisseurs de configuration par défaut, y compris le [fournisseur de configuration des variables d’environnement](#evcp) et le fournisseur de configuration de ligne de [commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="094ba-346">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="094ba-347">En général, vous ***ne souhaitez pas*** qu’une valeur de substitution de fichier JSON personnalisée soit définie dans le fournisseur de configuration des [variables d’environnement](#evcp) et dans le fournisseur de configuration de [ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="094ba-347">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="094ba-348">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-348">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="094ba-349">Dans le code précédent, les paramètres de la *MyConfig.jssur* et  *MyConfig*. `Environment` . fichiers *JSON* :</span><span class="sxs-lookup"><span data-stu-id="094ba-349">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="094ba-350">Substituez les paramètres dans *appsettings.json* et *appSettings*. `Environment` fichiers *JSON* .</span><span class="sxs-lookup"><span data-stu-id="094ba-350">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="094ba-351">Sont remplacées par les paramètres dans le [fournisseur de configuration des variables d’environnement](#evcp) et le fournisseur de configuration de ligne de [commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="094ba-351">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="094ba-352">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient les  *MyConfig.jssuivantes sur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="094ba-352">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="094ba-353">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="094ba-353">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="094ba-354">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="094ba-354">XML configuration provider</span></span>

<span data-ttu-id="094ba-355"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier XML lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="094ba-355">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="094ba-356">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-356">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="094ba-357">Dans le code précédent, les paramètres des *MyXMLFile.xml* et  *MyXMLFile*. `Environment` les fichiers *XML* sont remplacés par les paramètres dans le :</span><span class="sxs-lookup"><span data-stu-id="094ba-357">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="094ba-358">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="094ba-358">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="094ba-359">[Fournisseur de configuration de ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="094ba-359">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="094ba-360">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyXMLFile.xml* suivant :</span><span class="sxs-lookup"><span data-stu-id="094ba-360">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="094ba-361">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="094ba-361">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="094ba-362">Les éléments répétitifs qui utilisent le même nom d’élément fonctionnent si l’attribut `name` est utilisé pour distinguer les éléments :</span><span class="sxs-lookup"><span data-stu-id="094ba-362">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="094ba-363">Le code suivant lit le fichier de configuration précédent et affiche les clés et les valeurs :</span><span class="sxs-lookup"><span data-stu-id="094ba-363">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="094ba-364">Les attributs peuvent être utilisés pour fournir des valeurs :</span><span class="sxs-lookup"><span data-stu-id="094ba-364">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="094ba-365">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="094ba-365">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="094ba-366">key:attribute</span><span class="sxs-lookup"><span data-stu-id="094ba-366">key:attribute</span></span>
* <span data-ttu-id="094ba-367">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="094ba-367">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="094ba-368">Fournisseur de configuration de clé par fichier</span><span class="sxs-lookup"><span data-stu-id="094ba-368">Key-per-file configuration provider</span></span>

<span data-ttu-id="094ba-369">Le <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> utilise les fichiers d’un répertoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-369">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="094ba-370">La clé est le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="094ba-370">The key is the file name.</span></span> <span data-ttu-id="094ba-371">La valeur contient le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="094ba-371">The value contains the file's contents.</span></span> <span data-ttu-id="094ba-372">Le fournisseur de configuration par fichier clé est utilisé dans les scénarios d’hébergement de l’ancrage.</span><span class="sxs-lookup"><span data-stu-id="094ba-372">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="094ba-373">Pour activer la configuration clé par fichier, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="094ba-373">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="094ba-374">Le `directoryPath` aux fichiers doit être un chemin d’accès absolu.</span><span class="sxs-lookup"><span data-stu-id="094ba-374">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="094ba-375">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="094ba-375">Overloads permit specifying:</span></span>

* <span data-ttu-id="094ba-376">Un délégué `Action<KeyPerFileConfigurationSource>` qui configure la source.</span><span class="sxs-lookup"><span data-stu-id="094ba-376">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="094ba-377">Si le répertoire est facultatif et le chemin d’accès au répertoire.</span><span class="sxs-lookup"><span data-stu-id="094ba-377">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="094ba-378">Le double trait de soulignement (`__`) est utilisé comme un délimiteur de clé de configuration dans les noms de fichiers.</span><span class="sxs-lookup"><span data-stu-id="094ba-378">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="094ba-379">Par exemple, le nom de fichier `Logging__LogLevel__System` génère la clé de configuration `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="094ba-379">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="094ba-380">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="094ba-380">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="094ba-381">Fournisseur de configuration de la mémoire</span><span class="sxs-lookup"><span data-stu-id="094ba-381">Memory configuration provider</span></span>

<span data-ttu-id="094ba-382">Le <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> utilise une collection en mémoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-382">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="094ba-383">Le code suivant ajoute une collection de mémoire au système de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-383">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="094ba-384">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche les paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="094ba-384">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="094ba-385">Dans le code précédent, `config.AddInMemoryCollection(Dict)` est ajouté après les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="094ba-385">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="094ba-386">Pour obtenir un exemple de classement des fournisseurs de configuration, consultez [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="094ba-386">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="094ba-387">Pour un autre exemple, consultez [lier un tableau](#boa) à l’aide de `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="094ba-387">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-5.0"

<a name="kestrel"></a>

## <a name="kestrel-endpoint-configuration"></a><span data-ttu-id="094ba-388">Configuration de point de terminaison Kestrel</span><span class="sxs-lookup"><span data-stu-id="094ba-388">Kestrel endpoint configuration</span></span>

<span data-ttu-id="094ba-389">La configuration de point de terminaison spécifique à Kestrel remplace toutes les configurations [de point de terminaison inter-serveurs](xref:fundamentals/servers/index) .</span><span class="sxs-lookup"><span data-stu-id="094ba-389">Kestrel specific endpoint configuration overrides all [cross-server](xref:fundamentals/servers/index) endpoint configurations.</span></span> <span data-ttu-id="094ba-390">Les configurations de point de terminaison entre serveurs sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="094ba-390">Cross-server endpoint configurations include:</span></span>

  * [<span data-ttu-id="094ba-391">UseUrls</span><span class="sxs-lookup"><span data-stu-id="094ba-391">UseUrls</span></span>](xref:fundamentals/host/web-host#server-urls)
  * <span data-ttu-id="094ba-392">`--urls` sur la [ligne de commande](xref:fundamentals/configuration/index#command-line)</span><span class="sxs-lookup"><span data-stu-id="094ba-392">`--urls` on the [command line](xref:fundamentals/configuration/index#command-line)</span></span>
  * <span data-ttu-id="094ba-393">La [variable d’environnement](xref:fundamentals/configuration/index#environment-variables)`ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="094ba-393">The [environment variable](xref:fundamentals/configuration/index#environment-variables) `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="094ba-394">Considérez le *appsettings.json* fichier suivant utilisé dans une application web ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="094ba-394">Consider the following *appsettings.json* file used in an ASP.NET Core web app:</span></span>

[!code-json[](~/fundamentals/configuration/index/samples_snippets/5.x/appsettings.json?highlight=2-8)]

<span data-ttu-id="094ba-395">Lorsque le balisage en surbrillance précédent est utilisé dans une application Web ASP.NET Core ***et*** que l’application est lancée sur la ligne de commande avec la configuration de point de terminaison inter-serveurs suivante :</span><span class="sxs-lookup"><span data-stu-id="094ba-395">When the preceding highlighted markup is used in an ASP.NET Core web app ***and*** the app is launched on the command line with the following cross-server endpoint configuration:</span></span>

`dotnet run --urls="https://localhost:7777"`

<span data-ttu-id="094ba-396">Kestrel crée une liaison avec le point de terminaison configuré spécifiquement pour Kestrel dans le *appsettings.json* fichier ( `https://localhost:9999` ) et non `https://localhost:7777` .</span><span class="sxs-lookup"><span data-stu-id="094ba-396">Kestrel binds to the endpoint configured specifically for Kestrel in the *appsettings.json* file (`https://localhost:9999`) and not `https://localhost:7777`.</span></span>

<span data-ttu-id="094ba-397">Considérez le point de terminaison spécifique à Kestrel configuré comme une variable d’environnement :</span><span class="sxs-lookup"><span data-stu-id="094ba-397">Consider the Kestrel specific endpoint configured as an environment variable:</span></span>

`set Kestrel__Endpoints__Https__Url=https://localhost:8888`

<span data-ttu-id="094ba-398">Dans la variable d’environnement précédente, `Https` est le nom du point de terminaison spécifique à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="094ba-398">In the preceding environment variable, `Https` is the name of the Kestrel specific endpoint.</span></span> <span data-ttu-id="094ba-399">Le *appsettings.json* fichier précédent définit également un point de terminaison spécifique à Kestrel nommé `Https` .</span><span class="sxs-lookup"><span data-stu-id="094ba-399">The preceding *appsettings.json* file also defines a Kestrel specific endpoint named `Https`.</span></span> <span data-ttu-id="094ba-400">Par [défaut](#default-configuration), les variables d’environnement utilisant le [fournisseur de configuration des variables d’environnement](#evcp) sont lues après *appSettings.* `Environment` *. JSON*, par conséquent, la variable d’environnement précédente est utilisée pour le `Https` point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="094ba-400">By [default](#default-configuration), environment variables using the [Environment Variables configuration provider](#evcp) are read after *appsettings.*`Environment`*.json*, therefore, the preceding environment variable is used for the `Https` endpoint.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-3.0"

## <a name="getvalue"></a><span data-ttu-id="094ba-401">GetValue</span><span class="sxs-lookup"><span data-stu-id="094ba-401">GetValue</span></span>

<span data-ttu-id="094ba-402">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrait une valeur unique de la configuration avec une clé spécifiée et la convertit en type spécifié :</span><span class="sxs-lookup"><span data-stu-id="094ba-402">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="094ba-403">Dans le code précédent, si est `NumberKey` introuvable dans la configuration, la valeur par défaut de `99` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="094ba-403">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="094ba-404">GetSection, GetChildren et Exists</span><span class="sxs-lookup"><span data-stu-id="094ba-404">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="094ba-405">Pour les exemples qui suivent, prenez en compte les *MySubsection.jssuivantes sur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="094ba-405">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="094ba-406">Le code suivant ajoute *MySubsection.js* aux fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-406">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="094ba-407">GetSection</span><span class="sxs-lookup"><span data-stu-id="094ba-407">GetSection</span></span>

<span data-ttu-id="094ba-408">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) retourne une sous-section de configuration avec la clé de sous-section spécifiée.</span><span class="sxs-lookup"><span data-stu-id="094ba-408">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="094ba-409">Le code suivant retourne des valeurs pour `section1` :</span><span class="sxs-lookup"><span data-stu-id="094ba-409">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="094ba-410">Le code suivant retourne des valeurs pour `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="094ba-410">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="094ba-411">`GetSection` ne retourne jamais `null`.</span><span class="sxs-lookup"><span data-stu-id="094ba-411">`GetSection` never returns `null`.</span></span> <span data-ttu-id="094ba-412">Si aucune section correspondante n’est trouvée, une valeur `IConfigurationSection` vide est retournée.</span><span class="sxs-lookup"><span data-stu-id="094ba-412">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="094ba-413">Quand `GetSection` retourne une section correspondante, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="094ba-413">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="094ba-414"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> et <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> sont retournés quand la section existe.</span><span class="sxs-lookup"><span data-stu-id="094ba-414">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="094ba-415">GetChildren et EXISTS</span><span class="sxs-lookup"><span data-stu-id="094ba-415">GetChildren and Exists</span></span>

<span data-ttu-id="094ba-416">Le code suivant appelle [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) et retourne des valeurs pour `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="094ba-416">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="094ba-417">Le code précédent appelle [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pour vérifier l’existence de la section :</span><span class="sxs-lookup"><span data-stu-id="094ba-417">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="094ba-418">Lier un tableau</span><span class="sxs-lookup"><span data-stu-id="094ba-418">Bind an array</span></span>

<span data-ttu-id="094ba-419">[ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) prend en charge les tableaux de liaison aux objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-419">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="094ba-420">Tout format de tableau qui expose un segment de clé numérique est capable d’effectuer une liaison de tableau à un tableau de classes [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="094ba-420">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="094ba-421">Prenez *MyArray.js* à partir de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="094ba-421">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="094ba-422">Le code suivant ajoute *MyArray.js* aux fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-422">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="094ba-423">Le code suivant lit la configuration et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="094ba-423">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="094ba-424">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="094ba-424">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="094ba-425">Dans la sortie précédente, l’index 3 a la valeur `value40` , ce qui correspond à `"4": "value40",` dans *MyArray.jssur*.</span><span class="sxs-lookup"><span data-stu-id="094ba-425">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="094ba-426">Les index de tableau liés sont continus et non liés à l’index de clé de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-426">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="094ba-427">Le Binder de configuration n’est pas en capacité à lier des valeurs null ou à créer des entrées NULL dans des objets liés</span><span class="sxs-lookup"><span data-stu-id="094ba-427">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="094ba-428">Le code suivant charge la `array:entries` configuration avec la <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="094ba-428">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="094ba-429">Le code suivant lit la configuration dans `arrayDict` `Dictionary` et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="094ba-429">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="094ba-430">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="094ba-430">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="094ba-431">L’index &num;3 dans l’objet lié contient les données de configuration pour la clé de configuration `array:4` et sa valeur de `value4`.</span><span class="sxs-lookup"><span data-stu-id="094ba-431">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="094ba-432">Lorsque les données de configuration contenant un tableau sont liées, les index de tableau dans les clés de configuration sont utilisés pour itérer les données de configuration lors de la création de l’objet.</span><span class="sxs-lookup"><span data-stu-id="094ba-432">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="094ba-433">Une valeur null ne peut pas être conservée dans des données de configuration, et une entrée à valeur null n’est pas créée dans un objet lié quand un tableau dans des clés de configuration ignore un ou plusieurs index.</span><span class="sxs-lookup"><span data-stu-id="094ba-433">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="094ba-434">L’élément de configuration manquant pour l’index &num; 3 peut être fourni avant la liaison à l' `ArrayExample` instance par n’importe quel fournisseur de configuration qui lit la &num; paire clé/valeur de l’index 3.</span><span class="sxs-lookup"><span data-stu-id="094ba-434">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="094ba-435">Examinez le *Value3.jssuivant sur* le fichier à partir de l’exemple de téléchargement :</span><span class="sxs-lookup"><span data-stu-id="094ba-435">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="094ba-436">Le code suivant comprend la configuration de *Value3.jssur* et `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="094ba-436">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="094ba-437">Le code suivant lit la configuration précédente et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="094ba-437">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="094ba-438">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="094ba-438">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="094ba-439">Les fournisseurs de configuration personnalisés ne sont pas obligés d’implémenter la liaison de tableau.</span><span class="sxs-lookup"><span data-stu-id="094ba-439">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="094ba-440">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="094ba-440">Custom configuration provider</span></span>

<span data-ttu-id="094ba-441">L’exemple d’application montre comment créer un fournisseur de configuration de base qui lit les paires clé-valeur de configuration à partir d’une base de données à l’aide [d’Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="094ba-441">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="094ba-442">Le fournisseur présente les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="094ba-442">The provider has the following characteristics:</span></span>

* <span data-ttu-id="094ba-443">La base de données en mémoire EF est utilisée à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="094ba-443">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="094ba-444">Pour utiliser une base de données qui nécessite une chaîne de connexion, implémentez un autre `ConfigurationBuilder` pour fournir la chaîne de connexion à partir d’un autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-444">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="094ba-445">Le fournisseur lit une table de base de données dans la configuration au démarrage.</span><span class="sxs-lookup"><span data-stu-id="094ba-445">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="094ba-446">Le fournisseur n’interroge pas la base de données par clé.</span><span class="sxs-lookup"><span data-stu-id="094ba-446">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="094ba-447">Le rechargement en cas de changement n’est pas implémenté, par conséquent, la mise à jour la base de données après le démarrage de l’application n’a aucun effet sur la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-447">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="094ba-448">Définissez une entité `EFConfigurationValue` pour le stockage des valeurs de configuration dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="094ba-448">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="094ba-449">*Models/EFConfigurationValue.cs* :</span><span class="sxs-lookup"><span data-stu-id="094ba-449">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="094ba-450">Ajoutez un `EFConfigurationContext` pour stocker les valeurs configurées et y accéder.</span><span class="sxs-lookup"><span data-stu-id="094ba-450">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="094ba-451">*EFConfigurationProvider/EFConfigurationContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="094ba-451">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="094ba-452">Créez une classe qui implémente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="094ba-452">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="094ba-453">*EFConfigurationProvider/EFConfigurationSource.cs* :</span><span class="sxs-lookup"><span data-stu-id="094ba-453">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="094ba-454">Créez le fournisseur de configuration personnalisé en héritant de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="094ba-454">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="094ba-455">Le fournisseur de configuration initialise la base de données quand elle est vide.</span><span class="sxs-lookup"><span data-stu-id="094ba-455">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="094ba-456">Étant donné que les [clés de configuration ne](#keys)respectent pas la casse, le dictionnaire utilisé pour initialiser la base de données est créé avec le comparateur ne respectant pas la casse ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="094ba-456">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="094ba-457">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span><span class="sxs-lookup"><span data-stu-id="094ba-457">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="094ba-458">Une méthode d’extension `AddEFConfiguration` permet d’ajouter la source de configuration à un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="094ba-458">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="094ba-459">*Extensions/EntityFrameworkExtensions.cs* :</span><span class="sxs-lookup"><span data-stu-id="094ba-459">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="094ba-460">Le code suivant montre comment utiliser le `EFConfigurationProvider` personnalisé dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="094ba-460">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="094ba-461">Configuration de l’accès au démarrage</span><span class="sxs-lookup"><span data-stu-id="094ba-461">Access configuration in Startup</span></span>

<span data-ttu-id="094ba-462">Le code suivant affiche les données de configuration dans les `Startup` méthodes :</span><span class="sxs-lookup"><span data-stu-id="094ba-462">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="094ba-463">Pour obtenir un exemple d’accès à la configuration à l’aide des méthodes pratiques de démarrage, consultez [Démarrage de l’application : méthodes pratiques](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="094ba-463">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="094ba-464">Configuration de l’accès dans les Razor pages</span><span class="sxs-lookup"><span data-stu-id="094ba-464">Access configuration in Razor Pages</span></span>

<span data-ttu-id="094ba-465">Le code suivant affiche les données de configuration dans une Razor page :</span><span class="sxs-lookup"><span data-stu-id="094ba-465">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="094ba-466">Dans le code suivant, `MyOptions` est ajouté au conteneur de services avec <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> et lié à la configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-466">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="094ba-467">La balise suivante utilise la [`@inject`](xref:mvc/views/razor#inject) Razor directive pour résoudre et afficher les valeurs des options :</span><span class="sxs-lookup"><span data-stu-id="094ba-467">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="094ba-468">Configuration de l’accès dans un fichier de vue MVC</span><span class="sxs-lookup"><span data-stu-id="094ba-468">Access configuration in a MVC view file</span></span>

<span data-ttu-id="094ba-469">Le code suivant affiche les données de configuration dans une vue MVC :</span><span class="sxs-lookup"><span data-stu-id="094ba-469">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="094ba-470">Configurer des options avec un délégué</span><span class="sxs-lookup"><span data-stu-id="094ba-470">Configure options with a delegate</span></span>

<span data-ttu-id="094ba-471">Les options configurées dans un délégué remplacent les valeurs définies dans les fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-471">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="094ba-472">La configuration d’options avec un délégué est illustrée dans l’exemple 2 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-472">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="094ba-473">Dans le code suivant, un <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="094ba-473">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="094ba-474">Il utilise un délégué pour configurer des valeurs pour `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="094ba-474">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="094ba-475">Le code suivant affiche les valeurs des options :</span><span class="sxs-lookup"><span data-stu-id="094ba-475">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="094ba-476">Dans l’exemple précédent, les valeurs de `Option1` et `Option2` sont spécifiées dans, *appsettings.json* puis remplacées par le délégué configuré.</span><span class="sxs-lookup"><span data-stu-id="094ba-476">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="094ba-477">Configuration de l’hôte ou configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="094ba-477">Host versus app configuration</span></span>

<span data-ttu-id="094ba-478">Avant que l’application ne soit configurée et démarrée, un *hôte* est configuré et lancé.</span><span class="sxs-lookup"><span data-stu-id="094ba-478">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="094ba-479">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="094ba-479">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="094ba-480">L’application et l’hôte sont configurés à l’aide des fournisseurs de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="094ba-480">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="094ba-481">Les paires clé-valeur de la configuration de l’hôte sont également incluses dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-481">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="094ba-482">Pour plus d’informations sur l’utilisation des fournisseurs de configuration lors de la génération de l’hôte et l’impact des sources de configuration sur la configuration de l’hôte, consultez <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="094ba-482">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="094ba-483">Configuration de l’hôte par défaut</span><span class="sxs-lookup"><span data-stu-id="094ba-483">Default host configuration</span></span>

<span data-ttu-id="094ba-484">Pour plus de détails sur la configuration par défaut lors de l’utilisation de l’[hôte Web](xref:fundamentals/host/web-host), consultez la [version ASP.NET Core 2.2. de cette rubrique](?view=aspnetcore-2.2&preserve-view=true).</span><span class="sxs-lookup"><span data-stu-id="094ba-484">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2&preserve-view=true).</span></span>

* <span data-ttu-id="094ba-485">La configuration de l’hôte est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="094ba-485">Host configuration is provided from:</span></span>
  * <span data-ttu-id="094ba-486">Les variables d’environnement précédées du préfixe `DOTNET_` (par exemple, `DOTNET_ENVIRONMENT` ) à l’aide du [fournisseur de configuration des variables d’environnement](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="094ba-486">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="094ba-487">Le préfixe (`DOTNET_`) est supprimé lorsque les paires clé-valeur de la configuration sont chargées.</span><span class="sxs-lookup"><span data-stu-id="094ba-487">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="094ba-488">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="094ba-488">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="094ba-489">La configuration par défaut de l’hôte Web est établie (`ConfigureWebHostDefaults`) :</span><span class="sxs-lookup"><span data-stu-id="094ba-489">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="094ba-490">Kestrel est utilisé comme serveur web et configuré à l’aide des fournisseurs de configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-490">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="094ba-491">Ajoutez l’intergiciel de filtrage d’hôtes.</span><span class="sxs-lookup"><span data-stu-id="094ba-491">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="094ba-492">Ajoutez l’intergiciel d’en-têtes transférés si la variable d'environnement `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="094ba-492">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="094ba-493">Activez l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="094ba-493">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="094ba-494">Autre configuration</span><span class="sxs-lookup"><span data-stu-id="094ba-494">Other configuration</span></span>

<span data-ttu-id="094ba-495">Cette rubrique se rapporte uniquement à la configuration de l' *application*.</span><span class="sxs-lookup"><span data-stu-id="094ba-495">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="094ba-496">D’autres aspects de l’exécution et de l’hébergement des applications ASP.NET Core sont configurés à l’aide des fichiers de configuration non traités dans cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="094ba-496">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="094ba-497">*launch.js* / *launchSettings.jssur* sont des fichiers de configuration d’outils pour l’environnement de développement, décrits ci-après :</span><span class="sxs-lookup"><span data-stu-id="094ba-497">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="094ba-498">Dans <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="094ba-498">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="094ba-499">Dans l’ensemble de la documentation dans lequel les fichiers sont utilisés pour configurer des applications ASP.NET Core pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="094ba-499">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="094ba-500">*web.config* est un fichier de configuration de serveur, décrit dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="094ba-500">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="094ba-501">Les variables d’environnement définies dans *launchSettings.jslors de* la substitution de celles définies dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="094ba-501">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="094ba-502">Pour plus d’informations sur la migration de la configuration d’application à partir de versions antérieures de ASP.NET, consultez <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="094ba-502">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="094ba-503">Ajouter la configuration à partir d’un assembly externe</span><span class="sxs-lookup"><span data-stu-id="094ba-503">Add configuration from an external assembly</span></span>

<span data-ttu-id="094ba-504">Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-504">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="094ba-505">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="094ba-505">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="094ba-506">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="094ba-506">Additional resources</span></span>

* [<span data-ttu-id="094ba-507">Code source de configuration</span><span class="sxs-lookup"><span data-stu-id="094ba-507">Configuration source code</span></span>](https://github.com/dotnet/runtime/tree/master/src/libraries/Microsoft.Extensions.Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="094ba-508">La configuration d’application dans ASP.NET Core est basée sur des paires clé-valeur établies par les *fournisseurs de configuration*.</span><span class="sxs-lookup"><span data-stu-id="094ba-508">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="094ba-509">Les fournisseurs de configuration lisent les données de configuration dans les paires clé-valeur à partir de diverses sources de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-509">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="094ba-510">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="094ba-510">Azure Key Vault</span></span>
* <span data-ttu-id="094ba-511">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="094ba-511">Azure App Configuration</span></span>
* <span data-ttu-id="094ba-512">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="094ba-512">Command-line arguments</span></span>
* <span data-ttu-id="094ba-513">Fournisseurs personnalisés (installés ou créés)</span><span class="sxs-lookup"><span data-stu-id="094ba-513">Custom providers (installed or created)</span></span>
* <span data-ttu-id="094ba-514">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="094ba-514">Directory files</span></span>
* <span data-ttu-id="094ba-515">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="094ba-515">Environment variables</span></span>
* <span data-ttu-id="094ba-516">Objets .NET en mémoire</span><span class="sxs-lookup"><span data-stu-id="094ba-516">In-memory .NET objects</span></span>
* <span data-ttu-id="094ba-517">Fichiers de paramètres</span><span class="sxs-lookup"><span data-stu-id="094ba-517">Settings files</span></span>

<span data-ttu-id="094ba-518">Les packages de configuration pour les scénarios de fournisseur de configuration courants ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sont inclus implicitement dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="094ba-518">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="094ba-519">Les exemples de code qui suivent et dans l’échantillon d’application utilisent l’espace de noms <xref:Microsoft.Extensions.Configuration> :</span><span class="sxs-lookup"><span data-stu-id="094ba-519">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="094ba-520">Le *modèle d’options* est une extension des concepts de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="094ba-520">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="094ba-521">Les options utilisent des classes pour représenter les groupes de paramètres associés.</span><span class="sxs-lookup"><span data-stu-id="094ba-521">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="094ba-522">Pour plus d’informations, consultez <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="094ba-522">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="094ba-523">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="094ba-523">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="094ba-524">Configuration de l’hôte ou configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="094ba-524">Host versus app configuration</span></span>

<span data-ttu-id="094ba-525">Avant que l’application ne soit configurée et démarrée, un *hôte* est configuré et lancé.</span><span class="sxs-lookup"><span data-stu-id="094ba-525">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="094ba-526">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="094ba-526">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="094ba-527">L’application et l’hôte sont configurés à l’aide des fournisseurs de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="094ba-527">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="094ba-528">Les paires clé-valeur de la configuration de l’hôte sont également incluses dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-528">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="094ba-529">Pour plus d’informations sur l’utilisation des fournisseurs de configuration lors de la génération de l’hôte et l’impact des sources de configuration sur la configuration de l’hôte, consultez <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="094ba-529">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="094ba-530">Autre configuration</span><span class="sxs-lookup"><span data-stu-id="094ba-530">Other configuration</span></span>

<span data-ttu-id="094ba-531">Cette rubrique se rapporte uniquement à la configuration de l' *application*.</span><span class="sxs-lookup"><span data-stu-id="094ba-531">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="094ba-532">D’autres aspects de l’exécution et de l’hébergement des applications ASP.NET Core sont configurés à l’aide des fichiers de configuration non traités dans cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="094ba-532">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="094ba-533">*launch.js* / *launchSettings.jssur* sont des fichiers de configuration d’outils pour l’environnement de développement, décrits ci-après :</span><span class="sxs-lookup"><span data-stu-id="094ba-533">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="094ba-534">Dans <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="094ba-534">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="094ba-535">Dans l’ensemble de la documentation dans lequel les fichiers sont utilisés pour configurer des applications ASP.NET Core pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="094ba-535">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="094ba-536">*web.config* est un fichier de configuration de serveur, décrit dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="094ba-536">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="094ba-537">Pour plus d’informations sur la migration de la configuration d’application à partir de versions antérieures de ASP.NET, consultez <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="094ba-537">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="094ba-538">Configuration par défaut</span><span class="sxs-lookup"><span data-stu-id="094ba-538">Default configuration</span></span>

<span data-ttu-id="094ba-539">Les applications web basées sur les modèles ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) appellent <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> pendant la création d’un hôte.</span><span class="sxs-lookup"><span data-stu-id="094ba-539">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="094ba-540">`CreateDefaultBuilder` fournit la configuration par défaut de l’application dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="094ba-540">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="094ba-541">Les éléments suivants s’appliquent aux applications qui utilisent l’[hôte web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="094ba-541">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="094ba-542">Pour plus de détails sur la configuration par défaut lors de l’utilisation de l’[hôte générique](xref:fundamentals/host/generic-host), consultez la [dernière version de cette rubrique](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="094ba-542">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="094ba-543">La configuration de l’hôte est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="094ba-543">Host configuration is provided from:</span></span>
  * <span data-ttu-id="094ba-544">Des variables d’environnement préfixées avec `ASPNETCORE_` (par exemple, `ASPNETCORE_ENVIRONMENT`) à l’aide du [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="094ba-544">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="094ba-545">Le préfixe (`ASPNETCORE_`) est supprimé lorsque les paires clé-valeur de la configuration sont chargées.</span><span class="sxs-lookup"><span data-stu-id="094ba-545">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="094ba-546">Des arguments de ligne de commande à l’aide du [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="094ba-546">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="094ba-547">La configuration de l’application est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="094ba-547">App configuration is provided from:</span></span>
  * <span data-ttu-id="094ba-548">*appsettings.json* utilisation du [fournisseur de configuration de fichier](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="094ba-548">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="094ba-549">*appsettings.{Environment}.json* à l’aide du [Fournisseur de configuration de fichier](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="094ba-549">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="094ba-550">Les [secrets utilisateur](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development` à l’aide de l’assembly d’entrée</span><span class="sxs-lookup"><span data-stu-id="094ba-550">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="094ba-551">Des variables d’environnement à l’aide du [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="094ba-551">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="094ba-552">Des arguments de ligne de commande à l’aide du [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="094ba-552">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="094ba-553">Sécurité</span><span class="sxs-lookup"><span data-stu-id="094ba-553">Security</span></span>

<span data-ttu-id="094ba-554">Adoptez les pratiques suivantes pour sécuriser les données de configuration sensibles :</span><span class="sxs-lookup"><span data-stu-id="094ba-554">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="094ba-555">Ne stockez jamais des mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte clair.</span><span class="sxs-lookup"><span data-stu-id="094ba-555">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="094ba-556">N’utilisez aucun secret de production dans les environnements de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="094ba-556">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="094ba-557">Spécifiez les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans un référentiel de code source.</span><span class="sxs-lookup"><span data-stu-id="094ba-557">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="094ba-558">Pour plus d'informations, voir les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="094ba-558">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="094ba-559"><xref:security/app-secrets>: Fournit des conseils sur l’utilisation de variables d’environnement pour stocker des données sensibles.</span><span class="sxs-lookup"><span data-stu-id="094ba-559"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="094ba-560">L’outil Gestionnaire de secret utilise le fournisseur de configuration de fichiers pour stocker les secrets d’utilisateur dans un fichier JSON sur le système local.</span><span class="sxs-lookup"><span data-stu-id="094ba-560">The Secret Manager tool uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="094ba-561">Le fournisseur de configuration de fichier est décrit plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="094ba-561">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="094ba-562">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stocke en toute sécurité des secrets d’application pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="094ba-562">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="094ba-563">Pour plus d’informations, consultez <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="094ba-563">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="094ba-564">Données de configuration hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="094ba-564">Hierarchical configuration data</span></span>

<span data-ttu-id="094ba-565">L’API Configuration est capable de maintenir des données de configuration hiérarchiques en aplanissant les données hiérarchiques à l’aide d’un délimiteur dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-565">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="094ba-566">Dans le fichier JSON suivant, quatre clés existent dans une structure hiérarchique à deux sections :</span><span class="sxs-lookup"><span data-stu-id="094ba-566">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="094ba-567">Lorsque le fichier est lu dans la configuration, des clés uniques sont créées pour gérer la structure des données hiérarchiques d’origine de la source de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-567">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="094ba-568">Les sections et les clés sont aplanies à l’aide d’un signe deux-points (`:`) pour conserver la structure d’origine :</span><span class="sxs-lookup"><span data-stu-id="094ba-568">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="094ba-569">section0:key0</span><span class="sxs-lookup"><span data-stu-id="094ba-569">section0:key0</span></span>
* <span data-ttu-id="094ba-570">section0:key1</span><span class="sxs-lookup"><span data-stu-id="094ba-570">section0:key1</span></span>
* <span data-ttu-id="094ba-571">section1:key0</span><span class="sxs-lookup"><span data-stu-id="094ba-571">section1:key0</span></span>
* <span data-ttu-id="094ba-572">section1:key1</span><span class="sxs-lookup"><span data-stu-id="094ba-572">section1:key1</span></span>

<span data-ttu-id="094ba-573">Les méthodes <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> et <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sont disponibles pour isoler les sections et les enfants d’une section dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-573"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="094ba-574">Ces méthodes sont décrites plus loin dans [GetSection, GetChildren et Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="094ba-574">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="094ba-575">Conventions</span><span class="sxs-lookup"><span data-stu-id="094ba-575">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="094ba-576">Sources et fournisseurs</span><span class="sxs-lookup"><span data-stu-id="094ba-576">Sources and providers</span></span>

<span data-ttu-id="094ba-577">Au démarrage de l’application, les sources de configuration sont lues dans l’ordre où leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="094ba-577">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="094ba-578">Les fournisseurs de configuration qui implémentent la détection des modifications peuvent recharger la configuration lorsqu’un paramètre sous-jacent est modifié.</span><span class="sxs-lookup"><span data-stu-id="094ba-578">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="094ba-579">Par exemple, le fournisseur de configuration de fichier (décrit plus loin dans cette rubrique) et le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) implémentent la détection des modifications.</span><span class="sxs-lookup"><span data-stu-id="094ba-579">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="094ba-580"><xref:Microsoft.Extensions.Configuration.IConfiguration> est disponible dans le conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-580"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="094ba-581"><xref:Microsoft.Extensions.Configuration.IConfiguration> peut être injecté dans une Razor page <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou un MVC <xref:Microsoft.AspNetCore.Mvc.Controller> pour obtenir la configuration de la classe.</span><span class="sxs-lookup"><span data-stu-id="094ba-581"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="094ba-582">Dans les exemples suivants, le `_config` champ est utilisé pour accéder aux valeurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-582">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="094ba-583">Les fournisseurs de configuration ne peuvent pas utiliser le DI, car celui-ci n’est pas disponible lorsque les fournisseurs sont configurés par l’hôte.</span><span class="sxs-lookup"><span data-stu-id="094ba-583">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="094ba-584">Keys</span><span class="sxs-lookup"><span data-stu-id="094ba-584">Keys</span></span>

<span data-ttu-id="094ba-585">Les clés de configuration adoptent les conventions suivantes :</span><span class="sxs-lookup"><span data-stu-id="094ba-585">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="094ba-586">Les clés ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="094ba-586">Keys are case-insensitive.</span></span> <span data-ttu-id="094ba-587">Par exemple, `ConnectionString` et `connectionstring` sont traités en tant que clés équivalentes.</span><span class="sxs-lookup"><span data-stu-id="094ba-587">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="094ba-588">Si une valeur pour la même clé est définie par des fournisseurs de configuration identiques ou différents, la valeur utilisée est la dernière valeur définie sur la clé.</span><span class="sxs-lookup"><span data-stu-id="094ba-588">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="094ba-589">Pour plus d’informations sur les clés JSON en double, consultez [ce problème GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="094ba-589">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="094ba-590">Clés hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="094ba-590">Hierarchical keys</span></span>
  * <span data-ttu-id="094ba-591">Dans l’API Configuration, un séparateur sous forme de signe deux-points (`:`) fonctionne sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="094ba-591">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="094ba-592">Dans les variables d’environnement, un séparateur sous forme de signe deux-points peut ne pas fonctionner sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="094ba-592">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="094ba-593">Un trait de soulignement double (`__`) est pris en charge par toutes les plateformes et automatiquement transformé en signe deux-points.</span><span class="sxs-lookup"><span data-stu-id="094ba-593">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="094ba-594">Dans Azure Key Vault, les clés hiérarchiques utilisent `--` (deux tirets) comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="094ba-594">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="094ba-595">Écrivez du code pour remplacer les tirets par un signe deux-points lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-595">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="094ba-596"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-596">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="094ba-597">La liaison de tableau est décrite dans la section [Lier un tableau à une classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="094ba-597">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="094ba-598">Valeurs</span><span class="sxs-lookup"><span data-stu-id="094ba-598">Values</span></span>

<span data-ttu-id="094ba-599">Les valeurs de configuration adoptent les conventions suivantes :</span><span class="sxs-lookup"><span data-stu-id="094ba-599">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="094ba-600">Les valeurs sont des chaînes.</span><span class="sxs-lookup"><span data-stu-id="094ba-600">Values are strings.</span></span>
* <span data-ttu-id="094ba-601">Les valeurs NULL ne peuvent pas être stockées dans la configuration ou liées à des objets.</span><span class="sxs-lookup"><span data-stu-id="094ba-601">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="094ba-602">Fournisseurs</span><span class="sxs-lookup"><span data-stu-id="094ba-602">Providers</span></span>

<span data-ttu-id="094ba-603">Le tableau suivant présente les fournisseurs de configuration disponibles pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="094ba-603">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="094ba-604">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="094ba-604">Provider</span></span> | <span data-ttu-id="094ba-605">Fournit la configuration à partir de&hellip;</span><span class="sxs-lookup"><span data-stu-id="094ba-605">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="094ba-606">[Fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) (rubrique *Sécurité*)</span><span class="sxs-lookup"><span data-stu-id="094ba-606">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="094ba-607">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="094ba-607">Azure Key Vault</span></span> |
| <span data-ttu-id="094ba-608">[Fournisseur Azure App Configuration](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentation Azure)</span><span class="sxs-lookup"><span data-stu-id="094ba-608">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="094ba-609">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="094ba-609">Azure App Configuration</span></span> |
| [<span data-ttu-id="094ba-610">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="094ba-610">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="094ba-611">Paramètres de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="094ba-611">Command-line parameters</span></span> |
| [<span data-ttu-id="094ba-612">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="094ba-612">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="094ba-613">Source personnalisée</span><span class="sxs-lookup"><span data-stu-id="094ba-613">Custom source</span></span> |
| [<span data-ttu-id="094ba-614">Fournisseur de configuration de variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="094ba-614">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="094ba-615">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="094ba-615">Environment variables</span></span> |
| [<span data-ttu-id="094ba-616">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="094ba-616">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="094ba-617">Fichiers (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="094ba-617">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="094ba-618">Fournisseur de configuration clé par fichier</span><span class="sxs-lookup"><span data-stu-id="094ba-618">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="094ba-619">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="094ba-619">Directory files</span></span> |
| [<span data-ttu-id="094ba-620">Fournisseur de configuration de mémoire</span><span class="sxs-lookup"><span data-stu-id="094ba-620">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="094ba-621">Collections en mémoire</span><span class="sxs-lookup"><span data-stu-id="094ba-621">In-memory collections</span></span> |
| <span data-ttu-id="094ba-622">[Secrets](xref:security/app-secrets) de l’utilisateur (rubriques relatives à la *sécurité* )</span><span class="sxs-lookup"><span data-stu-id="094ba-622">[User secrets](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="094ba-623">Fichier dans le répertoire de profil utilisateur</span><span class="sxs-lookup"><span data-stu-id="094ba-623">File in the user profile directory</span></span> |

<span data-ttu-id="094ba-624">Au démarrage, les sources de configuration sont lues dans l’ordre où leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="094ba-624">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="094ba-625">Les fournisseurs de configuration décrits dans cette rubrique sont décrits par ordre alphabétique, et non pas dans l’ordre dans lequel le code les réorganise.</span><span class="sxs-lookup"><span data-stu-id="094ba-625">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="094ba-626">Commandez des fournisseurs de configuration dans le code pour répondre aux priorités des sources de configuration sous-jacentes requises par l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-626">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="094ba-627">Une séquence type des fournisseurs de configuration est la suivante :</span><span class="sxs-lookup"><span data-stu-id="094ba-627">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="094ba-628">Fichiers ( *appsettings.json* , *appSettings. { Environment}. JSON*, où `{Environment}` est l’environnement d’hébergement actuel de l’application)</span><span class="sxs-lookup"><span data-stu-id="094ba-628">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="094ba-629">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="094ba-629">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="094ba-630">[Secrets](xref:security/app-secrets) de l’utilisateur (environnement de développement uniquement)</span><span class="sxs-lookup"><span data-stu-id="094ba-630">[User secrets](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="094ba-631">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="094ba-631">Environment variables</span></span>
1. <span data-ttu-id="094ba-632">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="094ba-632">Command-line arguments</span></span>

<span data-ttu-id="094ba-633">Une pratique courante consiste à placer le Fournisseur de configuration de ligne de commande en dernier dans une série de fournisseurs pour permettre aux arguments de ligne de commande de remplacer la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="094ba-633">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="094ba-634">La séquence de fournisseurs précédente est utilisée lors de l’initialisation d’un nouveau générateur d’hôte `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="094ba-634">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="094ba-635">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="094ba-635">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="094ba-636">Configurer le générateur d’ordinateur hôte avec UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="094ba-636">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="094ba-637">Pour configurer le générateur d’ordinateur hôte, appelez <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> sur le générateur d’hôte avec la configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-637">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="094ba-638">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="094ba-638">ConfigureAppConfiguration</span></span>

<span data-ttu-id="094ba-639">Appelez `ConfigureAppConfiguration` quand vous créez l’hôte pour spécifier les fournisseurs de configuration de l’application en plus de ceux ajoutés automatiquement par `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="094ba-639">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="094ba-640">Remplacez la configuration précédente par des arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="094ba-640">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="094ba-641">Pour fournir une configuration d’application pouvant être remplacée par des arguments de ligne de commande, appelez `AddCommandLine` en dernier :</span><span class="sxs-lookup"><span data-stu-id="094ba-641">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="094ba-642">Supprimer les fournisseurs ajoutés par CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="094ba-642">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="094ba-643">Pour supprimer les fournisseurs ajoutés par `CreateDefaultBuilder` , appelez d’abord [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) sur [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="094ba-643">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="094ba-644">Utiliser la configuration lors du démarrage de l’application</span><span class="sxs-lookup"><span data-stu-id="094ba-644">Consume configuration during app startup</span></span>

<span data-ttu-id="094ba-645">La configuration fournie à l’application dans `ConfigureAppConfiguration` est disponible lors du démarrage de l’application, notamment `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="094ba-645">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="094ba-646">Pour plus d’informations, consultez la section [Accéder à la configuration lors du démarrage](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="094ba-646">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="094ba-647">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="094ba-647">Command-line Configuration Provider</span></span>

<span data-ttu-id="094ba-648"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> charge la configuration à partir des paires clé-valeur de l’argument de ligne de commande lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="094ba-648">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="094ba-649">Pour activer la configuration en ligne de commande, la méthode d’extension <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> est appelée sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="094ba-649">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="094ba-650">`AddCommandLine` est appelé automatiquement quand `CreateDefaultBuilder(string [])` est appelé.</span><span class="sxs-lookup"><span data-stu-id="094ba-650">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="094ba-651">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="094ba-651">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="094ba-652">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="094ba-652">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="094ba-653">Configuration facultative à partir de *appsettings.json* et *appSettings. { Fichiers Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="094ba-653">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="094ba-654">[Secrets](xref:security/app-secrets) de l’utilisateur dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="094ba-654">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="094ba-655">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="094ba-655">Environment variables.</span></span>

<span data-ttu-id="094ba-656">`CreateDefaultBuilder` ajoute en dernier le Fournisseur de configuration de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="094ba-656">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="094ba-657">Les arguments de ligne de commande passés lors de l’exécution remplacent la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="094ba-657">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="094ba-658">`CreateDefaultBuilder` agit lors de la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="094ba-658">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="094ba-659">Par conséquent, la configuration de ligne de commande activée par `CreateDefaultBuilder` peut affecter la façon dont l’hôte est configuré.</span><span class="sxs-lookup"><span data-stu-id="094ba-659">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="094ba-660">Pour les applications basées sur les modèles ASP.NET Core, `AddCommandLine` a déjà été appelé par `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="094ba-660">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="094ba-661">Pour ajouter des fournisseurs de configuration supplémentaires et conserver la capacité de remplacer leur configuration par des arguments de ligne de commande, appelez les fournisseurs supplémentaires de l’application dans `ConfigureAppConfiguration` et appelez `AddCommandLine` en dernier.</span><span class="sxs-lookup"><span data-stu-id="094ba-661">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="094ba-662">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="094ba-662">**Example**</span></span>

<span data-ttu-id="094ba-663">L’exemple d’application tire parti de la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte, qui inclut un appel à <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="094ba-663">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="094ba-664">Ouvrez une invite de commandes dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="094ba-664">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="094ba-665">Fournissez un argument de ligne de commande à la commande `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="094ba-665">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="094ba-666">Une fois que l’application est en cours d’exécution, ouvrez un navigateur à l’application à l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="094ba-666">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="094ba-667">Notez que la sortie contient la paire clé-valeur pour l’argument de ligne de commande de configuration fourni à `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="094ba-667">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="094ba-668">Arguments</span><span class="sxs-lookup"><span data-stu-id="094ba-668">Arguments</span></span>

<span data-ttu-id="094ba-669">La valeur doit suivre un signe égal (`=`) ou la clé doit avoir un préfixe (`--` ou `/`) lorsque la valeur suit un espace.</span><span class="sxs-lookup"><span data-stu-id="094ba-669">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="094ba-670">La valeur n’est pas requise si un signe égal est utilisé (par exemple, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="094ba-670">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="094ba-671">Préfixe de clé</span><span class="sxs-lookup"><span data-stu-id="094ba-671">Key prefix</span></span>               | <span data-ttu-id="094ba-672">Exemple</span><span class="sxs-lookup"><span data-stu-id="094ba-672">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="094ba-673">Aucun préfixe</span><span class="sxs-lookup"><span data-stu-id="094ba-673">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="094ba-674">Deux tirets (`--`)</span><span class="sxs-lookup"><span data-stu-id="094ba-674">Two dashes (`--`)</span></span>        | <span data-ttu-id="094ba-675">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="094ba-675">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="094ba-676">Barre oblique (`/`)</span><span class="sxs-lookup"><span data-stu-id="094ba-676">Forward slash (`/`)</span></span>      | <span data-ttu-id="094ba-677">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="094ba-677">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="094ba-678">Dans la même commande, ne mélangez pas des paires clé-valeur de l’argument de ligne de commande qui utilisent un signe égal avec des paires clé-valeur qui utilisent un espace.</span><span class="sxs-lookup"><span data-stu-id="094ba-678">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="094ba-679">Exemples de commandes :</span><span class="sxs-lookup"><span data-stu-id="094ba-679">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="094ba-680">Correspondances de commutateur</span><span class="sxs-lookup"><span data-stu-id="094ba-680">Switch mappings</span></span>

<span data-ttu-id="094ba-681">Les correspondances de commutateur permettent une logique de remplacement des noms de clés.</span><span class="sxs-lookup"><span data-stu-id="094ba-681">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="094ba-682">Lors de la génération manuelle d’une configuration avec un <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , fournissez un dictionnaire de remplacements de commutateur à la <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> méthode.</span><span class="sxs-lookup"><span data-stu-id="094ba-682">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="094ba-683">Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="094ba-683">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="094ba-684">Si la clé de ligne de commande est trouvée dans le dictionnaire, la valeur du dictionnaire (le remplacement de la clé) est repassée pour définir la paire clé-valeur dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-684">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="094ba-685">Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).</span><span class="sxs-lookup"><span data-stu-id="094ba-685">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="094ba-686">Règles des clés du dictionnaire de correspondances de commutateur :</span><span class="sxs-lookup"><span data-stu-id="094ba-686">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="094ba-687">Les commutateurs doivent commencer par un tiret (`-`) ou un double tiret (`--`).</span><span class="sxs-lookup"><span data-stu-id="094ba-687">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="094ba-688">Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.</span><span class="sxs-lookup"><span data-stu-id="094ba-688">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="094ba-689">Créez un dictionnaire des mappages de commutateurs.</span><span class="sxs-lookup"><span data-stu-id="094ba-689">Create a switch mappings dictionary.</span></span> <span data-ttu-id="094ba-690">Dans l’exemple suivant, deux mappages de commutateurs sont créés :</span><span class="sxs-lookup"><span data-stu-id="094ba-690">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="094ba-691">Lorsque l’hôte est généré, appelez `AddCommandLine` avec le dictionnaire de mappages de commutateurs :</span><span class="sxs-lookup"><span data-stu-id="094ba-691">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="094ba-692">Pour les applications qui utilisent des mappages de commutateurs, l’appel à `CreateDefaultBuilder` ne doit pas passer d’arguments.</span><span class="sxs-lookup"><span data-stu-id="094ba-692">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="094ba-693">L’appel `AddCommandLine` de la méthode `CreateDefaultBuilder` n’inclut pas de commutateurs mappés et il n’existe aucun moyen de transmettre le dictionnaire de correspondance de commutateur à `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="094ba-693">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="094ba-694">La solution ne consiste pas à transmettre les arguments à `CreateDefaultBuilder`, mais plutôt à permettre à la méthode `AddCommandLine` de la méthode `ConfigurationBuilder` de traiter les arguments et le dictionnaire de mappage de commutateur.</span><span class="sxs-lookup"><span data-stu-id="094ba-694">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="094ba-695">Une fois le dictionnaire de correspondances de commutateur créé, il contient les données affichées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="094ba-695">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="094ba-696">Clé</span><span class="sxs-lookup"><span data-stu-id="094ba-696">Key</span></span>       | <span data-ttu-id="094ba-697">Valeur</span><span class="sxs-lookup"><span data-stu-id="094ba-697">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="094ba-698">Si les clés mappées au commutateur sont utilisées lors du démarrage de l’application, la configuration reçoit la valeur de configuration sur la clé fournie par le dictionnaire :</span><span class="sxs-lookup"><span data-stu-id="094ba-698">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="094ba-699">Après avoir exécuté la commande précédente, la configuration contient les valeurs indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="094ba-699">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="094ba-700">Clé</span><span class="sxs-lookup"><span data-stu-id="094ba-700">Key</span></span>               | <span data-ttu-id="094ba-701">Valeur</span><span class="sxs-lookup"><span data-stu-id="094ba-701">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="094ba-702">Fournisseur de configuration de variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="094ba-702">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="094ba-703"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> charge la configuration à partir des paires clé-valeur de la variable d’environnement lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="094ba-703">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="094ba-704">Pour activer la configuration des variables d’environnement, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="094ba-704">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="094ba-705">[Azure App service](https://azure.microsoft.com/services/app-service/) permet de définir des variables d’environnement dans le portail Azure qui peuvent remplacer la configuration de l’application à l’aide du fournisseur de configuration des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="094ba-705">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="094ba-706">Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="094ba-706">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="094ba-707">`AddEnvironmentVariables` sert à charger les variables d’environnement préfixées avec `ASPNETCORE_` pour la [configuration hôte](#host-versus-app-configuration) lorsqu’un nouveau générateur d’hôte est initialisé avec l’[hôte web](xref:fundamentals/host/web-host) et que `CreateDefaultBuilder` est appelé.</span><span class="sxs-lookup"><span data-stu-id="094ba-707">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="094ba-708">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="094ba-708">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="094ba-709">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="094ba-709">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="094ba-710">Configuration de l’application à partir de variables d’environnement sans préfixe en appelant `AddEnvironmentVariables` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="094ba-710">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="094ba-711">Configuration facultative à partir de *appsettings.json* et *appSettings. { Fichiers Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="094ba-711">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="094ba-712">[Secrets](xref:security/app-secrets) de l’utilisateur dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="094ba-712">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="094ba-713">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="094ba-713">Command-line arguments.</span></span>

<span data-ttu-id="094ba-714">Le fournisseur de configuration de variables d’environnement est appelé une fois que la configuration est établie à partir des secrets utilisateur et des fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="094ba-714">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="094ba-715">Le fait d’appeler le fournisseur ainsi permet de lire les variables d’environnement pendant l’exécution pour substituer la configuration définie par les secrets utilisateur et les fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="094ba-715">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="094ba-716">Pour fournir la configuration d’application à partir de variables d’environnement supplémentaires, appelez les fournisseurs supplémentaires de l’application dans `ConfigureAppConfiguration` et appelez `AddEnvironmentVariables` avec le préfixe :</span><span class="sxs-lookup"><span data-stu-id="094ba-716">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="094ba-717">Appelez `AddEnvironmentVariables` Last pour autoriser les variables d’environnement avec le préfixe spécifié à substituer des valeurs d’autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="094ba-717">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="094ba-718">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="094ba-718">**Example**</span></span>

<span data-ttu-id="094ba-719">L’exemple d’application tire parti de la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte, qui inclut un appel à `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="094ba-719">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="094ba-720">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-720">Run the sample app.</span></span> <span data-ttu-id="094ba-721">Ouvrez un navigateur vers l’application avec l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="094ba-721">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="094ba-722">Notez que la sortie contient la paire clé-valeur pour la variable d’environnement `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="094ba-722">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="094ba-723">La valeur reflète l’environnement dans lequel l’application est en cours d’exécution, en général `Development` lors de l’exécution locale.</span><span class="sxs-lookup"><span data-stu-id="094ba-723">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="094ba-724">Pour que la liste des variables d’environnement restituée par l’application soit courte, l’application filtre les variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="094ba-724">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="094ba-725">Consultez le fichier *Pages/Index.cshtml.cs* de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-725">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="094ba-726">Pour exposer toutes les variables d’environnement disponibles pour l’application, remplacez `FilteredConfiguration` dans *pages/index. cshtml. cs* par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="094ba-726">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="094ba-727">Préfixes</span><span class="sxs-lookup"><span data-stu-id="094ba-727">Prefixes</span></span>

<span data-ttu-id="094ba-728">Les variables d’environnement chargées dans la configuration de l’application sont filtrées lors de la spécification d’un préfixe à la `AddEnvironmentVariables` méthode.</span><span class="sxs-lookup"><span data-stu-id="094ba-728">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="094ba-729">Par exemple, pour filtrer les variables d’environnement sur le préfixe `CUSTOM_`, fournissez le préfixe au fournisseur de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-729">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="094ba-730">Le préfixe est supprimé lorsque les paires clé-valeur de la configuration sont créées.</span><span class="sxs-lookup"><span data-stu-id="094ba-730">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="094ba-731">Lorsque le générateur d’hôte est créé, la configuration de l’hôte est fournie par des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="094ba-731">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="094ba-732">Pour plus d’informations sur le préfixe utilisé pour ces variables d’environnement, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="094ba-732">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="094ba-733">**Préfixes des chaînes de connexion**</span><span class="sxs-lookup"><span data-stu-id="094ba-733">**Connection string prefixes**</span></span>

<span data-ttu-id="094ba-734">L’API Configuration possède des règles de traitement spéciales pour quatre variables d’environnement de chaîne de connexion impliquées dans la configuration des chaînes de connexion Azure pour l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-734">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="094ba-735">Les variables d’environnement avec les préfixes indiqués dans le tableau sont chargées dans l’application si aucun préfixe n’est fourni à `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="094ba-735">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="094ba-736">Préfixe de la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="094ba-736">Connection string prefix</span></span> | <span data-ttu-id="094ba-737">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="094ba-737">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="094ba-738">Fournisseur personnalisé</span><span class="sxs-lookup"><span data-stu-id="094ba-738">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="094ba-739">MySQL</span><span class="sxs-lookup"><span data-stu-id="094ba-739">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="094ba-740">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="094ba-740">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="094ba-741">SQL Server</span><span class="sxs-lookup"><span data-stu-id="094ba-741">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="094ba-742">Quand une variable d’environnement est découverte et chargée dans la configuration avec l’un des quatre préfixes indiqués dans le tableau :</span><span class="sxs-lookup"><span data-stu-id="094ba-742">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="094ba-743">La clé de configuration est créée en supprimant le préfixe de la variable d’environnement et en ajoutant une section de clé de configuration (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="094ba-743">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="094ba-744">Une nouvelle paire clé-valeur de configuration est créée qui représente le fournisseur de connexion de base de données (à l’exception de `CUSTOMCONNSTR_`, qui ne possède aucun fournisseur indiqué).</span><span class="sxs-lookup"><span data-stu-id="094ba-744">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="094ba-745">Clé de variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="094ba-745">Environment variable key</span></span> | <span data-ttu-id="094ba-746">Clé de configuration convertie</span><span class="sxs-lookup"><span data-stu-id="094ba-746">Converted configuration key</span></span> | <span data-ttu-id="094ba-747">Entrée de configuration de fournisseur</span><span class="sxs-lookup"><span data-stu-id="094ba-747">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="094ba-748">Entrée de configuration non créée.</span><span class="sxs-lookup"><span data-stu-id="094ba-748">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="094ba-749">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="094ba-749">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="094ba-750">Valeur: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="094ba-750">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="094ba-751">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="094ba-751">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="094ba-752">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="094ba-752">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="094ba-753">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="094ba-753">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="094ba-754">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="094ba-754">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="094ba-755">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="094ba-755">**Example**</span></span>

<span data-ttu-id="094ba-756">Une variable d’environnement de chaîne de connexion personnalisée est créée sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="094ba-756">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="094ba-757">Nom : `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="094ba-757">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="094ba-758">Valeur: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="094ba-758">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="094ba-759">Si `IConfiguration` est injecté et affecté à un champ nommé `_config` , lisez la valeur :</span><span class="sxs-lookup"><span data-stu-id="094ba-759">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="094ba-760">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="094ba-760">File Configuration Provider</span></span>

<span data-ttu-id="094ba-761"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> est la classe de base pour charger la configuration à partir du système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="094ba-761"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="094ba-762">Les fournisseurs de configuration suivants sont dédiés à des types de fichiers spécifiques :</span><span class="sxs-lookup"><span data-stu-id="094ba-762">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="094ba-763">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="094ba-763">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="094ba-764">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="094ba-764">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="094ba-765">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="094ba-765">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="094ba-766">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="094ba-766">INI Configuration Provider</span></span>

<span data-ttu-id="094ba-767"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier INI lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="094ba-767">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="094ba-768">Pour activer la configuration du fichier INI, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="094ba-768">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="094ba-769">Le signe deux-points peut servir de délimiteur de section dans la configuration d’un fichier INI.</span><span class="sxs-lookup"><span data-stu-id="094ba-769">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="094ba-770">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="094ba-770">Overloads permit specifying:</span></span>

* <span data-ttu-id="094ba-771">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="094ba-771">Whether the file is optional.</span></span>
* <span data-ttu-id="094ba-772">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="094ba-772">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="094ba-773">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="094ba-773">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="094ba-774">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="094ba-774">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="094ba-775">Exemple générique d’un fichier de configuration INI :</span><span class="sxs-lookup"><span data-stu-id="094ba-775">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="094ba-776">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="094ba-776">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="094ba-777">section0:key0</span><span class="sxs-lookup"><span data-stu-id="094ba-777">section0:key0</span></span>
* <span data-ttu-id="094ba-778">section0:key1</span><span class="sxs-lookup"><span data-stu-id="094ba-778">section0:key1</span></span>
* <span data-ttu-id="094ba-779">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="094ba-779">section1:subsection:key</span></span>
* <span data-ttu-id="094ba-780">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="094ba-780">section2:subsection0:key</span></span>
* <span data-ttu-id="094ba-781">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="094ba-781">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="094ba-782">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="094ba-782">JSON Configuration Provider</span></span>

<span data-ttu-id="094ba-783"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier JSON lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="094ba-783">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="094ba-784">Pour activer la configuration du fichier JSON, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="094ba-784">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="094ba-785">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="094ba-785">Overloads permit specifying:</span></span>

* <span data-ttu-id="094ba-786">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="094ba-786">Whether the file is optional.</span></span>
* <span data-ttu-id="094ba-787">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="094ba-787">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="094ba-788">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="094ba-788">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="094ba-789">`AddJsonFile` est appelé automatiquement deux fois lors de l’initialisation d’un nouveau générateur d’hôte `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="094ba-789">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="094ba-790">La méthode est appelée pour charger la configuration à partir de :</span><span class="sxs-lookup"><span data-stu-id="094ba-790">The method is called to load configuration from:</span></span>

* <span data-ttu-id="094ba-791">*appsettings.json*: Ce fichier est lu en premier.</span><span class="sxs-lookup"><span data-stu-id="094ba-791">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="094ba-792">La version de l’environnement du fichier peut remplacer les valeurs fournies par le *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="094ba-792">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="094ba-793">*appSettings. {Environment}. JSON*: la version de l’environnement du fichier est chargée à partir de [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="094ba-793">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="094ba-794">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="094ba-794">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="094ba-795">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="094ba-795">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="094ba-796">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="094ba-796">Environment variables.</span></span>
* <span data-ttu-id="094ba-797">[Secrets](xref:security/app-secrets) de l’utilisateur dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="094ba-797">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="094ba-798">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="094ba-798">Command-line arguments.</span></span>

<span data-ttu-id="094ba-799">Le Fournisseur de configuration JSON est établi en premier.</span><span class="sxs-lookup"><span data-stu-id="094ba-799">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="094ba-800">Par conséquent, les secrets utilisateur, les variables d’environnement et les arguments de ligne de commande remplacent la configuration définie par les fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="094ba-800">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="094ba-801">Appelez `ConfigureAppConfiguration` lors de la génération de l’hôte pour spécifier la configuration de l’application pour les fichiers autres que *appsettings.json* et *appSettings. { Environnement}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="094ba-801">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="094ba-802">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="094ba-802">**Example**</span></span>

<span data-ttu-id="094ba-803">L’exemple d’application tire parti de la méthode de commodité statique `CreateDefaultBuilder` pour créer l’hôte, ce qui comprend deux appels à `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="094ba-803">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="094ba-804">Le premier appel à `AddJsonFile` charge la configuration à partir de *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="094ba-804">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="094ba-805">Le deuxième appel à `AddJsonFile` charge la configuration à partir de *appSettings. { Environnement}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="094ba-805">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="094ba-806">Pour *appsettings.Development.js* dans l’exemple d’application, le fichier suivant est chargé :</span><span class="sxs-lookup"><span data-stu-id="094ba-806">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="094ba-807">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-807">Run the sample app.</span></span> <span data-ttu-id="094ba-808">Ouvrez un navigateur vers l’application avec l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="094ba-808">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="094ba-809">La sortie contient des paires clé-valeur pour la configuration en fonction de l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-809">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="094ba-810">Le niveau de journalisation de la clé `Logging:LogLevel:Default` est `Debug` lors de l’exécution de l’application dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="094ba-810">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="094ba-811">Exécutez à nouveau l’exemple d’application dans l’environnement de production :</span><span class="sxs-lookup"><span data-stu-id="094ba-811">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="094ba-812">Ouvrez le fichier *Properties/launchSettings.js* .</span><span class="sxs-lookup"><span data-stu-id="094ba-812">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="094ba-813">Dans le `ConfigurationSample` profil, remplacez la valeur de la `ASPNETCORE_ENVIRONMENT` variable d’environnement par `Production` .</span><span class="sxs-lookup"><span data-stu-id="094ba-813">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="094ba-814">Enregistrez le fichier et exécutez l’application avec `dotnet run` dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="094ba-814">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="094ba-815">Les paramètres de la *appsettings.Development.js* qui ne se substituent plus aux paramètres dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="094ba-815">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="094ba-816">Le niveau de journalisation de la clé `Logging:LogLevel:Default` est `Warning` .</span><span class="sxs-lookup"><span data-stu-id="094ba-816">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="094ba-817">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="094ba-817">XML Configuration Provider</span></span>

<span data-ttu-id="094ba-818"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier XML lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="094ba-818">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="094ba-819">Pour activer la configuration du fichier XML, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="094ba-819">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="094ba-820">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="094ba-820">Overloads permit specifying:</span></span>

* <span data-ttu-id="094ba-821">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="094ba-821">Whether the file is optional.</span></span>
* <span data-ttu-id="094ba-822">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="094ba-822">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="094ba-823">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="094ba-823">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="094ba-824">Le nœud racine du fichier de configuration est ignoré lorsque les paires clé-valeur de la configuration sont créées.</span><span class="sxs-lookup"><span data-stu-id="094ba-824">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="094ba-825">Ne spécifiez pas une définition de type de document (DTD) ou un espace de noms dans le fichier.</span><span class="sxs-lookup"><span data-stu-id="094ba-825">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="094ba-826">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="094ba-826">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="094ba-827">Les fichiers de configuration XML peuvent utiliser des noms d’éléments distincts pour les sections répétitives :</span><span class="sxs-lookup"><span data-stu-id="094ba-827">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="094ba-828">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="094ba-828">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="094ba-829">section0:key0</span><span class="sxs-lookup"><span data-stu-id="094ba-829">section0:key0</span></span>
* <span data-ttu-id="094ba-830">section0:key1</span><span class="sxs-lookup"><span data-stu-id="094ba-830">section0:key1</span></span>
* <span data-ttu-id="094ba-831">section1:key0</span><span class="sxs-lookup"><span data-stu-id="094ba-831">section1:key0</span></span>
* <span data-ttu-id="094ba-832">section1:key1</span><span class="sxs-lookup"><span data-stu-id="094ba-832">section1:key1</span></span>

<span data-ttu-id="094ba-833">Les éléments répétitifs qui utilisent le même nom d’élément fonctionnent si l’attribut `name` est utilisé pour distinguer les éléments :</span><span class="sxs-lookup"><span data-stu-id="094ba-833">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="094ba-834">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="094ba-834">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="094ba-835">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="094ba-835">section:section0:key:key0</span></span>
* <span data-ttu-id="094ba-836">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="094ba-836">section:section0:key:key1</span></span>
* <span data-ttu-id="094ba-837">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="094ba-837">section:section1:key:key0</span></span>
* <span data-ttu-id="094ba-838">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="094ba-838">section:section1:key:key1</span></span>

<span data-ttu-id="094ba-839">Les attributs peuvent être utilisés pour fournir des valeurs :</span><span class="sxs-lookup"><span data-stu-id="094ba-839">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="094ba-840">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="094ba-840">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="094ba-841">key:attribute</span><span class="sxs-lookup"><span data-stu-id="094ba-841">key:attribute</span></span>
* <span data-ttu-id="094ba-842">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="094ba-842">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="094ba-843">Fournisseur de configuration clé par fichier</span><span class="sxs-lookup"><span data-stu-id="094ba-843">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="094ba-844">Le <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> utilise les fichiers d’un répertoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-844">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="094ba-845">La clé est le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="094ba-845">The key is the file name.</span></span> <span data-ttu-id="094ba-846">La valeur contient le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="094ba-846">The value contains the file's contents.</span></span> <span data-ttu-id="094ba-847">Le Fournisseur de configuration Clé par fichier est utilisé dans les scénarios d’hébergement de Docker.</span><span class="sxs-lookup"><span data-stu-id="094ba-847">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="094ba-848">Pour activer la configuration clé par fichier, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="094ba-848">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="094ba-849">Le `directoryPath` aux fichiers doit être un chemin d’accès absolu.</span><span class="sxs-lookup"><span data-stu-id="094ba-849">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="094ba-850">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="094ba-850">Overloads permit specifying:</span></span>

* <span data-ttu-id="094ba-851">Un délégué `Action<KeyPerFileConfigurationSource>` qui configure la source.</span><span class="sxs-lookup"><span data-stu-id="094ba-851">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="094ba-852">Si le répertoire est facultatif et le chemin d’accès au répertoire.</span><span class="sxs-lookup"><span data-stu-id="094ba-852">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="094ba-853">Le double trait de soulignement (`__`) est utilisé comme un délimiteur de clé de configuration dans les noms de fichiers.</span><span class="sxs-lookup"><span data-stu-id="094ba-853">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="094ba-854">Par exemple, le nom de fichier `Logging__LogLevel__System` génère la clé de configuration `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="094ba-854">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="094ba-855">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="094ba-855">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="094ba-856">Fournisseur de configuration de mémoire</span><span class="sxs-lookup"><span data-stu-id="094ba-856">Memory Configuration Provider</span></span>

<span data-ttu-id="094ba-857">Le <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> utilise une collection en mémoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-857">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="094ba-858">Pour activer la configuration de la collection en mémoire, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="094ba-858">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="094ba-859">Le fournisseur de configuration peut être initialisé avec un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="094ba-859">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="094ba-860">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-860">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="094ba-861">Dans l’exemple suivant, un dictionnaire de configuration est créé :</span><span class="sxs-lookup"><span data-stu-id="094ba-861">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="094ba-862">Le dictionnaire est utilisé avec un appel à `AddInMemoryCollection` pour fournir la configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-862">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="094ba-863">GetValue</span><span class="sxs-lookup"><span data-stu-id="094ba-863">GetValue</span></span>

<span data-ttu-id="094ba-864">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrait une valeur unique de la configuration avec une clé spécifiée et la convertit en type de non-collection spécifié.</span><span class="sxs-lookup"><span data-stu-id="094ba-864">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="094ba-865">Une surcharge accepte une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="094ba-865">An overload accepts a default value.</span></span>

<span data-ttu-id="094ba-866">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="094ba-866">The following example:</span></span>

* <span data-ttu-id="094ba-867">Extrait la valeur de chaîne de la configuration avec la clé `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="094ba-867">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="094ba-868">Si `NumberKey` est introuvable dans les clés de configuration, la valeur par défaut de `99` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="094ba-868">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="094ba-869">Tape la valeur comme `int`.</span><span class="sxs-lookup"><span data-stu-id="094ba-869">Types the value as an `int`.</span></span>
* <span data-ttu-id="094ba-870">Stocke la valeur dans la propriété `NumberConfig` pour une utilisation par la page.</span><span class="sxs-lookup"><span data-stu-id="094ba-870">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="094ba-871">GetSection, GetChildren et Exists</span><span class="sxs-lookup"><span data-stu-id="094ba-871">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="094ba-872">Pour les exemples qui suivent, utilisez le fichier JSON suivant.</span><span class="sxs-lookup"><span data-stu-id="094ba-872">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="094ba-873">Quatre clés se trouvent dans deux sections, dont l’une inclut deux sous-sections :</span><span class="sxs-lookup"><span data-stu-id="094ba-873">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="094ba-874">Lorsque le fichier est lu dans la configuration, les clés hiérarchiques uniques suivantes sont créées pour stocker les valeurs de la configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-874">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="094ba-875">section0:key0</span><span class="sxs-lookup"><span data-stu-id="094ba-875">section0:key0</span></span>
* <span data-ttu-id="094ba-876">section0:key1</span><span class="sxs-lookup"><span data-stu-id="094ba-876">section0:key1</span></span>
* <span data-ttu-id="094ba-877">section1:key0</span><span class="sxs-lookup"><span data-stu-id="094ba-877">section1:key0</span></span>
* <span data-ttu-id="094ba-878">section1:key1</span><span class="sxs-lookup"><span data-stu-id="094ba-878">section1:key1</span></span>
* <span data-ttu-id="094ba-879">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="094ba-879">section2:subsection0:key0</span></span>
* <span data-ttu-id="094ba-880">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="094ba-880">section2:subsection0:key1</span></span>
* <span data-ttu-id="094ba-881">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="094ba-881">section2:subsection1:key0</span></span>
* <span data-ttu-id="094ba-882">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="094ba-882">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="094ba-883">GetSection</span><span class="sxs-lookup"><span data-stu-id="094ba-883">GetSection</span></span>

<span data-ttu-id="094ba-884">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrait une sous-section de la configuration avec la clé de sous-section spécifiée.</span><span class="sxs-lookup"><span data-stu-id="094ba-884">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="094ba-885">Pour retourner un <xref:Microsoft.Extensions.Configuration.IConfigurationSection> contenant uniquement les paires clé-valeur dans `section1`, appelez `GetSection` et fournissez le nom de section :</span><span class="sxs-lookup"><span data-stu-id="094ba-885">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="094ba-886">`configSection` n’a pas de valeur, seulement une clé et un chemin.</span><span class="sxs-lookup"><span data-stu-id="094ba-886">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="094ba-887">De même, pour obtenir les valeurs des clés dans `section2:subsection0`, appelez `GetSection` et fournissez le chemin d’accès de la section :</span><span class="sxs-lookup"><span data-stu-id="094ba-887">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="094ba-888">`GetSection` ne retourne jamais `null`.</span><span class="sxs-lookup"><span data-stu-id="094ba-888">`GetSection` never returns `null`.</span></span> <span data-ttu-id="094ba-889">Si aucune section correspondante n’est trouvée, une valeur `IConfigurationSection` vide est retournée.</span><span class="sxs-lookup"><span data-stu-id="094ba-889">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="094ba-890">Quand `GetSection` retourne une section correspondante, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="094ba-890">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="094ba-891"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> et <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> sont retournés quand la section existe.</span><span class="sxs-lookup"><span data-stu-id="094ba-891">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="094ba-892">GetChildren</span><span class="sxs-lookup"><span data-stu-id="094ba-892">GetChildren</span></span>

<span data-ttu-id="094ba-893">Un appel à [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) sur `section2` obtient un `IEnumerable<IConfigurationSection>` qui inclut :</span><span class="sxs-lookup"><span data-stu-id="094ba-893">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="094ba-894">Exists</span><span class="sxs-lookup"><span data-stu-id="094ba-894">Exists</span></span>

<span data-ttu-id="094ba-895">Utilisez [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pour déterminer si une section de configuration existe :</span><span class="sxs-lookup"><span data-stu-id="094ba-895">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="094ba-896">Compte tenu des données d’exemple, `sectionExists` est `false`, car il n’y a pas de section `section2:subsection2` dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-896">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="094ba-897">Établir une liaison à un graphe d’objets</span><span class="sxs-lookup"><span data-stu-id="094ba-897">Bind to an object graph</span></span>

<span data-ttu-id="094ba-898"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> est capable de lier l’intégralité d’un graphe d’objets POCO.</span><span class="sxs-lookup"><span data-stu-id="094ba-898"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="094ba-899">Comme pour la liaison d’un objet simple, seules les propriétés accessibles en lecture/écriture publiques sont liées.</span><span class="sxs-lookup"><span data-stu-id="094ba-899">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="094ba-900">L’exemple contient un modèle `TvShow` dont le graphe d’objets inclut les classes `Metadata` et `Actors` (*Models/TvShow.cs*) :</span><span class="sxs-lookup"><span data-stu-id="094ba-900">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="094ba-901">L’exemple d’application a un fichier *tvshow.xml* contenant les données de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-901">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="094ba-902">La configuration est liée à l’intégralité du graphe d’objets `TvShow` avec la méthode `Bind`.</span><span class="sxs-lookup"><span data-stu-id="094ba-902">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="094ba-903">L’instance liée est affectée à une propriété pour le rendu :</span><span class="sxs-lookup"><span data-stu-id="094ba-903">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="094ba-904">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) lie et retourne le type spécifié.</span><span class="sxs-lookup"><span data-stu-id="094ba-904">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="094ba-905">Il est plus pratique d’utiliser `Get<T>` que `Bind`.</span><span class="sxs-lookup"><span data-stu-id="094ba-905">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="094ba-906">Le code suivant montre comment utiliser `Get<T>` avec l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="094ba-906">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="094ba-907">Lier un tableau à une classe</span><span class="sxs-lookup"><span data-stu-id="094ba-907">Bind an array to a class</span></span>

<span data-ttu-id="094ba-908">*L’exemple d’application illustre les concepts abordés dans cette section.*</span><span class="sxs-lookup"><span data-stu-id="094ba-908">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="094ba-909"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-909">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="094ba-910">Tout format de tableau qui expose un segment de clé numérique ( `:0:` , `:1:` , &hellip; `:{n}:` ) est capable d’effectuer une liaison de tableau à un tableau de classes POCO.</span><span class="sxs-lookup"><span data-stu-id="094ba-910">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="094ba-911">La liaison est fournie par convention.</span><span class="sxs-lookup"><span data-stu-id="094ba-911">Binding is provided by convention.</span></span> <span data-ttu-id="094ba-912">Les fournisseurs de configuration personnalisés ne sont pas obligés d’implémenter la liaison de tableau.</span><span class="sxs-lookup"><span data-stu-id="094ba-912">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="094ba-913">**Traitement de tableau en mémoire**</span><span class="sxs-lookup"><span data-stu-id="094ba-913">**In-memory array processing**</span></span>

<span data-ttu-id="094ba-914">Observez les valeurs et les clés de configuration indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="094ba-914">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="094ba-915">Clé</span><span class="sxs-lookup"><span data-stu-id="094ba-915">Key</span></span>             | <span data-ttu-id="094ba-916">Valeur</span><span class="sxs-lookup"><span data-stu-id="094ba-916">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="094ba-917">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="094ba-917">array:entries:0</span></span> | <span data-ttu-id="094ba-918">value0</span><span class="sxs-lookup"><span data-stu-id="094ba-918">value0</span></span> |
| <span data-ttu-id="094ba-919">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="094ba-919">array:entries:1</span></span> | <span data-ttu-id="094ba-920">valeur1</span><span class="sxs-lookup"><span data-stu-id="094ba-920">value1</span></span> |
| <span data-ttu-id="094ba-921">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="094ba-921">array:entries:2</span></span> | <span data-ttu-id="094ba-922">valeur2</span><span class="sxs-lookup"><span data-stu-id="094ba-922">value2</span></span> |
| <span data-ttu-id="094ba-923">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="094ba-923">array:entries:4</span></span> | <span data-ttu-id="094ba-924">value4</span><span class="sxs-lookup"><span data-stu-id="094ba-924">value4</span></span> |
| <span data-ttu-id="094ba-925">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="094ba-925">array:entries:5</span></span> | <span data-ttu-id="094ba-926">value5</span><span class="sxs-lookup"><span data-stu-id="094ba-926">value5</span></span> |

<span data-ttu-id="094ba-927">Ces clés et valeurs sont chargées dans l’exemple d’application à l’aide du Fournisseur de configuration de mémoire :</span><span class="sxs-lookup"><span data-stu-id="094ba-927">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="094ba-928">Le tableau ignore une valeur pour l’index &num;3.</span><span class="sxs-lookup"><span data-stu-id="094ba-928">The array skips a value for index &num;3.</span></span> <span data-ttu-id="094ba-929">Le binder de configuration n’est pas capable de lier des valeurs null ou de créer des entrées null dans les objets liés, ce qui deviendra clair dans un moment lorsque le résultat de liaison de ce tableau à un objet est illustré.</span><span class="sxs-lookup"><span data-stu-id="094ba-929">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="094ba-930">Dans l’exemple d’application, une classe POCO est disponible pour contenir les données de configuration liées :</span><span class="sxs-lookup"><span data-stu-id="094ba-930">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="094ba-931">Les données de configuration sont liées à l’objet :</span><span class="sxs-lookup"><span data-stu-id="094ba-931">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="094ba-932">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) la syntaxe peut également être utilisée, ce qui se traduit par un code plus compact :</span><span class="sxs-lookup"><span data-stu-id="094ba-932">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="094ba-933">L’objet lié, une instance de `ArrayExample`, reçoit les données de tableau à partir de la configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-933">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="094ba-934">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="094ba-934">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="094ba-935">Valeur `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="094ba-935">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="094ba-936">0</span><span class="sxs-lookup"><span data-stu-id="094ba-936">0</span></span>                            | <span data-ttu-id="094ba-937">value0</span><span class="sxs-lookup"><span data-stu-id="094ba-937">value0</span></span>                       |
| <span data-ttu-id="094ba-938">1</span><span class="sxs-lookup"><span data-stu-id="094ba-938">1</span></span>                            | <span data-ttu-id="094ba-939">valeur1</span><span class="sxs-lookup"><span data-stu-id="094ba-939">value1</span></span>                       |
| <span data-ttu-id="094ba-940">2</span><span class="sxs-lookup"><span data-stu-id="094ba-940">2</span></span>                            | <span data-ttu-id="094ba-941">valeur2</span><span class="sxs-lookup"><span data-stu-id="094ba-941">value2</span></span>                       |
| <span data-ttu-id="094ba-942">3</span><span class="sxs-lookup"><span data-stu-id="094ba-942">3</span></span>                            | <span data-ttu-id="094ba-943">value4</span><span class="sxs-lookup"><span data-stu-id="094ba-943">value4</span></span>                       |
| <span data-ttu-id="094ba-944">4</span><span class="sxs-lookup"><span data-stu-id="094ba-944">4</span></span>                            | <span data-ttu-id="094ba-945">value5</span><span class="sxs-lookup"><span data-stu-id="094ba-945">value5</span></span>                       |

<span data-ttu-id="094ba-946">L’index &num;3 dans l’objet lié contient les données de configuration pour la clé de configuration `array:4` et sa valeur de `value4`.</span><span class="sxs-lookup"><span data-stu-id="094ba-946">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="094ba-947">Lorsque des données de configuration contenant un tableau sont liées, les index de tableau dans les clés de configuration sont simplement utilisés pour itérer les données de configuration lors de la création de l’objet.</span><span class="sxs-lookup"><span data-stu-id="094ba-947">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="094ba-948">Une valeur null ne peut pas être conservée dans des données de configuration, et une entrée à valeur null n’est pas créée dans un objet lié quand un tableau dans des clés de configuration ignore un ou plusieurs index.</span><span class="sxs-lookup"><span data-stu-id="094ba-948">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="094ba-949">L’élément de configuration manquant pour l’index &num;3 peut être fourni avant la liaison à l’instance `ArrayExample` par n’importe quel fournisseur de configuration qui génère la paire clé-valeur correcte dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-949">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="094ba-950">Si l’exemple inclut un Fournisseur de configuration JSON supplémentaire avec la paire clé-valeur manquante, `ArrayExample.Entries` correspond à l’intégralité du tableau de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-950">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="094ba-951">*missing_value.json* :</span><span class="sxs-lookup"><span data-stu-id="094ba-951">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="094ba-952">Dans `ConfigureAppConfiguration` :</span><span class="sxs-lookup"><span data-stu-id="094ba-952">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="094ba-953">La paire clé-valeur indiquée dans le tableau est chargée dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-953">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="094ba-954">Clé</span><span class="sxs-lookup"><span data-stu-id="094ba-954">Key</span></span>             | <span data-ttu-id="094ba-955">Valeur</span><span class="sxs-lookup"><span data-stu-id="094ba-955">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="094ba-956">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="094ba-956">array:entries:3</span></span> | <span data-ttu-id="094ba-957">valeur3</span><span class="sxs-lookup"><span data-stu-id="094ba-957">value3</span></span> |

<span data-ttu-id="094ba-958">Si l’instance de classe `ArrayExample` est liée une fois que le Fournisseur de configuration JSON inclut l’entrée pour l’index &num;3, le tableau `ArrayExample.Entries` inclut la valeur.</span><span class="sxs-lookup"><span data-stu-id="094ba-958">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="094ba-959">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="094ba-959">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="094ba-960">Valeur `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="094ba-960">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="094ba-961">0</span><span class="sxs-lookup"><span data-stu-id="094ba-961">0</span></span>                            | <span data-ttu-id="094ba-962">value0</span><span class="sxs-lookup"><span data-stu-id="094ba-962">value0</span></span>                       |
| <span data-ttu-id="094ba-963">1</span><span class="sxs-lookup"><span data-stu-id="094ba-963">1</span></span>                            | <span data-ttu-id="094ba-964">valeur1</span><span class="sxs-lookup"><span data-stu-id="094ba-964">value1</span></span>                       |
| <span data-ttu-id="094ba-965">2</span><span class="sxs-lookup"><span data-stu-id="094ba-965">2</span></span>                            | <span data-ttu-id="094ba-966">valeur2</span><span class="sxs-lookup"><span data-stu-id="094ba-966">value2</span></span>                       |
| <span data-ttu-id="094ba-967">3</span><span class="sxs-lookup"><span data-stu-id="094ba-967">3</span></span>                            | <span data-ttu-id="094ba-968">valeur3</span><span class="sxs-lookup"><span data-stu-id="094ba-968">value3</span></span>                       |
| <span data-ttu-id="094ba-969">4</span><span class="sxs-lookup"><span data-stu-id="094ba-969">4</span></span>                            | <span data-ttu-id="094ba-970">value4</span><span class="sxs-lookup"><span data-stu-id="094ba-970">value4</span></span>                       |
| <span data-ttu-id="094ba-971">5</span><span class="sxs-lookup"><span data-stu-id="094ba-971">5</span></span>                            | <span data-ttu-id="094ba-972">value5</span><span class="sxs-lookup"><span data-stu-id="094ba-972">value5</span></span>                       |

<span data-ttu-id="094ba-973">**Traitement de tableau JSON**</span><span class="sxs-lookup"><span data-stu-id="094ba-973">**JSON array processing**</span></span>

<span data-ttu-id="094ba-974">Si un fichier JSON contient un tableau, les clés de configuration sont créés pour les éléments du tableau avec un index de section basé sur zéro.</span><span class="sxs-lookup"><span data-stu-id="094ba-974">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="094ba-975">Dans le fichier de configuration suivant, `subsection` est un tableau :</span><span class="sxs-lookup"><span data-stu-id="094ba-975">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="094ba-976">Le Fournisseur de configuration JSON lit les données de configuration dans les paires clé-valeur suivantes :</span><span class="sxs-lookup"><span data-stu-id="094ba-976">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="094ba-977">Clé</span><span class="sxs-lookup"><span data-stu-id="094ba-977">Key</span></span>                     | <span data-ttu-id="094ba-978">Valeur</span><span class="sxs-lookup"><span data-stu-id="094ba-978">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="094ba-979">json_array:key</span><span class="sxs-lookup"><span data-stu-id="094ba-979">json_array:key</span></span>          | <span data-ttu-id="094ba-980">valueA</span><span class="sxs-lookup"><span data-stu-id="094ba-980">valueA</span></span> |
| <span data-ttu-id="094ba-981">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="094ba-981">json_array:subsection:0</span></span> | <span data-ttu-id="094ba-982">valueB</span><span class="sxs-lookup"><span data-stu-id="094ba-982">valueB</span></span> |
| <span data-ttu-id="094ba-983">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="094ba-983">json_array:subsection:1</span></span> | <span data-ttu-id="094ba-984">valueC</span><span class="sxs-lookup"><span data-stu-id="094ba-984">valueC</span></span> |
| <span data-ttu-id="094ba-985">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="094ba-985">json_array:subsection:2</span></span> | <span data-ttu-id="094ba-986">valueD</span><span class="sxs-lookup"><span data-stu-id="094ba-986">valueD</span></span> |

<span data-ttu-id="094ba-987">Dans l’exemple d’application, la classe POCO suivante est disponible pour lier les paires clé-valeur de configuration :</span><span class="sxs-lookup"><span data-stu-id="094ba-987">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="094ba-988">Après la liaison, `JsonArrayExample.Key` contient la valeur `valueA`.</span><span class="sxs-lookup"><span data-stu-id="094ba-988">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="094ba-989">Les valeurs de la sous-section sont stockées dans la propriété de tableau POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="094ba-989">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="094ba-990">Index `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="094ba-990">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="094ba-991">Valeur `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="094ba-991">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="094ba-992">0</span><span class="sxs-lookup"><span data-stu-id="094ba-992">0</span></span>                                   | <span data-ttu-id="094ba-993">valueB</span><span class="sxs-lookup"><span data-stu-id="094ba-993">valueB</span></span>                              |
| <span data-ttu-id="094ba-994">1</span><span class="sxs-lookup"><span data-stu-id="094ba-994">1</span></span>                                   | <span data-ttu-id="094ba-995">valueC</span><span class="sxs-lookup"><span data-stu-id="094ba-995">valueC</span></span>                              |
| <span data-ttu-id="094ba-996">2</span><span class="sxs-lookup"><span data-stu-id="094ba-996">2</span></span>                                   | <span data-ttu-id="094ba-997">valueD</span><span class="sxs-lookup"><span data-stu-id="094ba-997">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="094ba-998">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="094ba-998">Custom configuration provider</span></span>

<span data-ttu-id="094ba-999">L’exemple d’application montre comment créer un fournisseur de configuration de base qui lit les paires clé-valeur de configuration à partir d’une base de données à l’aide [d’Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="094ba-999">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="094ba-1000">Le fournisseur présente les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="094ba-1000">The provider has the following characteristics:</span></span>

* <span data-ttu-id="094ba-1001">La base de données en mémoire EF est utilisée à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="094ba-1001">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="094ba-1002">Pour utiliser une base de données qui nécessite une chaîne de connexion, implémentez un autre `ConfigurationBuilder` pour fournir la chaîne de connexion à partir d’un autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="094ba-1002">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="094ba-1003">Le fournisseur lit une table de base de données dans la configuration au démarrage.</span><span class="sxs-lookup"><span data-stu-id="094ba-1003">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="094ba-1004">Le fournisseur n’interroge pas la base de données par clé.</span><span class="sxs-lookup"><span data-stu-id="094ba-1004">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="094ba-1005">Le rechargement en cas de changement n’est pas implémenté, par conséquent, la mise à jour la base de données après le démarrage de l’application n’a aucun effet sur la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-1005">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="094ba-1006">Définissez une entité `EFConfigurationValue` pour le stockage des valeurs de configuration dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="094ba-1006">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="094ba-1007">*Models/EFConfigurationValue.cs* :</span><span class="sxs-lookup"><span data-stu-id="094ba-1007">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="094ba-1008">Ajoutez un `EFConfigurationContext` pour stocker les valeurs configurées et y accéder.</span><span class="sxs-lookup"><span data-stu-id="094ba-1008">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="094ba-1009">*EFConfigurationProvider/EFConfigurationContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="094ba-1009">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="094ba-1010">Créez une classe qui implémente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="094ba-1010">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="094ba-1011">*EFConfigurationProvider/EFConfigurationSource.cs* :</span><span class="sxs-lookup"><span data-stu-id="094ba-1011">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="094ba-1012">Créez le fournisseur de configuration personnalisé en héritant de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="094ba-1012">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="094ba-1013">Le fournisseur de configuration initialise la base de données quand elle est vide.</span><span class="sxs-lookup"><span data-stu-id="094ba-1013">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="094ba-1014">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span><span class="sxs-lookup"><span data-stu-id="094ba-1014">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="094ba-1015">Une méthode d’extension `AddEFConfiguration` permet d’ajouter la source de configuration à un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="094ba-1015">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="094ba-1016">*Extensions/EntityFrameworkExtensions.cs* :</span><span class="sxs-lookup"><span data-stu-id="094ba-1016">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="094ba-1017">Le code suivant montre comment utiliser le `EFConfigurationProvider` personnalisé dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="094ba-1017">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="094ba-1018">Accéder à la configuration lors du démarrage</span><span class="sxs-lookup"><span data-stu-id="094ba-1018">Access configuration during startup</span></span>

<span data-ttu-id="094ba-1019">Injectez `IConfiguration` dans le constructeur `Startup` pour accéder aux valeurs de configuration dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="094ba-1019">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="094ba-1020">Pour accéder à la configuration dans `Startup.Configure`, injectez `IConfiguration` directement dans la méthode ou utilisez l’instance à partir du constructeur :</span><span class="sxs-lookup"><span data-stu-id="094ba-1020">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="094ba-1021">Pour obtenir un exemple d’accès à la configuration à l’aide des méthodes pratiques de démarrage, consultez [Démarrage de l’application : méthodes pratiques](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="094ba-1021">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="094ba-1022">Configuration de l’accès dans une Razor page pages ou une vue MVC</span><span class="sxs-lookup"><span data-stu-id="094ba-1022">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="094ba-1023">Pour accéder aux paramètres de configuration dans une Razor page pages ou une vue MVC, ajoutez une [directive using](xref:mvc/views/razor#using) ([référence C# : directive using](/dotnet/csharp/language-reference/keywords/using-directive)) pour l' [ espace de nomsMicrosoft.Extensions.Configfiguration](xref:Microsoft.Extensions.Configuration) et injectez <xref:Microsoft.Extensions.Configuration.IConfiguration> dans la page ou la vue.</span><span class="sxs-lookup"><span data-stu-id="094ba-1023">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="094ba-1024">Dans une Razor page pages :</span><span class="sxs-lookup"><span data-stu-id="094ba-1024">In a Razor Pages page:</span></span>

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

<span data-ttu-id="094ba-1025">Dans une vue MVC :</span><span class="sxs-lookup"><span data-stu-id="094ba-1025">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="094ba-1026">Ajouter la configuration à partir d’un assembly externe</span><span class="sxs-lookup"><span data-stu-id="094ba-1026">Add configuration from an external assembly</span></span>

<span data-ttu-id="094ba-1027">Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application.</span><span class="sxs-lookup"><span data-stu-id="094ba-1027">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="094ba-1028">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="094ba-1028">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="094ba-1029">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="094ba-1029">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

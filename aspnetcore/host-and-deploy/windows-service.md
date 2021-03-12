---
title: Héberger ASP.NET Core dans un service Windows
author: rick-anderson
description: Découvrez comment héberger une application ASP.NET Core dans un service Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: e5c7dd0e52f1246d3ac6ad9622573db4c276654b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588813"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="fe707-103">Héberger ASP.NET Core dans un service Windows</span><span class="sxs-lookup"><span data-stu-id="fe707-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fe707-104">Une application ASP.NET Core peut être hébergée sur Windows en tant que [service Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sans utiliser IIS.</span><span class="sxs-lookup"><span data-stu-id="fe707-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="fe707-105">Lorsqu’elle est hébergée en tant que service Windows, l’application se lance automatiquement après le redémarrage du serveur.</span><span class="sxs-lookup"><span data-stu-id="fe707-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="fe707-106">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fe707-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fe707-107">Prérequis</span><span class="sxs-lookup"><span data-stu-id="fe707-107">Prerequisites</span></span>

* [<span data-ttu-id="fe707-108">Kit de développement logiciel (SDK) ASP.NET Core 2.1 ou plus</span><span class="sxs-lookup"><span data-stu-id="fe707-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="fe707-109">PowerShell 6,2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="fe707-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="fe707-110">Modèle Service Worker</span><span class="sxs-lookup"><span data-stu-id="fe707-110">Worker Service template</span></span>

<span data-ttu-id="fe707-111">Le modèle Service Worker ASP.NET Core fournit un point de départ pour l’écriture d’applications de service durables.</span><span class="sxs-lookup"><span data-stu-id="fe707-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="fe707-112">Pour utiliser le modèle en tant que base d’une application de service Windows :</span><span class="sxs-lookup"><span data-stu-id="fe707-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="fe707-113">Créez une application Service Worker à partir du modèle .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fe707-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="fe707-114">Suivez l’aide fournie dans la section [Configuration d’application](#app-configuration) pour mettre à jour l’application Service Worker afin qu’elle puisse s’exécuter en tant que service Windows.</span><span class="sxs-lookup"><span data-stu-id="fe707-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="fe707-115">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="fe707-115">App configuration</span></span>

<span data-ttu-id="fe707-116">L’application requiert une référence de package pour [Microsoft. extensions. Hosting. services Windows,](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="fe707-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="fe707-117">`IHostBuilder.UseWindowsService` est appelé lors de la génération de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="fe707-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="fe707-118">Si l’application s’exécute comme un service Windows, la méthode :</span><span class="sxs-lookup"><span data-stu-id="fe707-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="fe707-119">définit la durée de vie de l’hôte sur `WindowsServiceLifetime` ;</span><span class="sxs-lookup"><span data-stu-id="fe707-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="fe707-120">Affecte à la [racine du contenu](xref:fundamentals/index#content-root) la valeur [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="fe707-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="fe707-121">Pour plus d’informations, consultez la section [Répertoire actif et racine du contenu](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="fe707-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="fe707-122">Active la journalisation dans le journal des événements :</span><span class="sxs-lookup"><span data-stu-id="fe707-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="fe707-123">Le nom de l’application est utilisé comme nom de source par défaut.</span><span class="sxs-lookup"><span data-stu-id="fe707-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="fe707-124">Le niveau de journalisation par défaut est *Avertissement* ou supérieur pour une application basée sur un modèle de ASP.net core qui appelle `CreateDefaultBuilder` pour créer l’hôte.</span><span class="sxs-lookup"><span data-stu-id="fe707-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="fe707-125">Remplacez le niveau de journalisation par défaut par la `Logging:EventLog:LogLevel:Default` clé dans *appsettings.json* / *appSettings. { Environment}. JSON* ou un autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="fe707-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="fe707-126">Seuls les administrateurs peuvent créer des sources d’événement.</span><span class="sxs-lookup"><span data-stu-id="fe707-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="fe707-127">Si une source d’événement ne peut pas être créée en utilisant le nom de l’application, un avertissement est consigné dans la source *Application* source et les journaux d’événements sont désactivés.</span><span class="sxs-lookup"><span data-stu-id="fe707-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="fe707-128">Dans `CreateHostBuilder` de *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="fe707-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="fe707-129">Les exemples d’applications suivants accompagnent cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="fe707-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="fe707-130">Exemple de service de travail en arrière-plan : exemple d’application non Web basé sur le [modèle de service Worker](#worker-service-template) qui utilise des [services hébergés](xref:fundamentals/host/hosted-services) pour les tâches en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="fe707-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="fe707-131">Exemple de App Service Web : Razor exemple d’application Web pages qui s’exécute en tant que service Windows avec les [services hébergés](xref:fundamentals/host/hosted-services) pour les tâches en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="fe707-131">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="fe707-132">Pour obtenir des conseils sur MVC, consultez les articles sous <xref:mvc/overview> et <xref:migration/22-to-30> .</span><span class="sxs-lookup"><span data-stu-id="fe707-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="fe707-133">Type de déploiement</span><span class="sxs-lookup"><span data-stu-id="fe707-133">Deployment type</span></span>

<span data-ttu-id="fe707-134">Pour des informations et des conseils sur les scénarios de déploiement, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="fe707-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="fe707-135">SDK</span><span class="sxs-lookup"><span data-stu-id="fe707-135">SDK</span></span>

<span data-ttu-id="fe707-136">Pour un service basé sur une application Web qui utilise les Razor pages ou les infrastructures MVC, spécifiez le kit de développement logiciel (SDK) Web dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="fe707-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="fe707-137">Si le service exécute uniquement des tâches en arrière-plan (par exemple, les [services hébergés](xref:fundamentals/host/hosted-services)), spécifiez le kit de développement logiciel (SDK) Worker dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="fe707-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="fe707-138">Déploiement dépendant du framework</span><span class="sxs-lookup"><span data-stu-id="fe707-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="fe707-139">Un déploiement dépendant du framework s’appuie sur la présence d’une version partagée à l’échelle du système de .NET Core sur le système cible.</span><span class="sxs-lookup"><span data-stu-id="fe707-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="fe707-140">Lorsque vous effectuez le scénario de déploiement dépendant du framework en suivant les conseils du présent article, le Kit de développement logiciel (SDK) produit un fichier exécutable (*.exe*), appelé *fichier exécutable dépendant du framework*.</span><span class="sxs-lookup"><span data-stu-id="fe707-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="fe707-141">Si vous utilisez le [Kit de développement logiciel (SDK) Web](#sdk), un fichier de *web.config* , qui est normalement généré lors de la publication d’une application ASP.net Core, n’est pas nécessaire pour une application de services Windows.</span><span class="sxs-lookup"><span data-stu-id="fe707-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="fe707-142">Pour désactiver la création d’un fichier *web.config*, ajoutez la propriété `<IsTransformWebConfigDisabled>` définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="fe707-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="fe707-143">Déploiement autonome</span><span class="sxs-lookup"><span data-stu-id="fe707-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="fe707-144">Un déploiement autonome ne s’appuie sur la présence d’aucune infrastructure partagée sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="fe707-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="fe707-145">Le runtime et les dépendances de l’application sont déployés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="fe707-146">Un [identificateur de runtime (RID)](/dotnet/core/rid-catalog) Windows est inclus dans l’élément `<PropertyGroup>` qui contient la version cible de .NET Framework :</span><span class="sxs-lookup"><span data-stu-id="fe707-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="fe707-147">Pour publier pour plusieurs RID :</span><span class="sxs-lookup"><span data-stu-id="fe707-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="fe707-148">Fournissez les RID dans une liste séparée par des points-virgules.</span><span class="sxs-lookup"><span data-stu-id="fe707-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="fe707-149">Utilisez le nom de la propriété [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (pluriel).</span><span class="sxs-lookup"><span data-stu-id="fe707-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="fe707-150">Pour plus d’informations, consultez le [Catalogue RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="fe707-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="fe707-151">Compte d’utilisateur du service</span><span class="sxs-lookup"><span data-stu-id="fe707-151">Service user account</span></span>

<span data-ttu-id="fe707-152">Pour créer un compte d’utilisateur du service, utilisez la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) depuis un interpréteur de commandes d’administration PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="fe707-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="fe707-153">Dans la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) ou plus :</span><span class="sxs-lookup"><span data-stu-id="fe707-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="fe707-154">Dans un système d’exploitation Windows antérieur à la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) :</span><span class="sxs-lookup"><span data-stu-id="fe707-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="fe707-155">Fournissez un [mot de passe fort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) à l’invite.</span><span class="sxs-lookup"><span data-stu-id="fe707-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="fe707-156">Le compte n’expire pas, sauf si le paramètre `-AccountExpires` est fourni à la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) avec un <xref:System.DateTime> d’expiration.</span><span class="sxs-lookup"><span data-stu-id="fe707-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="fe707-157">Pour plus d’informations, voir [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) et [Comptes d’utilisateurs de service](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="fe707-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="fe707-158">Une approche alternative à la gestion des utilisateurs lors de l’utilisation d’Active Directory consiste à utiliser des Comptes de service administrés.</span><span class="sxs-lookup"><span data-stu-id="fe707-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="fe707-159">Pour plus d’informations, consultez [Vue d’ensemble des comptes de service administrés de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="fe707-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="fe707-160">Droits d’ouverture de session en tant que service</span><span class="sxs-lookup"><span data-stu-id="fe707-160">Log on as a service rights</span></span>

<span data-ttu-id="fe707-161">Pour établir des droits *d’ouverture de session en tant que service* pour un compte d’utilisateur de service, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="fe707-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="fe707-162">Ouvrez l’éditeur de stratégie de sécurité locale en exécutant le fichier *secpool.msc*.</span><span class="sxs-lookup"><span data-stu-id="fe707-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="fe707-163">Développez le nœud **Stratégies locales** et sélectionnez **Attribution des droits utilisateur**.</span><span class="sxs-lookup"><span data-stu-id="fe707-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="fe707-164">Ouvrez la stratégie **Ouvrir une session en tant que service**.</span><span class="sxs-lookup"><span data-stu-id="fe707-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="fe707-165">Sélectionnez **Ajouter un utilisateur ou un groupe**.</span><span class="sxs-lookup"><span data-stu-id="fe707-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="fe707-166">Fournissez le nom d’objet (compte d’utilisateur) avec l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="fe707-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="fe707-167">Tapez le compte d’utilisateur (`{DOMAIN OR COMPUTER NAME\USER}`) dans le champ du nom d’objet et sélectionnez **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="fe707-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="fe707-168">Sélectionnez **Avancé**.</span><span class="sxs-lookup"><span data-stu-id="fe707-168">Select **Advanced**.</span></span> <span data-ttu-id="fe707-169">Sélectionnez **Rechercher maintenant**.</span><span class="sxs-lookup"><span data-stu-id="fe707-169">Select **Find Now**.</span></span> <span data-ttu-id="fe707-170">Sélectionnez le compte d’utilisateur dans la liste.</span><span class="sxs-lookup"><span data-stu-id="fe707-170">Select the user account from the list.</span></span> <span data-ttu-id="fe707-171">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="fe707-171">Select **OK**.</span></span> <span data-ttu-id="fe707-172">Cliquez à nouveau sur **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="fe707-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="fe707-173">Sélectionnez **OK** ou **Appliquer** pour accepter les modifications.</span><span class="sxs-lookup"><span data-stu-id="fe707-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="fe707-174">Créer et gérer le service Windows</span><span class="sxs-lookup"><span data-stu-id="fe707-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="fe707-175">Créer un service</span><span class="sxs-lookup"><span data-stu-id="fe707-175">Create a service</span></span>

<span data-ttu-id="fe707-176">Utilisez les commandes PowerShell pour enregistrer un service.</span><span class="sxs-lookup"><span data-stu-id="fe707-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="fe707-177">À partir d’un interpréteur de commandes d’administration PowerShell 6, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="fe707-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="fe707-178">`{EXE PATH}`: Chemin d’accès au dossier de l’application sur l’hôte (par exemple, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="fe707-179">N’incluez pas le fichier exécutable de l’application dans le chemin.</span><span class="sxs-lookup"><span data-stu-id="fe707-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="fe707-180">Aucune barre oblique de fin n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="fe707-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="fe707-181">`{DOMAIN OR COMPUTER NAME\USER}`: Compte d’utilisateur du service (par exemple, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="fe707-182">`{SERVICE NAME}`: Nom du service (par exemple, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="fe707-183">`{EXE FILE PATH}`: Le chemin d’accès de l’exécutable de l’application (par exemple, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="fe707-184">Incluez le nom de fichier de l’exécutable avec l’extension.</span><span class="sxs-lookup"><span data-stu-id="fe707-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="fe707-185">`{DESCRIPTION}`: Description du service (par exemple, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="fe707-186">`{DISPLAY NAME}`: Nom d’affichage du service (par exemple, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="fe707-187">Démarrer un service</span><span class="sxs-lookup"><span data-stu-id="fe707-187">Start a service</span></span>

<span data-ttu-id="fe707-188">Démarrez un service avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="fe707-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="fe707-189">La commande prend quelques secondes pour démarrer le service.</span><span class="sxs-lookup"><span data-stu-id="fe707-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="fe707-190">Déterminer l’état d’un service</span><span class="sxs-lookup"><span data-stu-id="fe707-190">Determine a service's status</span></span>

<span data-ttu-id="fe707-191">Pour vérifier l’état d’un service, utilisez la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="fe707-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="fe707-192">L’état est signalé comme étant l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="fe707-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="fe707-193">Arrêter un service</span><span class="sxs-lookup"><span data-stu-id="fe707-193">Stop a service</span></span>

<span data-ttu-id="fe707-194">Arrêtez un service avec la commande Powershell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="fe707-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="fe707-195">Supprimer un service</span><span class="sxs-lookup"><span data-stu-id="fe707-195">Remove a service</span></span>

<span data-ttu-id="fe707-196">Après un court délai pour arrêter un service, supprimez-le avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="fe707-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="fe707-197">Scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="fe707-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="fe707-198">Les services qui interagissent avec les requêtes provenant d’Internet ou d’un réseau d’entreprise et qui se trouvent derrière un proxy ou équilibreur de charge peuvent nécessiter une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="fe707-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="fe707-199">Pour plus d’informations, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="fe707-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="fe707-200">Configuration des points de terminaison</span><span class="sxs-lookup"><span data-stu-id="fe707-200">Configure endpoints</span></span>

<span data-ttu-id="fe707-201">Par défaut, ASP.NET Core est lié à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="fe707-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="fe707-202">Configurez l’URL et le port en définissant la `ASPNETCORE_URLS` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="fe707-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="fe707-203">Pour obtenir d’autres approches de configuration des ports et des URL, consultez l’article approprié sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="fe707-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <xref:fundamentals/servers/kestrel/endpoints>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

* <xref:fundamentals/servers/kestrel#endpoint-configuration>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="fe707-204">L’aide précédente couvre la prise en charge des points de terminaison HTTPs.</span><span class="sxs-lookup"><span data-stu-id="fe707-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="fe707-205">Par exemple, configurez l’application pour HTTPs lorsque l’authentification est utilisée avec un service Windows.</span><span class="sxs-lookup"><span data-stu-id="fe707-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="fe707-206">L’utilisation du certificat de développement HTTPS ASP.NET Core pour sécuriser un point de terminaison de service n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="fe707-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="fe707-207">Répertoire actif et racine du contenu</span><span class="sxs-lookup"><span data-stu-id="fe707-207">Current directory and content root</span></span>

<span data-ttu-id="fe707-208">Le répertoire de travail actuel retourné en appelant <xref:System.IO.Directory.GetCurrentDirectory%2A> pour un service Windows est le dossier *\\ \\ system32 de C : Windows* .</span><span class="sxs-lookup"><span data-stu-id="fe707-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory%2A> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="fe707-209">Le dossier *system32* n’est pas un emplacement approprié pour stocker les fichiers d’un service (tels que les fichiers de paramètres).</span><span class="sxs-lookup"><span data-stu-id="fe707-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="fe707-210">Utilisez une des approches suivantes pour gérer les ressources ainsi que les fichiers de paramètres d’un service, et y accéder.</span><span class="sxs-lookup"><span data-stu-id="fe707-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="fe707-211">Utiliser ContentRootPath ou ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="fe707-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="fe707-212">Utilisez les éléments [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) ou <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> pour localiser les ressources d’une application.</span><span class="sxs-lookup"><span data-stu-id="fe707-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="fe707-213">Lorsque l’application s’exécute en tant que service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> définit <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> sur [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="fe707-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="fe707-214">Les fichiers de paramètres par défaut de l’application, *appsettings.json* et *appSettings. { Environment}. JSON*, sont chargés à partir de la racine de contenu de l’application en appelant [CreateDefaultBuilder lors](xref:fundamentals/host/generic-host#set-up-a-host)de la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="fe707-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="fe707-215">Pour les autres fichiers de paramètres chargés par le code du développeur dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> , il n’est pas nécessaire d’appeler <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> .</span><span class="sxs-lookup"><span data-stu-id="fe707-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A>.</span></span> <span data-ttu-id="fe707-216">Dans l’exemple suivant, la *custom_settings.jssur* le fichier existe dans la racine de contenu de l’application et est chargée sans définir explicitement un chemin d’accès de base :</span><span class="sxs-lookup"><span data-stu-id="fe707-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="fe707-217">N’essayez pas d’utiliser <xref:System.IO.Directory.GetCurrentDirectory%2A> pour obtenir un chemin d’accès de ressource, car une application de service Windows retourne le dossier *C : \\ Windows \\ system32* comme répertoire actif.</span><span class="sxs-lookup"><span data-stu-id="fe707-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory%2A> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="fe707-218">Stocker les fichiers d’un service dans un emplacement approprié sur le disque</span><span class="sxs-lookup"><span data-stu-id="fe707-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="fe707-219">Spécifiez un chemin absolu avec <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A>, si vous utilisez <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, vers le dossier contenant les fichiers.</span><span class="sxs-lookup"><span data-stu-id="fe707-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="fe707-220">Dépanner</span><span class="sxs-lookup"><span data-stu-id="fe707-220">Troubleshoot</span></span>

<span data-ttu-id="fe707-221">Pour dépanner une application de service Windows, consultez <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="fe707-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="fe707-222">Erreurs courantes</span><span class="sxs-lookup"><span data-stu-id="fe707-222">Common errors</span></span>

* <span data-ttu-id="fe707-223">Une version ancienne ou préliminaire de PowerShell est en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="fe707-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="fe707-224">Le service inscrit n’utilise pas la sortie **publiée** de l’application à partir de la commande [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="fe707-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="fe707-225">La sortie de la commande [dotnet Build](/dotnet/core/tools/dotnet-build) n’est pas prise en charge pour le déploiement d’applications.</span><span class="sxs-lookup"><span data-stu-id="fe707-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="fe707-226">Les ressources publiées se trouvent dans l’un des dossiers suivants, selon le type de déploiement :</span><span class="sxs-lookup"><span data-stu-id="fe707-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="fe707-227">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="fe707-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="fe707-228">*bin/Release/{Target Framework}/{Runtime identificateur}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="fe707-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="fe707-229">Le service n’est pas à l’État en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="fe707-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="fe707-230">Les chemins d’accès aux ressources que l’application utilise (par exemple, les certificats) sont incorrects.</span><span class="sxs-lookup"><span data-stu-id="fe707-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="fe707-231">Le chemin d’accès de base d’un service Windows est *c : \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="fe707-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="fe707-232">L’utilisateur ne dispose pas de droits *d’ouverture de session en tant que service* .</span><span class="sxs-lookup"><span data-stu-id="fe707-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="fe707-233">Le mot de passe de l’utilisateur a expiré ou a été transmis de manière incorrecte lors de l’exécution de la `New-Service` commande PowerShell.</span><span class="sxs-lookup"><span data-stu-id="fe707-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="fe707-234">L’application requiert l’authentification ASP.NET Core, mais elle n’est pas configurée pour les connexions sécurisées (HTTPs).</span><span class="sxs-lookup"><span data-stu-id="fe707-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="fe707-235">Le port de l’URL de requête est incorrect ou n’est pas correctement configuré dans l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="fe707-236">Journaux des événements système et des applications</span><span class="sxs-lookup"><span data-stu-id="fe707-236">System and Application Event Logs</span></span>

<span data-ttu-id="fe707-237">Accédez aux journaux des événements système et des applications :</span><span class="sxs-lookup"><span data-stu-id="fe707-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="fe707-238">Ouvrez le menu Démarrer, recherchez *Observateur d’événements*, puis sélectionnez l’application **Observateur d’événements** .</span><span class="sxs-lookup"><span data-stu-id="fe707-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="fe707-239">Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.</span><span class="sxs-lookup"><span data-stu-id="fe707-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="fe707-240">Sélectionnez **système** pour ouvrir le journal des événements système.</span><span class="sxs-lookup"><span data-stu-id="fe707-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="fe707-241">Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="fe707-242">Recherchez les erreurs liées à l’application défectueuse.</span><span class="sxs-lookup"><span data-stu-id="fe707-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="fe707-243">Exécuter l’application depuis une invite de commandes</span><span class="sxs-lookup"><span data-stu-id="fe707-243">Run the app at a command prompt</span></span>

<span data-ttu-id="fe707-244">De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans les journaux des événements.</span><span class="sxs-lookup"><span data-stu-id="fe707-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="fe707-245">Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="fe707-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="fe707-246">Pour consigner des détails supplémentaires à partir de l’application, diminuez le [niveau de journalisation](xref:fundamentals/logging/index#log-level) ou exécutez l’application dans l' [environnement de développement](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="fe707-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="fe707-247">Effacer les caches de package</span><span class="sxs-lookup"><span data-stu-id="fe707-247">Clear package caches</span></span>

<span data-ttu-id="fe707-248">Une application fonctionnelle peut échouer immédiatement après la mise à niveau de la kit SDK .NET Core sur l’ordinateur de développement ou la modification des versions de package dans l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="fe707-249">Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures.</span><span class="sxs-lookup"><span data-stu-id="fe707-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="fe707-250">Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :</span><span class="sxs-lookup"><span data-stu-id="fe707-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="fe707-251">Supprimez les dossiers *bin* et *obj*.</span><span class="sxs-lookup"><span data-stu-id="fe707-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="fe707-252">Effacez les caches de package en exécutant [dotnet NuGet LOCALS tout--Clear](/dotnet/core/tools/dotnet-nuget-locals) dans une interface de commande.</span><span class="sxs-lookup"><span data-stu-id="fe707-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="fe707-253">L’effacement des caches de package peut également être effectué à l’aide de l’outil [nuget.exe](https://www.nuget.org/downloads) et en exécutant la commande `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="fe707-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="fe707-254">*NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="fe707-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="fe707-255">Restaurez et regénérez le projet.</span><span class="sxs-lookup"><span data-stu-id="fe707-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="fe707-256">Supprimez tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="fe707-257">Application lente ou bloquée</span><span class="sxs-lookup"><span data-stu-id="fe707-257">Slow or hanging app</span></span>

<span data-ttu-id="fe707-258">Un *vidage sur incident* est un instantané de la mémoire du système et peut aider à déterminer la cause d’un incident d’application, d’un échec de démarrage ou d’une application lente.</span><span class="sxs-lookup"><span data-stu-id="fe707-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="fe707-259">L’application cesse de fonctionner ou rencontre une exception</span><span class="sxs-lookup"><span data-stu-id="fe707-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="fe707-260">Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :</span><span class="sxs-lookup"><span data-stu-id="fe707-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="fe707-261">Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="fe707-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="fe707-262">Exécutez le [script PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) avec le nom de l’exécutable de l’application :</span><span class="sxs-lookup"><span data-stu-id="fe707-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="fe707-263">Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.</span><span class="sxs-lookup"><span data-stu-id="fe707-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="fe707-264">Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1) :</span><span class="sxs-lookup"><span data-stu-id="fe707-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="fe707-265">Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement.</span><span class="sxs-lookup"><span data-stu-id="fe707-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="fe707-266">Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.</span><span class="sxs-lookup"><span data-stu-id="fe707-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="fe707-267">Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).</span><span class="sxs-lookup"><span data-stu-id="fe707-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="fe707-268">L’application se bloque, ne démarre pas ou s’exécute normalement</span><span class="sxs-lookup"><span data-stu-id="fe707-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="fe707-269">Quand une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage ou s’exécute normalement, consultez [fichiers de vidage en mode utilisateur : choix de l’outil le mieux](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) adapté pour sélectionner un outil approprié pour produire le vidage.</span><span class="sxs-lookup"><span data-stu-id="fe707-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="fe707-270">Analyser le fichier dump</span><span class="sxs-lookup"><span data-stu-id="fe707-270">Analyze the dump</span></span>

<span data-ttu-id="fe707-271">Un fichier dump peut être analysé à l’aide de plusieurs approches.</span><span class="sxs-lookup"><span data-stu-id="fe707-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="fe707-272">Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).</span><span class="sxs-lookup"><span data-stu-id="fe707-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe707-273">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fe707-273">Additional resources</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"
* <span data-ttu-id="fe707-274">[Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel/endpoints) (inclut la configuration de HTTPS et la prise en charge de SNI)</span><span class="sxs-lookup"><span data-stu-id="fe707-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) (includes HTTPS configuration and SNI support)</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"
* <span data-ttu-id="fe707-275">[Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclut la configuration de HTTPS et la prise en charge de SNI)</span><span class="sxs-lookup"><span data-stu-id="fe707-275">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="fe707-276">Une application ASP.NET Core peut être hébergée sur Windows en tant que [service Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sans utiliser IIS.</span><span class="sxs-lookup"><span data-stu-id="fe707-276">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="fe707-277">Lorsqu’elle est hébergée en tant que service Windows, l’application se lance automatiquement après le redémarrage du serveur.</span><span class="sxs-lookup"><span data-stu-id="fe707-277">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="fe707-278">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fe707-278">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fe707-279">Prérequis</span><span class="sxs-lookup"><span data-stu-id="fe707-279">Prerequisites</span></span>

* [<span data-ttu-id="fe707-280">Kit de développement logiciel (SDK) ASP.NET Core 2.1 ou plus</span><span class="sxs-lookup"><span data-stu-id="fe707-280">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="fe707-281">PowerShell 6,2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="fe707-281">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="fe707-282">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="fe707-282">App configuration</span></span>

<span data-ttu-id="fe707-283">L’application nécessite des références de package pour [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) et [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="fe707-283">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="fe707-284">Pour effectuer des tests et un débogage lors de l’exécution en dehors d’un service, ajoutez un code pour déterminer si l’application s’exécute comme un service ou comme une application console.</span><span class="sxs-lookup"><span data-stu-id="fe707-284">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="fe707-285">Vérifiez si le débogueur est attaché ou si un switch `--console` est présent.</span><span class="sxs-lookup"><span data-stu-id="fe707-285">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="fe707-286">Si l’une de ces deux conditions est remplie (l’application n’est pas exécutée en tant que service), appelez <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="fe707-286">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="fe707-287">Si les conditions ne sont pas remplies (l’application est exécutée en tant que service) :</span><span class="sxs-lookup"><span data-stu-id="fe707-287">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="fe707-288">Appelez <xref:System.IO.Directory.SetCurrentDirectory*> et utilisez un chemin vers l’emplacement publié de l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-288">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="fe707-289">N’appelez pas <xref:System.IO.Directory.GetCurrentDirectory*> pour obtenir le chemin d’accès car une application Windows Service retourne le dossier *C:\\WINDOWS\\system32* lorsque <xref:System.IO.Directory.GetCurrentDirectory*> est appelée.</span><span class="sxs-lookup"><span data-stu-id="fe707-289">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="fe707-290">Pour plus d’informations, consultez la section [Répertoire actif et racine du contenu](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="fe707-290">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="fe707-291">Cette étape est effectuée avant la configuration de l’application dans `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="fe707-291">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="fe707-292">Appelez <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pour exécuter l’application en tant que service.</span><span class="sxs-lookup"><span data-stu-id="fe707-292">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="fe707-293">Étant donné que le [fournisseur de configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider) nécessite des paires nom/valeur pour les arguments de ligne de commande, le switch `--console` est supprimé des arguments avant que <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ne les reçoive.</span><span class="sxs-lookup"><span data-stu-id="fe707-293">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="fe707-294">Pour consigner des informations dans le journal des événements Windows, ajoutez le fournisseur EventLog à <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="fe707-294">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="fe707-295">Définissez le niveau de journalisation à l’aide de clé `Logging:LogLevel:Default` dans le fichier *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="fe707-295">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="fe707-296">Dans l’exemple suivant, qui provient de l’exemple d’application, l’élément `RunAsCustomService` est appelé à la place de l’élément <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> afin de gérer les événements de durée de vie au sein de l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-296">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="fe707-297">Pour plus d’informations, consultez la section [Gérer les événements de démarrage et d’arrêt](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="fe707-297">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="fe707-298">Type de déploiement</span><span class="sxs-lookup"><span data-stu-id="fe707-298">Deployment type</span></span>

<span data-ttu-id="fe707-299">Pour des informations et des conseils sur les scénarios de déploiement, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="fe707-299">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="fe707-300">SDK</span><span class="sxs-lookup"><span data-stu-id="fe707-300">SDK</span></span>

<span data-ttu-id="fe707-301">Pour un service basé sur une application Web qui utilise les Razor pages ou les infrastructures MVC, spécifiez le kit de développement logiciel (SDK) Web dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="fe707-301">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="fe707-302">Si le service exécute uniquement des tâches en arrière-plan (par exemple, les [services hébergés](xref:fundamentals/host/hosted-services)), spécifiez le kit de développement logiciel (SDK) Worker dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="fe707-302">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="fe707-303">Déploiement dépendant du framework</span><span class="sxs-lookup"><span data-stu-id="fe707-303">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="fe707-304">Un déploiement dépendant du framework s’appuie sur la présence d’une version partagée à l’échelle du système de .NET Core sur le système cible.</span><span class="sxs-lookup"><span data-stu-id="fe707-304">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="fe707-305">Lorsque vous effectuez le scénario de déploiement dépendant du framework en suivant les conseils du présent article, le Kit de développement logiciel (SDK) produit un fichier exécutable (*.exe*), appelé *fichier exécutable dépendant du framework*.</span><span class="sxs-lookup"><span data-stu-id="fe707-305">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="fe707-306">L' [identificateur Windows Runtime (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) contient la version cible de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="fe707-306">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="fe707-307">Dans l’exemple suivant, le RID est défini sur `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="fe707-307">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="fe707-308">La propriété `<SelfContained>` est définie sur `false`.</span><span class="sxs-lookup"><span data-stu-id="fe707-308">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="fe707-309">Ces propriétés demandent au Kit de développement logiciel (SDK) de générer un fichier exécutable (*.exe*) pour Windows et une application qui dépend du framework .NET Core partagé.</span><span class="sxs-lookup"><span data-stu-id="fe707-309">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="fe707-310">Un fichier *web.config*, qui est normalement produit lors de la publication d’une application ASP.NET Core, n’est pas nécessaire pour une application de Windows Services.</span><span class="sxs-lookup"><span data-stu-id="fe707-310">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="fe707-311">Pour désactiver la création d’un fichier *web.config*, ajoutez la propriété `<IsTransformWebConfigDisabled>` définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="fe707-311">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="fe707-312">Déploiement autonome</span><span class="sxs-lookup"><span data-stu-id="fe707-312">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="fe707-313">Un déploiement autonome ne s’appuie sur la présence d’aucune infrastructure partagée sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="fe707-313">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="fe707-314">Le runtime et les dépendances de l’application sont déployés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-314">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="fe707-315">Un [identificateur de runtime (RID)](/dotnet/core/rid-catalog) Windows est inclus dans l’élément `<PropertyGroup>` qui contient la version cible de .NET Framework :</span><span class="sxs-lookup"><span data-stu-id="fe707-315">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="fe707-316">Pour publier pour plusieurs RID :</span><span class="sxs-lookup"><span data-stu-id="fe707-316">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="fe707-317">Fournissez les RID dans une liste séparée par des points-virgules.</span><span class="sxs-lookup"><span data-stu-id="fe707-317">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="fe707-318">Utilisez le nom de la propriété [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (pluriel).</span><span class="sxs-lookup"><span data-stu-id="fe707-318">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="fe707-319">Pour plus d’informations, consultez le [Catalogue RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="fe707-319">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="fe707-320">Une propriété `<SelfContained>` est définie sur `true` :</span><span class="sxs-lookup"><span data-stu-id="fe707-320">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="fe707-321">Compte d’utilisateur du service</span><span class="sxs-lookup"><span data-stu-id="fe707-321">Service user account</span></span>

<span data-ttu-id="fe707-322">Pour créer un compte d’utilisateur du service, utilisez la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) depuis un interpréteur de commandes d’administration PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="fe707-322">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="fe707-323">Dans la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) ou plus :</span><span class="sxs-lookup"><span data-stu-id="fe707-323">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="fe707-324">Dans un système d’exploitation Windows antérieur à la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) :</span><span class="sxs-lookup"><span data-stu-id="fe707-324">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="fe707-325">Fournissez un [mot de passe fort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) à l’invite.</span><span class="sxs-lookup"><span data-stu-id="fe707-325">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="fe707-326">Le compte n’expire pas, sauf si le paramètre `-AccountExpires` est fourni à la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) avec un <xref:System.DateTime> d’expiration.</span><span class="sxs-lookup"><span data-stu-id="fe707-326">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="fe707-327">Pour plus d’informations, voir [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) et [Comptes d’utilisateurs de service](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="fe707-327">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="fe707-328">Une approche alternative à la gestion des utilisateurs lors de l’utilisation d’Active Directory consiste à utiliser des Comptes de service administrés.</span><span class="sxs-lookup"><span data-stu-id="fe707-328">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="fe707-329">Pour plus d’informations, consultez [Vue d’ensemble des comptes de service administrés de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="fe707-329">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="fe707-330">Droits d’ouverture de session en tant que service</span><span class="sxs-lookup"><span data-stu-id="fe707-330">Log on as a service rights</span></span>

<span data-ttu-id="fe707-331">Pour établir des droits *d’ouverture de session en tant que service* pour un compte d’utilisateur de service, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="fe707-331">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="fe707-332">Ouvrez l’éditeur de stratégie de sécurité locale en exécutant le fichier *secpool.msc*.</span><span class="sxs-lookup"><span data-stu-id="fe707-332">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="fe707-333">Développez le nœud **Stratégies locales** et sélectionnez **Attribution des droits utilisateur**.</span><span class="sxs-lookup"><span data-stu-id="fe707-333">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="fe707-334">Ouvrez la stratégie **Ouvrir une session en tant que service**.</span><span class="sxs-lookup"><span data-stu-id="fe707-334">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="fe707-335">Sélectionnez **Ajouter un utilisateur ou un groupe**.</span><span class="sxs-lookup"><span data-stu-id="fe707-335">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="fe707-336">Fournissez le nom d’objet (compte d’utilisateur) avec l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="fe707-336">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="fe707-337">Tapez le compte d’utilisateur (`{DOMAIN OR COMPUTER NAME\USER}`) dans le champ du nom d’objet et sélectionnez **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="fe707-337">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="fe707-338">Sélectionnez **Avancé**.</span><span class="sxs-lookup"><span data-stu-id="fe707-338">Select **Advanced**.</span></span> <span data-ttu-id="fe707-339">Sélectionnez **Rechercher maintenant**.</span><span class="sxs-lookup"><span data-stu-id="fe707-339">Select **Find Now**.</span></span> <span data-ttu-id="fe707-340">Sélectionnez le compte d’utilisateur dans la liste.</span><span class="sxs-lookup"><span data-stu-id="fe707-340">Select the user account from the list.</span></span> <span data-ttu-id="fe707-341">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="fe707-341">Select **OK**.</span></span> <span data-ttu-id="fe707-342">Cliquez à nouveau sur **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="fe707-342">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="fe707-343">Sélectionnez **OK** ou **Appliquer** pour accepter les modifications.</span><span class="sxs-lookup"><span data-stu-id="fe707-343">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="fe707-344">Créer et gérer le service Windows</span><span class="sxs-lookup"><span data-stu-id="fe707-344">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="fe707-345">Créer un service</span><span class="sxs-lookup"><span data-stu-id="fe707-345">Create a service</span></span>

<span data-ttu-id="fe707-346">Utilisez les commandes PowerShell pour enregistrer un service.</span><span class="sxs-lookup"><span data-stu-id="fe707-346">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="fe707-347">À partir d’un interpréteur de commandes d’administration PowerShell 6, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="fe707-347">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="fe707-348">`{EXE PATH}`: Chemin d’accès au dossier de l’application sur l’hôte (par exemple, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-348">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="fe707-349">N’incluez pas le fichier exécutable de l’application dans le chemin.</span><span class="sxs-lookup"><span data-stu-id="fe707-349">Don't include the app's executable in the path.</span></span> <span data-ttu-id="fe707-350">Aucune barre oblique de fin n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="fe707-350">A trailing slash isn't required.</span></span>
* <span data-ttu-id="fe707-351">`{DOMAIN OR COMPUTER NAME\USER}`: Compte d’utilisateur du service (par exemple, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-351">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="fe707-352">`{SERVICE NAME}`: Nom du service (par exemple, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-352">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="fe707-353">`{EXE FILE PATH}`: Le chemin d’accès de l’exécutable de l’application (par exemple, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-353">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="fe707-354">Incluez le nom de fichier de l’exécutable avec l’extension.</span><span class="sxs-lookup"><span data-stu-id="fe707-354">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="fe707-355">`{DESCRIPTION}`: Description du service (par exemple, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-355">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="fe707-356">`{DISPLAY NAME}`: Nom d’affichage du service (par exemple, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-356">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="fe707-357">Démarrer un service</span><span class="sxs-lookup"><span data-stu-id="fe707-357">Start a service</span></span>

<span data-ttu-id="fe707-358">Démarrez un service avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="fe707-358">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="fe707-359">La commande prend quelques secondes pour démarrer le service.</span><span class="sxs-lookup"><span data-stu-id="fe707-359">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="fe707-360">Déterminer l’état d’un service</span><span class="sxs-lookup"><span data-stu-id="fe707-360">Determine a service's status</span></span>

<span data-ttu-id="fe707-361">Pour vérifier l’état d’un service, utilisez la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="fe707-361">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="fe707-362">L’état est signalé comme étant l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="fe707-362">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="fe707-363">Arrêter un service</span><span class="sxs-lookup"><span data-stu-id="fe707-363">Stop a service</span></span>

<span data-ttu-id="fe707-364">Arrêtez un service avec la commande Powershell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="fe707-364">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="fe707-365">Supprimer un service</span><span class="sxs-lookup"><span data-stu-id="fe707-365">Remove a service</span></span>

<span data-ttu-id="fe707-366">Après un court délai pour arrêter un service, supprimez-le avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="fe707-366">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="fe707-367">Gérer les événements de démarrage et d’arrêt</span><span class="sxs-lookup"><span data-stu-id="fe707-367">Handle starting and stopping events</span></span>

<span data-ttu-id="fe707-368">Pour gérer les événements <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> et <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="fe707-368">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="fe707-369">Créez une classe qui dérive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> à l’aide des méthodes `OnStarting`, `OnStarted` et `OnStopping` :</span><span class="sxs-lookup"><span data-stu-id="fe707-369">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="fe707-370">Créez une méthode d’extension pour <xref:Microsoft.AspNetCore.Hosting.IWebHost> qui transmet `CustomWebHostService` à <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="fe707-370">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="fe707-371">Dans `Program.Main`, appelez la méthode d’extension `RunAsCustomService` au lieu de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="fe707-371">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="fe707-372">Pour afficher l’emplacement de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> dans `Program.Main`, reportez-vous à l’exemple de code indiqué dans la section [Type de déploiement](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="fe707-372">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="fe707-373">Scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="fe707-373">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="fe707-374">Les services qui interagissent avec les requêtes provenant d’Internet ou d’un réseau d’entreprise et qui se trouvent derrière un proxy ou équilibreur de charge peuvent nécessiter une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="fe707-374">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="fe707-375">Pour plus d’informations, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="fe707-375">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="fe707-376">Configuration des points de terminaison</span><span class="sxs-lookup"><span data-stu-id="fe707-376">Configure endpoints</span></span>

<span data-ttu-id="fe707-377">Par défaut, ASP.NET Core est lié à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="fe707-377">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="fe707-378">Configurez l’URL et le port en définissant la `ASPNETCORE_URLS` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="fe707-378">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="fe707-379">Pour obtenir d’autres approches de configuration des ports et des URL, consultez l’article approprié sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="fe707-379">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="fe707-380">L’aide précédente couvre la prise en charge des points de terminaison HTTPs.</span><span class="sxs-lookup"><span data-stu-id="fe707-380">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="fe707-381">Par exemple, configurez l’application pour HTTPs lorsque l’authentification est utilisée avec un service Windows.</span><span class="sxs-lookup"><span data-stu-id="fe707-381">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="fe707-382">L’utilisation du certificat de développement HTTPS ASP.NET Core pour sécuriser un point de terminaison de service n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="fe707-382">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="fe707-383">Répertoire actif et racine du contenu</span><span class="sxs-lookup"><span data-stu-id="fe707-383">Current directory and content root</span></span>

<span data-ttu-id="fe707-384">Le répertoire de travail actuel retourné en appelant <xref:System.IO.Directory.GetCurrentDirectory*> pour un service Windows est le dossier *\\ \\ system32 de C : Windows* .</span><span class="sxs-lookup"><span data-stu-id="fe707-384">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="fe707-385">Le dossier *system32* n’est pas un emplacement approprié pour stocker les fichiers d’un service (tels que les fichiers de paramètres).</span><span class="sxs-lookup"><span data-stu-id="fe707-385">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="fe707-386">Utilisez une des approches suivantes pour gérer les ressources ainsi que les fichiers de paramètres d’un service, et y accéder.</span><span class="sxs-lookup"><span data-stu-id="fe707-386">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="fe707-387">Définir le dossier de l’application comme chemin d’accès racine du contenu</span><span class="sxs-lookup"><span data-stu-id="fe707-387">Set the content root path to the app's folder</span></span>

<span data-ttu-id="fe707-388">La chaîne <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> correspond au même chemin que celui fourni à l’argument `binPath` lorsqu’un service est créé.</span><span class="sxs-lookup"><span data-stu-id="fe707-388">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="fe707-389">Au lieu d’appeler `GetCurrentDirectory` pour créer des chemins d’accès aux fichiers de paramètres, appelez <xref:System.IO.Directory.SetCurrentDirectory*> avec le chemin d’accès à la [racine de contenu](xref:fundamentals/index#content-root)de l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-389">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="fe707-390">Dans `Program.Main`, définissez le chemin d’accès au dossier du fichier exécutable du service ainsi que le chemin d’accès pour établir la racine du contenu de l’application :</span><span class="sxs-lookup"><span data-stu-id="fe707-390">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="fe707-391">Stocker les fichiers d’un service dans un emplacement approprié sur le disque</span><span class="sxs-lookup"><span data-stu-id="fe707-391">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="fe707-392">Spécifiez un chemin absolu avec <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>, si vous utilisez <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, vers le dossier contenant les fichiers.</span><span class="sxs-lookup"><span data-stu-id="fe707-392">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="fe707-393">Dépanner</span><span class="sxs-lookup"><span data-stu-id="fe707-393">Troubleshoot</span></span>

<span data-ttu-id="fe707-394">Pour dépanner une application de service Windows, consultez <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="fe707-394">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="fe707-395">Erreurs courantes</span><span class="sxs-lookup"><span data-stu-id="fe707-395">Common errors</span></span>

* <span data-ttu-id="fe707-396">Une version ancienne ou préliminaire de PowerShell est en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="fe707-396">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="fe707-397">Le service inscrit n’utilise pas la sortie **publiée** de l’application à partir de la commande [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="fe707-397">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="fe707-398">La sortie de la commande [dotnet Build](/dotnet/core/tools/dotnet-build) n’est pas prise en charge pour le déploiement d’applications.</span><span class="sxs-lookup"><span data-stu-id="fe707-398">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="fe707-399">Les ressources publiées se trouvent dans l’un des dossiers suivants, selon le type de déploiement :</span><span class="sxs-lookup"><span data-stu-id="fe707-399">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="fe707-400">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="fe707-400">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="fe707-401">*bin/Release/{Target Framework}/{Runtime identificateur}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="fe707-401">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="fe707-402">Le service n’est pas à l’État en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="fe707-402">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="fe707-403">Les chemins d’accès aux ressources que l’application utilise (par exemple, les certificats) sont incorrects.</span><span class="sxs-lookup"><span data-stu-id="fe707-403">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="fe707-404">Le chemin d’accès de base d’un service Windows est *c : \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="fe707-404">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="fe707-405">L’utilisateur ne dispose pas de droits *d’ouverture de session en tant que service* .</span><span class="sxs-lookup"><span data-stu-id="fe707-405">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="fe707-406">Le mot de passe de l’utilisateur a expiré ou a été transmis de manière incorrecte lors de l’exécution de la `New-Service` commande PowerShell.</span><span class="sxs-lookup"><span data-stu-id="fe707-406">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="fe707-407">L’application requiert l’authentification ASP.NET Core, mais elle n’est pas configurée pour les connexions sécurisées (HTTPs).</span><span class="sxs-lookup"><span data-stu-id="fe707-407">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="fe707-408">Le port de l’URL de requête est incorrect ou n’est pas correctement configuré dans l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-408">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="fe707-409">Journaux des événements système et des applications</span><span class="sxs-lookup"><span data-stu-id="fe707-409">System and Application Event Logs</span></span>

<span data-ttu-id="fe707-410">Accédez aux journaux des événements système et des applications :</span><span class="sxs-lookup"><span data-stu-id="fe707-410">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="fe707-411">Ouvrez le menu Démarrer, recherchez *Observateur d’événements*, puis sélectionnez l’application **Observateur d’événements** .</span><span class="sxs-lookup"><span data-stu-id="fe707-411">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="fe707-412">Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.</span><span class="sxs-lookup"><span data-stu-id="fe707-412">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="fe707-413">Sélectionnez **système** pour ouvrir le journal des événements système.</span><span class="sxs-lookup"><span data-stu-id="fe707-413">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="fe707-414">Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-414">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="fe707-415">Recherchez les erreurs liées à l’application défectueuse.</span><span class="sxs-lookup"><span data-stu-id="fe707-415">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="fe707-416">Exécuter l’application depuis une invite de commandes</span><span class="sxs-lookup"><span data-stu-id="fe707-416">Run the app at a command prompt</span></span>

<span data-ttu-id="fe707-417">De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans les journaux des événements.</span><span class="sxs-lookup"><span data-stu-id="fe707-417">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="fe707-418">Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="fe707-418">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="fe707-419">Pour consigner des détails supplémentaires à partir de l’application, diminuez le [niveau de journalisation](xref:fundamentals/logging/index#log-level) ou exécutez l’application dans l' [environnement de développement](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="fe707-419">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="fe707-420">Effacer les caches de package</span><span class="sxs-lookup"><span data-stu-id="fe707-420">Clear package caches</span></span>

<span data-ttu-id="fe707-421">Une application fonctionnelle peut échouer immédiatement après la mise à niveau de la kit SDK .NET Core sur l’ordinateur de développement ou la modification des versions de package dans l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-421">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="fe707-422">Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures.</span><span class="sxs-lookup"><span data-stu-id="fe707-422">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="fe707-423">Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :</span><span class="sxs-lookup"><span data-stu-id="fe707-423">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="fe707-424">Supprimez les dossiers *bin* et *obj*.</span><span class="sxs-lookup"><span data-stu-id="fe707-424">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="fe707-425">Effacez les caches de package en exécutant [dotnet NuGet LOCALS tout--Clear](/dotnet/core/tools/dotnet-nuget-locals) dans une interface de commande.</span><span class="sxs-lookup"><span data-stu-id="fe707-425">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="fe707-426">L’effacement des caches de package peut également être effectué à l’aide de l’outil [nuget.exe](https://www.nuget.org/downloads) et en exécutant la commande `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="fe707-426">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="fe707-427">*NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="fe707-427">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="fe707-428">Restaurez et regénérez le projet.</span><span class="sxs-lookup"><span data-stu-id="fe707-428">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="fe707-429">Supprimez tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-429">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="fe707-430">Application lente ou bloquée</span><span class="sxs-lookup"><span data-stu-id="fe707-430">Slow or hanging app</span></span>

<span data-ttu-id="fe707-431">Un *vidage sur incident* est un instantané de la mémoire du système et peut aider à déterminer la cause d’un incident d’application, d’un échec de démarrage ou d’une application lente.</span><span class="sxs-lookup"><span data-stu-id="fe707-431">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="fe707-432">L’application cesse de fonctionner ou rencontre une exception</span><span class="sxs-lookup"><span data-stu-id="fe707-432">App crashes or encounters an exception</span></span>

<span data-ttu-id="fe707-433">Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :</span><span class="sxs-lookup"><span data-stu-id="fe707-433">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="fe707-434">Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="fe707-434">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="fe707-435">Exécutez le [script PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) avec le nom de l’exécutable de l’application :</span><span class="sxs-lookup"><span data-stu-id="fe707-435">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="fe707-436">Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.</span><span class="sxs-lookup"><span data-stu-id="fe707-436">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="fe707-437">Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) :</span><span class="sxs-lookup"><span data-stu-id="fe707-437">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="fe707-438">Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement.</span><span class="sxs-lookup"><span data-stu-id="fe707-438">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="fe707-439">Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.</span><span class="sxs-lookup"><span data-stu-id="fe707-439">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="fe707-440">Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).</span><span class="sxs-lookup"><span data-stu-id="fe707-440">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="fe707-441">L’application se bloque, ne démarre pas ou s’exécute normalement</span><span class="sxs-lookup"><span data-stu-id="fe707-441">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="fe707-442">Quand une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage ou s’exécute normalement, consultez [fichiers de vidage en mode utilisateur : choix de l’outil le mieux](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) adapté pour sélectionner un outil approprié pour produire le vidage.</span><span class="sxs-lookup"><span data-stu-id="fe707-442">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="fe707-443">Analyser le fichier dump</span><span class="sxs-lookup"><span data-stu-id="fe707-443">Analyze the dump</span></span>

<span data-ttu-id="fe707-444">Un fichier dump peut être analysé à l’aide de plusieurs approches.</span><span class="sxs-lookup"><span data-stu-id="fe707-444">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="fe707-445">Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).</span><span class="sxs-lookup"><span data-stu-id="fe707-445">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe707-446">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fe707-446">Additional resources</span></span>

* <span data-ttu-id="fe707-447">[Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclut la configuration de HTTPS et la prise en charge de SNI)</span><span class="sxs-lookup"><span data-stu-id="fe707-447">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="fe707-448">Une application ASP.NET Core peut être hébergée sur Windows en tant que [service Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sans utiliser IIS.</span><span class="sxs-lookup"><span data-stu-id="fe707-448">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="fe707-449">Lorsqu’elle est hébergée en tant que service Windows, l’application se lance automatiquement après le redémarrage du serveur.</span><span class="sxs-lookup"><span data-stu-id="fe707-449">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="fe707-450">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fe707-450">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fe707-451">Prérequis</span><span class="sxs-lookup"><span data-stu-id="fe707-451">Prerequisites</span></span>

* [<span data-ttu-id="fe707-452">Kit de développement logiciel (SDK) ASP.NET Core 2.1 ou plus</span><span class="sxs-lookup"><span data-stu-id="fe707-452">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="fe707-453">PowerShell 6,2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="fe707-453">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="fe707-454">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="fe707-454">App configuration</span></span>

<span data-ttu-id="fe707-455">L’application nécessite des références de package pour [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) et [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="fe707-455">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="fe707-456">Pour effectuer des tests et un débogage lors de l’exécution en dehors d’un service, ajoutez un code pour déterminer si l’application s’exécute comme un service ou comme une application console.</span><span class="sxs-lookup"><span data-stu-id="fe707-456">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="fe707-457">Vérifiez si le débogueur est attaché ou si un switch `--console` est présent.</span><span class="sxs-lookup"><span data-stu-id="fe707-457">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="fe707-458">Si l’une de ces deux conditions est remplie (l’application n’est pas exécutée en tant que service), appelez <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="fe707-458">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="fe707-459">Si les conditions ne sont pas remplies (l’application est exécutée en tant que service) :</span><span class="sxs-lookup"><span data-stu-id="fe707-459">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="fe707-460">Appelez <xref:System.IO.Directory.SetCurrentDirectory*> et utilisez un chemin vers l’emplacement publié de l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-460">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="fe707-461">N’appelez pas <xref:System.IO.Directory.GetCurrentDirectory*> pour obtenir le chemin d’accès car une application Windows Service retourne le dossier *C:\\WINDOWS\\system32* lorsque <xref:System.IO.Directory.GetCurrentDirectory*> est appelée.</span><span class="sxs-lookup"><span data-stu-id="fe707-461">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="fe707-462">Pour plus d’informations, consultez la section [Répertoire actif et racine du contenu](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="fe707-462">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="fe707-463">Cette étape est effectuée avant la configuration de l’application dans `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="fe707-463">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="fe707-464">Appelez <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pour exécuter l’application en tant que service.</span><span class="sxs-lookup"><span data-stu-id="fe707-464">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="fe707-465">Étant donné que le [fournisseur de configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider) nécessite des paires nom/valeur pour les arguments de ligne de commande, le switch `--console` est supprimé des arguments avant que <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ne les reçoive.</span><span class="sxs-lookup"><span data-stu-id="fe707-465">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="fe707-466">Pour consigner des informations dans le journal des événements Windows, ajoutez le fournisseur EventLog à <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="fe707-466">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="fe707-467">Définissez le niveau de journalisation à l’aide de clé `Logging:LogLevel:Default` dans le fichier *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="fe707-467">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="fe707-468">Dans l’exemple suivant, qui provient de l’exemple d’application, l’élément `RunAsCustomService` est appelé à la place de l’élément <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> afin de gérer les événements de durée de vie au sein de l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-468">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="fe707-469">Pour plus d’informations, consultez la section [Gérer les événements de démarrage et d’arrêt](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="fe707-469">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="fe707-470">Type de déploiement</span><span class="sxs-lookup"><span data-stu-id="fe707-470">Deployment type</span></span>

<span data-ttu-id="fe707-471">Pour des informations et des conseils sur les scénarios de déploiement, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="fe707-471">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="fe707-472">SDK</span><span class="sxs-lookup"><span data-stu-id="fe707-472">SDK</span></span>

<span data-ttu-id="fe707-473">Pour un service basé sur une application Web qui utilise les Razor pages ou les infrastructures MVC, spécifiez le kit de développement logiciel (SDK) Web dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="fe707-473">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="fe707-474">Si le service exécute uniquement des tâches en arrière-plan (par exemple, les [services hébergés](xref:fundamentals/host/hosted-services)), spécifiez le kit de développement logiciel (SDK) Worker dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="fe707-474">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="fe707-475">Déploiement dépendant du framework</span><span class="sxs-lookup"><span data-stu-id="fe707-475">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="fe707-476">Un déploiement dépendant du framework s’appuie sur la présence d’une version partagée à l’échelle du système de .NET Core sur le système cible.</span><span class="sxs-lookup"><span data-stu-id="fe707-476">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="fe707-477">Lorsque vous effectuez le scénario de déploiement dépendant du framework en suivant les conseils du présent article, le Kit de développement logiciel (SDK) produit un fichier exécutable (*.exe*), appelé *fichier exécutable dépendant du framework*.</span><span class="sxs-lookup"><span data-stu-id="fe707-477">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="fe707-478">L' [identificateur Windows Runtime (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) contient la version cible de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="fe707-478">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="fe707-479">Dans l’exemple suivant, le RID est défini sur `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="fe707-479">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="fe707-480">La propriété `<SelfContained>` est définie sur `false`.</span><span class="sxs-lookup"><span data-stu-id="fe707-480">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="fe707-481">Ces propriétés demandent au Kit de développement logiciel (SDK) de générer un fichier exécutable (*.exe*) pour Windows et une application qui dépend du framework .NET Core partagé.</span><span class="sxs-lookup"><span data-stu-id="fe707-481">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="fe707-482">La propriété `<UseAppHost>` est définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="fe707-482">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="fe707-483">Cette propriété fournit au service un chemin d’activation (un fichier exécutable *.exe*) pour un déploiement dépendant du framework (FDD).</span><span class="sxs-lookup"><span data-stu-id="fe707-483">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="fe707-484">Un fichier *web.config*, qui est normalement produit lors de la publication d’une application ASP.NET Core, n’est pas nécessaire pour une application de Windows Services.</span><span class="sxs-lookup"><span data-stu-id="fe707-484">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="fe707-485">Pour désactiver la création d’un fichier *web.config*, ajoutez la propriété `<IsTransformWebConfigDisabled>` définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="fe707-485">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="fe707-486">Déploiement autonome</span><span class="sxs-lookup"><span data-stu-id="fe707-486">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="fe707-487">Un déploiement autonome ne s’appuie sur la présence d’aucune infrastructure partagée sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="fe707-487">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="fe707-488">Le runtime et les dépendances de l’application sont déployés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-488">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="fe707-489">Un [identificateur de runtime (RID)](/dotnet/core/rid-catalog) Windows est inclus dans l’élément `<PropertyGroup>` qui contient la version cible de .NET Framework :</span><span class="sxs-lookup"><span data-stu-id="fe707-489">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="fe707-490">Pour publier pour plusieurs RID :</span><span class="sxs-lookup"><span data-stu-id="fe707-490">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="fe707-491">Fournissez les RID dans une liste séparée par des points-virgules.</span><span class="sxs-lookup"><span data-stu-id="fe707-491">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="fe707-492">Utilisez le nom de la propriété [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (pluriel).</span><span class="sxs-lookup"><span data-stu-id="fe707-492">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="fe707-493">Pour plus d’informations, consultez le [Catalogue RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="fe707-493">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="fe707-494">Une propriété `<SelfContained>` est définie sur `true` :</span><span class="sxs-lookup"><span data-stu-id="fe707-494">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="fe707-495">Compte d’utilisateur du service</span><span class="sxs-lookup"><span data-stu-id="fe707-495">Service user account</span></span>

<span data-ttu-id="fe707-496">Pour créer un compte d’utilisateur du service, utilisez la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) depuis un interpréteur de commandes d’administration PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="fe707-496">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="fe707-497">Dans la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) ou plus :</span><span class="sxs-lookup"><span data-stu-id="fe707-497">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="fe707-498">Dans un système d’exploitation Windows antérieur à la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) :</span><span class="sxs-lookup"><span data-stu-id="fe707-498">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="fe707-499">Fournissez un [mot de passe fort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) à l’invite.</span><span class="sxs-lookup"><span data-stu-id="fe707-499">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="fe707-500">Le compte n’expire pas, sauf si le paramètre `-AccountExpires` est fourni à la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) avec un <xref:System.DateTime> d’expiration.</span><span class="sxs-lookup"><span data-stu-id="fe707-500">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="fe707-501">Pour plus d’informations, voir [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) et [Comptes d’utilisateurs de service](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="fe707-501">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="fe707-502">Une approche alternative à la gestion des utilisateurs lors de l’utilisation d’Active Directory consiste à utiliser des Comptes de service administrés.</span><span class="sxs-lookup"><span data-stu-id="fe707-502">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="fe707-503">Pour plus d’informations, consultez [Vue d’ensemble des comptes de service administrés de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="fe707-503">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="fe707-504">Droits d’ouverture de session en tant que service</span><span class="sxs-lookup"><span data-stu-id="fe707-504">Log on as a service rights</span></span>

<span data-ttu-id="fe707-505">Pour établir des droits *d’ouverture de session en tant que service* pour un compte d’utilisateur de service, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="fe707-505">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="fe707-506">Ouvrez l’éditeur de stratégie de sécurité locale en exécutant le fichier *secpool.msc*.</span><span class="sxs-lookup"><span data-stu-id="fe707-506">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="fe707-507">Développez le nœud **Stratégies locales** et sélectionnez **Attribution des droits utilisateur**.</span><span class="sxs-lookup"><span data-stu-id="fe707-507">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="fe707-508">Ouvrez la stratégie **Ouvrir une session en tant que service**.</span><span class="sxs-lookup"><span data-stu-id="fe707-508">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="fe707-509">Sélectionnez **Ajouter un utilisateur ou un groupe**.</span><span class="sxs-lookup"><span data-stu-id="fe707-509">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="fe707-510">Fournissez le nom d’objet (compte d’utilisateur) avec l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="fe707-510">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="fe707-511">Tapez le compte d’utilisateur (`{DOMAIN OR COMPUTER NAME\USER}`) dans le champ du nom d’objet et sélectionnez **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="fe707-511">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="fe707-512">Sélectionnez **Avancé**.</span><span class="sxs-lookup"><span data-stu-id="fe707-512">Select **Advanced**.</span></span> <span data-ttu-id="fe707-513">Sélectionnez **Rechercher maintenant**.</span><span class="sxs-lookup"><span data-stu-id="fe707-513">Select **Find Now**.</span></span> <span data-ttu-id="fe707-514">Sélectionnez le compte d’utilisateur dans la liste.</span><span class="sxs-lookup"><span data-stu-id="fe707-514">Select the user account from the list.</span></span> <span data-ttu-id="fe707-515">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="fe707-515">Select **OK**.</span></span> <span data-ttu-id="fe707-516">Cliquez à nouveau sur **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="fe707-516">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="fe707-517">Sélectionnez **OK** ou **Appliquer** pour accepter les modifications.</span><span class="sxs-lookup"><span data-stu-id="fe707-517">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="fe707-518">Créer et gérer le service Windows</span><span class="sxs-lookup"><span data-stu-id="fe707-518">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="fe707-519">Créer un service</span><span class="sxs-lookup"><span data-stu-id="fe707-519">Create a service</span></span>

<span data-ttu-id="fe707-520">Utilisez les commandes PowerShell pour enregistrer un service.</span><span class="sxs-lookup"><span data-stu-id="fe707-520">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="fe707-521">À partir d’un interpréteur de commandes d’administration PowerShell 6, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="fe707-521">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="fe707-522">`{EXE PATH}`: Chemin d’accès au dossier de l’application sur l’hôte (par exemple, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-522">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="fe707-523">N’incluez pas le fichier exécutable de l’application dans le chemin.</span><span class="sxs-lookup"><span data-stu-id="fe707-523">Don't include the app's executable in the path.</span></span> <span data-ttu-id="fe707-524">Aucune barre oblique de fin n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="fe707-524">A trailing slash isn't required.</span></span>
* <span data-ttu-id="fe707-525">`{DOMAIN OR COMPUTER NAME\USER}`: Compte d’utilisateur du service (par exemple, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-525">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="fe707-526">`{SERVICE NAME}`: Nom du service (par exemple, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-526">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="fe707-527">`{EXE FILE PATH}`: Le chemin d’accès de l’exécutable de l’application (par exemple, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-527">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="fe707-528">Incluez le nom de fichier de l’exécutable avec l’extension.</span><span class="sxs-lookup"><span data-stu-id="fe707-528">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="fe707-529">`{DESCRIPTION}`: Description du service (par exemple, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-529">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="fe707-530">`{DISPLAY NAME}`: Nom d’affichage du service (par exemple, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="fe707-530">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="fe707-531">Démarrer un service</span><span class="sxs-lookup"><span data-stu-id="fe707-531">Start a service</span></span>

<span data-ttu-id="fe707-532">Démarrez un service avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="fe707-532">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="fe707-533">La commande prend quelques secondes pour démarrer le service.</span><span class="sxs-lookup"><span data-stu-id="fe707-533">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="fe707-534">Déterminer l’état d’un service</span><span class="sxs-lookup"><span data-stu-id="fe707-534">Determine a service's status</span></span>

<span data-ttu-id="fe707-535">Pour vérifier l’état d’un service, utilisez la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="fe707-535">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="fe707-536">L’état est signalé comme étant l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="fe707-536">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="fe707-537">Arrêter un service</span><span class="sxs-lookup"><span data-stu-id="fe707-537">Stop a service</span></span>

<span data-ttu-id="fe707-538">Arrêtez un service avec la commande Powershell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="fe707-538">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="fe707-539">Supprimer un service</span><span class="sxs-lookup"><span data-stu-id="fe707-539">Remove a service</span></span>

<span data-ttu-id="fe707-540">Après un court délai pour arrêter un service, supprimez-le avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="fe707-540">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="fe707-541">Gérer les événements de démarrage et d’arrêt</span><span class="sxs-lookup"><span data-stu-id="fe707-541">Handle starting and stopping events</span></span>

<span data-ttu-id="fe707-542">Pour gérer les événements <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> et <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="fe707-542">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="fe707-543">Créez une classe qui dérive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> à l’aide des méthodes `OnStarting`, `OnStarted` et `OnStopping` :</span><span class="sxs-lookup"><span data-stu-id="fe707-543">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="fe707-544">Créez une méthode d’extension pour <xref:Microsoft.AspNetCore.Hosting.IWebHost> qui transmet `CustomWebHostService` à <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="fe707-544">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="fe707-545">Dans `Program.Main`, appelez la méthode d’extension `RunAsCustomService` au lieu de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="fe707-545">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="fe707-546">Pour afficher l’emplacement de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> dans `Program.Main`, reportez-vous à l’exemple de code indiqué dans la section [Type de déploiement](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="fe707-546">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="fe707-547">Scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="fe707-547">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="fe707-548">Les services qui interagissent avec les requêtes provenant d’Internet ou d’un réseau d’entreprise et qui se trouvent derrière un proxy ou équilibreur de charge peuvent nécessiter une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="fe707-548">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="fe707-549">Pour plus d’informations, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="fe707-549">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="fe707-550">Configuration des points de terminaison</span><span class="sxs-lookup"><span data-stu-id="fe707-550">Configure endpoints</span></span>

<span data-ttu-id="fe707-551">Par défaut, ASP.NET Core est lié à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="fe707-551">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="fe707-552">Configurez l’URL et le port en définissant la `ASPNETCORE_URLS` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="fe707-552">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="fe707-553">Pour obtenir d’autres approches de configuration des ports et des URL, consultez l’article approprié sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="fe707-553">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="fe707-554">L’aide précédente couvre la prise en charge des points de terminaison HTTPs.</span><span class="sxs-lookup"><span data-stu-id="fe707-554">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="fe707-555">Par exemple, configurez l’application pour HTTPs lorsque l’authentification est utilisée avec un service Windows.</span><span class="sxs-lookup"><span data-stu-id="fe707-555">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="fe707-556">L’utilisation du certificat de développement HTTPS ASP.NET Core pour sécuriser un point de terminaison de service n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="fe707-556">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="fe707-557">Répertoire actif et racine du contenu</span><span class="sxs-lookup"><span data-stu-id="fe707-557">Current directory and content root</span></span>

<span data-ttu-id="fe707-558">Le répertoire de travail actuel retourné en appelant <xref:System.IO.Directory.GetCurrentDirectory*> pour un service Windows est le dossier *\\ \\ system32 de C : Windows* .</span><span class="sxs-lookup"><span data-stu-id="fe707-558">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="fe707-559">Le dossier *system32* n’est pas un emplacement approprié pour stocker les fichiers d’un service (tels que les fichiers de paramètres).</span><span class="sxs-lookup"><span data-stu-id="fe707-559">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="fe707-560">Utilisez une des approches suivantes pour gérer les ressources ainsi que les fichiers de paramètres d’un service, et y accéder.</span><span class="sxs-lookup"><span data-stu-id="fe707-560">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="fe707-561">Définir le dossier de l’application comme chemin d’accès racine du contenu</span><span class="sxs-lookup"><span data-stu-id="fe707-561">Set the content root path to the app's folder</span></span>

<span data-ttu-id="fe707-562">La chaîne <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> correspond au même chemin que celui fourni à l’argument `binPath` lorsqu’un service est créé.</span><span class="sxs-lookup"><span data-stu-id="fe707-562">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="fe707-563">Au lieu d’appeler `GetCurrentDirectory` pour créer des chemins d’accès aux fichiers de paramètres, appelez <xref:System.IO.Directory.SetCurrentDirectory*> avec le chemin d’accès à la [racine de contenu](xref:fundamentals/index#content-root)de l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-563">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="fe707-564">Dans `Program.Main`, définissez le chemin d’accès au dossier du fichier exécutable du service ainsi que le chemin d’accès pour établir la racine du contenu de l’application :</span><span class="sxs-lookup"><span data-stu-id="fe707-564">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="fe707-565">Stocker les fichiers d’un service dans un emplacement approprié sur le disque</span><span class="sxs-lookup"><span data-stu-id="fe707-565">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="fe707-566">Spécifiez un chemin absolu avec <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>, si vous utilisez <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, vers le dossier contenant les fichiers.</span><span class="sxs-lookup"><span data-stu-id="fe707-566">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="fe707-567">Dépanner</span><span class="sxs-lookup"><span data-stu-id="fe707-567">Troubleshoot</span></span>

<span data-ttu-id="fe707-568">Pour dépanner une application de service Windows, consultez <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="fe707-568">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="fe707-569">Erreurs courantes</span><span class="sxs-lookup"><span data-stu-id="fe707-569">Common errors</span></span>

* <span data-ttu-id="fe707-570">Une version ancienne ou préliminaire de PowerShell est en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="fe707-570">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="fe707-571">Le service inscrit n’utilise pas la sortie **publiée** de l’application à partir de la commande [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="fe707-571">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="fe707-572">La sortie de la commande [dotnet Build](/dotnet/core/tools/dotnet-build) n’est pas prise en charge pour le déploiement d’applications.</span><span class="sxs-lookup"><span data-stu-id="fe707-572">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="fe707-573">Les ressources publiées se trouvent dans l’un des dossiers suivants, selon le type de déploiement :</span><span class="sxs-lookup"><span data-stu-id="fe707-573">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="fe707-574">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="fe707-574">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="fe707-575">*bin/Release/{Target Framework}/{Runtime identificateur}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="fe707-575">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="fe707-576">Le service n’est pas à l’État en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="fe707-576">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="fe707-577">Les chemins d’accès aux ressources que l’application utilise (par exemple, les certificats) sont incorrects.</span><span class="sxs-lookup"><span data-stu-id="fe707-577">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="fe707-578">Le chemin d’accès de base d’un service Windows est *c : \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="fe707-578">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="fe707-579">L’utilisateur ne dispose pas de droits *d’ouverture de session en tant que service* .</span><span class="sxs-lookup"><span data-stu-id="fe707-579">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="fe707-580">Le mot de passe de l’utilisateur a expiré ou a été transmis de manière incorrecte lors de l’exécution de la `New-Service` commande PowerShell.</span><span class="sxs-lookup"><span data-stu-id="fe707-580">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="fe707-581">L’application requiert l’authentification ASP.NET Core, mais elle n’est pas configurée pour les connexions sécurisées (HTTPs).</span><span class="sxs-lookup"><span data-stu-id="fe707-581">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="fe707-582">Le port de l’URL de requête est incorrect ou n’est pas correctement configuré dans l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-582">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="fe707-583">Journaux des événements système et des applications</span><span class="sxs-lookup"><span data-stu-id="fe707-583">System and Application Event Logs</span></span>

<span data-ttu-id="fe707-584">Accédez aux journaux des événements système et des applications :</span><span class="sxs-lookup"><span data-stu-id="fe707-584">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="fe707-585">Ouvrez le menu Démarrer, recherchez *Observateur d’événements*, puis sélectionnez l’application **Observateur d’événements** .</span><span class="sxs-lookup"><span data-stu-id="fe707-585">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="fe707-586">Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.</span><span class="sxs-lookup"><span data-stu-id="fe707-586">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="fe707-587">Sélectionnez **système** pour ouvrir le journal des événements système.</span><span class="sxs-lookup"><span data-stu-id="fe707-587">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="fe707-588">Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-588">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="fe707-589">Recherchez les erreurs liées à l’application défectueuse.</span><span class="sxs-lookup"><span data-stu-id="fe707-589">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="fe707-590">Exécuter l’application depuis une invite de commandes</span><span class="sxs-lookup"><span data-stu-id="fe707-590">Run the app at a command prompt</span></span>

<span data-ttu-id="fe707-591">De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans les journaux des événements.</span><span class="sxs-lookup"><span data-stu-id="fe707-591">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="fe707-592">Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="fe707-592">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="fe707-593">Pour consigner des détails supplémentaires à partir de l’application, diminuez le [niveau de journalisation](xref:fundamentals/logging/index#log-level) ou exécutez l’application dans l' [environnement de développement](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="fe707-593">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="fe707-594">Effacer les caches de package</span><span class="sxs-lookup"><span data-stu-id="fe707-594">Clear package caches</span></span>

<span data-ttu-id="fe707-595">Une application fonctionnelle peut échouer immédiatement après la mise à niveau de la kit SDK .NET Core sur l’ordinateur de développement ou la modification des versions de package dans l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-595">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="fe707-596">Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures.</span><span class="sxs-lookup"><span data-stu-id="fe707-596">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="fe707-597">Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :</span><span class="sxs-lookup"><span data-stu-id="fe707-597">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="fe707-598">Supprimez les dossiers *bin* et *obj*.</span><span class="sxs-lookup"><span data-stu-id="fe707-598">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="fe707-599">Effacez les caches de package en exécutant [dotnet NuGet LOCALS tout--Clear](/dotnet/core/tools/dotnet-nuget-locals) dans une interface de commande.</span><span class="sxs-lookup"><span data-stu-id="fe707-599">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="fe707-600">L’effacement des caches de package peut également être effectué à l’aide de l’outil [nuget.exe](https://www.nuget.org/downloads) et en exécutant la commande `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="fe707-600">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="fe707-601">*NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="fe707-601">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="fe707-602">Restaurez et regénérez le projet.</span><span class="sxs-lookup"><span data-stu-id="fe707-602">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="fe707-603">Supprimez tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.</span><span class="sxs-lookup"><span data-stu-id="fe707-603">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="fe707-604">Application lente ou bloquée</span><span class="sxs-lookup"><span data-stu-id="fe707-604">Slow or hanging app</span></span>

<span data-ttu-id="fe707-605">Un *vidage sur incident* est un instantané de la mémoire du système et peut aider à déterminer la cause d’un incident d’application, d’un échec de démarrage ou d’une application lente.</span><span class="sxs-lookup"><span data-stu-id="fe707-605">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="fe707-606">L’application cesse de fonctionner ou rencontre une exception</span><span class="sxs-lookup"><span data-stu-id="fe707-606">App crashes or encounters an exception</span></span>

<span data-ttu-id="fe707-607">Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :</span><span class="sxs-lookup"><span data-stu-id="fe707-607">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="fe707-608">Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="fe707-608">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="fe707-609">Exécutez le [script PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) avec le nom de l’exécutable de l’application :</span><span class="sxs-lookup"><span data-stu-id="fe707-609">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="fe707-610">Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.</span><span class="sxs-lookup"><span data-stu-id="fe707-610">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="fe707-611">Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) :</span><span class="sxs-lookup"><span data-stu-id="fe707-611">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="fe707-612">Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement.</span><span class="sxs-lookup"><span data-stu-id="fe707-612">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="fe707-613">Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.</span><span class="sxs-lookup"><span data-stu-id="fe707-613">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="fe707-614">Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).</span><span class="sxs-lookup"><span data-stu-id="fe707-614">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="fe707-615">L’application se bloque, ne démarre pas ou s’exécute normalement</span><span class="sxs-lookup"><span data-stu-id="fe707-615">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="fe707-616">Quand une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage ou s’exécute normalement, consultez [fichiers de vidage en mode utilisateur : choix de l’outil le mieux](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) adapté pour sélectionner un outil approprié pour produire le vidage.</span><span class="sxs-lookup"><span data-stu-id="fe707-616">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="fe707-617">Analyser le fichier dump</span><span class="sxs-lookup"><span data-stu-id="fe707-617">Analyze the dump</span></span>

<span data-ttu-id="fe707-618">Un fichier dump peut être analysé à l’aide de plusieurs approches.</span><span class="sxs-lookup"><span data-stu-id="fe707-618">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="fe707-619">Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).</span><span class="sxs-lookup"><span data-stu-id="fe707-619">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe707-620">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fe707-620">Additional resources</span></span>

* <span data-ttu-id="fe707-621">[Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclut la configuration de HTTPS et la prise en charge de SNI)</span><span class="sxs-lookup"><span data-stu-id="fe707-621">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

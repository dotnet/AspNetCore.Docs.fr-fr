---
title: Héberger ASP.NET Core dans un service Windows
author: rick-anderson
description: Découvrez comment héberger une application ASP.NET Core dans un service Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
ms.openlocfilehash: d4df10f9450ca956d7b1a4297caa63cdd0caf23e
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113749"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="56c73-103">Héberger ASP.NET Core dans un service Windows</span><span class="sxs-lookup"><span data-stu-id="56c73-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="56c73-104">Une application ASP.NET Core peut être hébergée sur Windows en tant que [service Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sans utiliser IIS.</span><span class="sxs-lookup"><span data-stu-id="56c73-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="56c73-105">Lorsqu’elle est hébergée en tant que service Windows, l’application se lance automatiquement après le redémarrage du serveur.</span><span class="sxs-lookup"><span data-stu-id="56c73-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="56c73-106">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="56c73-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="56c73-107">Prérequis</span><span class="sxs-lookup"><span data-stu-id="56c73-107">Prerequisites</span></span>

* [<span data-ttu-id="56c73-108">Kit de développement logiciel (SDK) ASP.NET Core 2.1 ou plus</span><span class="sxs-lookup"><span data-stu-id="56c73-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="56c73-109">PowerShell 6,2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="56c73-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="56c73-110">Modèle Service Worker</span><span class="sxs-lookup"><span data-stu-id="56c73-110">Worker Service template</span></span>

<span data-ttu-id="56c73-111">Le modèle Service Worker ASP.NET Core fournit un point de départ pour l’écriture d’applications de service durables.</span><span class="sxs-lookup"><span data-stu-id="56c73-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="56c73-112">Pour utiliser le modèle en tant que base d’une application de service Windows :</span><span class="sxs-lookup"><span data-stu-id="56c73-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="56c73-113">Créez une application Service Worker à partir du modèle .NET Core.</span><span class="sxs-lookup"><span data-stu-id="56c73-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="56c73-114">Suivez l’aide fournie dans la section [Configuration d’application](#app-configuration) pour mettre à jour l’application Service Worker afin qu’elle puisse s’exécuter en tant que service Windows.</span><span class="sxs-lookup"><span data-stu-id="56c73-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="56c73-115">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="56c73-115">App configuration</span></span>

<span data-ttu-id="56c73-116">L’application requiert une référence de package pour [Microsoft. extensions. Hosting. services Windows,](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="56c73-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="56c73-117">`IHostBuilder.UseWindowsService` est appelé lors de la génération de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="56c73-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="56c73-118">Si l’application s’exécute comme un service Windows, la méthode :</span><span class="sxs-lookup"><span data-stu-id="56c73-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="56c73-119">définit la durée de vie de l’hôte sur `WindowsServiceLifetime` ;</span><span class="sxs-lookup"><span data-stu-id="56c73-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="56c73-120">Affecte à la [racine du contenu](xref:fundamentals/index#content-root) la valeur [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="56c73-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="56c73-121">Pour plus d’informations, consultez la section [Répertoire actif et racine du contenu](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="56c73-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="56c73-122">Active la journalisation dans le journal des événements :</span><span class="sxs-lookup"><span data-stu-id="56c73-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="56c73-123">Le nom de l’application est utilisé comme nom de source par défaut.</span><span class="sxs-lookup"><span data-stu-id="56c73-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="56c73-124">Le niveau de journalisation par défaut est *Avertissement* ou supérieur pour une application basée sur un modèle de ASP.net core qui appelle `CreateDefaultBuilder` pour créer l’hôte.</span><span class="sxs-lookup"><span data-stu-id="56c73-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="56c73-125">Remplacez le niveau de journalisation par défaut par la `Logging:EventLog:LogLevel:Default` clé dans *appsettings.jssur* / *appSettings. { Environment}. JSON* ou un autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="56c73-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="56c73-126">Seuls les administrateurs peuvent créer des sources d’événement.</span><span class="sxs-lookup"><span data-stu-id="56c73-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="56c73-127">Si une source d’événement ne peut pas être créée en utilisant le nom de l’application, un avertissement est consigné dans la source *Application* source et les journaux d’événements sont désactivés.</span><span class="sxs-lookup"><span data-stu-id="56c73-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="56c73-128">Dans `CreateHostBuilder` de *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="56c73-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="56c73-129">Les exemples d’applications suivants accompagnent cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="56c73-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="56c73-130">Exemple de service de travail en arrière-plan : exemple d’application non Web basé sur le [modèle de service Worker](#worker-service-template) qui utilise des [services hébergés](xref:fundamentals/host/hosted-services) pour les tâches en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="56c73-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="56c73-131">Exemple de App Service Web : Razor exemple d’application Web pages qui s’exécute en tant que service Windows avec les [services hébergés](xref:fundamentals/host/hosted-services) pour les tâches en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="56c73-131">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="56c73-132">Pour obtenir des conseils sur MVC, consultez les articles sous <xref:mvc/overview> et <xref:migration/22-to-30> .</span><span class="sxs-lookup"><span data-stu-id="56c73-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="56c73-133">Type de déploiement</span><span class="sxs-lookup"><span data-stu-id="56c73-133">Deployment type</span></span>

<span data-ttu-id="56c73-134">Pour des informations et des conseils sur les scénarios de déploiement, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="56c73-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="56c73-135">Kit SDK</span><span class="sxs-lookup"><span data-stu-id="56c73-135">SDK</span></span>

<span data-ttu-id="56c73-136">Pour un service basé sur une application Web qui utilise les Razor pages ou les infrastructures MVC, spécifiez le kit de développement logiciel (SDK) Web dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="56c73-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="56c73-137">Si le service exécute uniquement des tâches en arrière-plan (par exemple, les [services hébergés](xref:fundamentals/host/hosted-services)), spécifiez le kit de développement logiciel (SDK) Worker dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="56c73-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="56c73-138">Déploiement dépendant du framework</span><span class="sxs-lookup"><span data-stu-id="56c73-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="56c73-139">Un déploiement dépendant du framework s’appuie sur la présence d’une version partagée à l’échelle du système de .NET Core sur le système cible.</span><span class="sxs-lookup"><span data-stu-id="56c73-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="56c73-140">Lorsque vous effectuez le scénario de déploiement dépendant du framework en suivant les conseils du présent article, le Kit de développement logiciel (SDK) produit un fichier exécutable (*.exe*), appelé *fichier exécutable dépendant du framework*.</span><span class="sxs-lookup"><span data-stu-id="56c73-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="56c73-141">Si vous utilisez le [Kit de développement logiciel (SDK) Web](#sdk), un fichier de *web.config* , qui est normalement généré lors de la publication d’une application ASP.net Core, n’est pas nécessaire pour une application de services Windows.</span><span class="sxs-lookup"><span data-stu-id="56c73-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="56c73-142">Pour désactiver la création d’un fichier *web.config*, ajoutez la propriété `<IsTransformWebConfigDisabled>` définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="56c73-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="56c73-143">Déploiement autonome</span><span class="sxs-lookup"><span data-stu-id="56c73-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="56c73-144">Un déploiement autonome ne s’appuie sur la présence d’aucune infrastructure partagée sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="56c73-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="56c73-145">Le runtime et les dépendances de l’application sont déployés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="56c73-146">Un [identificateur de runtime (RID)](/dotnet/core/rid-catalog) Windows est inclus dans l’élément `<PropertyGroup>` qui contient la version cible de .NET Framework :</span><span class="sxs-lookup"><span data-stu-id="56c73-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="56c73-147">Pour publier pour plusieurs RID :</span><span class="sxs-lookup"><span data-stu-id="56c73-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="56c73-148">Fournissez les RID dans une liste séparée par des points-virgules.</span><span class="sxs-lookup"><span data-stu-id="56c73-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="56c73-149">Utilisez le nom de la propriété [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (pluriel).</span><span class="sxs-lookup"><span data-stu-id="56c73-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="56c73-150">Pour plus d’informations, consultez le [Catalogue RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="56c73-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="56c73-151">Compte d’utilisateur du service</span><span class="sxs-lookup"><span data-stu-id="56c73-151">Service user account</span></span>

<span data-ttu-id="56c73-152">Pour créer un compte d’utilisateur du service, utilisez la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) depuis un interpréteur de commandes d’administration PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="56c73-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="56c73-153">Dans la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) ou plus :</span><span class="sxs-lookup"><span data-stu-id="56c73-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="56c73-154">Dans un système d’exploitation Windows antérieur à la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) :</span><span class="sxs-lookup"><span data-stu-id="56c73-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="56c73-155">Fournissez un [mot de passe fort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) à l’invite.</span><span class="sxs-lookup"><span data-stu-id="56c73-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="56c73-156">Le compte n’expire pas, sauf si le paramètre `-AccountExpires` est fourni à la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) avec un <xref:System.DateTime> d’expiration.</span><span class="sxs-lookup"><span data-stu-id="56c73-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="56c73-157">Pour plus d’informations, voir [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) et [Comptes d’utilisateurs de service](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="56c73-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="56c73-158">Une approche alternative à la gestion des utilisateurs lors de l’utilisation d’Active Directory consiste à utiliser des Comptes de service administrés.</span><span class="sxs-lookup"><span data-stu-id="56c73-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="56c73-159">Pour plus d’informations, consultez [Vue d’ensemble des comptes de service administrés de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="56c73-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="56c73-160">Droits d’ouverture de session en tant que service</span><span class="sxs-lookup"><span data-stu-id="56c73-160">Log on as a service rights</span></span>

<span data-ttu-id="56c73-161">Pour établir des droits *d’ouverture de session en tant que service* pour un compte d’utilisateur de service, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="56c73-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="56c73-162">Ouvrez l’éditeur de stratégie de sécurité locale en exécutant le fichier *secpool.msc*.</span><span class="sxs-lookup"><span data-stu-id="56c73-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="56c73-163">Développez le nœud **Stratégies locales** et sélectionnez **Attribution des droits utilisateur**.</span><span class="sxs-lookup"><span data-stu-id="56c73-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="56c73-164">Ouvrez la stratégie **Ouvrir une session en tant que service**.</span><span class="sxs-lookup"><span data-stu-id="56c73-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="56c73-165">Sélectionnez **Ajouter un utilisateur ou un groupe**.</span><span class="sxs-lookup"><span data-stu-id="56c73-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="56c73-166">Fournissez le nom d’objet (compte d’utilisateur) avec l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="56c73-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="56c73-167">Tapez le compte d’utilisateur (`{DOMAIN OR COMPUTER NAME\USER}`) dans le champ du nom d’objet et sélectionnez **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="56c73-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="56c73-168">Sélectionnez **Avancé**.</span><span class="sxs-lookup"><span data-stu-id="56c73-168">Select **Advanced**.</span></span> <span data-ttu-id="56c73-169">Sélectionnez **Rechercher maintenant**.</span><span class="sxs-lookup"><span data-stu-id="56c73-169">Select **Find Now**.</span></span> <span data-ttu-id="56c73-170">Sélectionnez le compte d’utilisateur dans la liste.</span><span class="sxs-lookup"><span data-stu-id="56c73-170">Select the user account from the list.</span></span> <span data-ttu-id="56c73-171">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="56c73-171">Select **OK**.</span></span> <span data-ttu-id="56c73-172">Cliquez à nouveau sur **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="56c73-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="56c73-173">Sélectionnez **OK** ou **Appliquer** pour accepter les modifications.</span><span class="sxs-lookup"><span data-stu-id="56c73-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="56c73-174">Créer et gérer le service Windows</span><span class="sxs-lookup"><span data-stu-id="56c73-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="56c73-175">Créer un service</span><span class="sxs-lookup"><span data-stu-id="56c73-175">Create a service</span></span>

<span data-ttu-id="56c73-176">Utilisez les commandes PowerShell pour enregistrer un service.</span><span class="sxs-lookup"><span data-stu-id="56c73-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="56c73-177">À partir d’un interpréteur de commandes d’administration PowerShell 6, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="56c73-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="56c73-178">`{EXE PATH}`: Chemin d’accès au dossier de l’application sur l’hôte (par exemple, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="56c73-179">N’incluez pas le fichier exécutable de l’application dans le chemin.</span><span class="sxs-lookup"><span data-stu-id="56c73-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="56c73-180">Aucune barre oblique de fin n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="56c73-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="56c73-181">`{DOMAIN OR COMPUTER NAME\USER}`: Compte d’utilisateur du service (par exemple, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="56c73-182">`{SERVICE NAME}`: Nom du service (par exemple, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="56c73-183">`{EXE FILE PATH}`: Le chemin d’accès de l’exécutable de l’application (par exemple, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="56c73-184">Incluez le nom de fichier de l’exécutable avec l’extension.</span><span class="sxs-lookup"><span data-stu-id="56c73-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="56c73-185">`{DESCRIPTION}`: Description du service (par exemple, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="56c73-186">`{DISPLAY NAME}`: Nom d’affichage du service (par exemple, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="56c73-187">Démarrer un service</span><span class="sxs-lookup"><span data-stu-id="56c73-187">Start a service</span></span>

<span data-ttu-id="56c73-188">Démarrez un service avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="56c73-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="56c73-189">La commande prend quelques secondes pour démarrer le service.</span><span class="sxs-lookup"><span data-stu-id="56c73-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="56c73-190">Déterminer l’état d’un service</span><span class="sxs-lookup"><span data-stu-id="56c73-190">Determine a service's status</span></span>

<span data-ttu-id="56c73-191">Pour vérifier l’état d’un service, utilisez la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="56c73-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="56c73-192">L’état est signalé comme étant l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="56c73-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="56c73-193">Arrêter un service</span><span class="sxs-lookup"><span data-stu-id="56c73-193">Stop a service</span></span>

<span data-ttu-id="56c73-194">Arrêtez un service avec la commande Powershell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="56c73-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="56c73-195">Supprimer un service</span><span class="sxs-lookup"><span data-stu-id="56c73-195">Remove a service</span></span>

<span data-ttu-id="56c73-196">Après un court délai pour arrêter un service, supprimez-le avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="56c73-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="56c73-197">Scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="56c73-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="56c73-198">Les services qui interagissent avec les requêtes provenant d’Internet ou d’un réseau d’entreprise et qui se trouvent derrière un proxy ou équilibreur de charge peuvent nécessiter une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="56c73-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="56c73-199">Pour plus d'informations, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="56c73-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="56c73-200">Configuration des points de terminaison</span><span class="sxs-lookup"><span data-stu-id="56c73-200">Configure endpoints</span></span>

<span data-ttu-id="56c73-201">Par défaut, ASP.NET Core est lié à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="56c73-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="56c73-202">Configurez l’URL et le port en définissant la `ASPNETCORE_URLS` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="56c73-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="56c73-203">Pour obtenir d’autres approches de configuration des ports et des URL, consultez l’article approprié sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="56c73-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="56c73-204">L’aide précédente couvre la prise en charge des points de terminaison HTTPs.</span><span class="sxs-lookup"><span data-stu-id="56c73-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="56c73-205">Par exemple, configurez l’application pour HTTPs lorsque l’authentification est utilisée avec un service Windows.</span><span class="sxs-lookup"><span data-stu-id="56c73-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="56c73-206">L’utilisation du certificat de développement HTTPS ASP.NET Core pour sécuriser un point de terminaison de service n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="56c73-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="56c73-207">Répertoire actif et racine du contenu</span><span class="sxs-lookup"><span data-stu-id="56c73-207">Current directory and content root</span></span>

<span data-ttu-id="56c73-208">Le répertoire de travail actuel retourné en appelant <xref:System.IO.Directory.GetCurrentDirectory*> pour un service Windows est le dossier \* \\ \\ system32 de C : Windows\* .</span><span class="sxs-lookup"><span data-stu-id="56c73-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="56c73-209">Le dossier *system32* n’est pas un emplacement approprié pour stocker les fichiers d’un service (tels que les fichiers de paramètres).</span><span class="sxs-lookup"><span data-stu-id="56c73-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="56c73-210">Utilisez une des approches suivantes pour gérer les ressources ainsi que les fichiers de paramètres d’un service, et y accéder.</span><span class="sxs-lookup"><span data-stu-id="56c73-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="56c73-211">Utiliser ContentRootPath ou ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="56c73-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="56c73-212">Utilisez les éléments [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) ou <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> pour localiser les ressources d’une application.</span><span class="sxs-lookup"><span data-stu-id="56c73-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="56c73-213">Lorsque l’application s’exécute en tant que service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> définit <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> sur [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="56c73-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="56c73-214">Les fichiers de paramètres par défaut de l’application, *appsettings.jssur* et *appSettings. { Environment}. JSON*, sont chargés à partir de la racine de contenu de l’application en appelant [CreateDefaultBuilder lors](xref:fundamentals/host/generic-host#set-up-a-host)de la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="56c73-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="56c73-215">Pour les autres fichiers de paramètres chargés par le code du développeur dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> , il n’est pas nécessaire d’appeler <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> .</span><span class="sxs-lookup"><span data-stu-id="56c73-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="56c73-216">Dans l’exemple suivant, la *custom_settings.jssur* le fichier existe dans la racine de contenu de l’application et est chargée sans définir explicitement un chemin d’accès de base :</span><span class="sxs-lookup"><span data-stu-id="56c73-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="56c73-217">N’essayez pas d’utiliser <xref:System.IO.Directory.GetCurrentDirectory*> pour obtenir un chemin d’accès de ressource, car une application de service Windows retourne le dossier *C : \\ Windows \\ system32* comme répertoire actif.</span><span class="sxs-lookup"><span data-stu-id="56c73-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="56c73-218">Stocker les fichiers d’un service dans un emplacement approprié sur le disque</span><span class="sxs-lookup"><span data-stu-id="56c73-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="56c73-219">Spécifiez un chemin absolu avec <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>, si vous utilisez <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, vers le dossier contenant les fichiers.</span><span class="sxs-lookup"><span data-stu-id="56c73-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="56c73-220">Dépanner</span><span class="sxs-lookup"><span data-stu-id="56c73-220">Troubleshoot</span></span>

<span data-ttu-id="56c73-221">Pour dépanner une application de service Windows, consultez <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="56c73-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="56c73-222">Erreurs courantes</span><span class="sxs-lookup"><span data-stu-id="56c73-222">Common errors</span></span>

* <span data-ttu-id="56c73-223">Une version ancienne ou préliminaire de PowerShell est en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="56c73-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="56c73-224">Le service inscrit n’utilise pas la sortie **publiée** de l’application à partir de la commande [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="56c73-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="56c73-225">La sortie de la commande [dotnet Build](/dotnet/core/tools/dotnet-build) n’est pas prise en charge pour le déploiement d’applications.</span><span class="sxs-lookup"><span data-stu-id="56c73-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="56c73-226">Les ressources publiées se trouvent dans l’un des dossiers suivants, selon le type de déploiement :</span><span class="sxs-lookup"><span data-stu-id="56c73-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="56c73-227">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="56c73-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="56c73-228">*bin/Release/{Target Framework}/{Runtime identificateur}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="56c73-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="56c73-229">Le service n’est pas à l’État en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="56c73-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="56c73-230">Les chemins d’accès aux ressources que l’application utilise (par exemple, les certificats) sont incorrects.</span><span class="sxs-lookup"><span data-stu-id="56c73-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="56c73-231">Le chemin d’accès de base d’un service Windows est *c : \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="56c73-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="56c73-232">L’utilisateur ne dispose pas de droits *d’ouverture de session en tant que service* .</span><span class="sxs-lookup"><span data-stu-id="56c73-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="56c73-233">Le mot de passe de l’utilisateur a expiré ou a été transmis de manière incorrecte lors de l’exécution de la `New-Service` commande PowerShell.</span><span class="sxs-lookup"><span data-stu-id="56c73-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="56c73-234">L’application requiert l’authentification ASP.NET Core, mais elle n’est pas configurée pour les connexions sécurisées (HTTPs).</span><span class="sxs-lookup"><span data-stu-id="56c73-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="56c73-235">Le port de l’URL de requête est incorrect ou n’est pas correctement configuré dans l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="56c73-236">Journaux des événements système et des applications</span><span class="sxs-lookup"><span data-stu-id="56c73-236">System and Application Event Logs</span></span>

<span data-ttu-id="56c73-237">Accédez aux journaux des événements système et des applications :</span><span class="sxs-lookup"><span data-stu-id="56c73-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="56c73-238">Ouvrez le menu Démarrer, recherchez *Observateur d’événements*, puis sélectionnez l’application **Observateur d’événements** .</span><span class="sxs-lookup"><span data-stu-id="56c73-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="56c73-239">Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.</span><span class="sxs-lookup"><span data-stu-id="56c73-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="56c73-240">Sélectionnez **système** pour ouvrir le journal des événements système.</span><span class="sxs-lookup"><span data-stu-id="56c73-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="56c73-241">Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="56c73-242">Recherchez les erreurs liées à l’application défectueuse.</span><span class="sxs-lookup"><span data-stu-id="56c73-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="56c73-243">Exécuter l’application depuis une invite de commandes</span><span class="sxs-lookup"><span data-stu-id="56c73-243">Run the app at a command prompt</span></span>

<span data-ttu-id="56c73-244">De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans les journaux des événements.</span><span class="sxs-lookup"><span data-stu-id="56c73-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="56c73-245">Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="56c73-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="56c73-246">Pour consigner des détails supplémentaires à partir de l’application, diminuez le [niveau de journalisation](xref:fundamentals/logging/index#log-level) ou exécutez l’application dans l' [environnement de développement](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="56c73-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="56c73-247">Effacer les caches de package</span><span class="sxs-lookup"><span data-stu-id="56c73-247">Clear package caches</span></span>

<span data-ttu-id="56c73-248">Une application fonctionnelle peut échouer immédiatement après la mise à niveau de la kit SDK .NET Core sur l’ordinateur de développement ou la modification des versions de package dans l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="56c73-249">Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures.</span><span class="sxs-lookup"><span data-stu-id="56c73-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="56c73-250">Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :</span><span class="sxs-lookup"><span data-stu-id="56c73-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="56c73-251">Supprimez les dossiers *bin* et *obj*.</span><span class="sxs-lookup"><span data-stu-id="56c73-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="56c73-252">Effacez les caches de package en exécutant [dotnet NuGet LOCALS tout--Clear](/dotnet/core/tools/dotnet-nuget-locals) dans une interface de commande.</span><span class="sxs-lookup"><span data-stu-id="56c73-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="56c73-253">L’effacement des caches de package peut également être effectué à l’aide de l’outil [nuget.exe](https://www.nuget.org/downloads) et en exécutant la commande `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="56c73-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="56c73-254">*NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="56c73-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="56c73-255">Restaurez et regénérez le projet.</span><span class="sxs-lookup"><span data-stu-id="56c73-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="56c73-256">Supprimez tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="56c73-257">Application lente ou bloquée</span><span class="sxs-lookup"><span data-stu-id="56c73-257">Slow or hanging app</span></span>

<span data-ttu-id="56c73-258">Un *vidage sur incident* est un instantané de la mémoire du système et peut aider à déterminer la cause d’un incident d’application, d’un échec de démarrage ou d’une application lente.</span><span class="sxs-lookup"><span data-stu-id="56c73-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="56c73-259">L’application cesse de fonctionner ou rencontre une exception</span><span class="sxs-lookup"><span data-stu-id="56c73-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="56c73-260">Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :</span><span class="sxs-lookup"><span data-stu-id="56c73-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="56c73-261">Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="56c73-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="56c73-262">Exécutez le [script PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) avec le nom de l’exécutable de l’application :</span><span class="sxs-lookup"><span data-stu-id="56c73-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="56c73-263">Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.</span><span class="sxs-lookup"><span data-stu-id="56c73-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="56c73-264">Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1) :</span><span class="sxs-lookup"><span data-stu-id="56c73-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="56c73-265">Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement.</span><span class="sxs-lookup"><span data-stu-id="56c73-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="56c73-266">Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.</span><span class="sxs-lookup"><span data-stu-id="56c73-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="56c73-267">Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).</span><span class="sxs-lookup"><span data-stu-id="56c73-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="56c73-268">L’application se bloque, ne démarre pas ou s’exécute normalement</span><span class="sxs-lookup"><span data-stu-id="56c73-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="56c73-269">Quand une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage ou s’exécute normalement, consultez [fichiers de vidage en mode utilisateur : choix de l’outil le mieux](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) adapté pour sélectionner un outil approprié pour produire le vidage.</span><span class="sxs-lookup"><span data-stu-id="56c73-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="56c73-270">Analyser le fichier dump</span><span class="sxs-lookup"><span data-stu-id="56c73-270">Analyze the dump</span></span>

<span data-ttu-id="56c73-271">Un fichier dump peut être analysé à l’aide de plusieurs approches.</span><span class="sxs-lookup"><span data-stu-id="56c73-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="56c73-272">Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).</span><span class="sxs-lookup"><span data-stu-id="56c73-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="56c73-273">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="56c73-273">Additional resources</span></span>

* <span data-ttu-id="56c73-274">[Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclut la configuration de HTTPS et la prise en charge de SNI)</span><span class="sxs-lookup"><span data-stu-id="56c73-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="56c73-275">Une application ASP.NET Core peut être hébergée sur Windows en tant que [service Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sans utiliser IIS.</span><span class="sxs-lookup"><span data-stu-id="56c73-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="56c73-276">Lorsqu’elle est hébergée en tant que service Windows, l’application se lance automatiquement après le redémarrage du serveur.</span><span class="sxs-lookup"><span data-stu-id="56c73-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="56c73-277">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="56c73-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="56c73-278">Prérequis</span><span class="sxs-lookup"><span data-stu-id="56c73-278">Prerequisites</span></span>

* [<span data-ttu-id="56c73-279">Kit de développement logiciel (SDK) ASP.NET Core 2.1 ou plus</span><span class="sxs-lookup"><span data-stu-id="56c73-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="56c73-280">PowerShell 6,2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="56c73-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="56c73-281">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="56c73-281">App configuration</span></span>

<span data-ttu-id="56c73-282">L’application nécessite des références de package pour [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) et [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="56c73-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="56c73-283">Pour effectuer des tests et un débogage lors de l’exécution en dehors d’un service, ajoutez un code pour déterminer si l’application s’exécute comme un service ou comme une application console.</span><span class="sxs-lookup"><span data-stu-id="56c73-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="56c73-284">Vérifiez si le débogueur est attaché ou si un switch `--console` est présent.</span><span class="sxs-lookup"><span data-stu-id="56c73-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="56c73-285">Si l’une de ces deux conditions est remplie (l’application n’est pas exécutée en tant que service), appelez <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="56c73-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="56c73-286">Si les conditions ne sont pas remplies (l’application est exécutée en tant que service) :</span><span class="sxs-lookup"><span data-stu-id="56c73-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="56c73-287">Appelez <xref:System.IO.Directory.SetCurrentDirectory*> et utilisez un chemin vers l’emplacement publié de l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="56c73-288">N’appelez pas <xref:System.IO.Directory.GetCurrentDirectory*> pour obtenir le chemin d’accès car une application Windows Service retourne le dossier *C:\\WINDOWS\\system32* lorsque <xref:System.IO.Directory.GetCurrentDirectory*> est appelée.</span><span class="sxs-lookup"><span data-stu-id="56c73-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="56c73-289">Pour plus d’informations, consultez la section [Répertoire actif et racine du contenu](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="56c73-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="56c73-290">Cette étape est effectuée avant la configuration de l’application dans `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="56c73-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="56c73-291">Appelez <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pour exécuter l’application en tant que service.</span><span class="sxs-lookup"><span data-stu-id="56c73-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="56c73-292">Étant donné que le [fournisseur de configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider) nécessite des paires nom/valeur pour les arguments de ligne de commande, le switch `--console` est supprimé des arguments avant que <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ne les reçoive.</span><span class="sxs-lookup"><span data-stu-id="56c73-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="56c73-293">Pour consigner des informations dans le journal des événements Windows, ajoutez le fournisseur EventLog à <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="56c73-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="56c73-294">Définissez le niveau de journalisation à l’aide de clé `Logging:LogLevel:Default` dans le fichier *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="56c73-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="56c73-295">Dans l’exemple suivant, qui provient de l’exemple d’application, l’élément `RunAsCustomService` est appelé à la place de l’élément <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> afin de gérer les événements de durée de vie au sein de l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="56c73-296">Pour plus d’informations, consultez la section [Gérer les événements de démarrage et d’arrêt](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="56c73-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="56c73-297">Type de déploiement</span><span class="sxs-lookup"><span data-stu-id="56c73-297">Deployment type</span></span>

<span data-ttu-id="56c73-298">Pour des informations et des conseils sur les scénarios de déploiement, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="56c73-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="56c73-299">Kit SDK</span><span class="sxs-lookup"><span data-stu-id="56c73-299">SDK</span></span>

<span data-ttu-id="56c73-300">Pour un service basé sur une application Web qui utilise les Razor pages ou les infrastructures MVC, spécifiez le kit de développement logiciel (SDK) Web dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="56c73-300">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="56c73-301">Si le service exécute uniquement des tâches en arrière-plan (par exemple, les [services hébergés](xref:fundamentals/host/hosted-services)), spécifiez le kit de développement logiciel (SDK) Worker dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="56c73-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="56c73-302">Déploiement dépendant du framework</span><span class="sxs-lookup"><span data-stu-id="56c73-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="56c73-303">Un déploiement dépendant du framework s’appuie sur la présence d’une version partagée à l’échelle du système de .NET Core sur le système cible.</span><span class="sxs-lookup"><span data-stu-id="56c73-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="56c73-304">Lorsque vous effectuez le scénario de déploiement dépendant du framework en suivant les conseils du présent article, le Kit de développement logiciel (SDK) produit un fichier exécutable (*.exe*), appelé *fichier exécutable dépendant du framework*.</span><span class="sxs-lookup"><span data-stu-id="56c73-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="56c73-305">L' [identificateur Windows Runtime (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) contient la version cible de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="56c73-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="56c73-306">Dans l’exemple suivant, le RID est défini sur `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="56c73-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="56c73-307">La propriété `<SelfContained>` est définie sur `false`.</span><span class="sxs-lookup"><span data-stu-id="56c73-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="56c73-308">Ces propriétés demandent au Kit de développement logiciel (SDK) de générer un fichier exécutable (*.exe*) pour Windows et une application qui dépend du framework .NET Core partagé.</span><span class="sxs-lookup"><span data-stu-id="56c73-308">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="56c73-309">Un fichier *web.config*, qui est normalement produit lors de la publication d’une application ASP.NET Core, n’est pas nécessaire pour une application de Windows Services.</span><span class="sxs-lookup"><span data-stu-id="56c73-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="56c73-310">Pour désactiver la création d’un fichier *web.config*, ajoutez la propriété `<IsTransformWebConfigDisabled>` définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="56c73-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="56c73-311">Déploiement autonome</span><span class="sxs-lookup"><span data-stu-id="56c73-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="56c73-312">Un déploiement autonome ne s’appuie sur la présence d’aucune infrastructure partagée sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="56c73-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="56c73-313">Le runtime et les dépendances de l’application sont déployés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="56c73-314">Un [identificateur de runtime (RID)](/dotnet/core/rid-catalog) Windows est inclus dans l’élément `<PropertyGroup>` qui contient la version cible de .NET Framework :</span><span class="sxs-lookup"><span data-stu-id="56c73-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="56c73-315">Pour publier pour plusieurs RID :</span><span class="sxs-lookup"><span data-stu-id="56c73-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="56c73-316">Fournissez les RID dans une liste séparée par des points-virgules.</span><span class="sxs-lookup"><span data-stu-id="56c73-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="56c73-317">Utilisez le nom de la propriété [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (pluriel).</span><span class="sxs-lookup"><span data-stu-id="56c73-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="56c73-318">Pour plus d’informations, consultez le [Catalogue RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="56c73-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="56c73-319">Une propriété `<SelfContained>` est définie sur `true` :</span><span class="sxs-lookup"><span data-stu-id="56c73-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="56c73-320">Compte d’utilisateur du service</span><span class="sxs-lookup"><span data-stu-id="56c73-320">Service user account</span></span>

<span data-ttu-id="56c73-321">Pour créer un compte d’utilisateur du service, utilisez la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) depuis un interpréteur de commandes d’administration PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="56c73-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="56c73-322">Dans la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) ou plus :</span><span class="sxs-lookup"><span data-stu-id="56c73-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="56c73-323">Dans un système d’exploitation Windows antérieur à la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) :</span><span class="sxs-lookup"><span data-stu-id="56c73-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="56c73-324">Fournissez un [mot de passe fort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) à l’invite.</span><span class="sxs-lookup"><span data-stu-id="56c73-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="56c73-325">Le compte n’expire pas, sauf si le paramètre `-AccountExpires` est fourni à la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) avec un <xref:System.DateTime> d’expiration.</span><span class="sxs-lookup"><span data-stu-id="56c73-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="56c73-326">Pour plus d’informations, voir [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) et [Comptes d’utilisateurs de service](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="56c73-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="56c73-327">Une approche alternative à la gestion des utilisateurs lors de l’utilisation d’Active Directory consiste à utiliser des Comptes de service administrés.</span><span class="sxs-lookup"><span data-stu-id="56c73-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="56c73-328">Pour plus d’informations, consultez [Vue d’ensemble des comptes de service administrés de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="56c73-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="56c73-329">Droits d’ouverture de session en tant que service</span><span class="sxs-lookup"><span data-stu-id="56c73-329">Log on as a service rights</span></span>

<span data-ttu-id="56c73-330">Pour établir des droits *d’ouverture de session en tant que service* pour un compte d’utilisateur de service, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="56c73-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="56c73-331">Ouvrez l’éditeur de stratégie de sécurité locale en exécutant le fichier *secpool.msc*.</span><span class="sxs-lookup"><span data-stu-id="56c73-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="56c73-332">Développez le nœud **Stratégies locales** et sélectionnez **Attribution des droits utilisateur**.</span><span class="sxs-lookup"><span data-stu-id="56c73-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="56c73-333">Ouvrez la stratégie **Ouvrir une session en tant que service**.</span><span class="sxs-lookup"><span data-stu-id="56c73-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="56c73-334">Sélectionnez **Ajouter un utilisateur ou un groupe**.</span><span class="sxs-lookup"><span data-stu-id="56c73-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="56c73-335">Fournissez le nom d’objet (compte d’utilisateur) avec l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="56c73-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="56c73-336">Tapez le compte d’utilisateur (`{DOMAIN OR COMPUTER NAME\USER}`) dans le champ du nom d’objet et sélectionnez **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="56c73-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="56c73-337">Sélectionnez **Avancé**.</span><span class="sxs-lookup"><span data-stu-id="56c73-337">Select **Advanced**.</span></span> <span data-ttu-id="56c73-338">Sélectionnez **Rechercher maintenant**.</span><span class="sxs-lookup"><span data-stu-id="56c73-338">Select **Find Now**.</span></span> <span data-ttu-id="56c73-339">Sélectionnez le compte d’utilisateur dans la liste.</span><span class="sxs-lookup"><span data-stu-id="56c73-339">Select the user account from the list.</span></span> <span data-ttu-id="56c73-340">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="56c73-340">Select **OK**.</span></span> <span data-ttu-id="56c73-341">Cliquez à nouveau sur **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="56c73-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="56c73-342">Sélectionnez **OK** ou **Appliquer** pour accepter les modifications.</span><span class="sxs-lookup"><span data-stu-id="56c73-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="56c73-343">Créer et gérer le service Windows</span><span class="sxs-lookup"><span data-stu-id="56c73-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="56c73-344">Créer un service</span><span class="sxs-lookup"><span data-stu-id="56c73-344">Create a service</span></span>

<span data-ttu-id="56c73-345">Utilisez les commandes PowerShell pour enregistrer un service.</span><span class="sxs-lookup"><span data-stu-id="56c73-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="56c73-346">À partir d’un interpréteur de commandes d’administration PowerShell 6, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="56c73-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="56c73-347">`{EXE PATH}`: Chemin d’accès au dossier de l’application sur l’hôte (par exemple, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-347">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="56c73-348">N’incluez pas le fichier exécutable de l’application dans le chemin.</span><span class="sxs-lookup"><span data-stu-id="56c73-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="56c73-349">Aucune barre oblique de fin n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="56c73-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="56c73-350">`{DOMAIN OR COMPUTER NAME\USER}`: Compte d’utilisateur du service (par exemple, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-350">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="56c73-351">`{SERVICE NAME}`: Nom du service (par exemple, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-351">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="56c73-352">`{EXE FILE PATH}`: Le chemin d’accès de l’exécutable de l’application (par exemple, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-352">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="56c73-353">Incluez le nom de fichier de l’exécutable avec l’extension.</span><span class="sxs-lookup"><span data-stu-id="56c73-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="56c73-354">`{DESCRIPTION}`: Description du service (par exemple, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-354">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="56c73-355">`{DISPLAY NAME}`: Nom d’affichage du service (par exemple, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-355">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="56c73-356">Démarrer un service</span><span class="sxs-lookup"><span data-stu-id="56c73-356">Start a service</span></span>

<span data-ttu-id="56c73-357">Démarrez un service avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="56c73-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="56c73-358">La commande prend quelques secondes pour démarrer le service.</span><span class="sxs-lookup"><span data-stu-id="56c73-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="56c73-359">Déterminer l’état d’un service</span><span class="sxs-lookup"><span data-stu-id="56c73-359">Determine a service's status</span></span>

<span data-ttu-id="56c73-360">Pour vérifier l’état d’un service, utilisez la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="56c73-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="56c73-361">L’état est signalé comme étant l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="56c73-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="56c73-362">Arrêter un service</span><span class="sxs-lookup"><span data-stu-id="56c73-362">Stop a service</span></span>

<span data-ttu-id="56c73-363">Arrêtez un service avec la commande Powershell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="56c73-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="56c73-364">Supprimer un service</span><span class="sxs-lookup"><span data-stu-id="56c73-364">Remove a service</span></span>

<span data-ttu-id="56c73-365">Après un court délai pour arrêter un service, supprimez-le avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="56c73-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="56c73-366">Gérer les événements de démarrage et d’arrêt</span><span class="sxs-lookup"><span data-stu-id="56c73-366">Handle starting and stopping events</span></span>

<span data-ttu-id="56c73-367">Pour gérer les événements <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> et <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="56c73-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="56c73-368">Créez une classe qui dérive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> à l’aide des méthodes `OnStarting`, `OnStarted` et `OnStopping` :</span><span class="sxs-lookup"><span data-stu-id="56c73-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="56c73-369">Créez une méthode d’extension pour <xref:Microsoft.AspNetCore.Hosting.IWebHost> qui transmet `CustomWebHostService` à <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="56c73-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="56c73-370">Dans `Program.Main`, appelez la méthode d’extension `RunAsCustomService` au lieu de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="56c73-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="56c73-371">Pour afficher l’emplacement de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> dans `Program.Main`, reportez-vous à l’exemple de code indiqué dans la section [Type de déploiement](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="56c73-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="56c73-372">Scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="56c73-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="56c73-373">Les services qui interagissent avec les requêtes provenant d’Internet ou d’un réseau d’entreprise et qui se trouvent derrière un proxy ou équilibreur de charge peuvent nécessiter une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="56c73-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="56c73-374">Pour plus d'informations, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="56c73-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="56c73-375">Configuration des points de terminaison</span><span class="sxs-lookup"><span data-stu-id="56c73-375">Configure endpoints</span></span>

<span data-ttu-id="56c73-376">Par défaut, ASP.NET Core est lié à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="56c73-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="56c73-377">Configurez l’URL et le port en définissant la `ASPNETCORE_URLS` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="56c73-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="56c73-378">Pour obtenir d’autres approches de configuration des ports et des URL, consultez l’article approprié sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="56c73-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="56c73-379">L’aide précédente couvre la prise en charge des points de terminaison HTTPs.</span><span class="sxs-lookup"><span data-stu-id="56c73-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="56c73-380">Par exemple, configurez l’application pour HTTPs lorsque l’authentification est utilisée avec un service Windows.</span><span class="sxs-lookup"><span data-stu-id="56c73-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="56c73-381">L’utilisation du certificat de développement HTTPS ASP.NET Core pour sécuriser un point de terminaison de service n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="56c73-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="56c73-382">Répertoire actif et racine du contenu</span><span class="sxs-lookup"><span data-stu-id="56c73-382">Current directory and content root</span></span>

<span data-ttu-id="56c73-383">Le répertoire de travail actuel retourné en appelant <xref:System.IO.Directory.GetCurrentDirectory*> pour un service Windows est le dossier \* \\ \\ system32 de C : Windows\* .</span><span class="sxs-lookup"><span data-stu-id="56c73-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="56c73-384">Le dossier *system32* n’est pas un emplacement approprié pour stocker les fichiers d’un service (tels que les fichiers de paramètres).</span><span class="sxs-lookup"><span data-stu-id="56c73-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="56c73-385">Utilisez une des approches suivantes pour gérer les ressources ainsi que les fichiers de paramètres d’un service, et y accéder.</span><span class="sxs-lookup"><span data-stu-id="56c73-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="56c73-386">Définir le dossier de l’application comme chemin d’accès racine du contenu</span><span class="sxs-lookup"><span data-stu-id="56c73-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="56c73-387">La chaîne <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> correspond au même chemin que celui fourni à l’argument `binPath` lorsqu’un service est créé.</span><span class="sxs-lookup"><span data-stu-id="56c73-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="56c73-388">Au lieu d’appeler `GetCurrentDirectory` pour créer des chemins d’accès aux fichiers de paramètres, appelez <xref:System.IO.Directory.SetCurrentDirectory*> avec le chemin d’accès à la [racine de contenu](xref:fundamentals/index#content-root)de l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="56c73-389">Dans `Program.Main`, définissez le chemin d’accès au dossier du fichier exécutable du service ainsi que le chemin d’accès pour établir la racine du contenu de l’application :</span><span class="sxs-lookup"><span data-stu-id="56c73-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="56c73-390">Stocker les fichiers d’un service dans un emplacement approprié sur le disque</span><span class="sxs-lookup"><span data-stu-id="56c73-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="56c73-391">Spécifiez un chemin absolu avec <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>, si vous utilisez <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, vers le dossier contenant les fichiers.</span><span class="sxs-lookup"><span data-stu-id="56c73-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="56c73-392">Dépanner</span><span class="sxs-lookup"><span data-stu-id="56c73-392">Troubleshoot</span></span>

<span data-ttu-id="56c73-393">Pour dépanner une application de service Windows, consultez <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="56c73-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="56c73-394">Erreurs courantes</span><span class="sxs-lookup"><span data-stu-id="56c73-394">Common errors</span></span>

* <span data-ttu-id="56c73-395">Une version ancienne ou préliminaire de PowerShell est en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="56c73-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="56c73-396">Le service inscrit n’utilise pas la sortie **publiée** de l’application à partir de la commande [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="56c73-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="56c73-397">La sortie de la commande [dotnet Build](/dotnet/core/tools/dotnet-build) n’est pas prise en charge pour le déploiement d’applications.</span><span class="sxs-lookup"><span data-stu-id="56c73-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="56c73-398">Les ressources publiées se trouvent dans l’un des dossiers suivants, selon le type de déploiement :</span><span class="sxs-lookup"><span data-stu-id="56c73-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="56c73-399">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="56c73-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="56c73-400">*bin/Release/{Target Framework}/{Runtime identificateur}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="56c73-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="56c73-401">Le service n’est pas à l’État en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="56c73-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="56c73-402">Les chemins d’accès aux ressources que l’application utilise (par exemple, les certificats) sont incorrects.</span><span class="sxs-lookup"><span data-stu-id="56c73-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="56c73-403">Le chemin d’accès de base d’un service Windows est *c : \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="56c73-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="56c73-404">L’utilisateur ne dispose pas de droits *d’ouverture de session en tant que service* .</span><span class="sxs-lookup"><span data-stu-id="56c73-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="56c73-405">Le mot de passe de l’utilisateur a expiré ou a été transmis de manière incorrecte lors de l’exécution de la `New-Service` commande PowerShell.</span><span class="sxs-lookup"><span data-stu-id="56c73-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="56c73-406">L’application requiert l’authentification ASP.NET Core, mais elle n’est pas configurée pour les connexions sécurisées (HTTPs).</span><span class="sxs-lookup"><span data-stu-id="56c73-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="56c73-407">Le port de l’URL de requête est incorrect ou n’est pas correctement configuré dans l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="56c73-408">Journaux des événements système et des applications</span><span class="sxs-lookup"><span data-stu-id="56c73-408">System and Application Event Logs</span></span>

<span data-ttu-id="56c73-409">Accédez aux journaux des événements système et des applications :</span><span class="sxs-lookup"><span data-stu-id="56c73-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="56c73-410">Ouvrez le menu Démarrer, recherchez *Observateur d’événements*, puis sélectionnez l’application **Observateur d’événements** .</span><span class="sxs-lookup"><span data-stu-id="56c73-410">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="56c73-411">Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.</span><span class="sxs-lookup"><span data-stu-id="56c73-411">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="56c73-412">Sélectionnez **système** pour ouvrir le journal des événements système.</span><span class="sxs-lookup"><span data-stu-id="56c73-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="56c73-413">Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="56c73-414">Recherchez les erreurs liées à l’application défectueuse.</span><span class="sxs-lookup"><span data-stu-id="56c73-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="56c73-415">Exécuter l’application depuis une invite de commandes</span><span class="sxs-lookup"><span data-stu-id="56c73-415">Run the app at a command prompt</span></span>

<span data-ttu-id="56c73-416">De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans les journaux des événements.</span><span class="sxs-lookup"><span data-stu-id="56c73-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="56c73-417">Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="56c73-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="56c73-418">Pour consigner des détails supplémentaires à partir de l’application, diminuez le [niveau de journalisation](xref:fundamentals/logging/index#log-level) ou exécutez l’application dans l' [environnement de développement](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="56c73-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="56c73-419">Effacer les caches de package</span><span class="sxs-lookup"><span data-stu-id="56c73-419">Clear package caches</span></span>

<span data-ttu-id="56c73-420">Une application fonctionnelle peut échouer immédiatement après la mise à niveau de la kit SDK .NET Core sur l’ordinateur de développement ou la modification des versions de package dans l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="56c73-421">Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures.</span><span class="sxs-lookup"><span data-stu-id="56c73-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="56c73-422">Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :</span><span class="sxs-lookup"><span data-stu-id="56c73-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="56c73-423">Supprimez les dossiers *bin* et *obj*.</span><span class="sxs-lookup"><span data-stu-id="56c73-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="56c73-424">Effacez les caches de package en exécutant [dotnet NuGet LOCALS tout--Clear](/dotnet/core/tools/dotnet-nuget-locals) dans une interface de commande.</span><span class="sxs-lookup"><span data-stu-id="56c73-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="56c73-425">L’effacement des caches de package peut également être effectué à l’aide de l’outil [nuget.exe](https://www.nuget.org/downloads) et en exécutant la commande `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="56c73-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="56c73-426">*NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="56c73-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="56c73-427">Restaurez et regénérez le projet.</span><span class="sxs-lookup"><span data-stu-id="56c73-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="56c73-428">Supprimez tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="56c73-429">Application lente ou bloquée</span><span class="sxs-lookup"><span data-stu-id="56c73-429">Slow or hanging app</span></span>

<span data-ttu-id="56c73-430">Un *vidage sur incident* est un instantané de la mémoire du système et peut aider à déterminer la cause d’un incident d’application, d’un échec de démarrage ou d’une application lente.</span><span class="sxs-lookup"><span data-stu-id="56c73-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="56c73-431">L’application cesse de fonctionner ou rencontre une exception</span><span class="sxs-lookup"><span data-stu-id="56c73-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="56c73-432">Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :</span><span class="sxs-lookup"><span data-stu-id="56c73-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="56c73-433">Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="56c73-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="56c73-434">Exécutez le [script PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) avec le nom de l’exécutable de l’application :</span><span class="sxs-lookup"><span data-stu-id="56c73-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="56c73-435">Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.</span><span class="sxs-lookup"><span data-stu-id="56c73-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="56c73-436">Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) :</span><span class="sxs-lookup"><span data-stu-id="56c73-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="56c73-437">Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement.</span><span class="sxs-lookup"><span data-stu-id="56c73-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="56c73-438">Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.</span><span class="sxs-lookup"><span data-stu-id="56c73-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="56c73-439">Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).</span><span class="sxs-lookup"><span data-stu-id="56c73-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="56c73-440">L’application se bloque, ne démarre pas ou s’exécute normalement</span><span class="sxs-lookup"><span data-stu-id="56c73-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="56c73-441">Quand une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage ou s’exécute normalement, consultez [fichiers de vidage en mode utilisateur : choix de l’outil le mieux](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) adapté pour sélectionner un outil approprié pour produire le vidage.</span><span class="sxs-lookup"><span data-stu-id="56c73-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="56c73-442">Analyser le fichier dump</span><span class="sxs-lookup"><span data-stu-id="56c73-442">Analyze the dump</span></span>

<span data-ttu-id="56c73-443">Un fichier dump peut être analysé à l’aide de plusieurs approches.</span><span class="sxs-lookup"><span data-stu-id="56c73-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="56c73-444">Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).</span><span class="sxs-lookup"><span data-stu-id="56c73-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="56c73-445">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="56c73-445">Additional resources</span></span>

* <span data-ttu-id="56c73-446">[Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclut la configuration de HTTPS et la prise en charge de SNI)</span><span class="sxs-lookup"><span data-stu-id="56c73-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="56c73-447">Une application ASP.NET Core peut être hébergée sur Windows en tant que [service Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sans utiliser IIS.</span><span class="sxs-lookup"><span data-stu-id="56c73-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="56c73-448">Lorsqu’elle est hébergée en tant que service Windows, l’application se lance automatiquement après le redémarrage du serveur.</span><span class="sxs-lookup"><span data-stu-id="56c73-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="56c73-449">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="56c73-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="56c73-450">Prérequis</span><span class="sxs-lookup"><span data-stu-id="56c73-450">Prerequisites</span></span>

* [<span data-ttu-id="56c73-451">Kit de développement logiciel (SDK) ASP.NET Core 2.1 ou plus</span><span class="sxs-lookup"><span data-stu-id="56c73-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="56c73-452">PowerShell 6,2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="56c73-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="56c73-453">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="56c73-453">App configuration</span></span>

<span data-ttu-id="56c73-454">L’application nécessite des références de package pour [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) et [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="56c73-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="56c73-455">Pour effectuer des tests et un débogage lors de l’exécution en dehors d’un service, ajoutez un code pour déterminer si l’application s’exécute comme un service ou comme une application console.</span><span class="sxs-lookup"><span data-stu-id="56c73-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="56c73-456">Vérifiez si le débogueur est attaché ou si un switch `--console` est présent.</span><span class="sxs-lookup"><span data-stu-id="56c73-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="56c73-457">Si l’une de ces deux conditions est remplie (l’application n’est pas exécutée en tant que service), appelez <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="56c73-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="56c73-458">Si les conditions ne sont pas remplies (l’application est exécutée en tant que service) :</span><span class="sxs-lookup"><span data-stu-id="56c73-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="56c73-459">Appelez <xref:System.IO.Directory.SetCurrentDirectory*> et utilisez un chemin vers l’emplacement publié de l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="56c73-460">N’appelez pas <xref:System.IO.Directory.GetCurrentDirectory*> pour obtenir le chemin d’accès car une application Windows Service retourne le dossier *C:\\WINDOWS\\system32* lorsque <xref:System.IO.Directory.GetCurrentDirectory*> est appelée.</span><span class="sxs-lookup"><span data-stu-id="56c73-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="56c73-461">Pour plus d’informations, consultez la section [Répertoire actif et racine du contenu](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="56c73-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="56c73-462">Cette étape est effectuée avant la configuration de l’application dans `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="56c73-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="56c73-463">Appelez <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pour exécuter l’application en tant que service.</span><span class="sxs-lookup"><span data-stu-id="56c73-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="56c73-464">Étant donné que le [fournisseur de configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider) nécessite des paires nom/valeur pour les arguments de ligne de commande, le switch `--console` est supprimé des arguments avant que <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ne les reçoive.</span><span class="sxs-lookup"><span data-stu-id="56c73-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="56c73-465">Pour consigner des informations dans le journal des événements Windows, ajoutez le fournisseur EventLog à <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="56c73-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="56c73-466">Définissez le niveau de journalisation à l’aide de clé `Logging:LogLevel:Default` dans le fichier *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="56c73-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="56c73-467">Dans l’exemple suivant, qui provient de l’exemple d’application, l’élément `RunAsCustomService` est appelé à la place de l’élément <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> afin de gérer les événements de durée de vie au sein de l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="56c73-468">Pour plus d’informations, consultez la section [Gérer les événements de démarrage et d’arrêt](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="56c73-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="56c73-469">Type de déploiement</span><span class="sxs-lookup"><span data-stu-id="56c73-469">Deployment type</span></span>

<span data-ttu-id="56c73-470">Pour des informations et des conseils sur les scénarios de déploiement, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="56c73-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="56c73-471">Kit SDK</span><span class="sxs-lookup"><span data-stu-id="56c73-471">SDK</span></span>

<span data-ttu-id="56c73-472">Pour un service basé sur une application Web qui utilise les Razor pages ou les infrastructures MVC, spécifiez le kit de développement logiciel (SDK) Web dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="56c73-472">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="56c73-473">Si le service exécute uniquement des tâches en arrière-plan (par exemple, les [services hébergés](xref:fundamentals/host/hosted-services)), spécifiez le kit de développement logiciel (SDK) Worker dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="56c73-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="56c73-474">Déploiement dépendant du framework</span><span class="sxs-lookup"><span data-stu-id="56c73-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="56c73-475">Un déploiement dépendant du framework s’appuie sur la présence d’une version partagée à l’échelle du système de .NET Core sur le système cible.</span><span class="sxs-lookup"><span data-stu-id="56c73-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="56c73-476">Lorsque vous effectuez le scénario de déploiement dépendant du framework en suivant les conseils du présent article, le Kit de développement logiciel (SDK) produit un fichier exécutable (*.exe*), appelé *fichier exécutable dépendant du framework*.</span><span class="sxs-lookup"><span data-stu-id="56c73-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="56c73-477">L' [identificateur Windows Runtime (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) contient la version cible de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="56c73-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="56c73-478">Dans l’exemple suivant, le RID est défini sur `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="56c73-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="56c73-479">La propriété `<SelfContained>` est définie sur `false`.</span><span class="sxs-lookup"><span data-stu-id="56c73-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="56c73-480">Ces propriétés demandent au Kit de développement logiciel (SDK) de générer un fichier exécutable (*.exe*) pour Windows et une application qui dépend du framework .NET Core partagé.</span><span class="sxs-lookup"><span data-stu-id="56c73-480">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="56c73-481">La propriété `<UseAppHost>` est définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="56c73-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="56c73-482">Cette propriété fournit au service un chemin d’activation (un fichier exécutable *.exe*) pour un déploiement dépendant du framework (FDD).</span><span class="sxs-lookup"><span data-stu-id="56c73-482">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="56c73-483">Un fichier *web.config*, qui est normalement produit lors de la publication d’une application ASP.NET Core, n’est pas nécessaire pour une application de Windows Services.</span><span class="sxs-lookup"><span data-stu-id="56c73-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="56c73-484">Pour désactiver la création d’un fichier *web.config*, ajoutez la propriété `<IsTransformWebConfigDisabled>` définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="56c73-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="56c73-485">Déploiement autonome</span><span class="sxs-lookup"><span data-stu-id="56c73-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="56c73-486">Un déploiement autonome ne s’appuie sur la présence d’aucune infrastructure partagée sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="56c73-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="56c73-487">Le runtime et les dépendances de l’application sont déployés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="56c73-488">Un [identificateur de runtime (RID)](/dotnet/core/rid-catalog) Windows est inclus dans l’élément `<PropertyGroup>` qui contient la version cible de .NET Framework :</span><span class="sxs-lookup"><span data-stu-id="56c73-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="56c73-489">Pour publier pour plusieurs RID :</span><span class="sxs-lookup"><span data-stu-id="56c73-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="56c73-490">Fournissez les RID dans une liste séparée par des points-virgules.</span><span class="sxs-lookup"><span data-stu-id="56c73-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="56c73-491">Utilisez le nom de la propriété [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (pluriel).</span><span class="sxs-lookup"><span data-stu-id="56c73-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="56c73-492">Pour plus d’informations, consultez le [Catalogue RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="56c73-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="56c73-493">Une propriété `<SelfContained>` est définie sur `true` :</span><span class="sxs-lookup"><span data-stu-id="56c73-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="56c73-494">Compte d’utilisateur du service</span><span class="sxs-lookup"><span data-stu-id="56c73-494">Service user account</span></span>

<span data-ttu-id="56c73-495">Pour créer un compte d’utilisateur du service, utilisez la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) depuis un interpréteur de commandes d’administration PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="56c73-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="56c73-496">Dans la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) ou plus :</span><span class="sxs-lookup"><span data-stu-id="56c73-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="56c73-497">Dans un système d’exploitation Windows antérieur à la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) :</span><span class="sxs-lookup"><span data-stu-id="56c73-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="56c73-498">Fournissez un [mot de passe fort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) à l’invite.</span><span class="sxs-lookup"><span data-stu-id="56c73-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="56c73-499">Le compte n’expire pas, sauf si le paramètre `-AccountExpires` est fourni à la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) avec un <xref:System.DateTime> d’expiration.</span><span class="sxs-lookup"><span data-stu-id="56c73-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="56c73-500">Pour plus d’informations, voir [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) et [Comptes d’utilisateurs de service](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="56c73-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="56c73-501">Une approche alternative à la gestion des utilisateurs lors de l’utilisation d’Active Directory consiste à utiliser des Comptes de service administrés.</span><span class="sxs-lookup"><span data-stu-id="56c73-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="56c73-502">Pour plus d’informations, consultez [Vue d’ensemble des comptes de service administrés de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="56c73-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="56c73-503">Droits d’ouverture de session en tant que service</span><span class="sxs-lookup"><span data-stu-id="56c73-503">Log on as a service rights</span></span>

<span data-ttu-id="56c73-504">Pour établir des droits *d’ouverture de session en tant que service* pour un compte d’utilisateur de service, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="56c73-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="56c73-505">Ouvrez l’éditeur de stratégie de sécurité locale en exécutant le fichier *secpool.msc*.</span><span class="sxs-lookup"><span data-stu-id="56c73-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="56c73-506">Développez le nœud **Stratégies locales** et sélectionnez **Attribution des droits utilisateur**.</span><span class="sxs-lookup"><span data-stu-id="56c73-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="56c73-507">Ouvrez la stratégie **Ouvrir une session en tant que service**.</span><span class="sxs-lookup"><span data-stu-id="56c73-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="56c73-508">Sélectionnez **Ajouter un utilisateur ou un groupe**.</span><span class="sxs-lookup"><span data-stu-id="56c73-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="56c73-509">Fournissez le nom d’objet (compte d’utilisateur) avec l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="56c73-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="56c73-510">Tapez le compte d’utilisateur (`{DOMAIN OR COMPUTER NAME\USER}`) dans le champ du nom d’objet et sélectionnez **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="56c73-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="56c73-511">Sélectionnez **Avancé**.</span><span class="sxs-lookup"><span data-stu-id="56c73-511">Select **Advanced**.</span></span> <span data-ttu-id="56c73-512">Sélectionnez **Rechercher maintenant**.</span><span class="sxs-lookup"><span data-stu-id="56c73-512">Select **Find Now**.</span></span> <span data-ttu-id="56c73-513">Sélectionnez le compte d’utilisateur dans la liste.</span><span class="sxs-lookup"><span data-stu-id="56c73-513">Select the user account from the list.</span></span> <span data-ttu-id="56c73-514">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="56c73-514">Select **OK**.</span></span> <span data-ttu-id="56c73-515">Cliquez à nouveau sur **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="56c73-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="56c73-516">Sélectionnez **OK** ou **Appliquer** pour accepter les modifications.</span><span class="sxs-lookup"><span data-stu-id="56c73-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="56c73-517">Créer et gérer le service Windows</span><span class="sxs-lookup"><span data-stu-id="56c73-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="56c73-518">Créer un service</span><span class="sxs-lookup"><span data-stu-id="56c73-518">Create a service</span></span>

<span data-ttu-id="56c73-519">Utilisez les commandes PowerShell pour enregistrer un service.</span><span class="sxs-lookup"><span data-stu-id="56c73-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="56c73-520">À partir d’un interpréteur de commandes d’administration PowerShell 6, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="56c73-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="56c73-521">`{EXE PATH}`: Chemin d’accès au dossier de l’application sur l’hôte (par exemple, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-521">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="56c73-522">N’incluez pas le fichier exécutable de l’application dans le chemin.</span><span class="sxs-lookup"><span data-stu-id="56c73-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="56c73-523">Aucune barre oblique de fin n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="56c73-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="56c73-524">`{DOMAIN OR COMPUTER NAME\USER}`: Compte d’utilisateur du service (par exemple, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-524">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="56c73-525">`{SERVICE NAME}`: Nom du service (par exemple, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-525">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="56c73-526">`{EXE FILE PATH}`: Le chemin d’accès de l’exécutable de l’application (par exemple, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-526">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="56c73-527">Incluez le nom de fichier de l’exécutable avec l’extension.</span><span class="sxs-lookup"><span data-stu-id="56c73-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="56c73-528">`{DESCRIPTION}`: Description du service (par exemple, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-528">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="56c73-529">`{DISPLAY NAME}`: Nom d’affichage du service (par exemple, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="56c73-529">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="56c73-530">Démarrer un service</span><span class="sxs-lookup"><span data-stu-id="56c73-530">Start a service</span></span>

<span data-ttu-id="56c73-531">Démarrez un service avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="56c73-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="56c73-532">La commande prend quelques secondes pour démarrer le service.</span><span class="sxs-lookup"><span data-stu-id="56c73-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="56c73-533">Déterminer l’état d’un service</span><span class="sxs-lookup"><span data-stu-id="56c73-533">Determine a service's status</span></span>

<span data-ttu-id="56c73-534">Pour vérifier l’état d’un service, utilisez la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="56c73-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="56c73-535">L’état est signalé comme étant l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="56c73-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="56c73-536">Arrêter un service</span><span class="sxs-lookup"><span data-stu-id="56c73-536">Stop a service</span></span>

<span data-ttu-id="56c73-537">Arrêtez un service avec la commande Powershell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="56c73-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="56c73-538">Supprimer un service</span><span class="sxs-lookup"><span data-stu-id="56c73-538">Remove a service</span></span>

<span data-ttu-id="56c73-539">Après un court délai pour arrêter un service, supprimez-le avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="56c73-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="56c73-540">Gérer les événements de démarrage et d’arrêt</span><span class="sxs-lookup"><span data-stu-id="56c73-540">Handle starting and stopping events</span></span>

<span data-ttu-id="56c73-541">Pour gérer les événements <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> et <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="56c73-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="56c73-542">Créez une classe qui dérive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> à l’aide des méthodes `OnStarting`, `OnStarted` et `OnStopping` :</span><span class="sxs-lookup"><span data-stu-id="56c73-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="56c73-543">Créez une méthode d’extension pour <xref:Microsoft.AspNetCore.Hosting.IWebHost> qui transmet `CustomWebHostService` à <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="56c73-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="56c73-544">Dans `Program.Main`, appelez la méthode d’extension `RunAsCustomService` au lieu de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="56c73-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="56c73-545">Pour afficher l’emplacement de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> dans `Program.Main`, reportez-vous à l’exemple de code indiqué dans la section [Type de déploiement](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="56c73-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="56c73-546">Scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="56c73-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="56c73-547">Les services qui interagissent avec les requêtes provenant d’Internet ou d’un réseau d’entreprise et qui se trouvent derrière un proxy ou équilibreur de charge peuvent nécessiter une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="56c73-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="56c73-548">Pour plus d'informations, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="56c73-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="56c73-549">Configuration des points de terminaison</span><span class="sxs-lookup"><span data-stu-id="56c73-549">Configure endpoints</span></span>

<span data-ttu-id="56c73-550">Par défaut, ASP.NET Core est lié à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="56c73-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="56c73-551">Configurez l’URL et le port en définissant la `ASPNETCORE_URLS` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="56c73-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="56c73-552">Pour obtenir d’autres approches de configuration des ports et des URL, consultez l’article approprié sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="56c73-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="56c73-553">L’aide précédente couvre la prise en charge des points de terminaison HTTPs.</span><span class="sxs-lookup"><span data-stu-id="56c73-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="56c73-554">Par exemple, configurez l’application pour HTTPs lorsque l’authentification est utilisée avec un service Windows.</span><span class="sxs-lookup"><span data-stu-id="56c73-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="56c73-555">L’utilisation du certificat de développement HTTPS ASP.NET Core pour sécuriser un point de terminaison de service n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="56c73-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="56c73-556">Répertoire actif et racine du contenu</span><span class="sxs-lookup"><span data-stu-id="56c73-556">Current directory and content root</span></span>

<span data-ttu-id="56c73-557">Le répertoire de travail actuel retourné en appelant <xref:System.IO.Directory.GetCurrentDirectory*> pour un service Windows est le dossier \* \\ \\ system32 de C : Windows\* .</span><span class="sxs-lookup"><span data-stu-id="56c73-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="56c73-558">Le dossier *system32* n’est pas un emplacement approprié pour stocker les fichiers d’un service (tels que les fichiers de paramètres).</span><span class="sxs-lookup"><span data-stu-id="56c73-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="56c73-559">Utilisez une des approches suivantes pour gérer les ressources ainsi que les fichiers de paramètres d’un service, et y accéder.</span><span class="sxs-lookup"><span data-stu-id="56c73-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="56c73-560">Définir le dossier de l’application comme chemin d’accès racine du contenu</span><span class="sxs-lookup"><span data-stu-id="56c73-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="56c73-561">La chaîne <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> correspond au même chemin que celui fourni à l’argument `binPath` lorsqu’un service est créé.</span><span class="sxs-lookup"><span data-stu-id="56c73-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="56c73-562">Au lieu d’appeler `GetCurrentDirectory` pour créer des chemins d’accès aux fichiers de paramètres, appelez <xref:System.IO.Directory.SetCurrentDirectory*> avec le chemin d’accès à la [racine de contenu](xref:fundamentals/index#content-root)de l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="56c73-563">Dans `Program.Main`, définissez le chemin d’accès au dossier du fichier exécutable du service ainsi que le chemin d’accès pour établir la racine du contenu de l’application :</span><span class="sxs-lookup"><span data-stu-id="56c73-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="56c73-564">Stocker les fichiers d’un service dans un emplacement approprié sur le disque</span><span class="sxs-lookup"><span data-stu-id="56c73-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="56c73-565">Spécifiez un chemin absolu avec <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>, si vous utilisez <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, vers le dossier contenant les fichiers.</span><span class="sxs-lookup"><span data-stu-id="56c73-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="56c73-566">Dépanner</span><span class="sxs-lookup"><span data-stu-id="56c73-566">Troubleshoot</span></span>

<span data-ttu-id="56c73-567">Pour dépanner une application de service Windows, consultez <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="56c73-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="56c73-568">Erreurs courantes</span><span class="sxs-lookup"><span data-stu-id="56c73-568">Common errors</span></span>

* <span data-ttu-id="56c73-569">Une version ancienne ou préliminaire de PowerShell est en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="56c73-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="56c73-570">Le service inscrit n’utilise pas la sortie **publiée** de l’application à partir de la commande [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="56c73-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="56c73-571">La sortie de la commande [dotnet Build](/dotnet/core/tools/dotnet-build) n’est pas prise en charge pour le déploiement d’applications.</span><span class="sxs-lookup"><span data-stu-id="56c73-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="56c73-572">Les ressources publiées se trouvent dans l’un des dossiers suivants, selon le type de déploiement :</span><span class="sxs-lookup"><span data-stu-id="56c73-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="56c73-573">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="56c73-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="56c73-574">*bin/Release/{Target Framework}/{Runtime identificateur}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="56c73-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="56c73-575">Le service n’est pas à l’État en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="56c73-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="56c73-576">Les chemins d’accès aux ressources que l’application utilise (par exemple, les certificats) sont incorrects.</span><span class="sxs-lookup"><span data-stu-id="56c73-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="56c73-577">Le chemin d’accès de base d’un service Windows est *c : \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="56c73-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="56c73-578">L’utilisateur ne dispose pas de droits *d’ouverture de session en tant que service* .</span><span class="sxs-lookup"><span data-stu-id="56c73-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="56c73-579">Le mot de passe de l’utilisateur a expiré ou a été transmis de manière incorrecte lors de l’exécution de la `New-Service` commande PowerShell.</span><span class="sxs-lookup"><span data-stu-id="56c73-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="56c73-580">L’application requiert l’authentification ASP.NET Core, mais elle n’est pas configurée pour les connexions sécurisées (HTTPs).</span><span class="sxs-lookup"><span data-stu-id="56c73-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="56c73-581">Le port de l’URL de requête est incorrect ou n’est pas correctement configuré dans l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="56c73-582">Journaux des événements système et des applications</span><span class="sxs-lookup"><span data-stu-id="56c73-582">System and Application Event Logs</span></span>

<span data-ttu-id="56c73-583">Accédez aux journaux des événements système et des applications :</span><span class="sxs-lookup"><span data-stu-id="56c73-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="56c73-584">Ouvrez le menu Démarrer, recherchez *Observateur d’événements*, puis sélectionnez l’application **Observateur d’événements** .</span><span class="sxs-lookup"><span data-stu-id="56c73-584">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="56c73-585">Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.</span><span class="sxs-lookup"><span data-stu-id="56c73-585">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="56c73-586">Sélectionnez **système** pour ouvrir le journal des événements système.</span><span class="sxs-lookup"><span data-stu-id="56c73-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="56c73-587">Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="56c73-588">Recherchez les erreurs liées à l’application défectueuse.</span><span class="sxs-lookup"><span data-stu-id="56c73-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="56c73-589">Exécuter l’application depuis une invite de commandes</span><span class="sxs-lookup"><span data-stu-id="56c73-589">Run the app at a command prompt</span></span>

<span data-ttu-id="56c73-590">De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans les journaux des événements.</span><span class="sxs-lookup"><span data-stu-id="56c73-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="56c73-591">Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="56c73-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="56c73-592">Pour consigner des détails supplémentaires à partir de l’application, diminuez le [niveau de journalisation](xref:fundamentals/logging/index#log-level) ou exécutez l’application dans l' [environnement de développement](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="56c73-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="56c73-593">Effacer les caches de package</span><span class="sxs-lookup"><span data-stu-id="56c73-593">Clear package caches</span></span>

<span data-ttu-id="56c73-594">Une application fonctionnelle peut échouer immédiatement après la mise à niveau de la kit SDK .NET Core sur l’ordinateur de développement ou la modification des versions de package dans l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="56c73-595">Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures.</span><span class="sxs-lookup"><span data-stu-id="56c73-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="56c73-596">Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :</span><span class="sxs-lookup"><span data-stu-id="56c73-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="56c73-597">Supprimez les dossiers *bin* et *obj*.</span><span class="sxs-lookup"><span data-stu-id="56c73-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="56c73-598">Effacez les caches de package en exécutant [dotnet NuGet LOCALS tout--Clear](/dotnet/core/tools/dotnet-nuget-locals) dans une interface de commande.</span><span class="sxs-lookup"><span data-stu-id="56c73-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="56c73-599">L’effacement des caches de package peut également être effectué à l’aide de l’outil [nuget.exe](https://www.nuget.org/downloads) et en exécutant la commande `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="56c73-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="56c73-600">*NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="56c73-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="56c73-601">Restaurez et regénérez le projet.</span><span class="sxs-lookup"><span data-stu-id="56c73-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="56c73-602">Supprimez tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.</span><span class="sxs-lookup"><span data-stu-id="56c73-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="56c73-603">Application lente ou bloquée</span><span class="sxs-lookup"><span data-stu-id="56c73-603">Slow or hanging app</span></span>

<span data-ttu-id="56c73-604">Un *vidage sur incident* est un instantané de la mémoire du système et peut aider à déterminer la cause d’un incident d’application, d’un échec de démarrage ou d’une application lente.</span><span class="sxs-lookup"><span data-stu-id="56c73-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="56c73-605">L’application cesse de fonctionner ou rencontre une exception</span><span class="sxs-lookup"><span data-stu-id="56c73-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="56c73-606">Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :</span><span class="sxs-lookup"><span data-stu-id="56c73-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="56c73-607">Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="56c73-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="56c73-608">Exécutez le [script PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) avec le nom de l’exécutable de l’application :</span><span class="sxs-lookup"><span data-stu-id="56c73-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="56c73-609">Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.</span><span class="sxs-lookup"><span data-stu-id="56c73-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="56c73-610">Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) :</span><span class="sxs-lookup"><span data-stu-id="56c73-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="56c73-611">Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement.</span><span class="sxs-lookup"><span data-stu-id="56c73-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="56c73-612">Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.</span><span class="sxs-lookup"><span data-stu-id="56c73-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="56c73-613">Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).</span><span class="sxs-lookup"><span data-stu-id="56c73-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="56c73-614">L’application se bloque, ne démarre pas ou s’exécute normalement</span><span class="sxs-lookup"><span data-stu-id="56c73-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="56c73-615">Quand une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage ou s’exécute normalement, consultez [fichiers de vidage en mode utilisateur : choix de l’outil le mieux](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) adapté pour sélectionner un outil approprié pour produire le vidage.</span><span class="sxs-lookup"><span data-stu-id="56c73-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="56c73-616">Analyser le fichier dump</span><span class="sxs-lookup"><span data-stu-id="56c73-616">Analyze the dump</span></span>

<span data-ttu-id="56c73-617">Un fichier dump peut être analysé à l’aide de plusieurs approches.</span><span class="sxs-lookup"><span data-stu-id="56c73-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="56c73-618">Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).</span><span class="sxs-lookup"><span data-stu-id="56c73-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="56c73-619">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="56c73-619">Additional resources</span></span>

* <span data-ttu-id="56c73-620">[Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclut la configuration de HTTPS et la prise en charge de SNI)</span><span class="sxs-lookup"><span data-stu-id="56c73-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

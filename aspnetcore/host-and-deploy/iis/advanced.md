---
title: Configuration avancée
author: rick-anderson
description: Configuration avancée avec le module ASP.NET Core et Internet Information Services (IIS).
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: de3abb7b5f89e3b84da38a8eabb183f9551cc1f9
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588917"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a><span data-ttu-id="66d6c-103">Configuration avancée du module ASP.NET Core et des services IIS</span><span class="sxs-lookup"><span data-stu-id="66d6c-103">Advanced configuration of the ASP.NET Core Module and IIS</span></span>

<span data-ttu-id="66d6c-104">Cet article traite des options de configuration avancées et des scénarios pour le module ASP.NET Core et IIS.</span><span class="sxs-lookup"><span data-stu-id="66d6c-104">This article covers advanced configuration options and scenarios for the ASP.NET Core Module and IIS.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="66d6c-105">Modifier la taille de pile</span><span class="sxs-lookup"><span data-stu-id="66d6c-105">Modify the stack size</span></span>

<span data-ttu-id="66d6c-106">*S’applique uniquement lors de l’utilisation du modèle d’hébergement in-process.*</span><span class="sxs-lookup"><span data-stu-id="66d6c-106">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="66d6c-107">Configurez la taille de la pile managée en utilisant le `stackSize` paramètre en octets dans le `web.config` fichier.</span><span class="sxs-lookup"><span data-stu-id="66d6c-107">Configure the managed stack size using the `stackSize` setting in bytes in the `web.config` file.</span></span> <span data-ttu-id="66d6c-108">La taille par défaut est de 1 048 576 octets (1 Mo).</span><span class="sxs-lookup"><span data-stu-id="66d6c-108">The default size is 1,048,576 bytes (1 MB).</span></span> <span data-ttu-id="66d6c-109">L’exemple suivant modifie la taille de la pile à 2 Mo (2 097 152 octets) :</span><span class="sxs-lookup"><span data-stu-id="66d6c-109">The following example changes the stack size to 2 MB (2,097,152 bytes):</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="66d6c-110">La configuration du proxy utilise le protocole HTTP et un jeton d’appariement</span><span class="sxs-lookup"><span data-stu-id="66d6c-110">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="66d6c-111">*S’applique uniquement à l’hébergement out-of-process.*</span><span class="sxs-lookup"><span data-stu-id="66d6c-111">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="66d6c-112">Le proxy créé entre le module ASP.NET Core et Kestrel utilise le protocole HTTP.</span><span class="sxs-lookup"><span data-stu-id="66d6c-112">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="66d6c-113">Il n’existe aucun risque d’écoute clandestine du trafic entre le module et Kestrel à partir d’un emplacement sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="66d6c-113">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="66d6c-114">Un jeton d’appariement est utilisé pour garantir que les demandes reçues par Kestrel ont été traitées par IIS et ne proviennent pas d’une autre source.</span><span class="sxs-lookup"><span data-stu-id="66d6c-114">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="66d6c-115">Le jeton d’appariement est créé et défini dans une variable d’environnement (`ASPNETCORE_TOKEN`) par le module.</span><span class="sxs-lookup"><span data-stu-id="66d6c-115">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="66d6c-116">Le jeton d’appariement est également défini dans un en-tête (`MS-ASPNETCORE-TOKEN`) sur toutes les demandes traitées.</span><span class="sxs-lookup"><span data-stu-id="66d6c-116">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="66d6c-117">L'intergiciel IIS vérifie chaque demande qu’il reçoit pour confirmer que la valeur d’en-tête du jeton d'appariement correspond à la valeur de la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="66d6c-117">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="66d6c-118">Si les valeurs de jeton ne correspondent pas, la demande est connectée et rejetée.</span><span class="sxs-lookup"><span data-stu-id="66d6c-118">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="66d6c-119">La variable d’environnement du jeton d'appariement et le trafic entre le module et Kestrel ne sont pas accessibles à partir d’un emplacement sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="66d6c-119">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="66d6c-120">Sans connaître la valeur du jeton d'appariement, une personne malveillante ne peut pas soumettre des demandes qui contournent la vérification de l’intergiciel IIS.</span><span class="sxs-lookup"><span data-stu-id="66d6c-120">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="66d6c-121">Module ASP.NET Core avec une configuration partagée IIS</span><span class="sxs-lookup"><span data-stu-id="66d6c-121">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="66d6c-122">Le programme d’installation du module ASP.NET Core s’exécute avec les privilèges du `TrustedInstaller` compte.</span><span class="sxs-lookup"><span data-stu-id="66d6c-122">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="66d6c-123">Étant donné que le compte système local ne dispose pas de l’autorisation de modification pour le chemin d’accès de partage utilisé par la configuration partagée IIS, le programme d’installation lève une erreur d’accès refusé lors de la tentative de configuration des paramètres du module dans le `applicationHost.config` fichier sur le partage.</span><span class="sxs-lookup"><span data-stu-id="66d6c-123">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="66d6c-124">Quand une configuration partagée IIS est utilisée sur la même machine que l’installation IIS, il convient d’exécuter le programme d’installation du bundle d’hébergement ASP.NET Core avec le paramètre `OPT_NO_SHARED_CONFIG_CHECK` défini sur `1` :</span><span class="sxs-lookup"><span data-stu-id="66d6c-124">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="66d6c-125">Quand le chemin de la configuration partagée ne se trouve pas sur la même machine que l’installation IIS, effectuez ces étapes :</span><span class="sxs-lookup"><span data-stu-id="66d6c-125">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="66d6c-126">Désactivez la configuration partagée IIS.</span><span class="sxs-lookup"><span data-stu-id="66d6c-126">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="66d6c-127">Exécutez le programme d’installation.</span><span class="sxs-lookup"><span data-stu-id="66d6c-127">Run the installer.</span></span>
1. <span data-ttu-id="66d6c-128">Exportez le fichier mis à jour `applicationHost.config` vers le partage de fichiers.</span><span class="sxs-lookup"><span data-stu-id="66d6c-128">Export the updated `applicationHost.config` file to the file share.</span></span>
1. <span data-ttu-id="66d6c-129">Réactivez la configuration partagée IIS.</span><span class="sxs-lookup"><span data-stu-id="66d6c-129">Re-enable the IIS Shared Configuration.</span></span>

## <a name="data-protection"></a><span data-ttu-id="66d6c-130">Protection des données</span><span class="sxs-lookup"><span data-stu-id="66d6c-130">Data protection</span></span>

<span data-ttu-id="66d6c-131">La [pile de protection des données ASP.NET Core](xref:security/data-protection/introduction) est utilisée par plusieurs [intergiciels (middlewares)](xref:fundamentals/middleware/index) ASP.NET Core, y compris l’intergiciel utilisé dans l’authentification.</span><span class="sxs-lookup"><span data-stu-id="66d6c-131">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="66d6c-132">Même si les API de protection des données ne sont pas appelées par le code de l’utilisateur, la protection des données doit être configurée avec un script de déploiement ou dans un code utilisateur pour créer un [magasin de clés](xref:security/data-protection/implementation/key-management) de chiffrement persistantes.</span><span class="sxs-lookup"><span data-stu-id="66d6c-132">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="66d6c-133">Si la protection des données n’est pas configurée, les clés sont conservées en mémoire et ignorées au redémarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="66d6c-133">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="66d6c-134">Si l’anneau de clé de protection des données est stocké en mémoire au redémarrage de l’application :</span><span class="sxs-lookup"><span data-stu-id="66d6c-134">If the Data Protection key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="66d6c-135">Tous les cookie jetons d’authentification de base sont invalidés.</span><span class="sxs-lookup"><span data-stu-id="66d6c-135">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="66d6c-136">Les utilisateurs doivent se reconnecter pour envoyer leur prochaine demande.</span><span class="sxs-lookup"><span data-stu-id="66d6c-136">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="66d6c-137">toutes les données protégées par le Key Ring ne peuvent plus être déchiffrées.</span><span class="sxs-lookup"><span data-stu-id="66d6c-137">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="66d6c-138">Cela peut inclure des [jetons CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) et des ASP.NET Core les de la [MVC cookie ](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="66d6c-138">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="66d6c-139">Pour configurer la protection des données sous IIS afin de rendre persistante le Key Ring, adoptez **l’une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="66d6c-139">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="66d6c-140">**Créer des clés de registre de protection des données**</span><span class="sxs-lookup"><span data-stu-id="66d6c-140">**Create Data Protection Registry keys**</span></span>

  <span data-ttu-id="66d6c-141">Les clés de protection des données utilisées par les applications ASP.NET Core sont stockées dans le registre à l’extérieur des applications.</span><span class="sxs-lookup"><span data-stu-id="66d6c-141">Data Protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="66d6c-142">Pour conserver les clés pour une application donnée, créez des clés de Registre pour le pool d’applications.</span><span class="sxs-lookup"><span data-stu-id="66d6c-142">To persist the keys for a given app, create Registry keys for the app pool.</span></span>

  <span data-ttu-id="66d6c-143">Pour les installations IIS autonomes hors d’une batterie de serveurs, le [script PowerShell Provision-AutoGenKeys.ps1 de protection des données](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1) peut être utilisé pour chaque pool d’applications utilisé avec une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="66d6c-143">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="66d6c-144">Ce script crée une clé de Registre dans le Registre HKLM qui est accessible uniquement au compte du processus de travail du pool d’applications de l’application.</span><span class="sxs-lookup"><span data-stu-id="66d6c-144">This script creates a Registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="66d6c-145">Les clés sont chiffrées au repos à l’aide de DPAPI avec une clé à l’échelle de la machine.</span><span class="sxs-lookup"><span data-stu-id="66d6c-145">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="66d6c-146">Dans les scénarios de batterie de serveurs Web, une application peut être configurée pour utiliser un chemin UNC pour stocker son anneau de clé de protection des données.</span><span class="sxs-lookup"><span data-stu-id="66d6c-146">In web farm scenarios, an app can be configured to use a UNC path to store its Data Protection key ring.</span></span> <span data-ttu-id="66d6c-147">Par défaut, les clés ne sont pas chiffrées.</span><span class="sxs-lookup"><span data-stu-id="66d6c-147">By default, the keys aren't encrypted.</span></span> <span data-ttu-id="66d6c-148">Assurez-vous que les autorisations de fichiers pour le partage réseau sont limitées au compte Windows sous lequel l’application s’exécute.</span><span class="sxs-lookup"><span data-stu-id="66d6c-148">Ensure that the file permissions for the network share are limited to the Windows account that the app runs under.</span></span> <span data-ttu-id="66d6c-149">Un certificat X509 peut être utilisé pour protéger les clés au repos.</span><span class="sxs-lookup"><span data-stu-id="66d6c-149">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="66d6c-150">Prenons l’exemple d’un mécanisme permettant aux utilisateurs de télécharger des certificats.</span><span class="sxs-lookup"><span data-stu-id="66d6c-150">Consider a mechanism to allow users to upload certificates.</span></span> <span data-ttu-id="66d6c-151">Placez les certificats dans le magasin de certificats approuvés de l’utilisateur et vérifiez qu’ils sont disponibles sur toutes les machines où s’exécute l’application de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="66d6c-151">Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="66d6c-152">Pour plus d’informations, consultez <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="66d6c-152">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>

* <span data-ttu-id="66d6c-153">**Configurer le pool d’applications IIS pour charger le profil utilisateur**</span><span class="sxs-lookup"><span data-stu-id="66d6c-153">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="66d6c-154">Ce paramètre se trouve dans la section **Modèle de processus** sous les **Paramètres avancés** du pool d’applications.</span><span class="sxs-lookup"><span data-stu-id="66d6c-154">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="66d6c-155">Affectez la valeur `True` à **Charger le profil utilisateur**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-155">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="66d6c-156">Lorsqu’elle est définie sur `True`, les clés sont stockées dans le répertoire du profil utilisateur, protégées à l’aide de DPAPI avec une clé propre au compte d’utilisateur utilisé pour le pool d’applications.</span><span class="sxs-lookup"><span data-stu-id="66d6c-156">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="66d6c-157">Les clés sont conservées dans le `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` dossier.</span><span class="sxs-lookup"><span data-stu-id="66d6c-157">Keys are persisted to the `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` folder.</span></span>

  <span data-ttu-id="66d6c-158">L' [ `setProfileEnvironment` attribut](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) du pool d’applications doit également être activé.</span><span class="sxs-lookup"><span data-stu-id="66d6c-158">The app pool's [`setProfileEnvironment` attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="66d6c-159">La valeur par défaut de `setProfileEnvironment` est `true`.</span><span class="sxs-lookup"><span data-stu-id="66d6c-159">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="66d6c-160">Dans certains scénarios (par exemple pour le système d’exploitation Windows), `setProfileEnvironment` est défini sur `false`.</span><span class="sxs-lookup"><span data-stu-id="66d6c-160">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="66d6c-161">Si les clés ne sont pas stockées dans le répertoire de profil utilisateur comme prévu :</span><span class="sxs-lookup"><span data-stu-id="66d6c-161">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="66d6c-162">Accédez au dossier `%windir%/system32/inetsrv/config`.</span><span class="sxs-lookup"><span data-stu-id="66d6c-162">Navigate to the `%windir%/system32/inetsrv/config` folder.</span></span>
  1. <span data-ttu-id="66d6c-163">Ouvrez le fichier `applicationHost.config` .</span><span class="sxs-lookup"><span data-stu-id="66d6c-163">Open the `applicationHost.config` file.</span></span>
  1. <span data-ttu-id="66d6c-164">Recherchez l’élément `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.</span><span class="sxs-lookup"><span data-stu-id="66d6c-164">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="66d6c-165">Confirmez que l’attribut `setProfileEnvironment` n’est pas présent, ce qui implique que la valeur par défaut est `true`, ou définissez de manière explicite la valeur de l’attribut sur `true`.</span><span class="sxs-lookup"><span data-stu-id="66d6c-165">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="66d6c-166">**Utiliser le système de fichiers comme magasin de Key Ring**</span><span class="sxs-lookup"><span data-stu-id="66d6c-166">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="66d6c-167">Ajustez le code d’application pour [utiliser le système de fichiers comme magasin de porte-clés](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="66d6c-167">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="66d6c-168">Utilisez un certificat X509 pour protéger le Key Ring et vérifiez qu’il s’agit d’un certificat approuvé.</span><span class="sxs-lookup"><span data-stu-id="66d6c-168">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="66d6c-169">S’il s’agit d’un certificat auto-signé, placez-le dans le magasin Racine approuvée.</span><span class="sxs-lookup"><span data-stu-id="66d6c-169">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="66d6c-170">Quand vous utilisez IIS dans une batterie de serveurs web :</span><span class="sxs-lookup"><span data-stu-id="66d6c-170">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="66d6c-171">Utilisez un partage de fichiers accessible par tous les ordinateurs.</span><span class="sxs-lookup"><span data-stu-id="66d6c-171">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="66d6c-172">Déployez un certificat X509 sur chaque ordinateur.</span><span class="sxs-lookup"><span data-stu-id="66d6c-172">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="66d6c-173">Configurez la [protection des données dans le code](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="66d6c-173">Configure [Data Protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="66d6c-174">**Définir une stratégie au niveau de l’ordinateur pour la protection des données**</span><span class="sxs-lookup"><span data-stu-id="66d6c-174">**Set a machine-wide policy for Data Protection**</span></span>

  <span data-ttu-id="66d6c-175">Le système de protection des données offre une prise en charge limitée de la définition d’une stratégie par défaut au niveau de l' [ordinateur](xref:security/data-protection/configuration/machine-wide-policy) pour toutes les applications qui utilisent les API de protection des données.</span><span class="sxs-lookup"><span data-stu-id="66d6c-175">The Data Protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="66d6c-176">Pour plus d’informations, consultez <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="66d6c-176">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="66d6c-177">Configuration IIS</span><span class="sxs-lookup"><span data-stu-id="66d6c-177">IIS configuration</span></span>

<span data-ttu-id="66d6c-178">**Systèmes d’exploitation Windows Server**</span><span class="sxs-lookup"><span data-stu-id="66d6c-178">**Windows Server operating systems**</span></span>

<span data-ttu-id="66d6c-179">Activez le rôle serveur **Serveur Web (IIS)** et établissez des services de rôle.</span><span class="sxs-lookup"><span data-stu-id="66d6c-179">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="66d6c-180">Utilisez l’Assistant **Ajouter des rôles et des fonctionnalités** par le biais du menu **Gérer** ou du lien dans **Gestionnaire de serveur**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-180">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="66d6c-181">À l’étape **Rôles de serveurs**, cochez la case **Serveur Web (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-181">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![Le rôle Serveur Web IIS est sélectionné à l’étape Sélectionner des rôles de serveurs.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="66d6c-183">Après l’étape **Fonctionnalités**, l’étape **Services de rôle** se charge pour le serveur Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="66d6c-183">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="66d6c-184">Sélectionnez les services de rôle IIS souhaités ou acceptez les services de rôle par défaut fournis.</span><span class="sxs-lookup"><span data-stu-id="66d6c-184">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Les services de rôle par défaut sont sélectionnés à l’étape Sélectionner des services de rôle.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="66d6c-186">**Authentification Windows (facultatif)**</span><span class="sxs-lookup"><span data-stu-id="66d6c-186">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="66d6c-187">Pour activer l’authentification Windows, développez les nœuds suivants : sécurité du **serveur Web**  >  .</span><span class="sxs-lookup"><span data-stu-id="66d6c-187">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="66d6c-188">Sélectionnez la fonctionnalité **Authentification Windows**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-188">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="66d6c-189">Pour plus d’informations, consultez [authentification `<windowsAuthentication>` Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) et [configurer l’authentification Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="66d6c-189">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="66d6c-190">**WebSockets (facultatif)**</span><span class="sxs-lookup"><span data-stu-id="66d6c-190">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="66d6c-191">WebSockets est pris en charge avec ASP.NET Core 1.1 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="66d6c-191">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="66d6c-192">Pour activer les WebSockets, développez les nœuds suivants : développement d’applications de **serveur Web**  >  .</span><span class="sxs-lookup"><span data-stu-id="66d6c-192">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="66d6c-193">Sélectionnez la fonctionnalité **Protocole WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-193">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="66d6c-194">Pour plus d’informations, consultez [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="66d6c-194">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="66d6c-195">Validez l’étape de **Confirmation** pour installer les services et le rôle de serveur web.</span><span class="sxs-lookup"><span data-stu-id="66d6c-195">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="66d6c-196">Un redémarrage du serveur/d’IIS n’est pas nécessaire après l’installation du rôle **Serveur Web (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-196">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="66d6c-197">**Systèmes d’exploitation Windows Desktop**</span><span class="sxs-lookup"><span data-stu-id="66d6c-197">**Windows desktop operating systems**</span></span>

<span data-ttu-id="66d6c-198">Activez la **Console de gestion IIS** et les **Services World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-198">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="66d6c-199">Accédez à **panneau de configuration**  >  **programmes** programmes  >  **et fonctionnalités**  >  **activer ou désactiver des fonctionnalités Windows** (côté gauche de l’écran).</span><span class="sxs-lookup"><span data-stu-id="66d6c-199">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="66d6c-200">Ouvrez le nœud **Internet Information Services**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-200">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="66d6c-201">Ouvrez le nœud **Outils de gestion Web**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-201">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="66d6c-202">Cochez la case **Console de gestion IIS**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-202">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="66d6c-203">Cochez la case **Services World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-203">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="66d6c-204">Acceptez les fonctionnalités par défaut pour **Services World Wide Web** ou personnalisez les fonctionnalités d’IIS.</span><span class="sxs-lookup"><span data-stu-id="66d6c-204">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="66d6c-205">**Authentification Windows (facultatif)**</span><span class="sxs-lookup"><span data-stu-id="66d6c-205">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="66d6c-206">Pour activer l’authentification Windows, développez les nœuds suivants : **World Wide webing services**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-206">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="66d6c-207">Sélectionnez la fonctionnalité **Authentification Windows**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-207">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="66d6c-208">Pour plus d’informations, consultez [authentification `<windowsAuthentication>` Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) et [configurer l’authentification Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="66d6c-208">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="66d6c-209">**WebSockets (facultatif)**</span><span class="sxs-lookup"><span data-stu-id="66d6c-209">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="66d6c-210">WebSockets est pris en charge avec ASP.NET Core 1.1 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="66d6c-210">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="66d6c-211">Pour activer WebSockets, développez les nœuds suivants : fonctionnalités de développement d’applications **World Wide Web services**  >  .</span><span class="sxs-lookup"><span data-stu-id="66d6c-211">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="66d6c-212">Sélectionnez la fonctionnalité **Protocole WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-212">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="66d6c-213">Pour plus d’informations, consultez [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="66d6c-213">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="66d6c-214">Si l’installation d’IIS requiert un redémarrage, redémarrez le système.</span><span class="sxs-lookup"><span data-stu-id="66d6c-214">If the IIS installation requires a restart, restart the system.</span></span>

![Console de gestion IIS et Services World Wide Web sont sélectionnés dans Fonctionnalités de Windows.](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a><span data-ttu-id="66d6c-216">Répertoires virtuels</span><span class="sxs-lookup"><span data-stu-id="66d6c-216">Virtual Directories</span></span>

<span data-ttu-id="66d6c-217">Les [répertoires virtuels IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) ne sont pas pris en charge avec les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="66d6c-217">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="66d6c-218">Une application peut être hébergée en tant que [sous-application](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="66d6c-218">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="66d6c-219">Sous-applications</span><span class="sxs-lookup"><span data-stu-id="66d6c-219">Sub-applications</span></span>

<span data-ttu-id="66d6c-220">Une application ASP.NET Core peut être hébergée en tant que [sous-application IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="66d6c-220">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="66d6c-221">Le chemin d'accès de la sous-application devient partie intégrante de l’URL de l’application racine.</span><span class="sxs-lookup"><span data-stu-id="66d6c-221">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="66d6c-222">Les liens de ressources statiques au sein de la sous-application doivent utiliser la notation tilde-barre oblique (`~/`).</span><span class="sxs-lookup"><span data-stu-id="66d6c-222">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="66d6c-223">La notation tilde-barre oblique déclenche un [Tag Helper](xref:mvc/views/tag-helpers/intro) afin d’ajouter l’élément pathbase de la sous-application au lien relatif rendu.</span><span class="sxs-lookup"><span data-stu-id="66d6c-223">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="66d6c-224">Pour une sous-application dans `/subapp_path`, une image liée à `src="~/image.png"` est restituée sous la forme `src="/subapp_path/image.png"`.</span><span class="sxs-lookup"><span data-stu-id="66d6c-224">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="66d6c-225">Le composant Static File Middleware de l’application racine ne traite la demande de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="66d6c-225">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="66d6c-226">La demande est traitée par le composant Static File Middleware de la sous-application.</span><span class="sxs-lookup"><span data-stu-id="66d6c-226">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="66d6c-227">Si l’attribut `src` d’une ressource statique est défini sur un chemin d’accès absolu (par exemple, `src="/image.png"`), le lien apparaît sans l’élément pathbase de la sous-application.</span><span class="sxs-lookup"><span data-stu-id="66d6c-227">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="66d6c-228">Le composant Static File Middleware de l’application racine tente de traiter la ressource à partir de l’élément [webroot](xref:fundamentals/index#web-root) de l’application racine, ce qui entraîne une erreur *404 - Introuvable*, sauf si la ressource statique est disponible depuis l’application racine.</span><span class="sxs-lookup"><span data-stu-id="66d6c-228">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="66d6c-229">Pour héberger une application ASP.NET Core en tant que sous-application d’une autre application ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="66d6c-229">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="66d6c-230">Établissez un pool d’applications pour la sous-application.</span><span class="sxs-lookup"><span data-stu-id="66d6c-230">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="66d6c-231">Définissez la **version CLR .NET** sur **Aucun code managé** car la prise en charge du Common Language Runtime Core (CoreCLR) pour Core .Net est démarrée pour héberger l’application dans le processus Worker et non dans le Desktop CLR (CLR .Net).</span><span class="sxs-lookup"><span data-stu-id="66d6c-231">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="66d6c-232">Ajoutez le site racine dans IIS Manager en plaçant la sous-application dans un dossier du site racine.</span><span class="sxs-lookup"><span data-stu-id="66d6c-232">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="66d6c-233">Cliquez avec le bouton droit sur le dossier de la sous-application dans IIS Manager, puis sélectionnez **Convertir en application**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-233">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="66d6c-234">Dans la boîte de dialogue **Ajouter une application**, utilisez le bouton **Sélectionner** de l’option **Pool d’applications** pour affecter le pool d’applications que vous avez créé pour la sous-application.</span><span class="sxs-lookup"><span data-stu-id="66d6c-234">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="66d6c-235">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-235">Select **OK**.</span></span>

<span data-ttu-id="66d6c-236">L’attribution d’un pool d’applications distinct à la sous-application est obligatoire lorsque vous utilisez le modèle d’hébergement in-process.</span><span class="sxs-lookup"><span data-stu-id="66d6c-236">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="66d6c-237">Pour plus d’informations sur le modèle d’hébergement in-process et sur la configuration du module ASP.NET Core, consultez <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="66d6c-237">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="application-pools"></a><span data-ttu-id="66d6c-238">Pools d'applications</span><span class="sxs-lookup"><span data-stu-id="66d6c-238">Application Pools</span></span>

<span data-ttu-id="66d6c-239">L’isolation des pools d’applications est déterminée par le modèle d’hébergement :</span><span class="sxs-lookup"><span data-stu-id="66d6c-239">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="66d6c-240">Hébergement in-process : les applications doivent être exécutées dans des pools d’applications distincts.</span><span class="sxs-lookup"><span data-stu-id="66d6c-240">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="66d6c-241">Hébergement hors processus : nous vous recommandons d’isoler les applications les unes des autres en exécutant chaque application dans son propre pool d’applications.</span><span class="sxs-lookup"><span data-stu-id="66d6c-241">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="66d6c-242">La boîte de dialogue **Ajouter un site web** d’IIS applique un seul pool d’applications par application par défaut.</span><span class="sxs-lookup"><span data-stu-id="66d6c-242">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="66d6c-243">Quand un **Nom du site** est fourni, le texte est automatiquement transféré vers la zone de texte **Pool d’applications**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-243">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="66d6c-244">Un nouveau pool d’applications est créé avec le nom du site une fois qu’il est ajouté.</span><span class="sxs-lookup"><span data-stu-id="66d6c-244">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="66d6c-245">Pool d’applications Identity</span><span class="sxs-lookup"><span data-stu-id="66d6c-245">Application Pool Identity</span></span>

<span data-ttu-id="66d6c-246">Un compte d’identité du pool d’applications permet à une application de s’exécuter sous un compte unique sans qu’il soit nécessaire de créer et de gérer des domaines ou des comptes locaux.</span><span class="sxs-lookup"><span data-stu-id="66d6c-246">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="66d6c-247">Sur IIS 8.0 ou version ultérieure, le processus Worker d’administration IIS (WAS) crée un compte virtuel avec le nom du nouveau pool d’applications et exécute les processus Worker du pool d’applications sous ce compte par défaut.</span><span class="sxs-lookup"><span data-stu-id="66d6c-247">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="66d6c-248">Dans la console de gestion IIS, sous **Paramètres avancés** pour le pool d’applications, assurez-vous que le **Identity** est configuré pour utiliser `ApplicationPoolIdentity` :</span><span class="sxs-lookup"><span data-stu-id="66d6c-248">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use `ApplicationPoolIdentity`:</span></span>

![Boîte de dialogue Paramètres avancés du pool applications](index/_static/apppool-identity.png)

<span data-ttu-id="66d6c-250">Le processus de gestion IIS crée un identificateur sécurisé avec le nom du pool d’applications dans le système de sécurité Windows.</span><span class="sxs-lookup"><span data-stu-id="66d6c-250">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="66d6c-251">Les ressources peuvent être sécurisées à l’aide de cette identité.</span><span class="sxs-lookup"><span data-stu-id="66d6c-251">Resources can be secured using this identity.</span></span> <span data-ttu-id="66d6c-252">Toutefois, cette identité n’est pas un compte d’utilisateur réel et n’apparaît pas dans la console de gestion d’utilisateurs Windows.</span><span class="sxs-lookup"><span data-stu-id="66d6c-252">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="66d6c-253">Si le processus Worker IIS a besoin d’un accès élevé à l’application, modifiez la liste de contrôle d’accès (ACL) du répertoire contenant l’application :</span><span class="sxs-lookup"><span data-stu-id="66d6c-253">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="66d6c-254">Ouvrez l’Explorateur Windows et accédez au répertoire.</span><span class="sxs-lookup"><span data-stu-id="66d6c-254">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="66d6c-255">Cliquez avec le bouton droit sur le répertoire et sélectionnez **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-255">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="66d6c-256">Sous l’onglet **Sécurité**, sélectionnez le bouton **Modifier**, puis le bouton **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-256">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="66d6c-257">Sélectionnez le bouton **Emplacements**, puis vérifiez que le système est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="66d6c-257">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="66d6c-258">Entrez `IIS AppPool\{APP POOL NAME}` format, où l’espace réservé `{APP POOL NAME}` est le nom du pool d’applications, dans **la zone Entrez les noms des objets à sélectionner** .</span><span class="sxs-lookup"><span data-stu-id="66d6c-258">Enter `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="66d6c-259">Sélectionnez le bouton **Vérifier les noms**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-259">Select the **Check Names** button.</span></span> <span data-ttu-id="66d6c-260">Pour l’option *DefaultAppPool* , vérifiez les noms à l’aide de `IIS AppPool\DefaultAppPool` .</span><span class="sxs-lookup"><span data-stu-id="66d6c-260">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="66d6c-261">Lorsque le bouton **vérifier les noms** est sélectionné, la valeur `DefaultAppPool` est indiquée dans la zone noms d’objets.</span><span class="sxs-lookup"><span data-stu-id="66d6c-261">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="66d6c-262">Il n’est pas possible d’entrer le nom du pool d’applications directement dans la zone des noms d’objets.</span><span class="sxs-lookup"><span data-stu-id="66d6c-262">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="66d6c-263">Utilisez le `IIS AppPool\{APP POOL NAME}` format, où l’espace réservé `{APP POOL NAME}` est le nom du pool d’applications, lors de la vérification du nom de l’objet.</span><span class="sxs-lookup"><span data-stu-id="66d6c-263">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![Sélectionnez la boîte de dialogue utilisateurs ou groupes pour le dossier d’applications : ajoutez le nom du pool d’applications « DefaultAppPool » à « IIS AppPool\" dans la zone des noms d’objets avant de sélectionner « Vérifier les noms ».](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="66d6c-265">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-265">Select **OK**.</span></span>

   ![Sélectionnez la boîte de dialogue utilisateurs ou groupes pour le dossier d’applications : après avoir sélectionné « Vérifier les noms », le nom d’objet « DefaultAppPool » est indiqué dans la zone des noms d’objets.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="66d6c-267">Les autorisations Lire &amp; exécuter doivent être accordées par défaut.</span><span class="sxs-lookup"><span data-stu-id="66d6c-267">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="66d6c-268">Fournissez des autorisations supplémentaires si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="66d6c-268">Provide additional permissions as needed.</span></span>

<span data-ttu-id="66d6c-269">L’accès peut également être octroyé par le biais d’une invite de commandes à l’aide de l’outil **ICACLS**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-269">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="66d6c-270">À l’aide de *DefaultAppPool* en exemple, la commande suivante est utilisée :</span><span class="sxs-lookup"><span data-stu-id="66d6c-270">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="66d6c-271">Pour plus d’informations, consultez la rubrique [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="66d6c-271">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="66d6c-272">Assistance HTTP/2</span><span class="sxs-lookup"><span data-stu-id="66d6c-272">HTTP/2 support</span></span>

<span data-ttu-id="66d6c-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) est pris en charge avec ASP.NET Core dans les scénarios de déploiement IIS suivants :</span><span class="sxs-lookup"><span data-stu-id="66d6c-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="66d6c-274">In-process</span><span class="sxs-lookup"><span data-stu-id="66d6c-274">In-process</span></span>
  * <span data-ttu-id="66d6c-275">Windows Server 2016/Windows 10 ou version ultérieure ; IIS 10 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="66d6c-275">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="66d6c-276">TLS 1.2 ou connexion ultérieure</span><span class="sxs-lookup"><span data-stu-id="66d6c-276">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="66d6c-277">Out-of-process</span><span class="sxs-lookup"><span data-stu-id="66d6c-277">Out-of-process</span></span>
  * <span data-ttu-id="66d6c-278">Windows Server 2016/Windows 10 ou version ultérieure ; IIS 10 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="66d6c-278">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="66d6c-279">Les connexions au serveur périphérique public utilisent HTTP/2, mais la connexion de proxy inverse pour le [serveur Kestrel](xref:fundamentals/servers/kestrel) utilise HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="66d6c-279">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="66d6c-280">TLS 1.2 ou connexion ultérieure</span><span class="sxs-lookup"><span data-stu-id="66d6c-280">TLS 1.2 or later connection</span></span>

<span data-ttu-id="66d6c-281">Pour un déploiement in-process lors de l’établissement d’une connexion HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) signale `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="66d6c-281">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="66d6c-282">Pour un déploiement hors processus lors de l’établissement d’une connexion HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) signale `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="66d6c-282">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="66d6c-283">Pour plus d’informations sur les modèles d’hébergement in-process et out-of-process, consultez <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="66d6c-283">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="66d6c-284">HTTP/2 est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="66d6c-284">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="66d6c-285">Les connexions reviennent à HTTP/1.1 si une connexion HTTP/2 n’est pas établie.</span><span class="sxs-lookup"><span data-stu-id="66d6c-285">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="66d6c-286">Pour plus d’informations sur la configuration de HTTP/2 avec les déploiements IIS, consultez [HTTP/2 sur IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="66d6c-286">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="66d6c-287">Requêtes préliminaires CORS</span><span class="sxs-lookup"><span data-stu-id="66d6c-287">CORS preflight requests</span></span>

<span data-ttu-id="66d6c-288">*Cette section s’applique uniquement aux applications ASP.NET Core qui ciblent le .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="66d6c-288">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="66d6c-289">Pour une application ASP.NET Core qui cible le .NET Framework, les requêtes OPTIONS ne sont pas transmises à l’application par défaut dans IIS.</span><span class="sxs-lookup"><span data-stu-id="66d6c-289">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="66d6c-290">Pour savoir comment configurer les gestionnaires IIS de l’application dans `web.config` pour passer des demandes d’options, consultez [activer les demandes Cross-origin dans API Web ASP.NET 2 : fonctionnement de cors](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="66d6c-290">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="66d6c-291">Module d’initialisation de l’application et délai d’inactivité</span><span class="sxs-lookup"><span data-stu-id="66d6c-291">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="66d6c-292">Quand ils sont hébergés dans IIS par le Module ASP.NET Core version 2 :</span><span class="sxs-lookup"><span data-stu-id="66d6c-292">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="66d6c-293">[Module d’initialisation d’application](#application-initialization-module): le processus [de](xref:host-and-deploy/iis/in-process-hosting) l’application hébergée dans le processus ou [hors processus](xref:host-and-deploy/iis/out-of-process-hosting) peut être configuré pour démarrer automatiquement lors du redémarrage d’un processus de travail ou du redémarrage du serveur.</span><span class="sxs-lookup"><span data-stu-id="66d6c-293">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) or [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="66d6c-294">[Délai d’inactivité](#idle-timeout): [le processus hébergé par](xref:host-and-deploy/iis/in-process-hosting) l’application peut être configuré pour ne pas expirer pendant les périodes d’inactivité.</span><span class="sxs-lookup"><span data-stu-id="66d6c-294">[Idle Timeout](#idle-timeout): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) can be configured not to time out during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="66d6c-295">Module d’initialisation de l’application</span><span class="sxs-lookup"><span data-stu-id="66d6c-295">Application Initialization Module</span></span>

<span data-ttu-id="66d6c-296">*S’applique aux applications hébergées in-process et hors processus.*</span><span class="sxs-lookup"><span data-stu-id="66d6c-296">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="66d6c-297">[L’Initialisation d’application IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) est une fonctionnalité IIS qui envoie une requête HTTP à l’application lorsque le pool d’applications démarre ou est recyclé.</span><span class="sxs-lookup"><span data-stu-id="66d6c-297">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="66d6c-298">La requête déclenche le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="66d6c-298">The request triggers the app to start.</span></span> <span data-ttu-id="66d6c-299">Par défaut, IIS émet une requête à l’URL racine de l’application (`/`) pour initialiser l’application (consultez les [ressources supplémentaires](#application-initialization-module-and-idle-timeout-additional-resources) pour plus d’informations sur la configuration).</span><span class="sxs-lookup"><span data-stu-id="66d6c-299">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="66d6c-300">Vérifiez que la fonctionnalité de rôle Initialisation d’application IIS est activée :</span><span class="sxs-lookup"><span data-stu-id="66d6c-300">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="66d6c-301">Sur Windows 7 ou systèmes de bureau de version ultérieure lorsque vous utilisez IIS localement :</span><span class="sxs-lookup"><span data-stu-id="66d6c-301">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="66d6c-302">Accédez à **panneau de configuration**  >  **programmes** programmes  >  **et fonctionnalités**  >  **activer ou désactiver des fonctionnalités Windows** (côté gauche de l’écran).</span><span class="sxs-lookup"><span data-stu-id="66d6c-302">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="66d6c-303">Ouvrez **Internet Information Services**  >  fonctionnalités de développement d’applications **World Wide Web services**  >  .</span><span class="sxs-lookup"><span data-stu-id="66d6c-303">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="66d6c-304">Cochez la case **Initialisation d’application**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-304">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="66d6c-305">Sur Windows Server 2008 R2 ou version ultérieure :</span><span class="sxs-lookup"><span data-stu-id="66d6c-305">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="66d6c-306">Ouvrez l’**Assistant Ajout de rôles et de fonctionnalités**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-306">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="66d6c-307">Dans le panneau **Sélectionnez les services de rôle**, ouvrez le nœud **Développement d’applications**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-307">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="66d6c-308">Cochez la case **Initialisation d’application**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-308">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="66d6c-309">Utilisez une des approches suivantes pour activer le Module d’initialisation de l’application pour le site :</span><span class="sxs-lookup"><span data-stu-id="66d6c-309">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="66d6c-310">Utilisation du Gestionnaire IIS :</span><span class="sxs-lookup"><span data-stu-id="66d6c-310">Using IIS Manager:</span></span>

  1. <span data-ttu-id="66d6c-311">Sélectionnez **Pools d’applications** dans le volet **Connexions**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-311">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="66d6c-312">Cliquez avec le bouton de droite sur le pool d’applications de l’application dans la liste, puis sélectionnez **Paramètres avancés**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-312">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="66d6c-313">Le **mode de démarrage** par défaut est `OnDemand` .</span><span class="sxs-lookup"><span data-stu-id="66d6c-313">The default **Start Mode** is `OnDemand`.</span></span> <span data-ttu-id="66d6c-314">Définissez le **mode de démarrage** sur `AlwaysRunning` .</span><span class="sxs-lookup"><span data-stu-id="66d6c-314">Set the **Start Mode** to `AlwaysRunning`.</span></span> <span data-ttu-id="66d6c-315">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-315">Select **OK**.</span></span>
  1. <span data-ttu-id="66d6c-316">Ouvrez le nœud **Sites** dans le panneau **Connexions**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-316">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="66d6c-317">Cliquez avec le bouton droit sur l’application, puis sélectionnez **gérer** les  >  **Paramètres avancés** du site Web.</span><span class="sxs-lookup"><span data-stu-id="66d6c-317">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="66d6c-318">Le paramètre de **préchargement** par défaut activé est `False` .</span><span class="sxs-lookup"><span data-stu-id="66d6c-318">The default **Preload Enabled** setting is `False`.</span></span> <span data-ttu-id="66d6c-319">Affectez à l' **option Précharger** la valeur `True` .</span><span class="sxs-lookup"><span data-stu-id="66d6c-319">Set **Preload Enabled** to `True`.</span></span> <span data-ttu-id="66d6c-320">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-320">Select **OK**.</span></span>

* <span data-ttu-id="66d6c-321">À l’aide de `web.config` , ajoutez l' `<applicationInitialization>` élément avec `doAppInitAfterRestart` `true` la valeur aux `<system.webServer>` éléments du fichier de l’application `web.config` :</span><span class="sxs-lookup"><span data-stu-id="66d6c-321">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's `web.config` file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="66d6c-322">Délai d’inactivité</span><span class="sxs-lookup"><span data-stu-id="66d6c-322">Idle Timeout</span></span>

<span data-ttu-id="66d6c-323">*S’applique aux applications hébergées in-process.*</span><span class="sxs-lookup"><span data-stu-id="66d6c-323">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="66d6c-324">Pour empêcher la mise en veille après une période d’inactivité de l’application, définissez le délai d’inactivité du pool d’applications à l’aide du Gestionnaire IIS :</span><span class="sxs-lookup"><span data-stu-id="66d6c-324">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="66d6c-325">Sélectionnez **Pools d’applications** dans le volet **Connexions**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-325">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="66d6c-326">Cliquez avec le bouton de droite sur le pool d’applications de l’application dans la liste, puis sélectionnez **Paramètres avancés**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="66d6c-327">Le **délai d’inactivité par défaut (en minutes)** est de `20` minutes.</span><span class="sxs-lookup"><span data-stu-id="66d6c-327">The default **Idle Time-out (minutes)** is `20` minutes.</span></span> <span data-ttu-id="66d6c-328">Définissez le **délai d’inactivité (en minutes)** sur `0` (zéro).</span><span class="sxs-lookup"><span data-stu-id="66d6c-328">Set the **Idle Time-out (minutes)** to `0` (zero).</span></span> <span data-ttu-id="66d6c-329">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="66d6c-329">Select **OK**.</span></span>
1. <span data-ttu-id="66d6c-330">Recyclez le processus Worker.</span><span class="sxs-lookup"><span data-stu-id="66d6c-330">Recycle the worker process.</span></span>

<span data-ttu-id="66d6c-331">Pour empêcher les applications hébergées [hors processus](xref:host-and-deploy/iis/out-of-process-hosting) d’expirer, utilisez une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="66d6c-331">To prevent apps hosted [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="66d6c-332">Effectuez un test ping de l’application à partir d’un service externe afin de garantir son fonctionnement.</span><span class="sxs-lookup"><span data-stu-id="66d6c-332">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="66d6c-333">Si l’application héberge uniquement les services d’arrière-plan, évitez l’hébergement IIS et utilisez un [Service Windows pour héberger l’application ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="66d6c-333">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="66d6c-334">Module d’initialisation de l’application et ressources supplémentaires du délai d’inactivité</span><span class="sxs-lookup"><span data-stu-id="66d6c-334">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="66d6c-335">Initialisation de l’application IIS 8.0</span><span class="sxs-lookup"><span data-stu-id="66d6c-335">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="66d6c-336">[Initialisation de l' `<applicationInitialization>` application ](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="66d6c-336">[Application Initialization `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="66d6c-337">[Paramètres du modèle de processus pour un `<processModel>` pool d’applications ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="66d6c-337">[Process Model Settings for an Application Pool `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="66d6c-338">Emplacements des fichiers du module, du schéma et de configuration</span><span class="sxs-lookup"><span data-stu-id="66d6c-338">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="66d6c-339">Module</span><span class="sxs-lookup"><span data-stu-id="66d6c-339">Module</span></span>

<span data-ttu-id="66d6c-340">**IIS (x86/amd64)**:</span><span class="sxs-lookup"><span data-stu-id="66d6c-340">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="66d6c-341">**IIS Express (x86/amd64)**:</span><span class="sxs-lookup"><span data-stu-id="66d6c-341">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="66d6c-342">schéma</span><span class="sxs-lookup"><span data-stu-id="66d6c-342">Schema</span></span>

<span data-ttu-id="66d6c-343">**IIS**</span><span class="sxs-lookup"><span data-stu-id="66d6c-343">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="66d6c-344">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="66d6c-344">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="66d6c-345">Configuration</span><span class="sxs-lookup"><span data-stu-id="66d6c-345">Configuration</span></span>

<span data-ttu-id="66d6c-346">**IIS**</span><span class="sxs-lookup"><span data-stu-id="66d6c-346">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="66d6c-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="66d6c-347">**IIS Express**</span></span>

* <span data-ttu-id="66d6c-348">Visual Studio : `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="66d6c-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="66d6c-349">*iisexpress.exe* COMMUN `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="66d6c-349">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="66d6c-350">Vous pouvez trouver les fichiers en recherchant `aspnetcore` dans le `applicationHost.config` fichier.</span><span class="sxs-lookup"><span data-stu-id="66d6c-350">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

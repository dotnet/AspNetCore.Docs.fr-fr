---
title: Hébergement d’images ASP.NET Core avec l’arrimeur sur HTTPs
author: rick-anderson
description: Découvrez comment héberger des images ASP.NET Core avec l’arrimeur sur HTTPs
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
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
uid: security/docker-https
ms.openlocfilehash: 3af2aff477604eb19ac211753f848d08d0c67c72
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588638"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="2c025-103">Hébergement d’images ASP.NET Core avec l’arrimeur sur HTTPs</span><span class="sxs-lookup"><span data-stu-id="2c025-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="2c025-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2c025-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2c025-105">ASP.NET Core utilise le [protocole HTTPS par défaut](./enforcing-ssl.md).</span><span class="sxs-lookup"><span data-stu-id="2c025-105">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="2c025-106">[Https](https://en.wikipedia.org/wiki/HTTPS) s’appuie sur des [certificats](https://en.wikipedia.org/wiki/Public_key_certificate) pour l’approbation, l’identité et le chiffrement.</span><span class="sxs-lookup"><span data-stu-id="2c025-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="2c025-107">Ce document explique comment exécuter des images conteneur prégénérées avec HTTPs.</span><span class="sxs-lookup"><span data-stu-id="2c025-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="2c025-108">Consultez [développement d’Applications ASP.net core avec l’arrimeur sur https](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md) pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="2c025-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="2c025-109">Cet exemple requiert l' [ancrage 17,06](https://docs.docker.com/release-notes/docker-ce) ou une version ultérieure du [client dockr](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="2c025-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2c025-110">Prérequis</span><span class="sxs-lookup"><span data-stu-id="2c025-110">Prerequisites</span></span>

<span data-ttu-id="2c025-111">Le [Kit de développement logiciel (SDK) .net Core 2,2](https://dotnet.microsoft.com/download) ou version ultérieure est requis pour certaines des instructions contenues dans ce document.</span><span class="sxs-lookup"><span data-stu-id="2c025-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="2c025-112">Certificats</span><span class="sxs-lookup"><span data-stu-id="2c025-112">Certificates</span></span>

<span data-ttu-id="2c025-113">Un certificat d’une [autorité de certification](https://wikipedia.org/wiki/Certificate_authority) est requis pour l' [Hébergement de production](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) pour un domaine.</span><span class="sxs-lookup"><span data-stu-id="2c025-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="2c025-114">[Let's Encrypt](https://letsencrypt.org/) est une autorité de certification qui offre des certificats gratuits.</span><span class="sxs-lookup"><span data-stu-id="2c025-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="2c025-115">Ce document utilise des [certificats de développement auto-signés](https://en.wikipedia.org/wiki/Self-signed_certificate) pour héberger des images prégénérées sur `localhost` .</span><span class="sxs-lookup"><span data-stu-id="2c025-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="2c025-116">Les instructions sont similaires à l’utilisation de certificats de production.</span><span class="sxs-lookup"><span data-stu-id="2c025-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="2c025-117">Utilisez [dotnet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) pour créer des certificats auto-signés pour le développement et le test.</span><span class="sxs-lookup"><span data-stu-id="2c025-117">Use [dotnet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) to create self-signed certificates for development and testing.</span></span>

<span data-ttu-id="2c025-118">Pour les certificats de production :</span><span class="sxs-lookup"><span data-stu-id="2c025-118">For production certs:</span></span>

* <span data-ttu-id="2c025-119">L' `dotnet dev-certs` outil n’est pas requis.</span><span class="sxs-lookup"><span data-stu-id="2c025-119">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="2c025-120">Les certificats n’ont pas besoin d’être stockés à l’emplacement utilisé dans les instructions.</span><span class="sxs-lookup"><span data-stu-id="2c025-120">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="2c025-121">Tout emplacement doit fonctionner, bien que le stockage des certificats dans votre répertoire de site ne soit pas recommandé.</span><span class="sxs-lookup"><span data-stu-id="2c025-121">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="2c025-122">Les instructions contenues dans la section suivante contiennent des certificats de montage de volume dans des conteneurs à l’aide de l’option de ligne de commande de l’ancrage `-v` .</span><span class="sxs-lookup"><span data-stu-id="2c025-122">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="2c025-123">Vous pouvez ajouter des certificats dans des images de conteneur à l’aide d’une `COPY` commande dans un *fichier dockerfile*, mais cela n’est pas recommandé.</span><span class="sxs-lookup"><span data-stu-id="2c025-123">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="2c025-124">La copie de certificats dans une image n’est pas recommandée pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="2c025-124">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="2c025-125">Il est difficile d’utiliser la même image pour le test avec des certificats de développeur.</span><span class="sxs-lookup"><span data-stu-id="2c025-125">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="2c025-126">Il est difficile d’utiliser la même image pour l’hébergement avec des certificats de production.</span><span class="sxs-lookup"><span data-stu-id="2c025-126">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="2c025-127">Il y a un risque significatif de divulgation de certificats.</span><span class="sxs-lookup"><span data-stu-id="2c025-127">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="2c025-128">Exécution d’images conteneur prégénérées avec HTTPs</span><span class="sxs-lookup"><span data-stu-id="2c025-128">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="2c025-129">Utilisez les instructions suivantes pour la configuration de votre système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="2c025-129">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="2c025-130">Windows utilisant des conteneurs Linux</span><span class="sxs-lookup"><span data-stu-id="2c025-130">Windows using Linux containers</span></span>

<span data-ttu-id="2c025-131">Générer un certificat et configurer l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="2c025-131">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="2c025-132">Dans les commandes précédentes, remplacez `{ password here }` par un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="2c025-132">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="2c025-133">Exécutez l’image de conteneur avec ASP.NET Core configuré pour le protocole HTTPs dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="2c025-133">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="2c025-134">Lorsque vous utilisez [PowerShell](/powershell/scripting/overview), remplacez `%USERPROFILE%` par `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="2c025-134">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="2c025-135">Le mot de passe doit correspondre au mot de passe utilisé pour le certificat.</span><span class="sxs-lookup"><span data-stu-id="2c025-135">The password must match the password used for the certificate.</span></span>


<span data-ttu-id="2c025-136">Remarque : dans ce cas, le certificat doit être un `.pfx` fichier.</span><span class="sxs-lookup"><span data-stu-id="2c025-136">Note: The certificate in this case must be a `.pfx` file.</span></span>  <span data-ttu-id="2c025-137">L’utilisation d' `.crt` un `.key` fichier ou avec ou sans mot de passe n’est pas prise en charge avec l’exemple de conteneur.</span><span class="sxs-lookup"><span data-stu-id="2c025-137">Utilizing a `.crt` or `.key` file with or without the password isn't supported with the sample container.</span></span>  <span data-ttu-id="2c025-138">Par exemple, lorsque vous spécifiez un `.crt` fichier, le conteneur peut retourner des messages d’erreur tels que « le mode serveur SSL doit utiliser un certificat avec la clé privée associée. ».</span><span class="sxs-lookup"><span data-stu-id="2c025-138">For example, when specifying a `.crt` file, the container may return error messages such as 'The server mode SSL must use a certificate with the associated private key.'.</span></span> <span data-ttu-id="2c025-139">Lorsque vous utilisez [WSL](/windows/wsl/about), validez le chemin d’accès de montage pour vous assurer que le certificat est chargé correctement.</span><span class="sxs-lookup"><span data-stu-id="2c025-139">When using [WSL](/windows/wsl/about), validate the mount path to ensure that the certificate loads correctly.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="2c025-140">macOS ou Linux</span><span class="sxs-lookup"><span data-stu-id="2c025-140">macOS or Linux</span></span>

<span data-ttu-id="2c025-141">Générer un certificat et configurer l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="2c025-141">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="2c025-142">`dotnet dev-certs https --trust` est pris en charge uniquement sur macOS et Windows.</span><span class="sxs-lookup"><span data-stu-id="2c025-142">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="2c025-143">Vous devez faire confiance aux certificats sur Linux de la même façon que votre distribution.</span><span class="sxs-lookup"><span data-stu-id="2c025-143">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="2c025-144">Il est probable que vous ayez besoin d’approuver le certificat dans votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="2c025-144">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="2c025-145">Dans les commandes précédentes, remplacez `{ password here }` par un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="2c025-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="2c025-146">Exécutez l’image de conteneur avec ASP.NET Core configuré pour le protocole HTTPs :</span><span class="sxs-lookup"><span data-stu-id="2c025-146">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="2c025-147">Le mot de passe doit correspondre au mot de passe utilisé pour le certificat.</span><span class="sxs-lookup"><span data-stu-id="2c025-147">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="2c025-148">Windows utilisant des conteneurs Windows</span><span class="sxs-lookup"><span data-stu-id="2c025-148">Windows using Windows containers</span></span>

<span data-ttu-id="2c025-149">Générer un certificat et configurer l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="2c025-149">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="2c025-150">Dans les commandes précédentes, remplacez `{ password here }` par un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="2c025-150">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="2c025-151">Lorsque vous utilisez [PowerShell](/powershell/scripting/overview), remplacez `%USERPROFILE%` par `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="2c025-151">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="2c025-152">Exécutez l’image de conteneur avec ASP.NET Core configuré pour le protocole HTTPs :</span><span class="sxs-lookup"><span data-stu-id="2c025-152">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="2c025-153">Le mot de passe doit correspondre au mot de passe utilisé pour le certificat.</span><span class="sxs-lookup"><span data-stu-id="2c025-153">The password must match the password used for the certificate.</span></span> <span data-ttu-id="2c025-154">Lorsque vous utilisez [PowerShell](/powershell/scripting/overview), remplacez `%USERPROFILE%` par `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="2c025-154">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

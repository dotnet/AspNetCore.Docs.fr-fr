---
title: Hébergement d’images ASP.NET Core avec l’arrimeur sur HTTPs
author: rick-anderson
description: Découvrez comment héberger des images ASP.NET Core avec l’arrimeur sur HTTPs
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/docker-https
ms.openlocfilehash: 63d6e220c0f28e552207039c1649041bfdf4a0d4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059674"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="ee341-103">Hébergement d’images ASP.NET Core avec l’arrimeur sur HTTPs</span><span class="sxs-lookup"><span data-stu-id="ee341-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="ee341-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ee341-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ee341-105">ASP.NET Core utilise le [protocole HTTPS par défaut](./enforcing-ssl.md).</span><span class="sxs-lookup"><span data-stu-id="ee341-105">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="ee341-106">[Https](https://en.wikipedia.org/wiki/HTTPS) s’appuie sur des [certificats](https://en.wikipedia.org/wiki/Public_key_certificate) pour l’approbation, l’identité et le chiffrement.</span><span class="sxs-lookup"><span data-stu-id="ee341-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="ee341-107">Ce document explique comment exécuter des images conteneur prégénérées avec HTTPs.</span><span class="sxs-lookup"><span data-stu-id="ee341-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="ee341-108">Consultez [développement d’Applications ASP.net core avec l’arrimeur sur https](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="ee341-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="ee341-109">Cet exemple requiert l' [ancrage 17,06](https://docs.docker.com/release-notes/docker-ce) ou une version ultérieure du [client dockr](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="ee341-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ee341-110">Prérequis</span><span class="sxs-lookup"><span data-stu-id="ee341-110">Prerequisites</span></span>

<span data-ttu-id="ee341-111">Le [Kit de développement logiciel (SDK) .net Core 2,2](https://dotnet.microsoft.com/download) ou version ultérieure est requis pour certaines des instructions contenues dans ce document.</span><span class="sxs-lookup"><span data-stu-id="ee341-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="ee341-112">Certificats</span><span class="sxs-lookup"><span data-stu-id="ee341-112">Certificates</span></span>

<span data-ttu-id="ee341-113">Un certificat d’une [autorité de certification](https://wikipedia.org/wiki/Certificate_authority) est requis pour l' [Hébergement de production](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) pour un domaine.</span><span class="sxs-lookup"><span data-stu-id="ee341-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="ee341-114">[:::no-loc(Let's Encrypt):::](https://letsencrypt.org/) est une autorité de certification qui offre des certificats gratuits.</span><span class="sxs-lookup"><span data-stu-id="ee341-114">[:::no-loc(Let's Encrypt):::](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="ee341-115">Ce document utilise des [certificats de développement auto-signés](https://en.wikipedia.org/wiki/Self-signed_certificate) pour héberger des images prégénérées sur `localhost` .</span><span class="sxs-lookup"><span data-stu-id="ee341-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="ee341-116">Les instructions sont similaires à l’utilisation de certificats de production.</span><span class="sxs-lookup"><span data-stu-id="ee341-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="ee341-117">Pour les certificats de production :</span><span class="sxs-lookup"><span data-stu-id="ee341-117">For production certs:</span></span>

* <span data-ttu-id="ee341-118">L' `dotnet dev-certs` outil n’est pas requis.</span><span class="sxs-lookup"><span data-stu-id="ee341-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="ee341-119">Les certificats n’ont pas besoin d’être stockés à l’emplacement utilisé dans les instructions.</span><span class="sxs-lookup"><span data-stu-id="ee341-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="ee341-120">Tout emplacement doit fonctionner, bien que le stockage des certificats dans votre répertoire de site ne soit pas recommandé.</span><span class="sxs-lookup"><span data-stu-id="ee341-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="ee341-121">Les instructions contenues dans la section suivante contiennent des certificats de montage de volume dans des conteneurs à l’aide de l’option de ligne de commande de l’ancrage `-v` .</span><span class="sxs-lookup"><span data-stu-id="ee341-121">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="ee341-122">Vous pouvez ajouter des certificats dans des images de conteneur à l’aide d’une `COPY` commande dans un *fichier dockerfile* , mais cela n’est pas recommandé.</span><span class="sxs-lookup"><span data-stu-id="ee341-122">You could add certificates into container images with a `COPY` command in a *Dockerfile* , but it's not recommended.</span></span> <span data-ttu-id="ee341-123">La copie de certificats dans une image n’est pas recommandée pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="ee341-123">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="ee341-124">Il est difficile d’utiliser la même image pour le test avec des certificats de développeur.</span><span class="sxs-lookup"><span data-stu-id="ee341-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="ee341-125">Il est difficile d’utiliser la même image pour l’hébergement avec des certificats de production.</span><span class="sxs-lookup"><span data-stu-id="ee341-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="ee341-126">Il y a un risque significatif de divulgation de certificats.</span><span class="sxs-lookup"><span data-stu-id="ee341-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="ee341-127">Exécution d’images conteneur prégénérées avec HTTPs</span><span class="sxs-lookup"><span data-stu-id="ee341-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="ee341-128">Utilisez les instructions suivantes pour la configuration de votre système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="ee341-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="ee341-129">Windows utilisant des conteneurs Linux</span><span class="sxs-lookup"><span data-stu-id="ee341-129">Windows using Linux containers</span></span>

<span data-ttu-id="ee341-130">Générer un certificat et configurer l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="ee341-130">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="ee341-131">Dans les commandes précédentes, remplacez `{ password here }` par un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="ee341-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="ee341-132">Exécutez l’image de conteneur avec ASP.NET Core configuré pour le protocole HTTPs dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="ee341-132">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="ee341-133">Lorsque vous utilisez [PowerShell](/powershell/scripting/overview), remplacez `%USERPROFILE%` par `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="ee341-133">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="ee341-134">Le mot de passe doit correspondre au mot de passe utilisé pour le certificat.</span><span class="sxs-lookup"><span data-stu-id="ee341-134">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="ee341-135">macOS ou Linux</span><span class="sxs-lookup"><span data-stu-id="ee341-135">macOS or Linux</span></span>

<span data-ttu-id="ee341-136">Générer un certificat et configurer l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="ee341-136">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="ee341-137">`dotnet dev-certs https --trust` est pris en charge uniquement sur macOS et Windows.</span><span class="sxs-lookup"><span data-stu-id="ee341-137">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="ee341-138">Vous devez faire confiance aux certificats sur Linux de la même façon que votre distribution.</span><span class="sxs-lookup"><span data-stu-id="ee341-138">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="ee341-139">Il est probable que vous ayez besoin d’approuver le certificat dans votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="ee341-139">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="ee341-140">Dans les commandes précédentes, remplacez `{ password here }` par un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="ee341-140">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="ee341-141">Exécutez l’image de conteneur avec ASP.NET Core configuré pour le protocole HTTPs :</span><span class="sxs-lookup"><span data-stu-id="ee341-141">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="ee341-142">Le mot de passe doit correspondre au mot de passe utilisé pour le certificat.</span><span class="sxs-lookup"><span data-stu-id="ee341-142">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="ee341-143">Windows utilisant des conteneurs Windows</span><span class="sxs-lookup"><span data-stu-id="ee341-143">Windows using Windows containers</span></span>

<span data-ttu-id="ee341-144">Générer un certificat et configurer l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="ee341-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="ee341-145">Dans les commandes précédentes, remplacez `{ password here }` par un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="ee341-145">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="ee341-146">Lorsque vous utilisez [PowerShell](/powershell/scripting/overview), remplacez `%USERPROFILE%` par `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="ee341-146">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="ee341-147">Exécutez l’image de conteneur avec ASP.NET Core configuré pour le protocole HTTPs :</span><span class="sxs-lookup"><span data-stu-id="ee341-147">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="ee341-148">Le mot de passe doit correspondre au mot de passe utilisé pour le certificat.</span><span class="sxs-lookup"><span data-stu-id="ee341-148">The password must match the password used for the certificate.</span></span> <span data-ttu-id="ee341-149">Lorsque vous utilisez [PowerShell](/powershell/scripting/overview), remplacez `%USERPROFILE%` par `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="ee341-149">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>
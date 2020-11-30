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
ms.openlocfilehash: a4aac2ce06fee20bdef157efc361f3099a217b1a
ms.sourcegitcommit: 619200f2981656ede6d89adb6a22ad1a0e16da22
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96332152"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a>Hébergement d’images ASP.NET Core avec l’arrimeur sur HTTPs

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core utilise le [protocole HTTPS par défaut](./enforcing-ssl.md). [Https](https://en.wikipedia.org/wiki/HTTPS) s’appuie sur des [certificats](https://en.wikipedia.org/wiki/Public_key_certificate) pour l’approbation, l’identité et le chiffrement.

Ce document explique comment exécuter des images conteneur prégénérées avec HTTPs.

Consultez [développement d’Applications ASP.net core avec l’arrimeur sur https](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) pour les scénarios de développement.

Cet exemple requiert l' [ancrage 17,06](https://docs.docker.com/release-notes/docker-ce) ou une version ultérieure du [client dockr](https://www.docker.com/products/docker).

## <a name="prerequisites"></a>Prérequis

Le [Kit de développement logiciel (SDK) .net Core 2,2](https://dotnet.microsoft.com/download) ou version ultérieure est requis pour certaines des instructions contenues dans ce document.

## <a name="certificates"></a>Certificats

Un certificat d’une [autorité de certification](https://wikipedia.org/wiki/Certificate_authority) est requis pour l' [Hébergement de production](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) pour un domaine. [Let's Encrypt](https://letsencrypt.org/) est une autorité de certification qui offre des certificats gratuits.

Ce document utilise des [certificats de développement auto-signés](https://en.wikipedia.org/wiki/Self-signed_certificate) pour héberger des images prégénérées sur `localhost` . Les instructions sont similaires à l’utilisation de certificats de production.

Utilisez [dotnet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) pour créer des certificats auto-signés pour le développement et le test.

Pour les certificats de production :

* L' `dotnet dev-certs` outil n’est pas requis.
* Les certificats n’ont pas besoin d’être stockés à l’emplacement utilisé dans les instructions. Tout emplacement doit fonctionner, bien que le stockage des certificats dans votre répertoire de site ne soit pas recommandé.

Les instructions contenues dans la section suivante contiennent des certificats de montage de volume dans des conteneurs à l’aide de l’option de ligne de commande de l’ancrage `-v` . Vous pouvez ajouter des certificats dans des images de conteneur à l’aide d’une `COPY` commande dans un *fichier dockerfile*, mais cela n’est pas recommandé. La copie de certificats dans une image n’est pas recommandée pour les raisons suivantes :

* Il est difficile d’utiliser la même image pour le test avec des certificats de développeur.
* Il est difficile d’utiliser la même image pour l’hébergement avec des certificats de production.
* Il y a un risque significatif de divulgation de certificats.

## <a name="running-pre-built-container-images-with-https"></a>Exécution d’images conteneur prégénérées avec HTTPs

Utilisez les instructions suivantes pour la configuration de votre système d’exploitation.

### <a name="windows-using-linux-containers"></a>Windows utilisant des conteneurs Linux

Générer un certificat et configurer l’ordinateur local :

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Dans les commandes précédentes, remplacez `{ password here }` par un mot de passe.

Exécutez l’image de conteneur avec ASP.NET Core configuré pour le protocole HTTPs dans une interface de commande :

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Lorsque vous utilisez [PowerShell](/powershell/scripting/overview), remplacez `%USERPROFILE%` par `$env:USERPROFILE` .

Le mot de passe doit correspondre au mot de passe utilisé pour le certificat.


Remarque : dans ce cas, le certificat doit être un `.pfx` fichier.  L’utilisation d' `.crt` un `.key` fichier ou avec ou sans mot de passe n’est pas prise en charge avec l’exemple de conteneur.  Par exemple, lorsque vous spécifiez un `.crt` fichier, le conteneur peut retourner des messages d’erreur tels que « le mode serveur SSL doit utiliser un certificat avec la clé privée associée. ». Lorsque vous utilisez [WSL](/windows/wsl/about), validez le chemin d’accès de montage pour vous assurer que le certificat est chargé correctement.

### <a name="macos-or-linux"></a>macOS ou Linux

Générer un certificat et configurer l’ordinateur local :

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust` est pris en charge uniquement sur macOS et Windows. Vous devez faire confiance aux certificats sur Linux de la même façon que votre distribution. Il est probable que vous ayez besoin d’approuver le certificat dans votre navigateur.

Dans les commandes précédentes, remplacez `{ password here }` par un mot de passe.

Exécutez l’image de conteneur avec ASP.NET Core configuré pour le protocole HTTPs :

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Le mot de passe doit correspondre au mot de passe utilisé pour le certificat.

### <a name="windows-using-windows-containers"></a>Windows utilisant des conteneurs Windows

Générer un certificat et configurer l’ordinateur local :

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Dans les commandes précédentes, remplacez `{ password here }` par un mot de passe. Lorsque vous utilisez [PowerShell](/powershell/scripting/overview), remplacez `%USERPROFILE%` par `$env:USERPROFILE` .

Exécutez l’image de conteneur avec ASP.NET Core configuré pour le protocole HTTPs :

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Le mot de passe doit correspondre au mot de passe utilisé pour le certificat. Lorsque vous utilisez [PowerShell](/powershell/scripting/overview), remplacez `%USERPROFILE%` par `$env:USERPROFILE` .

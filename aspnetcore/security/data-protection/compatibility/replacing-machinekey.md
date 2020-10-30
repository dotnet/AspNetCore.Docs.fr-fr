---
title: Remplacez ASP.NET machineKey dans ASP.NET Core
author: rick-anderson
description: Découvrez comment remplacer machineKey dans ASP.NET pour permettre l’utilisation d’un système de protection des données nouveau et plus sécurisé.
ms.author: riande
ms.date: 04/06/2019
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
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 7c6766fa20b0df021013da77b5d88fecefd40c62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053057"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a>Remplacez ASP.NET machineKey dans ASP.NET Core

<a name="compatibility-replacing-machinekey"></a>

L’implémentation de l' `<machineKey>` élément dans ASP.NET [est remplaçable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/). Cela permet d’acheminer la plupart des appels aux routines de chiffrement ASP.NET via un mécanisme de protection des données de remplacement, y compris le nouveau système de protection des données.

## <a name="package-installation"></a>Installation de package

> [!NOTE]
> Le nouveau système de protection des données ne peut être installé que dans une application ASP.NET existante ciblant .NET 4.5.1 ou version ultérieure. L’installation échoue si l’application cible .NET 4,5 ou une partie antérieure.

Pour installer le nouveau système de protection des données dans un projet ASP.NET 4.5.1 + existant, installez le package Microsoft.AspNetCore.DataProtection.SystemWeb. Cette opération instancie le système de protection des données à l’aide des paramètres de [configuration par défaut](xref:security/data-protection/configuration/default-settings) .

Quand vous installez le package, il insère une ligne dans *Web.config* qui indique à ASP.net de l’utiliser pour [la plupart des opérations de chiffrement](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), y compris l’authentification par formulaire, l’état d’affichage et les appels à machineKey. Protect. La ligne insérée est lue comme suit.

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> Vous pouvez déterminer si le nouveau système de protection des données est actif en inspectant des champs comme `__VIEWSTATE` , qui doit commencer par « CfDJ8 » comme dans l’exemple ci-dessous. « CfDJ8 » est la représentation en base64 de l’en-tête magique « 09 F0 C9 F0 » qui identifie une charge utile protégée par le système de protection des données.

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a>Configuration du package

Le système de protection des données est instancié avec une configuration d’installation zéro par défaut. Toutefois, étant donné que les clés par défaut sont conservées dans le système de fichiers local, cela ne fonctionne pas pour les applications qui sont déployées dans une batterie de serveurs. Pour résoudre ce risque, vous pouvez fournir la configuration en créant un type qui sous-classe DataProtectionStartup et substitue sa méthode ConfigureServices.

Voici un exemple de type de démarrage de protection des données personnalisé qui est configuré à la fois lorsque les clés sont conservées et comment elles sont chiffrées au repos. Il remplace également la stratégie d’isolation d’application par défaut en fournissant son propre nom d’application.

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> Vous pouvez également utiliser à la `<machineKey applicationName="my-app" ... />` place d’un appel explicite à SetApplicationName. Il s’agit d’un mécanisme pratique qui évite au développeur de créer un type dérivé de DataProtectionStartup s’il veut configurer le nom de l’application.

Pour activer cette configuration personnalisée, revenez à Web.config et recherchez l' `<appSettings>` élément que le package installe ajouté au fichier de configuration. Elle doit ressembler à la balise suivante :

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

Renseignez la valeur vide avec le nom qualifié d’assembly du type dérivé de DataProtectionStartup que vous venez de créer. Si le nom de l’application est DataProtectionDemo, cela ressemble à ce qui suit.

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

Le système de protection des données nouvellement configuré est maintenant prêt à être utilisé dans l’application.

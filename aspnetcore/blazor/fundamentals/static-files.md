---
title: ASP.NET Core les Blazor fichiers statiques
author: guardrex
description: Découvrez comment configurer et gérer des fichiers statiques pour les Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
uid: blazor/fundamentals/static-files
ms.openlocfilehash: 709250251113014027fe86c6a373e58a9c2a5009
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100110032"
---
# <a name="aspnet-core-blazor-static-files"></a><span data-ttu-id="80d7f-103">ASP.NET Core les Blazor fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="80d7f-103">ASP.NET Core Blazor static files</span></span>

<span data-ttu-id="80d7f-104">*Cet article s’applique à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="80d7f-104">*This article applies to Blazor Server.*</span></span>

<span data-ttu-id="80d7f-105">Pour créer des mappages de fichiers supplémentaires avec <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> ou configurer autre <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> , utilisez l' **une** des approches suivantes.</span><span class="sxs-lookup"><span data-stu-id="80d7f-105">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="80d7f-106">Dans les exemples suivants, l' `{EXTENSION}` espace réservé est l’extension de fichier et l' `{CONTENT TYPE}` espace réservé est le type de contenu.</span><span class="sxs-lookup"><span data-stu-id="80d7f-106">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="80d7f-107">Configurez les options par [injection de dépendance (di)](xref:blazor/fundamentals/dependency-injection) dans `Startup.ConfigureServices` ( `Startup.cs` ) à l’aide de <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :</span><span class="sxs-lookup"><span data-stu-id="80d7f-107">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  <span data-ttu-id="80d7f-108">Étant donné que cette approche configure le même fournisseur de fichiers que celui utilisé, assurez-vous `blazor.server.js` que votre configuration personnalisée n’interfère pas avec service `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="80d7f-108">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="80d7f-109">Par exemple, ne supprimez pas le mappage des fichiers JavaScript en configurant le fournisseur avec `provider.Mappings.Remove(".js")` .</span><span class="sxs-lookup"><span data-stu-id="80d7f-109">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="80d7f-110">Utilisez deux appels à <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` ( `Startup.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="80d7f-110">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="80d7f-111">Configurez le fournisseur de fichiers personnalisé dans le premier appel avec <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> .</span><span class="sxs-lookup"><span data-stu-id="80d7f-111">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="80d7f-112">Le deuxième intergiciel sert `blazor.server.js` , qui utilise la configuration de fichiers statiques par défaut fournie par l' Blazor infrastructure.</span><span class="sxs-lookup"><span data-stu-id="80d7f-112">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="80d7f-113">Vous pouvez éviter les interférences avec les services `_framework/blazor.server.js` en utilisant <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> pour exécuter un intergiciel (middleware) de fichiers statiques personnalisé :</span><span class="sxs-lookup"><span data-stu-id="80d7f-113">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

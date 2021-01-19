---
title: Interface utilisateur réutilisable Razor dans les bibliothèques de classes avec ASP.net Core
author: Rick-Anderson
description: Explique comment créer Razor une interface utilisateur réutilisable à l’aide de vues partielles dans une bibliothèque de classes dans ASP.net core.
ms.author: riande
ms.date: 01/19/2021
ms.custom: mvc, seodec18
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
uid: razor-pages/ui-class
ms.openlocfilehash: a878a3485ecee0782b21ac69c5ec6ff832b9f06c
ms.sourcegitcommit: cb984e0d7dc23a88c3a4121f23acfaea0acbfe1e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571009"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a>Créer une interface utilisateur réutilisable à l’aide du Razor projet de bibliothèque de classes dans ASP.net Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Razorles affichages, les pages, les contrôleurs, les modèles de page, les [ Razor composants](xref:blazor/components/class-libraries), les [composants de vue](xref:mvc/views/view-components)et les modèles de données peuvent être intégrés dans une Razor bibliothèque de classes (RCL). La RCL peut être empaquetée et réutilisée. Les applications peuvent inclure la RCL et remplacer les vues et les pages qu’elle contient. Lorsqu’une vue, une vue partielle ou une Razor page se trouve à la fois dans l’application Web et le RCL, le Razor balisage (fichier *. cshtml* ) dans l’application Web est prioritaire.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-no-locrazor-ui"></a>Créer une bibliothèque de classes contenant Razor l’interface utilisateur

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans Visual Studio, sélectionnez **créer un nouveau projet**.
* Sélectionnez **Razor bibliothèque de classes** > **suivant**.
* Nommez la bibliothèque (par exemple, « Razor ClassLib »), > **créer**. Pour éviter une collision de nom de fichier avec la bibliothèque de vues générée, vérifiez que le nom de la bibliothèque ne se termine pas par `.Views`.
* Sélectionnez **les pages et les vues de support** si vous avez besoin de prendre en charge les affichages. Par défaut, seules Razor les pages sont prises en charge. Sélectionnez **Create** (Créer).

Par défaut, le Razor modèle de bibliothèque de classes (RCL) est Razor développement de composant par défaut. L’option de **prise en charge des** pages et des affichages prend en charge les pages et les vues.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

À partir de la ligne de commande, exécutez `dotnet new razorclasslib`. Par exemple :

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Par défaut, le Razor modèle de bibliothèque de classes (RCL) est Razor développement de composant par défaut. Transmettez l' `--support-pages-and-views` option ( `dotnet new razorclasslib --support-pages-and-views` ) pour assurer la prise en charge des pages et des vues.

Pour plus d’informations, consultez [dotnet new](/dotnet/core/tools/dotnet-new). Pour éviter une collision de nom de fichier avec la bibliothèque de vues générée, vérifiez que le nom de la bibliothèque ne se termine pas par `.Views`.

---

Ajoutez Razor des fichiers à RCL.

Les modèles ASP.NET Core supposent que le contenu RCL se trouve dans le dossier *zones* . Consultez [RCL pages layout](#rcl-pages-layout) pour créer un RCL qui expose du contenu dans `~/Pages` plutôt que `~/Areas/Pages` .

## <a name="reference-rcl-content"></a>Référencer le contenu RCL

La RCL peut être référencée par :

* Un package NuGet. Consultez [Création de packages NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) et [Créer et publier un package NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* Un fichier *{NomProjet}.csproj*. Consultez [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="override-views-partial-views-and-pages"></a>Substituer des vues, des vues partielles et des pages

Lorsqu’une vue, une vue partielle ou une Razor page se trouve à la fois dans l’application Web et le RCL, le Razor balisage (fichier *. cshtml* ) dans l’application Web est prioritaire. Par exemple, ajoutez *application Web 1/Areas/MyFeature/pages/Page1. cshtml* à application Web 1, et Page1 dans le application Web 1 aura priorité sur Page1 dans le RCL.

Dans l’exemple proposé sous forme de téléchargement, renommez *WebApp1/Areas/MyFeature2* en *WebApp1/Areas/MyFeature* pour tester la priorité.

Copiez la vue partielle *Razor UIClassLib/zones/MyFeature/pages/Shared/_Message. cshtml* sur *application Web 1/Areas/MyFeature/pages/Shared/_Message. cshtml*. Mettez à jour le balisage pour indiquer le nouvel emplacement. Générez et exécutez l’application pour vérifier que la version de la vue partielle de l’application est utilisée.

### <a name="rcl-pages-layout"></a>Mise en page des pages RCL

Pour faire référence au contenu RCL comme s’il fait partie du dossier *pages* de l’application Web, créez le projet RCL avec la structure de fichiers suivante :

* *RazorUIClassLib/pages*
* *RazorUIClassLib/pages/partagé*

Supposons que *Razor UIClassLib/pages/Shared* contient deux fichiers partiels : *_Header. cshtml* et *_Footer. cshtml*. Les `<partial>` balises peuvent être ajoutées au fichier *_Layout. cshtml* :

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

Ajoutez le fichier *_ViewStart. cshtml* au dossier *pages* du projet RCL pour utiliser le fichier *_Layout. cshtml* à partir de l’application Web hôte :

```cshtml
@{
    Layout = "_Layout";
}
```

## <a name="create-an-rcl-with-static-assets"></a>Créer un RCL avec des ressources statiques

Un RCL peut nécessiter des ressources statiques auxiliaires qui peuvent être référencées par RCL ou l’application consommateur du RCL. ASP.NET Core permet de créer des RCLs qui incluent des ressources statiques disponibles pour une application consommatrice.

Pour inclure des ressources d’accompagnement dans le cadre d’un RCL, créez un dossier *wwwroot* dans la bibliothèque de classes et incluez tous les fichiers nécessaires dans ce dossier.

Lors de la compression d’un RCL, toutes les ressources associées dans le dossier *wwwroot* sont automatiquement incluses dans le package.

Utilisez la `dotnet pack` commande plutôt que la version NuGet.exe `nuget pack` .

### <a name="exclude-static-assets"></a>Exclure des ressources statiques

Pour exclure des ressources statiques, ajoutez le chemin d’exclusion souhaité au `$(DefaultItemExcludes)` groupe de propriétés dans le fichier projet. Séparez les entrées par un point-virgule ( `;` ).

Dans l’exemple suivant, la feuille de style *lib. CSS* du dossier *wwwroot* n’est pas considérée comme une ressource statique et n’est pas incluse dans le RCL publié :

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Intégration de la machine à écrire

Pour inclure des fichiers de machine à écrire dans un RCL :

1. Placez les fichiers de machine à écrire (*. TS*) en dehors du dossier *wwwroot* . Par exemple, placez les fichiers dans un dossier *client* .

1. Configurez la sortie de génération de machine à écrire pour le dossier *wwwroot* . Définissez la `TypescriptOutDir` propriété à l’intérieur d’un `PropertyGroup` dans le fichier projet :

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. Incluez la cible de la machine à écrire comme dépendance de la `ResolveCurrentProjectStaticWebAssets` cible en ajoutant la cible suivante à l’intérieur d’un `PropertyGroup` dans le fichier projet :

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Consommer du contenu à partir d’un RCL référencé

Les fichiers inclus dans le dossier *wwwroot* du RCL sont exposés à RCL ou à l’application consommatrice sous le préfixe `_content/{LIBRARY NAME}/` . Par exemple, une bibliothèque nommée *Razor . Class. lib* génère un chemin d’accès au contenu statique à l’emplacement `_content/Razor.Class.Lib/` . Lorsque vous générez un package NuGet et que le nom de l’assembly n’est pas le même que l’ID du package, utilisez l’ID de package pour `{LIBRARY NAME}` .

L’application consommatrice référence les ressources statiques fournies par la bibliothèque avec `<script>` ,, `<style>` et d' `<img>` autres balises html. L’application consommatrice doit avoir la [prise en charge des fichiers statiques](xref:fundamentals/static-files) activée dans `Startup.Configure` :

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

Lors de l’exécution de l’application consommatrice à partir de la sortie de génération ( `dotnet run` ), les ressources Web statiques sont activées par défaut dans l’environnement de développement. Pour prendre en charge les ressources dans d’autres environnements lors de l’exécution à partir de la sortie de génération, appelez `UseStaticWebAssets` sur le générateur d’hôte dans *Program.cs*:

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

`UseStaticWebAssets`L’appel de n’est pas requis lors de l’exécution d’une application à partir de la sortie publiée ( `dotnet publish` ).

### <a name="multi-project-development-flow"></a>Déroulement du développement de projets multiples

Lorsque l’application consommatrice s’exécute :

* Les ressources du RCL restent dans leurs dossiers d’origine. Les ressources ne sont pas déplacées vers l’application consommatrice.
* Toute modification dans le dossier *wwwroot* de RCL est reflétée dans l’application consommatrice une fois que le RCL est reconstruit et sans régénérer l’application consommateur.

Lorsque le RCL est généré, un manifeste qui décrit les emplacements des ressources Web statiques est généré. L’application consommatrice lit le manifeste au moment de l’exécution pour consommer les ressources des packages et des projets référencés. Lorsqu’un nouvel élément multimédia est ajouté à un RCL, le RCL doit être régénéré pour mettre à jour son manifeste avant qu’une application consommatrice puisse accéder au nouvel élément multimédia.

### <a name="publish"></a>Publier

Lorsque l’application est publiée, les ressources complémentaires de tous les packages et projets référencés sont copiées dans le dossier *wwwroot* de l’application publiée sous `_content/{LIBRARY NAME}/` .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razorles affichages, les pages, les contrôleurs, les modèles de page, les [ Razor composants](xref:blazor/components/class-libraries), les [composants de vue](xref:mvc/views/view-components)et les modèles de données peuvent être intégrés dans une Razor bibliothèque de classes (RCL). La RCL peut être empaquetée et réutilisée. Les applications peuvent inclure la RCL et remplacer les vues et les pages qu’elle contient. Lorsqu’une vue, une vue partielle ou une Razor page se trouve à la fois dans l’application Web et le RCL, le Razor balisage (fichier *. cshtml* ) dans l’application Web est prioritaire.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-no-locrazor-ui"></a>Créer une bibliothèque de classes contenant Razor l’interface utilisateur

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.
* Sélectionnez **Application web ASP.NET Core**.
* Nommez la bibliothèque (par exemple, « Razor ClassLib ») > **OK**. Pour éviter une collision de nom de fichier avec la bibliothèque de vues générée, vérifiez que le nom de la bibliothèque ne se termine pas par `.Views`.
* Vérifiez que **ASP.NET Core 2.1** ou ultérieur est sélectionné.
* Sélectionnez **Razor bibliothèque de classes** > **OK**.

Un RCL a le fichier projet suivant :

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

À partir de la ligne de commande, exécutez `dotnet new razorclasslib`. Par exemple :

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Pour plus d’informations, consultez [dotnet new](/dotnet/core/tools/dotnet-new). Pour éviter une collision de nom de fichier avec la bibliothèque de vues générée, vérifiez que le nom de la bibliothèque ne se termine pas par `.Views`.

---

Ajoutez Razor des fichiers à RCL.

Les modèles ASP.NET Core supposent que le contenu RCL se trouve dans le dossier *zones* . Consultez [RCL pages layout](#rcl-pages-layout) pour créer un RCL qui expose du contenu dans `~/Pages` plutôt que `~/Areas/Pages` .

## <a name="reference-rcl-content"></a>Référencer le contenu RCL

La RCL peut être référencée par :

* Un package NuGet. Consultez [Création de packages NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) et [Créer et publier un package NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* Un fichier *{NomProjet}.csproj*. Consultez [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a>Procédure pas à pas : création d’un projet RCL et utilisation à partir d’un Razor projet pages

Vous pouvez télécharger et tester le [projet complet](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) au lieu de le créer de toutes pièces. L’exemple proposé sous forme de téléchargement contient du code et des liens supplémentaires qui facilitent le test du projet. Si vous souhaitez commenter le mode d’obtention des exemples (téléchargement ou création au moyen d’instructions détaillées), entrez vos commentaires dans [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6098).

### <a name="test-the-download-app"></a>Tester l’application téléchargée

Si vous n’avez pas téléchargé l’application complète et que vous souhaitez créer le projet pas à pas, passez à la [section suivante](#create-an-rcl).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ouvrez le fichier *.sln* dans Visual Studio. Exécutez l'application.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

À partir d’une invite de commandes dans le répertoire *cli*, générez la RCL et l’application web.

```dotnetcli
dotnet build
```

Passez au répertoire *WebApp1* et exécutez l’application :

```dotnetcli
dotnet run
```

---

Suivez les instructions contenues dans [Test WebApp1](#test-webapp1)

## <a name="create-an-rcl"></a>Créer un RCL

Dans cette section, un RCL est créé. Razor des fichiers sont ajoutés à RCL.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Créez le projet RCL :

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.
* Sélectionnez **Application web ASP.NET Core**.
* Nommez l’application **Razor UIClassLib** > **OK**.
* Vérifiez que **ASP.NET Core 2.1** ou ultérieur est sélectionné.
* Sélectionnez **Razor bibliothèque de classes** > **OK**.
* Ajoutez un Razor fichier de vue partielle nommé *Razor UIClassLib/Areas/MyFeature/pages/shared/_Message. cshtml*.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

À partir de la ligne de commande, exécutez ce qui suit :

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Les commandes précédentes :

* Crée le `RazorUIClassLib` RCL.
* Crée une Razor _Message page et l’ajoute à RCL. Le paramètre `-np` crée la page sans `PageModel`.
* Crée un fichier [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) et l’ajoute à RCL.

Le fichier *_ViewStart. cshtml* est requis pour utiliser la disposition du Razor projet pages (qui est ajouté dans la section suivante).

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a>Ajouter Razor des fichiers et des dossiers au projet

* Remplacez le balisage dans *Razor UIClassLib/Areas/MyFeature/pages/Shared/_Message. cshtml* par le code suivant :

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Remplacez le balisage dans *Razor UIClassLib/Areas/MyFeature/pages/Page1. cshtml* par le code suivant :

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` est nécessaire pour utiliser la vue partielle (`<partial name="_Message" />`). Au lieu d’inclure la directive `@addTagHelper`, vous pouvez ajouter un fichier *_ViewImports.cshtml*. Par exemple :

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  Pour plus d’informations sur *_ViewImports. cshtml*, consultez [importation de directives partagées](xref:mvc/views/layout#importing-shared-directives)

* Générez la bibliothèque de classes pour vérifier l’absence d’erreurs de compilateur :

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

La sortie de la génération contient *RazorUIClassLib.dll* et *RazorUIClassLib.Views.dll*. *RazorUIClassLib.Views.dll* contient le Razor contenu compilé.

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a>Utiliser la Razor bibliothèque d’interface utilisateur à partir d’un Razor projet pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Créez l' Razor application Web pages :

* Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur la solution > **Ajouter** >  **Nouveau projet**.
* Sélectionnez **Application web ASP.NET Core**.
* Nommez l’application **WebApp1**.
* Vérifiez que **ASP.NET Core 2.1** ou ultérieur est sélectionné.
* Sélectionnez **Application web** > **OK**.

* Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **WebApp1**, puis sélectionnez **Définir comme projet de démarrage**.
* Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **WebApp1**, puis sélectionnez **Dépendances de build** > **Dépendances du projet**.
* Vérifiez **Razor UIClassLib** comme dépendance de **application Web 1**.
* Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **WebApp1**, puis sélectionnez **Ajouter** > **Référence**.
* Dans la boîte de dialogue **Gestionnaire de références** , activez la case à cocher **Razor UIClassLib** > **OK**.

Exécutez l'application.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Créez une Razor application Web pages et un fichier solution contenant l' Razor application pages et le RCL :

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Générez et exécutez l’application web :

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>Tester WebApp1

Accédez à `/MyFeature/Page1` pour vérifier que la Razor bibliothèque de classes d’interface utilisateur est en cours d’utilisation.

## <a name="override-views-partial-views-and-pages"></a>Substituer des vues, des vues partielles et des pages

Lorsqu’une vue, une vue partielle ou une Razor page se trouve à la fois dans l’application Web et le RCL, le Razor balisage (fichier *. cshtml* ) dans l’application Web est prioritaire. Par exemple, ajoutez *application Web 1/Areas/MyFeature/pages/Page1. cshtml* à application Web 1, et Page1 dans le application Web 1 aura priorité sur Page1 dans le RCL.

Dans l’exemple proposé sous forme de téléchargement, renommez *WebApp1/Areas/MyFeature2* en *WebApp1/Areas/MyFeature* pour tester la priorité.

Copiez la vue partielle *Razor UIClassLib/zones/MyFeature/pages/Shared/_Message. cshtml* sur *application Web 1/Areas/MyFeature/pages/Shared/_Message. cshtml*. Mettez à jour le balisage pour indiquer le nouvel emplacement. Générez et exécutez l’application pour vérifier que la version de la vue partielle de l’application est utilisée.

### <a name="rcl-pages-layout"></a>Mise en page des pages RCL

Pour faire référence au contenu RCL comme s’il fait partie du dossier *pages* de l’application Web, créez le projet RCL avec la structure de fichiers suivante :

* *RazorUIClassLib/pages*
* *RazorUIClassLib/pages/partagé*

Supposons que *Razor UIClassLib/pages/Shared* contient deux fichiers partiels : *_Header. cshtml* et *_Footer. cshtml*. Les `<partial>` balises peuvent être ajoutées au fichier *_Layout. cshtml* :

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

::: moniker range=">= aspnetcore-5.0"

* <xref:blazor/components/class-libraries>
* <xref:blazor/components/css-isolation#razor-class-library-rcl-support>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:blazor/components/class-libraries>

::: moniker-end

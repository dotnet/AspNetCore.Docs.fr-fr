# <a name="contribute-to-the-aspnet-core-documentation"></a><span data-ttu-id="89a24-101">Contribuer à la documentation ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="89a24-101">Contribute to the ASP.NET Core documentation</span></span>

<span data-ttu-id="89a24-102">Ce document aborde le processus de contribution aux articles et exemples de code qui sont hébergés sur le [site de la documentation ASP.NET](https://docs.microsoft.com/aspnet/).</span><span class="sxs-lookup"><span data-stu-id="89a24-102">This document covers the process for contributing to the articles and code samples that are hosted on the [ASP.NET documentation site](https://docs.microsoft.com/aspnet/).</span></span> <span data-ttu-id="89a24-103">Les corrections de fautes de frappe et les nouveaux articles sont des contributions bienvenues.</span><span class="sxs-lookup"><span data-stu-id="89a24-103">Typo corrections and new articles are welcome contributions.</span></span>

## <a name="how-to-make-a-simple-correction-or-suggestion"></a><span data-ttu-id="89a24-104">Comment apporter une correction ou suggestion simple</span><span class="sxs-lookup"><span data-stu-id="89a24-104">How to make a simple correction or suggestion</span></span>

<span data-ttu-id="89a24-105">Les articles sont stockés dans le dépôt en tant que fichiers Markdown.</span><span class="sxs-lookup"><span data-stu-id="89a24-105">Articles are stored in the repository as Markdown files.</span></span> <span data-ttu-id="89a24-106">Pour apporter des modifications simples au contenu d’un fichier Markdown, dans le navigateur, vous devez sélectionner le lien **Modifier** dans le coin supérieur droit de la fenêtre du navigateur.</span><span class="sxs-lookup"><span data-stu-id="89a24-106">Simple changes to the content of a Markdown file are made in the browser by selecting the **Edit** link in the upper-right corner of the browser window.</span></span> <span data-ttu-id="89a24-107">(Dans une fenêtre de navigateur fine, développez la barre d' **options** pour afficher le lien **modifier** .) Suivez les instructions pour créer une demande de tirage (pull request).</span><span class="sxs-lookup"><span data-stu-id="89a24-107">(In a narrow browser window, expand the **options** bar to see the **Edit** link.) Follow the directions to create a pull request (PR).</span></span> <span data-ttu-id="89a24-108">Nous examinerons la demande de tirage et l’accepterons ou suggérerons des modifications.</span><span class="sxs-lookup"><span data-stu-id="89a24-108">We will review the PR and accept it or suggest changes.</span></span>

## <a name="how-to-make-a-more-complex-submission"></a><span data-ttu-id="89a24-109">Comment effectuer une soumission plus complexe</span><span class="sxs-lookup"><span data-stu-id="89a24-109">How to make a more complex submission</span></span>

<span data-ttu-id="89a24-110">Vous devez avoir une connaissance élémentaire de [Git et GitHub.com](https://guides.github.com/activities/hello-world/).</span><span class="sxs-lookup"><span data-stu-id="89a24-110">You need a basic understanding of [Git and GitHub.com](https://guides.github.com/activities/hello-world/).</span></span>

* <span data-ttu-id="89a24-111">Ouvrez un [problème](https://github.com/dotnet/AspNetCore.Docs/issues/new) décrivant ce que vous voulez faire, par exemple changer un article existant ou en créer un.</span><span class="sxs-lookup"><span data-stu-id="89a24-111">Open an [issue](https://github.com/dotnet/AspNetCore.Docs/issues/new) describing what you want to do, such as changing an existing article or creating a new one.</span></span> <span data-ttu-id="89a24-112">Nous demandons souvent le plan des nouvelles rubriques suggérées.</span><span class="sxs-lookup"><span data-stu-id="89a24-112">We often request an outline for a new topic suggestion.</span></span> <span data-ttu-id="89a24-113">Attendez l’approbation de l’équipe avant de vous investir davantage.</span><span class="sxs-lookup"><span data-stu-id="89a24-113">Wait for approval from the team before you invest much time.</span></span>
* <span data-ttu-id="89a24-114">Dupliquez (fork) le dépôt [aspnet/Docs](https://github.com/dotnet/AspNetCore.Docs/) et créez une branche pour vos modifications.</span><span class="sxs-lookup"><span data-stu-id="89a24-114">Fork the [aspnet/Docs](https://github.com/dotnet/AspNetCore.Docs/) repo and create a branch for your changes.</span></span>
* <span data-ttu-id="89a24-115">Soumettez une demande de tirage dans la branche master avec vos modifications.</span><span class="sxs-lookup"><span data-stu-id="89a24-115">Submit a PR to master with your changes.</span></span>
* <span data-ttu-id="89a24-116">Si votre demande de tirage se voit attribuer l’étiquette « cla-required », [signez le contrat CLA (Contribution License Agreement)](https://cla.dotnetfoundation.org/).</span><span class="sxs-lookup"><span data-stu-id="89a24-116">If your PR has the label 'cla-required' assigned, [complete the Contribution License Agreement (CLA)](https://cla.dotnetfoundation.org/).</span></span>
* <span data-ttu-id="89a24-117">Répondez aux commentaires sur la demande de tirage.</span><span class="sxs-lookup"><span data-stu-id="89a24-117">Respond to PR feedback.</span></span>

<span data-ttu-id="89a24-118">Pour obtenir un exemple dans lequel ce processus a entraîné la publication d’un nouvel article, consultez le [problème 1477](https://github.com/dotnet/docs/issues/1477) et la [demande de tirage (Pull request) 18955](https://github.com/dotnet/docs/pull/18955) dans le référentiel docs .net.</span><span class="sxs-lookup"><span data-stu-id="89a24-118">For an example where this process led to publication of a new article, see [Issue 1477](https://github.com/dotnet/docs/issues/1477) and [Pull Request 18955](https://github.com/dotnet/docs/pull/18955) in the .NET Docs repository.</span></span> <span data-ttu-id="89a24-119">Le nouvel article [utilise la couverture du code pour les tests unitaires](/dotnet/core/testing/unit-testing-code-coverage).</span><span class="sxs-lookup"><span data-stu-id="89a24-119">The new article is [Use code coverage for unit testing](/dotnet/core/testing/unit-testing-code-coverage).</span></span>

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a><span data-ttu-id="89a24-120">Extension Docs Authoring Pack dans Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89a24-120">Docs Authoring Pack extension in Visual Studio Code</span></span>

<span data-ttu-id="89a24-121">Si vous utilisez Visual Studio Code pour contribuer à la documentation ASP.NET, vous pouvez augmenter votre productivité en installant l’extension [Docs Authoring Pack](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack).</span><span class="sxs-lookup"><span data-stu-id="89a24-121">If you use Visual Studio Code to contribute to the ASP.NET documentation, you can boost your productivity by installing the [Docs Authoring Pack](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack) extension.</span></span> <span data-ttu-id="89a24-122">L’extension fournit un éventail d’outils qui facilitent la vérification (linting) du code Markdown, la vérification orthographique du code et l’utilisation des modèles d’article.</span><span class="sxs-lookup"><span data-stu-id="89a24-122">The extension provides a variety of tools that helps with Markdown linting, code spell checking, and article templates.</span></span>

## <a name="markdown-syntax"></a><span data-ttu-id="89a24-123">Syntaxe Markdown</span><span class="sxs-lookup"><span data-stu-id="89a24-123">Markdown syntax</span></span>

<span data-ttu-id="89a24-124">Les articles sont écrits en [DFM (DocFX Flavored Markdown)](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), qui est un sur-ensemble de [GFM (GitHub Flavored Markdown)](https://guides.github.com/features/mastering-markdown/).</span><span class="sxs-lookup"><span data-stu-id="89a24-124">Articles are written in [DocFx-flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), which is a superset of [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span></span> <span data-ttu-id="89a24-125">Pour obtenir des exemples de syntaxe DFM illustrant les fonctionnalités de l’interface utilisateur couramment utilisées dans la documentation ASP.NET, consultez [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md) dans le guide de style du dépôt .NET Docs.</span><span class="sxs-lookup"><span data-stu-id="89a24-125">For examples of DFM syntax for UI features commonly used in the ASP.NET documentation, see [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md) in the .NET Docs repo style guide.</span></span> 

## <a name="folder-structure-conventions"></a><span data-ttu-id="89a24-126">Conventions relatives à la structure des dossiers</span><span class="sxs-lookup"><span data-stu-id="89a24-126">Folder structure conventions</span></span>

<span data-ttu-id="89a24-127">Pour chaque fichier Markdown, un dossier pour les images et un dossier pour l’exemple de code peuvent exister.</span><span class="sxs-lookup"><span data-stu-id="89a24-127">For each Markdown file, a folder for images and a folder for sample code may exist.</span></span> <span data-ttu-id="89a24-128">Si l’article est [fundamentals/configuration/index.md](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/fundamentals/configuration/index.md), les images se trouvent dans [fundamentals/configuration/index/\_static](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/_static), tandis que les exemples de fichiers de projet d’application se trouvent dans [fundamentals/configuration/index/sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample).</span><span class="sxs-lookup"><span data-stu-id="89a24-128">If the article is [fundamentals/configuration/index.md](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/fundamentals/configuration/index.md), the images are in [fundamentals/configuration/index/\_static](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/_static) and the sample app project files are in [fundamentals/configuration/index/sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample).</span></span> <span data-ttu-id="89a24-129">Une image dans le fichier *fundamentals/configuration/index.md* est affichée par le code Markdown suivant :</span><span class="sxs-lookup"><span data-stu-id="89a24-129">An image in the *fundamentals/configuration/index.md* file is rendered by the following Markdown:</span></span>

```md
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

<span data-ttu-id="89a24-130">Toutes les images doivent avoir un [texte de remplacement (alt)](https://wikipedia.org/wiki/Alt_attribute).</span><span class="sxs-lookup"><span data-stu-id="89a24-130">All images should have [alternative (alt) text](https://wikipedia.org/wiki/Alt_attribute).</span></span> <span data-ttu-id="89a24-131">Pour obtenir des conseils sur la spécification du texte de remplacement, consultez les ressources en ligne, telles que [WebAIM: Alternative Text](https://webaim.org/techniques/alttext/) (WebAIM : texte de remplacement).</span><span class="sxs-lookup"><span data-stu-id="89a24-131">For advice on specifying alt text, see online resources, such as [WebAIM: Alternative Text](https://webaim.org/techniques/alttext/).</span></span>

<span data-ttu-id="89a24-132">Utilisez des minuscules pour les noms de fichiers Markdown et les noms de fichiers image.</span><span class="sxs-lookup"><span data-stu-id="89a24-132">Use lowercase for Markdown file names and image file names.</span></span>

## <a name="internal-links"></a><span data-ttu-id="89a24-133">Liens internes</span><span class="sxs-lookup"><span data-stu-id="89a24-133">Internal links</span></span>

<span data-ttu-id="89a24-134">Les liens internes doivent utiliser l’`uid` de l’article cible avec un lien xref (le texte du lien est défini sur le titre du contenu lié) :</span><span class="sxs-lookup"><span data-stu-id="89a24-134">Internal links should use the `uid` of the target article with an xref link (link text is set to the linked content's title):</span></span>

```md
<xref:uid_of_the_topic>
```

<span data-ttu-id="89a24-135">Si le titre de l’article ne convient pas pour le texte du lien (par exemple, un mot ou une expression dans une phrase est le texte du lien), spécifiez le lien xref et le texte du lien comme suit :</span><span class="sxs-lookup"><span data-stu-id="89a24-135">If the title of the article is unsuitable for link text (for example, a word or phrase in a sentence is the link text), specify the xref link and link text with the following:</span></span>

```md
[link text](xref:uid_of_the_topic)
```

<span data-ttu-id="89a24-136">Pour plus d’informations, consultez [DocFX Cross Reference](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).</span><span class="sxs-lookup"><span data-stu-id="89a24-136">For more information, see the [DocFX Cross Reference](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).</span></span>

## <a name="images-and-screenshots"></a><span data-ttu-id="89a24-137">Images et captures d’écran</span><span class="sxs-lookup"><span data-stu-id="89a24-137">Images and screenshots</span></span>

<span data-ttu-id="89a24-138">N’incluez pas d’images avec les articles, sauf :</span><span class="sxs-lookup"><span data-stu-id="89a24-138">Don't include images with articles, except:</span></span>

* <span data-ttu-id="89a24-139">Dans les tutoriels d’intégration de base (débutant).</span><span class="sxs-lookup"><span data-stu-id="89a24-139">In basic onboarding (beginner) tutorials.</span></span>
* <span data-ttu-id="89a24-140">Quand une image est nécessaire par souci de clarté.</span><span class="sxs-lookup"><span data-stu-id="89a24-140">When an image is needed for clarity.</span></span>

<span data-ttu-id="89a24-141">Ces restrictions réduisent la taille du dépôt.</span><span class="sxs-lookup"><span data-stu-id="89a24-141">These restrictions reduce the size of the repository.</span></span>

<span data-ttu-id="89a24-142">Éventuellement, assurez-vous que les images et les captures d’écran utilisées dans la documentation sont compressées, ce qui permet de réduire la taille des fichiers et d’optimiser le chargement des pages.</span><span class="sxs-lookup"><span data-stu-id="89a24-142">As an optional step, ensure that any images and screenshots used in the documentation are compressed, which helps with file size and page load performance.</span></span> <span data-ttu-id="89a24-143">Parmi les outils connus, citons TinyPNG (utilisable par le biais du [site web TinyPNG](https://tinypng.com/) ou de l’[API TinyPNG](https://tinypng.com/developers)) ou l’extension Visual Studio [Image Optimizer](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer).</span><span class="sxs-lookup"><span data-stu-id="89a24-143">A few popular tools include TinyPNG (using the [TinyPNG website](https://tinypng.com/) or the [TinyPNG API](https://tinypng.com/developers)) or the [Image Optimizer](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) Visual Studio extension.</span></span> 

## <a name="code-snippets"></a><span data-ttu-id="89a24-144">Extraits de code</span><span class="sxs-lookup"><span data-stu-id="89a24-144">Code snippets</span></span>

<span data-ttu-id="89a24-145">Les articles contiennent fréquemment des extraits de code pour illustrer les points abordés.</span><span class="sxs-lookup"><span data-stu-id="89a24-145">Articles frequently contain code snippets to illustrate points.</span></span> <span data-ttu-id="89a24-146">DFM vous permet de copier le code dans le fichier Markdown ou de faire référence à un fichier de code distinct.</span><span class="sxs-lookup"><span data-stu-id="89a24-146">DFM allows you to copy code into the Markdown file or refer to a separate code file.</span></span> <span data-ttu-id="89a24-147">Nous préférons l’utilisation de fichiers de code distincts si possible afin de limiter les risques d’erreurs dans le code.</span><span class="sxs-lookup"><span data-stu-id="89a24-147">We prefer to use separate code files whenever possible to minimize the chance of errors in the code.</span></span> <span data-ttu-id="89a24-148">Les fichiers de code sont stockés dans le dépôt, conformément à la structure de dossiers décrite précédemment pour les exemples de projets.</span><span class="sxs-lookup"><span data-stu-id="89a24-148">The code files are stored in the repo using the folder structure described earlier for sample projects.</span></span> 

<span data-ttu-id="89a24-149">Les exemples suivants illustrent la [syntaxe d’extraits de code DFM](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) à utiliser dans un fichier *configuration/index.md*.</span><span class="sxs-lookup"><span data-stu-id="89a24-149">The following examples illustrate [DFM code snippet syntax](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) for use in a *configuration/index.md* file.</span></span>

<span data-ttu-id="89a24-150">Pour afficher un fichier de code entier en tant qu’extrait de code :</span><span class="sxs-lookup"><span data-stu-id="89a24-150">To render an entire code file as a snippet:</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs)]
```

<span data-ttu-id="89a24-151">Pour afficher une partie d’un fichier en tant qu’extrait de code en utilisant des numéros de ligne :</span><span class="sxs-lookup"><span data-stu-id="89a24-151">To render a portion of a file as a snippet by using line numbers:</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

<span data-ttu-id="89a24-152">Pour les extraits de code C#, référencez une [région C#](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region).</span><span class="sxs-lookup"><span data-stu-id="89a24-152">For C# snippets, reference a [C# region](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region).</span></span> <span data-ttu-id="89a24-153">Si possible, utilisez des régions plutôt que des numéros de ligne, car les numéros de ligne dans un fichier de code ont tendance à changer au point de ne plus être synchronisés avec les références de numéro de ligne dans un fichier Markdown.</span><span class="sxs-lookup"><span data-stu-id="89a24-153">Whenever possible, use regions rather than line numbers because line numbers in a code file tend to change and become out of sync with line number references in Markdown.</span></span> <span data-ttu-id="89a24-154">Les régions C# peuvent être imbriquées.</span><span class="sxs-lookup"><span data-stu-id="89a24-154">C# regions can be nested.</span></span> <span data-ttu-id="89a24-155">Si vous référencez la région externe, les directives `#region` et `#endregion` internes ne sont pas affichées dans un extrait de code.</span><span class="sxs-lookup"><span data-stu-id="89a24-155">If referencing the outer region, the inner `#region` and `#endregion` directives aren't rendered in a snippet.</span></span> 

<span data-ttu-id="89a24-156">Pour afficher une région C# nommée « snippet_Example » :</span><span class="sxs-lookup"><span data-stu-id="89a24-156">To render a C# region named "snippet_Example":</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

<span data-ttu-id="89a24-157">Pour mettre en surbrillance les lignes sélectionnées dans un extrait de code affiché (généralement sous la forme d’un arrière-plan jaune) :</span><span class="sxs-lookup"><span data-stu-id="89a24-157">To highlight selected lines in a rendered snippet (usually renders as yellow background color):</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a><span data-ttu-id="89a24-158">Tester les modifications avec DocFX</span><span class="sxs-lookup"><span data-stu-id="89a24-158">Test changes with DocFX</span></span>

<span data-ttu-id="89a24-159">Testez vos modifications avec l’[outil en ligne de commande DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), qui crée une version hébergée localement du site.</span><span class="sxs-lookup"><span data-stu-id="89a24-159">Test your changes with the [DocFX command-line tool](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), which creates a locally hosted version of the site.</span></span> <span data-ttu-id="89a24-160">DocFX n’affiche pas le style et les extensions de site créés pour docs.microsoft.com.</span><span class="sxs-lookup"><span data-stu-id="89a24-160">DocFX doesn't render style and site extensions created for docs.microsoft.com.</span></span>

<span data-ttu-id="89a24-161">DocFX nécessite :</span><span class="sxs-lookup"><span data-stu-id="89a24-161">DocFX requires:</span></span>

* <span data-ttu-id="89a24-162">.NET Framework sur Windows.</span><span class="sxs-lookup"><span data-stu-id="89a24-162">.NET Framework on Windows.</span></span>
* <span data-ttu-id="89a24-163">Mono pour Linux ou macOS.</span><span class="sxs-lookup"><span data-stu-id="89a24-163">Mono for Linux or macOS.</span></span> 

### <a name="windows-instructions"></a><span data-ttu-id="89a24-164">Instructions pour Windows</span><span class="sxs-lookup"><span data-stu-id="89a24-164">Windows instructions</span></span>

* <span data-ttu-id="89a24-165">Téléchargez et décompressez *docfx.zip* à partir des [versions DocFX](https://github.com/dotnet/docfx/releases).</span><span class="sxs-lookup"><span data-stu-id="89a24-165">Download and unzip *docfx.zip* from [DocFX releases](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="89a24-166">Ajoutez DocFX à votre chemin (PATH).</span><span class="sxs-lookup"><span data-stu-id="89a24-166">Add DocFX to your PATH.</span></span>
* <span data-ttu-id="89a24-167">Dans un shell de commande, accédez au dossier qui contient le fichier *docfx.json* (*aspnet* pour du contenu ASP.NET ou *aspnetcore* pour du contenu ASP.NET Core) et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="89a24-167">In a command shell, navigate to the folder that contains the *docfx.json* file (*aspnet* for ASP.NET content or *aspnetcore* for ASP.NET Core content) and run the following command:</span></span>

  ```console
  docfx --serve
  ```

* <span data-ttu-id="89a24-168">Dans un navigateur, accédez à `http://localhost:8080/group1-dest/`.</span><span class="sxs-lookup"><span data-stu-id="89a24-168">In a browser, navigate to `http://localhost:8080/group1-dest/`.</span></span>

### <a name="mono-instructions"></a><span data-ttu-id="89a24-169">Instructions pour Mono</span><span class="sxs-lookup"><span data-stu-id="89a24-169">Mono instructions</span></span>

* <span data-ttu-id="89a24-170">Installez Mono via Homebrew :</span><span class="sxs-lookup"><span data-stu-id="89a24-170">Install Mono via Homebrew:</span></span>

  ```console
  brew install mono
  ```

* <span data-ttu-id="89a24-171">Téléchargez la [dernière version de DocFX](https://github.com/dotnet/docfx/releases).</span><span class="sxs-lookup"><span data-stu-id="89a24-171">Download the [latest version of DocFX](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="89a24-172">Extrayez l’archive dans *$HOME/bin/docfx*.</span><span class="sxs-lookup"><span data-stu-id="89a24-172">Extract the archive to *$HOME/bin/docfx*.</span></span>
* <span data-ttu-id="89a24-173">Créez une paire d’alias pour **docfx** dans un interpréteur de commandes bash.</span><span class="sxs-lookup"><span data-stu-id="89a24-173">Create a pair of aliases for **docfx** in a bash shell.</span></span> <span data-ttu-id="89a24-174">Le premier alias est utilisé pour générer la documentation.</span><span class="sxs-lookup"><span data-stu-id="89a24-174">The first alias is used to build the documentation.</span></span> <span data-ttu-id="89a24-175">Le deuxième alias est utilisé pour générer et diffuser la documentation.</span><span class="sxs-lookup"><span data-stu-id="89a24-175">The second alias is used to build and serve the documentation.</span></span>

  ```console
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```

* <span data-ttu-id="89a24-176">Dans un shell de commande, accédez au dossier qui contient le fichier *docfx.json* (*aspnet* pour du contenu ASP.NET ou *aspnetcore* pour du contenu ASP.NET Core) et exécutez la commande suivante pour créer et diffuser les documents via son alias :</span><span class="sxs-lookup"><span data-stu-id="89a24-176">In a command shell, navigate to the folder that contains the *docfx.json* file (*aspnet* for ASP.NET content or *aspnetcore* for ASP.NET Core content) and run the following command to build and serve the docs via its alias:</span></span>

  ```console
  docfx-serve
  ```

* <span data-ttu-id="89a24-177">Dans un navigateur, accédez à `http://localhost:8080/group1-dest/`.</span><span class="sxs-lookup"><span data-stu-id="89a24-177">In a browser, navigate to `http://localhost:8080/group1-dest/`.</span></span>

## <a name="voice-and-tone"></a><span data-ttu-id="89a24-178">Voix et ton</span><span class="sxs-lookup"><span data-stu-id="89a24-178">Voice and tone</span></span>

<span data-ttu-id="89a24-179">Notre objectif est d’écrire une documentation facile à comprendre par le plus grand nombre.</span><span class="sxs-lookup"><span data-stu-id="89a24-179">Our goal is to write documentation that is easily understandable by the widest possible audience.</span></span> <span data-ttu-id="89a24-180">À cette fin, nous avons établi des instructions sur le style que nos collaborateurs sont invités à suivre.</span><span class="sxs-lookup"><span data-stu-id="89a24-180">To that end, we established guidelines for writing style that we ask our contributors to follow.</span></span> <span data-ttu-id="89a24-181">Pour plus d’informations, consultez [Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) dans le dépôt .NET.</span><span class="sxs-lookup"><span data-stu-id="89a24-181">For more information, see [Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) in the .NET repo.</span></span>

## <a name="microsoft-writing-style-guide"></a><span data-ttu-id="89a24-182">Guide de style d’écriture Microsoft</span><span class="sxs-lookup"><span data-stu-id="89a24-182">Microsoft Writing Style Guide</span></span>

<span data-ttu-id="89a24-183">Le [Guide de style d’écriture Microsoft](https://docs.microsoft.com/style-guide/welcome/) fournit des instructions sur le style et la terminologie pour toutes les formes de communication au sujet d’une technologie, notamment la documentation ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="89a24-183">The [Microsoft Writing Style Guide](https://docs.microsoft.com/style-guide/welcome/) provides writing style and terminology guidance for all forms of technology communication, including the ASP.NET Core documentation.</span></span>

## <a name="redirects"></a><span data-ttu-id="89a24-184">Redirection</span><span class="sxs-lookup"><span data-stu-id="89a24-184">Redirects</span></span>

<span data-ttu-id="89a24-185">Si vous supprimez un article, changez son nom de fichier ou déplacez-le vers un autre dossier, créez une redirection afin que les personnes qui ont créé un signet pour l’article ne reçoivent pas une erreur *404 Non trouvé*.</span><span class="sxs-lookup"><span data-stu-id="89a24-185">If you delete an article, change its file name, or move it to a different folder, create a redirect so that people who bookmarked the article don't receive a *404 Not Found* error.</span></span> <span data-ttu-id="89a24-186">Ajoutez les redirections au [fichier de redirection dans la branche master](https://github.com/dotnet/AspNetCore.Docs/blob/master/.openpublishing.redirection.json).</span><span class="sxs-lookup"><span data-stu-id="89a24-186">Add redirects to the [master redirect file](https://github.com/dotnet/AspNetCore.Docs/blob/master/.openpublishing.redirection.json).</span></span>

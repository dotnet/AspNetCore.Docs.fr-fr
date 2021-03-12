# <a name="contribute-to-the-aspnet-core-documentation"></a>Contribuer à la documentation ASP.NET Core

Ce document aborde le processus de contribution aux articles et exemples de code qui sont hébergés sur le [site de la documentation ASP.NET](https://docs.microsoft.com/aspnet/). Les corrections de fautes de frappe et les nouveaux articles sont des contributions bienvenues.

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>Comment apporter une correction ou suggestion simple

Les articles sont stockés dans le dépôt en tant que fichiers Markdown. Pour apporter des modifications simples au contenu d’un fichier Markdown, dans le navigateur, vous devez sélectionner le lien **Modifier** dans le coin supérieur droit de la fenêtre du navigateur. (Dans une fenêtre de navigateur fine, développez la barre d' **options** pour afficher le lien **modifier** .) Suivez les instructions pour créer une demande de tirage (pull request). Nous examinerons la demande de tirage et l’accepterons ou suggérerons des modifications.

## <a name="how-to-make-a-more-complex-submission"></a>Comment effectuer une soumission plus complexe

Vous devez avoir une connaissance élémentaire de [Git et GitHub.com](https://guides.github.com/activities/hello-world/).

* Ouvrez un [problème](https://github.com/dotnet/AspNetCore.Docs/issues/new) décrivant ce que vous voulez faire, par exemple changer un article existant ou en créer un. Nous demandons souvent le plan des nouvelles rubriques suggérées. Attendez l’approbation de l’équipe avant de vous investir davantage.
* Dupliquez (fork) le dépôt [aspnet/Docs](https://github.com/dotnet/AspNetCore.Docs/) et créez une branche pour vos modifications.
* Envoyez une demande de tirage (PR) à la branche *principale* avec vos modifications.
* Si votre demande de tirage se voit attribuer l’étiquette « cla-required », [signez le contrat CLA (Contribution License Agreement)](https://cla.dotnetfoundation.org/).
* Répondez aux commentaires sur la demande de tirage.

Pour obtenir un exemple dans lequel ce processus a entraîné la publication d’un nouvel article, consultez le [problème 1477](https://github.com/dotnet/docs/issues/1477) et la [demande de tirage (Pull request) 18955](https://github.com/dotnet/docs/pull/18955) dans le référentiel docs .net. Le nouvel article [utilise la couverture du code pour les tests unitaires](/dotnet/core/testing/unit-testing-code-coverage).

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a>Extension Docs Authoring Pack dans Visual Studio Code

Si vous utilisez Visual Studio Code pour contribuer à la documentation ASP.NET, vous pouvez augmenter votre productivité en installant l’extension [Docs Authoring Pack](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack). L’extension fournit un éventail d’outils qui facilitent la vérification (linting) du code Markdown, la vérification orthographique du code et l’utilisation des modèles d’article.

## <a name="markdown-syntax"></a>Syntaxe Markdown

Les articles sont écrits en [DFM (DocFX Flavored Markdown)](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), qui est un sur-ensemble de [GFM (GitHub Flavored Markdown)](https://guides.github.com/features/mastering-markdown/). Pour obtenir des exemples de syntaxe DFM illustrant les fonctionnalités de l’interface utilisateur couramment utilisées dans la documentation ASP.NET, consultez [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/main/styleguide/template.md) dans le guide de style du dépôt .NET Docs. 

## <a name="folder-structure-conventions"></a>Conventions relatives à la structure des dossiers

Pour chaque fichier Markdown, un dossier pour les images et un dossier pour l’exemple de code peuvent exister. Si l’article est [fundamentals/configuration/index.md](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/fundamentals/configuration/index.md), les images se trouvent dans [fundamentals/configuration/index/\_static](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/_static), tandis que les exemples de fichiers de projet d’application se trouvent dans [fundamentals/configuration/index/sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/sample). Une image dans le fichier *fundamentals/configuration/index.md* est affichée par le code Markdown suivant :

```md
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

Toutes les images doivent avoir un [texte de remplacement (alt)](https://wikipedia.org/wiki/Alt_attribute). Pour obtenir des conseils sur la spécification du texte de remplacement, consultez les ressources en ligne, telles que [WebAIM: Alternative Text](https://webaim.org/techniques/alttext/) (WebAIM : texte de remplacement).

Utilisez des minuscules pour les noms de fichiers Markdown et les noms de fichiers image.

## <a name="internal-links"></a>Liens internes

Les liens internes doivent utiliser l’`uid` de l’article cible avec un lien xref (le texte du lien est défini sur le titre du contenu lié) :

```md
<xref:uid_of_the_topic>
```

Si le titre de l’article ne convient pas pour le texte du lien (par exemple, un mot ou une expression dans une phrase est le texte du lien), spécifiez le lien xref et le texte du lien comme suit :

```md
[link text](xref:uid_of_the_topic)
```

Pour plus d’informations, consultez [DocFX Cross Reference](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).

## <a name="images-and-screenshots"></a>Images et captures d’écran

N’incluez pas d’images avec les articles, sauf :

* Dans les tutoriels d’intégration de base (débutant).
* Quand une image est nécessaire par souci de clarté.

Ces restrictions réduisent la taille du dépôt.

Éventuellement, assurez-vous que les images et les captures d’écran utilisées dans la documentation sont compressées, ce qui permet de réduire la taille des fichiers et d’optimiser le chargement des pages. Parmi les outils connus, citons TinyPNG (utilisable par le biais du [site web TinyPNG](https://tinypng.com/) ou de l’[API TinyPNG](https://tinypng.com/developers)) ou l’extension Visual Studio [Image Optimizer](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer). 

## <a name="code-snippets"></a>Extraits de code

Les articles contiennent fréquemment des extraits de code pour illustrer les points abordés. DFM vous permet de copier le code dans le fichier Markdown ou de faire référence à un fichier de code distinct. Nous préférons l’utilisation de fichiers de code distincts si possible afin de limiter les risques d’erreurs dans le code. Les fichiers de code sont stockés dans le dépôt, conformément à la structure de dossiers décrite précédemment pour les exemples de projets. 

Les exemples suivants illustrent la [syntaxe d’extraits de code DFM](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) à utiliser dans un fichier *configuration/index.md*.

Pour afficher un fichier de code entier en tant qu’extrait de code :

```md
[!code-csharp[](configuration/index/sample/Program.cs)]
```

Pour afficher une partie d’un fichier en tant qu’extrait de code en utilisant des numéros de ligne :

```md
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

Pour les extraits de code C#, référencez une [région C#](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region). Si possible, utilisez des régions plutôt que des numéros de ligne, car les numéros de ligne dans un fichier de code ont tendance à changer au point de ne plus être synchronisés avec les références de numéro de ligne dans un fichier Markdown. Les régions C# peuvent être imbriquées. Si vous référencez la région externe, les directives `#region` et `#endregion` internes ne sont pas affichées dans un extrait de code. 

Pour afficher une région C# nommée « snippet_Example » :

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

Pour mettre en surbrillance les lignes sélectionnées dans un extrait de code affiché (généralement sous la forme d’un arrière-plan jaune) :

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a>Tester les modifications avec DocFX

Testez vos modifications avec l’[outil en ligne de commande DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), qui crée une version hébergée localement du site. DocFX n’affiche pas le style et les extensions de site créés pour docs.microsoft.com.

DocFX nécessite :

* .NET Framework sur Windows.
* Mono pour Linux ou macOS. 

### <a name="windows-instructions"></a>Instructions pour Windows

* Téléchargez et décompressez *docfx.zip* à partir des [versions DocFX](https://github.com/dotnet/docfx/releases).
* Ajoutez DocFX à votre chemin (PATH).
* Dans un shell de commande, accédez au dossier qui contient le fichier *docfx.json* (*aspnet* pour du contenu ASP.NET ou *aspnetcore* pour du contenu ASP.NET Core) et exécutez la commande suivante :

  ```console
  docfx --serve
  ```

* Dans un navigateur, accédez à `http://localhost:8080/group1-dest/`.

### <a name="mono-instructions"></a>Instructions pour Mono

* Installez Mono via Homebrew :

  ```console
  brew install mono
  ```

* Téléchargez la [dernière version de DocFX](https://github.com/dotnet/docfx/releases).
* Extrayez l’archive dans *$HOME/bin/docfx*.
* Créez une paire d’alias pour **docfx** dans un interpréteur de commandes bash. Le premier alias est utilisé pour générer la documentation. Le deuxième alias est utilisé pour générer et diffuser la documentation.

  ```console
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```

* Dans un shell de commande, accédez au dossier qui contient le fichier *docfx.json* (*aspnet* pour du contenu ASP.NET ou *aspnetcore* pour du contenu ASP.NET Core) et exécutez la commande suivante pour créer et diffuser les documents via son alias :

  ```console
  docfx-serve
  ```

* Dans un navigateur, accédez à `http://localhost:8080/group1-dest/`.

## <a name="voice-and-tone"></a>Voix et ton

Notre objectif est d’écrire une documentation facile à comprendre par le plus grand nombre. À cette fin, nous avons établi des instructions sur le style que nos collaborateurs sont invités à suivre. Pour plus d’informations, consultez [Voice and tone guidelines](https://github.com/dotnet/docs/blob/main/styleguide/voice-tone.md) dans le dépôt .NET.

## <a name="microsoft-writing-style-guide"></a>Guide de style d’écriture Microsoft

Le [Guide de style d’écriture Microsoft](https://docs.microsoft.com/style-guide/welcome/) fournit des instructions sur le style et la terminologie pour toutes les formes de communication au sujet d’une technologie, notamment la documentation ASP.NET Core.

## <a name="redirects"></a>Redirection

Si vous supprimez un article, changez son nom de fichier ou déplacez-le vers un autre dossier, créez une redirection afin que les personnes qui ont créé un signet pour l’article ne reçoivent pas une erreur *404 Non trouvé*. Ajoutez des redirections au [fichier de redirection principal](https://github.com/dotnet/AspNetCore.Docs/blob/main/.openpublishing.redirection.json).

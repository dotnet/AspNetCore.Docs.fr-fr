---
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
ms.openlocfilehash: 05c94351ee4747813cfa8dc2318a6fc02c3a46bf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551369"
---
```console
npm run release
```

Cette commande génère les ressources côté client à utiliser lors de l’exécution de l’application. Les ressources sont placées dans le dossier *wwwroot*.

Webpack a effectué les tâches suivantes :

* Purge du contenu du répertoire *wwwroot*.
* Conversion de la machine à écrire en JavaScript dans un processus appelé *transpilation*.
* A tronqué le code JavaScript généré pour réduire la taille du fichier dans un processus connu sous le nom de *minimisation*.
* Copie des fichiers JavaScript, CSS et HTML traités à partir de *src* dans le répertoire *wwwroot*.
* Injection des éléments suivants dans le fichier *wwwroot/index.html* :
  * Une `<link>` balise, référençant le *wwwroot/main. \<hash\> fichier CSS* . Cette balise est placée immédiatement avant la balise `</head>` de fermeture.
  * Une `<script>` balise, référençant le minimisés *wwwroot/main. \<hash\> . fichier js* . Cette balise est placée immédiatement avant la balise `</body>` de fermeture.
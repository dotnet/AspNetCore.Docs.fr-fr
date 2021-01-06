---
title: Créez des applications Web progressifs avec ASP.NET Core Blazor WebAssembly
author: guardrex
description: Découvrez comment créer une Blazor application Web progressive (PWA) qui utilise des fonctionnalités de navigateur modernes pour se comporter comme une application de bureau.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/progressive-web-app
ms.openlocfilehash: f400319ef81b3d7768bdbdab84f46d3f9c50bb46
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "96855441"
---
# <a name="build-progressive-web-applications-with-aspnet-core-no-locblazor-webassembly"></a>Créez des applications Web progressifs avec ASP.NET Core Blazor WebAssembly

Par [Steve Sanderson](https://github.com/SteveSandersonMS)

Une application Web progressive (PWA) est généralement une application à page unique (SPA) qui utilise des API et des fonctionnalités de navigateur modernes pour se comporter comme une application de bureau. Blazor WebAssembly est une plate-forme d’application Web côté client basée sur des normes. elle peut donc utiliser n’importe quelle API de navigateur, y compris les API PWA requises pour les fonctionnalités suivantes :

* Travailler en mode hors connexion et se charger instantanément, indépendamment de la vitesse du réseau.
* S’exécutant dans sa propre fenêtre d’application, et pas seulement dans une fenêtre de navigateur.
* Lancé à partir du menu Démarrer, de l’ancrage ou de l’écran d’accueil du système d’exploitation de l’hôte.
* Réception de notifications push à partir d’un serveur principal, même si l’utilisateur n’utilise pas l’application.
* Mise à jour automatique en arrière-plan.

Le mot *progressif* est utilisé pour décrire de telles applications, car :

* Un utilisateur peut tout d’abord découvrir et utiliser l’application dans son navigateur Web comme n’importe quel autre SPA.
* Plus tard, l’utilisateur est en mesure de l’installer dans son système d’exploitation et d’activer les notifications push.

## <a name="create-a-project-from-the-pwa-template"></a>Créer un projet à partir du modèle PWA

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Quand vous créez une nouvelle **Blazor WebAssembly application** dans la boîte de dialogue **créer un nouveau projet** , activez la case à cocher **application Web progressive** :

![La case à cocher « application Web progressive » est activée dans la boîte de dialogue Nouveau projet de Visual Studio.](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

Créez un projet PWA dans une interface de commande avec le `--pwa` commutateur :

```dotnetcli
dotnet new blazorwasm -o MyNewProject --pwa
```

---

Éventuellement, PWA peut être configuré pour une application créée à partir du modèle ASP.NET Core hébergé. Le scénario PWA est indépendant du modèle d’hébergement.

## <a name="installation-and-app-manifest"></a>Manifeste d’installation et d’application

Quand vous visitez une application créée à l’aide du modèle PWA, les utilisateurs ont la possibilité d’installer l’application dans le menu Démarrer, l’ancre ou l’écran d’accueil de son système d’exploitation. La façon dont cette option est présentée dépend du navigateur de l’utilisateur. Quand vous utilisez des navigateurs basés sur Desktop chrome, tels que Edge ou chrome, un bouton **Ajouter** apparaît dans la barre d’URL. Une fois que l’utilisateur a sélectionné le bouton **Ajouter** , une boîte de dialogue de confirmation s’affiche :

![La boîte de dialogue de confirmation de Google Chrome présente un bouton installer pour l’utilisateur de l’application « my ::: No-Loc (éblouissant) :::P wa ».](progressive-web-app/_static/image2.png)

Sur iOS, les visiteurs peuvent installer le PWA à l’aide du bouton de **partage** de Safari et de l’option **Ajouter à homescreen** . Sur chrome pour Android, les utilisateurs doivent sélectionner le bouton de **menu** dans l’angle supérieur droit, puis **Ajouter à l’écran d’accueil**.

Une fois installé, l’application s’affiche dans sa propre fenêtre sans barre d’adresses :

![L’application’My ::: No-Loc (éblouissant) :::P wa’s’exécute dans Google Chrome sans barre d’adresses.](progressive-web-app/_static/image3.png)

Pour personnaliser le titre, le modèle de couleurs, l’icône ou d’autres détails de la fenêtre, consultez le `manifest.json` fichier dans le répertoire du projet `wwwroot` . Le schéma de ce fichier est défini par les normes Web. Pour plus d’informations, consultez [MDN Web docs : Web App manifest](https://developer.mozilla.org/docs/Web/Manifest).

## <a name="offline-support"></a>Prise en charge hors connexion

Par défaut, les applications créées à l’aide de l’option de modèle PWA prennent en charge l’exécution hors connexion. Un utilisateur doit d’abord accéder à l’application pendant qu’elle est en ligne. Le navigateur télécharge et met en cache automatiquement toutes les ressources nécessaires pour travailler hors connexion.

> [!IMPORTANT]
> Le support de développement interfère avec le cycle de développement habituel qui consiste à apporter des modifications et à les tester. Par conséquent, la prise en charge hors connexion est activée uniquement pour les applications *publiées* . 

> [!WARNING]
> Si vous envisagez de distribuer un PWA avec accès hors connexion, il y a [plusieurs avertissements et avertissements importants](#caveats-for-offline-pwas). Ces scénarios sont inhérents aux PWA hors connexion et ne sont pas spécifiques à Blazor . Veillez à lire et à comprendre ces avertissements avant de faire des hypothèses sur le fonctionnement de votre application en mode hors connexion.

Pour voir comment fonctionne la prise en charge hors connexion :

1. Publiez l’application. Pour plus d’informations, consultez <xref:blazor/host-and-deploy/index#publish-the-app>.
1. Déployez l’application sur un serveur qui prend en charge le protocole HTTPs, et accédez à l’application dans un navigateur à son adresse HTTPs sécurisée.
1. Ouvrez les outils de développement du navigateur, puis vérifiez qu’un *Worker service* est inscrit pour l’ordinateur hôte sous l’onglet **application** :

   ![L’onglet « application » des outils de développement Google Chrome montre un Worker service activé et en cours d’exécution.](progressive-web-app/_static/image4.png)

1. Rechargez la page et examinez l’onglet **réseau** . le **Service Worker** ou **cache mémoire** est répertorié comme source pour toutes les ressources de la page :

   ![L’onglet « Réseau » des outils de développement Google Chrome présente des sources pour toutes les ressources de la page.](progressive-web-app/_static/image5.png)

1. Pour vérifier que le navigateur n’est pas dépendant de l’accès réseau pour charger l’application, vous pouvez :

   * Arrêtez le serveur Web et observez comment l’application continue de fonctionner normalement, ce qui comprend les rechargements de pages. De même, l’application continue de fonctionner normalement lorsqu’il y a une connexion réseau lente.
   * Demandez au navigateur de simuler le mode hors connexion dans l’onglet **réseau** :

   ![L’onglet « Réseau » des outils de développement Google Chrome avec la liste déroulante mode navigateur passe de « en ligne » à « hors connexion ».](progressive-web-app/_static/image6.png)

La prise en charge hors connexion à l’aide d’un Worker service est une norme Web, non spécifique à Blazor . Pour plus d’informations sur les Workers de service, consultez [MDN Web docs : Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API). Pour en savoir plus sur les modèles d’utilisation courants pour les Workers de service, consultez [Google Web : the Service Worker Lifecycle](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).

Blazorle modèle PWA de génère deux fichiers de travail de service :

* `wwwroot/service-worker.js`, qui est utilisé pendant le développement.
* `wwwroot/service-worker.published.js`, qui est utilisé après la publication de l’application.

Pour partager la logique entre les deux fichiers de travail de service, envisagez l’approche suivante :

* Ajoutez un troisième fichier JavaScript pour contenir la logique commune.
* Utilisez [`self.importScripts`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) pour charger la logique commune dans les deux fichiers de travail de service.

### <a name="cache-first-fetch-strategy"></a>Stratégie d’extraction du cache en premier

Le `service-worker.published.js` Service Worker intégré résout les demandes à l’aide d’une stratégie de *cache-première* . Cela signifie que le processus de travail préfère retourner le contenu mis en cache, que l’utilisateur dispose d’un accès réseau ou que du contenu plus récent soit disponible sur le serveur.

La stratégie du cache en premier est utile pour les raisons suivantes :

* **Cela garantit la fiabilité.** L’accès réseau n’est pas un état booléen. Un utilisateur n’est pas simplement en ligne ou hors connexion :

  * L’appareil de l’utilisateur peut supposer qu’il est en ligne, mais il est possible que le réseau soit si lent, car il est difficile à attendre.
  * Le réseau peut retourner des résultats non valides pour certaines URL, par exemple lorsqu’il existe un portail WIFI captif qui bloque ou redirige actuellement certaines requêtes.
  
  C’est la raison pour laquelle l’API du navigateur `navigator.onLine` n’est pas fiable et ne doit pas être dépendante.

* **Cela garantit l’exactitude.** Lors de la création d’un cache de ressources hors connexion, le service worker utilise le hachage de contenu pour garantir qu’il a extrait un instantané complet et cohérent des ressources en un seul instant. Ce cache est ensuite utilisé comme une unité atomique. Il n’y a aucun point à demander au réseau des ressources plus récentes, puisque les seules versions requises sont celles déjà mises en cache. Tout autre risque d’incohérence et d’incompatibilité (par exemple, essayer d’utiliser des versions d’assemblys .NET qui n’ont pas été compilées ensemble).

### <a name="background-updates"></a>Mises à jour en arrière-plan

En tant que modèle mental, vous pouvez considérer qu’un PWA en mode hors connexion se comporte comme une application mobile qui peut être installée. L’application démarre immédiatement, quelle que soit la connectivité réseau, mais la logique de l’application installée provient d’un instantané à un moment donné qui peut ne pas être la dernière version.

Le Blazor modèle PWA produit des applications qui essaient automatiquement de se mettre à jour en arrière-plan chaque fois que l’utilisateur accède à une connexion réseau opérationnelle. Ce fonctionnement est le suivant :

* Au cours de la compilation, le projet génère un *manifeste des ressources de traitement de service*. Par défaut, cette méthode est appelée `service-worker-assets.js` . Le manifeste répertorie toutes les ressources statiques dont l’application a besoin pour fonctionner hors connexion, telles que les assemblys .NET, les fichiers JavaScript et CSS, y compris leurs hachages de contenu. La liste des ressources est chargée par le processus de travail du service afin qu’il sache quelles ressources mettre en cache.
* Chaque fois que l’utilisateur visite l’application, le navigateur redemande `service-worker.js` et `service-worker-assets.js` en arrière-plan. Les fichiers sont comparés octet par octet avec le processus de travail de service installé existant. Si le serveur retourne le contenu modifié de l’un de ces fichiers, le processus de travail de service tente d’installer une nouvelle version de lui-même.
* Lors de l’installation d’une nouvelle version de lui-même, le processus de travail crée un cache distinct pour les ressources hors connexion et démarre le remplissage du cache avec les ressources listées dans `service-worker-assets.js` . Cette logique est implémentée dans la `onInstall` fonction à l’intérieur de `service-worker.published.js` .
* Le processus se termine correctement lorsque toutes les ressources sont chargées sans erreur et que tous les hachages de contenu correspondent. En cas de réussite, le nouveau service Worker entre dans un état *d’activation en attente* . Dès que l’utilisateur ferme l’application (pas d’onglets ou de fenêtres d’application restants), le nouveau Worker service devient *actif* et est utilisé pour les visites de l’application suivante. L’ancien service Worker et son cache sont supprimés.
* Si le processus ne se termine pas correctement, la nouvelle instance de service worker est ignorée. Le processus de mise à jour est tenté à nouveau à la prochaine visite de l’utilisateur, lorsque j’espère que le client dispose d’une meilleure connexion réseau capable d’effectuer les requêtes.

Personnalisez ce processus en modifiant la logique du service Worker. Aucun des comportements précédents n’est propre à Blazor , mais il s’agit simplement de l’expérience par défaut fournie par l’option de modèle PWA. Pour plus d’informations, consultez [MDN Web docs : Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).

### <a name="how-requests-are-resolved"></a>Résolution des demandes

Comme décrit dans la section [stratégie de récupération du cache en premier](#cache-first-fetch-strategy) , le Worker de service par défaut utilise une stratégie de cache en *premier* , ce qui signifie qu’il tente de traiter le contenu mis en cache quand il est disponible. S’il n’existe aucun contenu mis en cache pour une certaine URL, par exemple lors de la demande de données à partir d’une API de serveur principal, le processus de travail de service revient sur une demande réseau normale. La demande réseau a abouti si le serveur est accessible. Cette logique est implémentée dans une `onFetch` fonction dans `service-worker.published.js` .

Si les composants de l’application s' Razor appuient sur la demande de données à partir des API principales et que vous souhaitez fournir une expérience utilisateur conviviale pour les demandes ayant échoué en raison d’une indisponibilité du réseau, implémentez la logique dans les composants de l’application. Par exemple, utilisez `try/catch` autour des <xref:System.Net.Http.HttpClient> demandes.

### <a name="support-server-rendered-pages"></a>Prendre en charge les pages rendues par le serveur

Réfléchissez à ce qui se passe quand l’utilisateur accède pour la première fois à une URL telle que `/counter` ou à tout autre lien profond dans l’application. Dans ce cas, vous ne souhaitez pas renvoyer le contenu mis en cache en tant que `/counter` , mais vous avez besoin du navigateur pour charger le contenu mis en cache en tant que `/index.html` pour démarrer votre Blazor WebAssembly application. Ces demandes initiales sont appelées demandes de *navigation* , par opposition à :

* `subresource` demandes d’images, de feuilles de style ou d’autres fichiers.
* `fetch/XHR` demandes de données d’API.

Le service Worker par défaut contient une logique de cas spéciale pour les demandes de navigation. Le Worker de service résout les demandes en retournant le contenu mis en cache pour `/index.html` , quelle que soit l’URL demandée. Cette logique est implémentée dans la `onFetch` fonction à l’intérieur de `service-worker.published.js` .

Si votre application comporte certaines URL qui doivent retourner le HTML rendu serveur et ne servent pas `/index.html` à partir du cache, vous devez modifier la logique de votre service Worker. Si toutes les URL contenant `/Identity/` doivent être gérées en tant que demandes standard en ligne uniquement au serveur, modifiez la `service-worker.published.js` `onFetch` logique. Recherchez le code suivant :

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

Remplacez le code par ce qui suit :

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

Si vous n’effectuez pas cette opération, quelle que soit la connectivité réseau, le service Worker intercepte les demandes de ces URL et les résout à l’aide de `/index.html` .

### <a name="control-asset-caching"></a>Contrôler la mise en cache des ressources

Si votre projet définit la `ServiceWorkerAssetsManifest` propriété MSBuild, les Blazor outils de génération de génèrent un manifeste des ressources de travail de service avec le nom spécifié. Le modèle PWA par défaut produit un fichier projet contenant la propriété suivante :

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

Le fichier est placé dans le `wwwroot` Répertoire de sortie, ce qui permet au navigateur de récupérer ce fichier en demandant `/service-worker-assets.js` . Pour afficher le contenu de ce fichier, ouvrez `/bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js` dans un éditeur de texte. Toutefois, ne modifiez pas le fichier, car il est régénéré à chaque Build.

Par défaut, ce manifeste répertorie les éléments suivants :

* Toutes les Blazor ressources managées, telles que les assemblys .net et les fichiers d’exécution Webassembly .net nécessaires pour fonctionner hors connexion.
* Toutes les ressources pour la publication dans le répertoire de l’application `wwwroot` , telles que les images, les feuilles de style et les fichiers JavaScript, y compris les ressources Web statiques fournies par les projets externes et les packages NuGet.

Vous pouvez contrôler les ressources qui sont extraites et mises en cache par le service Worker en modifiant la logique dans `onInstall` dans `service-worker.published.js` . Par défaut, le service Worker récupère et met en cache les fichiers correspondant à des extensions de nom de fichier Web typiques, telles que `.html` ,, `.css` et, ainsi que `.js` `.wasm` des types de fichiers spécifiques à Blazor WebAssembly ( `.dll` , `.pdb` ).

Pour inclure des ressources supplémentaires qui ne sont pas présentes dans le répertoire de l’application `wwwroot` , définissez des entrées MSBuild supplémentaires `ItemGroup` , comme indiqué dans l’exemple suivant :

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

Les `AssetUrl` métadonnées spécifient l’URL relative de base que le navigateur doit utiliser lors de la récupération de la ressource à mettre en cache. Cela peut être indépendant du nom du fichier source d’origine sur le disque.

> [!IMPORTANT]
> L’ajout de `ServiceWorkerAssetsManifestItem` n’entraîne pas la publication du fichier dans le répertoire de l’application `wwwroot` . La sortie de publication doit être contrôlée séparément. Le `ServiceWorkerAssetsManifestItem` seul fait apparaître une entrée supplémentaire dans le manifeste des ressources du Worker.

## <a name="push-notifications"></a>Notifications push

Comme tout autre PWA, Blazor WebAssembly PWA peut recevoir des notifications push à partir d’un serveur principal. Le serveur peut envoyer des notifications push à tout moment, même lorsque l’utilisateur n’utilise pas activement l’application. Par exemple, les notifications push peuvent être envoyées lorsqu’un autre utilisateur effectue une action appropriée.

Le mécanisme d’envoi d’une notification push est entièrement indépendant de Blazor WebAssembly , puisqu’il est implémenté par le serveur principal qui peut utiliser n’importe quelle technologie. Si vous souhaitez envoyer des notifications push à partir d’un serveur de ASP.NET Core, envisagez d' [utiliser une technique similaire à l’approche adoptée dans l’atelier de pizzas éblouissant](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications).

Le mécanisme de réception et d’affichage d’une notification push sur le client est également indépendant de Blazor WebAssembly , puisqu’il est implémenté dans le fichier JavaScript Service Worker. Pour obtenir un exemple, consultez [l’approche utilisée dans l’atelier de pizzas éblouissant](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).

## <a name="caveats-for-offline-pwas"></a>Avertissements pour les PWA hors connexion

Toutes les applications ne doivent pas essayer de prendre en charge l’utilisation hors connexion. La prise en charge hors connexion ajoute une complexité significative, mais n’est pas toujours pertinente pour les cas d’usage requis.

La prise en charge hors connexion est généralement pertinente uniquement :

* Si la Banque de données principale est locale pour le navigateur. Par exemple, l’approche est pertinente dans une application avec une interface utilisateur pour un appareil [IOT](https://en.wikipedia.org/wiki/Internet_of_things) qui stocke des données dans `localStorage` ou [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API).
* Si l’application effectue une quantité importante de travail pour extraire et mettre en cache les données de l’API backend pertinentes pour chaque utilisateur afin qu’elles puissent parcourir les données hors connexion. Si l’application doit prendre en charge la modification, un système de suivi des modifications et de synchronisation des données avec le serveur principal doit être généré.
* Si l’objectif est de garantir que l’application se charge immédiatement, indépendamment des conditions du réseau. Implémentez une expérience utilisateur appropriée autour des demandes d’API backend pour montrer la progression des requêtes et se comporter correctement lorsque les requêtes échouent en raison d’une indisponibilité du réseau.

En outre, les PWAS en mode hors connexion doivent gérer un éventail de complications supplémentaires. Les développeurs doivent se familiariser avec les limitations des sections suivantes.

### <a name="offline-support-only-when-published"></a>Prise en charge hors connexion uniquement lors de la publication

Pendant le développement, vous souhaitez généralement voir chaque modification reflétée immédiatement dans le navigateur sans passer par un processus de mise à jour en arrière-plan. Par conséquent, le Blazor modèle PWA de n’autorise la prise en charge hors connexion que lorsqu’il est publié.

Lors de la création d’une application de compatibilité hors connexion, il n’est pas suffisant de tester l’application dans l’environnement de développement. Vous devez tester l’application dans son état publié pour comprendre comment elle répond à différentes conditions réseau.

### <a name="update-completion-after-user-navigation-away-from-app"></a>Mettre à jour l’achèvement après la navigation de l’utilisateur hors de l’application

Les mises à jour ne se terminent pas tant que l’utilisateur n’a pas quitté l’application dans tous les onglets. Comme expliqué dans la section [mises à jour en arrière-plan](#background-updates) , après le déploiement d’une mise à jour de l’application, le navigateur récupère les fichiers de travail de service mis à jour pour commencer le processus de mise à jour.

Ce que de nombreux développeurs peuvent faire, même lorsque cette mise à jour est terminée, elle n’entre pas en vigueur tant que l’utilisateur n’a **pas** quitté tous les onglets. Il n’est **pas** suffisant d’actualiser l’onglet qui affiche l’application, même s’il s’agit du seul onglet qui affiche l’application. Tant que votre application n’est pas complètement fermée, le nouveau service Worker reste dans l’État en *attente d’activation* . **Cela n’est pas spécifique à Blazor , mais plutôt à un comportement de plateforme Web standard.**

Cela ne pose généralement pas de ennuis aux développeurs qui essaient de tester les mises à jour de leur service Worker ou des ressources mises en cache hors connexion. Si vous archivez dans les outils de développement du navigateur, vous pouvez voir un résultat semblable à ce qui suit :

![L’onglet « application » de Google Chrome indique que le service Worker de l’application est en attente d’activation.](progressive-web-app/_static/image7.png)

Tant que la liste des « clients », qui sont des onglets ou des fenêtres affichant votre application, n’est pas vide, le processus de travail continue à attendre. La raison pour laquelle le service Workers effectue cette tâche est de garantir la cohérence. La cohérence signifie que toutes les ressources sont extraites du même cache atomique.

Lorsque vous testez des modifications, il peut s’avérer utile de cliquer sur le lien « skipWaiting » comme indiqué dans la capture d’écran précédente, puis de recharger la page. Vous pouvez automatiser cette automatisation pour tous les utilisateurs en codant votre service Workers pour [ignorer la phase « en attente » et activer immédiatement la mise à jour](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase). Si vous ignorez la phase d’attente, vous vous soumettez à la garantie que les ressources sont toujours extraites de façon cohérente à partir de la même instance de cache.

### <a name="users-may-run-any-historical-version-of-the-app"></a>Les utilisateurs peuvent exécuter n’importe quelle version historique de l’application

Les développeurs Web s’attendent habituellement à ce que les utilisateurs exécutent uniquement la dernière version déployée de leur application Web, car cela est normal dans le modèle de distribution Web traditionnel. Toutefois, un PWA en mode hors connexion est plus similaire à une application mobile native, où les utilisateurs n’exécutent pas nécessairement la dernière version.

Comme expliqué dans la section [mises à jour en arrière-plan](#background-updates) , après le déploiement d’une mise à jour de votre application, **chaque utilisateur existant continue d’utiliser une version précédente pendant au moins une autre visite** , car la mise à jour se produit en arrière-plan et n’est pas activée tant que l’utilisateur ne l’a pas quitté par la suite. En outre, la version précédente utilisée n’est pas nécessairement la version précédente que vous avez déployée. La version précédente peut être *n’importe quelle* version historique, selon la date de la dernière exécution d’une mise à jour par l’utilisateur.

Cela peut être un problème si les composants frontend et backend de votre application requièrent un accord sur le schéma pour les demandes d’API. Vous ne devez pas déployer des modifications de schéma d’API à compatibilité descendante tant que vous n’êtes pas certain que tous les utilisateurs ont été mis à niveau. Vous pouvez également empêcher les utilisateurs d’utiliser des versions antérieures incompatibles de l’application. Cette exigence de scénario est la même que pour les applications mobiles natives. Si vous déployez une modification avec rupture dans les API de serveur, l’application cliente est interrompue pour les utilisateurs qui n’ont pas encore mis à jour.

Si possible, ne déployez pas les modifications importantes apportées à vos API backend. Si vous devez le faire, envisagez d’utiliser des [API de travail de service standard, telles que ServiceWorkerRegistration](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) , pour déterminer si l’application est à jour et, si ce n’est pas le cas, pour empêcher l’utilisation.

### <a name="interference-with-server-rendered-pages"></a>Interférence avec les pages rendues par le serveur

Comme décrit dans la section [prise en charge des pages rendues](#support-server-rendered-pages) par le serveur, si vous souhaitez contourner le comportement du processus de travail de retour de `/index.html` contenu pour toutes les demandes de navigation, modifiez la logique de votre service Worker.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>Tous les contenus du manifeste de la ressource Service Worker sont mis en cache par défaut

Comme décrit dans la section [mise en cache des ressources de contrôle](#control-asset-caching) , le fichier `service-worker-assets.js` est généré pendant la génération et répertorie toutes les ressources que le processus de travail de service doit extraire et mettre en cache.

Étant donné que cette liste inclut par défaut tout ce qui est émis vers `wwwroot` , y compris le contenu fourni par des packages et des projets externes, vous devez veiller à ne pas y placer trop de contenu. Si le `wwwroot` répertoire contient des millions d’images, le processus de travail de service tente de les extraire et de les mettre en cache, ce qui consomme une bande passante excessive et risque de ne pas s’exécuter correctement.

Implémentez une logique arbitraire pour contrôler le sous-ensemble du contenu du manifeste qui doit être extrait et mis en cache en modifiant la `onInstall` fonction dans `service-worker.published.js` .

### <a name="interaction-with-authentication"></a>Interaction avec l’authentification

Le modèle PWA peut être utilisé conjointement avec l’authentification. Un PWA compatible hors connexion peut également prendre en charge l’authentification lorsque l’utilisateur dispose d’une connectivité réseau initiale.

Lorsqu’un utilisateur n’a pas de connectivité réseau, il ne peut pas s’authentifier ou obtenir des jetons d’accès. Par défaut, si vous tentez d’accéder à la page de connexion sans accès réseau, le message « erreur réseau » s’affiche. Vous devez concevoir un workflow d’interface utilisateur qui permet à l’utilisateur d’effectuer des tâches utiles en mode hors connexion sans tenter d’authentifier l’utilisateur ou d’obtenir des jetons d’accès. Vous pouvez également concevoir l’application pour qu’elle échoue correctement lorsque le réseau n’est pas disponible. Si l’application ne peut pas être conçue pour gérer ces scénarios, il se peut que vous ne souhaitiez pas activer la prise en charge hors connexion.

Quand une application conçue pour une utilisation en ligne et hors connexion est à nouveau en ligne :

* L’application peut avoir besoin de configurer un nouveau jeton d’accès.
* L’application doit détecter si un autre utilisateur est connecté au service afin de pouvoir appliquer des opérations au compte de l’utilisateur qui ont été effectuées pendant qu’il était hors connexion.

Pour créer une application PWA hors connexion qui interagit avec l’authentification :

* Remplacez <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> par une fabrique qui stocke le dernier utilisateur connecté et qui utilise l’utilisateur stocké lorsque l’application est hors connexion.
* Les opérations de file d’attente pendant que l’application est hors connexion et les appliquent lorsque l’application est retournée en ligne.
* Pendant la déconnexion, effacez l’utilisateur stocké.

L' [`CarChecker`](https://github.com/SteveSandersonMS/CarChecker) exemple d’application illustre les approches précédentes. Consultez les parties suivantes de l’application :

* `OfflineAccountClaimsPrincipalFactory` (`Client/Data/OfflineAccountClaimsPrincipalFactory.cs`)
* `LocalVehiclesStore` (`Client/Data/LocalVehiclesStore.cs`)
* `LoginStatus` composant ( `Client/Shared/LoginStatus.razor` )

## <a name="additional-resources"></a>Ressources supplémentaires

* [Résoudre les problèmes de script PowerShell d’intégrité](xref:blazor/host-and-deploy/webassembly#troubleshoot-integrity-powershell-script)
* [SignalR négociation Cross-Origin pour l’authentification](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)

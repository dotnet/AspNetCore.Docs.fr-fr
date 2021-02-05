---
title: BlazorModèles d’hébergement ASP.net Core
author: guardrex
description: Comprendre Blazor WebAssembly et Blazor Server héberger des modèles.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
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
uid: blazor/hosting-models
ms.openlocfilehash: c60191cc8822dd875db613fb70516beb08a659d8
ms.sourcegitcommit: 20a41c8e40a2e69e99291e2fe18caa04c02e7109
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99578280"
---
# <a name="aspnet-core-blazor-hosting-models"></a>BlazorModèles d’hébergement ASP.net Core

Par [Daniel Roth](https://github.com/danroth27)

Blazor est un Framework Web conçu pour s’exécuter côté client dans le navigateur sur un Runtime .NET basé sur [Webassembly](https://webassembly.org/)( *Blazor WebAssembly* ) ou côté serveur dans ASP.net Core ( *Blazor Server* ). Quel que soit le modèle d’hébergement, les modèles d’application et de composant *sont les mêmes*.

## Blazor WebAssembly

Le Blazor modèle d’hébergement principal exécute côté client dans le navigateur sur Webassembly. L' Blazor application, ses dépendances et le Runtime .net sont téléchargés dans le navigateur. L’application est exécutée directement sur le thread d’interface utilisateur du navigateur. Les mises à jour de l’interface utilisateur et la gestion des événements se produisent dans le même processus. Les ressources de l’application sont déployées en tant que fichiers statiques sur un serveur Web ou un service qui prend en charge le contenu statique sur les clients.

![::: No-Loc (éblouissant webassembly) :::::::: No-Loc (éblouissant) ::: app s’exécute sur un thread d’interface utilisateur dans le navigateur.](hosting-models/_static/blazor-webassembly.png)

Lorsque l' Blazor WebAssembly application est créée pour le déploiement sans ASP.net Core application principale pour servir ses fichiers, l’application est appelée application *autonome* Blazor WebAssembly . Lorsque l’application est créée pour le déploiement avec une application principale pour servir ses fichiers, l’application est appelée application *hébergée* Blazor WebAssembly . Une application hébergée Blazor WebAssembly interagit généralement avec le serveur sur le réseau à l’aide d’appels d’API Web ou [SignalR](xref:signalr/introduction) ( <xref:tutorials/signalr-blazor> ).

Le `blazor.webassembly.js` script est fourni par l’infrastructure et les handles :

* Téléchargement du Runtime .NET, de l’application et des dépendances de l’application.
* Initialisation du runtime pour exécuter l’application.

Le Blazor WebAssembly modèle d’hébergement offre plusieurs avantages :

* Il n’existe aucune dépendance côté serveur .NET. L’application fonctionne entièrement une fois qu’elle a été téléchargée sur le client.
* Les ressources et les fonctionnalités du client sont pleinement exploitées.
* Le travail est déchargé du serveur vers le client.
* Un serveur Web ASP.NET Core n’est pas requis pour héberger l’application. Les scénarios de déploiement sans serveur sont possibles, tels que la mise en service de l’application à partir d’un réseau de distribution de contenu (CDN).

Le Blazor WebAssembly modèle d’hébergement présente les limitations suivantes :

* L’application est limitée aux fonctionnalités du navigateur.
* Le matériel et le logiciel client compatibles (par exemple, la prise en charge de webassembly) sont requis.
* La taille du téléchargement est supérieure, et les applications prennent plus de temps à se charger.
* Le Runtime .NET et la prise en charge des outils sont moins matures. Par exemple, des limitations existent dans la prise en charge de [.NET standard](/dotnet/standard/net-standard) et le débogage.

Pour créer une Blazor WebAssembly application, consultez <xref:blazor/tooling> .

Le modèle d’application hébergée Blazor prend en charge les [conteneurs dockers](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Pour la prise en charge de l’ancrage dans Visual Studio, cliquez avec le bouton droit sur le `Server` projet d’une solution hébergée, Blazor WebAssembly puis sélectionnez **Ajouter** la  >  **prise en charge** de l’ancrage.

## Blazor Server

Avec le Blazor Server modèle d’hébergement, l’application est exécutée sur le serveur à partir d’une application ASP.net core. Les mises à jour de l’interface utilisateur, la gestion des événements et les appels JavaScript sont gérés sur une [SignalR](xref:signalr/introduction) connexion.

![Le navigateur interagit avec l’application (hébergée à l’intérieur d’une application ASP.NET Core) sur le serveur via un ::: No-Loc (Signalr) ::: Connection.](hosting-models/_static/blazor-server.png)

L’application ASP.NET Core fait référence à la classe de l’application `Startup` à ajouter :

* Services côté serveur.
* L’application vers le pipeline de traitement des demandes.

Sur le client, le `blazor.server.js` script établit la SignalR connexion avec le serveur. Le script est fourni à l’application côté client à partir d’une ressource incorporée dans le ASP.NET Core Framework partagé. L’application côté client est responsable de la persistance et de la restauration de l’état de l’application en fonction des besoins. 

Le Blazor Server modèle d’hébergement offre plusieurs avantages :

* La taille du téléchargement est beaucoup plus petite qu’une Blazor WebAssembly application, et l’application est chargée beaucoup plus rapidement.
* L’application tire pleinement parti des fonctionnalités du serveur, notamment de l’utilisation de toutes les API compatibles avec .NET Core.
* .NET Core sur le serveur est utilisé pour exécuter l’application, de sorte que les outils .NET existants, tels que le débogage, fonctionnent comme prévu.
* Les clients légers sont pris en charge. Par exemple, les Blazor Server applications fonctionnent avec des navigateurs qui ne prennent pas en charge Webassembly et sur des appareils à ressources restreintes.
* La base de code .NET/C# de l’application, y compris le code du composant de l’application, n’est pas traitée aux clients.

> [!IMPORTANT]
> Une Blazor Server application effectue un prérendu en réponse à la première demande du client, ce qui crée l’état de l’interface utilisateur sur le serveur. Lorsque le client tente de créer une SignalR connexion, **le client doit se reconnecter au même serveur**. Blazor Server les applications qui utilisent plusieurs serveurs principaux doivent implémenter des *sessions rémanentes* pour les SignalR connexions. Pour plus d’informations, consultez la section [connexion au serveur](#connection-to-the-server) .

Le Blazor Server modèle d’hébergement présente les limitations suivantes :

* Une latence plus élevée existe généralement. Chaque interaction utilisateur implique un tronçon réseau.
* Il n’existe aucune prise en charge hors connexion. Si la connexion cliente échoue, l’application cesse de fonctionner.
* L’évolutivité est difficile pour les applications avec de nombreux utilisateurs. Le serveur doit gérer plusieurs connexions clientes et gérer l’état du client.
* Un serveur de ASP.NET Core est requis pour servir l’application. Les scénarios de déploiement sans serveur ne sont pas possibles, tels que la fourniture de l’application à partir d’un réseau de distribution de contenu (CDN).

Pour créer une Blazor Server application, consultez <xref:blazor/tooling> .

Le Blazor Server modèle d’application prend en charge les conteneurs de l' [ancrage](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Pour la prise en charge de l’ancrage dans Visual Studio, cliquez avec le bouton droit sur le projet dans Visual Studio, puis sélectionnez **Ajouter** la  >  **prise en charge** de l’ancrage.

### <a name="comparison-to-server-rendered-ui"></a>Comparaison avec l’interface utilisateur du rendu serveur

L’une des façons de comprendre Blazor Server les applications consiste à comprendre comment elle diffère des modèles traditionnels pour le rendu de l’interface utilisateur dans les applications de ASP.net core à l’aide de Razor vues ou de Razor pages. Les deux modèles utilisent le [ Razor langage](xref:mvc/views/razor) pour décrire le contenu HTML pour *le* rendu, mais ils diffèrent de manière significative dans le rendu du balisage.

Quand une Razor page ou une vue est restituée, chaque ligne de Razor code émet du code HTML sous forme de texte. Après le rendu, le serveur supprime l’instance de la page ou de la vue, y compris tout état produit. Lorsqu’une autre demande pour la page se produit, par exemple lorsque la validation du serveur échoue et que le résumé des validations s’affiche :

* La page entière est de nouveau restituée au texte HTML.
* La page est envoyée au client.

Une Blazor application est composée d’éléments réutilisables de l’interface utilisateur appelée *composants*. Un composant contient le code C#, le balisage et d’autres composants. Lorsqu’un composant est rendu, Blazor produit un graphique des composants inclus similaires à un document Object Model XML ou XML (DOM). Ce graphique comprend l’état des composants contenus dans les propriétés et les champs. Blazor évalue le graphique du composant pour produire une représentation binaire de la balise. Le format binaire peut être :

* Converti en texte HTML (lors du prérendu &dagger; ).
* Utilisé pour mettre à jour efficacement le balisage pendant le rendu normal.

&dagger;*Prérendu*: le composant demandé Razor est compilé sur le serveur en HTML statique et envoyé au client, où il est rendu à l’utilisateur. Une fois la connexion établie entre le client et le serveur, les éléments statiques prérendus du composant sont remplacés par des éléments interactifs. Le prérendu rend l’application plus réactive pour l’utilisateur.

Une mise à jour de l’interface utilisateur dans Blazor est déclenchée par :

* Intervention de l’utilisateur, telle que la sélection d’un bouton.
* Déclencheurs d’application, tels qu’un minuteur.

Le graphique du composant est rerendu et une *diff* . d’interface utilisateur (différence) est calculée. Cette différence est le plus petit ensemble de modifications DOM requis pour mettre à jour l’interface utilisateur sur le client. Le diff est envoyé au client dans un format binaire et appliqué par le navigateur.

Un composant est supprimé une fois que l’utilisateur l’a quittée sur le client. Lorsqu’un utilisateur interagit avec un composant, l’état du composant (services, ressources) doit être conservé dans la mémoire du serveur. Étant donné que l’état de nombreux composants peut être géré simultanément par le serveur, l’épuisement de la mémoire est un problème qui doit être résolu. Pour obtenir des conseils sur la façon de créer une Blazor Server application pour garantir la meilleure utilisation de la mémoire du serveur, consultez <xref:blazor/security/server/threat-mitigation> .

### <a name="circuits"></a>Circuits

Une Blazor Server application est générée par-dessus [ASP.net Core SignalR ](xref:signalr/introduction). Chaque client communique avec le serveur sur une ou plusieurs SignalR connexions appelées *circuit*. Un circuit est une Blazor abstraction sur les SignalR connexions qui peuvent tolérer des interruptions réseau temporaires. Lorsqu’un Blazor client constate que la SignalR connexion est déconnectée, il tente de se reconnecter au serveur à l’aide d’une nouvelle SignalR connexion.

Chaque écran de navigateur (onglet de navigateur ou IFRAME) qui est connecté à une Blazor Server application utilise une SignalR connexion. Il s’agit encore d’une autre distinction importante par rapport aux applications classiques affichées par le serveur. Dans une application affichée sur un serveur, l’ouverture de la même application dans plusieurs écrans de navigateur n’est généralement pas convertie en demandes de ressources supplémentaires sur le serveur. Dans une Blazor Server application, chaque écran de navigateur requiert un circuit distinct et des instances distinctes de l’état du composant à gérer par le serveur.

Blazor tient compte de la fermeture d’un onglet de navigateur ou de la navigation vers une URL externe un arrêt *normal* . En cas de résiliation appropriée, le circuit et les ressources associées sont immédiatement libérés. Un client peut également se déconnecter de manière non appropriée, par exemple en raison d’une interruption du réseau. Blazor Server stocke les circuits déconnectés pour un intervalle configurable afin de permettre au client de se reconnecter.

Blazor Server permet au code de définir un *Gestionnaire de circuit* qui permet d’exécuter du code sur les modifications de l’état du circuit d’un utilisateur. Pour plus d’informations, consultez <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.

### <a name="ui-latency"></a>Latence de l’interface utilisateur

La latence de l’interface utilisateur est le temps qu’il faut après une action initiée jusqu’au moment où l’interface utilisateur est mise à jour. Des valeurs plus petites pour la latence de l’interface utilisateur sont impératives pour qu’une application soit réactive à un utilisateur. Dans une Blazor Server application, chaque action est envoyée au serveur, traitée et une comparaison d’interface utilisateur est renvoyée. Par conséquent, la latence de l’interface utilisateur est la somme de la latence du réseau et de la latence du serveur lors du traitement de l’action.

Pour une application d’entreprise qui est limitée à un réseau d’entreprise privé, l’effet sur les perceptions de l’utilisateur de la latence en raison de la latence du réseau est généralement inperceptible. Pour une application déployée sur Internet, la latence peut devenir perceptible pour les utilisateurs, en particulier si les utilisateurs sont largement répartis géographiquement.

L’utilisation de la mémoire peut également contribuer à la latence de l’application. L’augmentation de l’utilisation de la mémoire permet de garbage collection fréquentes ou la pagination de la mémoire sur le disque, qui dégradent les performances des applications et augmentent la latence de l’interface utilisateur.

Blazor Server les applications doivent être optimisées pour réduire la latence de l’interface utilisateur en réduisant la latence du réseau et l’utilisation de la mémoire. Pour une approche de la mesure de la latence du réseau, consultez <xref:blazor/host-and-deploy/server#measure-network-latency> . Pour plus d’informations sur SignalR et Blazor , consultez :

* <xref:blazor/host-and-deploy/server>
* <xref:blazor/security/server/threat-mitigation>

### <a name="connection-to-the-server"></a>Connexion au serveur

Blazor Server les applications requièrent une SignalR connexion active au serveur. Si la connexion est perdue, l’application tente de se reconnecter au serveur. Tant que l’état du client reste dans la mémoire du serveur, la session client reprend sans perte d’État.

Une Blazor Server application effectue un prérendu en réponse à la première demande du client, ce qui crée l’état de l’interface utilisateur sur le serveur. Lorsque le client tente de créer une SignalR connexion, le client doit se reconnecter au même serveur. Blazor Server les applications qui utilisent plusieurs serveurs principaux doivent implémenter des *sessions rémanentes* pour les SignalR connexions.

Nous vous recommandons d’utiliser le [ SignalR service Azure](/azure/azure-signalr) pour les Blazor Server applications. Le service permet la mise à l’échelle d’une Blazor Server application vers un grand nombre de connexions simultanées SignalR . Les sessions rémanentes sont activées pour le SignalR service Azure en définissant l' `ServerStickyMode` option ou la valeur de configuration du service sur `Required` . Pour plus d’informations, consultez <xref:blazor/host-and-deploy/server#signalr-configuration>.

Lorsque vous utilisez IIS, les sessions rémanentes sont activées avec *application Request Routing*. Pour plus d’informations, consultez [équilibrage de charge http à l’aide de application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/tooling>
* <xref:signalr/introduction>
* <xref:blazor/fundamentals/additional-scenarios>
* <xref:tutorials/signalr-blazor>

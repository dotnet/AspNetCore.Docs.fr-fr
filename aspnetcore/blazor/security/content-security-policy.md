---
title: Appliquer une stratégie de sécurité de contenu pour ASP.NET Core Blazor
author: guardrex
description: Découvrez comment utiliser une stratégie de sécurité de contenu (CSP) avec des Blazor applications de ASP.net Core pour vous protéger contre les attaques de script entre sites (XSS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/content-security-policy
ms.openlocfilehash: f44f348947e31864f5d0d44c9caf1a3aa9c3b1d4
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280348"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a>Appliquer une stratégie de sécurité de contenu pour ASP.NET Core Blazor

L’exécution de scripts [entre sites (XSS)](xref:security/cross-site-scripting) est une faille de sécurité dans laquelle un attaquant place un ou plusieurs scripts malveillants côté client dans le contenu rendu d’une application. Une stratégie de sécurité de contenu (CSP) vous aide à vous protéger contre les attaques XSS en informant le navigateur de la validité :

* Sources pour le contenu chargé, y compris les scripts, les feuilles de style et les images.
* Actions effectuées par une page, en spécifiant des cibles d’URL autorisées de formulaires.
* Plug-ins qui peuvent être chargés.

Pour appliquer un CSP à une application, le développeur spécifie plusieurs *directives* de sécurité de contenu CSP dans un ou plusieurs `Content-Security-Policy` en-têtes ou `<meta>` balises.

Les stratégies sont évaluées par le navigateur pendant le chargement d’une page. Le navigateur inspecte les sources de la page et détermine si elles répondent aux exigences des directives de sécurité du contenu. Lorsque les directives de stratégie ne sont pas respectées pour une ressource, le navigateur ne charge pas la ressource. Par exemple, considérez une stratégie qui n’autorise pas les scripts tiers. Quand une page contient une `<script>` balise avec une origine tierce dans l' `src` attribut, le navigateur empêche le chargement du script.

CSP est pris en charge dans la plupart des navigateurs mobiles et de bureau modernes, notamment chrome, Edge, Firefox, Opera et Safari. CSP est recommandé pour les Blazor applications.

## <a name="policy-directives"></a>Directives de stratégie

Au minimum, spécifiez les directives et les sources suivantes pour les Blazor applications. Ajoutez des directives et des sources supplémentaires selon vos besoins. Les directives suivantes sont utilisées dans la section [appliquer la stratégie](#apply-the-policy) de cet article, où des exemples de stratégies de sécurité pour Blazor WebAssembly et Blazor Server sont fournis :

* [base-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): restreint les URL pour la balise d’une page `<base>` . Spécifiez `self` pour indiquer que l’origine de l’application, y compris le schéma et le numéro de port, est une source valide.
* [bloc-tout-contenu mixte](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): empêche le chargement de contenu HTTP et HTTPS mixte.
* [default-SRC](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): indique une solution de secours pour les directives source qui ne sont pas explicitement spécifiées par la stratégie. Spécifiez `self` pour indiquer que l’origine de l’application, y compris le schéma et le numéro de port, est une source valide.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): indique des sources valides pour les images.
  * Spécifiez `data:` pour autoriser le chargement d’images à partir d' `data:` URL.
  * Spécifiez `https:` pour autoriser le chargement d’images à partir de points de terminaison HTTPS.
* [Object-SRC](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): indique des sources valides pour les `<object>` `<embed>` `<applet>` balises, et. Spécifiez `none` pour empêcher toutes les sources d’URL.
* [script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): indique des sources valides pour les scripts.
  * Spécifiez la `https://stackpath.bootstrapcdn.com/` source de l’hôte pour les scripts de démarrage.
  * Spécifiez `self` pour indiquer que l’origine de l’application, y compris le schéma et le numéro de port, est une source valide.
  * Dans une Blazor WebAssembly application :
    * Spécifiez les hachages pour autoriser le chargement des scripts requis.
    * Spécifiez `unsafe-eval` pour utiliser `eval()` les méthodes et afin de créer du code à partir de chaînes.
  * Dans une Blazor Server application, spécifiez les hachages pour autoriser le chargement des scripts requis.
* [style-SRC](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): indique des sources valides pour les feuilles de style.
  * Spécifiez la `https://stackpath.bootstrapcdn.com/` source de l’hôte pour les feuilles de style de démarrage.
  * Spécifiez `self` pour indiquer que l’origine de l’application, y compris le schéma et le numéro de port, est une source valide.
  * Spécifiez `unsafe-inline` pour autoriser l’utilisation de styles intralignes. La déclaration inline est requise pour l’interface utilisateur dans les Blazor Server applications pour la reconnexion du client et du serveur après la demande initiale. Dans une version ultérieure, le style intraligne peut être supprimé afin qu' `unsafe-inline` il ne soit plus nécessaire.
* [Upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): indique que les URL de contenu provenant de sources non sécurisées (http) doivent être acquises en toute sécurité via HTTPS.

Les directives précédentes sont prises en charge par tous les navigateurs, à l’exception de Microsoft Internet Explorer.

Pour obtenir des hachages SHA pour d’autres scripts inline :

* Appliquez le CSP présenté dans la section [appliquer la stratégie](#apply-the-policy) .
* Accédez à la console outils de développement du navigateur tout en exécutant l’application localement. Le navigateur calcule et affiche les hachages pour les scripts bloqués lorsqu’un en-tête ou une `meta` balise CSP est présent.
* Copiez les hachages fournis par le navigateur dans les `script-src` sources. Utilisez des guillemets simples autour de chaque hachage.

Pour obtenir une matrice de prise en charge des navigateurs de niveau 2 de stratégie de sécurité de contenu, consultez la page [comment utiliser le niveau de stratégie de sécurité de contenu 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).

## <a name="apply-the-policy"></a>Application de la stratégie

Utilisez une `<meta>` balise pour appliquer la stratégie :

* Affectez `http-equiv` à l’attribut la valeur `Content-Security-Policy` .
* Placez les directives dans la `content` valeur de l’attribut. Séparez les directives par un point-virgule ( `;` ).
* Placez toujours la `meta` balise dans le `<head>` contenu.

Les sections suivantes présentent des exemples de stratégies pour Blazor WebAssembly et Blazor Server . Ces exemples sont associés à des versions de cet article pour chaque version de Blazor . Pour utiliser une version appropriée pour votre version, sélectionnez la version du document avec le sélecteur de **version** déroulante sur cette page Web.

### Blazor WebAssembly

Dans la `<head>` page contenu de l' `wwwroot/index.html` hôte, appliquez les directives décrites dans la section [directives de stratégie](#policy-directives) :

::: moniker range=">= aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

Ajoutez `script-src` `style-src` les hachages supplémentaires requis par l’application. Pendant le développement, utilisez un outil en ligne ou des outils de développement de navigateur pour que les hachages soient calculés pour vous. Par exemple, l’erreur de la console outils de navigateur suivante signale le hachage pour un script requis non couvert par la stratégie :

> Refus d’exécuter le script inline, car il viole la directive de stratégie de sécurité de contenu suivante : "... ". Le mot clé unsafe-Inline, un hash ('SHA256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA = ') ou une valeur à usage unique ('nonce-... ') est requis pour permettre l’exécution inline.

Le script particulier associé à l’erreur s’affiche dans la console en regard de l’erreur.

### Blazor Server

Dans la `<head>` page contenu de l' `Pages/_Host.cshtml` hôte, appliquez les directives décrites dans la section [directives de stratégie](#policy-directives) :

::: moniker range=">= aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

Ajoutez `script-src` `style-src` les hachages supplémentaires requis par l’application. Pendant le développement, utilisez un outil en ligne ou des outils de développement de navigateur pour que les hachages soient calculés pour vous. Par exemple, l’erreur de la console outils de navigateur suivante signale le hachage pour un script requis non couvert par la stratégie :

> Refus d’exécuter le script inline, car il viole la directive de stratégie de sécurité de contenu suivante : "... ". Le mot clé unsafe-Inline, un hash ('SHA256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA = ') ou une valeur à usage unique ('nonce-... ') est requis pour permettre l’exécution inline.

Le script particulier associé à l’erreur s’affiche dans la console en regard de l’erreur.

## <a name="meta-tag-limitations"></a>Limitations des balises meta

Une `<meta>` stratégie de balise ne prend pas en charge les directives suivantes :

* [ancêtres de cadre](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [rapport-à](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [rapport-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [immédiatement](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

Pour prendre en charge les directives précédentes, utilisez un en-tête nommé `Content-Security-Policy` . La chaîne de directive est la valeur de l’en-tête.

## <a name="test-a-policy-and-receive-violation-reports"></a>Tester une stratégie et recevoir des rapports de violation

Le test permet de vérifier que les scripts tiers ne sont pas bloqués par inadvertance lors de la création d’une stratégie initiale.

Pour tester une stratégie sur une période sans appliquer les directives de stratégie, définissez le `<meta>` `http-equiv` nom d’en-tête ou d’attribut de la balise d’une stratégie basée sur un en-tête sur `Content-Security-Policy-Report-Only` . Les rapports d’échec sont envoyés en tant que documents JSON à une URL spécifiée. Pour plus d’informations, consultez la [page documentation Web MDN : contenu-sécurité-stratégie-rapport uniquement](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).

Pour la création de rapports sur les violations pendant qu’une stratégie est active, consultez les articles suivants :

* [rapport-à](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [rapport-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

Bien que ne `report-uri` soit plus recommandé pour une utilisation, les deux directives doivent être utilisées jusqu’à ce que `report-to` soit pris en charge par tous les principaux navigateurs. N’utilisez pas exclusivement `report-uri` , car la prise en charge de `report-uri` est sujette à être supprimée à *tout moment* à partir des navigateurs. Supprimez la prise en charge de `report-uri` dans vos stratégies lorsque `report-to` est entièrement pris en charge. Pour suivre l’adoption de `report-to` , consultez [puis-je utiliser : Report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).

Testez et mettez à jour la stratégie d’une application chaque version.

## <a name="troubleshoot"></a>Dépanner

* Les erreurs s’affichent dans la console outils de développement du navigateur. Les navigateurs fournissent des informations sur les éléments suivants :
  * Éléments qui ne sont pas conformes à la stratégie.
  * Comment modifier la stratégie pour autoriser un élément bloqué.
* Une stratégie est entièrement efficace lorsque le navigateur du client prend en charge toutes les directives incluses. Pour obtenir une matrice de prise en charge actuelle du navigateur, consultez Comment [utiliser : Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).

## <a name="additional-resources"></a>Ressources supplémentaires

* [MDN Web docs : Content-Security-Policy](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [Niveau de stratégie de sécurité du contenu 2](https://www.w3.org/TR/CSP2/)
* [Évaluateur Google CSP](https://csp-evaluator.withgoogle.com/)

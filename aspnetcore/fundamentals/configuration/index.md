---
title: Configuration dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser l’API de configuration pour configurer une application ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 1/29/2021
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
uid: fundamentals/configuration/index
ms.openlocfilehash: 0f069b049889f7caade493e238ac7a23db5e79af
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536290"
---
# <a name="configuration-in-aspnet-core"></a>Configuration dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Kirk Larkin](https://twitter.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

La configuration dans ASP.NET Core est effectuée à l’aide d’un ou de plusieurs [fournisseurs de configuration](#cp). Les fournisseurs de configuration lisent les données de configuration des paires clé-valeur à l’aide d’une variété de sources de configuration :

* Les fichiers de paramètres, tels que *appsettings.json*
* Variables d'environnement
* Azure Key Vault
* Azure App Configuration
* Arguments de ligne de commande
* Fournisseurs personnalisés, installés ou créés
* Fichiers de répertoire
* Objets .NET en mémoire

Cette rubrique fournit des informations sur la configuration dans ASP.NET Core. Pour plus d’informations sur l’utilisation de la configuration dans les applications console, consultez [Configuration .net](/dotnet/core/extensions/configuration).

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

<a name="default"></a>

## <a name="default-configuration"></a>Configuration par défaut

ASP.NET Core les applications Web créées avec [dotnet New](/dotnet/core/tools/dotnet-new) ou Visual Studio génèrent le code suivant :

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fournit la configuration par défaut de l’application dans l’ordre suivant :

1. [ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : ajoute un existant `IConfiguration` en tant que source. Dans le cas de configuration par défaut, ajoute la configuration d' [hôte](#hvac) et la définit en tant que première source de la configuration de l' _application_ .
1. [appsettings.json](#appsettingsjson) utilisation du [fournisseur de configuration JSON](#file-configuration-provider).
1. *appSettings.* `Environment` *. JSON* à l’aide du [fournisseur de configuration JSON](#file-configuration-provider). Par exemple, *appSettings*. ***Production * * _._json* et *appSettings*. * * * développement** _._json *.
1. [Secrets d’application](xref:security/app-secrets) lorsque l’application s’exécute dans l' `Development` environnement.
1. Variables d’environnement à l’aide du [fournisseur de configuration des variables d’environnement](#evcp).
1. Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line).

Les fournisseurs de configuration ajoutés ultérieurement remplacent les paramètres de clé précédents. Par exemple, si `MyKey` est défini à la fois dans *appsettings.json* et dans l’environnement, la valeur d’environnement est utilisée. À l’aide des fournisseurs de configuration par défaut, le  [fournisseur de configuration de ligne de commande](#clcp) remplace tous les autres fournisseurs.

Pour plus d’informations sur `CreateDefaultBuilder` , consultez [paramètres par défaut du générateur](xref:fundamentals/host/generic-host#default-builder-settings).

Le code suivant affiche les fournisseurs de configuration activés dans l’ordre dans lequel ils ont été ajoutés :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

Prenons le *appsettings.json* fichier suivant :

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

La configuration par défaut <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge dans l’ordre suivant :

1. *appsettings.json*
1. *appSettings.* `Environment` *. JSON* : par exemple, *appSettings*. **Les fichiers de *production * * _._json* et *appSettings*. * * * Development** _._json *. La version de l’environnement du fichier est chargée à partir de [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*). Pour plus d’informations, consultez <xref:fundamentals/environments>.

*appSettings*. `Environment` . les valeurs *JSON* remplacent les clés dans *appsettings.json* . Par exemple, par défaut :

* Dans le développement, *appSettings*. ***Development** _._json * configuration remplace les valeurs trouvées dans *appsettings.json* .
* En production, *appSettings*. ***production** _._json * configuration remplace les valeurs trouvées dans *appsettings.json* . Par exemple, lors du déploiement de l’application sur Azure.

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a>Lier des données de configuration hiérarchiques à l’aide du modèle options

[!INCLUDE[](~/includes/bind.md)]

À l’aide de la configuration [par défaut](#default) , de *appsettings.json* et de *appSettings.* `Environment` les fichiers *. JSON* sont activés avec [reloadOnChange : true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75). Modifications apportées *appsettings.json* aux *appSettings et.* `Environment` le fichier *. JSON* ***après*** le démarrage de l’application est lu par le [fournisseur de configuration JSON](#jcp).

Pour plus d’informations sur l’ajout de fichiers de configuration JSON supplémentaires, consultez [fournisseur de configuration JSON](#jcp) dans ce document.

## <a name="combining-service-collection"></a>Combinaison de la collection de services

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a>Sécurité et secrets de l’utilisateur

Instructions relatives aux données de configuration :

* Ne stockez jamais des mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte clair. L’outil [Gestionnaire de secret](xref:security/app-secrets) peut être utilisé pour stocker les secrets en développement.
* N’utilisez aucun secret de production dans les environnements de développement ou de test.
* Spécifiez les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans un référentiel de code source.

Par [défaut](#default), la source de configuration des secrets de l’utilisateur est inscrite après les sources de configuration JSON. Par conséquent, les clés de secrets d’utilisateur sont prioritaires sur les clés dans *appsettings.json* et *appSettings.* `Environment` *. JSON*.

Pour plus d’informations sur le stockage des mots de passe ou d’autres données sensibles :

* <xref:fundamentals/environments>
* <xref:security/app-secrets>: Fournit des conseils sur l’utilisation de variables d’environnement pour stocker des données sensibles. L’outil Gestionnaire de secret utilise le [fournisseur de configuration de fichiers](#fcp) pour stocker les secrets d’utilisateur dans un fichier JSON sur le système local.

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stocke en toute sécurité des secrets d’application pour les applications ASP.NET Core. Pour plus d’informations, consultez <xref:security/key-vault-configuration>.

<a name="evcp"></a>

## <a name="environment-variables"></a>Variables d'environnement

À l’aide de la configuration [par défaut](#default) , le <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> charge la configuration à partir des paires clé-valeur de variable d’environnement après la lecture *appsettings.json* , *appSettings.* `Environment` *. JSON* et les [secrets](xref:security/app-secrets)de l’utilisateur. Par conséquent, les valeurs de clés lues à partir de l’environnement remplacent les valeurs lues à partir de *appsettings.json* *appSettings.* `Environment` *. JSON* et les secrets de l’utilisateur.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Les `set` commandes suivantes :

* Définissez les clés et les valeurs d’environnement de l' [exemple précédent](#appsettingsjson) sur Windows.
* Testez les paramètres lors de l’utilisation de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample). La `dotnet run` commande doit être exécutée dans le répertoire du projet.

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

Paramètres d’environnement précédents :

* Sont uniquement définies dans les processus lancés à partir de la fenêtre de commande dans laquelle ils ont été définis.
* Ne seront pas lues par les navigateurs lancés avec Visual Studio.

Les commandes [setx](/windows-server/administration/windows-commands/setx) suivantes peuvent être utilisées pour définir les clés et les valeurs d’environnement sur Windows. Contrairement à `set` , `setx` les paramètres sont conservés. `/M` définit la variable dans l’environnement système. Si le `/M` commutateur n’est pas utilisé, une variable d’environnement utilisateur est définie.

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

Pour vérifier que les commandes précédentes remplacent *appsettings.json* et *appSettings.* `Environment` *. JSON*:

* Avec Visual Studio : quittez et redémarrez Visual Studio.
* Avec l’interface CLI : démarrez une nouvelle fenêtre de commande et entrez `dotnet run` .

Appelez <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> avec une chaîne pour spécifier un préfixe pour les variables d’environnement :

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

Dans le code précédent :

* `config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` est ajouté après les [fournisseurs de configuration par défaut](#default). Pour obtenir un exemple de classement des fournisseurs de configuration, consultez [fournisseur de configuration JSON](#jcp).
* Les variables d’environnement définies avec le `MyCustomPrefix_` préfixe remplacent les [fournisseurs de configuration par défaut](#default). Cela comprend les variables d’environnement sans le préfixe.

Le préfixe est supprimé lorsque les paires clé-valeur de configuration sont lues.

Les commandes suivantes testent le préfixe personnalisé :

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

La [configuration par défaut](#default) charge les variables d’environnement et les arguments de ligne de commande préfixé avec `DOTNET_` et `ASPNETCORE_` . Les `DOTNET_` `ASPNETCORE_` préfixes et sont utilisés par ASP.net Core pour la configuration de l' [hôte et](xref:fundamentals/host/generic-host#host-configuration)de l’application, mais pas pour la configuration de l’utilisateur. Pour plus d’informations sur la configuration de l’hôte et de l’application, consultez [hôte générique .net](xref:fundamentals/host/generic-host).

Dans [Azure App service](https://azure.microsoft.com/services/app-service/), sélectionnez **nouveau paramètre d’application** dans la page **paramètres > configuration** . Azure App Service paramètres de l’application sont les suivants :

* Chiffré au repos et transmis sur un canal chiffré.
* Exposés en tant que variables d’environnement.

Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

Consultez [préfixes de chaîne de connexion](#constr) pour plus d’informations sur les chaînes de connexion de base de données Azure.

### <a name="naming-of-environment-variables"></a>Affectation des noms de variables d’environnement

Les noms des variables d’environnement reflètent la structure d’un *appsettings.json* fichier. Chaque élément de la hiérarchie est séparé par un trait de soulignement double (préférable) ou par deux-points. Lorsque la structure de l’élément comprend un tableau, l’index du tableau doit être traité comme un nom d’élément supplémentaire dans ce chemin d’accès. Considérez le *appsettings.json* fichier suivant et ses valeurs équivalentes représentées sous la forme de variables d’environnement.

**appsettings.json**

```json
{
    "SmtpServer": "smtp.example.com",
    "Logging": [
        {
            "Name": "ToEmail",
            "Level": "Critical",
            "Args": {
                "FromAddress": "MySystem@example.com",
                "ToAddress": "SRE@example.com"
            }
        },
        {
            "Name": "ToConsole",
            "Level": "Information"
        }
    ]
}
```

**variables d’environnement**

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-generated-launchsettingsjson"></a>Variables d’environnement définies dans les launchSettings.jsgénérées sur

Les variables d’environnement définies dans *launchSettings.jslors de* la substitution de celles définies dans l’environnement système. Par exemple, les modèles Web ASP.NET Core génèrent un *launchSettings.jssur* un fichier qui définit la configuration du point de terminaison sur :

```json
"applicationUrl": "https://localhost:5001;http://localhost:5000"
```

La configuration de `applicationUrl` définit la `ASPNETCORE_URLS` variable d’environnement et remplace les valeurs définies dans l’environnement.

### <a name="escape-environment-variables-on-linux"></a>Variables d’environnement d’échappement sur Linux

Sur Linux, la valeur des variables d’environnement URL doit être placée dans une séquence d’échappement pour `systemd` pouvoir être analysée. Utiliser l’outil Linux `systemd-escape` qui génère `http:--localhost:5001`
 
 ```cmd
 groot@terminus:~$ systemd-escape http://localhost:5001
 http:--localhost:5001
 ```

### <a name="display-environment-variables"></a>Afficher les variables d’environnement

Le code suivant affiche les variables d’environnement et les valeurs au démarrage de l’application, ce qui peut être utile lors du débogage des paramètres d’environnement :

[!code-csharp[](~/fundamentals/configuration/index/samples_snippets/5.x/Program.cs?name=snippet)]

<a name="clcp"></a>

## <a name="command-line"></a>Ligne de commande

À l’aide de la configuration [par défaut](#default) , le <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> charge la configuration à partir de paires clé-valeur d’argument de ligne de commande après les sources de configuration suivantes :

* *appsettings.json* et *appSettings*. `Environment` . fichiers *JSON* .
* [Secrets](xref:security/app-secrets) de l’application dans l’environnement de développement.
* Variables d'environnement.

Par [défaut](#default), les valeurs de configuration définies sur la ligne de commande remplacent les valeurs de configuration définies avec tous les autres fournisseurs de configuration.

### <a name="command-line-arguments"></a>Arguments de ligne de commande

La commande suivante définit des clés et des valeurs à l’aide de `=` :

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

La commande suivante définit des clés et des valeurs à l’aide de `/` :

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

La commande suivante définit des clés et des valeurs à l’aide de `--` :

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

Valeur de la clé :

* Doit suivre `=` ou la clé doit avoir un préfixe `--` ou `/` lorsque la valeur suit un espace.
* N’est pas obligatoire si `=` est utilisé. Par exemple : `MySetting=`.

Dans la même commande, ne mélangez pas les paires clé-valeur d’argument de ligne de commande qui utilisent `=` des paires clé-valeur utilisant un espace.

### <a name="switch-mappings"></a>Correspondances de commutateur

Les mappages de commutateur autorisent la logique de remplacement de nom de **clé** . Fournissez un dictionnaire de remplacements de commutateur dans la <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> méthode.

Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande. Si la clé de ligne de commande est trouvée dans le dictionnaire, la valeur du dictionnaire est retournée pour définir la paire clé-valeur dans la configuration de l’application. Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).

Règles des clés du dictionnaire de correspondances de commutateur :

* Les commutateurs doivent commencer par `-` ou `--` .
* Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.

Pour utiliser un dictionnaire de mappages de commutateur, transmettez-le à l’appel à `AddCommandLine` :

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

Le code suivant montre les valeurs de clé pour les clés remplacées :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

La commande suivante fonctionne pour tester le remplacement de la clé :

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

Pour les applications qui utilisent des mappages de commutateurs, l’appel à `CreateDefaultBuilder` ne doit pas passer d’arguments. L' `CreateDefaultBuilder` appel de la méthode `AddCommandLine` n’inclut pas de commutateurs mappés, et il n’existe aucun moyen de passer le dictionnaire de mappage de commutateur à `CreateDefaultBuilder` . La solution ne consiste pas à passer les arguments à `CreateDefaultBuilder` , mais à autoriser la méthode de la `ConfigurationBuilder` méthode `AddCommandLine` à traiter à la fois les arguments et le dictionnaire de mappage de commutateur.

## <a name="hierarchical-configuration-data"></a>Données de configuration hiérarchiques

L’API de configuration lit les données de configuration hiérarchiques en aplatit les données hiérarchiques à l’aide d’un délimiteur dans les clés de configuration.

L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le  *appsettings.json* fichier suivant :

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

La meilleure façon de lire des données de configuration hiérarchiques consiste à utiliser le modèle d’options. Pour plus d’informations, consultez [lier des données de configuration hiérarchiques](#optpat) dans ce document.

Les méthodes <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> et <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sont disponibles pour isoler les sections et les enfants d’une section dans les données de configuration. Ces méthodes sont décrites plus loin dans [GetSection, GetChildren et Exists](#getsection).

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a>Clés et valeurs de configuration

Clés de configuration :

* Ne respectent pas la casse. Par exemple, `ConnectionString` et `connectionstring` sont traités en tant que clés équivalentes.
* Si une clé et une valeur sont définies dans plusieurs fournisseurs de configuration, la valeur du dernier fournisseur ajouté est utilisée. Pour plus d’informations, consultez [configuration par défaut](#default).
* Clés hiérarchiques
  * Dans l’API Configuration, un séparateur sous forme de signe deux-points (`:`) fonctionne sur toutes les plateformes.
  * Dans les variables d’environnement, un séparateur sous forme de signe deux-points peut ne pas fonctionner sur toutes les plateformes. Un trait de soulignement double, `__` , est pris en charge par toutes les plateformes et est automatiquement converti en deux-points `:` .
  * Dans Azure Key Vault, les clés hiérarchiques utilisent `--` comme séparateur. Le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) remplace automatiquement `--` par un `:` lorsque les secrets sont chargés dans la configuration de l’application.
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration. La liaison de tableau est décrite dans la section [Lier un tableau à une classe](#boa).

Valeurs de configuration :

* Sont des chaînes.
* Les valeurs NULL ne peuvent pas être stockées dans la configuration ou liées à des objets.

<a name="cp"></a>

## <a name="configuration-providers"></a>Fournisseurs de configuration

Le tableau suivant présente les fournisseurs de configuration disponibles pour les applications ASP.NET Core.

| Fournisseur | Fournit la configuration à partir de |
| -------- | ----------------------------------- |
| [Fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) | Azure Key Vault |
| [Fournisseur de configuration Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration |
| [Fournisseur de configuration de ligne de commande](#clcp) | Paramètres de ligne de commande |
| [Fournisseur de configuration personnalisé](#custom-configuration-provider) | Source personnalisée |
| [Fournisseur de configuration des variables d’environnement](#evcp) | Variables d'environnement |
| [Fournisseur de configuration de fichier](#file-configuration-provider) | Fichiers INI, JSON et XML |
| [Fournisseur de configuration de clé par fichier](#key-per-file-configuration-provider) | Fichiers de répertoire |
| [Fournisseur de configuration de la mémoire](#memory-configuration-provider) | Collections en mémoire |
| [Secrets utilisateur](xref:security/app-secrets) | Fichier dans le répertoire de profil utilisateur |

Les sources de configuration sont lues dans l’ordre dans lequel leurs fournisseurs de configuration sont spécifiés. Commandez des fournisseurs de configuration dans le code pour répondre aux priorités des sources de configuration sous-jacentes requises par l’application.

Une séquence type des fournisseurs de configuration est la suivante :

1. *appsettings.json*
1. *appSettings*. `Environment` . *JSON*
1. [Secrets utilisateur](xref:security/app-secrets)
1. Variables d’environnement à l’aide du [fournisseur de configuration des variables d’environnement](#evcp).
1. Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line-configuration-provider).

Une pratique courante consiste à ajouter le dernier fournisseur de configuration de ligne de commande dans une série de fournisseurs pour permettre aux arguments de ligne de commande de remplacer la configuration définie par les autres fournisseurs.

La séquence de fournisseurs précédente est utilisée dans la [configuration par défaut](#default).

<a name="constr"></a>

### <a name="connection-string-prefixes"></a>Préfixes des chaînes de connexion

L’API de configuration a des règles de traitement spéciales pour quatre variables d’environnement de chaîne de connexion. Ces chaînes de connexion sont impliquées dans la configuration des chaînes de connexion Azure pour l’environnement de l’application. Les variables d’environnement avec les préfixes indiqués dans le tableau sont chargées dans l’application avec la [configuration par défaut](#default) ou lorsqu’aucun préfixe n’est fourni à `AddEnvironmentVariables` .

| Préfixe de la chaîne de connexion | Fournisseur |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Fournisseur personnalisé |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Quand une variable d’environnement est découverte et chargée dans la configuration avec l’un des quatre préfixes indiqués dans le tableau :

* La clé de configuration est créée en supprimant le préfixe de la variable d’environnement et en ajoutant une section de clé de configuration (`ConnectionStrings`).
* Une nouvelle paire clé-valeur de configuration est créée qui représente le fournisseur de connexion de base de données (à l’exception de `CUSTOMCONNSTR_`, qui ne possède aucun fournisseur indiqué).

| Clé de variable d’environnement | Clé de configuration convertie | Entrée de configuration de fournisseur                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Entrée de configuration non créée.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Clé : `ConnectionStrings:{KEY}_ProviderName` :<br>Valeur: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Clé : `ConnectionStrings:{KEY}_ProviderName` :<br>Valeur: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Clé : `ConnectionStrings:{KEY}_ProviderName` :<br>Valeur: `System.Data.SqlClient`  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a>Fournisseur de configuration de fichier

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> est la classe de base pour charger la configuration à partir du système de fichiers. Les fournisseurs de configuration suivants dérivent de `FileConfigurationProvider` :

* [Fournisseur de configuration INI](#ini-configuration-provider)
* [Fournisseur de configuration JSON](#jcp)
* [Fournisseur de configuration XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Fournisseur de configuration INI

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier INI lors de l’exécution.

Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

Dans le code précédent, les paramètres des *MyIniConfig.ini* et  *MyIniConfig*. `Environment` les fichiers *ini* sont remplacés par les paramètres dans le :

* [Fournisseur de configuration des variables d’environnement](#evcp)
* [Fournisseur de configuration de ligne de commande](#clcp).

L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyIniConfig.ini* suivant :

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a>Fournisseur de configuration JSON

Le <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge la configuration à partir de paires clé-valeur de fichier JSON.

Les surcharges peuvent spécifier :

* Si le fichier est facultatif.
* Si la configuration est rechargée quand le fichier est modifié.

Considérez le code suivant :

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

Le code précédent :

* Configure le fournisseur de configuration JSON pour charger le *MyConfig.jssur* le fichier avec les options suivantes :
  * `optional: true`: Le fichier est facultatif.
  * `reloadOnChange: true` : Le fichier est rechargé lorsque des modifications sont enregistrées.
* Lit les [fournisseurs de configuration par défaut](#default) avant l' *MyConfig.jssur* le fichier. Les paramètres dans le *MyConfig.js* paramètre de remplacement de fichier dans les fournisseurs de configuration par défaut, y compris le [fournisseur de configuration des variables d’environnement](#evcp) et le fournisseur de configuration de ligne de [commande](#clcp).

En général, vous ***ne souhaitez pas*** qu’une valeur de substitution de fichier JSON personnalisée soit définie dans le fournisseur de configuration des [variables d’environnement](#evcp) et dans le fournisseur de configuration de [ligne de commande](#clcp).

Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

Dans le code précédent, les paramètres de la *MyConfig.jssur* et  *MyConfig*. `Environment` . fichiers *JSON* :

* Substituez les paramètres dans *appsettings.json* et *appSettings*. `Environment` fichiers *JSON* .
* Sont remplacées par les paramètres dans le [fournisseur de configuration des variables d’environnement](#evcp) et le fournisseur de configuration de ligne de [commande](#clcp).

L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient les  *MyConfig.jssuivantes sur* le fichier :

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a>Fournisseur de configuration XML

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier XML lors de l’exécution.

Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

Dans le code précédent, les paramètres des *MyXMLFile.xml* et  *MyXMLFile*. `Environment` les fichiers *XML* sont remplacés par les paramètres dans le :

* [Fournisseur de configuration des variables d’environnement](#evcp)
* [Fournisseur de configuration de ligne de commande](#clcp).

L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyXMLFile.xml* suivant :

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Les éléments répétitifs qui utilisent le même nom d’élément fonctionnent si l’attribut `name` est utilisé pour distinguer les éléments :

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

Le code suivant lit le fichier de configuration précédent et affiche les clés et les valeurs :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

Les attributs peuvent être utilisés pour fournir des valeurs :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

Le fichier de configuration précédent charge les clés suivantes avec `value` :

* key:attribute
* section:key:attribute

## <a name="key-per-file-configuration-provider"></a>Fournisseur de configuration de clé par fichier

Le <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> utilise les fichiers d’un répertoire en tant que paires clé-valeur de configuration. La clé est le nom de fichier. La valeur contient le contenu du fichier. Le fournisseur de configuration par fichier clé est utilisé dans les scénarios d’hébergement de l’ancrage.

Pour activer la configuration clé par fichier, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>. Le `directoryPath` aux fichiers doit être un chemin d’accès absolu.

Les surcharges permettent de spécifier :

* Un délégué `Action<KeyPerFileConfigurationSource>` qui configure la source.
* Si le répertoire est facultatif et le chemin d’accès au répertoire.

Le double trait de soulignement (`__`) est utilisé comme un délimiteur de clé de configuration dans les noms de fichiers. Par exemple, le nom de fichier `Logging__LogLevel__System` génère la clé de configuration `Logging:LogLevel:System`.

Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a>Fournisseur de configuration de la mémoire

Le <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> utilise une collection en mémoire en tant que paires clé-valeur de configuration.

Le code suivant ajoute une collection de mémoire au système de configuration :

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche les paramètres de configuration précédents :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Dans le code précédent, `config.AddInMemoryCollection(Dict)` est ajouté après les [fournisseurs de configuration par défaut](#default). Pour obtenir un exemple de classement des fournisseurs de configuration, consultez [fournisseur de configuration JSON](#jcp).

Pour un autre exemple, consultez [lier un tableau](#boa) à l’aide de `MemoryConfigurationProvider` .

::: moniker-end
::: moniker range=">= aspnetcore-5.0"

<a name="kestrel"></a>

## <a name="kestrel-endpoint-configuration"></a>Configuration de point de terminaison Kestrel

La configuration de point de terminaison spécifique à Kestrel remplace toutes les configurations [de point de terminaison inter-serveurs](xref:fundamentals/servers/index) . Les configurations de point de terminaison entre serveurs sont les suivantes :

  * [UseUrls](xref:fundamentals/host/web-host#server-urls)
  * `--urls` sur la [ligne de commande](xref:fundamentals/configuration/index#command-line)
  * La [variable d’environnement](xref:fundamentals/configuration/index#environment-variables)`ASPNETCORE_URLS`

Considérez le *appsettings.json* fichier suivant utilisé dans une application web ASP.net Core :

[!code-json[](~/fundamentals/configuration/index/samples_snippets/5.x/appsettings.json?highlight=2-8)]

Lorsque le balisage en surbrillance précédent est utilisé dans une application Web ASP.NET Core ***et*** que l’application est lancée sur la ligne de commande avec la configuration de point de terminaison inter-serveurs suivante :

`dotnet run --urls="https://localhost:7777"`

Kestrel crée une liaison avec le point de terminaison configuré spécifiquement pour Kestrel dans le *appsettings.json* fichier ( `https://localhost:9999` ) et non `https://localhost:7777` .

Considérez le point de terminaison spécifique à Kestrel configuré comme une variable d’environnement :

`set Kestrel__Endpoints__Https__Url=https://localhost:8888`

Dans la variable d’environnement précédente, `Https` est le nom du point de terminaison spécifique à Kestrel. Le *appsettings.json* fichier précédent définit également un point de terminaison spécifique à Kestrel nommé `Https` . Par [défaut](#default-configuration), les variables d’environnement utilisant le [fournisseur de configuration des variables d’environnement](#evcp) sont lues après *appSettings.* `Environment` *. JSON*, par conséquent, la variable d’environnement précédente est utilisée pour le `Https` point de terminaison.

::: moniker-end
::: moniker range=">= aspnetcore-3.0"

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrait une valeur unique de la configuration avec une clé spécifiée et la convertit en type spécifié :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

Dans le code précédent, si est `NumberKey` introuvable dans la configuration, la valeur par défaut de `99` est utilisée.

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren et Exists

Pour les exemples qui suivent, prenez en compte les *MySubsection.jssuivantes sur* le fichier :

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

Le code suivant ajoute *MySubsection.js* aux fournisseurs de configuration :

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a>GetSection

[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) retourne une sous-section de configuration avec la clé de sous-section spécifiée.

Le code suivant retourne des valeurs pour `section1` :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

Le code suivant retourne des valeurs pour `section2:subsection0` :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

`GetSection` ne retourne jamais `null`. Si aucune section correspondante n’est trouvée, une valeur `IConfigurationSection` vide est retournée.

Quand `GetSection` retourne une section correspondante, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> n’est pas rempli. <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> et <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> sont retournés quand la section existe.

### <a name="getchildren-and-exists"></a>GetChildren et EXISTS

Le code suivant appelle [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) et retourne des valeurs pour `section2:subsection0` :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

Le code précédent appelle [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pour vérifier l’existence de la section :

 <a name="boa"></a>

## <a name="bind-an-array"></a>Lier un tableau

[ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) prend en charge les tableaux de liaison aux objets à l’aide d’index de tableau dans les clés de configuration. Tout format de tableau qui expose un segment de clé numérique est capable d’effectuer une liaison de tableau à un tableau de classes [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .

Prenez *MyArray.js* à partir de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

Le code suivant ajoute *MyArray.js* aux fournisseurs de configuration :

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

Le code suivant lit la configuration et affiche les valeurs :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Le code précédent retourne la sortie suivante :

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

Dans la sortie précédente, l’index 3 a la valeur `value40` , ce qui correspond à `"4": "value40",` dans *MyArray.jssur*. Les index de tableau liés sont continus et non liés à l’index de clé de configuration. Le Binder de configuration n’est pas en capacité à lier des valeurs null ou à créer des entrées NULL dans des objets liés

Le code suivant charge la `array:entries` configuration avec la <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> méthode d’extension :

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

Le code suivant lit la configuration dans `arrayDict` `Dictionary` et affiche les valeurs :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Le code précédent retourne la sortie suivante :

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

L’index &num;3 dans l’objet lié contient les données de configuration pour la clé de configuration `array:4` et sa valeur de `value4`. Lorsque les données de configuration contenant un tableau sont liées, les index de tableau dans les clés de configuration sont utilisés pour itérer les données de configuration lors de la création de l’objet. Une valeur null ne peut pas être conservée dans des données de configuration, et une entrée à valeur null n’est pas créée dans un objet lié quand un tableau dans des clés de configuration ignore un ou plusieurs index.

L’élément de configuration manquant pour l’index &num; 3 peut être fourni avant la liaison à l' `ArrayExample` instance par n’importe quel fournisseur de configuration qui lit la &num; paire clé/valeur de l’index 3. Examinez le *Value3.jssuivant sur* le fichier à partir de l’exemple de téléchargement :

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

Le code suivant comprend la configuration de *Value3.jssur* et `arrayDict` `Dictionary` :

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

Le code suivant lit la configuration précédente et affiche les valeurs :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Le code précédent retourne la sortie suivante :

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

Les fournisseurs de configuration personnalisés ne sont pas obligés d’implémenter la liaison de tableau.

## <a name="custom-configuration-provider"></a>Fournisseur de configuration personnalisé

L’exemple d’application montre comment créer un fournisseur de configuration de base qui lit les paires clé-valeur de configuration à partir d’une base de données à l’aide [d’Entity Framework (EF)](/ef/core/).

Le fournisseur présente les caractéristiques suivantes :

* La base de données en mémoire EF est utilisée à des fins de démonstration. Pour utiliser une base de données qui nécessite une chaîne de connexion, implémentez un autre `ConfigurationBuilder` pour fournir la chaîne de connexion à partir d’un autre fournisseur de configuration.
* Le fournisseur lit une table de base de données dans la configuration au démarrage. Le fournisseur n’interroge pas la base de données par clé.
* Le rechargement en cas de changement n’est pas implémenté, par conséquent, la mise à jour la base de données après le démarrage de l’application n’a aucun effet sur la configuration de l’application.

Définissez une entité `EFConfigurationValue` pour le stockage des valeurs de configuration dans la base de données.

*Models/EFConfigurationValue.cs* :

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Ajoutez un `EFConfigurationContext` pour stocker les valeurs configurées et y accéder.

*EFConfigurationProvider/EFConfigurationContext.cs* :

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Créez une classe qui implémente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.

*EFConfigurationProvider/EFConfigurationSource.cs* :

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Créez le fournisseur de configuration personnalisé en héritant de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>. Le fournisseur de configuration initialise la base de données quand elle est vide. Étant donné que les [clés de configuration ne](#keys)respectent pas la casse, le dictionnaire utilisé pour initialiser la base de données est créé avec le comparateur ne respectant pas la casse ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).

*EFConfigurationProvider/EFConfigurationProvider.cs* :

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Une méthode d’extension `AddEFConfiguration` permet d’ajouter la source de configuration à un `ConfigurationBuilder`.

*Extensions/EntityFrameworkExtensions.cs* :

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

Le code suivant montre comment utiliser le `EFConfigurationProvider` personnalisé dans *Program.cs* :

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a>Configuration de l’accès au démarrage

Le code suivant affiche les données de configuration dans les `Startup` méthodes :

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

Pour obtenir un exemple d’accès à la configuration à l’aide des méthodes pratiques de démarrage, consultez [Démarrage de l’application : méthodes pratiques](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-razor-pages"></a>Configuration de l’accès dans les Razor pages

Le code suivant affiche les données de configuration dans une Razor page :

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

Dans le code suivant, `MyOptions` est ajouté au conteneur de services avec <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> et lié à la configuration :

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

La balise suivante utilise la [`@inject`](xref:mvc/views/razor#inject) Razor directive pour résoudre et afficher les valeurs des options :

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a>Configuration de l’accès dans un fichier de vue MVC

Le code suivant affiche les données de configuration dans une vue MVC :

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a>Configurer des options avec un délégué

Les options configurées dans un délégué remplacent les valeurs définies dans les fournisseurs de configuration.

La configuration d’options avec un délégué est illustrée dans l’exemple 2 de l’exemple d’application.

Dans le code suivant, un <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service est ajouté au conteneur de services. Il utilise un délégué pour configurer des valeurs pour `MyOptions` :

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

Le code suivant affiche les valeurs des options :

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

Dans l’exemple précédent, les valeurs de `Option1` et `Option2` sont spécifiées dans, *appsettings.json* puis remplacées par le délégué configuré.

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a>Configuration de l’hôte ou configuration de l’application

Avant que l’application ne soit configurée et démarrée, un *hôte* est configuré et lancé. L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications. L’application et l’hôte sont configurés à l’aide des fournisseurs de configuration décrits dans cette rubrique. Les paires clé-valeur de la configuration de l’hôte sont également incluses dans la configuration de l’application. Pour plus d’informations sur l’utilisation des fournisseurs de configuration lors de la génération de l’hôte et l’impact des sources de configuration sur la configuration de l’hôte, consultez <xref:fundamentals/index#host>.

<a name="dhc"></a>

## <a name="default-host-configuration"></a>Configuration de l’hôte par défaut

Pour plus de détails sur la configuration par défaut lors de l’utilisation de l’[hôte Web](xref:fundamentals/host/web-host), consultez la [version ASP.NET Core 2.2. de cette rubrique](?view=aspnetcore-2.2&preserve-view=true).

* La configuration de l’hôte est fournie à partir des éléments suivants :
  * Les variables d’environnement précédées du préfixe `DOTNET_` (par exemple, `DOTNET_ENVIRONMENT` ) à l’aide du [fournisseur de configuration des variables d’environnement](#environment-variables). Le préfixe (`DOTNET_`) est supprimé lorsque les paires clé-valeur de la configuration sont chargées.
  * Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line-configuration-provider).
* La configuration par défaut de l’hôte Web est établie (`ConfigureWebHostDefaults`) :
  * Kestrel est utilisé comme serveur web et configuré à l’aide des fournisseurs de configuration de l’application.
  * Ajoutez l’intergiciel de filtrage d’hôtes.
  * Ajoutez l’intergiciel d’en-têtes transférés si la variable d'environnement `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est définie sur `true`.
  * Activez l’intégration d’IIS.

## <a name="other-configuration"></a>Autre configuration

Cette rubrique se rapporte uniquement à la configuration de l' *application*. D’autres aspects de l’exécution et de l’hébergement des applications ASP.NET Core sont configurés à l’aide des fichiers de configuration non traités dans cette rubrique :

* *launch.js* / *launchSettings.jssur* sont des fichiers de configuration d’outils pour l’environnement de développement, décrits ci-après :
  * Dans <xref:fundamentals/environments#development> .
  * Dans l’ensemble de la documentation dans lequel les fichiers sont utilisés pour configurer des applications ASP.NET Core pour les scénarios de développement.
* *web.config* est un fichier de configuration de serveur, décrit dans les rubriques suivantes :
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Les variables d’environnement définies dans *launchSettings.jslors de* la substitution de celles définies dans l’environnement système.

Pour plus d’informations sur la migration de la configuration d’application à partir de versions antérieures de ASP.NET, consultez <xref:migration/proper-to-2x/index#store-configurations> .

## <a name="add-configuration-from-an-external-assembly"></a>Ajouter la configuration à partir d’un assembly externe

Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application. Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Code source de configuration](https://github.com/dotnet/runtime/tree/master/src/libraries/Microsoft.Extensions.Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

La configuration d’application dans ASP.NET Core est basée sur des paires clé-valeur établies par les *fournisseurs de configuration*. Les fournisseurs de configuration lisent les données de configuration dans les paires clé-valeur à partir de diverses sources de configuration :

* Azure Key Vault
* Azure App Configuration
* Arguments de ligne de commande
* Fournisseurs personnalisés (installés ou créés)
* Fichiers de répertoire
* Variables d'environnement
* Objets .NET en mémoire
* Fichiers de paramètres

Les packages de configuration pour les scénarios de fournisseur de configuration courants ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sont inclus implicitement dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Les exemples de code qui suivent et dans l’échantillon d’application utilisent l’espace de noms <xref:Microsoft.Extensions.Configuration> :

```csharp
using Microsoft.Extensions.Configuration;
```

Le *modèle d’options* est une extension des concepts de configuration décrits dans cette rubrique. Les options utilisent des classes pour représenter les groupes de paramètres associés. Pour plus d’informations, consultez <xref:fundamentals/configuration/options>.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="host-versus-app-configuration"></a>Configuration de l’hôte ou configuration de l’application

Avant que l’application ne soit configurée et démarrée, un *hôte* est configuré et lancé. L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications. L’application et l’hôte sont configurés à l’aide des fournisseurs de configuration décrits dans cette rubrique. Les paires clé-valeur de la configuration de l’hôte sont également incluses dans la configuration de l’application. Pour plus d’informations sur l’utilisation des fournisseurs de configuration lors de la génération de l’hôte et l’impact des sources de configuration sur la configuration de l’hôte, consultez <xref:fundamentals/index#host>.

## <a name="other-configuration"></a>Autre configuration

Cette rubrique se rapporte uniquement à la configuration de l' *application*. D’autres aspects de l’exécution et de l’hébergement des applications ASP.NET Core sont configurés à l’aide des fichiers de configuration non traités dans cette rubrique :

* *launch.js* / *launchSettings.jssur* sont des fichiers de configuration d’outils pour l’environnement de développement, décrits ci-après :
  * Dans <xref:fundamentals/environments#development> .
  * Dans l’ensemble de la documentation dans lequel les fichiers sont utilisés pour configurer des applications ASP.NET Core pour les scénarios de développement.
* *web.config* est un fichier de configuration de serveur, décrit dans les rubriques suivantes :
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Pour plus d’informations sur la migration de la configuration d’application à partir de versions antérieures de ASP.NET, consultez <xref:migration/proper-to-2x/index#store-configurations> .

## <a name="default-configuration"></a>Configuration par défaut

Les applications web basées sur les modèles ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) appellent <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> pendant la création d’un hôte. `CreateDefaultBuilder` fournit la configuration par défaut de l’application dans l’ordre suivant :

Les éléments suivants s’appliquent aux applications qui utilisent l’[hôte web](xref:fundamentals/host/web-host). Pour plus de détails sur la configuration par défaut lors de l’utilisation de l’[hôte générique](xref:fundamentals/host/generic-host), consultez la [dernière version de cette rubrique](xref:fundamentals/configuration/index).

* La configuration de l’hôte est fournie à partir des éléments suivants :
  * Des variables d’environnement préfixées avec `ASPNETCORE_` (par exemple, `ASPNETCORE_ENVIRONMENT`) à l’aide du [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider). Le préfixe (`ASPNETCORE_`) est supprimé lorsque les paires clé-valeur de la configuration sont chargées.
  * Des arguments de ligne de commande à l’aide du [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider).
* La configuration de l’application est fournie à partir des éléments suivants :
  * *appsettings.json* utilisation du [fournisseur de configuration de fichier](#file-configuration-provider).
  * *appsettings.{Environment}.json* à l’aide du [Fournisseur de configuration de fichier](#file-configuration-provider).
  * Les [secrets utilisateur](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development` à l’aide de l’assembly d’entrée
  * Des variables d’environnement à l’aide du [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider).
  * Des arguments de ligne de commande à l’aide du [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider).

## <a name="security"></a>Sécurité

Adoptez les pratiques suivantes pour sécuriser les données de configuration sensibles :

* Ne stockez jamais des mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte clair.
* N’utilisez aucun secret de production dans les environnements de développement ou de test.
* Spécifiez les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans un référentiel de code source.

Pour plus d'informations, voir les rubriques suivantes :

* <xref:fundamentals/environments>
* <xref:security/app-secrets>: Fournit des conseils sur l’utilisation de variables d’environnement pour stocker des données sensibles. L’outil Gestionnaire de secret utilise le fournisseur de configuration de fichiers pour stocker les secrets d’utilisateur dans un fichier JSON sur le système local. Le fournisseur de configuration de fichier est décrit plus loin dans cette rubrique.

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stocke en toute sécurité des secrets d’application pour les applications ASP.NET Core. Pour plus d’informations, consultez <xref:security/key-vault-configuration>.

## <a name="hierarchical-configuration-data"></a>Données de configuration hiérarchiques

L’API Configuration est capable de maintenir des données de configuration hiérarchiques en aplanissant les données hiérarchiques à l’aide d’un délimiteur dans les clés de configuration.

Dans le fichier JSON suivant, quatre clés existent dans une structure hiérarchique à deux sections :

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

Lorsque le fichier est lu dans la configuration, des clés uniques sont créées pour gérer la structure des données hiérarchiques d’origine de la source de configuration. Les sections et les clés sont aplanies à l’aide d’un signe deux-points (`:`) pour conserver la structure d’origine :

* section0:key0
* section0:key1
* section1:key0
* section1:key1

Les méthodes <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> et <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sont disponibles pour isoler les sections et les enfants d’une section dans les données de configuration. Ces méthodes sont décrites plus loin dans [GetSection, GetChildren et Exists](#getsection-getchildren-and-exists).

## <a name="conventions"></a>Conventions

### <a name="sources-and-providers"></a>Sources et fournisseurs

Au démarrage de l’application, les sources de configuration sont lues dans l’ordre où leurs fournisseurs de configuration sont spécifiés.

Les fournisseurs de configuration qui implémentent la détection des modifications peuvent recharger la configuration lorsqu’un paramètre sous-jacent est modifié. Par exemple, le fournisseur de configuration de fichier (décrit plus loin dans cette rubrique) et le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) implémentent la détection des modifications.

<xref:Microsoft.Extensions.Configuration.IConfiguration> est disponible dans le conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) de l’application. <xref:Microsoft.Extensions.Configuration.IConfiguration> peut être injecté dans une Razor page <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou un MVC <xref:Microsoft.AspNetCore.Mvc.Controller> pour obtenir la configuration de la classe.

Dans les exemples suivants, le `_config` champ est utilisé pour accéder aux valeurs de configuration :

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

Les fournisseurs de configuration ne peuvent pas utiliser le DI, car celui-ci n’est pas disponible lorsque les fournisseurs sont configurés par l’hôte.

### <a name="keys"></a>Keys

Les clés de configuration adoptent les conventions suivantes :

* Les clés ne respectent pas la casse. Par exemple, `ConnectionString` et `connectionstring` sont traités en tant que clés équivalentes.
* Si une valeur pour la même clé est définie par des fournisseurs de configuration identiques ou différents, la valeur utilisée est la dernière valeur définie sur la clé. Pour plus d’informations sur les clés JSON en double, consultez [ce problème GitHub](https://github.com/dotnet/extensions/issues/2381).
* Clés hiérarchiques
  * Dans l’API Configuration, un séparateur sous forme de signe deux-points (`:`) fonctionne sur toutes les plateformes.
  * Dans les variables d’environnement, un séparateur sous forme de signe deux-points peut ne pas fonctionner sur toutes les plateformes. Un trait de soulignement double (`__`) est pris en charge par toutes les plateformes et automatiquement transformé en signe deux-points.
  * Dans Azure Key Vault, les clés hiérarchiques utilisent `--` (deux tirets) comme séparateur. Écrivez du code pour remplacer les tirets par un signe deux-points lorsque les secrets sont chargés dans la configuration de l’application.
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration. La liaison de tableau est décrite dans la section [Lier un tableau à une classe](#bind-an-array-to-a-class).

### <a name="values"></a>Valeurs

Les valeurs de configuration adoptent les conventions suivantes :

* Les valeurs sont des chaînes.
* Les valeurs NULL ne peuvent pas être stockées dans la configuration ou liées à des objets.

## <a name="providers"></a>Fournisseurs

Le tableau suivant présente les fournisseurs de configuration disponibles pour les applications ASP.NET Core.

| Fournisseur | Fournit la configuration à partir de&hellip; |
| -------- | ----------------------------------- |
| [Fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) (rubrique *Sécurité*) | Azure Key Vault |
| [Fournisseur Azure App Configuration](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentation Azure) | Azure App Configuration |
| [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider) | Paramètres de ligne de commande |
| [Fournisseur de configuration personnalisé](#custom-configuration-provider) | Source personnalisée |
| [Fournisseur de configuration de variables d’environnement](#environment-variables-configuration-provider) | Variables d'environnement |
| [Fournisseur de configuration de fichier](#file-configuration-provider) | Fichiers (INI, JSON, XML) |
| [Fournisseur de configuration clé par fichier](#key-per-file-configuration-provider) | Fichiers de répertoire |
| [Fournisseur de configuration de mémoire](#memory-configuration-provider) | Collections en mémoire |
| [Secrets](xref:security/app-secrets) de l’utilisateur (rubriques relatives à la *sécurité* ) | Fichier dans le répertoire de profil utilisateur |

Au démarrage, les sources de configuration sont lues dans l’ordre où leurs fournisseurs de configuration sont spécifiés. Les fournisseurs de configuration décrits dans cette rubrique sont décrits par ordre alphabétique, et non pas dans l’ordre dans lequel le code les réorganise. Commandez des fournisseurs de configuration dans le code pour répondre aux priorités des sources de configuration sous-jacentes requises par l’application.

Une séquence type des fournisseurs de configuration est la suivante :

1. Fichiers ( *appsettings.json* , *appSettings. { Environment}. JSON*, où `{Environment}` est l’environnement d’hébergement actuel de l’application)
1. [Azure Key Vault](xref:security/key-vault-configuration)
1. [Secrets](xref:security/app-secrets) de l’utilisateur (environnement de développement uniquement)
1. Variables d'environnement
1. Arguments de ligne de commande

Une pratique courante consiste à placer le Fournisseur de configuration de ligne de commande en dernier dans une série de fournisseurs pour permettre aux arguments de ligne de commande de remplacer la configuration définie par les autres fournisseurs.

La séquence de fournisseurs précédente est utilisée lors de l’initialisation d’un nouveau générateur d’hôte `CreateDefaultBuilder` . Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).

## <a name="configure-the-host-builder-with-useconfiguration"></a>Configurer le générateur d’ordinateur hôte avec UseConfiguration

Pour configurer le générateur d’ordinateur hôte, appelez <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> sur le générateur d’hôte avec la configuration.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Appelez `ConfigureAppConfiguration` quand vous créez l’hôte pour spécifier les fournisseurs de configuration de l’application en plus de ceux ajoutés automatiquement par `CreateDefaultBuilder` :

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a>Remplacez la configuration précédente par des arguments de ligne de commande

Pour fournir une configuration d’application pouvant être remplacée par des arguments de ligne de commande, appelez `AddCommandLine` en dernier :

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a>Supprimer les fournisseurs ajoutés par CreateDefaultBuilder

Pour supprimer les fournisseurs ajoutés par `CreateDefaultBuilder` , appelez d’abord [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) sur [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a>Utiliser la configuration lors du démarrage de l’application

La configuration fournie à l’application dans `ConfigureAppConfiguration` est disponible lors du démarrage de l’application, notamment `Startup.ConfigureServices`. Pour plus d’informations, consultez la section [Accéder à la configuration lors du démarrage](#access-configuration-during-startup).

## <a name="command-line-configuration-provider"></a>Fournisseur de configuration de ligne de commande

<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> charge la configuration à partir des paires clé-valeur de l’argument de ligne de commande lors de l’exécution.

Pour activer la configuration en ligne de commande, la méthode d’extension <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> est appelée sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

`AddCommandLine` est appelé automatiquement quand `CreateDefaultBuilder(string [])` est appelé. Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).

`CreateDefaultBuilder` charge également :

* Configuration facultative à partir de *appsettings.json* et *appSettings. { Fichiers Environment}. JSON* .
* [Secrets](xref:security/app-secrets) de l’utilisateur dans l’environnement de développement.
* Variables d'environnement.

`CreateDefaultBuilder` ajoute en dernier le Fournisseur de configuration de ligne de commande. Les arguments de ligne de commande passés lors de l’exécution remplacent la configuration définie par les autres fournisseurs.

`CreateDefaultBuilder` agit lors de la construction de l’hôte. Par conséquent, la configuration de ligne de commande activée par `CreateDefaultBuilder` peut affecter la façon dont l’hôte est configuré.

Pour les applications basées sur les modèles ASP.NET Core, `AddCommandLine` a déjà été appelé par `CreateDefaultBuilder`. Pour ajouter des fournisseurs de configuration supplémentaires et conserver la capacité de remplacer leur configuration par des arguments de ligne de commande, appelez les fournisseurs supplémentaires de l’application dans `ConfigureAppConfiguration` et appelez `AddCommandLine` en dernier.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**Exemple**

L’exemple d’application tire parti de la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte, qui inclut un appel à <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.

1. Ouvrez une invite de commandes dans le répertoire du projet.
1. Fournissez un argument de ligne de commande à la commande `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.
1. Une fois que l’application est en cours d’exécution, ouvrez un navigateur à l’application à l’adresse `http://localhost:5000`.
1. Notez que la sortie contient la paire clé-valeur pour l’argument de ligne de commande de configuration fourni à `dotnet run`.

### <a name="arguments"></a>Arguments

La valeur doit suivre un signe égal (`=`) ou la clé doit avoir un préfixe (`--` ou `/`) lorsque la valeur suit un espace. La valeur n’est pas requise si un signe égal est utilisé (par exemple, `CommandLineKey=`).

| Préfixe de clé               | Exemple                                                |
| ------------------------ | ------------------------------------------------------ |
| Aucun préfixe                | `CommandLineKey1=value1`                               |
| Deux tirets (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Barre oblique (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |

Dans la même commande, ne mélangez pas des paires clé-valeur de l’argument de ligne de commande qui utilisent un signe égal avec des paires clé-valeur qui utilisent un espace.

Exemples de commandes :

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>Correspondances de commutateur

Les correspondances de commutateur permettent une logique de remplacement des noms de clés. Lors de la génération manuelle d’une configuration avec un <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , fournissez un dictionnaire de remplacements de commutateur à la <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> méthode.

Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande. Si la clé de ligne de commande est trouvée dans le dictionnaire, la valeur du dictionnaire (le remplacement de la clé) est repassée pour définir la paire clé-valeur dans la configuration de l’application. Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).

Règles des clés du dictionnaire de correspondances de commutateur :

* Les commutateurs doivent commencer par un tiret (`-`) ou un double tiret (`--`).
* Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.

Créez un dictionnaire des mappages de commutateurs. Dans l’exemple suivant, deux mappages de commutateurs sont créés :

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

Lorsque l’hôte est généré, appelez `AddCommandLine` avec le dictionnaire de mappages de commutateurs :

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

Pour les applications qui utilisent des mappages de commutateurs, l’appel à `CreateDefaultBuilder` ne doit pas passer d’arguments. L’appel `AddCommandLine` de la méthode `CreateDefaultBuilder` n’inclut pas de commutateurs mappés et il n’existe aucun moyen de transmettre le dictionnaire de correspondance de commutateur à `CreateDefaultBuilder`. La solution ne consiste pas à transmettre les arguments à `CreateDefaultBuilder`, mais plutôt à permettre à la méthode `AddCommandLine` de la méthode `ConfigurationBuilder` de traiter les arguments et le dictionnaire de mappage de commutateur.

Une fois le dictionnaire de correspondances de commutateur créé, il contient les données affichées dans le tableau suivant.

| Clé       | Valeur             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

Si les clés mappées au commutateur sont utilisées lors du démarrage de l’application, la configuration reçoit la valeur de configuration sur la clé fournie par le dictionnaire :

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

Après avoir exécuté la commande précédente, la configuration contient les valeurs indiquées dans le tableau suivant.

| Clé               | Valeur    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>Fournisseur de configuration de variables d’environnement

<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> charge la configuration à partir des paires clé-valeur de la variable d’environnement lors de l’exécution.

Pour activer la configuration des variables d’environnement, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[Azure App service](https://azure.microsoft.com/services/app-service/) permet de définir des variables d’environnement dans le portail Azure qui peuvent remplacer la configuration de l’application à l’aide du fournisseur de configuration des variables d’environnement. Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

`AddEnvironmentVariables` sert à charger les variables d’environnement préfixées avec `ASPNETCORE_` pour la [configuration hôte](#host-versus-app-configuration) lorsqu’un nouveau générateur d’hôte est initialisé avec l’[hôte web](xref:fundamentals/host/web-host) et que `CreateDefaultBuilder` est appelé. Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).

`CreateDefaultBuilder` charge également :

* Configuration de l’application à partir de variables d’environnement sans préfixe en appelant `AddEnvironmentVariables` sans préfixe.
* Configuration facultative à partir de *appsettings.json* et *appSettings. { Fichiers Environment}. JSON* .
* [Secrets](xref:security/app-secrets) de l’utilisateur dans l’environnement de développement.
* Arguments de ligne de commande

Le fournisseur de configuration de variables d’environnement est appelé une fois que la configuration est établie à partir des secrets utilisateur et des fichiers *appsettings*. Le fait d’appeler le fournisseur ainsi permet de lire les variables d’environnement pendant l’exécution pour substituer la configuration définie par les secrets utilisateur et les fichiers *appsettings*.

Pour fournir la configuration d’application à partir de variables d’environnement supplémentaires, appelez les fournisseurs supplémentaires de l’application dans `ConfigureAppConfiguration` et appelez `AddEnvironmentVariables` avec le préfixe :

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

Appelez `AddEnvironmentVariables` Last pour autoriser les variables d’environnement avec le préfixe spécifié à substituer des valeurs d’autres fournisseurs.

**Exemple**

L’exemple d’application tire parti de la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte, qui inclut un appel à `AddEnvironmentVariables`.

1. Exécutez l’exemple d’application. Ouvrez un navigateur vers l’application avec l’adresse `http://localhost:5000`.
1. Notez que la sortie contient la paire clé-valeur pour la variable d’environnement `ENVIRONMENT`. La valeur reflète l’environnement dans lequel l’application est en cours d’exécution, en général `Development` lors de l’exécution locale.

Pour que la liste des variables d’environnement restituée par l’application soit courte, l’application filtre les variables d’environnement. Consultez le fichier *Pages/Index.cshtml.cs* de l’exemple d’application.

Pour exposer toutes les variables d’environnement disponibles pour l’application, remplacez `FilteredConfiguration` dans *pages/index. cshtml. cs* par ce qui suit :

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>Préfixes

Les variables d’environnement chargées dans la configuration de l’application sont filtrées lors de la spécification d’un préfixe à la `AddEnvironmentVariables` méthode. Par exemple, pour filtrer les variables d’environnement sur le préfixe `CUSTOM_`, fournissez le préfixe au fournisseur de configuration :

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

Le préfixe est supprimé lorsque les paires clé-valeur de la configuration sont créées.

Lorsque le générateur d’hôte est créé, la configuration de l’hôte est fournie par des variables d’environnement. Pour plus d’informations sur le préfixe utilisé pour ces variables d’environnement, consultez la section [Configuration par défaut](#default-configuration).

**Préfixes des chaînes de connexion**

L’API Configuration possède des règles de traitement spéciales pour quatre variables d’environnement de chaîne de connexion impliquées dans la configuration des chaînes de connexion Azure pour l’environnement de l’application. Les variables d’environnement avec les préfixes indiqués dans le tableau sont chargées dans l’application si aucun préfixe n’est fourni à `AddEnvironmentVariables`.

| Préfixe de la chaîne de connexion | Fournisseur |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Fournisseur personnalisé |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Quand une variable d’environnement est découverte et chargée dans la configuration avec l’un des quatre préfixes indiqués dans le tableau :

* La clé de configuration est créée en supprimant le préfixe de la variable d’environnement et en ajoutant une section de clé de configuration (`ConnectionStrings`).
* Une nouvelle paire clé-valeur de configuration est créée qui représente le fournisseur de connexion de base de données (à l’exception de `CUSTOMCONNSTR_`, qui ne possède aucun fournisseur indiqué).

| Clé de variable d’environnement | Clé de configuration convertie | Entrée de configuration de fournisseur                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Entrée de configuration non créée.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Clé : `ConnectionStrings:{KEY}_ProviderName` :<br>Valeur: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Clé : `ConnectionStrings:{KEY}_ProviderName` :<br>Valeur: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Clé : `ConnectionStrings:{KEY}_ProviderName` :<br>Valeur: `System.Data.SqlClient`  |

**Exemple**

Une variable d’environnement de chaîne de connexion personnalisée est créée sur le serveur :

* Nom : `CUSTOMCONNSTR_ReleaseDB`
* Valeur: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`

Si `IConfiguration` est injecté et affecté à un champ nommé `_config` , lisez la valeur :

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a>Fournisseur de configuration de fichier

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> est la classe de base pour charger la configuration à partir du système de fichiers. Les fournisseurs de configuration suivants sont dédiés à des types de fichiers spécifiques :

* [Fournisseur de configuration INI](#ini-configuration-provider)
* [Fournisseur de configuration JSON](#json-configuration-provider)
* [Fournisseur de configuration XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Fournisseur de configuration INI

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier INI lors de l’exécution.

Pour activer la configuration du fichier INI, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Le signe deux-points peut servir de délimiteur de section dans la configuration d’un fichier INI.

Les surcharges permettent de spécifier :

* Si le fichier est facultatif.
* Si la configuration est rechargée quand le fichier est modifié.
* Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.

Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

Exemple générique d’un fichier de configuration INI :

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

Le fichier de configuration précédent charge les clés suivantes avec `value` :

* section0:key0
* section0:key1
* section1:subsection:key
* section2:subsection0:key
* section2:subsection1:key

### <a name="json-configuration-provider"></a>Fournisseur de configuration JSON

<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier JSON lors de l’exécution.

Pour activer la configuration du fichier JSON, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Les surcharges permettent de spécifier :

* Si le fichier est facultatif.
* Si la configuration est rechargée quand le fichier est modifié.
* Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.

`AddJsonFile` est appelé automatiquement deux fois lors de l’initialisation d’un nouveau générateur d’hôte `CreateDefaultBuilder` . La méthode est appelée pour charger la configuration à partir de :

* *appsettings.json*: Ce fichier est lu en premier. La version de l’environnement du fichier peut remplacer les valeurs fournies par le *appsettings.json* fichier.
* *appSettings. {Environment}. JSON*: la version de l’environnement du fichier est chargée à partir de [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).

Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).

`CreateDefaultBuilder` charge également :

* Variables d'environnement.
* [Secrets](xref:security/app-secrets) de l’utilisateur dans l’environnement de développement.
* Arguments de ligne de commande

Le Fournisseur de configuration JSON est établi en premier. Par conséquent, les secrets utilisateur, les variables d’environnement et les arguments de ligne de commande remplacent la configuration définie par les fichiers *appsettings*.

Appelez `ConfigureAppConfiguration` lors de la génération de l’hôte pour spécifier la configuration de l’application pour les fichiers autres que *appsettings.json* et *appSettings. { Environnement}. JSON*:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

**Exemple**

L’exemple d’application tire parti de la méthode de commodité statique `CreateDefaultBuilder` pour créer l’hôte, ce qui comprend deux appels à `AddJsonFile` :

* Le premier appel à `AddJsonFile` charge la configuration à partir de *appsettings.json* :

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* Le deuxième appel à `AddJsonFile` charge la configuration à partir de *appSettings. { Environnement}. JSON*. Pour *appsettings.Development.js* dans l’exemple d’application, le fichier suivant est chargé :

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. Exécutez l’exemple d’application. Ouvrez un navigateur vers l’application avec l’adresse `http://localhost:5000`.
1. La sortie contient des paires clé-valeur pour la configuration en fonction de l’environnement de l’application. Le niveau de journalisation de la clé `Logging:LogLevel:Default` est `Debug` lors de l’exécution de l’application dans l’environnement de développement.
1. Exécutez à nouveau l’exemple d’application dans l’environnement de production :
   1. Ouvrez le fichier *Properties/launchSettings.js* .
   1. Dans le `ConfigurationSample` profil, remplacez la valeur de la `ASPNETCORE_ENVIRONMENT` variable d’environnement par `Production` .
   1. Enregistrez le fichier et exécutez l’application avec `dotnet run` dans un interpréteur de commandes.
1. Les paramètres de la *appsettings.Development.js* qui ne se substituent plus aux paramètres dans *appsettings.json* . Le niveau de journalisation de la clé `Logging:LogLevel:Default` est `Warning` .

### <a name="xml-configuration-provider"></a>Fournisseur de configuration XML

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier XML lors de l’exécution.

Pour activer la configuration du fichier XML, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Les surcharges permettent de spécifier :

* Si le fichier est facultatif.
* Si la configuration est rechargée quand le fichier est modifié.
* Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.

Le nœud racine du fichier de configuration est ignoré lorsque les paires clé-valeur de la configuration sont créées. Ne spécifiez pas une définition de type de document (DTD) ou un espace de noms dans le fichier.

Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

Les fichiers de configuration XML peuvent utiliser des noms d’éléments distincts pour les sections répétitives :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

Le fichier de configuration précédent charge les clés suivantes avec `value` :

* section0:key0
* section0:key1
* section1:key0
* section1:key1

Les éléments répétitifs qui utilisent le même nom d’élément fonctionnent si l’attribut `name` est utilisé pour distinguer les éléments :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

Le fichier de configuration précédent charge les clés suivantes avec `value` :

* section:section0:key:key0
* section:section0:key:key1
* section:section1:key:key0
* section:section1:key:key1

Les attributs peuvent être utilisés pour fournir des valeurs :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

Le fichier de configuration précédent charge les clés suivantes avec `value` :

* key:attribute
* section:key:attribute

## <a name="key-per-file-configuration-provider"></a>Fournisseur de configuration clé par fichier

Le <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> utilise les fichiers d’un répertoire en tant que paires clé-valeur de configuration. La clé est le nom de fichier. La valeur contient le contenu du fichier. Le Fournisseur de configuration Clé par fichier est utilisé dans les scénarios d’hébergement de Docker.

Pour activer la configuration clé par fichier, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>. Le `directoryPath` aux fichiers doit être un chemin d’accès absolu.

Les surcharges permettent de spécifier :

* Un délégué `Action<KeyPerFileConfigurationSource>` qui configure la source.
* Si le répertoire est facultatif et le chemin d’accès au répertoire.

Le double trait de soulignement (`__`) est utilisé comme un délimiteur de clé de configuration dans les noms de fichiers. Par exemple, le nom de fichier `Logging__LogLevel__System` génère la clé de configuration `Logging:LogLevel:System`.

Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a>Fournisseur de configuration de mémoire

Le <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> utilise une collection en mémoire en tant que paires clé-valeur de configuration.

Pour activer la configuration de la collection en mémoire, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Le fournisseur de configuration peut être initialisé avec un `IEnumerable<KeyValuePair<String,String>>`.

Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application.

Dans l’exemple suivant, un dictionnaire de configuration est créé :

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

Le dictionnaire est utilisé avec un appel à `AddInMemoryCollection` pour fournir la configuration :

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrait une valeur unique de la configuration avec une clé spécifiée et la convertit en type de non-collection spécifié. Une surcharge accepte une valeur par défaut.

L’exemple suivant :

* Extrait la valeur de chaîne de la configuration avec la clé `NumberKey`. Si `NumberKey` est introuvable dans les clés de configuration, la valeur par défaut de `99` est utilisée.
* Tape la valeur comme `int`.
* Stocke la valeur dans la propriété `NumberConfig` pour une utilisation par la page.

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren et Exists

Pour les exemples qui suivent, utilisez le fichier JSON suivant. Quatre clés se trouvent dans deux sections, dont l’une inclut deux sous-sections :

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

Lorsque le fichier est lu dans la configuration, les clés hiérarchiques uniques suivantes sont créées pour stocker les valeurs de la configuration :

* section0:key0
* section0:key1
* section1:key0
* section1:key1
* section2:subsection0:key0
* section2:subsection0:key1
* section2:subsection1:key0
* section2:subsection1:key1

### <a name="getsection"></a>GetSection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrait une sous-section de la configuration avec la clé de sous-section spécifiée.

Pour retourner un <xref:Microsoft.Extensions.Configuration.IConfigurationSection> contenant uniquement les paires clé-valeur dans `section1`, appelez `GetSection` et fournissez le nom de section :

```csharp
var configSection = _config.GetSection("section1");
```

`configSection` n’a pas de valeur, seulement une clé et un chemin.

De même, pour obtenir les valeurs des clés dans `section2:subsection0`, appelez `GetSection` et fournissez le chemin d’accès de la section :

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

`GetSection` ne retourne jamais `null`. Si aucune section correspondante n’est trouvée, une valeur `IConfigurationSection` vide est retournée.

Quand `GetSection` retourne une section correspondante, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> n’est pas rempli. <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> et <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> sont retournés quand la section existe.

### <a name="getchildren"></a>GetChildren

Un appel à [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) sur `section2` obtient un `IEnumerable<IConfigurationSection>` qui inclut :

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a>Exists

Utilisez [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pour déterminer si une section de configuration existe :

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

Compte tenu des données d’exemple, `sectionExists` est `false`, car il n’y a pas de section `section2:subsection2` dans les données de configuration.

## <a name="bind-to-an-object-graph"></a>Établir une liaison à un graphe d’objets

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> est capable de lier l’intégralité d’un graphe d’objets POCO. Comme pour la liaison d’un objet simple, seules les propriétés accessibles en lecture/écriture publiques sont liées.

L’exemple contient un modèle `TvShow` dont le graphe d’objets inclut les classes `Metadata` et `Actors` (*Models/TvShow.cs*) :

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

L’exemple d’application a un fichier *tvshow.xml* contenant les données de configuration :

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

La configuration est liée à l’intégralité du graphe d’objets `TvShow` avec la méthode `Bind`. L’instance liée est affectée à une propriété pour le rendu :

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) lie et retourne le type spécifié. Il est plus pratique d’utiliser `Get<T>` que `Bind`. Le code suivant montre comment utiliser `Get<T>` avec l’exemple précédent :

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a>Lier un tableau à une classe

*L’exemple d’application illustre les concepts abordés dans cette section.*

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration. Tout format de tableau qui expose un segment de clé numérique ( `:0:` , `:1:` , &hellip; `:{n}:` ) est capable d’effectuer une liaison de tableau à un tableau de classes POCO.

> [!NOTE]
> La liaison est fournie par convention. Les fournisseurs de configuration personnalisés ne sont pas obligés d’implémenter la liaison de tableau.

**Traitement de tableau en mémoire**

Observez les valeurs et les clés de configuration indiquées dans le tableau suivant.

| Clé             | Valeur  |
| :-------------: | :----: |
| array:entries:0 | value0 |
| array:entries:1 | valeur1 |
| array:entries:2 | valeur2 |
| array:entries:4 | value4 |
| array:entries:5 | value5 |

Ces clés et valeurs sont chargées dans l’exemple d’application à l’aide du Fournisseur de configuration de mémoire :

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

Le tableau ignore une valeur pour l’index &num;3. Le binder de configuration n’est pas capable de lier des valeurs null ou de créer des entrées null dans les objets liés, ce qui deviendra clair dans un moment lorsque le résultat de liaison de ce tableau à un objet est illustré.

Dans l’exemple d’application, une classe POCO est disponible pour contenir les données de configuration liées :

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

Les données de configuration sont liées à l’objet :

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) la syntaxe peut également être utilisée, ce qui se traduit par un code plus compact :

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

L’objet lié, une instance de `ArrayExample`, reçoit les données de tableau à partir de la configuration.

| Index `ArrayExample.Entries` | Valeur `ArrayExample.Entries` |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | valeur1                       |
| 2                            | valeur2                       |
| 3                            | value4                       |
| 4                            | value5                       |

L’index &num;3 dans l’objet lié contient les données de configuration pour la clé de configuration `array:4` et sa valeur de `value4`. Lorsque des données de configuration contenant un tableau sont liées, les index de tableau dans les clés de configuration sont simplement utilisés pour itérer les données de configuration lors de la création de l’objet. Une valeur null ne peut pas être conservée dans des données de configuration, et une entrée à valeur null n’est pas créée dans un objet lié quand un tableau dans des clés de configuration ignore un ou plusieurs index.

L’élément de configuration manquant pour l’index &num;3 peut être fourni avant la liaison à l’instance `ArrayExample` par n’importe quel fournisseur de configuration qui génère la paire clé-valeur correcte dans la configuration. Si l’exemple inclut un Fournisseur de configuration JSON supplémentaire avec la paire clé-valeur manquante, `ArrayExample.Entries` correspond à l’intégralité du tableau de configuration :

*missing_value.json* :

```json
{
  "array:entries:3": "value3"
}
```

Dans `ConfigureAppConfiguration` :

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

La paire clé-valeur indiquée dans le tableau est chargée dans la configuration.

| Clé             | Valeur  |
| :-------------: | :----: |
| array:entries:3 | valeur3 |

Si l’instance de classe `ArrayExample` est liée une fois que le Fournisseur de configuration JSON inclut l’entrée pour l’index &num;3, le tableau `ArrayExample.Entries` inclut la valeur.

| Index `ArrayExample.Entries` | Valeur `ArrayExample.Entries` |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | valeur1                       |
| 2                            | valeur2                       |
| 3                            | valeur3                       |
| 4                            | value4                       |
| 5                            | value5                       |

**Traitement de tableau JSON**

Si un fichier JSON contient un tableau, les clés de configuration sont créés pour les éléments du tableau avec un index de section basé sur zéro. Dans le fichier de configuration suivant, `subsection` est un tableau :

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

Le Fournisseur de configuration JSON lit les données de configuration dans les paires clé-valeur suivantes :

| Clé                     | Valeur  |
| ----------------------- | :----: |
| json_array:key          | valueA |
| json_array:subsection:0 | valueB |
| json_array:subsection:1 | valueC |
| json_array:subsection:2 | valueD |

Dans l’exemple d’application, la classe POCO suivante est disponible pour lier les paires clé-valeur de configuration :

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

Après la liaison, `JsonArrayExample.Key` contient la valeur `valueA`. Les valeurs de la sous-section sont stockées dans la propriété de tableau POCO, `Subsection`.

| Index `JsonArrayExample.Subsection` | Valeur `JsonArrayExample.Subsection` |
| :---------------------------------: | :---------------------------------: |
| 0                                   | valueB                              |
| 1                                   | valueC                              |
| 2                                   | valueD                              |

## <a name="custom-configuration-provider"></a>Fournisseur de configuration personnalisé

L’exemple d’application montre comment créer un fournisseur de configuration de base qui lit les paires clé-valeur de configuration à partir d’une base de données à l’aide [d’Entity Framework (EF)](/ef/core/).

Le fournisseur présente les caractéristiques suivantes :

* La base de données en mémoire EF est utilisée à des fins de démonstration. Pour utiliser une base de données qui nécessite une chaîne de connexion, implémentez un autre `ConfigurationBuilder` pour fournir la chaîne de connexion à partir d’un autre fournisseur de configuration.
* Le fournisseur lit une table de base de données dans la configuration au démarrage. Le fournisseur n’interroge pas la base de données par clé.
* Le rechargement en cas de changement n’est pas implémenté, par conséquent, la mise à jour la base de données après le démarrage de l’application n’a aucun effet sur la configuration de l’application.

Définissez une entité `EFConfigurationValue` pour le stockage des valeurs de configuration dans la base de données.

*Models/EFConfigurationValue.cs* :

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Ajoutez un `EFConfigurationContext` pour stocker les valeurs configurées et y accéder.

*EFConfigurationProvider/EFConfigurationContext.cs* :

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Créez une classe qui implémente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.

*EFConfigurationProvider/EFConfigurationSource.cs* :

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Créez le fournisseur de configuration personnalisé en héritant de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>. Le fournisseur de configuration initialise la base de données quand elle est vide.

*EFConfigurationProvider/EFConfigurationProvider.cs* :

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Une méthode d’extension `AddEFConfiguration` permet d’ajouter la source de configuration à un `ConfigurationBuilder`.

*Extensions/EntityFrameworkExtensions.cs* :

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

Le code suivant montre comment utiliser le `EFConfigurationProvider` personnalisé dans *Program.cs* :

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a>Accéder à la configuration lors du démarrage

Injectez `IConfiguration` dans le constructeur `Startup` pour accéder aux valeurs de configuration dans `Startup.ConfigureServices`. Pour accéder à la configuration dans `Startup.Configure`, injectez `IConfiguration` directement dans la méthode ou utilisez l’instance à partir du constructeur :

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

Pour obtenir un exemple d’accès à la configuration à l’aide des méthodes pratiques de démarrage, consultez [Démarrage de l’application : méthodes pratiques](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a>Configuration de l’accès dans une Razor page pages ou une vue MVC

Pour accéder aux paramètres de configuration dans une Razor page pages ou une vue MVC, ajoutez une [directive using](xref:mvc/views/razor#using) ([référence C# : directive using](/dotnet/csharp/language-reference/keywords/using-directive)) pour l' [ espace de nomsMicrosoft.Extensions.Configfiguration](xref:Microsoft.Extensions.Configuration) et injectez <xref:Microsoft.Extensions.Configuration.IConfiguration> dans la page ou la vue.

Dans une Razor page pages :

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

Dans une vue MVC :

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a>Ajouter la configuration à partir d’un assembly externe

Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application. Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/configuration/options>

::: moniker-end

---
title: Pages d’aide d’API web ASP.NET Core avec Swagger/OpenAPI
author: RicoSuter
description: Ce tutoriel montre comment ajouter Swagger pour générer des pages d’aide et de documentation pour une application d’API web.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/06/2020
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
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: b4b27e6b845d960b4b92612b90938f0770f23170
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056671"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>Pages d’aide d’API web ASP.NET Core avec Swagger/OpenAPI

Par [Christoph Nienaber](https://twitter.com/zuckerthoben) et [Rico Suter](https://blog.rsuter.com/)

Lorsque vous consommez une API Web, la compréhension de ses différentes méthodes peut être difficile pour un développeur. [Swagger](https://swagger.io/), également connu sous le nom de [openapi](https://www.openapis.org/), résout le problème de génération de la documentation et des pages d’aide utiles pour les API Web. Ses avantages sont, entre autres, la documentation interactive, la génération de SDK client et la découvertibilité des API.

Cet article décrit les implémentations .NET Swagger suivantes [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) et [NSwag](https://github.com/RicoSuter/NSwag) :

* **Swashbuckle.AspNetCore** est un projet open source pour la génération de documents Swagger pour des API web ASP.NET Core.

* **NSwag** est un autre projet open source pour générer des documents Swagger et intégrer [l’IU Swagger](https://swagger.io/swagger-ui/) ou [ReDoc](https://github.com/Rebilly/ReDoc) aux API web ASP.NET Core. NSwag fournit aussi des méthodes pour générer du code client C# et TypeScript pour votre API.

## <a name="what-is-swagger--openapi"></a>Qu’est-ce que Swagger/OpenAPI ?

Swagger est une spécification indépendante du langage pour décrire les API [REST](https://en.wikipedia.org/wiki/Representational_state_transfer). Le projet Swagger a été donné au projet [OpenAPI Initiative](https://www.openapis.org/) et s’appelle maintenant OpenAPI. Les deux noms sont utilisés indifféremment, mais OpenAPI est préféré. Il permet aux ordinateurs et aux utilisateurs de comprendre les fonctionnalités d’un service sans aucun accès direct à l’implémentation (code source, accès réseau, documentation). L’un des objectifs est de limiter la quantité de travail nécessaire pour connecter des services dissociés. Un autre objectif est de réduire le temps nécessaire pour documenter un service avec précision.

## <a name="openapi-specification-openapijson"></a>Spécification OpenAPI (openapi.js)

Le cœur du Flow OpenAPI est la spécification &mdash; par défaut, un document nommé *openapi.jssur* . Elle est générée par la chaîne d’outils OpenAPI (ou des implémentations tierces de celle-ci) en fonction de votre service. Elle décrit les fonctionnalités de votre API et comment y accéder avec HTTP. Elle gère l’interface utilisateur Swagger et est utilisée par la chaîne d’outils pour activer la découverte et la génération de code client. Voici un exemple de spécification OpenAPI, réduite par souci de concision :

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "API V1",
    "version": "v1"
  },
  "paths": {
    "/api/Todo": {
      "get": {
        "tags": [
          "Todo"
        ],
        "operationId": "ApiTodoGet",
        "responses": {
          "200": {
            "description": "Success",
            "content": {
              "text/plain": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "application/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "text/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              }
            }
          }
        }
      },
      "post": {
        …
      }
    },
    "/api/Todo/{id}": {
      "get": {
        …
      },
      "put": {
        …
      },
      "delete": {
        …
      }
    }
  },
  "components": {
    "schemas": {
      "ToDoItem": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer",
            "format": "int32"
          },
          "name": {
            "type": "string",
            "nullable": true
          },
          "isCompleted": {
            "type": "boolean"
          }
        },
        "additionalProperties": false
      }
    }
  }
}
```

## <a name="swagger-ui"></a>Interface utilisateur Swagger

L' [interface utilisateur de Swagger](https://swagger.io/swagger-ui/) offre une interface utilisateur basée sur le Web qui fournit des informations sur le service, à l’aide de la spécification openapi générée. Swashbuckle et NSwag ont une version incorporée à l’interface utilisateur Swagger pour que vous puissiez l’héberger dans votre application ASP.NET Core à l’aide d’un appel d’inscription d’intergiciel (middleware). L’IU web ressemble à ceci :

![Interface utilisateur Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Chaque méthode d’action publique dans vos contrôleurs peut être testée à partir de l’IU. Cliquez sur un nom de méthode pour développer la section. Ajoutez tous les paramètres nécessaires, puis cliquez sur **essayer !** .

![Exemple de test GET Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> La version de l’IU Swagger utilisée pour les captures d’écran est la version 2. Pour obtenir un exemple de la version 3, consultez [l’exemple Petstore](https://petstore.swagger.io/).

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Bien démarrer avec NSwag](xref:tutorials/get-started-with-nswag)

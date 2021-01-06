---
title: ASP.NET Core la documentation de l’API Web avec Swagger/OpenAPI
author: RicoSuter
description: Ce didacticiel fournit une procédure pas à pas pour l’ajout de Swagger afin de générer de la documentation et des pages d’aide pour une application API Web.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
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
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93062452"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a>ASP.NET Core la documentation de l’API Web avec Swagger/OpenAPI

Par [Christoph Nienaber](https://twitter.com/zuckerthoben) et [Rico Suter](https://blog.rsuter.com/)

Swagger (OpenAPI) est une spécification indépendante du langage pour décrire les API REST. Cela permet aux ordinateurs et aux êtres humains de comprendre les fonctionnalités d’une API REST sans accès direct au code source. Ses principaux objectifs sont les suivants :

* Réduisez la quantité de travail nécessaire pour connecter les services découplés.
* Réduire la durée nécessaire pour documenter un service avec précision.

Les deux principales implémentations OpenAPI pour .NET sont [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) et [NSwag](https://github.com/RicoSuter/NSwag), consultez :

* [Prise en main avec Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Prise en main avec NSwag](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a>OpenApi et Swagger

Le projet Swagger a été reporté à l’initiative OpenAPI dans 2015 et a été désigné comme OpenAPI. Les deux noms sont utilisés de façon interchangeable. Toutefois, « OpenAPI » fait référence à la spécification. « Swagger » fait référence à la famille de produits open source et commerciales de l’API smartbear qui fonctionnent avec la spécification OpenAPI. Les produits open source suivants, tels que [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), sont également sous le nom de famille Swagger, bien qu’ils ne soient pas publiés par l’API smartbear.

En résumé :

* OpenAPI est une spécification.
* Swagger est un outil qui utilise la spécification OpenAPI. Par exemple, OpenAPIGenerator et SwaggerUI.

## <a name="openapi-specification-openapijson"></a>Spécification OpenAPI (openapi.js)

La spécification OpenAPI est un document qui décrit les fonctionnalités de votre API. Le document est basé sur les annotations XML et d’attribut dans les contrôleurs et les modèles. Il s’agit de la partie fondamentale du Flow OpenAPI et est utilisée pour diriger les outils tels que SwaggerUI. Par défaut, il est nommé *openapi.js*. Voici un exemple de spécification OpenAPI, réduite par souci de concision :

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

Chaque méthode d’action publique dans vos contrôleurs peut être testée à partir de l’IU. Sélectionnez un nom de méthode pour développer la section. Ajoutez tous les paramètres nécessaires, puis sélectionnez **essayer !**.

![Exemple de test GET Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> La version de l’IU Swagger utilisée pour les captures d’écran est la version 2. Pour obtenir un exemple de la version 3, consultez [l’exemple Petstore](https://petstore.swagger.io/).

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Bien démarrer avec NSwag](xref:tutorials/get-started-with-nswag)

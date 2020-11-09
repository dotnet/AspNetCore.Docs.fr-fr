---
title: ASP.NET Core la documentation de l’API Web avec Swagger/OpenAPI
author: RicoSuter
description: Ce didacticiel fournit une procédure pas à pas pour l’ajout de Swagger afin de générer de la documentation et des pages d’aide pour une application API Web.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062452"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a><span data-ttu-id="e22cb-103">ASP.NET Core la documentation de l’API Web avec Swagger/OpenAPI</span><span class="sxs-lookup"><span data-stu-id="e22cb-103">ASP.NET Core web API documentation with Swagger / OpenAPI</span></span>

<span data-ttu-id="e22cb-104">Par [Christoph Nienaber](https://twitter.com/zuckerthoben) et [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="e22cb-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="e22cb-105">Swagger (OpenAPI) est une spécification indépendante du langage pour décrire les API REST.</span><span class="sxs-lookup"><span data-stu-id="e22cb-105">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="e22cb-106">Cela permet aux ordinateurs et aux êtres humains de comprendre les fonctionnalités d’une API REST sans accès direct au code source.</span><span class="sxs-lookup"><span data-stu-id="e22cb-106">It allows both computers and humans to understand the capabilities of a REST API without direct access to the source code.</span></span> <span data-ttu-id="e22cb-107">Ses principaux objectifs sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="e22cb-107">Its main goals are to:</span></span>

* <span data-ttu-id="e22cb-108">Réduisez la quantité de travail nécessaire pour connecter les services découplés.</span><span class="sxs-lookup"><span data-stu-id="e22cb-108">Minimize the amount of work needed to connect decoupled services.</span></span>
* <span data-ttu-id="e22cb-109">Réduire la durée nécessaire pour documenter un service avec précision.</span><span class="sxs-lookup"><span data-stu-id="e22cb-109">Reduce the amount of time needed to accurately document a service.</span></span>

<span data-ttu-id="e22cb-110">Les deux principales implémentations OpenAPI pour .NET sont [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) et [NSwag](https://github.com/RicoSuter/NSwag), consultez :</span><span class="sxs-lookup"><span data-stu-id="e22cb-110">The two main OpenAPI implementations for .NET are [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag), see:</span></span>

* [<span data-ttu-id="e22cb-111">Prise en main avec Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="e22cb-111">Getting Started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="e22cb-112">Prise en main avec NSwag</span><span class="sxs-lookup"><span data-stu-id="e22cb-112">Getting Started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a><span data-ttu-id="e22cb-113">OpenApi et Swagger</span><span class="sxs-lookup"><span data-stu-id="e22cb-113">OpenApi vs. Swagger</span></span>

<span data-ttu-id="e22cb-114">Le projet Swagger a été reporté à l’initiative OpenAPI dans 2015 et a été désigné comme OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="e22cb-114">The Swagger project was donated to the OpenAPI Initiative in 2015 and has since been referred to as OpenAPI.</span></span> <span data-ttu-id="e22cb-115">Les deux noms sont utilisés de façon interchangeable.</span><span class="sxs-lookup"><span data-stu-id="e22cb-115">Both names are used interchangeably.</span></span> <span data-ttu-id="e22cb-116">Toutefois, « OpenAPI » fait référence à la spécification.</span><span class="sxs-lookup"><span data-stu-id="e22cb-116">However, "OpenAPI" refers to the specification.</span></span> <span data-ttu-id="e22cb-117">« Swagger » fait référence à la famille de produits open source et commerciales de l’API smartbear qui fonctionnent avec la spécification OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="e22cb-117">"Swagger" refers to the family of open-source and commercial products from SmartBear that work with the OpenAPI Specification.</span></span> <span data-ttu-id="e22cb-118">Les produits open source suivants, tels que [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), sont également sous le nom de famille Swagger, bien qu’ils ne soient pas publiés par l’API smartbear.</span><span class="sxs-lookup"><span data-stu-id="e22cb-118">Subsequent open-source products, such as [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), also fall under the Swagger family name, despite not being released by SmartBear.</span></span>

<span data-ttu-id="e22cb-119">En résumé :</span><span class="sxs-lookup"><span data-stu-id="e22cb-119">In short:</span></span>

* <span data-ttu-id="e22cb-120">OpenAPI est une spécification.</span><span class="sxs-lookup"><span data-stu-id="e22cb-120">OpenAPI is a specification.</span></span>
* <span data-ttu-id="e22cb-121">Swagger est un outil qui utilise la spécification OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="e22cb-121">Swagger is tooling that uses the OpenAPI specification.</span></span> <span data-ttu-id="e22cb-122">Par exemple, OpenAPIGenerator et SwaggerUI.</span><span class="sxs-lookup"><span data-stu-id="e22cb-122">For example, OpenAPIGenerator and SwaggerUI.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="e22cb-123">Spécification OpenAPI (openapi.js)</span><span class="sxs-lookup"><span data-stu-id="e22cb-123">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="e22cb-124">La spécification OpenAPI est un document qui décrit les fonctionnalités de votre API.</span><span class="sxs-lookup"><span data-stu-id="e22cb-124">The OpenAPI specification is a document that describes the capabilities of your API.</span></span> <span data-ttu-id="e22cb-125">Le document est basé sur les annotations XML et d’attribut dans les contrôleurs et les modèles.</span><span class="sxs-lookup"><span data-stu-id="e22cb-125">The document is based on the XML and attribute annotations within the controllers and models.</span></span> <span data-ttu-id="e22cb-126">Il s’agit de la partie fondamentale du Flow OpenAPI et est utilisée pour diriger les outils tels que SwaggerUI.</span><span class="sxs-lookup"><span data-stu-id="e22cb-126">It's the core part of the OpenAPI flow and is used to drive tooling such as SwaggerUI.</span></span> <span data-ttu-id="e22cb-127">Par défaut, il est nommé *openapi.js* .</span><span class="sxs-lookup"><span data-stu-id="e22cb-127">By default, it's named *openapi.json* .</span></span> <span data-ttu-id="e22cb-128">Voici un exemple de spécification OpenAPI, réduite par souci de concision :</span><span class="sxs-lookup"><span data-stu-id="e22cb-128">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

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

## <a name="swagger-ui"></a><span data-ttu-id="e22cb-129">Interface utilisateur Swagger</span><span class="sxs-lookup"><span data-stu-id="e22cb-129">Swagger UI</span></span>

<span data-ttu-id="e22cb-130">L' [interface utilisateur de Swagger](https://swagger.io/swagger-ui/) offre une interface utilisateur basée sur le Web qui fournit des informations sur le service, à l’aide de la spécification openapi générée.</span><span class="sxs-lookup"><span data-stu-id="e22cb-130">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="e22cb-131">Swashbuckle et NSwag ont une version incorporée à l’interface utilisateur Swagger pour que vous puissiez l’héberger dans votre application ASP.NET Core à l’aide d’un appel d’inscription d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="e22cb-131">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="e22cb-132">L’IU web ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="e22cb-132">The web UI looks like this:</span></span>

![Interface utilisateur Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="e22cb-134">Chaque méthode d’action publique dans vos contrôleurs peut être testée à partir de l’IU.</span><span class="sxs-lookup"><span data-stu-id="e22cb-134">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="e22cb-135">Sélectionnez un nom de méthode pour développer la section.</span><span class="sxs-lookup"><span data-stu-id="e22cb-135">Select a method name to expand the section.</span></span> <span data-ttu-id="e22cb-136">Ajoutez tous les paramètres nécessaires, puis sélectionnez **essayer !** .</span><span class="sxs-lookup"><span data-stu-id="e22cb-136">Add any necessary parameters, and select **Try it out!** .</span></span>

![Exemple de test GET Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="e22cb-138">La version de l’IU Swagger utilisée pour les captures d’écran est la version 2.</span><span class="sxs-lookup"><span data-stu-id="e22cb-138">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="e22cb-139">Pour obtenir un exemple de la version 3, consultez [l’exemple Petstore](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="e22cb-139">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="e22cb-140">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="e22cb-140">Next steps</span></span>

* [<span data-ttu-id="e22cb-141">Bien démarrer avec Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="e22cb-141">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="e22cb-142">Bien démarrer avec NSwag</span><span class="sxs-lookup"><span data-stu-id="e22cb-142">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)

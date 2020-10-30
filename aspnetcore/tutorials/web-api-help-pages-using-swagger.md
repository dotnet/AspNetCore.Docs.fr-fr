---
title: Pages d’aide d’API web ASP.NET Core avec Swagger/OpenAPI
author: RicoSuter
description: Ce tutoriel montre comment ajouter Swagger pour générer des pages d’aide et de documentation pour une application d’API web.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: b4b27e6b845d960b4b92612b90938f0770f23170
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056671"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a><span data-ttu-id="117c0-103">Pages d’aide d’API web ASP.NET Core avec Swagger/OpenAPI</span><span class="sxs-lookup"><span data-stu-id="117c0-103">ASP.NET Core web API help pages with Swagger / OpenAPI</span></span>

<span data-ttu-id="117c0-104">Par [Christoph Nienaber](https://twitter.com/zuckerthoben) et [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="117c0-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="117c0-105">Lorsque vous consommez une API Web, la compréhension de ses différentes méthodes peut être difficile pour un développeur.</span><span class="sxs-lookup"><span data-stu-id="117c0-105">When consuming a web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="117c0-106">[Swagger](https://swagger.io/), également connu sous le nom de [openapi](https://www.openapis.org/), résout le problème de génération de la documentation et des pages d’aide utiles pour les API Web.</span><span class="sxs-lookup"><span data-stu-id="117c0-106">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="117c0-107">Ses avantages sont, entre autres, la documentation interactive, la génération de SDK client et la découvertibilité des API.</span><span class="sxs-lookup"><span data-stu-id="117c0-107">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="117c0-108">Cet article décrit les implémentations .NET Swagger suivantes [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) et [NSwag](https://github.com/RicoSuter/NSwag) :</span><span class="sxs-lookup"><span data-stu-id="117c0-108">In this article, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger implementations are showcased:</span></span>

* <span data-ttu-id="117c0-109">**Swashbuckle.AspNetCore** est un projet open source pour la génération de documents Swagger pour des API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="117c0-109">**Swashbuckle.AspNetCore** is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="117c0-110">**NSwag** est un autre projet open source pour générer des documents Swagger et intégrer [l’IU Swagger](https://swagger.io/swagger-ui/) ou [ReDoc](https://github.com/Rebilly/ReDoc) aux API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="117c0-110">**NSwag** is another open source project for generating Swagger documents and integrating [Swagger UI](https://swagger.io/swagger-ui/) or [ReDoc](https://github.com/Rebilly/ReDoc) into ASP.NET Core web APIs.</span></span> <span data-ttu-id="117c0-111">NSwag fournit aussi des méthodes pour générer du code client C# et TypeScript pour votre API.</span><span class="sxs-lookup"><span data-stu-id="117c0-111">Additionally, NSwag offers approaches to generate C# and TypeScript client code for your API.</span></span>

## <a name="what-is-swagger--openapi"></a><span data-ttu-id="117c0-112">Qu’est-ce que Swagger/OpenAPI ?</span><span class="sxs-lookup"><span data-stu-id="117c0-112">What is Swagger / OpenAPI?</span></span>

<span data-ttu-id="117c0-113">Swagger est une spécification indépendante du langage pour décrire les API [REST](https://en.wikipedia.org/wiki/Representational_state_transfer).</span><span class="sxs-lookup"><span data-stu-id="117c0-113">Swagger is a language-agnostic specification for describing [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs.</span></span> <span data-ttu-id="117c0-114">Le projet Swagger a été donné au projet [OpenAPI Initiative](https://www.openapis.org/) et s’appelle maintenant OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="117c0-114">The Swagger project was donated to the [OpenAPI Initiative](https://www.openapis.org/), where it's now referred to as OpenAPI.</span></span> <span data-ttu-id="117c0-115">Les deux noms sont utilisés indifféremment, mais OpenAPI est préféré.</span><span class="sxs-lookup"><span data-stu-id="117c0-115">Both names are used interchangeably; however, OpenAPI is preferred.</span></span> <span data-ttu-id="117c0-116">Il permet aux ordinateurs et aux utilisateurs de comprendre les fonctionnalités d’un service sans aucun accès direct à l’implémentation (code source, accès réseau, documentation).</span><span class="sxs-lookup"><span data-stu-id="117c0-116">It allows both computers and humans to understand the capabilities of a service without any direct access to the implementation (source code, network access, documentation).</span></span> <span data-ttu-id="117c0-117">L’un des objectifs est de limiter la quantité de travail nécessaire pour connecter des services dissociés.</span><span class="sxs-lookup"><span data-stu-id="117c0-117">One goal is to minimize the amount of work needed to connect disassociated services.</span></span> <span data-ttu-id="117c0-118">Un autre objectif est de réduire le temps nécessaire pour documenter un service avec précision.</span><span class="sxs-lookup"><span data-stu-id="117c0-118">Another goal is to reduce the amount of time needed to accurately document a service.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="117c0-119">Spécification OpenAPI (openapi.js)</span><span class="sxs-lookup"><span data-stu-id="117c0-119">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="117c0-120">Le cœur du Flow OpenAPI est la spécification &mdash; par défaut, un document nommé *openapi.jssur* .</span><span class="sxs-lookup"><span data-stu-id="117c0-120">The core to the OpenAPI flow is the specification&mdash;by default, a document named *openapi.json* .</span></span> <span data-ttu-id="117c0-121">Elle est générée par la chaîne d’outils OpenAPI (ou des implémentations tierces de celle-ci) en fonction de votre service.</span><span class="sxs-lookup"><span data-stu-id="117c0-121">It's generated by the OpenAPI tool chain (or third-party implementations of it) based on your service.</span></span> <span data-ttu-id="117c0-122">Elle décrit les fonctionnalités de votre API et comment y accéder avec HTTP.</span><span class="sxs-lookup"><span data-stu-id="117c0-122">It describes the capabilities of your API and how to access it with HTTP.</span></span> <span data-ttu-id="117c0-123">Elle gère l’interface utilisateur Swagger et est utilisée par la chaîne d’outils pour activer la découverte et la génération de code client.</span><span class="sxs-lookup"><span data-stu-id="117c0-123">It drives the Swagger UI and is used by the tool chain to enable discovery and client code generation.</span></span> <span data-ttu-id="117c0-124">Voici un exemple de spécification OpenAPI, réduite par souci de concision :</span><span class="sxs-lookup"><span data-stu-id="117c0-124">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

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

## <a name="swagger-ui"></a><span data-ttu-id="117c0-125">Interface utilisateur Swagger</span><span class="sxs-lookup"><span data-stu-id="117c0-125">Swagger UI</span></span>

<span data-ttu-id="117c0-126">L' [interface utilisateur de Swagger](https://swagger.io/swagger-ui/) offre une interface utilisateur basée sur le Web qui fournit des informations sur le service, à l’aide de la spécification openapi générée.</span><span class="sxs-lookup"><span data-stu-id="117c0-126">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="117c0-127">Swashbuckle et NSwag ont une version incorporée à l’interface utilisateur Swagger pour que vous puissiez l’héberger dans votre application ASP.NET Core à l’aide d’un appel d’inscription d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="117c0-127">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="117c0-128">L’IU web ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="117c0-128">The web UI looks like this:</span></span>

![Interface utilisateur Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="117c0-130">Chaque méthode d’action publique dans vos contrôleurs peut être testée à partir de l’IU.</span><span class="sxs-lookup"><span data-stu-id="117c0-130">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="117c0-131">Cliquez sur un nom de méthode pour développer la section.</span><span class="sxs-lookup"><span data-stu-id="117c0-131">Click a method name to expand the section.</span></span> <span data-ttu-id="117c0-132">Ajoutez tous les paramètres nécessaires, puis cliquez sur **essayer !** .</span><span class="sxs-lookup"><span data-stu-id="117c0-132">Add any necessary parameters, and click **Try it out!** .</span></span>

![Exemple de test GET Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="117c0-134">La version de l’IU Swagger utilisée pour les captures d’écran est la version 2.</span><span class="sxs-lookup"><span data-stu-id="117c0-134">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="117c0-135">Pour obtenir un exemple de la version 3, consultez [l’exemple Petstore](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="117c0-135">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="117c0-136">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="117c0-136">Next steps</span></span>

* [<span data-ttu-id="117c0-137">Bien démarrer avec Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="117c0-137">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="117c0-138">Bien démarrer avec NSwag</span><span class="sxs-lookup"><span data-stu-id="117c0-138">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)

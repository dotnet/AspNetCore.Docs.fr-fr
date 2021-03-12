---
title: configuration de gRPC pour .NET
author: jamesnk
description: Découvrez comment configurer gRPC pour les applications .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/23/2020
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
uid: grpc/configuration
ms.openlocfilehash: 3f03d774a2223dc1fc08adcbc85cdc9a2b63dea0
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588891"
---
# <a name="grpc-for-net-configuration"></a>configuration de gRPC pour .NET

## <a name="configure-services-options"></a>Configurer les options des services

les services gRPC sont configurés avec `AddGrpc` dans *Startup.cs*. Le tableau suivant décrit les options de configuration des services gRPC :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | Taille maximale du message, en octets, qui peut être envoyée à partir du serveur. Toute tentative d’envoi d’un message qui dépasse la taille de message maximale configurée entraîne une exception. Lorsque la valeur `null` est, la taille du message est illimitée. |
| MaxReceiveMessageSize | 4 Mo | Taille maximale du message, en octets, qui peut être reçue par le serveur. Si le serveur reçoit un message qui dépasse cette limite, il lève une exception. L’augmentation de cette valeur permet au serveur de recevoir des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire. Lorsque la valeur `null` est, la taille du message est illimitée. |
| EnableDetailedErrors | `false` | Si `true` la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de service. Par défaut, il s’agit de `false`. La définition `EnableDetailedErrors` de sur peut entraîner une `true` fuite d’informations sensibles. |
| CompressionProviders | gzip | Collection de fournisseurs de compression utilisée pour compresser et décompresser des messages. Des fournisseurs de compression personnalisés peuvent être créés et ajoutés à la collection. Les fournisseurs configurés par défaut prennent en charge la compression **gzip** . |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | Algorithme de compression utilisé pour compresser les messages envoyés à partir du serveur. L’algorithme doit correspondre à un fournisseur de compression dans `CompressionProviders` . Pour que l’algorithme compresse une réponse, le client doit indiquer qu’il prend en charge l’algorithme en l’envoyant dans l’en-tête **GRPC-Accept-Encoding** . |
| ResponseCompressionLevel | `null` | Niveau de compression utilisé pour compresser les messages envoyés à partir du serveur. |
| Intercepteurs | Aucun | Collection d’intercepteurs exécutés avec chaque appel gRPC. Les intercepteurs sont exécutés dans l’ordre dans lequel ils sont inscrits. Les intercepteurs configurés globalement sont exécutés avant les intercepteurs configurés pour un service unique. Pour plus d’informations sur les intercepteurs gRPC, consultez [intercepteurs gRPC et intergiciel](xref:grpc/migration#grpc-interceptors-vs-middleware). |
| IgnoreUnknownServices | `false` | Si `true` la valeur est, les appels aux services et méthodes inconnus ne retournent pas un État non **implémenté** et la demande passe au middleware inscrit suivant dans ASP.net core. |

Les options peuvent être configurées pour tous les services en fournissant un délégué d’options à l' `AddGrpc` appel dans `Startup.ConfigureServices` :

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Les options pour un seul service remplacent les options globales fournies dans `AddGrpc` et peuvent être configurées à l’aide de `AddServiceOptions<TService>` :

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Configurer les options du client

la configuration du client gRPC est activée `GrpcChannelOptions` . Le tableau suivant décrit les options de configuration des canaux gRPC :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| Gestionnaires | Nouvelle instance | `HttpMessageHandler`Utilisé pour effectuer des appels gRPC. Un client peut être configuré pour configurer un personnalisé `HttpClientHandler` ou ajouter des gestionnaires supplémentaires au pipeline HTTP pour les appels gRPC. Si aucun `HttpMessageHandler` n’est spécifié, une nouvelle `HttpClientHandler` instance est créée pour le canal avec suppression automatique. |
| HttpClient | `null` | `HttpClient`Utilisé pour effectuer des appels gRPC. Ce paramètre est une alternative à `HttpHandler` . |
| DisposeHttpClient | `false` | Si la valeur est définie sur `true` et si `HttpMessageHandler` ou `HttpClient` est spécifié, le `HttpHandler` ou `HttpClient` , respectivement, est supprimé lorsque `GrpcChannel` est supprimé. |
| LoggerFactory | `null` | `LoggerFactory`Utilisé par le client pour enregistrer des informations sur les appels gRPC. Une `LoggerFactory` instance peut être résolue à partir de l’injection de dépendances ou créée à l’aide de `LoggerFactory.Create` . Pour obtenir des exemples de configuration de la journalisation, consultez <xref:grpc/diagnostics#grpc-client-logging> . |
| MaxSendMessageSize | `null` | Taille maximale du message, en octets, qui peut être envoyée à partir du client. Toute tentative d’envoi d’un message qui dépasse la taille de message maximale configurée entraîne une exception. Lorsque la valeur `null` est, la taille du message est illimitée. |
| MaxReceiveMessageSize | 4 Mo | Taille maximale du message, en octets, qui peut être reçue par le client. Si le client reçoit un message qui dépasse cette limite, il lève une exception. L’augmentation de cette valeur permet au client de recevoir des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire. Lorsque la valeur `null` est, la taille du message est illimitée. |
| Informations d'identification | `null` | Instance de `ChannelCredentials`. Les informations d’identification sont utilisées pour ajouter des métadonnées d’authentification à des appels gRPC. |
| CompressionProviders | gzip | Collection de fournisseurs de compression utilisée pour compresser et décompresser des messages. Des fournisseurs de compression personnalisés peuvent être créés et ajoutés à la collection. Les fournisseurs configurés par défaut prennent en charge la compression **gzip** . |
| ThrowOperationCanceledOnCancellation | `false` | Si la valeur est `true` , les clients lèvent <xref:System.OperationCanceledException> une exception lorsqu’un appel est annulé ou que son échéance est dépassée. |
| MaxRetryAttempts | 5 | Nombre maximal de nouvelles tentatives. Cette valeur limite les nouvelles tentatives et les valeurs de tentative de couverture spécifiées dans la configuration du service. La définition de cette valeur seule ne permet pas d’activer les nouvelles tentatives. Les nouvelles tentatives sont activées dans la configuration du service, ce qui peut être fait à l’aide de `ServiceConfig` . Une `null` valeur supprime la limite maximale de nouvelles tentatives. Pour plus d’informations sur les nouvelles tentatives, consultez <xref:grpc/retries> . |
| MaxRetryBufferSize | 16 Mo | Taille maximale de la mémoire tampon en octets qui peut être utilisée pour stocker les messages envoyés lors de nouvelles tentatives ou d’appels de couverture. Si la limite de la mémoire tampon est dépassée, aucune nouvelle tentative n’est effectuée et tous les appels de couverture, mais l’un d’eux seront annulés. Cette limite est appliquée pour tous les appels effectués à l’aide du canal. Une `null` valeur supprime la limite maximale de la taille de la mémoire tampon des nouvelles tentatives. |
| <span style="word-break:normal;word-wrap:normal">MaxRetryBufferPerCallSize</span> | 1 Mo | Taille maximale de la mémoire tampon en octets qui peut être utilisée pour stocker les messages envoyés lors de nouvelles tentatives ou d’appels de couverture. Si la limite de la mémoire tampon est dépassée, aucune nouvelle tentative n’est effectuée et tous les appels de couverture, mais l’un d’eux seront annulés. Cette limite est appliquée à un appel. Une `null` valeur supprime la limite maximale de la taille de la mémoire tampon des nouvelles tentatives par appel. |
| ServiceConfig | `null` | Configuration du service pour un canal gRPC. Une configuration de service peut être utilisée pour configurer les [nouvelles tentatives gRPC](xref:grpc/retries). |

Le code suivant :

* Définit la taille maximale des messages d’envoi et de réception sur le canal.
* Crée un client.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>

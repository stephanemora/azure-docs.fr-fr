---
title: 'Kit de développement logiciel (SDK) Java Azure Cosmos DB v4 pour API SQL : notes de publication et ressources'
description: Découvrez le Kit de développement logiciel (SDK) Java Azure Cosmos DB v4 pour API SQL ainsi que le Kit de développement logiciel (SDK), notamment les dates de lancement, les dates de suppression et les modifications apportées entre chaque version du Kit de développement logiciel (SDK) Java Async SQL d’Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: b222a94ee754b24192261451d8ddc429886e705c
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725650"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Kit de développement logiciel (SDK) Java Azure Cosmos DB v4 pour API Core (SQL) : notes de publication et ressources
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Flux de modification .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Kit de développement logiciel (SDK) Java v4](sql-api-sdk-java-v4.md)
> * [Kit de développement logiciel (SDK) Java asynchrone v2](sql-api-sdk-async-java.md)
> * [Kit de développement logiciel (SDK) Java synchrone v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [API REST Resource Provider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Exécuteur en bloc – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Exécuteur en bloc – Java](sql-api-sdk-bulk-executor-java.md)

Le Kit de développement logiciel (SDK) Java Azure Cosmos DB v4 pour API Core (SQL) combine une API asynchrone et une API synchrone en un seul artefact Maven. Le Kit de développement logiciel (SDK) v4 offre des performances améliorées, de nouvelles fonctionnalités d’API et une prise en charge asynchrone basée sur le réacteur de projet et la [bibliothèque Netty](https://netty.io/). Les utilisateurs peuvent s’attendre à des performances améliorées avec le Kit de développement logiciel (SDK) Java Azure Cosmos DB v4 par rapport au [Kit de développement logiciel (SDK) Java Azure Cosmos DB v2 asynchrone](sql-api-sdk-async-java.md) et au [Kit de développement logiciel (SDK) Java Azure Cosmos DB v2 synchrone](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Ces notes de publication concernent uniquement le Kit de développement logiciel (SDK) Java Azure Cosmos DB v4. Si vous utilisez actuellement une version antérieure à v4, consultez le guide [Migrer votre application pour utiliser le SDK Java v4 Azure Cosmos DB](migrate-java-v4-sdk.md) afin d’obtenir de l’aide sur la mise à niveau vers v4.
>
> Voici trois étapes pour aller plus vite.
> 1. Installez le [Runtime Java minimal pris en charge, JDK 8,](/java/azure/jdk/?view=azure-java-stable) pour pouvoir utiliser le Kit de développement logiciel (SDK).
> 2. Utilisez le [guide de démarrage rapide pour le Kit de développement logiciel (SDK) Java Azure Cosmos DB v4](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java), qui vous permet d’accéder à l’artefact Maven et de passer en revue les requêtes Azure Cosmos DB de base.
> 3. Pour optimiser le Kit de développement logiciel (SDK) pour votre application, lisez les [conseils sur les performances](performance-tips-java-sdk-v4-sql.md) et [sur le dépannage](troubleshoot-java-sdk-v4-sql.md) relatifs au Kit de développement logiciel (SDK) Java Azure Cosmos DB v4.
>
> Les [ateliers et laboratoires Azure Cosmos DB](https://aka.ms/cosmosworkshop) sont une autre formidable ressource pour apprendre à utiliser le Kit de développement logiciel (SDK) Java Azure Cosmos DB v4.
>

| |  |
|---|---|
| **Téléchargement du Kit de développement logiciel (SDK)** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Documentation de l’API** | [Documentation de référence sur l’API Java](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1-beta.3/index.html) |
|**Contribution au Kit de développement logiciel (SDK)** | [Référentiel centralisé relatif au Kit de développement logiciel (SDK) Azure pour Java sur GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Prise en main** | [Démarrage rapide : Créer une application Java pour gérer les données de l’API SQL d’Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [Lien vers le référentiel GitHub avec le code de démarrage rapide](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Exemples de code de base** | [Azure Cosmos DB : Exemples Java pour l’API SQL](sql-api-java-sdk-samples.md) · [Lien vers le référentiel GitHub incluant un exemple de code](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Application console avec flux de modification**| [Flux de modification – Exemple de Kit de développement logiciel (SDK) Java v4](create-sql-api-java-changefeed.md) · [Lien vers le référentiel GitHub avec un exemple de code](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Exemple d’application web**| [Création d’une application web avec le Kit de développement logiciel (SDK) Java v4](sql-api-java-application.md) · [Lien vers le référentiel GitHub avec un exemple de code](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Conseils sur les performances**| [Conseils en performances pour le Kit de développement logiciel (SDK) Java v4](performance-tips-java-sdk-v4-sql.md)| 
| **Dépannage** | [Résoudre les problèmes du Kit de développement logiciel (SDK) Java v4](troubleshoot-java-sdk-v4-sql.md) |
| **Migration d’un ancien Kit de développement logiciel (SDK) vers la v4** | [Migrer vers le Kit de développement logiciel (SDK) Java V4](migrate-java-v4-sdk.md) |
| **Runtime minimal pris en charge**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Ateliers et laboratoires Azure Cosmos DB** |[Page d’accueil relative aux ateliers Cosmos DB](https://aka.ms/cosmosworkshop)


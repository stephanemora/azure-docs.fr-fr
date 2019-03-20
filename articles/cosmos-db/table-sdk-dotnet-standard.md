---
title: Ressources et kit SDK .NET Standard de l’API Table Azure Cosmos DB
description: Découvrez plus d’informations sur l’API Table d’Azure Cosmos DB et le SDK .NET Standard , notamment les dates de publication, les dates de mise hors service et les modifications effectuées entre chaque version.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/18/2018
ms.openlocfilehash: 38b283ed666b39b4e090bd95051a4454a9b47e62
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57975659"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>API .NET Standard Table Azure Cosmos DB : Téléchargement et notes de publication
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.JS](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Téléchargement du Kit de développement logiciel (SDK)**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Exemple**|[Exemple de COSMOS DB Table API .NET](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Démarrage rapide**|[Démarrage rapide](create-table-dotnet.md)|
|**Didacticiel**|[Didacticiel](tutorial-develop-table-dotnet.md)|
|**Infrastructure actuellement prise en charge**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Notes de publication

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Version en disponibilité générale

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-Preview
* Modifications apportées à la configuration de CloudTableClient. Il accepte désormais un un objet TableClientConfiguration pendant la construction. TableClientConfiguration fournit différentes propriétés pour configurer le comportement du client selon que le point de terminaison cible est l’API de Table Cosmos DB ou l’API de Table de stockage Azure.
* Prise en charge de TableQuery pour retourner des résultats dans l’ordre de tri sur une colonne personnalisée. Cette fonctionnalité est uniquement pris en charge sur les points de terminaison Cosmos DB Table.
* Prise en charge ajoutée pour exposer RequestCharges sur différents types de résultats. Cette fonctionnalité est uniquement pris en charge sur les points de terminaison Cosmos DB Table.

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1-preview
* Ajout de la prise en charge du jeton SAS et des opérations de TablePermissions, ServiceProperties et ServiceStats par rapport à des points de terminaison Table de stockage Azure. 
   > [!NOTE]
   > Certaines fonctionnalités des précédents kits SDK Table du stockage Azure ne sont pas encore prises en charge, comme le chiffrement côté client.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-preview
* Ajout de la prise en charge des principales opérations CRUD, de traitement par lots et d'interrogation par rapport à des points de terminaison Table de stockage Azure. 
   > [!NOTE]
   > Certaines fonctionnalités des précédents kits SDK Table du stockage Azure ne sont pas encore prises en charge, comme le chiffrement côté client.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* Le SDK .NET Standard Table d’Azure Cosmos DB est une bibliothèque .NET multiplateforme qui fournit un accès efficace au modèle de données Table sur Cosmos DB. Cette version initiale prend en charge l’ensemble complet des fonctionnalités CRUD + Requête sur les tables et les entités avec des API similaires à celles du [SDK Table de Cosmos DB pour .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Les points de terminaison Table de Stockage Azure ne sont pas encore pris en charge dans la version 0.9.1-preview.

## <a name="release-and-retirement-dates"></a>Dates de publication et de retrait
Microsoft envoie une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

| Version | Date de lancement | Date de suppression |
| --- | --- | --- |
| [1.0.0](#1.0.0) |13 mars 2019 |--- |
| [0.11.0-preview](#0.11.0-preview) |5 mars 2019 |--- |
| [0.10.1-preview](#0.10.1-preview) |22 janvier 2019 |--- |
| [0.10.0-preview](#0.10.0-preview) |18 décembre 2018 |--- |
| [0.9.1-preview](#0.9.1-preview) |18 octobre 2018 |--- |


## <a name="faq"></a>Forum Aux Questions

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur l’API Table Azure Cosmos DB, consultez [Présentation d’Azure Cosmos DB : API Table](table-introduction.md). 

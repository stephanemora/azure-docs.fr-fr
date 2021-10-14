---
title: Résoudre les problèmes liés au connecteur Azure Cosmos DB
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes liés aux connecteurs Azure Cosmos DB et Azure Cosmos DB (API SQL) dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: cd5f10c05ab1ca2524f384a4d085a913193e7293
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390586"
---
# <a name="troubleshoot-the-azure-cosmos-db-connector-in-azure-data-factory-and-azure-synapse"></a>Dépanner le connecteur Azure Cosmos DB dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés aux connecteurs Azure Cosmos DB et Azure Cosmos DB (API SQL) dans Azure Data Factory et Azure Synapse.

## <a name="error-message-request-size-is-too-large"></a>Message d’erreur : La taille de la demande est trop grande

- **Symptômes** : Lorsque vous copiez des données dans Azure Cosmos DB avec une taille du lot d’écriture par défaut, vous recevez l’erreur suivante : `Request size is too large.`

- **Cause** : Azure Cosmos DB limite la taille d’une seule requête à 2 Mo. La formule est la suivante : *taille de la requête = taille de document unique \* taille du lot d’écriture*. Si la taille de votre document est importante, le comportement par défaut entraîne une trop grande taille de la requête. Vous pouvez ajuster la taille du lot d’écriture.

- **Résolution** : Dans le récepteur de l’activité de copie, réduisez la valeur de la *taille du lot d’écriture* (la valeur par défaut est 10 000).

## <a name="error-message-unique-index-constraint-violation"></a>Message d’erreur : Violation de contrainte d’index unique

- **Symptômes** : Lorsque vous copiez des données dans Azure Cosmos DB, vous recevez l’erreur suivante :

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Cause** : Il existe deux causes possibles :

    - Cause 1 : Si vous utilisez **Insérer** comme comportement d’écriture, cette erreur signifie que vos données sources ont des lignes ou des objets avec le même ID.
    - Cause 2 : Si vous utilisez **Upsert** comme comportement d’écriture et que vous définissez une autre clé unique sur le conteneur, cette erreur signifie que vos données sources ont des lignes ou des objets avec des ID différents, mais une même valeur pour la clé unique définie.

- **Résolution** : 

    - Pour la cause 1, définissez **Upsert** comme comportement d’écriture.
    - Pour la cause 2, assurez-vous que chaque document a une valeur différente pour la clé unique définie.

## <a name="error-message-request-rate-is-large"></a>Message d’erreur : Le taux de demandes est élevé

- **Symptômes** : Lorsque vous copiez des données dans Azure Cosmos DB, vous recevez l’erreur suivante :

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Cause** : Le nombre d’unités de requête (RU) utilisées est supérieur au nombre de RU disponibles configuré dans Azure Cosmos DB. Pour savoir comment Azure Cosmos DB calcule les unités de requête, consultez [Unités de requête dans Azure Cosmos DB](../cosmos-db/request-units.md#request-unit-considerations).

- **Résolution** : Essayez l’une des deux solutions suivantes :

    - Définissez le nombre de *RU dans le conteneur* sur une valeur supérieure dans Azure Cosmos DB. Cette solution permet d’améliorer les performances de l’activité de copie, mais elle engendre des frais supplémentaires dans Azure Cosmos DB. 
    - Diminuez la valeur *writeBatchSize*, par exemple sur 1 000, et diminuez *parallelCopies*, par exemple sur 1. Cette solution réduit les performances d’exécution de la copie, mais elle n’engendre pas de frais supplémentaires dans Azure Cosmos DB.

## <a name="columns-missing-in-column-mapping"></a>Colonnes manquantes dans le mappage de colonnes

- **Symptômes** : Lorsque vous importez un schéma pour Azure Cosmos DB pour le mappage de colonnes, certaines colonnes sont manquantes. 

- **Cause** : Les pipelines Azure Data Factory et Synapse déduisent le schéma des 10 premiers documents Azure Cosmos DB. Si certaines colonnes ou propriétés du document ne contiennent pas de valeurs, le schéma n’est pas détecté et, par conséquent, n’est pas affiché.

- **Résolution** : Vous pouvez paramétrer la requête comme indiqué dans le code suivant pour forcer l’affichage des valeurs de colonnes dans le jeu de résultats avec des valeurs vides. Supposons que la colonne *impossible* est absente dans les 10 premiers documents. Vous pouvez également ajouter manuellement la colonne pour le mappage.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

## <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Message d’erreur : Le GuidRepresentation pour le lecteur est CSharpLegacy

- **Symptômes** : Lorsque vous copiez des données à partir de MongoAPI ou MongoDB Azure Cosmos DB avec le champ d’identificateur unique universel (UUID), vous recevez l’erreur suivante :

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Cause** : Il existe deux façons de représenter l’UUID en JSON binaire (BSON) : UuidStandard et UuidLegacy. Par défaut, UuidLegacy est utilisé pour lire les données. Vous recevez une erreur si vos données UUID dans MongoDB sont de type UuidStandard.

- **Résolution** : Dans la chaîne de connexion MongoDB, ajoutez l’option *uuidRepresentation=standard*. Pour plus d’informations, consultez [Chaîne de connexion MongoDB](connector-mongodb.md#linked-service-properties).

## <a name="error-code-cosmosdbsqlapioperationfailed"></a>Code d’erreur : CosmosDbSqlApiOperationFailed

- **Message** : `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Cause** : Problème lié à l’opération CosmosDbSqlApi.  Cela s’applique spécifiquement au connecteur Cosmos DB (API SQL).

- **Recommandation** :  Pour voir les détails de l’erreur, consultez [Document d’aide Azure Cosmos DB](../cosmos-db/troubleshoot-dot-net-sdk.md). Pour une aide supplémentaire, contactez l’équipe Azure Cosmos DB.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)

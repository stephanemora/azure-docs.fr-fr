---
title: Utilisation du multimaître Azure Cosmos DB avec des bases de données NoSQL open source
description: ''
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ea3a16cf6b5aa4e46ad401e59aacb4d936a7429a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963898"
---
# <a name="using-azure-cosmos-db-multi-master-with-open-source-nosql-databases"></a>Utilisation du multimaître Azure Cosmos DB avec des bases de données NoSQL open source

La prise en charge du multimaître Azure Cosmos DB est une implémentation native côté serveur qui est disponible pour toutes les offres NoSQL open source prises en charge par Cosmos DB. Elle est également accessible par tous les SDK pris en charge par Cosmos DB.

Azure Cosmos DB est le premier service au monde qui offre une prise en charge du multimaître pour ces bases de données NoSQL open source.

|Modèle  |Support  |
|---------|---------|
|MongoDB  | Actif/actif  |
|Graph  | Actif/actif |
|Cassandra  | Actif/actif   |

## <a name="use-mongodb-clients-with-multi-master"></a>Utiliser des clients MongoDB avec le multimaître

Le multimaître est activé pour les comptes d’API MongoDB de la même façon que pour les autres API Azure Cosmos DB. Une fois le multimaître activé pour les comptes d’API MongoDB, chaque instance d’une application cliente peut sélectionner sa région préférée pour les lectures et écritures. Du point de vue du pilote MongoDB, la région préférée apparaît au client en tant que jeu de réplicas principal. De cette manière, toutes les régions de votre base de données distribuée peuvent agir en tant que jeu de réplicas principal. Le multimaître Azure Cosmos DB vous permet de réduire de manière significative les latences d’écriture pour vos applications MongoDB distribuées à l’échelle mondiale. 

### <a name="set-the-primary-region"></a>Définir la région primaire

Chaque instance d’un client MongoDB peut sélectionner la région primaire en ajoutant `@<preferred_primary_region_name>` au champ « application name » (nom de l’application) accepté par le pilote client MongoDB. La plupart des pilotes l’acceptent dans la chaîne de connexion, par exemple :

`mongodb://fabrikam:KEY@fabrikam.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

Une fois la connexion établie, un des cas suivants peut se produire :

* Si le nom de la région préférée est disponible en tant que région d’écriture, Azure Cosmos DB la sélectionne comme région primaire.

* Si aucun nom de région préférée n’est fourni, Azure Cosmos DB sélectionne une région par défaut comme région primaire.

* Si un nom de région préférée est fourni, mais qu’il n’est pas disponible en tant que région d’écriture pour le compte de base de données, Azure Cosmos DB sélectionne comme région primaire la région d’écriture disponible la plus proche.

Certains pilotes tels que le pilote NodeJS émettent toujours en premier des écritures vers l’hôte spécifié dans la chaîne de connexion initiale. Dans le cas de ces pilotes, pour que les écritures soient dirigées vers la région préférée, outre indiquer le nom de l’application, vous devez modifier le nom DNS au sein de la chaîne de connexion afin qu’il inclue le nom de la région. Veillez à spécifier le nom de la région sans espaces. Par exemple : 

`mongodb://fabrikam:KEY@fabrikam-westus.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

### <a name="conflict-resolution-mode"></a>Mode de résolution des conflits

Le mode de résolution des conflits pour les comptes d’API MongoDB d’Azure Cosmos DB est toujours last-writer-wins (le dernier qui écrit gagne), sur la base de l’horodatage du serveur régional qui a accepté l’écriture.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert la prise en charge du multimaître pour les comptes d’API MongoDB d’Azure Cosmos DB. Vous pouvez maintenant consulter les ressources suivantes :

* [Guide pratique pour activer le multimaître pour les comptes Azure Cosmos DB](enable-multi-master.md)

* [Présentation de la résolution des conflits dans Azure Cosmos DB](multi-master-conflict-resolution.md)

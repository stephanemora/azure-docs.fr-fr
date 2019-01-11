---
title: Haute disponibilité dans Azure Cosmos DB
description: Cet article décrit comment Azure Cosmos DB offre une haute disponibilité
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 4fc17daf640e95ab028150cec029471a0c7bc565
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062993"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Haute disponibilité avec Azure Cosmos DB

Azure Cosmos DB réplique en toute transparence vos données vers toutes les régions Azure associées votre compte Cosmos. Cosmos DB emploie plusieurs couches de redondance pour vos données, comme le montre l’image suivante :

![Partitionnement physique](./media/high-availability/cosmosdb-data-redundancy.png)

- Les données dans les conteneurs Cosmos sont partitionnées horizontalement.

- Dans chaque région, chaque partition est protégée par un jeu de réplicas avec toutes les écritures répliquées et durablement validées par une majorité de réplicas. Les réplicas sont répartis entre 10 et 20 domaines d’erreur.

- Chaque partition est répliquée dans toutes les régions. Chaque région contient toutes les partitions de données d’un conteneur Cosmos et peut accepter des écritures et traiter les lectures.  

Si votre compte Cosmos est distribué entre N régions Azure, il y aura au moins N x quatre copies de toutes vos données. En plus de fournir un accès à faible latence aux données et la mise à l’échelle du débit d’écriture/de lecture dans les régions associées à votre compte Cosmos, le fait d’avoir plus de régions (N supérieur) améliore la disponibilité.  

## <a name="slas-for-availability"></a>Contrats SLA pour la disponibilité

En tant que base de données mondialement distribuée, Cosmos DB fournit des contrats SLA complets qui englobent le débit, la latence au 99e centile, la cohérence et une haute disponibilité. Le tableau suivant comporte les garanties de haute disponibilité fournie par Cosmos DB pour les comptes à région unique et multirégion. Pour bénéficier de la haute disponibilité, configurez vos comptes Cosmos afin d’avoir plusieurs régions d’écriture.

|Type d'opération  | Région unique |Multirégion (écritures dans une seule région)|Multirégion (écritures dans une plusieurs régions) |
|---------|---------|---------|-------|
|Écritures    | 99,99    |99,99   |99,999|
|Lectures     | 99,99    |99,999  |99,999|

> [!NOTE]
> Dans la pratique, la disponibilité d’écriture réelle pour l’obsolescence limitée, la session, les modèles de cohérence éventuelle et à préfixe cohérent, est beaucoup plus importante que les contrats SLA publiés. La disponibilité réelle de lecture pour tous les niveaux de cohérence est beaucoup plus importante que les contrats SLA publiés.

## <a name="high-availability-with-cosmos-db-in-the-face-of-regional-outages"></a>Haute disponibilité avec Cosmos DB en cas de pannes régionales

Les pannes régionales ne sont pas rares et Azure Cosmos DB permet de s’assurer que votre base de données est toujours disponible. Les détails suivants permettent de capturer le comportement de Cosmos DB pendant une panne, en fonction de la configuration de votre compte Cosmos :

- Avant la reconnaissance d’une opération d’écriture auprès du client alors que Cosmos DB est utilisé, les données sont validées durablement par un quorum de réplicas se trouvant la région qui accepte les opérations d’écriture.

- Les comptes multirégion configurés avec plusieurs régions d’écriture sont hautement disponibles pour les écritures et les lectures. Les basculements régionaux sont instantanés et ne nécessitent aucune modification à partir de l’application.

- Comptes multirégion avec une seule région d’écriture : pendant une panne de région d’écriture, ces comptes restent hautement disponibles pour les lectures. Toutefois, pour les écritures vous devez « activer le basculement automatique » sur votre compte Cosmos pour le basculement de la région impactée vers une autre région associée. Le basculement se produit dans l’ordre de priorité des régions que vous avez spécifié. Au final, lorsque la région impactée est de nouveau en ligne, les données non répliquées présentes dans la région d’écriture impactée pendant la panne sont accessibles via le flux de conflits. Les applications peuvent lire le flux de conflits, résoudre les conflits en fonction de la logique propre à l’application, et réécrire les données mises à jour dans le conteneur Cosmos comme il convient. Une fois que la région d’écriture précédemment impactée a récupéré, elle devient automatiquement disponible en tant que région de lecture. Vous pouvez appeler un basculement manuel et configurer la région impactée en tant que région d’écriture. Vous pouvez effectuer un basculement manuel à l’aide d’[Azure CLI ou du portail Azure](how-to-manage-database-account.md#manual-failover).  

- Comptes multirégion avec une seule région d’écriture : pendant une panne de région de lecture, ces comptes restent hautement disponibles pour les lectures et les écritures. La région impactée est automatiquement déconnectée de la région d’écriture et marquée comme étant hors connexion. Les SDK Cosmos DB redirigent les appels de lecture vers la prochaine région disponible dans la liste des régions préférées. Si aucune des régions dans la liste des régions préférées n'est disponible, les appels sont automatiquement acheminés vers la zone d’écriture en cours. Aucune modification n’est nécessaire dans le code de votre application pour gérer la panne de la région de lecture. Au final, lorsque la région impactée est de nouveau en ligne, la région de lecture précédemment concernée se synchronise automatiquement avec la région d’écriture active et est à nouveau disponible pour le traitement des requêtes de lecture. Les lectures suivantes sont redirigées vers la région récupérée sans modification nécessaire de votre code d’application. Pendant le basculement et la réintégration d’une région ayant précédemment échoué, les garanties de cohérence de lecture continuent à être respectées par Cosmos DB.

- Les comptes dans une seule région peuvent perdre leur disponibilité en raison d’une panne régionale. Il est recommandé de configurer au moins deux régions (de préférence, au moins deux régions d’écriture) avec votre compte Cosmos pour garantir une haute disponibilité en permanence.

- Même dans un cas extrêmement rare et malheureux où la région Azure est définitivement irrécupérable, il n’y a aucune perte potentielle de données si votre compte Cosmos multirégion est configuré avec le niveau de cohérence par défaut, c’est-à-dire fort. Dans le cas d’une région d’écriture irrécupérable de façon permanente, pour les comptes Cosmos multirégion configurés avec une cohérence d’obsolescence limitée, la fenêtre de perte de données potentielle est limitée à la fenêtre d’obsolescence ; pour les niveaux de session, de cohérence et de cohérence éventuelle, la fenêtre de perte de données potentielle est limitée à cinq secondes maximum.

## <a name="building-highly-available-applications"></a>Génération d’applications hautement disponibles

- Pour garantir une disponibilité élevée en écriture et en lecture, configurez votre compte Cosmos de façon à ce qu’il s’étendre sur au moins deux régions, avec plusieurs régions d’écriture. Cette configuration offre la disponibilité, la plus faible latence et l’évolutivité pour les lectures et les écritures soutenues par les contrats SLA. Pour en savoir plus, découvrez comment [configurer votre compte Cosmos avec plusieurs régions d’écriture](tutorial-global-distribution-sql-api.md).

- Pour les comptes Cosmos multirégion qui sont configurés avec une seule région d’écriture, [activez le « basculement automatique » à l’aide d’Azure CLI ou du portail Azure](how-to-manage-database-account.md#automatic-failover). Une fois le basculement automatique activé, Cosmos DB bascule automatiquement votre compte en cas de sinistre régional.  

- Même si votre compte Cosmos est hautement disponible, votre application peut ne pas être pas correctement conçue pour rester hautement disponible. Pour tester la haute disponibilité de bout en bout pour votre application, appelez régulièrement le [basculement manuel à l’aide d’Azure CLI ou du portail Azure](how-to-manage-database-account.md#manual-failover), dans le cadre de procédures de récupération d’urgence ou de test de votre application.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ensuite découvrir la mise à l’échelle du débit dans l’article suivant :

* [Compromis entre disponibilité et performance pour différents niveaux de cohérence](consistency-levels-tradeoffs.md)
* [Mise à l’échelle du débit provisionné au niveau global](scaling-throughput.md)
* [Article relatif au principe de la distribution mondiale d’Azure Cosmos DB](global-dist-under-the-hood.md)
* [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md)

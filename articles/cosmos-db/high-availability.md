---
title: Haute disponibilité dans Azure Cosmos DB
description: Cet article décrit comment Azure Cosmos DB offre une haute disponibilité
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/24/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 1e866560ceab342f08a98ba3db05980a2b0947d2
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407553"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Haute disponibilité avec Azure Cosmos DB

Azure Cosmos DB réplique en toute transparence vos données vers toutes les régions Azure associées votre compte Cosmos. Cosmos DB emploie plusieurs couches de redondance pour vos données, comme le montre l’image suivante :

![Partitionnement physique](./media/high-availability/cosmosdb-data-redundancy.png)

- Les données dans les conteneurs Cosmos sont [partitionnée horizontalement](partitioning-overview.md).

- Dans chaque région, chaque partition est protégée par un jeu de réplicas avec toutes les écritures répliquées et durablement validées par une majorité de réplicas. Les réplicas sont répartis entre 10 et 20 domaines d’erreur.

- Chaque partition est répliquée dans toutes les régions. Chaque région contient toutes les partitions de données d’un conteneur Cosmos et peut accepter des écritures et traiter les lectures.  

Si votre compte Cosmos est répartie sur *N* régions Azure, il y aura au moins *N* x 4 copies de toutes vos données. En plus de fournir l’accès des données à faible latence et de mise à l’échelle de débit de lecture/écriture dans les régions associées à votre compte Cosmos, avoir plus de régions (supérieur *N*) améliore la disponibilité.  

## <a name="slas-for-availability"></a>Contrats SLA pour la disponibilité

En tant que base de données mondialement distribuée, Cosmos DB fournit des contrats SLA complets qui englobent le débit, la latence au 99e centile, la cohérence et une haute disponibilité. Le tableau suivant comporte les garanties de haute disponibilité fournie par Cosmos DB pour les comptes à région unique et multirégion. Pour la haute disponibilité, configurez toujours vos comptes Cosmos pour avoir plusieurs régions d’écriture.

|Type d'opération  | Région unique |Multirégion (écritures dans une seule région)|Multirégion (écritures dans une plusieurs régions) |
|---------|---------|---------|-------|
|Écritures    | 99,99    |99,99   |99,999|
|Lectures     | 99,99    |99,999  |99,999|

> [!NOTE]
> Dans la pratique, la disponibilité de l’écriture réelle de l’obsolescence, session, préfixe cohérent et modèles de cohérence éventuelle est beaucoup plus importante que les contrats SLA publiés. La disponibilité réelle de lecture pour tous les niveaux de cohérence est beaucoup plus importante que les contrats SLA publiés.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Haute disponibilité avec Cosmos DB en cas de pannes de courant régionales

Pannes de courant régionales ne sont pas rares et Azure Cosmos DB permet de s’assurer de que votre base de données est toujours hautement disponible. Les détails suivants capturer le comportement de Cosmos DB en cas de panne, en fonction de la configuration de votre compte Cosmos :

- Avant la reconnaissance d’une opération d’écriture auprès du client alors que Cosmos DB est utilisé, les données sont validées durablement par un quorum de réplicas se trouvant la région qui accepte les opérations d’écriture.

- Les comptes multirégion configurés avec plusieurs régions d’écriture sont hautement disponibles pour les écritures et les lectures. Les basculements régionaux sont instantanés et ne nécessitent aucune modification à partir de l’application.

- **Comptes dans plusieurs régions avec une région unique-écriture (panne de région d’écriture) :** pendant une panne de région d’écriture, ces comptes restent hautement disponibles pour les lectures. Toutefois, pour les écritures doivent **« activer le basculement automatique »** sur votre Cosmos compte pour basculer la région impactée vers une autre région. Le basculement se produit dans l’ordre de priorité des régions que vous avez spécifié. Lorsque la région impactée soit de nouveau en ligne, les données non répliquées présentes dans la région d’écriture impactés pendant la panne sont rendues disponibles via le [conflits flux](how-to-manage-conflicts.md#read-from-conflict-feed). Applications peuvent lire les conflits de flux, résolvez les conflits en fonction de la logique spécifique à l’application et réécrire les données mises à jour vers le conteneur Cosmos comme il convient. Une fois que la région d’écriture précédemment impactée a récupéré, elle devient automatiquement disponible en tant que région de lecture. Vous pouvez appeler un basculement manuel et configurer la région impactée en tant que région d’écriture. À nouveau faire un basculement manuel, à l’aide de [Azure CLI ou le portail Azure](how-to-manage-database-account.md#manual-failover). Il n’y a **aucune perte de données ou de disponibilité** avant, pendant ou après le basculement manuel. Votre application continue d’être hautement disponible. 

- **Comptes dans plusieurs régions avec une région unique-écriture (interruption de la région de lecture) :** pendant une panne de région de lecture, ces comptes restent hautement disponibles pour les lectures et les écritures. La région impactée est automatiquement déconnectée de la région d’écriture et marquée comme étant hors connexion. Le [SDK Cosmos DB](sql-api-sdk-dotnet.md) redirection lira les appels à la prochaine région disponible dans la liste des régions préférées. Si aucune des régions dans la liste des régions préférées n'est disponible, les appels sont automatiquement acheminés vers la zone d’écriture en cours. Aucune modification n’est nécessaire dans le code de votre application pour gérer la panne de la région de lecture. Au final, lorsque la région impactée est de nouveau en ligne, la région de lecture précédemment concernée se synchronise automatiquement avec la région d’écriture active et est à nouveau disponible pour le traitement des requêtes de lecture. Les lectures suivantes sont redirigées vers la région récupérée sans modification nécessaire de votre code d’application. Pendant le basculement et réintégration d’une région ayant échouée précédemment, la cohérence des garanties continuent à être respectées par Cosmos DB en lecture.

- Les comptes dans une seule région peuvent perdre leur disponibilité en raison d’une panne régionale. Il est recommandé de toujours configurer **au moins deux régions** (de préférence, au moins deux écrire régions) avec votre compte Cosmos pour garantir une haute disponibilité en permanence.

- Même dans un événement rare et malheureux lorsque la région Azure est définitivement irrécupérable, il est sans perte de données si votre compte Cosmos avec plusieurs régions est configuré avec le niveau de cohérence par défaut de *fort*. En cas d’une région d’écriture définitivement irrécupérable, pour les comptes de Cosmos dans plusieurs régions configurés avec une cohérence obsolescence limitée, la fenêtre de perte de données potentielle est limitée à la fenêtre d’obsolescence (*K* ou *T*) ; pour la session, les niveaux de cohérence préfixe cohérent et éventuel, la fenêtre de perte de données potentielle est limitée à un maximum de cinq secondes.

## <a name="building-highly-available-applications"></a>Génération d’applications hautement disponibles

- Pour garantir une disponibilité élevée en écriture et en lecture, configurez votre compte Cosmos de façon à ce qu’il s’étendre sur au moins deux régions, avec plusieurs régions d’écriture. Cette configuration assure la disponibilité la plus élevée, la latence la plus faible et meilleure évolutivité pour les lectures et écritures soutenue par des contrats SLA. Pour en savoir plus, découvrez comment [configurer votre compte Cosmos avec plusieurs régions d’écriture](tutorial-global-distribution-sql-api.md).

- Pour les comptes Cosmos multirégion qui sont configurés avec une seule région d’écriture, [activez le « basculement automatique » à l’aide d’Azure CLI ou du portail Azure](how-to-manage-database-account.md#automatic-failover). Une fois le basculement automatique activé, Cosmos DB bascule automatiquement votre compte en cas de sinistre régional.  

- Même si votre compte Cosmos est hautement disponible, votre application peut ne pas être pas correctement conçue pour rester hautement disponible. Pour tester la haute disponibilité de bout en bout de votre application, appeler régulièrement la [basculement manuel à l’aide d’Azure CLI ou le portail Azure](how-to-manage-database-account.md#manual-failover), dans le cadre de votre application de test ou de récupération d’urgence (DR) exercices.

- Dans un environnement de base de données globalement distribuée, il existe une relation directe entre le niveau de cohérence et la durabilité des données en situation de panne à l'échelle d'une région. Au moment de l'élaboration de votre plan de continuité d'activité, vous devez identifier le délai maximal acceptable nécessaire à la récupération complète de l'application après un événement perturbateur. Ce délai s’appelle l’objectif de délai de récupération (RTO, recovery time objective). Vous devez également déterminer sur quelle période maximale l'application peut accepter de perdre les mises à jour de données récentes lors de la récupération après l'événement perturbateur. Il s’agit de l’objectif de point de récupération (RPO, recovery point objective). Pour obtenir le RPO et le RTO pour Azure Cosmos DB, consultez [Niveaux de cohérence et durabilité des données](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Étapes suivantes

Ensuite, vous pouvez lire les articles suivants :

* [Compromis entre disponibilité et performance pour différents niveaux de cohérence](consistency-levels-tradeoffs.md)
* [Mise à l’échelle du débit provisionné au niveau global](scaling-throughput.md)
* [Article relatif au principe de la distribution mondiale d’Azure Cosmos DB](global-dist-under-the-hood.md)
* [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md)
* [Comment configurer votre compte Cosmos avec plusieurs régions d’écriture](how-to-multi-master.md)

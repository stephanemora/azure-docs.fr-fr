---
title: Haute disponibilité dans Azure Cosmos DB
description: Cet article décrit comment Azure Cosmos DB offre une haute disponibilité
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 74eee3d164e7ee3831f292568da9cf0620e576e5
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399281"
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

## <a name="availability-zone-support"></a>Prise en charge de la Zone de disponibilité

Azure Cosmos DB est un service de base de données multimaîtres globalement distribué qui fournit une haute disponibilité et la résilience lors de pannes régionales. En outre à l’intersection de la résilience de région, vous pouvez maintenant activer **redondance de zone** lors de la sélection d’une région à associer à votre base de données Azure Cosmos. 

Avec prise en charge de la Zone de disponibilité, Azure Cosmos DB garantit sont placés les réplicas sur plusieurs fuseaux horaires dans une région donnée pour fournir une haute disponibilité et la résilience lors de pannes zonales. Il n’y a aucune modification à la latence et d’autres contrats SLA dans cette configuration. En cas d’un échec de zone unique, redondance de zone fournit la durabilité des données complète avec RPO = 0 et disponibilité avec RTO = 0. 

Redondance de zone est un *fonctionnalité supplémentaire* à la [réplication multimaître](how-to-multi-master.md) fonctionnalité. Redondance de zone autonome ne peut pas se reposer pour atteindre une résilience régionale. Par exemple, en cas de pannes de courant régionales ou accès à faible latence dans les régions, il est conseillé d’avoir plusieurs régions d’écriture en plus de redondance de zone. 

Lorsque vous configurez des écritures dans plusieurs régions pour votre compte Azure Cosmos, vous pouvez opter pour redondance de zone à aucun coût supplémentaire. Sinon, consultez la Remarque ci-dessous concernant la tarification pour la prise en charge de redondance de zone. Vous pouvez activer une redondance de zone sur une région existante de votre compte Azure Cosmos en supprimant la région, puis ajoutez-la avec la redondance de zone est activée.

Cette fonctionnalité est disponible dans les régions Azure suivantes :

* Sud du Royaume-Uni
* Asie du Sud-Est 

> [!NOTE] 
> L’activation des Zones de disponibilité pour une seule région compte Azure Cosmos entraîne des frais équivalents à l’ajout d’une région supplémentaire à votre compte. Pour plus d’informations sur la tarification, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/) et [coût dans plusieurs régions dans Azure Cosmos DB](optimize-cost-regions.md) articles. 

Le tableau suivant récapitule la fonctionnalité de haute disponibilité de différentes configurations de compte : 

|Indicateur de performance clé  |Région unique sans Zones de disponibilité (Non-AZ)  |Région unique avec des Zones de disponibilité (AZ)  |Plusieurs régions avec des Zones de disponibilité (AZ, 2 régions) – plus les paramètre recommandé |
|---------|---------|---------|---------|
|Écrire le contrat SLA de disponibilité     |   99,99 %      |    99,99 %     |  99, 999 %  |
|Lire le contrat SLA de disponibilité   |   99,99 %      |   99,99 %      |  99, 999 %       |
|Prix  |  Taux de facturation d’une seule région |  Taux de facturation de Zone de disponibilité de région unique |  Taux de facturation dans plusieurs régions       |
|Échecs de zone : perte de données   |  Perte de données  |   Aucune perte de données |   Aucune perte de données  |
|Échecs de zone – disponibilité |  Perte de disponibilité  | Aucune perte de disponibilité  |  Aucune perte de disponibilité  |
|Latence de lecture    |  Cross-région    |   Cross-région   |    Faible  |
|Latence d’écriture    |   Cross-région   |  Cross-région    |   Faible   |
|Panne régionale : perte de données    |   Perte de données      |  Perte de données       |   Perte de données <br/><br/> Lors de l’aide délimité cohérence obsolescence limitée avec multi maître et plusieurs régions, une perte de données est limitée à l’obsolescence limitée configuré sur votre compte. <br/><br/> Perte de données pendant une panne régionale peut être évitée en configurant une cohérence forte avec plusieurs régions. Cette option est fourni avec les compromis qui affectent les performances et disponibilité.      |
|Panne régionale – disponibilité  |  Perte de disponibilité       |  Perte de disponibilité       |  Aucune perte de disponibilité  |
|Débit    |  X RU/s de débit configuré      |  X RU/s de débit configuré       |  2 x débit approvisionné de RU/s <br/><br/> Ce mode de configuration nécessite deux fois la quantité de débit par rapport à une seule région avec des Zones de disponibilité, car il existe deux régions.   |

Vous pouvez activer la redondance de zone lorsque vous ajoutez une région à des comptes Azure Cosmos nouveau ou existants. Actuellement, vous pouvez uniquement activer redondance de zone à l’aide de PowerShell ou Azure Resource Manager de modèles. Pour activer la redondance de zone sur votre compte Azure Cosmos, vous devez définir le `isZoneRedundant` indicateur `true` pour un emplacement spécifique. Vous pouvez définir cet indicateur dans la propriété d’emplacements. Par exemple, l’extrait de code powershell suivant permet la redondance de zone pour la région « Asie du Sud-est » :

```powershell
$locations = @( 
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" }, 
    @{ "locationName"="East US"; "failoverPriority"=1 } 
) 
```

## <a name="building-highly-available-applications"></a>Génération d’applications hautement disponibles

- Pour garantir une disponibilité élevée en écriture et en lecture, configurez votre compte Cosmos de façon à ce qu’il s’étendre sur au moins deux régions, avec plusieurs régions d’écriture. Cette configuration assure la disponibilité la plus élevée, la latence la plus faible et meilleure évolutivité pour les lectures et écritures soutenue par des contrats SLA. Pour en savoir plus, découvrez comment [configurer votre compte Cosmos avec plusieurs régions d’écriture](tutorial-global-distribution-sql-api.md).

- Pour les comptes Cosmos multirégion qui sont configurés avec une seule région d’écriture, [activez le « basculement automatique » à l’aide d’Azure CLI ou du portail Azure](how-to-manage-database-account.md#automatic-failover). Une fois le basculement automatique activé, Cosmos DB bascule automatiquement votre compte en cas de sinistre régional.  

- Même si votre compte Cosmos est hautement disponible, votre application peut ne pas être pas correctement conçue pour rester hautement disponible. Pour tester la haute disponibilité de bout en bout de votre application, appeler régulièrement la [basculement manuel à l’aide d’Azure CLI ou le portail Azure](how-to-manage-database-account.md#manual-failover), dans le cadre de votre application de test ou de récupération d’urgence (DR) exercices.

- Dans un environnement de base de données distribuée globalement, il est une relation directe entre la durabilité de la cohérence des données de niveau et en présence d’une panne au niveau régional. Au moment de l'élaboration de votre plan de continuité d'activité, vous devez identifier le délai maximal acceptable nécessaire à la récupération complète de l'application après un événement perturbateur. Ce délai s’appelle l’objectif de délai de récupération (RTO, recovery time objective). Vous devez également déterminer sur quelle période maximale l'application peut accepter de perdre les mises à jour de données récentes lors de la récupération après l'événement perturbateur. Il s’agit de l’objectif de point de récupération (RPO, recovery point objective). Pour obtenir le RPO et le RTO pour Azure Cosmos DB, consultez [Niveaux de cohérence et durabilité des données](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ensuite lire les articles suivants :

* [Compromis entre disponibilité et performance pour différents niveaux de cohérence](consistency-levels-tradeoffs.md)
* [Mise à l’échelle du débit provisionné au niveau global](scaling-throughput.md)
* [Article relatif au principe de la distribution mondiale d’Azure Cosmos DB](global-dist-under-the-hood.md)
* [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md)
* [Comment configurer votre compte Cosmos avec plusieurs régions d’écriture](how-to-multi-master.md)

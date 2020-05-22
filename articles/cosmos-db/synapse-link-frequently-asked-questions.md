---
title: Foire aux questions sur Azure Synapse Link pour Azure Cosmos DB
description: Recevez des réponses aux questions fréquemment posées sur Synapse Link pour Azure Cosmos DB dans des domaines comme la facturation, le magasin analytique, la sécurité et la durée de vie dans le magasin analytique.
author: srchi
ms.author: srchi
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 36be05f72597ae9864eff812862589cafb1f5b0d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596468"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Foire aux questions sur Azure Synapse Link pour Azure Cosmos DB

Azure Synapse Link pour Azure Cosmos DB crée une intégration entre Azure Cosmos DB et Azure Synapse Analytics. Il permet aux clients d’exécuter des analyses quasiment en temps réel sur leurs données opérationnelles avec une isolation complète des performances de leurs charges de travail transactionnelles et sans pipeline ETL. Cet article répond aux questions fréquemment posées sur Azure Synapse Link pour Azure Cosmos DB.

## <a name="general-faq"></a>FAQ général

### <a name="is-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Synapse Link est-il pris en charge pour toutes les API Azure Cosmos DB ?
Dans la préversion publique, Synapse Link est pris en charge uniquement pour l’API Azure Cosmos DB SQL (Core). La prise en charge de l’API Cosmos DB pour Mongo DB et l’API Cassandra est actuellement en préversion limitée.  Pour demander l’accès à cette préversion limitée, contactez l’équipe [Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="is-synapse-link-supported-for-multi-region-azure-cosmos-accounts"></a>Synapse Link est-il pris en charge pour les comptes Azure Cosmos à plusieurs régions ?
Oui, pour les comptes Azure Cosmos à plusieurs régions, les données stockées dans le magasin analytique sont également distribuées à l’échelle mondiale. Indépendamment d’une région d’écriture unique (maître unique) ou de plusieurs régions d’écriture (également appelées « multimaîtres »), les requêtes analytiques effectuées à partir d’Azure Synapse Analytics peuvent être fournies à partir de la région la plus proche.

Lors de la planification de la configuration d’un compte Azure Cosmos à plusieurs régions avec prise en charge du magasin analytique, il est recommandé d’ajouter toutes les régions nécessaires au moment de la création du compte.

### <a name="can-i-choose-to-enable-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Puis-je choisir d’activer Synapse Link pour une seule région et non dans toutes les régions d’un compte à plusieurs régions ?
Dans la préversion, lorsque Synapse Link est activé pour un compte à plusieurs régions, le magasin analytique est créé dans toutes les régions. Les données sous-jacentes sont optimisées pour le débit et la cohérence transactionnelle dans le magasin transactionnel.

### <a name="can-i-disable-the-synapse-link-feature-for-my-azure-cosmos-account"></a>Puis-je désactiver la fonctionnalité Synapse Link de mon compte Azure Cosmos ?
Actuellement, une fois que la fonction Synapse Link est activée au niveau du compte, vous ne pouvez pas la désactiver.  Si vous souhaitez désactiver cette fonctionnalité, vous devez supprimer votre compte Azure Cosmos et en créer un nouveau.

Sachez qu’il n’y aura **aucune** incidence sur la facturation si Synapse Link est activé au niveau du compte, mais qu’aucun conteneur n’est activé pour le magasin analytique.

## <a name="azure-cosmos-db-analytical-store"></a>Magasin analytique Azure Cosmos DB

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Puis-je activer le magasin analytique sur les conteneurs existants ?
Actuellement, le magasin analytique ne peut être activé que pour les nouveaux conteneurs (à la fois dans les comptes nouveaux et existants).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-containers-after-enabling-it-during-container-creation"></a>Puis-je désactiver le magasin analytique sur mes conteneurs Azure Cosmos après l’avoir activé lors de la création du conteneur ?
Actuellement, le magasin analytique ne peut pas être désactivé sur un conteneur Azure Cosmos une fois qu’il est activé lors de la création du conteneur.

### <a name="is-analytical-store-supported-for-azure-cosmos-containers-with-autoscale-provisioned-throughput"></a>Le magasin analytique est-il pris en charge pour les conteneurs Azure Cosmos avec débit approvisionné par mise à l’échelle automatique ?
Oui, le magasin analytique peut être activé sur les conteneurs avec un débit approvisionné par mise à l’échelle automatique.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Existe-t-il un effet sur les RU approvisionnées pour le magasin transactionnel Azure Cosmos DB ?
Azure Cosmos DB garantit l’isolation des performances entre les charges de travail transactionnelles et analytiques. L’activation du magasin analytique sur un conteneur n’a pas d’impact sur les RU/s configurées sur le magasin transactionnel Azure Cosmos DB. Les transactions (lecture et écriture) et les coûts de stockage du magasin analytique sont facturées séparément. Pour plus d’informations, consultez la [tarification du magasin analytique Azure Cosmos DB](analytical-store-introduction.md#analytical-store-pricing).

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>Les opérations de suppression et de mise à jour sur le magasin transactionnel sont-elles reflétées dans le magasin analytique ? 
Oui, les suppressions et les mises à jour des données dans le magasin transactionnel sont reflétées dans le magasin analytique. Vous pouvez configurer la durée de vie (TTL) sur le conteneur pour inclure les données d’historique afin que le magasin analytique conserve toutes les versions des éléments qui répondent aux critères de durée de vie analytique. Pour plus d’informations, consultez [Vue d’ensemble de la durée de vie analytique](analytical-store-introduction.md#analytical-ttl).

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Puis-je me connecter à un magasin analytique à partir de moteurs d’analyse autres qu’Azure Synapse Analytics ?
Vous pouvez uniquement accéder aux requêtes et les exécuter sur le magasin analytique avec les runtimes fournis par Azure Synapse Analytics. Le magasin analytique peut être interrogé et analysé à l’aide des éléments suivants :

* Synapse Spark avec prise en charge complète de Scala, Python, SparkSQL et C#. Synapse Spark est essentiel à l’ingénierie des données et aux scénarios de science des données
* SQL Server serverless avec langage T-SQL et la prise en charge des outils décisionnels courants (par exemple, Power BI Premium, etc.)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>Puis-je me connecter au magasin analytique à partir de Synapse SQL provisionné ?
À ce stade, le magasin analytique n’est pas accessible à partir de Synapse SQL provisionné.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Puis-je réécrire les résultats de l’agrégation des requêtes de la requête Synapse dans le magasin analytique ?
Le magasin analytique est un magasin en lecture seule dans un conteneur Azure Cosmos. Par conséquent, vous ne pouvez pas réécrire directement les résultats de l’agrégation dans le magasin analytique, mais vous pouvez les écrire dans le magasin transactionnel Azure Cosmos DB d’un autre conteneur, qui peut ensuite être exploité comme une couche de service.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>La réplication de synchronisation automatique entre le magasin transactionnel et le magasin d’analyse est-elle synchrone ou asynchrone et quelles sont les délais de latence ? 
La réplication est asynchrone, et actuellement la latence normale est d’environ 2 minutes.

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Existe-t-il des scénarios dans lesquels les éléments du magasin transactionnel ne sont pas propagés automatiquement dans le magasin analytique ?
Si certains éléments de votre conteneur enfreignent le [schéma bien défini pour l’analyse](analytical-store-introduction.md#analytical-schema), ils ne seront pas inclus dans le magasin analytique. Si vous vous trouvez face à scénario bloqué par un schéma bien défini pour l’analyse, envoyez un message à l’[équipe Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com) pour obtenir de l’aide.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Puis-je partitionner les données du magasin analytique différemment de la banque transactionnelle ?
Les données du magasin analytique sont partitionnées en fonction du partitionnement horizontal des partitions dans le magasin transactionnel. Actuellement, vous ne pouvez pas choisir une autre stratégie de partitionnement pour le magasin analytique.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>Puis-je personnaliser ou substituer la façon dont les données transactionnelles sont transformées en colonnes dans le magasin analytique ?
Actuellement, vous ne pouvez pas transformer les éléments de données lorsqu’ils sont propagés automatiquement du magasin transactionnel vers le magasin analytique. Si vous vous trouvez face à des scénarios bloqués par cette limitation, envoyez un message à l’[équipe Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

## <a name="analytical-time-to-live-ttl"></a>Durée de vie (TTL) analytique

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>La durée de vie des données analytiques est-elle prise en charge au niveau conteneur et au niveau élément ?
À ce stade, la durée de vie des données analytiques ne peut être configurée qu’au niveau du conteneur et il n’y a pas de prise en charge pour définir la durée de vie analytique au niveau de l’élément.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Après avoir défini la durée de vie analytique au niveau conteneur sur un conteneur Azure Cosmos DB, puis-je utiliser ultérieurement une autre valeur ?
Oui, la durée de vie analytique peut être mise à jour et appliquer n’importe quelle valeur valide. Pour plus d’informations sur la durée de vie analytique, consultez l’article relatifs à la [durée de vie analytique](analytical-store-introduction.md#analytical-ttl).

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Puis-je mettre à jour ou supprimer un élément du magasin analytique après sa durée de vie dans le magasin transactionnel ?
Toutes les mises à jour et suppressions transactionnelles sont copiées dans le magasin analytique, mais si l’élément a été purgé du magasin transactionnel, il ne peut pas être mis à jour dans le magasin analytique. Pour plus d’informations, consultez l’article relatif à la [durée de vie analytique](analytical-store-introduction.md#analytical-ttl).

## <a name="billing"></a>Facturation

### <a name="what-is-the-billing-model-of-synapse-link-for-azure-cosmos-db"></a>Quel est le modèle de facturation de Synapse Link pour Azure Cosmos DB ?
Le [magasin analytique Azure Cosmos DB](analytical-store-introduction.md) est disponible gratuitement en préversion pour le magasin analytique jusqu’au 30 août 2020. Synapse Spark et Synapse SQL sont facturés par le biais de la [consommation du service Synapse](https://azure.microsoft.com/pricing/details/synapse-analytics/).

## <a name="security"></a>Sécurité

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Quelles sont les moyens d’authentification auprès du magasin analytique ?
L’authentification auprès du magasin analytique est identique à celle du magasin transactionnel. Pour une base de données spécifique, vous pouvez vous authentifier à l’aide de la clé principale ou de la clé en lecture seule. Vous pouvez utiliser le service lié dans Synapse Studio pour empêcher le collage des clés Azure Cosmos DB dans les notebooks Spark. L’accès à ce service lié est accessible à toute personne ayant accès à l’espace de travail.

## <a name="synapse-run-times"></a>Runtimes Synapse

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Quelles sont les runtimes Synapse actuellement pris en charge pour accéder au magasin analytique Azure Cosmos DB ?

|Runtime Synapse |Prise en charge actuelle |
|---------|---------|
|Pools Spark Synapse | Lecture, écriture (via un magasin transactionnel), table, vue temporaire |
|Synapse SQL serverless    | Lire, afficher (préversion limitée)  |
|Synapse SQL approvisionné   |  Non disponible |

### <a name="do-my-synapse-spark-tables-sync-with-my-synapse-sql-serverless-tables-the-same-way-they-do-with-azure-data-lake"></a>Mes tables Synapse Spark se synchronisent-elles avec mes tables Synapse SQL Serverless de la même manière qu’avec Azure Data Lake ?
Cette fonctionnalité n’est pas disponible pour l’instant.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Puis-je utiliser le flux structuré Spark à partir du magasin analytique ?
Actuellement, la prise en charge du flux structuré Spark pour Azure Cosmos DB est implémentée par la fonctionnalité de flux de modification du magasin transactionnel et n’est pas encore prise en charge à partir du magasin analytique.

## <a name="synapse-studio"></a>Synapse Studio

### <a name="in-the-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>Dans Synapse Studio, comment puis-je savoir si je suis connecté à un conteneur Azure Cosmos DB avec le magasin d’analytique activé ?
Les conteneurs Azure Cosmos DB activés avec le magasin analytique présentent l’icône suivante :

![Conteneur Azure Cosmos DB activé avec icône de magasin analytique](./media/synapse-link-frequently-asked-questions/analytical-store-icon.png)

Le conteneur de magasin transactionnel est représenté par l’icône suivante :

![Conteneur Azure Cosmos DB activé avec l’icône de magasin transactionnel](./media/synapse-link-frequently-asked-questions/transactional-store-icon.png)
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-synapse-studio"></a>Comment transmettre les informations d’identification Azure Cosmos DB depuis Synapse Studio ?
Actuellement, les informations d’identification Azure Cosmos DB sont transmises lors de la création du service lié par l’utilisateur qui a accès aux bases de données Azure Cosmos DB. L’accès à ce magasin est possible pour les autres utilisateurs qui ont accès à l’espace de travail.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [avantages de Synapse Link](synapse-link.md#synapse-link-benefits).

* En savoir plus sur l’intégration de [Synapse Link et Azure Cosmos DB](synapse-link.md#synapse-link-integration).

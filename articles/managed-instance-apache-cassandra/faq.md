---
title: Questions fréquentes sur Azure Managed Instance pour Apache Cassandra dans le portail Azure
description: Questions fréquentes sur Azure Managed Instance pour Apache Cassandra. Cet article répond à certaines questions relatives à l’utilisation des instances managées, les avantages, les limites de débit, les régions prises en charge et d’autres points de configuration.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1ba2b7d648c86912118b83a566bf2eb0800baee2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101747434"
---
# <a name="frequently-asked-questions-about-azure-managed-instance-for-apache-cassandra-preview"></a>Questions fréquentes sur Azure Managed Instance pour Apache Cassandra (préversion)

Cet article répond à certaines questions fréquentes sur Azure Managed Instance pour Apache Cassandra. Vous allez découvrir quand utiliser des instances managées, leurs avantages, les limites de débit, les régions prises en charge et leurs détails de configuration.

> [!IMPORTANT]
> Azure Managed Instance pour Apache Cassandra est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="general-faq"></a>FAQ général

### <a name="what-are-the-benefits-azure-managed-instance-for-apache-cassandra"></a>Quels sont les avantages d’Azure Managed Instance pour Apache Cassandra ?

La base de données Apache Cassandra est un bon choix si vous recherchez scalabilité et haute disponibilité, sans impact sur les performances. Elle constitue une plateforme idéale pour les données stratégiques en raison de sa scalabilité linéaire et de sa tolérance de panne éprouvée sur le matériel ou l’infrastructure cloud. Azure Managed Instance pour Apache Cassandra est un service qui permet de gérer les instances des centres de données Apache Cassandra open source qui sont déployés dans Azure.

Il peut être utilisé en tant que solution cloud complète ou dans le cadre d’un cluster hybride (cloud et local). Ce service est un bon choix si vous souhaitez une configuration et un contrôle affinés comme dans Apache Cassandra open source, sans avoir la surcharge de la maintenance.

### <a name="why-should-i-use-this-service-instead-of-azure-cosmos-db-cassandra-api"></a>Pourquoi utiliser ce service plutôt que l’API Cassandra Azure Cosmos DB ?

Azure Managed Instance pour Apache Cassandra est fourni par l’équipe d’Azure Cosmos DB. C’est un service managé autonome qui permet de déployer, gérer et redimensionner des clusters et des centres de données open source Apache Cassandra. De son côté, l’[API Cassandra Azure Cosmos DB](../cosmos-db/cassandra-introduction.md) est une plateforme PaaS (Platform as a Service) qui fournit une couche d’interopérabilité pour le protocole filaire Apache Cassandra. Si votre objectif est que la plateforme se comporte exactement de la même façon qu’un cluster Apache Cassandra, choisissez le service Managed Instance. Pour en savoir plus, consultez l’article [Différences entre Azure Managed Instance pour Apache Cassandra et l’API Cassandra dans Azure Cosmos DB](compare-cosmosdb-managed-instance.md).

### <a name="is-azure-managed-instance-for-apache-cassandra-dependent-on-azure-cosmos-db"></a>Azure Managed Instance pour Apache Cassandra a-t-il une dépendance envers Azure Cosmos DB ?

Non, il n’y a aucune dépendance architecturale entre Azure Managed Instance pour Apache Cassandra et Azure Cosmos DB. 

#### <a name="can-i-deploy-azure-managed-instance-for-apache-cassandra-in-any-region"></a>Puis-je déployer Azure Managed Instance pour Apache Cassandra dans toutes les régions ?

Managed Instance sera disponible dans un nombre limité de régions durant la préversion.

### <a name="what-are-the-storage-and-throughput-limits-of-azure-managed-instance-for-apache-cassandra"></a>Quelles sont les limites de stockage et de débit d’Azure Managed Instance pour Apache Cassandra ?

Ces limites dépendent des références SKU de machines virtuelles que vous choisissez.

### <a name="what-is-the-cost-of-azure-managed-instance-for-apache-cassandra"></a>Quel est le coût d’Azure Managed Instance pour Apache Cassandra ?

Les frais d’utilisation de Managed Instance sont basés sur le coût des machines virtuelles sous-jacentes, légèrement majoré. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/managed-instance-apache-cassandra/).

### <a name="can-i-use-yaml-file-settings-to-configure-behavior"></a>Puis-je utiliser des paramètres de fichier YAML pour configurer le comportement ?

Oui, vous pouvez incorporer des configurations de fichiers YAML dans le cadre d’un déploiement de modèle Resource Manager.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Comment puis-je surveiller l’infrastructure en même temps que le débit ?

Le serveur [Prometheus](https://prometheus.io/docs/introduction/overview/) est hébergé pour superviser l’activité dans l’ensemble de votre cluster, et il expose un point de terminaison. Il conserve 10 minutes ou 10 Go de données (selon le premier des deux seuils atteint). Pour bénéficier de cette supervision, vous devez configurer une [fédération](https://prometheus.io/docs/prometheus/latest/federation/) et un outil de tableau de bord approprié comme Grafana.

### <a name="does-azure-managed-instance-for-apache-cassandra-provide-full-backups"></a>Azure Managed Instance pour Apache Cassandra fournit-il des sauvegardes complètes ?

Oui, il fournit des sauvegardes complètes dans Stockage Azure et les restaure sur un nouveau cluster

### <a name="how-can-i-migrate-data-from-my-existing-apache-cassandra-cluster-to-azure-managed-instance-for-apache-cassandra"></a>Comment migrer les données de mon cluster Apache Cassandra existant vers Azure Managed Instance pour Apache Cassandra ?

Azure Managed Instance pour Apache Cassandra prend en charge toutes les fonctionnalités d’Apache Cassandra pour répliquer et envoyer en streaming les données entre les centres de données.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-azure-managed-instance-for-apache-cassandra"></a>Puis-je associer un cluster Apache Cassandra local à Azure Managed Instance pour Apache Cassandra ?

Oui, vous pouvez configurer un cluster hybride avec des centres de données injectés sur le réseau virtuel Azure et déployés par le service. Les centres de données Managed Instance peuvent communiquer avec des centres de données locaux situés dans le même anneau de cluster.

### <a name="where-can-i-give-feedback-on-azure-managed-instance-for-apache-cassandra-features"></a>Où envoyer mes commentaires sur les fonctionnalités d’Azure Managed Instance pour Apache Cassandra ?

Partagez vos commentaires en les postant dans les [commentaires User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db?category_id=398548) dans la catégorie « Managed Apache Cassandra ».

Pour résoudre un problème relatif à votre compte, enregistrez une [demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) sur le portail Azure.

## <a name="deployment-specific-faq"></a>FAQ spécifique au déploiement

### <a name="will-the-managed-instance-support-node-addition-cluster-status-and-node-status-commands"></a>Est-ce que Managed Instance prend en charge les commandes d’ajout de nœud, d’état de cluster et d’état de nœud ?

Toutes les commandes Nodetool *en lecture seule*, comme `status`, sont disponibles dans Azure CLI. Toutefois, certaines opérations telles que l’*ajout de nœud* ne sont pas disponibles parce que nous gérons l’intégrité des nœuds dans Managed Instance. En mode hybride, vous pouvez vous connecter au cluster avec *Nodetool*. L’utilisation de Nodetool n’est pas recommandée, car cela risque de déstabiliser le cluster. Cela peut aussi invalider les éventuels contrats SLA de support de production relatifs à l’intégrité des centres de données Managed Instance dans le cluster.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Que se passe-t-il avec les différents paramètres pour les métadonnées de table ?

Les paramètres pour les métadonnées de table, comme le filtre de Bloom, la mise en cache, les chances de réparation des lectures, la période de grâce du garbage collection et la période de vidage des memtables pour la compression, sont entièrement pris en charge comme avec tout environnement Apache Cassandra autohébergé.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les questions fréquentes relatives à d’autres API, consultez :

* [Créer un cluster Managed Instance à partir du portail Azure](create-cluster-portal.md)
* [Déployer un cluster Apache Spark managé avec Azure Databricks](deploy-cluster-databricks.md)
* [Gérer les ressources Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI](manage-resources-cli.md)
---
title: Opérations de gestion dans Azure Managed Instance pour Apache Cassandra
description: En savoir plus sur les opérations de gestion prises en charge par Azure Managed Instance pour Apache Cassandra. Elle explique également la séparation des responsabilités entre l’équipe de support Azure et les clients lors de la gestion de clusters hybrides et autonomes.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d5a3dfe84c18d85d83148dc30a6092b85245e7b5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098199"
---
# <a name="management-operations-in-azure-managed-instance-for-apache-cassandra"></a>Opérations de gestion dans Azure Managed Instance pour Apache Cassandra

Azure Managed Instance pour Apache Cassandra offre des opérations de déploiement et de mise à l’échelle automatisées pour les centres de données Apache Cassandra open source managés. Cet article définit les opérations et les fonctionnalités de gestion fournies par le service. Elle explique également la séparation des responsabilités entre l’équipe de support Azure et les clients lors de la gestion de clusters [hybrides](configure-hybrid-cluster.md) et autonomes.

## <a name="patching"></a>Application de correctifs

* Les correctifs au niveau du système d’exploitation sont effectués automatiquement à une cadence de 2 semaines environ.

* Les correctifs de niveau logiciel Apache Cassandra sont effectués lorsque des failles de sécurité sont identifiées. La cadence des correctifs peut varier.

* Lors de la mise à jour corrective, les machines sont redémarrées dans un rack à la fois. Vous ne devriez pas rencontrer de dégradation au niveau de l’application tant que le **paramètre quorum ALL n’est pas utilisé**, et que le facteur de réplication est **supérieur ou égal à 3**.

* La version dans Apache Cassandra est au format `X.Y.Z`. Vous pouvez contrôler le déploiement des versions majeures (X) et mineures (Y) manuellement via les outils de service. Tandis que les correctifs Cassandra (Z) qui peuvent être nécessaires pour cette combinaison de version majeure/mineure sont effectués automatiquement.  

## <a name="maintenance"></a>Maintenance

* La [réparation Nodetool](https://docs.datastax.com/cassandra-oss/3.0/cassandra/tools/toolsRepair.html) est exécutée automatiquement par le service à l’aide du [réparer](http://cassandra-reaper.io/). Cet outil est exécuté une fois par semaine. Vous souhaiterez peut-être la désactiver si vous utilisez votre propre service pour un [déploiement hybride](configure-hybrid-cluster.md).

* La surveillance de l’intégrité des nœuds se compose des éléments suivants :

  * Surveillance active de l’appartenance de chaque nœud à l’anneau Cassandra.
  * Surveillance active des machines virtuelles pour identifier et résoudre les problèmes liés à Azure, aux machines virtuelles, au stockage, à Linux et aux logiciels de support.

* Vous êtes responsable de tous les problèmes d’utilisation du processeur, du disque et du réseau, qui peuvent survenir en raison de votre utilisation de Cassandra. Voici quelques exemples de ces problèmes :

  * Opérations de requête inefficaces.
  * Débit qui dépasse la capacité.
  * Réception des données qui dépassent la capacité de stockage.
  * Paramètres de configuration de l’espace de réponse incorrects.
  * Stratégie de modèle de données ou de clé de partition médiocre.

## <a name="security"></a>Sécurité

Azure Managed Instance pour Apache Cassandra fournit de nombreux contrôles et fonctionnalités de sécurité explicites intégrés :

* Images de machines virtuelles Linux renforcées avec une chaîne d’approvisionnement contrôlée.
* Surveillance des vulnérabilités courantes & exposition (CVE) au niveau du système d’exploitation.
* La rotation des certificats pour les logiciels Apache Cassandra et Prometheus hébergés sur les machines virtuelles gérées.
* Analyse active des vulnérabilités.
* Analyse antivirus active.
* Pratiques de codage sécurisées.

## <a name="hybrid-support"></a>Support hybride

Lorsqu’un cluster [hybride](configure-hybrid-cluster.md) est configuré, les opérations de surblocage automatisées qui s’exécutent dans le service bénéficient de l’ensemble du cluster. Cela comprend les centres de données qui ne sont pas approvisionnés par le service. En dehors de cela, il vous incombe de gérer votre centre de données local ou hébergé en externe.

## <a name="next-steps"></a>Étapes suivantes

Démarrez avec l’un de nos guides de démarrage rapide :
* [Créer un cluster Managed Instance à partir du portail Azure](create-cluster-portal.md)
* [Déployer un cluster Apache Spark managé avec Azure Databricks](deploy-cluster-databricks.md)
* [Gérer les ressources Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI](manage-resources-cli.md)

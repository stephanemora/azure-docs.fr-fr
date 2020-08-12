---
title: Comment augmenter la résilience
titleSuffix: Azure Machine Learning
description: Découvrez comment rendre vos ressources Azure Machine Learning plus résistantes aux pannes à l’aide d’une configuration à haute disponibilité.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 07/16/2020
ms.openlocfilehash: 4f2bf239e1157f5c927c4449857ad5f7793ccb49
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87094313"
---
# <a name="increase-the-resiliency-of-azure-machine-learning"></a>Augmentez la résilience d’Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Découvrez comment rendre vos ressources Azure Machine Learning plus résilientes à l’aide de configurations à haute disponibilité. Les services Azure dont dépend Azure Machine Learning peuvent être configurés pour la haute disponibilité. Cet article fournit des informations sur les services qui peuvent être configurés pour la haute disponibilité, ainsi que des liens vers des informations sur la configuration de ces ressources.

> [!NOTE]
> Azure Machine Learning lui-même n’offre pas d’option de récupération d’urgence.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Présentation des services Azure pour Azure Machine Learning

Azure Machine Learning dépend de plusieurs services Azure et possède plusieurs couches. Certaines d’entre elles sont approvisionnées dans votre abonnement (client). Vous êtes responsable de la configuration de la haute disponibilité de ces services. Certains sont créés dans un abonnement Microsoft et sont gérés par Microsoft.

* **Infrastructure d’Azure Machine Learning** : Environnement géré par Microsoft pour un espace de travail Azure Machine Learning.

* **Ressources associées** : Ressources approvisionnées dans votre abonnement lors de la création d’un espace de travail Azure Machine Learning. Elles incluent le stockage Azure, Azure Key Vault, Azure Container Registry (ACR) et Application Insights. Vous êtes responsable des paramètres de haute disponibilité pour ces ressources.
  * Le stockage par défaut contient des données telles que le modèle, les données du journal d’apprentissage et le jeu de données.
  * Key Vault possède des informations d’identification pour le stockage, ACR et les magasins de données.
  * ACR dispose d’une image Docker pour l’apprentissage et l’environnement d’inférence.
  * Application Insights est destiné à la surveillance d’Azure Machine Learning.

* **Ressources de calcul** : Ressources que vous créez après le déploiement de l’espace de travail. Par exemple, vous pouvez créer une instance de calcul ou un cluster de calcul pour former un modèle Machine Learning.
  * Instance de calcul et cluster de calcul : Environnement de développement de modèle géré par Microsoft.
  * Autres ressources : Il s’agit des ressources de calcul qui peuvent être attachées à Azure Machine Learning, comme Azure Kubernetes Service (AKS), Azure Databricks, Azure Container Instances (ACI) et HDInsight. Vous êtes responsable du paramètre de haute disponibilité.

* **Magasins de données supplémentaires** : Azure Machine Learning peut monter des magasins de données supplémentaires, comme le stockage Azure, Azure Data Lake Storage et Azure SQL Database pour les données d’apprentissage.  Vous les trouverez dans votre abonnement et êtes responsable du paramètre de haute disponibilité.

Le tableau suivant répertorie les services gérés par Microsoft, ceux qui sont gérés par vous et ceux qui sont hautement disponibles par défaut :

| Service | Géré par | Haute disponibilité par défaut |
| ----- | ----- | ----- |
| **Infrastructure Azure Machine Learning** | Microsoft | |
| **Ressources associées** |
| Stockage Azure | Vous | |
| Azure Key Vault | Vous | ✓ |
| Azure Container Registry | Vous | |
| Application Insights | Vous | N/D |
| **Ressources de calcul** |
| Instance de calcul | Microsoft |  |
| Cluster de calcul | Microsoft |  |
| Autres ressources, comme le service Azure Kubernetes, <br>Azure Databricks, Azure Container Instance, Azure HDInsight | Vous |  |
| **Des magasins de données supplémentaires**, comme le stockage Azure, Azure SQL Database,<br> Azure Database pour PostgreSQL, Azure Database pour MySQL, <br>Système de fichiers Azure Databricks | Vous | |

Utilisez les informations contenues dans le reste de ce document pour connaître les actions nécessaires pour rendre chacun de ces services hautement disponible.

## <a name="associated-resources"></a>Ressources associées

> [!IMPORTANT]
> Azure Machine Learning ne prend pas en charge le basculement de compte de stockage par défaut à l’aide du stockage géo-redondant (GRS) ou du stockage géo-redondant interzone (GZRS), ou stockage géo-redondant avec accès en lecture (RA-GRS) ou du stockage géo-redondant interzone avec accès en lecture (RA-GZRS).

Vérifiez le paramètre de haute disponibilité de chaque ressource avec les informations ci-dessous.

* **Stockage Azure** : Pour configurer le paramètre de haute disponibilité, consultez [Redondance du stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).
* **Azure Key Vault** : Il fournit un service de haute disponibilité par défaut et aucune action de l’utilisateur n’est requise.  Consultez [Disponibilité et redondance d’Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance).
* **Azure Container Registry** : Choisissez la référence SKU Premium pour la géo-réplication. Consultez [Géoréplication dans Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
* **Application Insights** : Il n’assure pas le paramètre de haute disponibilité. Vous pouvez ajuster la période de rétention des données et les détails dans [Collecte, rétention et stockage des données dans Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Ressources de calcul

Vérifiez le paramètre de haute disponibilité de chaque ressource avec la documentation ci-dessous.

* **Azure Kubernetes Service** : Consultez [Bonnes pratiques pour la continuité d’activité et la reprise d’activité dans AKS (Azure Kubernetes Services)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) et [Créer un cluster Azure Kubernetes Service (AKS) qui utilise des zones de disponibilité](https://docs.microsoft.com/azure/aks/availability-zones). Si le cluster AKS a été créé par Azure Machine Learning (à l’aide de Studio, du kit de développement logiciel (SDK) ou de l’interface CLI de ML), la haute disponibilité inter-régions n’est pas prise en charge.
* **Azure Databricks** : Consultez [Récupération d’urgence régionale pour les clusters Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).
* **Azure Container Instance** : L’orchestrateur ACI est responsable du basculement. Consultez [Azure Container Instances et les orchestrateurs de conteneurs](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship).
* **Azure HDInsight** : Consultez [Services de haute disponibilité pris en charge par Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components).

## <a name="additional-data-stores"></a>Magasins de données supplémentaires

Vérifiez le paramètre de haute disponibilité de chaque ressource avec la documentation ci-dessous.

* **Conteneur d’objets BLOB Azure/partage de fichiers Azure/Azure Data Lake Gen2** : Identique au stockage par défaut.
* **Azure Data Lake Gen1** : Consultez [Conseils sur la reprise d’activité pour les données dans Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).
* **Azure SQL Database** : Consultez [Haute disponibilité et Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability).
* **Azure Database pour PostgreSQL** : Consultez [Concepts de haute disponibilité dans Azure Database pour PostgreSQL (serveur unique)](https://docs.microsoft.com/azure/postgresql/concepts-high-availability).
* **Azure Database pour MySQL** : Consultez [Comprendre la continuité d’activité dans Azure Database pour MySQL](https://docs.microsoft.com/azure/mysql/concepts-business-continuity).
* **Système de fichiers Databricks** : Consultez [Récupération d’urgence régionale pour les clusters Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Si vous fournissez votre propre clé (clé gérée par le client) pour déployer un espace de travail Azure Machine Learning, Cosmos DB est également approvisionné dans votre abonnement. Dans ce cas, vous êtes responsable de sa haute disponibilité. Consultez [Haute disponibilité avec Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability)

## <a name="next-steps"></a>Étapes suivantes

Pour déployer Azure Machine Learning avec les ressources associées avec vos paramètres de haute disponibilité, utilisez un [Modèle Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced).
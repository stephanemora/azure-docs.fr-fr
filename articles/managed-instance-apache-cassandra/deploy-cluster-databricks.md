---
title: 'Démarrage rapide : Déployer un cluster Apache Spark managé avec Azure Databricks'
description: Ce guide de démarrage rapide montre comment déployer un cluster Apache Spark managé avec Azure Databricks en utilisant le portail Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 06/02/2021
ms.openlocfilehash: 1e66e8a3358bdcbca3d5dea247e1e6af2000393f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735056"
---
# <a name="quickstart-deploy-a-managed-apache-spark-cluster-preview-with-azure-databricks"></a>Démarrage rapide : Déployer un cluster Apache Spark managé (préversion) avec Azure Databricks

Azure Managed Instance pour Apache Cassandra offre des opérations de déploiement et de mise à l’échelle automatisées pour les centres de données Apache Cassandra open source managés, ce qui permet d’accélérer les scénarios hybrides et de réduire la maintenance en cours.

> [!IMPORTANT]
> Azure Managed Instance pour Apache Cassandra est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce guide de démarrage rapide explique comment utiliser le portail Azure pour créer un cluster Apache Spark complètement managé dans le réseau virtuel Azure de votre cluster Azure Managed Instance pour Apache Cassandra. Vous allez créer le cluster Spark dans Azure Databricks. Plus tard, vous pourrez créer ou attacher des notebooks au cluster, lire les données de différentes sources de données et analyser des insights.

Pour en savoir plus et obtenir des instructions détaillées, consultez [Déployer Azure Databricks dans votre réseau virtuel Azure (injection dans le réseau virtuel)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

## <a name="create-an-azure-databricks-cluster"></a>Créer un cluster Azure Databricks

Effectuez les étapes suivantes pour créer un cluster Azure Databricks dans un réseau virtuel avec Azure Managed Instance pour Apache Cassandra :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Dans le volet de navigation de gauche, sous **Groupes de ressources**, accédez au groupe de ressources qui contient le réseau virtuel sur lequel votre instance managée est déployée.

1. Ouvrez la ressource **Réseau virtuel** et prenez note de la valeur dans **Espace d’adressage** :

    :::image type="content" source="./media/deploy-cluster-databricks/virtual-network-address-space.png" alt-text="Obtenir l’espace d'adressage de votre réseau virtuel." border="true":::

1. Dans le groupe de ressources, sélectionnez **Ajouter** et faites une recherche sur **Azure Databricks** dans le champ de recherche :

    :::image type="content" source="./media/deploy-cluster-databricks/databricks.png" alt-text="Effectuer une recherche sur Azure Databricks." border="true":::

1. Sélectionnez **Créer** pour créer un compte Azure Databricks :

    :::image type="content" source="./media/deploy-cluster-databricks/databricks-create.png" alt-text="Créer un compte Azure Databricks." border="true":::

1. Renseignez les valeurs suivantes :

   * **Nom de l’espace de travail** : entrez un nom pour votre espace de travail Databricks.
   * **Région** : veillez à sélectionner la même région que votre réseau virtuel.
   * **Niveau tarifaire** : choisissez le niveau Standard, Premium ou Essai. Pour plus d’informations sur ces niveaux, consultez la [page de tarification Databricks](https://azure.microsoft.com/pricing/details/databricks/).

    :::image type="content" source="./media/deploy-cluster-databricks/select-name.png" alt-text="Renseigner le nom de l’espace de travail, la région et le niveau tarifaire pour le compte Databricks." border="true":::

1. Ensuite, sélectionnez l’onglet **Réseau** et renseignez les informations suivantes :

   * **Déployer l’espace de travail Azure Databricks dans votre réseau virtuel (VNet)**  : sélectionnez **Oui**.
   * **Réseau virtuel** : dans la liste déroulante, choisissez le réseau virtuel sur lequel se trouve votre instance managée.
   * **Nom du sous-réseau public** : entrez un nom pour le sous-réseau public.
   * **Plage CIDR du sous-réseau public** : entrez une plage d’adresses IP pour le sous-réseau public.
   * **Nom du sous-réseau privé** : entrez un nom pour le sous-réseau privé.
   * **Plage CIDR du sous-réseau privé** : entrez une plage d’adresses IP pour le sous-réseau privé.

   Pour éviter les collisions d’étendues, sélectionnez des plages plus grandes. Si nécessaire, utilisez un [calculateur de sous-réseau visuel](https://www.fryguy.net/wp-content/tools/subnets.html) pour diviser les plages :

   :::image type="content" source="./media/deploy-cluster-databricks/subnet-calculator.png" alt-text="Utiliser le calculateur de sous-réseau du réseau virtuel." border="true":::

   La capture d’écran suivante montre des exemples de détails dans le volet Réseau :

   :::image type="content" source="./media/deploy-cluster-databricks/subnets.png" alt-text="Spécifier les noms des sous-réseaux public et privé." border="true":::

1. Sélectionnez **Vérifier et créer**, puis sélectionnez **Créer** pour déployer l’espace de travail.

1. **Lancer l’espace de travail** après sa création.

1. Vous êtes redirigé vers le portail Azure Databricks. Dans le portail, sélectionnez **Nouveau cluster**.

1. Dans le volet **Nouveau cluster**, acceptez les valeurs par défaut pour tous les champs autres que les champs suivants :

   * **Nom du cluster** : entrez un nom pour le cluster.
   * **Version du runtime Databricks** : nous recommandons de sélectionner la version du runtime Databricks 7.5 ou supérieure pour la prise en charge de Spark 3.x. 

    :::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-runtime.png" alt-text="Sélectionner la version de Databricks Runtime et le cluster Spark." border="true":::

1. Développez **Options avancées** et ajoutez la configuration suivante. N’oubliez pas d’indiquer les adresses IP des nœuds et les informations d’identification :

    ```java
    spark.cassandra.connection.host <node1 IP>,<node 2 IP>, <node IP>
    spark.cassandra.auth.password cassandra
    spark.cassandra.connection.port 9042
    spark.cassandra.auth.username cassandra
    spark.cassandra.connection.ssl.enabled true
    ```

1. Ajoutez la bibliothèque du connecteur Apache Spark Cassandra à votre cluster pour vous connecter aux points de terminaison Cassandra natifs et Azure Cosmos DB. Dans votre cluster, sélectionnez **Bibliothèques** > **Installer nouveau** > **Maven**, puis ajoutez `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` dans les coordonnées Maven.

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-search-packages.png" alt-text="Capture d’écran montrant la recherche de packages Maven dans Databricks.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser ce cluster Managed Instance, supprimez-le en effectuant les étapes suivantes :

1. Dans le menu de gauche du portail Azure, sélectionnez **Groupes de ressources**.
1. Dans la liste, sélectionnez le groupe de ressources créé pour ce guide de démarrage rapide.
1. Dans le volet **Vue d’ensemble** du groupe de ressources, sélectionnez **Supprimer un groupe de ressources**.
3. Dans la fenêtre suivante, entrez le nom du groupe de ressources à supprimer, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez vu comment créer un cluster Apache Spark complètement managé dans le réseau virtuel de votre cluster Azure Managed Instance pour Apache Cassandra. Vous pouvez ensuite apprendre à gérer les ressources du cluster et du centre de ressources : 

> [!div class="nextstepaction"]
> [Gérer les ressources Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI](manage-resources-cli.md)


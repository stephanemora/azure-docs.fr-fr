---
title: Base de données Apache Ambari personnalisée sur Azure HDInsight
description: Découvrez comment créer des clusters HDInsight avec votre propre base de données Apache Ambari personnalisée.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: 4cb96e1299010636e0bce3cb99fbba9862822564
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022263"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Configurer des clusters HDInsight avec une base de données Ambari personnalisée

Apache Ambari simplifie la gestion et la surveillance d’un cluster Apache Hadoop. Ambari fournit une interface utilisateur web et une API REST faciles à utiliser. Ambari est inclus dans les clusters HDInsight, et sert à surveiller le cluster et à apporter des modifications de configuration.

Dans le cadre de la création normale de clusters, comme décrit dans d’autres articles tels que [Configurer des clusters dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md), Ambari est déployé dans une [base de données Azure SQL S0](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) qui est gérée par HDInsight et n’est pas accessible aux utilisateurs.

La fonctionnalité Ambari DB personnalisée vous permet de déployer un nouveau cluster et de configurer Ambari dans une base de données externe que vous gérez. Le déploiement est effectué avec un modèle Azure Resource Manager. Cette fonctionnalité permet de bénéficier des avantages suivants :

- Personnalisation : vous choisissez la taille et la capacité de traitement de la base de données. Si vous avez des clusters de grande taille qui traitent des charges de travail intensives, une base de données Ambari avec des spécifications inférieures peut devenir un goulot d’étranglement pour les opérations de gestion.
- Flexibilité : vous pouvez mettre à l’échelle la base de données si nécessaire pour répondre à vos besoins.
- Contrôle : vous pouvez gérer les sauvegardes et la sécurité de votre base de données en fonction des besoins de votre organisation.

Le reste de cet article aborde les points suivants :

- Configuration requise pour utiliser la fonctionnalité Ambari DB personnalisée
- Étapes nécessaires à la configuration de clusters HDInsight à l’aide de votre propre base de données externe pour Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Configuration requise pour la base de données Ambari personnalisée

Vous pouvez déployer une base de données Ambari personnalisée avec tous les types et versions de cluster. Plusieurs clusters ne peuvent pas utiliser la même base de données Ambari.

La base de données Ambari personnalisée présente les autres exigences suivantes :

- Vous devez disposer d’un serveur et d’une base de données Azure SQL DB existants.
- La base de données que vous fournissez pour l’installation d’Ambari doit être vide. Le schéma dbo par défaut ne doit pas contenir de tables.
- L’utilisateur utilisé pour se connecter à la base de données doit disposer d’autorisations SELECT, CREATE TABLE et INSERT sur la base de données.
- Activez l’option pour [autoriser l’accès aux services Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps) sur le serveur qui hébergera Ambari.
- Les adresses IP de gestion du service HDInsight doivent être autorisées dans la règle de pare-feu. Pour obtenir la liste des adresses IP qui doivent être ajoutées à la règle de pare-feu au niveau du serveur, consultez [Adresses IP de gestion HDInsight](hdinsight-management-ip-addresses.md).

Lorsque vous hébergez votre base de données Apache Ambari dans une base de données externe, gardez à l’esprit les points suivants :

- Vous êtes responsable des coûts supplémentaires de la base de données Azure SQL DB qui contient Ambari.
- Sauvegardez régulièrement votre base de données Ambari personnalisée. Azure SQL Database génère automatiquement des sauvegardes, mais la période de conservation des sauvegardes varie. Pour plus d’informations, consultez [En savoir plus sur les sauvegardes automatiques SQL Database](../azure-sql/database/automated-backups-overview.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Déployer des clusters avec une base de données Ambari personnalisée

Pour créer un cluster HDInsight qui utilise votre propre base de données Ambari externe, utilisez le [modèle de démarrage rapide Ambari DB personnalisé](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db).

Modifiez les paramètres dans le fichier `azuredeploy.parameters.json` pour spécifier des informations sur votre nouveau cluster et la base de données qui contiendra Ambari.

Vous pouvez commencer le déploiement à l’aide de l’interface de ligne de commande Azure. Remplacez `<RESOURCEGROUPNAME>` par le groupe de ressources dans lequel vous souhaitez déployer votre cluster.

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser des magasins de métadonnées externes dans Azure HDInsight](hdinsight-use-external-metadata-stores.md)
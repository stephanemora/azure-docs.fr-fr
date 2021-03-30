---
title: Activer la création automatique de rubriques dans Apache Kafka - Azure HDInsight
description: Découvrez comment configurer Apache Kafka sur HDInsight pour créer des rubriques automatiquement. Vous pouvez configurer Kafka en définissant `auto.create.topics.enable` sur la valeur True par le biais d’Ambari. Vous pouvez également le faire pendant la création du cluster par le biais de PowerShell ou de modèles Resource Manager.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: 3766d41959383d802e50aafbf59b9841d1c8d74e
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870685"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Comment configurer Apache Kafka sur HDInsight pour créer des rubriques automatiquement

Par défaut, Apache Kafka sur HDInsight n’autorise pas la création automatique de rubriques. Vous pouvez activer la création automatique de rubriques pour les clusters existants à l’aide d’Apache Ambari. Vous pouvez également activer la création automatique de rubriques quand vous créez un cluster Kafka à l’aide d’un modèle Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Interface utilisateur web d’Apache Ambari

Pour activer la création automatique de rubriques sur un cluster existant par le biais de l’interface utilisateur web d’Ambari, effectuez les étapes suivantes :

1. Sur le [Portail Azure](https://portal.azure.com), sélectionnez votre cluster Kafka.

1. Dans **Tableaux de bord du cluster**, sélectionnez **Accueil Ambari**.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png" alt-text="Image du portail avec le tableau de bord du cluster sélectionné" border="true":::

    Lorsque vous y êtes invité, authentifiez-vous à l’aide des informations d’identification de connexion (admin) pour le cluster. Au lieu de cela, vous pouvez vous connecter directement à Amabri sur `https://CLUSTERNAME.azurehdinsight.net/`, où `CLUSTERNAME` est le nom de votre cluster Kafka.

1. Sélectionnez le service Kafka dans la liste à gauche de la page.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-service-list.png" alt-text="Apache Ambari - Onglet avec la liste des services" border="true":::

1. Sélectionnez Configs au milieu de la page.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-service-config.png" alt-text="Apache Ambari - Onglet Configs du service" border="true":::

1. Dans le champ Filter (Filtrer), entrez la valeur `auto.create`.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png" alt-text="Apache Ambari - Champ Filtre de recherche" border="true":::

    Ce paramètre filtre la liste des propriétés et affiche le paramètre `auto.create.topics.enable`.

1. Remplacez la valeur de `auto.create.topics.enable` par `true`, puis sélectionnez **Save** (Enregistrer). Ajoutez une note, puis sélectionnez à nouveau **Save** (Enregistrer).

    :::image type="content" source="./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png" alt-text="Image de l’entrée auto.create.topics.enable" border="true":::

1. Sélectionnez le service Kafka, choisissez __Restart__ (Redémarrer), puis __Restart all affected__ (Redémarrer tous les éléments affectés). Lorsque vous y êtes invité, sélectionnez __Confirm Restart All__ (Confirmer le redémarrage).

    :::image type="content" source="./media/apache-kafka-auto-create-topics/restart-all-affected.png" alt-text="`Apache Ambari – Redémarrer tous les éléments affectés`" border="true":::

> [!NOTE]  
> Vous pouvez également définir les valeurs Ambari par le biais de l’API REST d’Ambari. Cette méthode est généralement plus difficile, car vous devez effectuer plusieurs appels REST pour récupérer la configuration actuelle, la modifier, etc. Pour plus d’informations, consultez le document [Gérer des clusters HDInsight à l’aide de l’API REST d’Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="resource-manager-templates"></a>Modèles Resource Manager

Quand vous créez un cluster Kafka à l’aide d’un modèle Azure Resource Manager, vous pouvez définir directement `auto.create.topics.enable` en l’ajoutant dans un `kafka-broker`. L’extrait de code JSON suivant montre comment définir cette valeur sur `true` :

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à activer la création automatique de rubriques pour Apache Kafka sur HDInsight. Pour en savoir plus sur l’utilisation de Kafka, consultez les liens suivants :

* [Analyser les journaux d’activité Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Répliquer des données d’un cluster Kafka à un autre](apache-kafka-mirroring.md)

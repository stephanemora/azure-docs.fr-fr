---
title: Activer la création automatique de rubriques dans Apache Kafka - Azure HDInsight | Microsoft Docs
description: Découvrez comment configurer Apache Kafka sur HDInsight pour créer des rubriques automatiquement. Vous pouvez configurer Kafka en définissant auto.create.topics.enable sur true par le biais d’Ambari ou pendant la création du cluster par le biais de PowerShell ou de modèles Resource Manager.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/18/2018
ms.author: larryfr
ms.openlocfilehash: fa5dd7533259c794671cd16231fd3f530173bfa3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Comment configurer Apache Kafka sur HDInsight pour créer des rubriques automatiquement

Par défaut, Kafka sur HDInsight n’active pas la création automatique de rubriques. Vous pouvez activer la création automatique de rubriques pour les clusters existants à l’aide d’Ambari. Vous pouvez également activer la création automatique de rubriques quand vous créez un cluster Kafka à l’aide d’un modèle Azure Resource Manager.

## <a name="ambari-web-ui"></a>Interface utilisateur web d'Ambari

Pour activer la création automatique de rubriques sur un cluster existant par le biais de l’interface utilisateur web d’Ambari, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez le cluster Kafka.

2. Dans la __vue d’ensemble du cluster__, sélectionnez __Tableau de bord du cluster__. 

    ![Image du portail avec le tableau de bord du cluster sélectionné](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Puis choisissez __Tableau de bord du cluster HDInsight__. Lorsque vous y êtes invité, authentifiez-vous à l’aide des informations d’identification de connexion (admin) pour le cluster.

    ![Image de l’entrée du tableau de bord du cluster HDInsight](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Sélectionnez le service Kafka dans la liste à gauche de la page.

    ![Liste de services](./media/apache-kafka-auto-create-topics/service-list.png)

4. Sélectionnez Configs au milieu de la page.

    ![Onglet de la configuration du service](./media/apache-kafka-auto-create-topics/service-config.png)

5. Dans le champ Filter (Filtrer), entrez la valeur `auto.create`. 

    ![Image du champ de filtrage](./media/apache-kafka-auto-create-topics/filter.png)

    Cette option filtre la liste des propriétés et affiche le paramètre `auto.create.topics.enable`.

6. Définissez la valeur `auto.create.topics.enable` sur `true`, puis sélectionnez Save (Enregistrer). Ajoutez une note, puis resélectionnez Save (Enregistrer).

    ![Image de l’entrée auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Sélectionnez le service Kafka, choisissez __Restart__ (Redémarrer), puis __Restart all affected__ (Redémarrer tous les éléments affectés). Lorsque vous y êtes invité, sélectionnez __Confirm Restart All__ (Confirmer le redémarrage).

    ![Image de la sélection du redémarrage](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]
> Vous pouvez également définir les valeurs Ambari par le biais de l’API REST d’Ambari. Cette méthode est généralement plus difficile, car vous devez effectuer plusieurs appels REST pour récupérer la configuration actuelle, la modifier, etc. Pour plus d’informations, consultez le document [Gérer des clusters HDInsight à l’aide de l’API REST d’Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md).

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

Dans ce document, vous avez appris à activer la création automatique de rubriques pour Kafka sur HDInsight. Pour en savoir plus sur l’utilisation de Kafka, consultez les liens suivants :

* [Analyser les journaux Kafka](apache-kafka-log-analytics-operations-management.md)
* [Répliquer des données entre des clusters Kafka](apache-kafka-mirroring.md)
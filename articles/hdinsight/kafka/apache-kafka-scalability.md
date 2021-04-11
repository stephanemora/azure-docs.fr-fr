---
title: Augmenter l’échelle pour Apache Kafka - Azure HDInsight
description: Découvrez comment configurer des disques managés pour un cluster Apache Kafka sur Azure HDInsight afin d’accroître l’extensibilité.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: 9aa11be42aca59458fea0462a90b6aeb70df893d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863137"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Configurer le stockage et l’extensibilité pour Apache Kafka sur HDInsight

Découvrez comment configurer le nombre de disques managés utilisés par [Apache Kafka](https://kafka.apache.org/) sur HDInsight.

Kafka sur HDInsight utilise le disque local des machines virtuelles dans le cluster HDInsight. Étant donné que Kafka fait une utilisation intensive des E/S, [Azure Disques managés](../../virtual-machines/managed-disks-overview.md) est utilisé pour fournir un haut débit et un espace de stockage plus important pour chaque nœud. Si des disques durs virtuels (VHD) traditionnels ont été utilisés pour Kafka, chaque nœud est limité à 1 To. Grâce aux disques managés, vous pouvez utiliser plusieurs disques afin d’atteindre une capacité de 16 To pour chaque nœud du cluster.

Le diagramme ci-après compare l’utilisation de Kafka sur HDInsight avant les disques managés et celle de Kafka sur HDInsight avec les disques managés :

:::image type="content" source="./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png" alt-text="Kafka - Architecture des disques managés" border="false":::

## <a name="configure-managed-disks-azure-portal"></a>Configurer les disques managés : Portail Azure

1. Pour comprendre la procédure courante de création d’un cluster à l’aide du portail, suivez les instructions de l’article [Création d’un cluster HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md). Ne terminez pas la procédure de création du portail.

2. Dans la section **Configuration et tarification**, utilisez le champ __Nombre de nœuds__ pour configurer le nombre de disques.

    > [!NOTE]  
    > Le type de disque managé peut être soit __Standard__ (HDD), soit __Premium__ (SSD). Les disques Premium sont utilisés avec les machines virtuelles séries DS et GS. Tous les autres types de machines virtuelles utilisent des disques Standard.

    :::image type="content" source="./media/apache-kafka-scalability/azure-portal-cluster-configuration-pricing-kafka-disks.png" alt-text="Section Taille du cluster avec mise en surbrillance des disques par nœud Worker" border="true":::

## <a name="configure-managed-disks-resource-manager-template"></a>Configurer les disques managés : Modèle Resource Manager

Pour contrôler le nombre de disques utilisés par les nœuds Worker dans un cluster Kafka, utilisez la section ci-après du modèle :

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Un modèle complet expliquant comment configurer les disques managés est disponible à l’adresse [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation d’Apache Kafka sur HDInsight, consultez les documents suivants :

* [Utiliser MirrorMaker pour créer un réplica d’Apache Kafka sur HDInsight](apache-kafka-mirroring.md)
* [Utiliser Apache Storm avec Apache Kafka sur HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Utiliser Apache Spark avec Apache Kafka sur HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Se connecter à Apache Kafka via un réseau virtuel Azure](apache-kafka-connect-vpn-gateway.md)

* [Blog HDInsight sur les disques managés avec Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)

---
title: Configurations de nœuds prises en charge pour Azure HDInsight
description: Découvrez les configurations minimales et recommandées pour les nœuds de cluster HDInsight.
keywords: tailles de machine virtuelle, tailles de cluster, configuration de cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e482cf9b5367beba00784e69c5bad88142df5225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076073"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Quelles sont les configurations de nœud par défaut et recommandées pour Azure HDInsight ?

Cet article décrit les configurations de nœud par défaut et recommandées pour les clusters Azure HDInsight.

## <a name="default-and-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Tailles de machine virtuelle et configuration de nœud par défaut et recommandées pour les clusters

Les tableaux suivants répertorient les tailles de machine virtuelle par défaut et recommandées des clusters HDInsight.  Ces informations sont nécessaires pour comprendre les tailles de machine virtuelle à utiliser lorsque vous créez des scripts PowerShell ou Azure CLI pour déployer des clusters HDInsight. 

Si vous avez besoin de plus de 32 nœuds Worker dans un cluster, sélectionnez une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM. 

Les seuls types de clusters dotés de disques de données sont les clusters Kafka et HBase avec la fonctionnalité Écritures accélérées activée. HDInsight prend en charge les tailles de disque P30 et S30 dans ces scénarios.

Pour plus d’informations sur la spécification de chaque type de machine virtuelle, consultez les documents suivants :

* [Tailles de machine virtuelle à usage général : Série Dv2 1-5](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dv2-series)
* [Tailles de machine virtuelle à mémoire optimisée : Série Dv2 11-15](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#dv2-series-11-15)
* [Tailles de machine virtuelle à usage général : Série Av2 1-8](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#av2-series)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Toutes les régions prises en charge à l’exception des régions Brésil Sud et Japon Ouest

> [!Note]
> Pour obtenir l’identificateur de référence (SKU) à utiliser dans PowerShell et d’autres scripts, ajoutez `Standard_` au début de toutes les références (SKU) de machine virtuelle dans les tableaux ci-dessous. Par exemple, `D12_v2` deviendrait `Standard_D12_v2`.

| Type de cluster | Hadoop | hbase | Interactive Query | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Head : taille de machine virtuelle par défaut | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 | D3_v2 |
| Head : tailles de machine virtuelle recommandées | D13_v2,<br/>D14_v2,<br/>D5_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 | D13_v2,<br/>D14_v2 | A4_v2,<br/>A8_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 |
| Worker : taille de machine virtuelle par défaut | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 avec 2 disques S30 par répartiteur |
| Worker : tailles de machine virtuelle recommandées | D5_v2,<br>D12_v2,<br/>D13_v2 | D3_v2,<br/>D4_v2,<br/>D13_v2 | D13_v2,<br/>D14_v2 | D3_v2<br/>D4_v2,<br/>D12_v2 | D12_v2,<br>D13_v2,<br>D14_v2 | D4_v2,<br/>D12_v2,<br>D13_v2,<br>D14_v2 | D3_v2,<br/>D4_v2,<br/>DS3_v2,<br/>DS4_v2 |
| Zookeeper : taille de machine virtuelle par défaut |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| Zookeeper : tailles de machine virtuelle recommandées |  | A4_v2, <br/>A8_v2, <br/>A2m_v2 | A4_v2,<br/>A8_v2,<br/>A2m_v2 | A4_v2,<br/>A2_v2,<br/>A8_v2 |  | A2_v2 | A4_v2,<br/> A8_v2,<br/>A2m_v2 |
| ML Services : taille de machine virtuelle par défaut |  |  |  |  |  | D4_v2 |  |
| ML Services : taille de machine virtuelle recommandée |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/>D14_v2 |  |

### <a name="brazil-south-and-japan-west-only"></a>Brésil Sud et Japon Ouest uniquement

| Type de cluster | Hadoop | hbase | Interactive Query | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Head : taille de machine virtuelle par défaut | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Head : tailles de machine virtuelle recommandées | D5_v2,<br/> D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D13_v2,<br/> D14_v2 | A4_v2,<br/> A8_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 |
| Worker : taille de machine virtuelle par défaut | D4 | D4 | D14 | D3 | D13 | D4 |
| Worker : tailles de machine virtuelle recommandées | D5_v2,<br/> D12_v2,<br/> D13_v2 | D3_v2,<br/> D4_v2,<br/> D13_v2 | D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |
| Zookeeper : taille de machine virtuelle par défaut |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Zookeeper : tailles de machine virtuelle recommandées |  | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2 |  | A2_v2 |
| ML Services : tailles de machine virtuelle par défaut |  |  |  |  |  | D4 |
| ML Services : tailles de machine virtuelle recommandées |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |

> [!NOTE]
> - Head est appelé *Nimbus* pour le type de cluster Storm.
> - L’élément Worker est appelé *Supervisor* pour le type de cluster Storm.
> - L’élément Worker est appelé *Region* pour le type de cluster HBase.

## <a name="next-steps"></a>Étapes suivantes

* [Quels sont les composants et versions Apache Hadoop disponibles avec HDInsight ?](hdinsight-component-versioning.md)

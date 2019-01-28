---
title: Mettre à l’échelle automatiquement les clusters Azure HDInsight
description: Utilisez la fonction HDInsight de mise à l’échelle automatique pour mettre automatiquement à l’échelle les clusters
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: hrasheed
ms.openlocfilehash: 043c83e2039d87b1650ba17f770ce16a2ad2c13d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811160"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Mettre à l’échelle automatiquement les clusters Azure HDInsight

La fonction de mise à l’échelle automatique de cluster d’Azure HDInsight met automatiquement à l’échelle le nombre de nœuds Worker dans un cluster en fonction de la charge dans une plage prédéfinie. Lors de la création d’un cluster HDInsight, il est possible de définir un nombre minimum et un nombre maximum de nœuds Worker. La mise à l’échelle automatique surveille ensuite les besoins en ressources de la charge analytique et augmente ou diminue le nombre de nœuds Worker en conséquence. L’utilisation de cette fonctionnalité n’entraîne aucun coût supplémentaire.

## <a name="getting-started"></a>Mise en route

### <a name="create-cluster-with-azure-portal"></a>Créer un cluster avec le portail Azure

> [!Note]
> La fonction de mise à l’échelle automatique n’est actuellement prise en charge que pour les clusters Azure HDInsight Hive, MapReduce et Spark version 3.6.

Suivez les étapes de [Créer des clusters Linux dans HDInsight à l’aide du portail Azure](hdinsight-hadoop-create-linux-clusters-portal.md) et, lorsque vous atteignez l’étape 5, **Taille du cluster**, sélectionnez **Worker node Autoscale (preview)** (Mise à l’échelle des nœuds Worker (préversion)) comme indiqué ci-dessous. 

![Activer la mise à l’échelle automatique des nœuds Worker](./media/hdinsight-autoscale-clusters/worker-node-autoscale-option.png)

En cochant cette option, vous pouvez spécifier les paramètres suivants :

* Nombre initial de nœuds Worker
* Nombre minimal de nœuds Worker
* Nombre maximal de nœuds Worker

Le nombre initial de nœuds Worker doit se situer entre le minimum et le maximum inclus. Cette valeur définit la taille initiale du cluster lors de sa création. Le nombre minimal de nœuds Worker doit être supérieur à zéro.

Après avoir sélectionné le type de machine virtuelle pour chaque type de nœud, vous pouvez voir la fourchette de coûts estimée pour l’ensemble du cluster. Vous pouvez ensuite ajuster ces paramètres en fonction de votre budget.

Votre abonnement a un quota de capacité pour chaque région. Le nombre total de cœurs de vos nœuds principaux combiné au nombre maximum de nœuds Worker ne peut dépasser le quota de capacité. Toutefois, ce quota est une limite logicielle ; vous pouvez toujours créer un ticket de support pour l’augmenter aisément.

> [!Note]
> Si vous dépassez la limite totale de quota de base, vous recevrez un message d’erreur disant indiquant que le nœud maximum a dépassé les noyaux disponibles dans cette région et que vous devez choisir une autre région ou contacter le support pour augmenter le quota (« the maximum node exceeded the available cores in this region, please choose another region or contact the support to increase the quota »).

### <a name="create-cluster-with-an-resource-manager-template"></a>Créer un cluster avec un modèle Resource Manager

Lorsque vous créez un cluster HDInsight avec un modèle de Resource Manager, vous devez ajouter les paramètres suivants à la section "computeProfile" "worker node" :

```json
{                            
    "name": "workernode",
    "targetInstanceCount": 4,
    "autoscale": {
        "minInstanceCount": 2,
        "maxInstanceCount": 10
    },
    "hardwareProfile": {
        "vmSize": "Standard_D13_V2"
    },
    "osProfile": {
        "linuxOperatingSystemProfile": {
            "username": "[parameters('sshUserName')]",
            "password": "[parameters('sshPassword')]"
        }
    },
    "virtualNetworkProfile": null,
    "scriptActions": []
}
```

### <a name="enable-and-disabling-autoscale-for-a-running-cluster"></a>Activer et désactiver la mise à l’échelle pour un cluster en cours d’exécution

L’activation de la mise à l’échelle automatique pour un cluster en cours d’exécution n’est pas prise en charge pendant la préversion privée. Elle doit être activée lors de la création du cluster.

La désactivation de la mise à l’échelle automatique ou la modification des paramètres de mise à l’échelle automatique pour un cluster en cours d’exécution n’est pas prise en charge en préversion privée. Vous devez supprimer le cluster et en créer un nouveau pour supprimer ou modifier les paramètres.

## <a name="monitoring"></a>Surveillance

Vous pouvez afficher l’historique de Scale up/Scale down du cluster dans le cadre des métriques de celui-ci. Vous pouvez dresser la liste de toutes les actions de mise à l’échelle au cours de la journée, de la semaine ou d’une période de temps plus longue.

## <a name="how-it-works"></a>Fonctionnement

### <a name="metrics-monitoring"></a>Supervision des métriques

La mise à l’échelle automatique supervise en permanence le cluster et collecte les métriques suivantes :

1. **Total Pending CPU** : Nombre total de cœurs nécessaires pour commencer l’exécution de tous les conteneurs en attente.
2. **Total Pending Memory** : Mémoire totale (en Mo) nécessaire pour commencer l’exécution de tous les conteneurs en attente.
3. **Total Free CPU** : Somme de tous les cœurs inutilisés sur les nœuds Worker actifs.
4. **Total Free Memory** : Somme de la mémoire inutilisée (en Mo) sur les nœuds Worker actifs.
5. **Mémoire utilisée par nœud** : Charge sur un nœud Worker. Un nœud Worker sur lequel 10 Go de mémoire sont utilisés est considéré comme étant plus sollicité qu’un nœud avec 2 Go de mémoire utilisés.
6. **Nombre d’Application Masters par nœud** : Nombre de conteneurs Application Master (AM) en cours d’exécution sur un nœud Worker. Un nœud Worker hébergeant 2 conteneurs AM est considéré comme plus important qu’un nœud Worker hébergeant 0 conteneur AM.

Les métriques ci-dessus sont contrôlées toutes les 60 secondes. La fonction de mise à l’échelle automatique prend des décisions de montée en puissance ou de descente en puissance en fonction de ces métriques.

### <a name="cluster-scale-up"></a>Montée en puissance des clusters

Lorsque les conditions suivantes sont détectées, la fonction de mise à l’échelle automatique émet une demande de montée en puissance :

* « Total pending CPU » est supérieur à la valeur de « Total free CPU » pendant plus de 1 minute.
* « Total pending memory » est supérieur à la valeur de « Total free memory » pendant plus de 1 minute.

Nous allons calculer que N nouveaux nœuds Worker sont nécessaires pour répondre aux besoins actuels en UC et en mémoire, puis nous allons effectuer une requête de mise à l’échelle en demandant N nouveaux nœuds Worker.

### <a name="cluster-scale-down"></a>Descendre en puissance pour les clusters

Lorsque les conditions suivantes sont détectées, la fonction de mise à l’échelle automatique émet une demande de descente en puissance :

* « Total pending CPU » est inférieur à la valeur de « Total free CPU » pendant plus de 10 minutes.
* « Total pending memory » est inférieur à la valeur de « Total free memory » pendant plus de 10 minutes.

En fonction du nombre de conteneurs AM par nœud ainsi que des besoins actuels en UC et en mémoire, la fonction de mise à l’échelle automatique émet une demande de suppression de N nœuds, en précisant quels nœuds sont des candidats potentiels à la suppression. Par défaut, deux nœuds sont supprimés par cycle.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les meilleures pratiques de mise à l’échelle manuelle des clusters, lisez [Scaling best practices](hdinsight-scaling-best-practices.md) (Meilleures pratiques de la mise à l’échelle).

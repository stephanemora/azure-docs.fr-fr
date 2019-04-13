---
title: Automatiquement à l’échelle des clusters Azure HDInsight (version préliminaire)
description: Utilisez la fonction HDInsight de mise à l’échelle automatique pour mettre automatiquement à l’échelle les clusters
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: hrasheed
ms.openlocfilehash: 6642f80a40343546285770531ac42423bee779b8
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526487"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Automatiquement à l’échelle des clusters Azure HDInsight (version préliminaire)

>[!Important]
>La fonctionnalité HDInsight Autoscale est actuellement en version préliminaire. Envoyez un e-mail à hdiautoscalepm@microsoft.com pour que l’échelle automatique est activée pour votre abonnement.

La fonction de mise à l’échelle automatique de cluster d’Azure HDInsight met automatiquement à l’échelle le nombre de nœuds Worker dans un cluster en fonction de la charge dans une plage prédéfinie. Lors de la création d’un cluster HDInsight, il est possible de définir un nombre minimum et un nombre maximum de nœuds Worker. La mise à l’échelle automatique surveille ensuite les besoins en ressources de la charge analytique et augmente ou diminue le nombre de nœuds Worker en conséquence. L’utilisation de cette fonctionnalité n’entraîne aucun coût supplémentaire.

## <a name="getting-started"></a>Prise en main

### <a name="create-a-cluster-with-the-azure-portal"></a>Création d’un cluster dans le portail Azure

> [!Note]
> La fonction de mise à l’échelle automatique n’est actuellement prise en charge que pour les clusters Azure HDInsight Hive, MapReduce et Spark version 3.6.

Pour activer la fonctionnalité de mise à l’échelle, procédez comme suit dans le cadre du processus de création de cluster normal :

1. Sélectionnez **Personnalisé (taille, paramètres, applications)** plutôt que **Création rapide**.
2. À l’étape 5 **Personnalisé** (**Taille du cluster**), cochez la case **Mise à l’échelle automatique de nœud worker (préversion)**.
3. Entrez les valeurs souhaitées pour les propriétés suivantes :  

    * le **nombre initial de nœuds worker** ;  
    * le nombre **minimal** de nœuds worker ;  
    * le nombre **maximal** de nœuds worker.  

![Activer la mise à l’échelle automatique des nœuds Worker](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

Le nombre initial de nœuds Worker doit se situer entre le minimum et le maximum inclus. Cette valeur définit la taille initiale du cluster lors de sa création. Le nombre minimal de nœuds Worker doit être supérieur à zéro.

Après avoir sélectionné le type de machine virtuelle pour chaque type de nœud, vous pouvez voir la fourchette de coûts estimée pour l’ensemble du cluster. Vous pouvez ensuite ajuster ces paramètres en fonction de votre budget.

Votre abonnement a un quota de capacité pour chaque région. Le nombre total de cœurs de vos nœuds principaux combiné au nombre maximum de nœuds Worker ne peut dépasser le quota de capacité. Toutefois, ce quota est une limite logicielle ; vous pouvez toujours créer un ticket de support pour l’augmenter aisément.

> [!Note]  
> Si vous dépassez la limite totale de quota de base, vous recevrez un message d’erreur disant indiquant que le nœud maximum a dépassé les noyaux disponibles dans cette région et que vous devez choisir une autre région ou contacter le support pour augmenter le quota (« the maximum node exceeded the available cores in this region, please choose another region or contact the support to increase the quota »).

Pour plus d’informations sur la création de clusters HDInsight à l’aide du portail Azure, consultez [Créer des clusters Linux dans HDInsight à l’aide du portail Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Création d’un cluster avec un modèle Resource Manager

Pour créer un cluster HDInsight avec un modèle Azure Resource Manager, ajoutez un nœud `autoscale` à la section `computeProfile` > `workernode` avec les propriétés `minInstanceCount` et `maxInstanceCount`, comme indiqué dans l’extrait de code json indiqué ci-dessous.

```json
{                            
    "name": "workernode",
    "targetInstanceCount": 4,
    "autoscale": {
        "capacity": {
            "minInstanceCount": 2,
            "maxInstanceCount": 10
        }        
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

Pour plus d’informations sur la création de clusters avec des modèles Resource Manager, consultez [Création de clusters Apache Hadoop dans HDInsight à l’aide de modèles Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Activation et désactivation de la mise à l’échelle automatique d’un cluster en cours d’exécution

Vous pouvez uniquement activer ou désactiver l’échelle automatique pour les nouveaux clusters HDInsight.

## <a name="monitoring"></a>Surveillance

Vous pouvez afficher l’historique de montée et descente en puissance du cluster dans le cadre des mesures de cluster. Vous pouvez également dresser la liste de toutes les actions de mise à l’échelle au cours de la journée, de la semaine ou d’une période de temps plus longue.

## <a name="how-it-works"></a>Fonctionnement

### <a name="metrics-monitoring"></a>Supervision des métriques

La mise à l’échelle automatique supervise en permanence le cluster et collecte les métriques suivantes :

1. **Total Pending CPU** : Nombre total de cœurs nécessaires pour commencer l’exécution de tous les conteneurs en attente.
2. **Total Pending Memory** : Mémoire totale (en Mo) nécessaire pour commencer l’exécution de tous les conteneurs en attente.
3. **Total Free CPU** : Somme de tous les cœurs inutilisés sur les nœuds Worker actifs.
4. **Total Free Memory** : Somme de la mémoire inutilisée (en Mo) sur les nœuds Worker actifs.
5. **Mémoire utilisée par nœud** : Charge sur un nœud Worker. Un nœud Worker sur lequel 10 Go de mémoire sont utilisés est considéré comme étant plus sollicité qu’un nœud avec 2 Go de mémoire utilisés.
6. **Nombre d’Application Masters par nœud** : Nombre de conteneurs Application Master (AM) en cours d’exécution sur un nœud Worker. Un nœud de travail qui héberge deux conteneurs h, est considéré comme plus important qu’un nœud de travail qui héberge les conteneurs de zéro AM.

Les métriques ci-dessus sont contrôlées toutes les 60 secondes. Mise à l’échelle prendrez des décisions montée et descente en puissance en fonction de ces mesures.

### <a name="cluster-scale-up"></a>Monter en puissance du cluster

Lorsque les conditions suivantes sont détectées, mise à l’échelle émet une demande de montée en charge :

* Total en attente du processeur est supérieure à processeur total pour plus de 3 minutes.
* Total mémoire en attente est supérieur à la mémoire disponible totale. pour plus de 3 minutes.

Nous calculera qu’un certain nombre de nouveaux nœuds worker est nécessaires pour répondre aux besoins de processeur et mémoire actuels et puis émettre une demande de montée en charge qui ajoute ce nombre de nouveaux nœuds worker.

### <a name="cluster-scale-down"></a>Augmentez la taille de cluster

Lorsque les conditions suivantes sont détectées, mise à l’échelle émet une demande de mise à l’échelle vers le bas :

* « Total pending CPU » est inférieur à la valeur de « Total free CPU » pendant plus de 10 minutes.
* « Total pending memory » est inférieur à la valeur de « Total free memory » pendant plus de 10 minutes.

Selon le nombre de conteneurs h par nœud et le processeur actuel et les besoins en mémoire, mise à l’échelle émet une demande pour supprimer un certain nombre de nœuds, qui spécifie les nœuds sur lesquels sont des candidats potentiels pour la suppression. La mise à l’échelle vers le bas déclenchera la désaffectation des nœuds et une fois que les nœuds sont complètement désactivés, ils seront supprimés.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les meilleures pratiques de mise à l’échelle manuelle des clusters, lisez [Scaling best practices](hdinsight-scaling-best-practices.md) (Meilleures pratiques de la mise à l’échelle).

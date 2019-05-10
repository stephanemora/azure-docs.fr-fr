---
title: Automatiquement à l’échelle des clusters Azure HDInsight (version préliminaire)
description: Utilisez la fonction HDInsight de mise à l’échelle automatique pour mettre automatiquement à l’échelle les clusters
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: hrasheed
ms.openlocfilehash: f8803a498e62958a5488f2ac8830137c37533e54
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413689"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Automatiquement à l’échelle des clusters Azure HDInsight (version préliminaire)

Fonctionnalité de mise à l’échelle d’Azure HDInsight cluster s’ajuste automatiquement le nombre de nœuds de travail dans un cluster de haut et bas. Autres types de nœuds dans le cluster ne peut pas être mis à l’échelle actuellement.  Lors de la création d’un cluster HDInsight, il est possible de définir un nombre minimum et un nombre maximum de nœuds Worker. Mise à l’échelle surveille les besoins en ressources de la charge d’analytique, puis met à l’échelle le nombre de nœuds de travail vers le haut ou vers le bas. Aucun frais supplémentaire n’est pour cette fonctionnalité.

## <a name="cluster-compatibility"></a>Compatibilité de cluster

> [!Important]
> La fonctionnalité de mise à l’échelle fonctionne uniquement pour les clusters créés après la disponibilité publique de la fonctionnalité de mai 2019. Il ne fonctionnera pas pour les clusters préexistants.

Le tableau suivant décrit les types de cluster et les versions qui sont compatibles avec la fonctionnalité de mise à l’échelle.

| Version | Spark | Hive | LLAP | hbase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 sans ESP | Oui | Oui | Non  | Non  | Non  | Non  | Non |
| HDInsight 4.0 sans ESP | Oui | Oui | Non  | Non  | Non  | Non  | Non |
| HDInsight 3.6 avec ESP | Oui | Oui | Non  | Non  | Non  | Non  | Non |
| HDInsight 3.6 avec ESP | Oui | Oui | Non  | Non  | Non  | Non  | Non |

## <a name="how-it-works"></a>Fonctionnement

Vous pouvez choisir en fonction de charge mise à l’échelle ou basée sur la planification de mise à l’échelle de votre cluster HDInsight. Basée sur la charge mise à l’échelle modifie le nombre de nœuds dans votre cluster, dans une plage que vous définissez, pour garantir une utilisation optimale du processeur et de réduire les coûts en cours d’exécution.

Modifications de mise à l’échelle basée sur la planification du nombre de nœuds dans votre cluster selon les conditions qui prennent effet à des moments donnés. Ces conditions à l’échelle du cluster à un nombre de nœuds souhaité.

### <a name="metrics-monitoring"></a>Supervision des métriques

La mise à l’échelle automatique supervise en permanence le cluster et collecte les métriques suivantes :

* **Total Pending CPU** : Nombre total de cœurs nécessaires pour commencer l’exécution de tous les conteneurs en attente.
* **Total Pending Memory** : Mémoire totale (en Mo) nécessaire pour commencer l’exécution de tous les conteneurs en attente.
* **Total Free CPU** : Somme de tous les cœurs inutilisés sur les nœuds Worker actifs.
* **Total Free Memory** : Somme de la mémoire inutilisée (en Mo) sur les nœuds Worker actifs.
* **Mémoire utilisée par nœud** : Charge sur un nœud Worker. Un nœud Worker sur lequel 10 Go de mémoire sont utilisés est considéré comme étant plus sollicité qu’un nœud avec 2 Go de mémoire utilisés.
* **Nombre d’Application Masters par nœud** : Nombre de conteneurs Application Master (AM) en cours d’exécution sur un nœud Worker. Un nœud de travail qui héberge deux conteneurs h, est considéré comme plus important qu’un nœud de travail qui héberge les conteneurs de zéro AM.

Les métriques ci-dessus sont contrôlées toutes les 60 secondes. Mise à l’échelle prend les décisions de montée et descente en puissance en fonction de ces mesures.

### <a name="load-based-cluster-scale-up"></a>Monter d’un cluster basé sur la charge

Lorsque les conditions suivantes sont détectées, mise à l’échelle émet une demande de montée en charge :

* Total en attente du processeur est supérieure à temps processeur total gratuit pendant plus de 3 minutes.
* Total en attente de la mémoire est supérieure à la mémoire libre total pendant plus de 3 minutes.

Le service HDInsight calcule le nombre de nœuds worker nouvelle est nécessaires pour répondre aux besoins en mémoire et processeur actuel et lui envoie ensuite une demande de monter en puissance pour ajouter le nombre de nœuds requis.

### <a name="load-based-cluster-scale-down"></a>Cluster basé sur les charge mise à l’échelle vers le bas

Lorsque les conditions suivantes sont détectées, mise à l’échelle émet une demande de mise à l’échelle vers le bas :

* « Total pending CPU » est inférieur à la valeur de « Total free CPU » pendant plus de 10 minutes.
* « Total pending memory » est inférieur à la valeur de « Total free memory » pendant plus de 10 minutes.

Selon le nombre de conteneurs h par nœud et le processeur actuel et les besoins en mémoire, la mise à l’échelle émet une demande pour supprimer un certain nombre de nœuds. Le service détecte également qui nœuds sont des candidats pour la suppression en fonction de l’exécution du travail en cours. L’opération de réduction démantèle tout d’abord les nœuds et les supprime du cluster.

## <a name="get-started"></a>Prise en main

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Créer un cluster avec mise à l’échelle basée sur la charge

Pour activer la fonctionnalité de mise à l’échelle basée sur la charge mise à l’échelle, procédez comme suit dans le cadre du processus de création de cluster normal :

1. Sélectionnez **Personnalisé (taille, paramètres, applications)** plutôt que **Création rapide**.
1. Sur **personnalisé** étape 5 (**taille du Cluster**), vérifiez le **l’échelle automatique du nœud Worker** case à cocher.
1. Sélectionnez l’option **basée sur la charge** sous **type de mise à l’échelle**.
1. Entrez les valeurs souhaitées pour les propriétés suivantes :  

    * le **nombre initial de nœuds worker** ;  
    * le nombre **minimal** de nœuds worker ;  
    * le nombre **maximal** de nœuds worker.  

    ![Activer l’option de mise à l’échelle basée sur la charge de nœud de travail](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

Le nombre initial de nœuds Worker doit se situer entre le minimum et le maximum inclus. Cette valeur définit la taille initiale du cluster lors de sa création. Le nombre minimal de nœuds Worker doit être supérieur à zéro.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Créer un cluster avec mise à l’échelle basée sur la planification

Pour activer la fonctionnalité de mise à l’échelle basée sur la planification de mise à l’échelle, procédez comme suit dans le cadre du processus de création de cluster normal :

1. Sélectionnez **Personnalisé (taille, paramètres, applications)** plutôt que **Création rapide**.
1. Sur **personnalisé** étape 5 (**taille du Cluster**), vérifiez le **l’échelle automatique du nœud Worker** case à cocher.
1. Entrez le **nombre de nœuds de travail**, qui contrôle la limite de mise à l’échelle le cluster.
1. Sélectionnez l’option **basée sur la planification** sous **type de mise à l’échelle**.
1. Cliquez sur **configurer** pour ouvrir le **configuration de la mise à l’échelle** fenêtre.
1. Sélectionnez votre fuseau horaire, puis cliquez **+ ajouter une condition**
1. Sélectionnez les jours de la semaine où la nouvelle condition doit s’appliquer à.
1. Modifier l’heure d’à que la condition doit prendre effet et le nombre de nœuds du cluster doit être mis à l’échelle.
1. Ajouter d’autres conditions si nécessaire.

    ![Activer l’option de mise à l’échelle basée sur la planification de nœud de travail](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Le nombre de nœuds doit être comprise entre 1 et le nombre de nœuds de travail que vous avez entré avant d’ajouter des conditions.

### <a name="final-creation-steps"></a>Étapes de création finale

Pour les charge et basée sur la planification de mise à l’échelle, sélectionnez le type de machine virtuelle pour les nœuds de travail en cliquant sur **taille du nœud Worker** et **taille du nœud principal**. Une fois que vous choisissez le type de machine virtuelle pour chaque type de nœud, vous pouvez voir la plage de coût estimé pour l’ensemble du cluster. Ajuster les types de machine virtuelle à votre budget.

![Activer l’option de mise à l’échelle basée sur la planification de nœud de travail](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-node-size-selection.png)

Votre abonnement a un quota de capacité pour chaque région. Le nombre total de cœurs de vos nœuds principaux combiné au nombre maximum de nœuds Worker ne peut dépasser le quota de capacité. Toutefois, ce quota est une limite logicielle ; vous pouvez toujours créer un ticket de support pour l’augmenter aisément.

> [!Note]  
> Si vous dépassez la limite de quota de cœurs total, vous recevrez un message d’erreur indiquant que « le nœud maximal dépassé les cœurs disponibles dans cette région, veuillez choisir une autre région ou contactez le support technique pour augmenter le quota. »

Pour plus d’informations sur la création de clusters HDInsight à l’aide du portail Azure, consultez [Créer des clusters Linux dans HDInsight à l’aide du portail Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Création d’un cluster avec un modèle Resource Manager

#### <a name="load-based-autoscaling"></a>Mise à l’échelle basée sur la charge

Vous pouvez créer un cluster HDInsight avec la mise à l’échelle basée sur la charge un modèle Azure Resource Manager, en ajoutant un `autoscale` nœud à la `computeProfile`  >  `workernode` section avec les propriétés `minInstanceCount` et `maxInstanceCount` en tant que indiqué dans l’extrait de code json ci-dessous.

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

#### <a name="schedule-based-autoscaling"></a>Mise à l’échelle basée sur la planification

Vous pouvez créer un cluster HDInsight avec basée sur la planification de mise à l’échelle un modèle Azure Resource Manager, en ajoutant un `autoscale` nœud à la `computeProfile`  >  `workernode` section. Le `autoscale` nœud contient un `recurrence` qui a un `timezone` et `schedule` qui décrit quand la modification aura lieu.

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Activation et désactivation de la mise à l’échelle automatique d’un cluster en cours d’exécution

Pour activer la mise à l’échelle sur un cluster en cours d’exécution, sélectionnez **taille du Cluster** sous **paramètres**. Puis cliquez sur **activer la mise à l’échelle**. Sélectionnez le type de mise à l’échelle que vous voulez et entrez les options de mise à l’échelle basée sur la planification ou charge. Puis, cliquez sur **Enregistrer**.

![Activer l’option de mise à l’échelle basée sur la planification de nœud de travail](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

## <a name="monitoring"></a>Surveillance

### <a name="cluster-status"></a>État du cluster

L’état du cluster répertoriée dans le portail Azure peut vous aider à surveiller les activités de mise à l’échelle.

![Activer l’option de mise à l’échelle basée sur la charge de nœud de travail](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Tous les messages d’état de cluster que vous pouvez voir sont expliquées dans la liste ci-dessous.

| État du cluster | Explication |
|---|---|
| Exécution | Le cluster fonctionne normalement. Toutes les activités de mise à l’échelle précédentes est terminée avec succès. |
| Mise à jour  | La configuration de mise à l’échelle du cluster est en cours de mise à jour.  |
| Configuration HdInsight  | Monter en puissance un cluster ou opération de réduction est en cours.  |
| Erreur de la mise à jour  | HDInsight a rencontré des problèmes pendant la mise à jour de la configuration mise à l’échelle. Les clients peuvent choisir réessayer la mise à jour ou désactiver la mise à l’échelle.  |
| Error  | Quelque chose ne va pas avec le cluster, et il n’est pas utilisable. Supprimer ce cluster et créez-en un.  |

Pour afficher le nombre actuel de nœuds dans votre cluster, accédez à la **taille du Cluster** du graphique sur le **vue d’ensemble** page de votre cluster, ou cliquez sur **taille du Cluster** sous  **Paramètres**.

### <a name="operation-history"></a>Historique de l’opération

Vous pouvez afficher l’historique de montée et descente en puissance du cluster dans le cadre des mesures de cluster. Vous pouvez également répertorier toutes les actions de mise à l’échelle via le jour précédent, semaine ou autre période de temps.

Sélectionnez **métriques** sous **surveillance**. Puis cliquez sur **ajouter une métrique** et **nombre de threads de travail actifs** à partir de la **métrique** zone de liste déroulante. Cliquez sur le bouton en haut à droite pour modifier l’intervalle de temps.

![Activer l’option de mise à l’échelle basée sur la planification de nœud de travail](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)


## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les meilleures pratiques de mise à l’échelle manuelle des clusters, lisez [Scaling best practices](hdinsight-scaling-best-practices.md) (Meilleures pratiques de la mise à l’échelle).

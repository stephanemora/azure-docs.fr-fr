---
title: Le répartiteur ne peut pas démarrer en raison d’un disque plein dans Azure HDInsight
description: Étapes de dépannage pour le processus du répartiteur Apache Kafka qui ne peut pas démarrer en raison d’une erreur de disque plein.
ms.service: hdinsight
ms.topic: troubleshooting
author: Jacky-hdi
ms.author: linjzhu
ms.date: 10/11/2021
ms.openlocfilehash: 3e7d113da1c086c0a95916dd29c9149beb897862
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859642"
---
# <a name="scenario-brokers-are-unhealthy-or-cant-restart-due-to-disk-space-full-issue"></a>Scénario : Les répartiteurs ne sont pas intègres ou ne peuvent pas redémarrer en raison d’un problème d’espace disque plein

Cet article décrit les étapes de dépannage et les résolutions possibles pour les problèmes d’utilisation d’Apache Kafka dans les clusters Azure HDInsight.

## <a name="issue"></a>Problème

Les disques de données utilisés par les répartiteurs Apache Kafka dans les clusters Azure HDInsight peuvent se remplir. Lorsque cela se produit, le processus du répartiteur Apache Kafka ne peut pas démarrer et échoue en raison d’une erreur de disque plein. Si vous avez apporté des modifications récentes à la configuration, ces modifications ne prennent pas effet, car le processus du répartiteur Kafka ne démarre pas.

## <a name="cause"></a>Cause

Le problème peut être causé par un ou plusieurs des scénarios suivants :

- Vous ne pouvez pas augmenter le nombre de disques ou augmenter la taille du disque après avoir créé le cluster Kafka.
- En règle générale, les alertes de disque sont configurées dans l’interface utilisateur d’Apache Ambari. Lorsque l’utilisation du disque dépasse 60 %, une alerte vous informe que vous devez soit effectuer un scale-out, soit réduire le nombre de journaux présents dans le cluster Kafka.
- Ce problème se produit uniquement si vous ignorez les alertes de disque. Vous pouvez effectuer un scale-out du cluster afin de libérer de l’espace pour répondre aux alertes d’espace disque.
- Les messages ne sont pas immédiatement supprimés, même si la durée de rétention est écoulée. Les messages à supprimer seront marqués pour suppression. Plus tard, un processus de nettoyage en arrière-plan supprime les messages. Seuls les messages des segments passifs sont supprimés.

> [!IMPORTANT]
> Vous pouvez utiliser une configuration pour améliorer les performances du nettoyage des journaux, mais soyez *prudent* lorsque vous appliquez cette amélioration, car cela peut avoir un impact sur la production ou la consommation des messages.

## <a name="mitigation"></a>Limitation des risques

Pour atténuer le problème :

1. Vérifiez les fichiers `server.properties` pour trouver la durée de rétention pour chaque rubrique. Parfois, la stratégie de rétention des journaux est définie au niveau de la rubrique. Pour rechercher la durée de rétention configurée au niveau de la rubrique, exécutez la commande suivante :

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --describe --zookeeper <zookeeper-list>
    ```

2. Lorsque vous disposez de ces informations, vérifiez quelles partitions lourdes occupent le maximum d’espace disque.

    ```bash
    # Command to sort the directories based on size:
    du -hs * | sort -rh | head -5 
    ```

3. Sauvegardez les fichiers antérieurs à la nouvelle durée de rétention.

4. Lorsque de l’espace libre est disponible, vous pouvez redémarrer les répartiteurs pour utiliser la nouvelle configuration de la durée de rétention. Le redémarrage des répartiteurs nettoie les journaux plus anciens et libère de l’espace sur le disque.

   > [!IMPORTANT]
   > Parfois, il est impossible d’effectuer une sauvegarde, car le disque du système d’exploitation peut être saturé ou cela surchargerait les autres disques Kafka. Dans ce cas, vous devrez peut-être supprimer des fichiers dont l’ancienneté est supérieure à la durée de rétention.

## <a name="resolution"></a>Résolution

Même si vous pouvez réduire la durée de rétention, la configuration n’est pas évolutive si vous souhaitez ajouter des rubriques supplémentaires dans le cluster ou si la charge ou la quantité de données qui est ingérée augmente.

Pour éviter ces scénarios, envisagez d’utiliser l’une des options suivantes :

- Si les partitions sont trop volumineuses, augmentez le nombre de partitions pour les rubriques les plus lourdes.

  > [!NOTE]
  > L’augmentation du nombre de partitions dans une rubrique existante ne réorganise pas les données de la rubrique. Vous devez d’abord copier manuellement les données d’une ancienne rubrique à partition basse vers une nouvelle rubrique à partition plus élevée. 

- Effectuez un scale-out du cluster et rééquilibrez toutes les partitions sur plusieurs disques.

- Créez un cluster qui dispose d’un SKU plus élevé et de plus de disques attachés.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]

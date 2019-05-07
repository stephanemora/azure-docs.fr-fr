---
title: Configurer la planification de la mise à jour corrective du système d’exploitation pour les clusters HDInsight basés sur Linux - Azure
description: Découvrez comment configurer la planification de la mise à jour corrective du système d’exploitation pour les clusters HDInsight sous Linux.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: 5b8ed75863087e077d483c792ac4134a0c3e1eb0
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203634"
---
# <a name="os-patching-for-hdinsight"></a>Mise à jour corrective du système d’exploitation pour HDInsight 

> [!IMPORTANT]
> Les images Ubuntu sont accessibles pour la création d'un nouveau cluster HDInsight dans les 3 mois qui suivent leur publication. Depuis janvier 2019, **plus aucun** correctif automatique n'est disponible pour les clusters en cours d'exécution. Les clients doivent utiliser des actions de script ou d'autres mécanismes pour corriger un cluster en cours d'exécution. Les clusters nouvellement créés disposeront toujours des mises à jour et des correctifs de sécurité les plus récents.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Comment configurer la planification de la mise à jour corrective du système d’exploitation pour les clusters HDInsight sous Linux
Les machines virtuelles d’un cluster HDInsight doivent être occasionnellement redémarrées pour assurer l’installation des correctifs de sécurité importants. 

À l’aide de l’action de script décrite dans cet article, vous pouvez modifier la planification de la mise à jour corrective du système d’exploitation comme suit :
1. Installez les mises à jour du système d’exploitation complets ou uniquement les mises à jour de sécurité
2. Redémarrez la machine virtuelle

> [!NOTE]  
> Cette action de script fonctionne uniquement avec les clusters HDInsight sous Linux créés après le 1er août 2016. Les correctifs seront appliqués une fois les machines virtuelles redémarrées. Ce script n’applique pas automatiquement les mises à jour de tous les cycles de mise à jour ultérieure. Exécutez le script que chaque fois qu’une nouvelle mises à jour doivent être appliquées pour installer les mises à jour et de redémarrer la machine virtuelle.

## <a name="how-to-use-the-script"></a>Utilisation du script 

L’utilisation de ce script requiert les informations suivantes :
1. L’emplacement du script : https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/os-patching-reboot-config.sh.  HDInsight utilise cet URI pour rechercher et exécuter le script sur toutes les machines virtuelles du cluster.
  
2. Les types de nœud de cluster auxquels s’applique le script : headnode, workernode, zookeeper. Ce script doit être appliqué à tous les types de nœud du cluster. S’il n’est pas appliqué à un type de nœud, puis les machines virtuelles pour ce type de nœud ne sera pas mis à jour.


3.  Paramètre : Ce script accepte un paramètre numérique :

    | Paramètre | Définition |
    | --- | --- |
    | Du système d’exploitation complet de l’installation des mises à jour/installer uniquement les mises à jour de sécurité |0 ou 1. La valeur 0 installe les mises à jour de sécurité uniquement pendant que 1 installe des mises à jour de système d’exploitation complets. Si aucun paramètre n’est fourni à que la valeur par défaut est 0. |

> [!NOTE]  
> Vous devez marquer ce script comme persistant lorsqu’il s’applique à un cluster existant. Sinon, les nœuds créés lors d’opérations de mise à l’échelle utilisent la planification de mise à jour corrective par défaut.  Si vous appliquez le script dans le cadre du processus de création du cluster, il est automatiquement défini comme persistant.

## <a name="next-steps"></a>Étapes suivantes

Pour connaître les étapes spécifiques à l’utilisation de l’action de script, consultez les sections suivantes de l’article [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md) :

* [Utiliser une action de script lors de la création du cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Appliquer une action de script sur un cluster en cours d’exécution](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)

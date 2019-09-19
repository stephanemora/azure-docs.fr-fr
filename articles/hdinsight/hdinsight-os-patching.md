---
title: Configurer la planification de la mise à jour corrective du système d’exploitation pour les clusters HDInsight sous Linux - Azure
description: Découvrez comment configurer la planification de la mise à jour corrective du système d’exploitation pour les clusters HDInsight sous Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: 06111ec35a127cf17fdcc77ff717de7a4bc7299f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076856"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Configurer la planification de la mise à jour corrective du système d’exploitation pour les clusters HDInsight sous Linux 

> [!IMPORTANT]
> Les images Ubuntu sont accessibles pour la création d’un nouveau cluster Azure HDInsight dans les trois mois qui suivent leur publication. Depuis janvier 2019, plus aucun correctif automatique n’est disponible pour les clusters en cours. Les clients doivent utiliser des actions de script ou d'autres mécanismes pour corriger un cluster en cours d'exécution. Les clusters nouvellement créés disposeront toujours des mises à jour et des correctifs de sécurité les plus récents.

Parfois, vous devez redémarrer les machines virtuelles (VM) dans un cluster HDInsight pour installer les correctifs de sécurité importants.

À l’aide des actions de script décrites dans cet article, vous pouvez modifier la planification de la mise à jour corrective du système d’exploitation comme suit :

1. Installez toutes les mises à jour, les mises à jour du noyau et de la sécurité ou les mises à jour du noyau uniquement.
2. Effectuez un redémarrage immédiat ou planifiez un redémarrage sur la machine virtuelle.

> [!NOTE]  
> Les actions de script décrites dans cet article fonctionnent uniquement avec les clusters HDInsight sous Linux créés après le 1er août 1, 2016. Les correctifs ne sont effectifs qu’après le redémarrage des machines virtuelles.
> Ces action de script n’appliquent pas automatiquement les mises à jour pour tous les cycles de mise à jour ultérieurs. Exécutez les scripts chaque fois que de nouvelles mises à jour doivent être appliquées pour installer les mises à jour et redémarrez la machine virtuelle.

## <a name="add-information-to-the-script"></a>Ajouter des informations au script

L’utilisation d’un script requiert les informations suivantes :

- Emplacement du script install-updates-schedule-reboots : https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight utilise cet URI pour rechercher et exécuter le script sur toutes les machines virtuelles du cluster. Ce script propose des options d’installation des mises à jour et de redémarrage de la machine virtuelle.
  
- Emplacement du script schedule-reboots : https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight utilise cet URI pour rechercher et exécuter le script sur toutes les machines virtuelles du cluster. Ce script redémarre la machine virtuelle.
  
- Types de nœud de cluster auxquels s’applique le script : headnode, workernode, zookeeper. Appliquez le script à tous les types de nœuds du cluster. Si le script n’est pas appliqué à un type de nœud, les machines virtuelles de ce type de nœud ne seront pas mises à jour ni redémarrées.

- Le script install-updates-schedule-reboots accepte deux paramètres numériques :

    | Paramètre | Définition |
    | --- | --- |
    | Installer les mises à jour du noyau uniquement/toutes les mises à jour/les mises à jour du noyau et de la sécurité uniquement|0, 1 ou 2. La valeur 0 installe uniquement les mises à jour du noyau. La valeur 1 installe toutes les mises à jour et la valeur 2 installe uniquement les mise à jour du noyau et les mises à jour de sécurité. Si aucun paramètre n’est fourni, la valeur par défaut est 0. |
    | Aucun redémarrage/Active le redémarrage planifié/Active le redémarrage immédiat |0, 1 ou 2. La valeur 0 désactive le redémarrage. La valeur 1 active le redémarrage planifié et la valeur 2 active le redémarrage immédiat. Si aucun paramètre n’est fourni, la valeur par défaut est 0. L’utilisateur doit modifier le paramètre 1 pour entrer le paramètre 2. |
   
 - Le script schedule-reboots accepte un paramètre numérique :

    | Paramètre | Définition |
    | --- | --- |
    | Permet d’activer le redémarrage planifié/activer le redémarrage immédiat |1 ou 2. La valeur 1 active le redémarrage planifié (entre 12 et 24 heures). La valeur 2 active le redémarrage immédiat (dans les 5 minutes). Si aucun paramètre n’est fourni, la valeur par défaut est 1. |  

> [!NOTE]
> Vous devez marquer un script comme persistant lorsque vous l’appliquez à un cluster existant. Sinon, les nœuds créés lors d’opérations de mise à l’échelle utilisent la planification de mise à jour corrective par défaut. Si vous appliquez le script dans le cadre du processus de création du cluster, il est automatiquement défini comme persistant.


## <a name="next-steps"></a>Étapes suivantes

Pour connaître les étapes spécifiques à l’utilisation de l’action de script, consultez les sections suivantes de l’article [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md) :

* [Utiliser une action de script lors de la création du cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Appliquer une action de script sur un cluster en cours d’exécution](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)

---
title: Configurer la planification de la mise à jour corrective du système d’exploitation pour les clusters HDInsight basés sur Linux - Azure
description: Découvrez comment configurer la planification de la mise à jour corrective du système d’exploitation pour les clusters HDInsight sous Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: a73866a8898042b546fa47d9c3d14ab4e58e9a12
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503242"
---
# <a name="os-patching-for-hdinsight"></a>Mise à jour corrective du système d’exploitation pour HDInsight 

> [!IMPORTANT]
> Les images Ubuntu sont accessibles pour la création d’un nouveau cluster HDInsight dans les trois mois qui suivent leur publication. Depuis janvier 2019, **plus aucun** correctif automatique n'est disponible pour les clusters en cours d'exécution. Les clients doivent utiliser des actions de script ou d'autres mécanismes pour corriger un cluster en cours d'exécution. Les clusters nouvellement créés disposeront toujours des mises à jour et des correctifs de sécurité les plus récents.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Comment configurer la planification de la mise à jour corrective du système d’exploitation pour les clusters HDInsight sous Linux
Les machines virtuelles d’un cluster HDInsight doivent être occasionnellement redémarrées pour assurer l’installation des correctifs de sécurité importants. 

À l’aide des actions de script décrites dans cet article, vous pouvez modifier la planification de la mise à jour corrective du système d’exploitation comme suit :
1. Installez toutes les mises à jour, des mises à jour du noyau et de la sécurité ou des mises à jour du noyau uniquement.
2. Redémarrez immédiatement ou planifiez un redémarrage sur la machine virtuelle.

> [!NOTE]  
> Ces actions de script fonctionnent uniquement avec les clusters HDInsight sous Linux créés après le 1er août 2016. Les correctifs seront appliqués une fois les machines virtuelles redémarrées. Ces scripts n’appliquent pas automatiquement les mises à jour pour tous les cycles de mise à jour ultérieurs. Exécutez les scripts chaque fois que de nouvelles mises à jour doivent être appliquées pour installer les mises à jour et redémarrez la machine virtuelle.

## <a name="how-to-use-the-script"></a>Utilisation du script 

L’utilisation de ce script requiert les informations suivantes :
1. Emplacement du script install-updates-schedule-reboots : https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight utilise cet URI pour rechercher et exécuter le script sur toutes les machines virtuelles du cluster. Ce script propose des options d’installation des mises à jour et de redémarrage de la machine virtuelle.
  
2. Emplacement du script schedule-reboots : https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight utilise cet URI pour rechercher et exécuter le script sur toutes les machines virtuelles du cluster. Ce script permet de redémarrer la machine virtuelle.
  
3. Les types de nœud de cluster auxquels s’applique le script : headnode, workernode, zookeeper. Ce script doit être appliqué à tous les types de nœud du cluster. S’il n’est pas appliqué à un type de nœud, les machines virtuelles associées au type de nœud concerné ne seront ni mises à jour ni redémarrées.

4. Paramètre : Le script install-updates-schedule-reboots accepte deux paramètres numériques :

    | Paramètre | Définition |
    | --- | --- |
    | Permet d’installer les mises à jour du noyau uniquement/toutes les mises à jour/les mises à jour du noyau et de la sécurité uniquement. |0, 1 ou 2. La valeur 0 permet d’installer les mises à jour du noyau uniquement, tandis que la valeur 1 permet d’installer toutes les mises à jour et la valeur 2 les mises à jour du noyau et de la sécurité uniquement. Si aucun paramètre n’est fourni, la valeur par défaut est 0. |
    | Aucun redémarrage/Active le redémarrage planifié/Active le redémarrage immédiat |0, 1 ou 2. La valeur 0 désactive le redémarrage, tandis que la valeur 1 active le redémarrage planifié et la valeur 2 le redémarrage immédiat. Si aucun paramètre n’est fourni, la valeur par défaut est 0. L’utilisateur doit saisir le paramètre 1 pour entrer le paramètre 2. |
   
 5. Paramètre : Le script schedule-reboots accepte un paramètre numérique :

    | Paramètre | Définition |
    | --- | --- |
    | Permet d’activer le redémarrage planifié/activer le redémarrage immédiat |1 ou 2. La valeur 1 active le redémarrage planifié (le redémarrage est planifié dans les 12 à 24 heures suivantes), tandis que la valeur 2 permet d’activer le redémarrage immédiat (dans les 5 minutes). Si aucun paramètre n’est fourni, la valeur par défaut est 1. |  

> [!NOTE] 
> Vous devez marquer le script comme persistant lorsqu’il s’applique à un cluster existant. Sinon, les nœuds créés lors d’opérations de mise à l’échelle utilisent la planification de mise à jour corrective par défaut.  Si vous appliquez le script dans le cadre du processus de création du cluster, il est automatiquement défini comme persistant.


## <a name="next-steps"></a>Étapes suivantes

Pour connaître les étapes spécifiques à l’utilisation de l’action de script, consultez les sections suivantes de l’article [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md) :

* [Utiliser une action de script lors de la création du cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Appliquer une action de script sur un cluster en cours d’exécution](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)

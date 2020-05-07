---
title: Arrêter la surveillance de votre cluster Kubernetes hybride | Microsoft Docs
description: Cet article décrit comment arrêter la surveillance de votre cluster Kubernetes hybride à l’aide d’Azure Monitor pour conteneurs.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: f2f3a8671c1f2baf60d399cc87f2f843dfee4f70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195724"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>Arrêter la surveillance de votre cluster hybride

Une fois que vous avez activé la surveillance de votre cluster Kubernetes s’exécutant sur Azure Stack ou localement, vous pouvez arrêter la surveillance à l’aide d’Azure Monitor pour conteneurs si vous décidez de ne plus le surveiller. Cet article explique comment effectuer cette procédure.  

## <a name="how-to-stop-monitoring-using-helm"></a>Arrêt de la surveillance à l’aide de Helm

1. Pour identifier d’abord la version de chart Helm pour Azure Monitor pour conteneurs qui est installée sur votre cluster, exécutez la commande Helm suivante.

    ```
    helm list
    ```

    La sortie doit ressembler à ce qui suit :

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-release-1* représente la version du chart Helm pour Azure Monitor pour conteneurs.

2. Pour supprimer la version du chart, exécutez la commande Helm suivante.

    `helm delete <releaseName>`

    Exemple :

    `helm delete azmon-containers-release-1`

    Cette opération supprime la version du cluster. Vous pouvez vérifier cela en exécutant la commande `helm list` :

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Le changement de configuration peut prendre quelques minutes. Étant donné que Helm suit vos versions même après les avoir supprimées, vous pouvez auditer l’historique d’un cluster et même annuler la suppression d’une version grâce à `helm rollback`.

## <a name="next-steps"></a>Étapes suivantes

Si l’espace de travail Log Analytics a été créé uniquement pour prendre en charge la surveillance du cluster et qu’il n’est plus nécessaire, vous devez le supprimer manuellement. Si vous n’êtes pas familiarisé avec la suppression d’un espace de travail, consultez la rubrique [Supprimer un espace de travail Azure Log Analytics](../../log-analytics/log-analytics-manage-del-workspace.md).

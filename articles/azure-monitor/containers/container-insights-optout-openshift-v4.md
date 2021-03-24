---
title: Comment arrêter de surveiller votre cluster Azure et Red Hat OpenShift v4 | Microsoft Docs
description: Cet article décrit comment arrêter l’analyse d’Azure Red Hat OpenShift et du cluster Red OpenShift version 4 avec Container Insights.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 09ca05a25ce9bb02b8a3d515acf060e2e9e7e8c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731797"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>Comment arrêter de surveiller votre cluster Azure et Red Hat OpenShift v4

Après avoir activé l’analyse d’Azure Red Hat OpenShift et du cluster Red Hat OpenShift 4.x, vous pouvez l'arrêter avec Container Insights si vous décidez de ne plus l’analyser. Cet article explique comment effectuer cette procédure.  

## <a name="how-to-stop-monitoring-using-helm"></a>Arrêt de la surveillance à l’aide de Helm

1. Pour identifier d’abord la version de chart Helm Container Insights qui est installée sur votre cluster, exécutez la commande Helm suivante.

    ```
    helm list
    ```

    La sortie doit ressembler à ce qui suit :

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-release-1* représente la version de chart Helm pour Container Insights.

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

Si l’espace de travail Log Analytics a été créé uniquement pour prendre en charge la surveillance du cluster et qu’il n’est plus nécessaire, vous devez le supprimer manuellement. Si vous n’êtes pas familiarisé avec la suppression d’un espace de travail, consultez la rubrique [Supprimer un espace de travail Azure Log Analytics](../logs/delete-workspace.md).
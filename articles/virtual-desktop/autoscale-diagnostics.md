---
title: Configurer les diagnostics pour la fonctionnalité de mise à l’échelle automatique d’Azure Virtual Desktop
description: Comment configurer des rapports de diagnostic pour le service de mise à l’échelle dans votre déploiement d’Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 10/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b5621d829050c6749795df1191ea53d835e16487
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181679"
---
# <a name="set-up-diagnostics-for-the-autoscale-feature-preview"></a>Configurer les diagnostics pour la fonctionnalité de mise à l’échelle automatique (préversion)

> [!IMPORTANT]
> La fonctionnalité de mise à l'échelle automatique est actuellement disponible en version préliminaire.
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les diagnostics vous permettent de superviser les problèmes potentiels et de les résoudre avant qu’ils n’interfèrent avec votre plan de mise à l’échelle automatique (préversion).

Actuellement, vous pouvez soit envoyer des journaux de diagnostic pour la fonctionnalité de mise à l’échelle automatique à un compte Stockage Azure, soit utiliser des journaux avec Event Hub. Si vous utilisez un compte Stockage Azure, assurez-vous qu’il se trouve dans la même région que votre plan de mise à l’échelle. Pour en savoir plus sur les paramètres de diagnostic, consultez [Créer des paramètres de diagnostic](../azure-monitor/essentials/diagnostic-settings.md). Pour plus d’informations sur l’ingestion des données du journal des ressources, consultez [Durée d’ingestion des données de journal dans Azure Monitor](../azure-monitor/logs/data-ingestion-time.md).

## <a name="enable-diagnostics-for-scaling-plans"></a>Activer les diagnostics pour les plans de mise à l’échelle

Pour activer les diagnostics pour votre plan de mise à l’échelle :

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com/).

2. Sélectionnez **Plans de mise à l’échelle**, puis sélectionnez le plan de mise à l’échelle pour lequel vous souhaitez un suivi du rapport.

3. Accédez aux **Paramètres de diagnostic** et sélectionnez **Ajouter un paramètre de diagnostic**.

4. Entrez un nom pour le paramètre de diagnostic.

5. Sélectionnez ensuite **Mise à l’échelle automatique** et choisissez **compte de stockage** ou **Event Hub** selon l’emplacement où vous souhaitez envoyer le rapport.

6. Sélectionnez **Enregistrer**.

## <a name="set-log-location-in-azure-storage"></a>Définir l’emplacement du journal dans Stockage Azure

Une fois que vous avez configuré vos paramètres de diagnostic, vous pouvez trouver les journaux en procédant comme suit :

1. Dans le portail Azure, accédez au groupe de stockage auquel vous avez envoyé les journaux de diagnostic.

2. Sélectionnez **Conteneurs**. Un dossier appelé **insight-logs-autoscaling** doit s’ouvrir.

3. Sélectionnez le **dossier insight-logs-autoscaling** et ouvrez le journal que vous souhaitez passer en revue. Ouvrez des dossiers au sein de ce dossier jusqu’à ce que le fichier JSON s’affiche, puis sélectionnez tous les éléments dans ce dossier, cliquez avec le bouton droit et téléchargez-les sur votre ordinateur local.

4. Enfin, ouvrez le fichier JSON dans l’éditeur de texte de votre choix.

## <a name="view-diagnostic-logs"></a>Afficher les journaux de diagnostic

Maintenant que vous avez ouvert le fichier JSON, présentons rapidement chaque partie du rapport :

- **CorrelationID** est l’ID que vous devez afficher quand vous créez une demande de support.

- **OperationName** est le type d’opération en cours d’exécution quand le problème s’est produit.

- **ResultType** est le résultat de l’opération. Cet élément peut vous indiquer où se trouvent les problèmes si vous remarquez des résultats incomplets.

- **Message** est le message d’erreur qui fournit des informations sur l’opération incomplète. Ce message peut inclure des liens vers des documents de résolution des problèmes importants. Par conséquent, examinez-le attentivement.

Le fichier JSON suivant est un exemple de ce que vous voyez quand vous ouvrez un rapport :

```json
{
    "host_Ring": "R0",
    "Level": 4,
    "ActivityId": "c1111111-1111-1111-b111-11111cd1ba1b1",
    "time": "2021-08-31T16:00:46.5246835Z",
    "resourceId": "/SUBSCRIPTIONS/AD11111A-1C21-1CF1-A7DE-CB1111E1D111/RESOURCEGROUPS/TEST/PROVIDERS/MICROSOFT.DESKTOPVIRTUALIZATION/SCALINGPLANS/TESTPLAN",
    "operationName": "HostPoolLoadBalancerTypeUpdated",
    "category": "Autoscale",
    "resultType": "Succeeded",
    "level": "Informational",
    "correlationId": "35ec619b-b5d8-5b5f-9242-824aa4d2b878",
    "properties": {
        "Message": "Host pool's load balancing algorithm updated",
        "HostPoolArmPath": "/subscriptions/AD11111A-1C21-1CF1-A7DE-CB1111E1D111/resourcegroups/test/providers/microsoft.desktopvirtualization/hostpools/testHostPool ",
        "PreviousLoadBalancerType": "BreadthFirst",
        "NewLoadBalancerType": "DepthFirst"
    }
}. L
```

## <a name="next-steps"></a>Étapes suivantes

- Consultez la marche à suivre pour créer un plan de mise à l’échelle à la section [Mise à l’échelle automatique pour les hôte de session Azure Virtual Desktop](autoscale-scaling-plan.md).
- [Attribuez des pool d’hôtes nouveaux ou existants à votre plan de mise à l’échelle](autoscale-new-existing-host-pool.md).

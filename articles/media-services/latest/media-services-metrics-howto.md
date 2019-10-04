---
title: Afficher les mesures avec Azure Monitor
description: Cet article montre comment surveiller les mesures avec les graphiques du portail Azure et Azure CLI.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: d331dc4eb0c6668426e1ab1a01a1dd1dcebe0c85
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795818"
---
# <a name="monitor-media-services-metrics"></a>Surveiller les mesures Media Services 

[Azure Monitor](../../azure-monitor/overview.md) vous permet de surveiller les métriques et les journaux de diagnostic pour vous aider à comprendre le fonctionnement de vos applications. Pour une description détaillée de cette fonctionnalité et pour comprendre pourquoi utiliser les mesures et les journaux de diagnostic Azure Media Services, consultez la section [Surveiller les mesures et les journaux de diagnostic Media Services](media-services-metrics-diagnostic-logs.md).

Azure Monitor offre plusieurs moyens d’interagir avec les mesures, y compris en créant des graphiques dans le portail, en y accédant via l’API REST ou en envoyant des requêtes avec l’interface CLI. Cet article montre comment surveiller les mesures avec les graphiques du portail Azure et Azure CLI.

## <a name="prerequisites"></a>Prérequis

- [Créer un compte Media Services](create-account-cli-how-to.md)
- Évaluation [Surveiller les mesures et les journaux de diagnostic Media Services](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Voir les mesures depuis le portail Azure

1. Connectez-vous au portail Azure sur https://portal.azure.com.
1. Accédez à votre compte Azure Media Services et sélectionnez **Mesures**.
1. Cliquez sur la case **RESSOURCE** et sélectionnez la ressource pour laquelle vous souhaitez suivre les mesures. 

    La fenêtre **Sélectionner une ressource** s’affiche sur la droite avec la liste des ressources disponibles pour vous. Dans ce cas, vous voyez 

    * &lt;Nom du compte Media Services&gt;
    * Nom du compte Media Servicesnom de point de terminaison de streaming&gt;
    * &lt;Nom du compte de stockage&gt;

    Sélectionnez la ressource puis appuyez sur **Appliquer**. Pour plus d’informations sur les ressources prises en charge et les mesures, consultez [Surveiller les mesures Media Services](media-services-metrics-diagnostic-logs.md).
 
    ![Mesures](media/media-services-metrics/metrics02.png)
    
    > [!NOTE]
    > Pour basculer entre les ressources pour lesquelles vous souhaitez surveiller les mesures, cliquez sur la case **RESSOURCE** à nouveau et répétez cette étape.
1. (Facultatif) Nommez votre graphique (modifier le nom en appuyant sur le crayon en haut).
1. Ajouter des mesures que vous souhaitez afficher.

    ![Mesures](media/media-services-metrics/metrics03.png)
1. Vous pouvez épingler votre graphique à votre tableau de bord.

## <a name="view-metrics-with-azure-cli"></a>Afficher les mesures avec Azure CLI

Pour obtenir des mesures de « Sortie » avec CLI, vous devez exécuter la commande CLI suivante :

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Pour obtenir d’autres mesures, remplacez « Sortie » par le nom de la mesure qui vous intéresse.

## <a name="see-also"></a>Voir aussi

* [Mesures Azure Monitor](../../azure-monitor/platform/data-platform.md)
* [Créer, afficher et gérer des alertes de mesure à l’aide d’Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Étapes suivantes

[Journaux de diagnostic](media-services-diagnostic-logs-howto.md)
